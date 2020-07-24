---
title: 多重 DRM 內容保護系統-Azure 媒體服務 v3
description: 這篇文章提供如何使用 Azure 媒體服務設計多重 DRM 內容保護系統的詳細說明。
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2018
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 79f06bd5d6af05e334faf4e1f6d8cd3e358f89ba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039186"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>設計具有存取控制的多重 DRM 內容保護系統 

設計及建置適用於 Over-The-Pop (OTT) 或線上串流解決方案的數位版權管理 (DRM) 子系統是一項複雜的工作。 操作員/線上視訊提供者通常會將這個工作外包給專門的 DRM 服務提供者。 這份文件的目標是要呈現 OTT 或線上串流解決方案中端對端 DRM 子系統的參考設計和參考實作。

這份文件的目標讀者是使用 OTT 或線上串流/多重螢幕解決方案的 DRM 子系統工程師，或是對於 DRM 子系統有興趣的任何讀者。 假設讀者都熟悉市場上至少一個 DRM 技術，例如 PlayReady、Widevine、FairPlay 或 Adobe Access。

在本文的討論中，我們針對多重 DRM 納入了 Azure 媒體服務所支援的 3 個 DRM：適用於 PlayReady 和 Widevine 的一般加密 (CENC)、FairPlay 以及 AES-128 清除金鑰加密。 線上串流和 OTT 產業中的主要趨勢，是在各種用戶端平台上使用原生 DRM。 這個趨勢是從先前趨勢轉移而來，先前的趨勢是將單一 DRM 及其用戶端 SDK 用於各種用戶端平台。 使用 CENC 搭配多重原生 DRM 時，PlayReady 和 Widevine 會依照 [Common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) 規格加密。

使用原生多重 DRM 進行內容保護的優點，是它能夠：

* 降低加密成本，因為系統會對於不同平台使用搭配原生 DRM 的單一加密處理。
* 降低管理資產的成本，因為儲存體中只需要一份資產。
* 排除 DRM 用戶端授權成本，因為原生 DRM 用戶端在其原生平台上通常是免費的。

### <a name="goals-of-the-article"></a>本文的目標

本文宗旨如下：

* 提供 3 種 DRM (適用於 DASH 的 CENC、適用於 HLS 的 FairPlay，和適用於 Smooth Streaming 的 PlayReady) 全都使用的 DRM 子系統所適用的參考設計。
* 提供 Azure 和 Azure 媒體服務平台的參考實作。
* 討論某些設計和實作主題。

下表摘要說明不同平台上的原生 DRM 支援，以及不同瀏覽器中的 EME 支援。

| **用戶端平台** | **原生 DRM** | **EME** |
| --- | --- | --- |
| **智慧型電視、STB** | PlayReady、Widevine 及/或其他 | 內嵌瀏覽器/EME for PlayReady 及/或 Widevine|
| **Windows 10** | PlayReady | Microsoft Edge/IE11 for PlayReady|
| **Android 裝置 (電話、平板電腦、電視)** |Widevine |Chrome for Widevine |
| **iOS** | FairPlay | Safari for FairPlay (自 iOS 11.2 起) |
| **macOS** | FairPlay | Safari for FairPlay (自 Mac OS X 10.11+ El Capitan 上的 Safari 9+ 起)|
| **tvOS** | FairPlay | |

考量目前每種 DRM 的部署狀態，服務通常需要實作 2 或 3 個 DRM，以確保您能以最佳方式來處理所有類型的端點。

您必須在服務邏輯複雜度和用戶端複雜度之間做出取捨，以便在各種不同的用戶端上讓使用者經驗能達到某種等級。

當您下決定時，請記住下列這幾點：

* PlayReady 原生實作在每種 Windows 裝置及部分 Android 裝置中，且可透過軟體 SDK 在幾乎所有平台上實作。
* Widevine 原生實作在每種 Android 裝置、Chrome 及部分其他的裝置中。 在 Firefox 和 Opera 瀏覽器中也可透過 DASH 支援 Widevine。
* FairPlay 適用於 iOS、macOS 和 tvOS。


## <a name="a-reference-design"></a>參考設計
本節所探討的參考設計與用來實作它的技術無關。

DRM 子系統可能包含下列元件：

* 金鑰管理
* DRM 加密封裝
* DRM 授權傳遞
* 權利檢查/存取控制
* 使用者驗證/授權
* 播放器應用程式
* 來源/內容傳遞網路 (CDN)

下圖說明 DRM 子系統中的元件之高階互動：

![DRM 子系統與 CENC](./media/design-multi-drm-system-with-access-control/media-services-generic-drm-subsystem-with-cenc.png)

設計具有三個基本層級：

* 後台系統層級 (黑色) 不會對外部公開。
* DMZ 層級 (深藍色) 包含對應公眾的所有端點。
* 公用網際網路層級 (淺藍色)，包含具有跨公用網際網路流量的 CDN 和播放器。

應該還有用來控制 DRM 保護的內容管理工具，無論是靜態或動態加密。 DRM 加密的輸入包括：

* MBR 視訊內容
* 內容金鑰
* 授權取得 URL

以下是播放期間的高階流程：

* 使用者已通過驗證。
* 已為使用者建立授權權杖。
* DRM 保護內容 (資訊清單) 已下載至播放器。
* 播放器會將授權取得要求以及金鑰識別碼與授權權杖提交至授權伺服器。

下列章節會討論金鑰管理的設計。

| **ContentKey-to-asset** | **案例** |
| --- | --- |
| 1 對 1 |最簡單的案例。 它提供最佳的控制。 但是，這種排列方式通常會產生最高的授權傳遞成本。 每個受保護的資產需要至少一個授權要求。 |
| 1 對多 |您可以對多個資產使用相同的內容金鑰。 例如，對於如內容類型或內容類型子集 (或 Movie Gene) 之邏輯群組中的所有資產，您可以使用單一內容金鑰。 |
| 多對 1 |每個資產需要有多個內容金鑰。 <br/><br/>舉例來說，如果您需要針對 MPEG-DASH 套用具有多重 DRM 的動態 CENC 保護，且針對 HLS 套用動態 AES-128 加密，您需要兩個不同的內容金鑰。 每個內容金鑰都需要自己的 ContentKeyType。 (對於用來保護動態 CENC 的內容金鑰，使用 ContentKeyType.CommonEncryption。 對於用來加密動態 AES 128 的內容金鑰，使用 ContentKeyType.EnvelopeEncryption)。<br/><br/>另一個範例中，理論上，在 DASH 內容的 CENC 保護中，您可以使用一個內容金鑰來保護視訊串流，並使用其他內容金鑰來保護音訊串流。 |
| 多對多 |結合上述兩種案例。 對於相同資料群組中的每個多重資產，使用一組內容金鑰。 |

另一個需要考量的重要因素，是使用持續性和非持續性的授權。

這些考量為何重要？

如果您是使用公用雲端來傳遞授權，持續性和非持續性的授權就會直接影響授權傳遞的成本。 下列兩個不同的設計案例用來說明：

* 每月訂用帳戶：使用持續性授權，以及 1 對多的內容金鑰對資產對應。 例如，對於所有兒童影片，我們會使用單一內容金鑰進行加密。 在此案例中：

    所有兒童影片/裝置的授權總數 = 1

* 每月訂用帳戶：使用非持續性授權，以及內容金鑰和資產之間的 1 對 1 對應。 在此案例中：

    所有兒童影片/裝置的授權總數 = [觀賞影片數] x [工作階段數]

兩個不同的設計會導致非常不同的授權要求模式。 如果授權傳遞服務是由如媒體服務的公用雲端所提供，則不同的模式會導致授權傳遞成本的不同。

## <a name="map-design-to-technology-for-implementation"></a>將設計對應至技術以進行實作
接下來，一般設計會對應至 Azure/媒體服務平台上的技術，方法是指定要用於每個建置組塊的技術。

下表會顯示對應。

| **建置組塊** | **技術** |
| --- | --- |
| **球員** |[Azure 媒體播放器](https://azure.microsoft.com/services/media-services/media-player/) |
| **識別提供者（IDP）** |Azure Active Directory (Azure AD) |
| **安全權杖服務 (STS)** |Azure AD |
| **DRM 保護工作流程** |Azure 媒體服務動態保護 |
| **DRM 授權傳遞** |* 媒體服務授權傳遞 (PlayReady、Widevine、FairPlay) <br/>* Axinom 授權伺服器 <br/>* 自訂 PlayReady 授權伺服器 |
| **來源** |Azure 媒體服務串流端點 |
| **金鑰管理** |不需要參考實作 |
| **內容管理** |C# 主控台應用程式 |

換言之，Azure AD 會提供 IDP 和 STS。 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 用於播放器。 Azure 媒體服務和 Azure 媒體播放器均支援透過 DASH 的 CENC、透過 HLS 的 FairPlay、透過 Smooth Streaming 的 PlayReady，以及適用於 DASH、HLS 和 Smooth 的 AES-128 加密。

下圖顯示上述技術對應的整體結構與流程：

![媒體服務上的 CENC](./media/design-multi-drm-system-with-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

為了設定 DRM 內容保護，內容管理工具會使用下列輸入：

* 開啟內容
* 來自金鑰管理的內容金鑰
* 授權取得 URL
* 來自 Azure AD 的資訊清單，例如對象、簽發者和權杖宣告

以下是內容管理工具的輸出：

* ContentKeyPolicy 會說明所使用的各類 DRM 適用的 DRM 授權範本；
* ContentKeyPolicyRestriction 說明發出 DRM 授權之前的存取控制
* Streamingpolicy 說明各種用於串流的 DRM 組合 - 加密模式 - 串流通訊協定 - 容器格式
* StreamingLocator 說明用於加密的內容金鑰/IV 和串流 URL 

以下是執行階段期間的流程：

* 在使用者驗證時，會產生 JWT。
* JWT 中包含的其中一個宣告是群組宣告，其包含 EntitledUserGroup 的群組物件識別碼。 系統會使用這個宣告來通過權限檢查。
* 播放器會下載 CENC 保護內容的用戶端資訊清單，並識別下列項目：
   * 金鑰識別碼。
   * 內容會受 DRM 保護。
   * 授權取得 URL。
* 播放器會根據支援的瀏覽器/DRM 進行授權取得要求。 在授權取得要求中，也會提交金鑰識別碼和 JWT。 授權傳遞服務會驗證 JWT，以及發行所需授權之前所包含的宣告。

## <a name="implementation"></a>實作
### <a name="implementation-procedures"></a>實作程序
實作包含下列步驟：

1. 準備測試資產。 編碼/封裝測試視訊為媒體服務中的多位元速率分散 MP4。 此資產*不*受 DRM 保護。 DRM 保護稍後會由動態保護完成。

2. 建立金鑰識別碼和內容金鑰 (選擇性地從金鑰種子)。 在本例中，並不需要金鑰管理系統，因為數個測試資產只需要單一金鑰識別碼和內容金鑰。

3. 使用媒體服務 API 來針對測試資產設定多重 DRM 授權傳遞服務。 如果您是使用貴公司或貴公司廠商的自訂授權伺服器，而不是媒體服務中的授權服務，可以略過此步驟。 當您設定授權傳遞時，可以在步驟中指定授權取得 URL。 需要媒體服務 API 以指定一些詳細組態，例如不同 DRM 授權服務的授權原則限制和授權回應範本。 目前，Azure 入口網站並未提供此組態所需的 UI。 如需 API 層級的資訊及範例程式碼，請參閱[使用 PlayReady 和/或 Widevine 動態一般加密](protect-with-drm.md)。

4. 使用媒體服務 API，針對測試資產設定資產傳遞原則。 如需 API 層級的資訊及範例程式碼，請參閱[使用 PlayReady 和/或 Widevine 動態一般加密](protect-with-drm.md)。

5. 在 Azure 中建立和設定 Azure AD 租用戶。

6. 在 Azure AD 租用戶中建立幾個使用者帳戶和群組。 建立至少一個「有權限的使用者」群組，並將使用者新增至此群組。 此群組中的使用者會通過授權取得中的權限檢查。 不在此群組中的使用者無法通過驗證檢查，且無法取得授權。 此「有權限的使用者」群組的成員資格，是由 Azure AD 發出的 JWT 中必要的「群組」宣告。 當您設定多個 DRM 授權傳遞服務時，可以在步驟中指定此宣告需求。

7. 建立 ASP.NET MVC 應用程式來裝載您的影片播放器。 此 ASP.NET 應用程式會根據 Azure AD 租用戶受到使用者驗證的保護。 使用者驗證之後，會在取得的存取權杖中包含適當的宣告。 建議在此步驟使用 OpenID Connect API。 安裝下列 NuGet 封裝：

   * Install-Package Microsoft.Azure.ActiveDirectory.GraphClient
   * Install-Package Microsoft.Owin.Security.OpenIdConnect
   * Install-Package Microsoft.Owin.Security.Cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

8. 使用 [Azure 媒體播放器 API](https://amp.azure.net/libs/amp/latest/docs/) 來建立播放器。 使用 [Azure 媒體播放器的 ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) 來指定要在不同的 DRM 平台上使用哪個 DRM 技術。

9. 下表會顯示測試矩陣。

    | **DRM** | **瀏覽器** | **有權限使用者的結果** | **無權限使用者的結果** |
    | --- | --- | --- | --- |
    | **PlayReady** |Windows 10 的 Microsoft Edge 或 Internet Explorer 11 |成功 |失敗 |
    | **Widevine** |Chrome、Firefox、Opera |成功 |失敗 |
    | **FairPlay** |macOS 上的 Safari      |成功 |失敗 |
    | **AES-128** |大部分的新式瀏覽器  |成功 |失敗 |

如需如何針對 ASP.NET MVC 播放器應用程式設定 Azure AD 的相關資訊，請參閱[整合 Azure 媒體服務 OWIN MVC 型應用程式與 Azure Active Directory 並根據 JWT 宣告限制內容金鑰傳遞](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)。

如需詳細資訊，請參閱[Azure 媒體服務和動態加密中的 JWT 權杖驗證](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。  

如需 Azure AD 的資訊：

* 您可以在 [Azure Active Directory 開發人員指南](../../active-directory/develop/v2-overview.md)中找到開發人員的資訊。
* 您可以在 [管理 Azure AD 租用戶目錄](../../active-directory/fundamentals/active-directory-whatis.md)中找到系統管理員的資訊。

### <a name="some-issues-in-implementation"></a>實作中的一些問題

使用下列疑難排解資訊來取得實作問題的說明。

* 簽發者 URL 的結尾必須為 "/"。 目標對象必須是播放器應用程式用戶端識別碼。 此外，請在簽發者 URL 的結尾新增 "/"。

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    在 [JWT 解碼器](http://jwt.calebb.net/)中，您會看到 **aud** 和 **iss**，如 JWT 權杖中所示：

    ![JWT](./media/design-multi-drm-system-with-access-control/media-services-1st-gotcha.png)

* 將權限新增至應用程式的 Azure AD 中 (在應用程式的 [設定]**** 索引標籤)。 每個應用程式皆需要權限 (本機和已部署版本)。

    ![權限](./media/design-multi-drm-system-with-access-control/media-services-perms-to-other-apps.png)

* 當您設定動態 CENC 保護時，請使用正確的簽發者。

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    下列項目無法運作︰

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    GUID 是 Azure AD 租用戶識別碼。 您可以在 Azure 入口網站的 [端點]**** 快顯功能表中找到 GUID。

* 授與群組成員資格宣告權限。 請確定下列項目位於 Azure AD 應用程式資訊清單檔中： 

    "groupMembershipClaims": "All"    (預設值是 null)

* 建立限制需求時，設定適當的 TokenType。

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    因為您新增了 SWT (ACS) 以外的 JWT (Azure AD) 支援，預設 TokenType 是 TokenType.JWT。 如果您使用 SWT/ACS，就必須將權杖設定為 TokenType.SWT。

## <a name="the-completed-system-and-test"></a>完整的系統和測試

本節將引導您在完整的端對端系統中進行幾個案例，讓您在取得登入帳戶之前，能夠對此行為產生基本印象：

* 如果您需要非整合的案例：

    * 對於媒體服務中裝載的視訊資產，這些資產可能是未受保護、或是受 DRM 保護但沒有權杖驗證 (向任何要求者發出授權)，您無須登入即可進行測試。 如果您是透過 HTTP 進行視訊串流，請切換到 HTTP。

* 如果您需要端對端整合案例：

    * 對於媒體服務中受動態 DRM 保護的視訊資產，且包含權杖驗證與 Azure AD 所產生的 JWT，您必須登入。

如需播放器 web 應用程式和其登入的相關資訊，請參閱[此網站](https://openidconnectweb.azurewebsites.net/)。

### <a name="user-sign-in"></a>使用者登入
為了測試端對端整合 DRM 系統，您必須建立或新增帳戶。

哪個帳戶？

雖然 Azure 原先只允許 Microsoft 帳戶使用者進行存取，但它現在可讓這兩個系統的使用者進行存取。 現在所有 Azure 屬性都信任 Azure AD 進行驗證，且 Azure AD 會驗證組織使用者。 系統會建立同盟關聯性，其中 Azure AD 會信任 Microsoft 帳戶取用者識別系統，從而驗證取用者使用者。 如此一來，Azure AD 便能驗證來賓 Microsoft 帳戶，以及原生 Azure AD 帳戶。

因為 Azure AD 信任 Microsoft 帳戶網域，您可以將下列任何網域的任何帳戶新增至自訂 Azure AD 租用戶，並使用該帳戶登入：

| **網域名稱** | **網域** |
| --- | --- |
| **自訂 Azure AD 租用戶網域** |somename.onmicrosoft.com |
| **公司網域** |microsoft.com |
| **Microsoft 帳戶網域** |outlook.com、live.com、hotmail.com |

您可以連絡任何作者為您建立或新增帳戶。

以下螢幕擷取畫面會顯示不同網域帳戶所使用的不同登入頁面：

**自訂 Azure AD 租用戶網域帳戶**：自訂 Azure AD 租用戶網域的自訂登入頁面。

![自訂 Azure AD 租用戶網域帳戶 1](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain1.png)

**採用智慧卡的 Microsoft 網域帳戶**：由 Microsoft Corporate IT 自訂且採用雙因素驗證的登入頁面。

![自訂 Azure AD 租用戶網域帳戶 2](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain2.png)

**Microsoft 帳戶**：Microsoft 取用者帳戶的登入頁面。

![自訂 Azure AD 租用戶網域帳戶 3](./media/design-multi-drm-system-with-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>使用 PlayReady 的加密媒體擴充功能

在具有支援 PlayReady 之加密媒體擴充功能 (EME) 的最新瀏覽器 (例如 Windows 8.1 和更新版本上的 Internet Explorer 11，以及 Windows 10 上的 Microsoft Edge 瀏覽器) 上，PlayReady 是 EME 的基本 DRM。

![針對 PlayReady 使用 EME](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready1.png)

深色的播放器區域是因為 PlayReady 保護會防止對受保護的視訊進行螢幕擷取。

下列螢幕擷取畫面會顯示播放器的外掛程式，以及 Microsoft Security Essentials (MSE)/EME 支援：

![適用於 PlayReady 的播放器外掛程式](./media/design-multi-drm-system-with-access-control/media-services-eme-for-playready2.png)

Windows 10 的 Microsoft Edge 及 Internet Explorer 11 中的 EME，允許在支援 [PlayReady SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) \(英文\) 的 Windows 10 裝置上加以叫用。 PlayReady SL3000 會解除增強型付費內容 (4K、HDR)，以及新的內容傳遞模型 (增強型內容) 流程的鎖定。

若要將焦點放在 Windows 裝置上，PlayReady 是 Windows 裝置可用的硬體中唯一的 DRM (PlayReady SL3000)。 串流服務可透過 EME 或通用 Windows 平台應用程式來使用 PlayReady，並利用 PlayReady SL3000 提供比其他 DRM 更高的影片品質。 一般而言，最高 2K 的內容會流經 Chrome 或 Firefox，最高 4K 的內容會流經 Microsoft Edge/Internet Explorer 11 或相同裝置上的通用 Windows 平台應用程式。 數量取決於服務設定和實作。

#### <a name="use-eme-for-widevine"></a>針對 Widevine 使用 EME

在支援 EME/Widevine 的現代瀏覽器上，例如 Windows 10、Windows 8.1、Mac OSX Yosemite 上的 Chrome 41+，以及 Android 4.4.4 上的 Chrome，Google Widevine 是 EME 背後的 DRM。

![針對 Widevine 使用 EME](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine1.png)

Widevine 不會防止您對受保護的視訊進行螢幕擷取。

![適用於 Widevine 的播放器外掛程式](./media/design-multi-drm-system-with-access-control/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>針對 FairPlay 使用 EME

同樣地，您也可以在 macOS 或 iOS 11.2 和更新版本的 Safari 中，使用此測試播放器來測試 FairPlay 保護的內容。

請確實將 "FairPlay" 設為 protectionInfo.type，並在 FPS AC 路徑 (FairPlay 串流處理應用程式憑證路徑) 中將其放入您應用程式憑證的正確 URL 內。

### <a name="unentitled-users"></a>未獲授權的使用者

如果使用者不是「有權限的使用者」群組的成員，該使用者就不會通過權限檢查。 然後，多重 DRM 授權服務會拒絕發出所示的要求授權。 詳細的描述是「授權取得失敗」，這是按設計角度。

![未獲授權的使用者](./media/design-multi-drm-system-with-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>執行自訂 Security Token Service

如果您執行自訂 STS，就會使用對稱或非對稱金鑰，由自訂 STS 發出 JWT。

下列螢幕擷取畫面會顯示使用對稱金鑰 (使用 Chrome) 的案例：

![使用對稱金鑰的自訂 STS](./media/design-multi-drm-system-with-access-control/media-services-running-sts1.png)

下列螢幕擷取畫面顯示的案例是透過 X509 憑證使用非對稱金鑰 (使用 Microsoft 新式瀏覽器)：

![使用非對稱金鑰的自訂 STS](./media/design-multi-drm-system-with-access-control/media-services-running-sts2.png)

在上述兩個案例中，使用者驗證會保持相同。 它會透過 Azure AD 進行。 唯一的差別在於，JWT 是由自訂 STS 發出，而不是 Azure AD。 設定動態 CENC 保護時，授權傳遞服務限制會指定 JWT 的類型 (對稱或非對稱金鑰)。

## <a name="next-steps"></a>後續步驟

* [常見問題集](frequently-asked-questions.md)
* [內容保護概觀](content-protection-overview.md)
* [使用 DRM 保護內容](protect-with-drm.md)
