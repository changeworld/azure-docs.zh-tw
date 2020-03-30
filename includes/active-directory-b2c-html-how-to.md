---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 03/19/2020
ms.author: mimart
ms.openlocfilehash: af11283f9e9dbd925ec994dcb1d96393332b90fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117200"
---
## <a name="use-custom-page-content"></a>使用自訂頁面內容

您可以使用頁面 UI 自訂功能，自訂任何自訂原則的外觀與風格。 您也可以維護應用程式與 Azure AD B2C 之間的品牌和視覺一致性。

### <a name="how-it-works"></a>運作方式

Azure AD B2C 使用[跨源資源分享 （CORS）](https://www.w3.org/TR/cors/)在客戶的瀏覽器中運行代碼。 在運行時，內容從您在使用者流或自訂策略中指定的 URL 載入。 使用者體驗中的每個頁面都從您為該頁面指定的 URL 載入其內容。 從 URL 載入內容後，該內容將與 Azure AD B2C 插入的 HTML 片段合併，然後將頁面顯示給客戶。

![自訂頁面內容邊距](./media/active-directory-b2c-html-templates/html-content-merging.png)

## <a name="custom-html-page-content"></a>自訂 HTML 頁面內容

創建具有您自己的品牌的 HTML 頁面，以提供自訂頁面內容。 此頁面可以是靜態`*.html`頁面，也可以是動態頁面，如 .NET、Node.js 或 PHP。

自訂頁面內容可以包含任何 HTML 元素，包括 CSS 和 JavaScript，但不能包含不安全元素（如 iframe）。 唯一需要的元素是設置為`id``api`的 div 元素，例如 HTML`<div id="api"></div>`頁中的此元素。

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

您可以自訂 Azure AD B2C 的預設頁面內容，而不是從頭開始創建自訂頁面內容。

下表列出了 Azure AD B2C 提供的預設頁面內容。 下載檔案並將其用作創建您自己的自訂頁面的起點。

| 預設頁面 | 描述 | 內容定義識別碼<br/>（僅限自訂策略） |
|:-----------------------|:--------|-------------|
| [例外.html](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **錯誤頁**。 在發生例外狀況或錯誤時，系統會顯示此頁面。 | api.error** |
| [selfasserted.html](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) |  **自我斷言頁**。 使用此檔作為社交帳戶註冊頁面、本地帳戶註冊頁面、本地帳戶登錄頁面、密碼重設等的自訂頁面內容。 此表單可以包含各種輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 | *api.本地帳戶signin，* *api.本地帳戶註冊*， *api.local帳戶密碼重設*， *api.自我斷言* |
| [多因素-1.0.0.html](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **Multi-Factor Authentication 頁面**。 在此頁面上，使用者可以在註冊或登入期間驗證其電話號碼 (藉由使用文字或語音)。 | *api.phonefactor* |
| [updateprofile.html](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **設定檔更新頁面**。 此頁面包含一份表單，使用者可用來更新其設定檔。 此頁面類似於社交帳戶註冊頁面，但密碼輸入欄位除外。 | *api.selfasserted.profileupdate* |
| [unified.html](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統一的註冊或登入頁面**。 此頁面可處理使用者的註冊和登入程序。 使用者可以使用企業識別提供者、社交識別提供者 (如 Facebook 或 Google+) 或本機帳戶。 | *api.signuporsignin* |

## <a name="hosting-the-page-content"></a>託管頁面內容

使用自己的 HTML 和 CSS 檔自訂 UI 時，請在支援 CORS 的任何公開可用的 HTTPS 終結點上託管 UI 內容。 例如[，Azure Blob](../articles/storage/blobs/storage-blobs-introduction.md)存儲[、Azure 應用服務](/azure/app-service/)、Web 服務器、CDN、AWS S3 或檔共用系統。

## <a name="guidelines-for-using-custom-page-content"></a>使用自訂頁面內容的指南

- 在 HTML 檔案中包含媒體、CSS 和 JavaScript 檔等外部資源時，請使用絕對 URL。
- 使用[頁面配置版本](../articles/active-directory-b2c/page-layout.md)1.2.0 及以上，可以在`data-preload="true"`HTML 標籤中添加屬性以控制 CSS 和 JavaScript 的載入順序。 使用`data-preload=true`時，將構造頁面，然後再向使用者顯示。 此屬性通過預載入 CSS 檔，而不向使用者顯示未樣式的 HTML，有助於防止頁面"閃爍"。 以下 HTML 程式碼片段顯示了標記的使用`data-preload`。
  ```HTML
  <link href="https://path-to-your-file/sample.css" rel="stylesheet" type="text/css" data-preload="true"/>
  ```
- 我們建議您從預設頁面內容開始，並在它之上構建。
- 您可以在自訂內容中包括 JavaScript，用於[使用者流](../articles/active-directory-b2c/user-flow-javascript-overview.md)和[自訂策略](../articles/active-directory-b2c/javascript-samples.md)。
- 支援的瀏覽器版本包括︰
  - 互聯網瀏覽器 11、 10 和微軟邊緣
  - 對 Internet Explorer 9 和 8 僅提供有限支援
  - Google Chrome 42.0 和更新版本
  - Mozilla Firefox 38.0 和更新版本
  - 適用于 iOS 和 macOS 的 Safari，版本 12 及以上
- 由於安全限制，Azure AD B2C 不支援`frame`，`iframe`或`form`HTML 元素。

## <a name="custom-page-content-walkthrough"></a>自訂頁面內容演練

以下是流程概述：

1. 準備一個位置來承載自訂頁面內容（可公開訪問的、支援 CORS 的 HTTPS 終結點）。
1. 下載並自訂預設頁面內容檔，例如`unified.html`。
1. 發佈自訂頁面內容，以公開可用的 HTTPS 終結點。
1. 為您的 Web 應用程式設定跨原始來源資源分享 (CORS)。
1. 將策略指向自訂策略內容 URI。

### <a name="1-create-your-html-content"></a>1. 創建 HTML 內容

在標題中使用產品的品牌名稱創建自訂頁面內容。

1. 複製下列 HTML 程式碼片段。 它是格式良好的 HTML5，具有一個稱為*\<div\>\<id_"api"/div\>* 的空元素，位於*\<正文\>* 標記中。 這個元素指出要插入 Azure AD B2C 內容的地方。

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
> 如果使用login.microsoftonline.com，則由於安全限制，HTML 表單元素將被刪除。 如果要在自訂 HTML 內容中使用 HTML 表單元素，[請使用b2clogin.com](../articles/active-directory-b2c/b2clogin.md)。

### <a name="2-create-an-azure-blob-storage-account"></a>2. 創建 Azure Blob 存儲帳戶

在本文中，我們使用 Azure Blob 儲存體來裝載內容。 您可以選擇在 Web 伺服器上裝載您的內容，但必須[在 Web 伺服器上啟用 CORS](https://enable-cors.org/server.html)。

要在 Blob 存儲中託管 HTML 內容，請執行以下步驟：

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在 **"中心"** 功能表上，選擇 **"新建** > **存儲** > **存儲帳戶**"。
1. 為存儲帳戶選擇**訂閱**。
1. 創建資源組或選擇現有**資源組**。
1. 輸入存儲帳戶的唯一**名稱**。
1. 選擇存儲帳戶的**地理位置**。
1. [部署模型]**** 可保持為 [資源管理員]****。
1. [效能]**** 可保持為 [標準]****。
1. 將 [帳戶類型]**** 變更為 [Blob 儲存體]****。
1. [複寫]**** 可保持為 [RA-GRS]****。
1. [存取層]**** 可保持為 [經常性存取]****。
1. 選擇 **"查看 + 創建**"以創建存儲帳戶。
    部署完成後，**將自動打開存儲帳戶**頁。

#### <a name="21-create-a-container"></a>2.1 創建容器

要在 Blob 存儲中創建公共容器，請執行以下步驟：

1. 在左側功能表中的**Blob 服務**下，選擇**Blob**。
1. 選擇 **+容器**。
1. 對於**名稱**，請輸入*根*。 名稱可以是您選擇的名稱，例如*contoso*，但為了簡單起見，我們在此示例中使用*根*。
1. 對於**公共存取層級**，選擇**Blob，** 然後**選擇"確定**"。
1. 選擇**root**以打開新容器。

#### <a name="22-upload-your-custom-page-content-files"></a>2.2 上傳自訂頁面內容檔

1. 選取 [上傳]****。
1. 選擇 **"選擇檔"** 旁邊的資料夾圖示。
1. 導航到並選擇**自訂-ui.html**，您之前在"頁面 UI 自訂"部分中創建。
1. 如果要上載到子資料夾，請展開 **"高級"，** 並在 **"上載到"資料夾中**輸入資料夾名稱。
1. 選取 [上傳]****。
1. 選擇您上傳的**自訂-ui.html** blob。
1. 在**URL**文字方塊的右側，選擇"**複製到剪貼簿**"圖示以將 URL 複製到剪貼簿。
1. 在 Web 瀏覽器中，導航到複製的 URL 以驗證您上傳的 Blob 是可訪問的。 如果無法訪問它，例如，如果遇到`ResourceNotFound`錯誤，請確保容器訪問類型設置為**blob**。

### <a name="3-configure-cors"></a>3. 配置 CORS

通過執行以下步驟為跨源資源分享配置 Blob 存儲：

1. 在功能表中，選取 [CORS]****。
1. 針對 [允許的來源]****，輸入 `https://your-tenant-name.b2clogin.com`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 例如： `https://fabrikam.b2clogin.com` 。 輸入租戶名稱時，請使用所有小寫字母。
1. 針對 [允許的方法]****，選取 `GET` 和 `OPTIONS`。
1. 針對 [允許的標頭]****，輸入星號 (*)。
1. 針對 [公開的標頭]****，輸入星號 (*)。
1. 針對 [最大壽命]****，輸入 200。
1. 選取 [儲存]****。

#### <a name="31-test-cors"></a>3.1 測試 CORS

通過執行以下步驟驗證您是否已準備就緒：

1. 重複配置 CORS 步驟。 對於**允許的源**，輸入`https://www.test-cors.org`
1. 導航到[www.test-cors.org](https://www.test-cors.org/) 
1. 對於 **"遠端 URL"** 框，請粘貼 HTML 檔案的 URL。 例如， `https://your-account.blob.core.windows.net/azure-ad-b2c/unified.html`
1. 選擇 **"發送請求**"。
    結果應為`XHR status: 200`。 
    如果您收到錯誤，請確定您的 CORS 設定正確無誤。 您也可能需要清除瀏覽器快取，或按 Ctrl+Shift+P 來開啟 InPrivate 瀏覽工作階段。
