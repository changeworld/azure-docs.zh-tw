---
title: 自訂使用者介面
titleSuffix: Azure AD B2C
description: 瞭解如何為使用 Azure Active Directory B2C 的應用程式自訂使用者介面。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 869cf5a47831844b04e0461a95fb7d16aa4d1569
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97111192"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中自訂使用者介面

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

商標和自訂 Azure Active Directory B2C (Azure AD B2C) 向客戶顯示的使用者介面，可協助您在應用程式中提供順暢的使用者體驗。 這些體驗包括註冊、登入、設定檔編輯和密碼重設。 本文介紹 (UI) 自訂的使用者介面方法。 

> [!TIP]
> 如果您只想要修改使用者流程頁面的橫幅標誌、背景影像和背景色彩，您可以嘗試 [公司品牌](company-branding.md) 功能。


## <a name="custom-html-and-css-overview"></a>自訂 HTML 和 CSS 總覽


Azure AD B2C 在客戶的瀏覽器中使用 [跨原始資源分享 (CORS) ](https://www.w3.org/TR/cors/)來執行程式碼。 在執行時間，會從您在使用者流程或自訂原則中指定的 URL 載入內容。 使用者體驗中的每個頁面都會從您為該頁面指定的 URL 載入其內容。 從您的 URL 載入內容之後，它會與 Azure AD B2C 所插入的 HTML 片段合併，然後向您的客戶顯示該頁面。

![自訂頁面內容邊界](./media/customize-ui-with-html/html-content-merging.png)

### <a name="custom-html-page-content"></a>自訂 HTML 網頁內容

使用您自己的商標建立 HTML 網頁，以提供您的自訂頁面內容。 此頁面可以是靜態 `*.html` 頁面，也可以是動態頁面，例如 .net、Node.js 或 PHP。

您的自訂頁面內容可以包含任何 HTML 專案（包括 CSS 和 JavaScript），但不能包含不安全的元素，例如 iframe。 唯一必要的元素是設定為的 div 元素 `id` `api` ，例如在您的 `<div id="api"></div>` HTML 網頁中。

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Product Brand Name</title>
</head>
<body>
    <div id="api"></div>
</body>
</html>
```

#### <a name="customize-the-default-azure-ad-b2c-pages"></a>自訂預設的 Azure AD B2C 頁面

您可以自訂 Azure AD B2c 預設頁面內容，而不需要從頭開始建立自訂頁面內容。

下表列出 Azure AD B2C 所提供的預設頁面內容。 下載這些檔案，並使用它們做為建立您專屬自訂頁面的起點。

| 預設頁面 | 說明 | 內容定義識別碼<br/>僅 (自訂原則)  |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **錯誤頁面**。 在發生例外狀況或錯誤時，系統會顯示此頁面。 | api.error |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **自我判斷頁面**。 使用此檔案作為社交帳戶註冊頁面、本機帳戶註冊頁面、本機帳戶登入頁面、密碼重設等的自訂頁面內容。 此表單可以包含各種輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 | *localaccountsignin*、api. *localaccountsignup*、 *localaccountpasswordreset*、 *api. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 頁面**。 在此頁面上，使用者可以在註冊或登入期間驗證其電話號碼 (藉由使用文字或語音)。 | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **設定檔更新頁面**。 此頁面包含一份表單，使用者可用來更新其設定檔。 此頁面類似於社交帳戶註冊頁面，但密碼輸入欄位除外。 | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統一的註冊或登入頁面**。 此頁面可處理使用者的註冊和登入程序。 使用者可以使用企業識別提供者、社交識別提供者 (如 Facebook 或 Google+) 或本機帳戶。 | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>裝載頁面內容

使用您自己的 HTML 和 CSS 檔案自訂 UI 時，請將 UI 內容裝載于任何支援 CORS 的公開可用 HTTPS 端點上。 例如， [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)、 [Azure App 服務](../app-service/index.yml)、網頁伺服器、cdn、AWS S3 或檔案共用系統。

## <a name="guidelines-for-using-custom-page-content"></a>使用自訂頁面內容的指導方針

- 當您在 HTML 檔案中包含媒體、CSS 和 JavaScript 檔案等外部資源時，請使用絕對 URL。
- 使用 [頁面配置版本](page-layout.md) 1.2.0 和更新版本時，您可以 `data-preload="true"` 在 HTML 標籤中加入屬性，以控制 CSS 和 JavaScript 的載入順序。 使用時 `data-preload=true` ，會在向使用者顯示頁面之前先建立頁面。 這個屬性可透過預先載入 CSS 檔案來防止頁面「閃爍」，而不會對使用者顯示未設樣式的 HTML。 下列 HTML 程式碼片段會示範如何使用 `data-preload` 標記。
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 我們建議您從預設頁面內容開始，並在其上建立。
- 您可以在自訂內容中 [包含 JavaScript](javascript-and-page-layout.md) 。
- 支援的瀏覽器版本包括︰
  - Internet Explorer 11、10和 Microsoft Edge
  - 對 Internet Explorer 9 和 8 僅提供有限支援
  - Google Chrome 42.0 和更新版本
  - Mozilla Firefox 38.0 和更新版本
  - 適用于 iOS 和 macOS 的 Safari，第12版和更新版本
- 由於安全性限制，Azure AD B2C 不支援 `frame` 、 `iframe` 或 `form` HTML 元素。

## <a name="localize-content"></a>當地語系化內容

您可以在 Azure AD B2C 租用戶上啟用[語言自訂](language-customization.md)，以將 HTML 內容當地語系化。 啟用這項功能可讓 Azure AD B2C 將 OpenID Connect 參數轉送 `ui_locales` 至您的端點。 內容伺服器可使用此參數來提供語言特定的 HTML 頁面。

您可以根據所使用的地區設定，從不同的地方提取內容。 在已啟用 CORS 的端點中，您可以針對特定語言設定主機內容的資料夾結構。 如果您使用萬用字元值 `{Culture:RFC5646}`，則會呼叫正確的語言。

例如，您的自訂頁面 URI 可能看起來像這樣：

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

您可以從下列來源提取內容，以法文載入頁面：

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="custom-page-content-walkthrough"></a>自訂頁面內容逐步解說

以下是流程的總覽：

1. 準備一個位置來裝載自訂頁面內容 (可公開存取、已啟用 CORS 的 HTTPS 端點) 。
1. 下載並自訂預設頁面內容檔案，例如 `unified.html` 。
1. 將您的自訂頁面內容發佈至公開可用的 HTTPS 端點。
1. 為您的 Web 應用程式設定跨原始來源資源分享 (CORS)。
1. 將您的原則指向您的自訂原則內容 URI。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


### <a name="1-create-your-html-content"></a>1. 建立您的 HTML 內容

使用您產品在標題中的品牌名稱來建立自訂頁面內容。

1. 複製下列 HTML 程式碼片段。 它是格式正確的 HTML5，其中有一個名為的空白元素， *\<div id="api"\>\</div\>* 位於 *\<body\>* 標記內。 這個元素指出要插入 Azure AD B2C 內容的地方。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

1. 在文字編輯器中貼上複製的程式碼片段
1. 使用 CSS 為 Azure AD B2C 在您的頁面中插入的 UI 元素設定樣式。 下列範例顯示簡單的 CSS 檔案，其中也包含註冊插入 HTML 元素的設定：

    ```css
    h1 {
      color: blue;
      text-align: center;
    }
    .intro h2 {
      text-align: center;
    }
    .entry {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    .divider h2 {
      text-align: center;
    }
    .create {
      width: 400px ;
      margin-left: auto ;
      margin-right: auto ;
    }
    ```

1.  將檔案儲存為 *customize-ui.html*。

> [!NOTE]
> 如果您使用 login.microsoftonline.com，HTML 表單元素將會因為安全性限制而移除。 如果您想要在自訂 HTML 內容中使用 HTML 表單元素，請 [使用 b2clogin.com](b2clogin.md)。

### <a name="2-create-an-azure-blob-storage-account"></a>2. 建立 Azure Blob 儲存體帳戶

在本文中，我們使用 Azure Blob 儲存體來裝載內容。 您可以選擇在 Web 伺服器上裝載您的內容，但必須[在 Web 伺服器上啟用 CORS](https://enable-cors.org/server.html)。

若要在 Blob 儲存體中裝載您的 HTML 內容，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 [**中樞**] 功能表上，選取 [**新增**  >  **儲存體**  >  **儲存體帳戶**]。
1. 選取儲存體帳戶的 **訂** 用帳戶。
1. 建立資源群組，或選取現有的 **資源群組** 。
1. 輸入儲存體帳戶的唯一 **名稱** 。
1. 選取儲存體帳戶的 **地理位置** 。
1. [部署模型] 可保持為 [資源管理員]。
1. [效能] 可保持為 [標準]。
1. 將 [帳戶類型] 變更為 [Blob 儲存體]。
1. [複寫] 可保持為 [RA-GRS]。
1. [存取層] 可保持為 [經常性存取]。
1. 選取 [ **審核 + 建立** ] 以建立儲存體帳戶。
    部署完成後，[ **儲存體帳戶** ] 頁面會自動開啟。

#### <a name="21-create-a-container"></a>2.1 建立容器

若要在 Blob 儲存體中建立公用容器，請執行下列步驟：

1. 在左側功能表的 [ **blob 服務** ] 底下，選取 [ **blob**]。
1. 選取 [ **+ 容器**]。
1. 針對 [ **名稱**]，輸入 *root*。 名稱可以是您選擇的名稱（例如 *contoso*），但為了簡單起見，我們使用此範例中的 *根* 。
1. 若為 **公用存取層級**，請選取 [ **Blob**]，然後選取 **[確定]**。
1. 選取 [ **根** ] 以開啟新的容器。

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 上傳您的自訂頁面內容檔案

1. 選取 [上傳] 。
1. 選取 [ **選取** 檔案] 旁的資料夾圖示。
1. 流覽至並選取您稍早在 [頁面 UI 自訂] 區段中建立的 **customize-ui.html**。
1. 如果您想要上傳至子資料夾，請展開 [ **Advanced** ]，然後在 **[上傳至資料夾**] 中輸入資料夾名稱。
1. 選取 [上傳] 。
1. 選取您上傳的 **customize-ui.html** blob。
1. 選取 [ **URL** ] 文字方塊右邊的 [ **複製到剪貼** 簿] 圖示，將 URL 複製到剪貼簿。
1. 在網頁瀏覽器中，流覽至您所複製的 URL，以確認您上傳的 blob 可以存取。 如果無法存取，例如如果您遇到 `ResourceNotFound` 錯誤，請確定容器的存取類型設定為 **blob**。

### <a name="3-configure-cors"></a>3. 設定 CORS

執行下列步驟，以設定跨原始來源資源分享的 Blob 儲存體：

1. 在功能表中，選取 [CORS]。
1. 針對 [允許的來源]，輸入 `https://your-tenant-name.b2clogin.com`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 例如： `https://fabrikam.b2clogin.com` 。 輸入您的租使用者名稱時，全部使用小寫字母。
1. 針對 [允許的方法]，選取 `GET` 和 `OPTIONS`。
1. 針對 [允許的標頭]，輸入星號 (*)。
1. 針對 [公開的標頭]，輸入星號 (*)。
1. 針對 [最大壽命]，輸入 200。
1. 選取 [儲存]。

#### <a name="31-test-cors"></a>3.1 測試 CORS

藉由執行下列步驟來驗證您是否已準備就緒：

1. 重複「設定 CORS」步驟。 針對 [ **允許的來源**]，輸入 `https://www.test-cors.org`
1. 流覽至 [www.test-cors.org](https://www.test-cors.org/) 
1. 在 [ **遠端 URL** ] 方塊中，貼上 HTML 檔案的 url。 例如， `https://your-account.blob.core.windows.net/root/azure-ad-b2c/unified.html`
1. 選取 [ **傳送要求**]。
    結果應該是 `XHR status: 200` 。 
    如果您收到錯誤，請確定您的 CORS 設定正確無誤。 您也可能需要清除瀏覽器快取，或按 Ctrl+Shift+P 來開啟 InPrivate 瀏覽工作階段。


::: zone pivot="b2c-user-flow"

### <a name="4-update-the-user-flow"></a>4. 更新使用者流程

1. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
1. 選取 [使用者流程]，然後選取 *B2C_1_signupsignin1* 使用者流程。
1. 選取 [頁面配置]，然後針對 [統一註冊或登入頁面] 底下的 [使用自訂頁面內容] 按一下 [是]。
1. 在 [自訂頁面 URI] 中，輸入您先前記下的 custom-ui.html 檔案 URI。
1. 在頁面上方選取 [儲存]。

### <a name="5-test-the-user-flow"></a>5. 測試使用者流程

1. 在 Azure AD B2C 租用戶中，選取 [使用者流程]，然後選取 B2C_1_signupsignin1 使用者流程。
1. 按一下頁面頂端的 [執行使用者流程]。
1. 按一下 [執行使用者流程]  按鈕。

您應該會看到類似下列範例的頁面，而頁面上的置中元素會以您所建立的 CSS 檔案為基礎：

![網頁瀏覽器顯示具有自訂 UI 元素的註冊或登入頁面](./media/customize-ui-with-html/run-now.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="4-modify-the-extensions-file"></a>4. 修改擴充檔

若要設定 UI 自訂，請將 **ContentDefinition** 及其子項目從基底檔案複製到延伸模組檔案。

1. 開啟原則的基底檔案。 例如，<em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>。 此基底檔案是自訂原則入門套件中所含的其中一個原則檔案，您應該在必要條件中取得該檔案，以 [開始使用自訂](./custom-policy-get-started.md)原則。
1. 搜尋 **ContentDefinitions** 元素的完整內容並且複製。
1. 開啟擴充檔案。 例如 TrustFrameworkExtensions.xml。 搜尋 **BuildingBlocks** 元素。 如果此元素不存在，請加以新增。
1. 貼上您複製的 **ContentDefinitions** 元素的整個內容，做為 **BuildingBlocks** 元素的子系。
1. 在您複製的 XML 中，搜尋包含 `Id="api.signuporsignin"` 的 **ContentDefinition** 元素。
1. 將 **LoadUri** 的值變更為您上傳至儲存體的 HTML 檔案 URL。 例如： `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html` 。

    您的自訂原則看起來應該如下列程式碼片段所示：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. 儲存擴充檔案。

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. 上傳並測試已更新的自訂原則

#### <a name="51-upload-the-custom-policy"></a>5.1 上傳自訂原則

1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 搜尋並選取 [Azure AD B2C]。
1. 在 [原則] 之下，選取 [Identity Experience Framework]。
1. 選取 [上傳自訂原則]。
1. 上傳您先前變更的擴充檔案。

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 使用 [**立即執行**] 測試自訂原則

1. 選取您上傳的原則，然後選取 [立即執行]。
1. 您應該可以使用電子郵件地址註冊。

## <a name="configure-dynamic-custom-page-content-uri"></a>設定動態自訂頁面內容 URI

藉由使用 Azure AD B2C 自訂原則，您可以在 URL 路徑或查詢字串中傳送參數。 將參數傳遞至您的 HTML 端點，即可動態變更網頁內容。 例如，視您從 Web 或行動裝置應用程式傳遞的參數而定，您可以變更 Azure AD B2C 註冊或登入背景影像。 參數可以是任何宣告 [解析](claim-resolver-overview.md)程式，例如應用程式識別碼、語言識別項或自訂查詢字串參數（例如） `campaignId` 。

### <a name="sending-query-string-parameters"></a>傳送查詢字串參數

若要傳送查詢字串參數，請在信賴憑證者 [原則](relyingparty.md)中新增 `ContentDefinitionParameters` 元素，如下所示。

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

在內容定義中，將的值變更 `LoadUri` 為 `https://<app_name>.azurewebsites.net/home/unified` 。 您的自訂原則 `ContentDefinition` 看起來應該如下列程式碼片段所示：

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

當 Azure AD B2C 載入頁面時，它會呼叫您的 web 伺服器端點：

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>動態頁面內容 URI

您可以根據所使用的參數，從不同的位置提取內容。 在啟用 CORS 的端點中，設定用來裝載內容的資料夾結構。 例如，您可以將內容組織成下列結構。 每一語言的根 *資料夾/資料夾/您的 html* 檔案。 例如，您的自訂頁面 URI 可能看起來像這樣：

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C 傳送語言的兩個字母 ISO 代碼， `fr` 為法文：

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

::: zone-end

## <a name="sample-templates"></a>範例範本

您可以在此找到 UI 自訂的範例範本：

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

此專案包含下列範本：
- [海洋藍](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [石板灰色](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

若要使用範例：

1. 在本機電腦上複製存放庫。 選擇範本資料夾 `/ocean_blue` 或 `/slate_gray` 。
1. 將範本資料夾和資料夾下的所有檔案上傳 `/assets` 至 Blob 儲存體（如上一節所述）。
1. 接下來，開啟 `\*.html` 或的根目錄中的每個檔案 `/ocean_blue` `/slate_gray` ，將相對 url 的所有實例取代為您在步驟2中上傳的 css、影像和字型檔案的 url。 例如：
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    收件者
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. 儲存檔案 `\*.html` ，並將它們上傳至 Blob 儲存體。
1. 現在修改原則，指向您的 HTML 檔案（如先前所述）。
1. 如果您看到遺漏字型、影像或 CSS，請檢查延伸模組原則和 .html 檔案中的參考 \* 。


## <a name="next-steps"></a>後續步驟

瞭解如何啟用 [用戶端 JavaScript 程式碼](javascript-and-page-layout.md)。



