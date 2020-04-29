---
title: 使用租使用者限制來管理對 SaaS 應用程式的存取-Azure AD
description: 如何使用租使用者限制來管理哪些使用者可以根據其 Azure AD 租使用者存取應用程式。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: mimart
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecd49b340810f92727f0fc98f84031c8cbf68179
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481172"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>使用租使用者限制來管理 SaaS 雲端應用程式的存取權

注重安全性的大型組織想要移到 Office 365 之類的雲端服務，但需要確知其使用者只能存取獲得核准的資源。 傳統上，當公司想要管理存取時，會限制網域名稱或 IP 位址。 這種方法在軟體即服務（或 SaaS）應用程式裝載于公用雲端的世界中失敗，在共用的功能變數名稱（例如[outlook.office.com](https://outlook.office.com/)和[login.microsoftonline.com](https://login.microsoftonline.com/)）上執行。 封鎖這些位址會讓使用者無法存取整個網路上的 Outlook，而不僅是限制他們只能存取已核准的身分識別和資源。

此挑戰的 Azure Active Directory （Azure AD）解決方案是一項稱為「租使用者限制」的功能。 透過租使用者限制，組織可以根據應用程式用於單一登入的 Azure AD 租使用者，控制 SaaS 雲端應用程式的存取權。 例如，您可能想要允許使用者存取您組織的 Office 365 應用程式，但又防止他們存取其他組織的這些相同應用程式執行個體。  

有了租使用者限制，組織就可以指定允許其使用者存取的租使用者清單。 Azure AD 接著便可只授與對這些已允許之租用戶的存取權。

本文著重于 Office 365 的租使用者限制，但此功能應適用于使用新式驗證通訊協定搭配 Azure AD 進行單一登入的任何 SaaS 雲端應用程式。 如果您使用 SaaS 應用程式搭配與 Office 365 所用租用戶不同的 Azure AD 租用戶，請務必核准所有必要的租用戶。 如需有關 SaaS 雲端應用程式的詳細資訊，請參閱 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)。

## <a name="how-it-works"></a>運作方式

整體解決方案包含下列元件：

1. **Azure AD**：如果存在`Restrict-Access-To-Tenants: <permitted tenant list>` ，Azure AD 只會對允許的租使用者發出安全性權杖。

2. 內部**部署 proxy 伺服器基礎結構**：此基礎結構是能夠進行傳輸層安全性（TLS）檢查的 proxy 裝置。 您必須將 proxy 設定為將包含允許的租使用者清單的標頭插入至目的地為 Azure AD 的流量。

3. **用戶端軟體**：若要支援租使用者限制，用戶端軟體必須直接從 Azure AD 要求權杖，讓 proxy 基礎結構可以攔截流量。 以瀏覽器為基礎的 Office 365 應用程式目前支援租使用者限制，如同使用新式驗證的 Office 用戶端（例如 OAuth 2.0）。

4. **新式驗證**：雲端服務必須使用新式驗證來使用租使用者限制，並封鎖對所有非允許之租使用者的存取。 根據預設，您必須將 Office 365 雲端服務設定為使用新式驗證通訊協定。 如需有關 Office 365 對新式驗證之支援的最新資訊，請參閱[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。

下圖說明概略的流量流程。 租使用者限制只需要對 Azure AD 的流量進行 TLS 檢查，而不是 Office 365 雲端服務。 這項區別很重要，因為用於驗證 Azure AD 的流量量，通常比流量磁片區更低，例如 Exchange Online 和 SharePoint Online。

![租使用者限制流量流程-圖表](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>設定租使用者限制

有兩個步驟可讓您開始使用租使用者限制。 首先，請確定您的用戶端可以連接到正確的位址。 第二，設定您的 proxy 基礎結構。

### <a name="urls-and-ip-addresses"></a>URL 和 IP 位址

若要使用租使用者限制，您的用戶端必須能夠連線到下列 Azure AD Url 以進行驗證： [login.microsoftonline.com](https://login.microsoftonline.com/)、 [login.microsoft.com](https://login.microsoft.com/)和[login.windows.net](https://login.windows.net/)。 此外，若要存取 Office 365，您的用戶端也必須能夠連線到[office 365 url 和 ip 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)中所定義的完整功能變數名稱（fqdn）、URL 和 ip 位址。 

### <a name="proxy-configuration-and-requirements"></a>Proxy 組態和需求

下列設定是透過您的 proxy 基礎結構啟用租使用者限制所需的設定。 本指導方針是通用的，因此如需了解特定的實作步驟，您應該參考您 Proxy 廠商的文件。

#### <a name="prerequisites"></a>先決條件

- Proxy 必須能夠執行 TLS 攔截、HTTP 標頭插入，以及使用 Fqdn/Url 來篩選目的地。

- 用戶端必須信任 proxy 出示的憑證鏈以進行 TLS 通訊。 例如，如果使用來自內部[公開金鑰基礎結構（PKI）](/windows/desktop/seccertenroll/public-key-infrastructure)的憑證，內部發行的根憑證授權單位憑證必須是受信任的。

- 這項功能包含在 Office 365 訂閱中，但如果您想要使用租使用者限制來控制對其他 SaaS 應用程式的存取，則需要 Azure AD Premium 1 個授權。

#### <a name="configuration"></a>設定

針對每個傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的連入要求，請插入兩個 HTTP 標頭：*Restrict-Access-To-Tenants* 和 *Restrict-Access-Context*。

這些標頭應該包含下列元素︰

- 對於 [*限制存取至*租使用者]，使用 [允許的\<租使用者清單\>] 的值，這是您想要允許使用者存取的 tenant 清單（以逗號分隔）。 與租用戶一起註冊的任何網域都可用來在此清單中識別該租用戶。 例如，若要允許存取 Contoso 和 Fabrikam 租用戶，名稱/值組會顯示如下： `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- 針對*限制存取內容*，使用單一目錄識別碼的值，宣告哪個租使用者正在設定租使用者限制。 例如，若要宣告 Contoso 作為設定「租使用者限制」原則的租使用者，名稱/值組看起來會像這樣： `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> 您可以在[Azure Active Directory 入口網站](https://aad.portal.azure.com/)中找到您的目錄識別碼。 請以系統管理員身分登入，選取 [Azure Active Directory]****，然後選取 [屬性]****。

若要防止使用者插入自己的 HTTP 標頭，並將其設為未核准的租使用者，proxy 必須取代*限制存取-* 租使用者標頭（如果傳入要求中已存在）。

必須強制用戶端針對所有傳送到 login.microsoftonline.com、login.microsoft.com 及 login.windows.net 的要求使用 Proxy。 例如，如果使用 PAC 檔案來指示用戶端使用 proxy，使用者就不應該能夠編輯或停用 PAC 檔案。

## <a name="the-user-experience"></a>使用者體驗

本節說明使用者和系統管理員的體驗。

### <a name="end-user-experience"></a>使用者體驗

範例使用者位於 Contoso 網路上，但正在嘗試存取 Fabrikam 的共用 SaaS 應用程式執行個體 (例如 Outlook Online)。 如果 Fabrikam 是 Contoso 實例的非允許租使用者，則使用者會看到「拒絕存取」訊息，這表示您嘗試存取的資源屬於您的 IT 部門未核准的組織。

### <a name="admin-experience"></a>管理員體驗

雖然租使用者限制的設定是在公司 proxy 基礎結構上進行，但是系統管理員可以直接存取 Azure 入口網站中的租使用者限制報告。 若要查看報告：

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com/)。 [ **Azure Active Directory 系統管理中心**] 儀表板隨即出現。

2. 在左窗格中，選取 [Azure Active Directory]****。 [Azure Active Directory 總覽] 頁面隨即出現。

3. 在 [**其他功能**] 標題中，選取 [**租使用者限制**]。

指定為受限制存取內容的租使用者的系統管理員可以使用此報告來查看因為租使用者限制原則而封鎖的登入，包括所使用的身分識別和目標目錄識別碼。 如果設定限制的租用戶為登入的使用者租用戶或資源租用戶，則會包含登入。

> [!NOTE]
> 當位在受限制存取內容租使用者以外的租使用者登入時，此報表可能會包含有限的資訊，例如目標目錄識別碼。 在此情況下，會遮罩使用者可識別的資訊，例如名稱和使用者主體名稱，以保護其他租使用者中的使用者資料。

與 Azure 入口網站中的其他報告相同，您可以使用篩選來指定報告的範圍。 您可以根據特定時間間隔、使用者、應用程式、用戶端或狀態進行篩選。 如果您選取 [資料**行**] 按鈕，則可以選擇使用下欄欄位的任意組合來顯示資料：

- **使用者**
- **應用程式**
- **狀態**
- **日期**
- **日期（utc）** （其中 utc 是國際標準時間）
- **MFA 驗證方法**（多重要素驗證方法）
- **MFA 驗證詳細資料**（多重要素驗證詳細資料）
- **MFA 結果**
- **IP 位址**
- **用戶端**
- **使用者名稱**
- **位置**
- **目標租使用者識別碼**

## <a name="office-365-support"></a>Office 365 支援

Office 365 應用程式必須符合兩個準則，才能完全支援租使用者限制：

1. 使用的用戶端支援新式驗證。
2. 啟用新式驗證作為雲端服務的預設驗證通訊協定。

如需有關哪些 Office 用戶端目前支援新式驗證的最新資訊，請參閱[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)。 該頁面也包含在特定 Exchange Online 和「商務用 Skype Online」租用戶上啟用新式驗證的指示連結。 SharePoint Online 預設已啟用新式驗證。

Office 365 瀏覽器型應用程式（Office 入口網站、Yammer、SharePoint 網站、Web 上的 Outlook 等等）目前支援租使用者限制。 豐富型用戶端（Outlook、商務用 Skype、Word、Excel、PowerPoint 等）只有在使用新式驗證時才可以強制執行租使用者限制。  

支援新式驗證的 Outlook 和商務用 Skype 用戶端，仍然可以針對未啟用新式驗證的租使用者使用傳統通訊協定，有效地略過租使用者限制。 如果應用程式在驗證期間聯絡 login.microsoftonline.com、login.microsoft.com 或 login.windows.net，則租使用者限制可能會封鎖使用舊版通訊協定的應用程式。

針對 Windows 上的 Outlook，客戶可以選擇實作可防止使用者將非已核准郵件帳戶新增至其設定檔的限制。 例如，請參閱[防止新增非預設 Exchange 帳戶](https://gpsearch.azurewebsites.net/default.aspx?ref=1)群組原則設定。

## <a name="testing"></a>測試

如果您想要先嘗試租使用者限制，再為整個組織執行，您有兩個選項：使用 Fiddler 之類工具的主機型方法，或是 proxy 設定的分段推出。

### <a name="fiddler-for-a-host-based-approach"></a>適用於主機型方法的 Fiddler

Fiddler 是一個免費的 Web 偵錯 Proxy，可用來擷取和修改 HTTP/HTTPS 流量，包括插入 HTTP 標頭。 若要設定 Fiddler 來測試租使用者限制，請執行下列步驟：

1. [下載並安裝 Fiddler](https://www.telerik.com/fiddler)。

2. 依照 [Fiddler 的說明文件 (英文)](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) 操作，設定 Fiddler 以將 HTTPS 流量解密。

3. 設定 Fiddler 以使用自訂規則來插入 *Restrict-Access-To-Tenants* 和 *Restrict-Access-Context* 標頭：

   1. 在「Fiddler Web 偵錯工具」中，選取 [Rules] \(規則)**** 功能表，然後選取 [Customize Rules] \(自訂規則)**** 以開啟 CustomRules 檔案。

   2. 在函式的開頭`OnBeforeRequest`新增下列幾行。 以\<您的\>租使用者註冊的網域取代租使用者網域（例如`contoso.onmicrosoft.com`，）。 使用您租用戶的 Azure AD GUID 識別碼來取代 \<directory ID\>。

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      如果您需要允許多個租用戶，請使用逗號來分隔租用戶名稱。 例如：

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. 儲存並關閉 CustomRules 檔案。

設定 Fiddler 之後，您可以移至 [File] \(檔案)**** 功能表並選取 [Capture Traffic] \(擷取流量)****，來擷取流量。

### <a name="staged-rollout-of-proxy-settings"></a>分段推出 Proxy 設定

視您 Proxy 基礎結構的功能而定，您可能可以向使用者分段推出設定。 以下是一些可供考量的概略選項：

1. 使用 PAC 檔案將測試使用者指向測試 Proxy 基礎結構，同時又讓一般使用者繼續使用生產環境 Proxy 基礎結構。
2. 有些 Proxy 伺服器可以使用群組來支援不同的組態。

如需特定詳細資料，請參閱您的 proxy 伺服器檔。

## <a name="next-steps"></a>後續步驟

- 閱讀[更新的 Office 365 新式驗證 (英文)](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- 檢閱 [Office 365 URL 與 IP 位址範圍](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
