---
title: 使用自訂原則來自訂應用程式的使用者介面
titleSuffix: Azure AD B2C
description: 深入了解在 Azure Active Directory B2C 中使用自訂原則來自訂使用者介面。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 42426e8249f3b6ceb592254833f38ab0df99c83c
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848963"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自訂原則來自訂應用程式的使用者介面

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文之後，您將會有一項具有品牌和外觀的註冊和登入自訂原則。 使用 Azure Active Directory B2C (Azure AD B2C)，幾乎可完全掌控對使用者呈現的 HTML 和 CSS 內容。 使用自訂原則時，您會以 XML 設定 UI 自訂，而不是在 Azure 入口網站中使用控制項。

## <a name="prerequisites"></a>必要條件

完成[開始使用自訂原則](custom-policy-get-started.md)中的步驟。 您應該有一個使用本機帳戶來註冊和登入的有效自訂原則。

## <a name="page-ui-customization"></a>頁面 UI 自訂

您可以使用頁面 UI 自訂功能，自訂任何自訂原則的外觀與風格。 您也可以維護應用程式與 Azure AD B2C 之間的品牌和視覺一致性。

運作方式如下：Azure AD B2C 會在客戶的瀏覽器中執行程式碼並使用稱為[跨原始資源共用 (CORS)](https://www.w3.org/TR/cors/) 的新式方法。 首先，在自訂原則中使用自訂 HTML 內容指定 URL。 Azure AD B2C 會合併 UI 元素與從您 URL 載入的 HTML 內容，然後對客戶顯示此頁面。

## <a name="create-your-html5-content"></a>建立您的 HTML5 內容

建立 HTML 內容並在標題中顯示您的產品品牌名稱。

1. 複製下列 HTML 程式碼片段。 它是語式正確的 HTML5，在 *\<body\>* 內標籤內有一個稱為 *\<div id="api"\>\</div\>* 的空元素。 這個元素指出要插入 Azure AD B2C 內容的地方。

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

1. 在文字編輯器中貼上所複製的程式碼片段，然後將檔案儲存為 customize-ui.html。

> [!NOTE]
> 如果您使用 login.microsoftonline.com，HTML 表單元素會因為安全性限制而移除。 如果您想要在自訂 HTML 內容中使用 HTML 表單元素，請使用 b2clogin.com。 如需其他權益，請參閱[使用 b2clogin.com](b2clogin.md) 。

## <a name="create-an-azure-blob-storage-account"></a>建立 Azure Blob 儲存體帳戶

>[!NOTE]
> 在本文中，我們使用 Azure Blob 儲存體來裝載內容。 您可以選擇在 Web 伺服器上裝載您的內容，但必須[在 Web 伺服器上啟用 CORS](https://enable-cors.org/server.html)。

若要在 Blob 儲存體中裝載此 HTML 內容，請執行下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 [中樞] 功能表上，選取 [新增] > [儲存體] > [儲存體帳戶]。
1. 選取儲存體帳戶的 [訂用帳戶]。
1. 建立**資源群組**，或選取現有的資源群組。
1. 輸入儲存體帳戶的 [名稱]。
1. 選取儲存體帳戶的 [地理位置]。
1. [部署模型] 可保持為 [資源管理員]。
1. [效能] 可保持為 [標準]。
1. 將 [帳戶類型] 變更為 [Blob 儲存體]。
1. [複寫] 可保持為 [RA-GRS]。
1. [存取層] 可保持為 [經常性存取]。
1. 按一下 [**檢查 + 建立**] 以建立儲存體帳戶。
    部署完成之後，[**儲存體帳戶**] 頁面會自動開啟。

## <a name="create-a-container"></a>建立容器

若要在 Blob 儲存體中建立公用容器，請執行下列步驟：

1. 在左側功能表的 [ **Blob 服務**] 底下，選取 [ **blob**]。
1. 按一下 [ **+ 容器**]。
1. 針對 [**名稱**]，輸入*root*。 這可以是您選擇的名稱（例如*wingtiptoys*），但我們會在此範例中使用*root*來求簡單。
1. 針對 [**公用存取層級**]，選取 [ **Blob**]，然後選取 **[確定]** 。
1. 按一下 [**根**] 以開啟新的容器。
1. 按一下 [上傳]。
1. 按一下 [選取檔案] 旁的資料夾圖示。
1. 流覽至，然後選取您稍早在 [頁面 UI 自訂] 區段中建立的**customize-ui.html** 。
1. 如果您想要上傳至子資料夾，請展開 [ **Advanced** ]，然後在 **[上傳到資料夾**] 中輸入資料夾名稱。
1. 選取 [上傳]。
1. 選取您上傳的**customize-ui.html** blob。
1. 在 [ **URL** ] 文字方塊的右邊，選取 [**複製到剪貼**簿] 圖示，將 URL 複製到剪貼簿。
1. 在網頁瀏覽器中，流覽至您複製的 URL，以確認您上傳的 blob 可供存取。 如果無法存取，例如，如果您遇到 `ResourceNotFound` 錯誤，請確定容器的存取類型設定為**blob**。

## <a name="configure-cors"></a>設定 CORS

執行下列步驟，以設定跨原始來源資源分享的 Blob 儲存體：

1. 在功能表中，選取 [CORS]。
1. 針對 [允許的來源]，輸入 `https://your-tenant-name.b2clogin.com`。 將 `your-tenant-name` 取代為您的 Azure AD B2C 租用戶名稱。 例如： `https://fabrikam.b2clogin.com` 。 輸入您的租用戶名稱時，必須全部使用小寫字母。
1. 針對 [允許的方法]，選取 `GET` 和 `OPTIONS`。
1. 針對 [允許的標頭]，輸入星號 (*)。
1. 針對 [公開的標頭]，輸入星號 (*)。
1. 針對 [最大壽命]，輸入 200。
1. 按一下 [檔案]。

## <a name="test-cors"></a>測試 CORS

執行下列步驟來驗證您是否已準備就緒：

1. 移至 [www.test-cors.org](https://www.test-cors.org/) 網站，然後在 [遠端 URL] 方塊中貼上 URL。
1. 按一下 [傳送要求]。
    如果您收到錯誤，請確定您的 [CORS 設定](#configure-cors)正確無誤。 您也可能需要清除瀏覽器快取，或按 Ctrl+Shift+P 來開啟 InPrivate 瀏覽工作階段。

## <a name="modify-the-extensions-file"></a>修改擴充檔案

若要設定 UI 自訂，您要從基底檔案將 **ContentDefinition** 及其子元素複製到擴充檔案。

1. 開啟原則的基底檔案。 例如， <em>`SocialAndLocalAccounts/` **`TrustFrameworkBase.xml`** </em> 。 這是自訂原則入門套件中所包含的其中一個原則檔案，您應該已在必要條件中取得這些檔案，以[開始使用自訂原則](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom)。
1. 搜尋 **ContentDefinitions** 元素的完整內容並且複製。
1. 開啟擴充檔案。 例如 TrustFrameworkExtensions.xml。 搜尋 **BuildingBlocks** 元素。 如果此元素不存在，請加以新增。
1. 貼上您複製的 **ContentDefinitions** 元素完整內容，作為 **BuildingBlocks** 元素的子項目。
1. 在您複製的 XML 中，搜尋包含 `Id="api.signuporsignin"` 的 **ContentDefinition** 元素。
1. 將 **LoadUri** 的值變更為您上傳至儲存體的 HTML 檔案 URL。 例如： `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html` 。

    自訂原則看起來應該如下所示：

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

## <a name="upload-and-test-your-updated-custom-policy"></a>上傳並測試您已更新的自訂原則

### <a name="upload-the-custom-policy"></a>上傳自訂原則

1. 選取頂端功能表中的 [目錄 + 訂用帳戶] 篩選，然後選擇包含您租用戶的目錄，以確定您使用的是包含 Azure AD B2C 租用戶的目錄。
1. 搜尋並選取 [ **Azure AD B2C**]。
1. 在 [**原則**] 底下，選取 [ **Identity Experience Framework**]。
1. 選取 **[上傳自訂原則**]。
1. 上傳您先前變更的擴充檔案。

### <a name="test-the-custom-policy-by-using-run-now"></a>使用 [立即執行] 測試自訂原則

1. 選取您上傳的原則，然後選取 [**立即執行**]。
1. 您應該可以使用電子郵件地址註冊。

## <a name="reference"></a>參考

### <a name="sample-templates"></a>範例範本
您可以在此找到 UI 自訂的範例範本：

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

Sample_templates/wingtip 資料夾包含下列 HTML 檔案：

| HTML5 範本 | 說明 |
|----------------|-------------|
| phonefactor.html | 使用此檔案作為多重要素驗證頁面的範本。 |
| resetpassword.html | 使用此檔案作為忘記密碼頁面的範本。 |
| *selfasserted.html* | 使用此檔案作為社交帳戶註冊頁面、本機帳戶註冊頁面或本機帳戶登入頁面的範本。 |
| *unified.html* | 使用此檔案作為統一註冊或登入頁面的範本。 |
| *updateprofile.html* | 使用此檔案作為統一註冊或登入頁面的範本。 |

以下是如何使用範例的步驟：

1. 複製本機電腦上的存放庫。 選擇 [sample_templates] 底下的範本資料夾。 您可以使用 `wingtip` 或 `contoso`。
1. 將 `css`、`fonts`和 `images` 資料夾下的所有檔案上傳至 Blob 儲存體，如上一節所述。
1. 接下來，在 `wingtip` 或 `contoso` 的根目錄中開啟每個 \*.html 檔案（以您在第一個步驟中選取的方式），並將 "http://localhost" 的所有實例取代為您在步驟2中上傳的 css、影像和字型檔案的 Url。
1. 儲存 \*.html 檔案，並將其上傳至 Blob 儲存體。
1. 現在修改延伸模組檔案，如先前在[修改擴充](#modify-the-extensions-file)檔案中所述。
1. 如果您看到遺漏字型、影像或 CSS，請檢查延伸模組原則和 \*.html 檔案中的參考。

### <a name="content-definition-ids"></a>內容定義識別碼

在「修改註冊或登入自訂原則」一節中，您設定了 `api.idpselections` 的內容定義。 Azure AD B2C 身分識別體驗架構所能辨識的一組完整內容定義識別碼，而其說明位於下表中：

| 內容定義識別碼 | 說明 |
|-----------------------|-------------|
| api.error | **錯誤頁面**。 在發生例外狀況或錯誤時，系統會顯示此頁面。 |
| api.idpselections | **識別提供者選取頁面**。 此頁面包含使用者可以在登入期間選擇的識別提供者清單。 這些選項是企業識別提供者、社交識別提供者 (如 Facebook 和 Google+) 或本機帳戶。 |
| api.idpselections.signup | **用於註冊的識別提供者選取**。 此頁面包含使用者可以在註冊期間選擇的識別提供者清單。 這些選項是企業識別提供者、社交識別提供者 (如 Facebook 和 Google+) 或本機帳戶。 |
| api.localaccountpasswordreset | **忘記密碼頁面**。 此頁面包含一份表單，使用者必須填妥此表單才能開始重設密碼。  |
| *api.localaccountsignin* | **本機帳戶登入頁面**。 此頁面包含登入表單，可供使用以電子郵件地址或使用者名稱為基礎的本機帳戶進行登入。 此表單可以包含文字輸入方塊和密碼輸入方塊。 |
| *api.localaccountsignup* | **本機帳戶註冊頁面**。 此頁面包含登入表單，可供註冊以電子郵件地址或使用者名稱為基礎的本機帳戶。 此表單可以包含各種輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 |
| *api.phonefactor* | **Multi-Factor Authentication 頁面**。 在此頁面上，使用者可以在註冊或登入期間驗證其電話號碼 (藉由使用文字或語音)。 |
| *api.selfasserted* | **社交帳戶註冊頁面**。 此頁面包含使用者在使用社交識別提供者 (例如 Facebook 或 Google+) 的現有帳戶註冊時必須填妥的註冊表單。 此頁面類似於上述的社交帳戶註冊頁面，但密碼輸入欄位除外。 |
| *api.selfasserted.profileupdate* | **設定檔更新頁面**。 此頁面包含一份表單，以供使用者用來更新其設定檔。 此頁面類似於社交帳戶註冊頁面，但密碼輸入欄位除外。 |
| *api.signuporsignin* | **統一的註冊或登入頁面**。 此頁面可處理使用者的註冊和登入，這些使用者可使用企業識別提供者、社交識別提供者 (例如 Facebook 或 Google+) 或本機帳戶。  |

## <a name="next-steps"></a>後續步驟

如需可自訂之 UI 元素的詳細資訊，請參閱[使用者流程的 ui 自訂參考指南](customize-ui-overview.md)。
