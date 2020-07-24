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
ms.date: 04/04/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6bcf268ba45ee8fefa027263a25ff411344b4669
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116432"
---
# <a name="customize-the-user-interface-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中自訂使用者介面

品牌化和自訂 Azure Active Directory B2C （Azure AD B2C）向您的客戶顯示的使用者介面，有助於在應用程式中提供順暢的使用者體驗。 這些體驗包括註冊、登入、設定檔編輯和密碼重設。 本文介紹使用者流程和自訂原則的使用者介面（UI）自訂方法。

## <a name="ui-customization-in-different-scenarios"></a>不同案例中的 UI 自訂

有數種方式可自訂使用者體驗應用程式的 UI，每個方法都適用于不同的案例。

### <a name="user-flows"></a>使用者流程

如果您使用[使用者流程](user-flow-overview.md)，您可以使用內建的*頁面配置範本*，或使用您自己的 HTML 和 CSS 來變更使用者流程頁面的外觀。 本文稍後將討論這兩種方法。

您可以使用[Azure 入口網站](tutorial-customize-ui.md)來設定使用者流程的 UI 自訂。

> [!TIP]
> 如果您只想修改使用者流程頁面的橫幅標誌、背景影像和背景色彩，可以嘗試本文稍後所述的[公司商標（預覽）](#company-branding-preview)功能。

### <a name="custom-policies"></a>自訂原則

如果您在應用程式中使用[自訂原則](custom-policy-overview.md)來提供註冊或登入、密碼重設或設定檔編輯，請使用[原則檔案來自訂 UI](custom-policy-ui-customization.md)。

如果您需要根據客戶的決策提供動態內容，請使用可根據查詢字串中傳送的參數，[動態變更頁面內容](custom-policy-ui-customization.md#configure-dynamic-custom-page-content-uri)的自訂原則。 例如，您可以根據您從 web 或行動裝置應用程式傳遞的參數，在 Azure AD B2C 註冊或登入頁面上變更背景影像。

### <a name="javascript"></a>JavaScript

您可以在[使用者流程](user-flow-javascript-overview.md)和[自訂原則](page-layout.md)中啟用用戶端 JavaScript 程式碼。

### <a name="sign-in-only-ui-customization"></a>僅限登入 UI 自訂

如果您只提供登入，連同其隨附的密碼重設頁面和驗證電子郵件，請使用[Azure AD 登入頁面](../active-directory/fundamentals/customize-branding.md)所使用的相同自訂步驟。

如果客戶在登入之前嘗試編輯其設定檔，系統會將他們重新導向至您使用自訂 Azure AD 登入頁面時所使用的相同步驟自訂的頁面。

## <a name="page-layout-templates"></a>頁面配置範本

使用者流程提供數個可供您選擇的內建範本，讓您的使用者體驗頁面具有專業型式。 這些版面配置範本也可以做為自訂的起點。

在左側功能表中的 [**自訂**] 底下，選取 [**頁面配置**]，然後選取 [**範本**]。

![Azure 入口網站的 [使用者流程] 頁面中的範本選取下拉式選單](media/customize-ui-overview/template-selection.png)

接下來，從清單中選取範本。 以下是每個範本的登入頁面範例：

| 海運藍色 | 平板電腦灰色 | 傳統 |
|:-:|:-:|:-:|
|![在註冊登入頁面上轉譯的海運藍色範本範例](media/customize-ui-overview/template-ocean-blue.png)|![在註冊登入頁面上呈現的平板電腦灰色範本範例](media/customize-ui-overview/template-slate-gray.png)|![在註冊登入頁面上呈現的傳統範本範例](media/customize-ui-overview/template-classic.png)|

選擇範本時，選取的配置將套用到使用者流程中的所有頁面，並且每一頁的 URI 會顯示在 [自訂頁面 URI]**** 欄位中。

## <a name="custom-html-and-css"></a>自訂 HTML 和 CSS

如果您想要使用自訂的 HTML 和 CSS 來設計自己的原則配置，可以針對原則中的每個版面配置名稱，切換 [使用自訂頁面內容] 切換來執行此動作。 請遵循下列關於自訂版面配置設定的指示：

Azure AD B2C 會使用稱為[跨原始資源分享（CORS）](https://www.w3.org/TR/cors/)的方法，在客戶的瀏覽器中執行程式碼。

在執行時間，會從您在使用者流程或自訂原則中指定的 URL 載入內容。 使用者體驗中的每個頁面都會從您為該頁面指定的 URL 載入其內容。 從您的 URL 載入內容之後，它會與 Azure AD B2C 插入的 HTML 片段合併，然後向您的客戶顯示該頁面。

使用您自己的 HTML 和 CSS 檔案來自訂 UI 之前，請先參閱下列指導方針：

- Azure AD B2C 會將 HTML 內容**合併**至您的頁面。 請勿複製及嘗試變更 Azure AD B2C 所提供的預設內容。 最好是從頭建置您的 HTML 內容，將預設範本當作參考即可。
- **JavaScript**可以包含在您的自訂內容中，供[使用者流程](user-flow-javascript-overview.md)和[自訂原則](javascript-samples.md)之用。
- 支援的**瀏覽器版本**包括：
  - Internet Explorer 11、10和 Microsoft Edge
  - 對 Internet Explorer 9 和 8 僅提供有限支援
  - Google Chrome 42.0 和更新版本
  - Mozilla Firefox 38.0 和更新版本
  - 適用于 iOS 和 macOS 的 Safari，第12版及更新版本
- 請勿在您的 HTML 中包含**表單標記**。 表單標記會干擾由 Azure AD B2C 插入的 HTML 所產生的 POST 作業。

### <a name="where-do-i-store-ui-content"></a>UI 內容應儲存於何處？

使用您自己的 HTML 和 CSS 檔案來自訂 UI 時，您可以將 UI 內容裝載在任何支援 CORS 的公開可用 HTTPS 端點上。 例如， [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md)、web 伺服器、CDN、AWS S3 或檔案共用系統。

重點在於，您會將內容裝載在公開可用的 HTTPS 端點，並[啟用 CORS](https://enable-cors.org/server.html)。 在您的內容中指定 URL 時，必須使用絕對 URL。

> [!NOTE]
> 如需有關建立 HTML 內容、將內容上傳至 Azure Blob 儲存體，以及設定 CORS 的詳細資訊，請參閱 UI 自訂一文中的[自訂頁面內容逐步](custom-policy-ui-customization.md#custom-page-content-walkthrough)解說一節。

## <a name="get-started-with-custom-html-and-css"></a>開始使用自訂 HTML 和 CSS

遵循這些指導方針，開始在您的使用者體驗頁面中使用您自己的 HTML 和 CSS。

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
- 建立或編輯使用者流程或自訂原則，以使用您所建立的內容。

### <a name="html-fragments-from-azure-ad-b2c"></a>Azure AD B2C 的 HTML 片段

下表列出 Azure AD B2C 合併至 `<div id="api"></div>` 元素 (位於您的內容中) 的 HTML 片段範例。

| 插入的頁面 | HTML 的描述 |
| ------------- | ------------------- |
| 識別提供者選取項目 | 包含客戶在註冊或登入期間可選擇的識別提供者按鈕清單。 這些按鈕包括社交識別提供者 (例如 Facebook、Google) 或本機帳戶 (以電子郵件地址或使用者名稱作為基礎)。 |
| 本機帳戶註冊 | 包含可供使用者根據電子郵件地址或使用者名稱進行本機帳戶註冊的表單。 此表單可以包含不同的輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 |
| 社交帳戶註冊 | 可在使用社交識別提供者 (例如 Facebook 或 Google) 的現有帳戶註冊時顯示。 當您必須使用註冊表單從客戶收集其他資訊時，就會使用它。 |
| 統一的註冊或登入 | 可處理客戶的註冊和登入，這些客戶可使用社交識別提供者 (例如 Facebook、Google) 或本機帳戶。 |
| Multi-Factor Authentication | 客戶可以在註冊或登入期間驗證其電話號碼 (使用文字或語音)。 |
| 錯誤 | 提供錯誤資訊給客戶。 |

## <a name="company-branding-preview"></a>公司商標（預覽）

您可以使用 Azure Active Directory[公司商標](../active-directory/fundamentals/customize-branding.md)，透過橫幅標誌、背景影像和背景色彩來自訂您的使用者流程頁面。

若要自訂您的使用者流程頁面，請先在 Azure Active Directory 中設定公司商標，然後在 Azure AD B2C 的使用者流程頁面配置中加以啟用。

[!INCLUDE [preview note](../../includes/active-directory-b2c-public-preview.md)]

### <a name="configure-company-branding"></a>設定公司商標

首先，設定**公司商標**內的橫幅標誌、背景影像和背景色彩。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在頂端功能表中選取 [目錄 + 訂用帳戶] 篩選，然後選取包含您 Azure AD B2C 租用戶的目錄。
1. 在 Azure 入口網站中，搜尋並選取 [Azure AD B2C]。
1. 在 [**管理**] 下，選取 [**公司商標**]。
1. 依照[將商標新增至您組織的 Azure Active Directory 登入頁面](../active-directory/fundamentals/customize-branding.md)中的步驟進行。

當您在 Azure AD B2C 中設定公司商標時，請記住下列事項：

* Azure AD B2C 中的公司商標目前僅限於**背景影像**、**橫幅標誌**和**背景色彩**自訂。 *不支援*[公司商標] 窗格中的其他屬性（例如，在 [**高級設定**] 中）。
* 在您的使用者流程頁面中，背景色彩會在載入背景影像之前顯示。 我們建議您選擇與背景影像中的色彩緊密相符的背景色彩，以提供更流暢的載入體驗。
* 橫幅標誌會在使用者起始註冊使用者流程時，出現在傳送給使用者的驗證電子郵件中。

### <a name="enable-branding-in-user-flow-pages"></a>在使用者流程頁面中啟用商標

設定公司商標之後，請在您的使用者流程中加以啟用。

1. 在 Azure 入口網站的左側功能表中，選取 [ **Azure AD B2C**]。
1. 在 [原則] 底下，選取 [使用者流程 (原則)]。
1. 選取您想要啟用公司商標的使用者流程。 「登*入 v1* 」和「*設定檔編輯 v1* 」使用者流程類型**不支援**公司商標。
1. 在 [**自訂**] 底下，選取 [**頁面配置**]，然後選取您想要品牌的版面配置。 例如，選取 [**統一註冊或登入頁面**]。
1. 針對 [**頁面配置版本（預覽）**]，選擇 [版本**1.2.0** ] 或 [以上]。
1. 選取 [儲存]。

如果您想要在使用者流程中建立所有頁面的品牌，請在使用者流程中設定每個頁面配置的頁面配置版本。

![Azure 入口網站中 Azure AD B2C 的頁面配置選取範圍](media/customize-ui-overview/portal-02-page-layout-select.png)

此批註範例會在使用海藍色範本的 [*註冊和登入*使用者流程] 頁面上，顯示自訂橫幅標誌和背景影像：

![Azure AD B2C 提供品牌化的註冊/登入頁面](media/customize-ui-overview/template-ocean-blue-branded.png)

### <a name="use-company-branding-assets-in-custom-html"></a>在自訂 HTML 中使用公司商標資產

若要在自訂 HTML 中使用您的公司商標資產，請在標記外部新增下列標記 `<div id="api">` ：

```HTML
<img data-tenant-branding-background="true" />
<img data-tenant-branding-logo="true" alt="Company Logo" />
```

影像來源會以背景影像和橫幅標誌的取代。 如[開始使用自訂 HTML 和 CSS](#get-started-with-custom-html-and-css)一節中所述，使用 CSS 類別來樣式並將資產放置在頁面上。

## <a name="localize-content"></a>當地語系化內容

您可以在 Azure AD B2C 租用戶上啟用[語言自訂](user-flow-language-customization.md)，以將 HTML 內容當地語系化。 啟用此功能可讓 Azure AD B2C 將 OpenID Connect 參數轉送 `ui_locales` 至您的端點。 內容伺服器可使用此參數來提供語言特定的 HTML 頁面。

您可以根據所使用的地區設定，從不同的地方提取內容。 在已啟用 CORS 的端點中，您可以針對特定語言設定主機內容的資料夾結構。 如果您使用萬用字元值 `{Culture:RFC5646}`，則會呼叫正確的語言。

例如，您的自訂頁面 URI 可能如下所示：

```http
https://contoso.blob.core.windows.net/{Culture:RFC5646}/myHTML/unified.html
```

您可以從下列來源提取內容，以在法文中載入頁面：

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="examples"></a>範例

您可以在 GitHub 上的[B2C AzureBlobStorage-用戶端](https://github.com/azureadquickstarts/b2c-azureblobstorage-client)存放庫中找到數個範例範本檔案。

範本中的範例 HTML 和 CSS 檔案位於[/sample_templates](https://github.com/AzureADQuickStarts/B2C-AzureBlobStorage-Client/tree/master/sample_templates)目錄中。

## <a name="next-steps"></a>後續步驟

- 如果您使用的是**使用者流程**，您可以使用教學課程來開始自訂 UI：

    [在 Azure Active Directory B2C 中自訂應用程式的使用者介面](tutorial-customize-ui.md)。
- 如果您使用的是**自訂原則**，您可以使用下列文章開始自訂 UI：

    [使用 Azure Active Directory B2C 中的自訂原則，自訂應用程式的使用者介面](custom-policy-ui-customization.md)。
