---
title: 自訂使用者介面
titleSuffix: Azure AD B2C
description: 瞭解如何為使用 Azure 活動目錄 B2C 的應用程式自定義使用者介面。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37ddf57057b736cd76a74276e5593a865e7df8cc
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666859"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>自訂 Azure 活動目錄 B2C 的使用者介面

將 Azure 活動目錄 B2C(Azure AD B2C) 向客戶顯示的使用者介面品牌化和自定義有助於在應用程式中提供無縫的使用者體驗。 這些體驗包括註冊、登錄、配置檔編輯和密碼重置。 本文介紹了使用者介面 (UI) 自定義方法,包括使用者流和自定義策略。

## <a name="ui-customization-in-different-scenarios"></a>不同方案中的 UI 自訂

有幾種方法可以自定義應用程式的用戶體驗的 UI,每種方法都適合不同的方案。

### <a name="user-flows"></a>使用者流程

如果使用[使用者流](user-flow-overview.md),則可以使用內置*頁面佈局範本*或使用自己的 HTML 和 CSS 來更改使用者流頁的外觀。 本文稍後將討論這兩種方法。

您可以使用[Azure 門戶](tutorial-customize-ui.md)為使用者流配置 UI 自訂項。

> [!TIP]
> 如果只想修改使用者流頁的橫幅徽標、背景圖像和背景顏色,可以嘗試本文後面介紹[的公司品牌(預覽)](#company-branding-preview)功能。

### <a name="custom-policies"></a>自訂原則

如果使用[自訂策略](custom-policy-overview.md)在應用程式中提供註冊或登入、密碼重置或設定檔編輯,請使用[策略檔自訂 UI](custom-policy-ui-customization.md)。

如果需要根據客戶的決策提供動態內容,請使用自訂策略,根據查詢字串中發送的參數動態[更改頁面內容](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)。 例如,您可以基於從 Web 或行動應用程式傳遞的參數更改 Azure AD B2C 註冊或登入頁上的背景影像。

### <a name="javascript"></a>JavaScript

您可以在[使用者流](user-flow-javascript-overview.md)和[自訂策略](page-layout.md)中啟用用戶端 JavaScript 代碼。

### <a name="sign-in-only-ui-customization"></a>只登入 UI 自訂

如果僅提供登錄及其隨附的密碼重置頁和驗證電子郵件,請使用與[Azure AD 登錄頁](../active-directory/fundamentals/customize-branding.md)相同的自定義步驟。

如果客戶嘗試在登錄之前編輯其配置檔,則他們會使用用於自定義 Azure AD 登錄頁的步驟將其重定向到您自定義的頁面。

## <a name="page-layout-templates"></a>頁面配置範本

使用者流提供了多個內建範本,您可以選擇為使用者體驗頁面提供專業外觀。 這些佈局範本還可以作為您自己的自定義的起點。

在左方選單中的 **「自訂」** 下,選擇**頁面佈局**,然後選擇**樣本**。

![Azure 門戶使用者流頁中的樣本選擇下拉清單](media/customize-ui-overview/template-selection.png)

接下來,從清單中選擇一個範本。 以下是每個樣本的登入頁範例:

| 海洋藍 | 石板灰色 | 傳統 |
|:-:|:-:|:-:|
|![註冊登錄頁面上呈現的海洋藍色範本範例](media/customize-ui-overview/template-ocean-blue.png)|![註冊登入頁面上呈現的板底灰色範本範例](media/customize-ui-overview/template-slate-gray.png)|![註冊登入頁面上呈現的經典樣本範例](media/customize-ui-overview/template-classic.png)|

選擇範本時，選取的配置將套用到使用者流程中的所有頁面，並且每一頁的 URI 會顯示在 [自訂頁面 URI]**** 欄位中。

## <a name="custom-html-and-css"></a>自訂 HTML 與 CSS

如果要使用自定義 HTML 和 CSS 設計自己的策略佈局,可以通過切換策略中每個佈局名稱的「使用自訂頁面內容」切換來執行此操作。 請按照以下有關自訂佈局設定的說明:

Azure AD B2C 使用稱為[跨源資源分享 (CORS)](https://www.w3.org/TR/cors/)的方法在客戶的瀏覽器中運行代碼。

在運行時,內容從您在使用者流或自定義策略中指定的 URL 載入。 使用者體驗中的每個頁面都從您為該頁面指定的 URL 載入其內容。 從網址載入內容後,該內容將與 Azure AD B2C 插入的 HTML 片段合併,然後將頁面顯示給客戶。

在使用自己的 HTML 與 CSS 檔自訂 UI 之前,請查看以下指南:

- Azure AD B2C 將 HTML 內容**合併**到您的頁面中。 請勿複製及嘗試變更 Azure AD B2C 所提供的預設內容。 最好是從頭建置您的 HTML 內容，將預設範本當作參考即可。
- **JavaScript**可以包含在自訂內容中,用於[使用者流](user-flow-javascript-overview.md), 並[自訂政策](javascript-samples.md)。
- 支援的**瀏覽器版本**包括:
  - 網際網路瀏覽器 11、 10 和微軟邊緣
  - 對 Internet Explorer 9 和 8 僅提供有限支援
  - Google Chrome 42.0 和更新版本
  - Mozilla Firefox 38.0 和更新版本
  - 適用於 iOS 和 macOS 的 Safari,版本 12 及以上
- 不要在 HTML 中包含**表單標記**。 表單標記干擾 Azure AD B2C 注入的 HTML 生成的 POST 操作。

### <a name="where-do-i-store-ui-content"></a>UI 內容應儲存於何處？

使用自己的 HTML 和 CSS 檔自訂 UI 時,可以在支援 CORS 的任何公開可用的 HTTPS 終結點上託管 UI 內容。 例如[,Azure Blob 儲存](../storage/blobs/storage-blobs-introduction.md)、Web 伺服器、CDN、AWS S3 或檔案共享系統。

重要的是,在[啟用了 CORS](https://enable-cors.org/server.html)的可用 HTTPS 終結點上託管內容。 在您的內容中指定 URL 時，必須使用絕對 URL。

> [!NOTE]
> 有關創建 HTML 內容、將內容上載到 Azure Blob 儲存以及配置 CORS 的詳細資訊,請參閱 UI 自訂文章中的[自訂頁面內容演練](custom-policy-ui-customization.md#custom-page-content-walkthrough)部分。

## <a name="get-started-with-custom-html-and-css"></a>開始使用自訂 HTML 與 CSS

以以下準則,在使用者體驗頁面中開始使用您自己的 HTML 和 CSS。

- 建立格式正確的 HTML 內容，其中的空白 `<div id="api"></div>` 元素位於 `<body>` 中的某處。 這個元素會標記插入 Azure AD B2C 內容的地方。 下列範例顯示最小頁面：

    ```html
    <!DOCTYPE html>
    <html>
      <head>
        <title>!Add your title here!</title>
        <link rel="stylesheet" href="https://mystore1.blob.core.windows.net/b2c/style.css">
      </head>
      <body>
        <h1>My B2C Application</h1>
        <div id="api"></div>   <!-- Leave this element empty because Azure AD B2C will insert content here. -->
      </body>
    </html>
    ```

- 使用 CSS 為 Azure AD B2C 在您的頁面中插入的 UI 元素設定樣式。 下列範例顯示簡單的 CSS 檔案，其中也包含註冊插入 HTML 元素的設定：

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

- 將您的內容裝載於 HTTPS 端點 (允許 CORS)。 設定 CORS 時，必須同時啟用 GET 和 OPTIONS 要求方法。
- 建立或編輯使用者流或自訂策略以使用您創建的內容。

### <a name="html-fragments-from-azure-ad-b2c"></a>從 Azure AD B2C 的 HTML 片段

下表列出 Azure AD B2C 合併至 `<div id="api"></div>` 元素 (位於您的內容中) 的 HTML 片段範例。

| 插入的頁面 | HTML 的描述 |
| ------------- | ------------------- |
| 識別提供者選取項目 | 包含客戶在註冊或登入期間可選擇的識別提供者按鈕清單。 這些按鈕包括社交識別提供者 (例如 Facebook、Google) 或本機帳戶 (以電子郵件地址或使用者名稱作為基礎)。 |
| 本機帳戶註冊 | 包含可供使用者根據電子郵件地址或使用者名稱進行本機帳戶註冊的表單。 此表單可以包含不同的輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 |
| 社交帳戶註冊 | 可在使用社交識別提供者 (例如 Facebook 或 Google) 的現有帳戶註冊時顯示。 當必須使用註冊表單從客戶那裡收集其他資訊時,將使用它。 |
| 統一的註冊或登入 | 可處理客戶的註冊和登入，這些客戶可使用社交識別提供者 (例如 Facebook、Google) 或本機帳戶。 |
| Multi-Factor Authentication | 客戶可以在註冊或登入期間驗證其電話號碼 (使用文字或語音)。 |
| 錯誤 | 提供錯誤資訊給客戶。 |

## <a name="company-branding-preview"></a>公司品牌(預覽)

您可以使用 Azure 活動目錄[公司品牌](../active-directory/fundamentals/customize-branding.md),使用橫幅徽標、背景圖像和背景顏色自定義使用者流頁。

要自定義使用者流頁,請先在 Azure 活動目錄中配置公司品牌,然後在 Azure AD B2C 中的使用者流頁面佈局中啟用它。

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>設定公司商標

首先在公司**品牌**中設置橫幅徽標、背景圖像和背景顏色。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶]**** 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在 Azure 門戶中,搜尋並選擇**Azure AD B2C**。
1. 在 **"管理**"下,選擇 **"公司品牌**"。
1. 按照[將品牌添加到組織的 Azure 活動目錄登錄頁中](../active-directory/fundamentals/customize-branding.md)的步驟操作。

在 Azure AD B2C 中設定公司品牌時,請記住以下事項:

* Azure AD B2C 中的公司品牌目前僅限於**背景圖像**、**橫幅徽標**和**背景顏色**自定義。 *不支援*公司品牌窗格中的其他屬性,例如 **「高級設置**」中的屬性。
* 在使用者流頁中,背景顏色在載入背景圖像之前顯示。 我們建議您選擇與背景圖像中的顏色緊密匹配的背景顏色,以便更流暢地獲得載入體驗。
* 橫幅徽標將顯示在使用者啟動註冊用戶流時發送給用戶的驗證電子郵件中。

### <a name="enable-branding-in-user-flow-pages"></a>在使用者流頁中啟用品牌

配置公司品牌后,在使用者流中啟用它。

1. 在 Azure 門戶的左方選單中,選擇**Azure AD B2C**。
1. 在**策略**下,選擇**使用者流(策略)。**
1. 選擇要為其啟用公司品牌的使用者流。 *"登入 v1"* 和 *"設定檔編輯 v1"* 使用者流類型**不支援**公司品牌。
1. 在 **「自定義」** 下,選擇**頁面佈局**,然後選擇要打造品牌的佈局。 例如,選擇 **「統一註冊」 或 「 登入」 頁**。
1. 對於**頁面佈局版本(預覽),** 請選擇版本**1.2.0**或以上。
1. 選取 [儲存]  。

如果要為使用者流中的所有頁面設置品牌,可以設置使用者流中每個頁面布局的頁面佈局版本。

![Azure 門戶中的 Azure AD B2C 中的頁面配置選擇](media/customize-ui-overview/portal-02-page-layout-select.png)

此帶注注的範例在使用「海洋藍色」樣本*的註冊和登錄*使用者流頁上顯示自訂橫幅徽標和背景圖像:

![Azure AD B2C 提供的品牌註冊/登入頁面](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>使用公司品牌資產

要在自訂 HTML 中使用公司品牌資產,`<div id="api">`請在 標籤之外新增以下標記:

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

圖像源替換為背景圖像和橫幅徽標的圖像源。 如[「開始使用自訂 HTML 和 CSS」](#get-started-with-custom-html-and-css)部分所述,使用 CSS 類對頁面上的資產進行樣式設定和定位。

## <a name="localize-content"></a>本地化內容

您可以在 Azure AD B2C 租用戶上啟用[語言自訂](user-flow-language-customization.md)，以將 HTML 內容當地語系化。 啟用此功能允許 Azure AD B2C`ui-locales`將 OpenID 連接參數轉發到終結點。 內容伺服器可使用此參數來提供語言特定的 HTML 頁面。

您可以根據所使用的地區設定，從不同的地方提取內容。 在已啟用 CORS 的端點中，您可以針對特定語言設定主機內容的資料夾結構。 如果您使用萬用字元值 `{Culture:RFC5646}`，則會呼叫正確的語言。

例如,自定義頁面 URI 可能如下所示:

```HTTP
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

您可以通過從以下方面提取內容來載入法語頁面:

```HTTP
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>範例

您可以在 GitHub 上的[B2C-AzureBlob 儲存-用戶端](https://github.com/azureadquickstarts/b2c-azureblobstorage-client)儲存庫中找到多個範例樣本檔。

樣本中的範例 HTML 和 CSS 檔位於[/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)目錄中。

## <a name="next-steps"></a>後續步驟

- 如果使用**使用者流**,可以使用本教程開始自定義 UI:

    [在 Azure Active Directory B2C 中自訂應用程式的使用者介面](tutorial-customize-ui.md)。
- 如果使用**自訂策略**,可以使用 本文開始自定義 UI:

    [使用 Azure 的目錄 B2C 中的自訂政策自訂應用程式的使用者介面](custom-policy-ui-customization.md)。
