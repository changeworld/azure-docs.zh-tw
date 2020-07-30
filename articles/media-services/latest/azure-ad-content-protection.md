---
title: 使用 Azure AD 進行端對端內容保護
description: 本文會教您如何使用 Azure 媒體服務和 Azure Active Directory 保護您的內容
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/1/2020
ms.author: inhenkel
ms.openlocfilehash: 81c83cd8dcea5f8746b67a7bd52ea52a09c8a711
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001395"
---
# <a name="tutorial-end-to-end-content-protection-using-azure-ad"></a>教學課程：使用 Azure AD 進行端對端內容保護

透過本教學課程和所提供的播放器範例，您可以在 Azure 媒體服務 (AMS) 和 Azure Active Directory (AAD) 上設定端對端媒體內容保護子系統，以使用所有 AMS 支援的 DRM/AES-128、串流通訊協定、轉碼器和容器格式來串流媒體內容。 此範例極為泛用，已足供您透過具有代碼交換證明金鑰 (PKCE) 的授權碼流程來安全存取 OAuth 2 所保護的任何 REST API。 (Azure 媒體服務授權傳遞服務只是其中之一)。其也適用於使用 OAuth 2 授權碼流程來加以保護的 Microsoft Graph API 或任何自訂開發的 REST API。 這是[程式碼範例](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)的隨附文件。

在本教學課程中，您將：

> [!div class="checklist"]
>
> * 思考驗證需求
> * 了解應用程式的運作方式
> * 註冊後端資源應用程式
> * 註冊用戶端應用程式
> * 設定媒體服務帳戶的內容金鑰原則和串流原則
> * 設定播放器應用程式

如果您沒有 Azure 媒體服務訂用帳戶，請建立 Azure [免費試用帳戶](https://azure.microsoft.com/free/)，然後再建立媒體服務帳戶。

### <a name="duration"></a>Duration
在您準備好必要技術後，本教學課程應該需要兩個小時左右的時間才能完成。

## <a name="prerequisites"></a>必要條件

我們會使用下列最新的技術版本和概念。 建議您先熟悉這些資訊，再開始進行本教學課程。

### <a name="prerequisite-knowledge"></a>必備知識

雖是選擇性的，但還是建議您先熟悉下列概念再開始進行本教學課程：

* 數位版權管理 (DRM)
* [Azure 媒體服務 (AMS) v3](./media-services-overview.md)
* 使用 AMS API v3、Azure 入口網站或 [Azure 媒體服務總管 (AMSE) 工具](https://github.com/Azure/Azure-Media-Services-Explorer)的 AMS [內容金鑰原則](content-key-policy-concept.md)
* [Microsoft 身分識別平台](../../active-directory/develop/index.yml)上的 Azure AD v2 端點
* 新式雲端驗證，例如 [OAuth 2.0 和 OpenID Connect](../../active-directory/develop/active-directory-v2-protocols.md)
  * [OAuth 2.0 中的授權碼流程](../../active-directory/develop/v2-oauth2-auth-code-flow.md)以及為何需要 PKCE
  * [委派權限與應用程式權限](../../active-directory/develop/developer-glossary.md#permissions)
* [JWT 權杖](../../active-directory/develop/access-tokens.md)、其宣告和簽署金鑰變換 (包含在範例中)。

### <a name="prerequisite-code-and-installations"></a>必要的程式碼和安裝

* 程式碼範例。 下載[程式碼範例](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)。
* Visual Studio Code 的安裝。 在此下載 Visual Studio Code：[https://code.visualstudio.com/download](https://code.visualstudio.com/download)。
* Node.js 的安裝。 在此下載 Node.js：[https://nodejs.org](https://nodejs.org)。NPM 隨附於安裝。
* [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
* Azure 媒體服務 (AMS) 帳戶。
* @azure/msal-browser v2.0，[Microsoft 驗證程式庫 (MSAL)](../../active-directory/develop/msal-overview.md) SDK 系列中適用於不同用戶端平台的其中一個成員
* 最新版本的 [Azure 媒體播放器](https://github.com/Azure-Samples/azure-media-player-samples) (包含在範例中)。
* 來自 Apple 的 FPS 認證 (如果您想要納入 FairPlay DRM 的話)，以及隨 CORS 所裝載、可透過用戶端 JavaScript 加以存取的應用程式憑證。

> [!IMPORTANT]
> 本教學課程使用 .NET 來建立內容金鑰原則限制。  如果您不是 .NET 開發人員，但想要嘗試使用 Node.js 來連線到 Azure 媒體服務，請閱讀[連線到媒體服務 v3 API - Node.js](configure-connect-nodejs-howto.md)。 也有可用來自動處理金鑰變換的 Node.js 模組，請參閱 Node.js [passport-ad 模組](https://github.com/AzureAD/passport-azure-ad)。

## <a name="consider-the-authentication-and-authorization-requirements"></a>思考驗證和授權需求

在設計子系統時會出現幾項挑戰。 其有多個可動組件、會有用戶端應用程式條件約束，以及每隔六週就會發生一次的 Azure AD 金鑰變換。

本教學課程中使用的單頁應用程式 (SPA) 會考量到驗證需求以及後續限制的挑戰。 它會使用：

* Azure AD v2 端點，因為 Azure AD 開發人員平台 (v1 端點) 將會變更為 Microsoft 身分識別平台 (v2 端點)。
* 授權碼流程，因為 OAuth 2 隱含授與流程已遭到取代。
* 受限於下列條件約束的應用程式：
    * 公用用戶端無法隱藏用戶端密碼。  授權碼流程本身需要隱藏用戶端密碼，因此會使用具有 PKCE 的授權碼流程。
    * 瀏覽器型應用程式，受限於瀏覽器安全性沙箱 (CORS/預檢條件約束)。
    * 瀏覽器型應用程式，其使用受限於 JavaScript 安全性條件約束 (自訂標頭協助工具、相互關聯識別碼) 的新式 JavaScript。

## <a name="understand-the-subsystem-design"></a>了解子系統的設計

下圖顯示子系統的設計。  其具有三個層級：

* 後台系統層 (黑色)，用於設定內容金鑰原則和發佈要串流的內容
* 公用端點 (藍色)，其為播放器/客戶面向的端點，會提供驗證、授權、DRM 授權和加密內容
* 播放器應用程式 (淺藍色)，會整合所有元件和
    * 透過 Azure AD 處理使用者驗證。
    * 處理從 Azure AD 取得 access_token。
    * 從 AMS/CDN 接收資訊清單和加密內容。
    * 從 Azure 媒體服務取得 DRM 授權。
    * 處理內容解密、解碼和顯示。

![剖析 JWT 權杖的畫面](media/aad-ams-content-protection/subsystem.svg)

如需子系統的詳細資訊，請參閱[設計具有存取控制的多重 DRM 內容保護系統](./design-multi-drm-system-with-access-control.md)。

## <a name="understand-the-single-page-app"></a>了解單頁應用程式

播放器應用程式是在 Visual Studio Code 中使用下列工具所開發的單頁應用程式 (SPA)：

* Node.js 與 ES 6 JavaScript
* @azure/msal-browser 2.0 搶鮮版 (Beta)
* Azure 媒體播放器 SDK
* 針對 Azure AD v2 端點 (Microsoft 身分識別平台) 的 OAuth 2 流量

SPA 播放器應用程式會完成下列動作：

* 針對租用戶的原生使用者以及來自其他 AAD 租用戶或 MSA 帳戶的來賓使用者進行使用者驗證。 使用者可以選擇透過瀏覽器快顯視窗或重新導向來進行登入 (針對不允許快顯視窗的瀏覽器，例如 Safari)。
* 透過具有 PKCE 的授權碼流程取得 `access_token`。
* 更新 `access_token` (AAD 所簽發的權杖可有效使用 1 小時)，也會取得 `refresh_token`。
* 剖析 JWT 權杖 (`access_token` 和 `id_token`) 以進行測試/檢查。
* 取得所有三個 DRM 或 AES-128 內容金鑰的 DRM 授權。
* 在各種 DRM、串流通訊協定與容器格式的組合下串流內容。 針對每種組合會產生正確的格式字串。
* 解密、解碼和顯示。
* 用於進行疑難排解的 Microsoft Graph API 呼叫。 <!--See more details in the subsection Shortest path: testing my protected asset in my subscription with your hosted player app and underlying tenant. -->

登入、權杖取得、權杖更新和權杖顯示的畫面：

 ![登入、權杖取得、權杖更新和權杖顯示的畫面](media/aad-ams-content-protection/token-acquisition.png)

剖析 JWT 權杖 (access_token 或 id_token) 的畫面：

![剖析 JWT 權杖的畫面](media/aad-ams-content-protection/parsing-jwt-tokens.png)

使用不同的 DRM/AES、串流通訊協定與容器格式組合來測試受保護內容的畫面：

![剖析 JWT 權杖的畫面](media/aad-ams-content-protection/testing-protected-content.png)
-->

<!-- You can see a hosted version of the sample at [https://aka.ms/ott](https://aka.ms/ott)-->

## <a name="choose-an-azure-ad-tenant"></a>選擇 Azure AD 租用戶

> [!NOTE]
> 從這裡開始，我們會假設您已登入 Azure 入口網站，而且至少有一個 Azure AD 租用戶。

選擇要用於端對端範例的 Azure AD 租用戶。 您有兩個選擇：

* 現有的 Azure AD 租用戶。 任何 Azure 訂用帳戶都必須有一個 Azure AD 租用戶，但 Azure AD 租用戶可供多個 Azure 訂用帳戶使用。
* 新的 Azure AD 租用戶，*不*為任何 Azure 訂用帳戶所使用。 如果您選擇新租用戶選項，媒體服務帳戶和範例播放器應用程式則必須位於使用不同 Azure AD 租用戶的 Azure 訂用帳戶中。 這會讓您獲得一些彈性。 例如，您可以既使用自己的 Azure AD 租用戶，又使用客戶在 Azure 訂用帳戶中所擁有的客戶媒體服務帳戶。

## <a name="register-the-backend-resource-app"></a>註冊後端資源應用程式

1. 瀏覽至您所選擇或建立的 Azure AD 租用戶。
1. 從功能表中選取 [Azure Active Directory]。
1. 從功能表中選取 [應用程式註冊]。
1. 按一下 [+ 新增註冊]。
1. 將應用程式命名為 *LicenseDeliveryResource2* (其中 2 表示 AAD v2 端點)。
1. 選取 [僅限此組織目錄中的帳戶 (僅限 [*您的租用戶名稱*] - 單一租用戶)]。 如果您想要啟用多個租用戶的存取權，請選取其他多個租用戶選項的其中一個。
1. [重新導向 URI] 是選擇性選項，可於稍後變更。
1. 按一下 [註冊] 。 [應用程式註冊] 檢視隨即出現。
1. 從功能表中選取 [資訊清單]。 [資訊清單] 檢視隨即出現。
1. 將 `accessTokenAcceptedVersion` 的值變更為 *2* (不加引號)。
1. 將 `groupMembershipClaims` 的值變更為 *"SecurityGroup"* (要加上引號)。
1. 按一下 [檔案] 。
1. 從功能表中選取 [公開 API]。 [新增範圍] 檢視隨即出現。 (Azure 會提供應用程式識別碼 URI，但如果您想要加以變更，則可以在 [應用程式識別碼 URI] 欄位中編輯)。
1. 按一下 [儲存並繼續]。 此檢視將會變更。 針對下表中 [設定] 資料行內的每個設定，請在 [值] 資料行中輸入值，然後按一下 [新增範圍]。

| 設定 | 值 | 描述 |
| ------- | ----- | ----------- |
| 範圍名稱 | *DRM.License.Delivery* | 要求存取此 API 時，範圍的出現方式，在存取權杖中，則是當範圍已授與給用戶端應用程式時，範圍會如何出現。 這在此應用程式中必須是唯一的。 最佳做法是使用「resource.operation.constraint」作為用來產生名稱的模式。 |
| 誰可以同意？ | *管理員和使用者* | 當使用者同意啟用時，判斷使用者是否可以同意目錄中的這個範圍。 |
| 管理員同意顯示名稱 | *DRM 授權傳遞* | 當管理員同意此範圍時，同意畫面所會呼叫的範圍。 |
| 管理員同意說明** | *DRM 授權傳遞後端資源範圍* | 當租用戶管理員在同意畫面上展開範圍時，所顯示範圍的詳細說明。 |
| 使用者同意顯示名稱 | *DRM.License.Delivery* | 當使用者同意此範圍時，同意畫面所會呼叫的範圍。 |
| 使用者同意說明 | *DRM 授權傳遞後端資源範圍* | 這是當使用者在同意畫面上展開範圍時所顯示之範圍的詳細描述。 |
| State | *已啟用* | 判斷此範圍是否可供用戶端要求。 針對不想讓用戶端看見的範圍，請將其設定為 [停用]。 您只能刪除已停用的範圍，建議您在範圍停用後至少先等候一週再將其刪除，以確保沒有任何用戶端仍在使用該範圍。 |

## <a name="register-the-client-app"></a>註冊用戶端應用程式

1. 瀏覽至您所選擇或建立的 Azure AD 租用戶。
1. 從功能表中選取 [Azure Active Directory]。
1. 從功能表中選取 [應用程式註冊]。
1. 按一下 [+ 新增註冊]。
1. 提供用戶端應用程式的名稱，例如 *AMS AAD 內容保護*。
1. 選取 [僅限此組織目錄中的帳戶 (僅限 [*您的租用戶名稱*] - 單一租用戶)]。 如果您想要啟用多個租用戶的存取權，請選取其他多個租用戶選項的其中一個。
1. [重新導向 URI] 是選擇性選項，可於稍後變更。
1. 按一下 [註冊] 。
1. 從功能表中選取 [API 權限]。
1. 按一下 [+ 新增權限]。 [要求 API 權限] 檢視隨即開啟。
1. 按一下 [我的 API] 索引標籤，然後選取您在上一節建立的 [LicenseDeliveryResource2] 應用程式。
1. 按一下 [DRM] 箭號，然後檢查 [DRM.License.Delivery] 權限。
1. 按一下 [新增權限]。 [新增權限] 檢視隨即關閉。
1. 從功能表中選取 [資訊清單]。 [資訊清單] 檢視隨即出現。
1. 尋找下列值組，並將其新增至 `replyUrlsWithType` 屬性：

   ```json
   "replyUrlsWithType": [
        {
            "url": "https://npmwebapp.azurewebsites.net/",
            "type": "SPA"
        },
        {
            "url": "http://localhost:3000/",
            "type": "SPA"
        }
    ],
   ```

    > [!NOTE]
    > 此時，您還沒有播放器應用程式的 URL。  如果您從 localhost 網頁伺服器執行應用程式，則可以直接使用 localhost 值組。 部署播放器應用程式之後，您可以在這裡新增具有已部署 URL 的項目。  如果您忘記這麼做，則會在 Azure AD 登入中看到錯誤訊息。

1. 按一下 [檔案] 。
1. 最後，為了確認設定是否正確，請選取 [驗證]。  [驗證] 檢視隨即出現。 您的用戶端應用程式將會列為單頁應用程式 (SPA)，重新導向 URI 將會列出，而授與類型會是具有 PKCE 的授權碼流程。

### <a name="set-up-the-media-services-account-content-key-policy-and-streaming-policies"></a>設定媒體服務帳戶的內容金鑰原則和串流原則

**本節假設您是 .NET 開發人員，並熟悉如何使用 AMS v3 API。**

> [!NOTE]
> 本文撰寫當下，您還無法使用 Azure 入口網站來設定媒體服務帳戶金鑰原則，因為其不支援搭配使用非對稱權杖簽署金鑰與 OpenID-Config。設定必須支援 Azure AD 金鑰變換，因為 Azure AD 發行的權杖會由非對稱金鑰簽署，而金鑰則是每隔六週變換一次。 因此，本教學課程使用 .NET 和 AMS v3 API。

會套用 DRM 和 AES-128 的[內容金鑰原則](content-key-policy-concept.md)和[串流原則](streaming-policy-concept.md)設定。  請變更內容金鑰原則中的 `ContentKeyPolicyRestriction`。

以下是用來建立內容金鑰原則限制的 .NET 程式碼。

```dotnetcli
ContentKeyPolicyRestriction objContentKeyPolicyRestriction;

//use Azure Active Directory OpenId discovery document, supporting key rollover
objContentKeyPolicyRestriction = new ContentKeyPolicyTokenRestriction()
    {
        OpenIdConnectDiscoveryDocument = ConfigAccessor.AppSettings["ida_AADOpenIdDiscoveryDocument"]
    };

string audience = ConfigAccessor.AppSettings["ida_audience"];
string issuer   = ConfigAccessor.AppSettings["ida_issuer"];

ContentKeyPolicyTokenRestriction objContentKeyPolicyTokenRestriction = (ContentKeyPolicyTokenRestriction)objContentKeyPolicyRestriction;
objContentKeyPolicyTokenRestriction.Audience             = audience;
objContentKeyPolicyTokenRestriction.Issuer               = issuer;
objContentKeyPolicyTokenRestriction.RestrictionTokenType = ContentKeyPolicyRestrictionTokenType.Jwt;

objContentKeyPolicyRestriction = objContentKeyPolicyTokenRestriction;

return objContentKeyPolicyRestriction;
```

變更上述程式碼中的 `ida_AADOpenIdDiscoveryDocument`、`ida_audience` 和 `ida_issuer` 值。 若要在 Azure 入口網站中尋找這些項目的值：

1. 選取您稍早使用的 AAD 租用戶、按一下功能表中的 [應用程式註冊]，然後按一下 [端點] 連結。
1. 選取並複製 [OpenIdConnect 中繼資料文件] 欄位的值，並將其貼到程式碼中作為 `ida_AADOpenIdDiscoveryDocument` 值。
1. `ida_audience` 值是已註冊應用程式 *LicenseDeliveryResource2* 的應用程式 (用戶端) 識別碼。
1. `ida_issuer` 值是 URL `https://login.microsoftonline.com/[tenant_id]/v2.0`。 將 [tenant_id] 取代為您的租用戶識別碼。

## <a name="set-up-the-sample-player-app"></a>設定範例播放器應用程式

如果您尚未這麼做，請從來源存放庫複製或下載應用程式：[https://github.com/Azure-Samples/media-services-content-protection-azure-ad](https://github.com/Azure-Samples/media-services-content-protection-azure-ad)。

您有兩個可用來設定播放器應用程式的選項：

* 最少自訂 (只會取代一些常數字串值，例如 client_id、tenant_id 和串流 URL)，但您必須使用 Visual Studio Code 和 Node.js。
* 如果您想要使用其他 IDE 和 Web 平台 (例如 ASP.NET Core)，則可以將網頁檔案、JavaScript 檔案和 CSS 檔案放到您的專案中，因為播放器應用程式本身不會使用任何伺服器端程式碼。

### <a name="option-1"></a>選項 1

1. 啟動 Visual Studio Code。
1. 若要開啟專案，請按一下 [檔案] > [開啟資料夾]-> 瀏覽並選取 *package.json* 檔案的父資料夾。
1. 開啟 JavaScript 檔案 *public/javascript/constants.js*。
1. 將 `OAUTH2_CONST.CLIENT_ID` 取代為 AAD 租用戶中已註冊用戶端應用程式的 `client_id`。  您可以在 Azure 入口網站的已註冊應用程式 [概觀] 區段中找到 `client_id`。 注意：這是用戶端識別碼，而不是物件識別碼。
1. 將 `OAUTH2_CONST.TENANT_ID` 取代為 Azure AD 租用戶的 `tenant_id`。 按一下 Azure Active Directory 功能表即可找到您的 `tenant_id`。 tenant_id 會出現在 [概觀] 區段中。
1. 將 `OAUTH2_CONST.SCOPE` 取代為新增至已註冊用戶端應用程式的範圍。 您可以從 [應用程式註冊] 功能表中瀏覽至已註冊的用戶端應用程式，然後選取您的用戶端應用程式，以尋找此範圍：
    1. 選取您的用戶端應用程式、按一下 [API 權限] 功能表，然後選取 API 權限 [LicenseDeliveryResource2] 底下的範圍 [DRM.License.Delivery]。 權限的格式應該會類似 *api://df4ed433-dbf0-4da6-b328-e1fe05786db5/DRM.License.Delivery*。 **重要**：將 `offline_access` 前面的空間保留在 `OAUTH2_CONST.SCOPE` 中。
1. 取代 `AMS_CONST` 的兩個常數字串，如下所示。 一個是您測試資產的受保護串流 URL，另一個是 FPS 應用程式憑證 URL (如果您想要納入 FairPlay 測試案例的話)。 否則，您可以針對 `AMS_CONST.APP_CERT_URL` 將其保持原狀。 然後按一下 [ **儲存**]。

```javascript
//defaults in ams.js
class AMS_CONST {
    static get DEFAULT_URL() {
        return "https://eventgridmediaservice-usw22.streaming.media.azure.net/9591e337-ae90-420e-be30-1da36c06665b/MicrosoftElite01.ism/manifest(format=mpd-time-csf,encryption=cenc)";
    }
    //FairPlay application cert URL
    static get APP_CERT_URL() {
     return `${window.location.href}cert/FPSAC.cer`;
    }
}
```

若要進行本機測試：

1. 在 Visual Studio Code (VSC) 中，從主功能表選取 [檢視]，然後選取 [終端機]。
1. 如果您尚未安裝 npm，請在命令提示字元中輸入 `npm install`。
1. 在命令提示字元中輸入 `npm start`。 (如果 npm 未啟動，請嘗試在命令提示字元中輸入 `cd npmweb`，將目錄變更為 `npmweb`)。
1. 使用瀏覽器瀏覽至 `http://localhost:3000`。

視您使用的瀏覽器而定，挑選正確組合的 DRM/AES、串流通訊協定與容器格式，以在登入後進行測試 (取得 `access_token`)。 如果您要在 macOS 上的 Safari 中進行測試，請核取 [重新導向 API] 選項，因為 Safari 不允許快顯視窗。 其他瀏覽器大多會同時允許快顯視窗和重新導向選項。

### <a name="option-2"></a>選項 2

如果您打算使用其他 IDE/Web 平台及/或網頁伺服器 (例如在開發機器上執行的 IIS)，請將下列檔案複製到本機網頁伺服器中的新目錄。 以下路徑是您可以在所下載的程式碼中找到這些檔案的位置。

* *views/index.ejs* (將尾碼變更為 .html)
* *views/jwt.ejs* (將尾碼變更為 .html)
* *views/info.ejs* (將尾碼變更為 html)
* *public/* * (JavaScript 檔案、CSS、映像，如下所示)

1. 將 *view* 資料夾中找到的檔案複製到新目錄的根目錄。
1. 將 *public* 資料夾中找到的*資料夾*複製到新目錄的根目錄。
1. 將 `.ejs` 檔案的副檔名變更為 `.html`。 (未使用伺服器端變數，因此您可以安全地加以變更)。
1. 在 VSC (或其他程式碼編輯器) 中開啟 *index.html*，然後變更 `<script>` 和 `<link>` 路徑，使其反映檔案所在的位置。  如果您有遵循前面的步驟來進行，則只需要刪除路徑中的 `\`。  例如，`<script type="text/javascript" src="/javascript/constants.js"></script>` 會成為 `<script type="text/javascript" src="javascript/constants.js"></script>`。
1. 和選項 1 一樣，自訂 *javascript/constants.js* 檔案中的常數。

## <a name="common-customer-scenarios"></a>常見客戶案例

您已完成本教學課程並擁有運作中的子系統，因此接下來可以嘗試加以修改以符合下列客戶案例：

### <a name="role-based-access-control-rbac-for-license-delivery-via-azure-ad-group-membership"></a>透過 Azure AD 群組成員資格來傳遞授權的角色型存取控制 (RBAC)

到目前為止，系統允許任何可以登入的使用者取得有效的授權，並播放受保護的內容。

客戶常有這樣的需求，要讓已驗證的使用者子集能夠觀看內容，其他使用者則不行，例如，針對其影片內容提供基本和進階訂閱的客戶。 付費獲得基本訂閱的客戶不能夠觀看需要進階訂閱的內容。 以下是要符合這項需求所需進行的額外步驟：

#### <a name="set-up-the-azure-ad-tenant"></a>設定 Azure AD 租用戶

1. 在您的租用戶中設定兩個帳戶。 這些帳戶可以命名為 *premium_user* 和 *basic_user*；
1. 建立使用者群組，並將其命名為 *PremiumGroup*。
1. 將 *premium_user* 新增至 *PremiumGroup* 作為成員，但不要將 *basic_user* 新增至群組。
1. 記下 *PremiumGroup* 的**物件識別碼**。

#### <a name="set-up-the-media-services-account"></a>設定媒體服務帳戶

藉由新增名為 *groups* 的宣告來修改 `ContentKeyPolicyRestriction` (如上一節中的＜在媒體服務帳戶中設定＞所示)，其中 `ida_EntitledGroupObjectId` 以物件識別碼 *PremiumGroup* 作為其值：

```dotnetcli

var tokenClaims = new ContentKeyPolicyTokenClaim[] { new ContentKeyPolicyTokenClaim("groups", ConfigAccessor.AppSettings["ida_EntitledGroupObjectId"])
//, other claims, if any.
};

if (tokenClaims != null && tokenClaims.Length > 0)
{
     objContentKeyPolicyTokenRestriction.RequiredClaims = new List<ContentKeyPolicyTokenClaim>(tokenClaims);
}
```

*groups* 宣告是 Azure AD 中[受限制的宣告集](../../active-directory/develop/active-directory-claims-mapping.md#claim-sets)的成員。

#### <a name="test"></a>測試

1. 使用 *premium_user* 帳戶登入。 您應該能夠播放受保護的內容。
1. 使用 *basic_user* 帳戶登入。 您應該會收到錯誤指出影片已加密，但沒有金鑰可將其解密。 如果您在播放器診斷重疊底部的下拉式清單中檢視事件、錯誤和下載，錯誤訊息應該會指出授權取得失敗，因為在 Azure AD 權杖端點所發出的 JWT 中，缺少群組宣告的宣告值。

### <a name="supporting-multiple-media-service-accounts-across-multiple-subscriptions"></a>支援多個媒體服務帳戶 (跨多個訂用帳戶)

客戶可以在單一或多個 Azure 訂用帳戶中擁有多個媒體服務帳戶。 例如，客戶可能會有一個媒體服務帳戶作為生產環境的主要帳戶，另一個帳戶作為次要帳戶/備份，以及另一個帳戶用於開發/測試。

您只需要確定在所有媒體服務帳戶中建立 `ContentKeyPolicyRestriction` 時，所使用的參數與您在＜在媒體服務帳戶中設定＞一節中所使用的參數是同一組即可。

### <a name="supporting-a-customer-its-vendors-andor-subsidiaries-across-multiple-aad-tenants"></a>跨多個 AAD 租用戶支援客戶、其廠商和/或分公司

身為解決方案的使用者，客戶的分公司、廠商/合作夥伴可能位於不同的 AAD 租用戶，例如 `mycustomer.com`、`mysubsidiary.com` 和 `myparther.com`。 雖然此解決方案建置在單一的特定 AAD 租用戶之上，例如 `mycustomer.com`，但您也可以讓其可供其他租用戶的使用者使用。

針對此解決方案使用 `mycustomer.com`，將來自 `mypartner.com` 的使用者以來賓使用者的形式新增至 `mycustomer.com` 租用戶。 請確定 `mypartner.com` 使用者會啟用來賓帳戶。 來賓帳戶可以來自另一個 AAD 租用戶，也可以來自 `outlook.com` 帳戶。

請注意，來自 `mypartner.com` 的來賓使用者在 `mycustomer.com` 中啟用之後，仍會透過其本身/原始的 AAD 租用戶 `mypartner.com` 進行驗證，但 `access_token` 則會由 `mycustomer.com` 核發。

### <a name="supporting-a-customer-tenantsubscription-with-a-setup-in-your-subscriptiontenant"></a>使用訂用帳戶/租用戶中的設定來支援客戶租用戶/訂用帳戶

您可以使用您的設定，在客戶的媒體服務帳戶/訂用帳戶中測試受保護的內容。 您會使用相同訂用帳戶中的 Azure AD 租用戶和媒體服務帳戶來進行設定。 客戶的媒體服務帳戶會在其 Azure 訂用帳戶中，並具有自己的 Azure AD 租用戶。

1. 將客戶的帳戶新增到租用戶中作為來賓帳戶。
1. 藉由提供三個參數 (如＜在媒體服務帳戶中設定＞一節所列)，與客戶合作在客戶的媒體服務帳戶中準備好受保護的內容。

接著，客戶可以瀏覽至您的設定，使用來賓帳戶登入，並測試自己的受保護內容。 您也可以使用自己的帳戶登入，並測試客戶的內容。

您的範例解決方案可能會在具有 Microsoft 訂用帳戶的 Microsoft 租用戶中或具有 Microsoft 訂用帳戶的自訂租用戶中進行設定。 Azure 媒體服務執行個體可以來自具有其租用戶的另一個訂用帳戶。

## <a name="clean-up-resources"></a>清除資源

> [!WARNING]
> 如果您不打算繼續使用此應用程式，請刪除遵循此教學課程時所建立的資源。 否則，您必須支付這些資源的費用。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [快速入門：加密內容](encrypt-content-quickstart.md)
