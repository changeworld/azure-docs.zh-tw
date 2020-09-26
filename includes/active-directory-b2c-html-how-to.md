---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: 9b660bf20c90a84780175e70573c96a0ce1b0b7d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377553"
---
## <a name="use-custom-page-content"></a>使用自訂頁面內容

您可以使用頁面 UI 自訂功能，自訂任何自訂原則的外觀與風格。 您也可以維護應用程式與 Azure AD B2C 之間的品牌和視覺一致性。

### <a name="how-it-works"></a>運作方式

Azure AD B2C 在客戶的瀏覽器中使用 [跨原始資源分享 (CORS) ](https://www.w3.org/TR/cors/)來執行程式碼。 在執行時間，會從您在使用者流程或自訂原則中指定的 URL 載入內容。 使用者體驗中的每個頁面都會從您為該頁面指定的 URL 載入其內容。 從您的 URL 載入內容之後，它會與 Azure AD B2C 所插入的 HTML 片段合併，然後向您的客戶顯示該頁面。

![自訂頁面內容邊界](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>自訂 HTML 網頁內容

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

### <a name="customize-the-default-azure-ad-b2c-pages"></a>自訂預設的 Azure AD B2C 頁面

您可以自訂 Azure AD B2c 預設頁面內容，而不需要從頭開始建立自訂頁面內容。

下表列出 Azure AD B2C 所提供的預設頁面內容。 下載這些檔案，並使用它們做為建立您專屬自訂頁面的起點。

| 預設頁面 | 說明 | 內容定義識別碼<br/>僅 (自訂原則)  |
|:-----------------------|:--------|-------------|
| [exception.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **錯誤頁面**。 在發生例外狀況或錯誤時，系統會顯示此頁面。 | api.error** |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **自我判斷頁面**。 使用此檔案作為社交帳戶註冊頁面、本機帳戶註冊頁面、本機帳戶登入頁面、密碼重設等的自訂頁面內容。 此表單可以包含各種輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 | *localaccountsignin*、api. *localaccountsignup*、 *localaccountpasswordreset*、 *api. selfasserted* |
| [multifactor-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 頁面**。 在此頁面上，使用者可以在註冊或登入期間驗證其電話號碼 (藉由使用文字或語音)。 | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **設定檔更新頁面**。 此頁面包含一份表單，使用者可用來更新其設定檔。 此頁面類似於社交帳戶註冊頁面，但密碼輸入欄位除外。 | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統一的註冊或登入頁面**。 此頁面可處理使用者的註冊和登入程序。 使用者可以使用企業識別提供者、社交識別提供者 (如 Facebook 或 Google+) 或本機帳戶。 | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>裝載頁面內容

使用您自己的 HTML 和 CSS 檔案自訂 UI 時，請將 UI 內容裝載于任何支援 CORS 的公開可用 HTTPS 端點上。 例如， [Azure Blob 儲存體](../articles/storage/blobs/storage-blobs-introduction.md)、 [Azure App 服務](/azure/app-service/)、網頁伺服器、cdn、AWS S3 或檔案共用系統。

## <a name="guidelines-for-using-custom-page-content"></a>使用自訂頁面內容的指導方針

- 當您在 HTML 檔案中包含媒體、CSS 和 JavaScript 檔案等外部資源時，請使用絕對 URL。
- 使用 [頁面配置版本](../articles/active-directory-b2c/page-layout.md) 1.2.0 和更新版本時，您可以 `data-preload="true"` 在 HTML 標籤中加入屬性，以控制 CSS 和 JavaScript 的載入順序。 使用時 `data-preload=true` ，會在向使用者顯示頁面之前先建立頁面。 這個屬性可透過預先載入 CSS 檔案來防止頁面「閃爍」，而不會對使用者顯示未設樣式的 HTML。 下列 HTML 程式碼片段會示範如何使用 `data-preload` 標記。
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 我們建議您從預設頁面內容開始，並在其上建立。
- 您可以在您的自訂內容中包含 [使用者流程](../articles/active-directory-b2c/user-flow-javascript-overview.md) 和 [自訂原則](../articles/active-directory-b2c/javascript-samples.md)的 JavaScript。
- 支援的瀏覽器版本包括︰
  - Internet Explorer 11、10和 Microsoft Edge
  - 對 Internet Explorer 9 和 8 僅提供有限支援
  - Google Chrome 42.0 和更新版本
  - Mozilla Firefox 38.0 和更新版本
  - 適用于 iOS 和 macOS 的 Safari，第12版和更新版本
- 由於安全性限制，Azure AD B2C 不支援 `frame` 、 `iframe` 或 `form` HTML 元素。

## <a name="custom-page-content-walkthrough"></a>自訂頁面內容逐步解說

以下是流程的總覽：

1. 準備一個位置來裝載自訂頁面內容 (可公開存取、已啟用 CORS 的 HTTPS 端點) 。
1. 下載並自訂預設頁面內容檔案，例如 `unified.html` 。
1. 將您的自訂頁面內容發佈至公開可用的 HTTPS 端點。
1. 為您的 Web 應用程式設定跨原始來源資源分享 (CORS)。
1. 將您的原則指向您的自訂原則內容 URI。

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

1. 在文字編輯器中貼上所複製的程式碼片段，然後將檔案儲存為 customize-ui.html**。

> [!NOTE]
> 如果您使用 login.microsoftonline.com，HTML 表單元素將會因為安全性限制而移除。 如果您想要在自訂 HTML 內容中使用 HTML 表單元素，請 [使用 b2clogin.com](../articles/active-directory-b2c/b2clogin.md)。

### <a name="2-create-an-azure-blob-storage-account"></a>2. 建立 Azure Blob 儲存體帳戶

在本文中，我們使用 Azure Blob 儲存體來裝載內容。 您可以選擇在 Web 伺服器上裝載您的內容，但必須[在 Web 伺服器上啟用 CORS](https://enable-cors.org/server.html)。

若要在 Blob 儲存體中裝載您的 HTML 內容，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 [**中樞**] 功能表上，選取 [**新增**  >  **儲存體**  >  **儲存體帳戶**]。
1. 選取儲存體帳戶的 **訂** 用帳戶。
1. 建立資源群組，或選取現有的 **資源群組** 。
1. 輸入儲存體帳戶的唯一 **名稱** 。
1. 選取儲存體帳戶的 **地理位置** 。
1. [部署模型]**** 可保持為 [資源管理員]****。
1. [效能]**** 可保持為 [標準]****。
1. 將 [帳戶類型]**** 變更為 [Blob 儲存體]****。
1. [複寫]**** 可保持為 [RA-GRS]****。
1. [存取層]**** 可保持為 [經常性存取]****。
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

1. 選取 [上傳]  。
1. 選取 [ **選取**檔案] 旁的資料夾圖示。
1. 流覽至並選取您稍早在 [頁面 UI 自訂] 區段中建立的 **customize-ui.html**。
1. 如果您想要上傳至子資料夾，請展開 [ **Advanced** ]，然後在 **[上傳至資料夾**] 中輸入資料夾名稱。
1. 選取 [上傳]  。
1. 選取您上傳的 **customize-ui.html** blob。
1. 選取 [ **URL** ] 文字方塊右邊的 [ **複製到剪貼** 簿] 圖示，將 URL 複製到剪貼簿。
1. 在網頁瀏覽器中，流覽至您所複製的 URL，以確認您上傳的 blob 可以存取。 如果無法存取，例如如果您遇到 `ResourceNotFound` 錯誤，請確定容器的存取類型設定為 **blob**。

### <a name="3-configure-cors"></a>3. 設定 CORS

執行下列步驟，以設定跨原始來源資源分享的 Blob 儲存體：

1. 在功能表中，選取 [CORS]。
1. 針對 [允許的來源]，輸入 `https://your-tenant-name.b2clogin.com`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 例如： `https://fabrikam.b2clogin.com` 。 輸入您的租使用者名稱時，全部使用小寫字母。
1. 針對 [允許的方法]****，選取 `GET` 和 `OPTIONS`。
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
