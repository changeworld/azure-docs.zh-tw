---
title: Azure AD 使用者流程中的語言自訂
description: 深入了解使用者流程中的自訂語言體驗。
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: c73a5491c0ad543d57a6721f37d81ae661c0a653
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441431"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Azure Active Directory 中的語言自訂 (預覽)

> [!NOTE]
> 自助式註冊是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Active Directory (Azure AD) 中的語言自訂可讓您的使用者流程適應不同的語言，以符合您使用者的需求。 Microsoft 提供 [36 種語言](#supported-languages)的翻譯。 即使您的體驗僅提供單一語言，也可以自訂屬性集合頁面上的屬性名稱。

## <a name="how-language-customization-works"></a>語言自訂的運作方式

根據預設，系統會針對註冊的使用者啟用語言自訂，以確保一致的註冊體驗。 在註冊過程中，您可以使用語言將顯示給使用者的字串修改為屬性集合流程的一部分。

> [!NOTE]
> 如果您使用自訂使用者屬性，則必須提供自己的翻譯。 如需詳細資訊，請參閱[自訂您的字串](#customize-your-strings)。

## <a name="customize-your-strings"></a>自訂您的字串

語言自訂可讓您自訂使用者流程中的任何字串。

1. 以 Azure AD 系統管理員身分登入 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Azure 服務] 底下，選取 [Azure Active Directory]。
3. 在左側功能表中，選取 [外部身分識別]。
4. 選取 [使用者流程 (預覽)]。
3. 選取您要啟用翻譯的使用者流程。
4. 選取 [語言]。
5. 在使用者流程的 [語言] 頁面上，選取您想要自訂的語言。
6. 展開**屬性集合頁面**。
7. 選取 [下載預設值] (如果您先前已編輯這個語言，請按一下 [下載覆寫])。

上述步驟會向您提供 JSON 檔案，以供您用來開始編輯字串。

### <a name="change-any-string-on-the-page"></a>變更頁面上的任何字串

1. 在 JSON 編輯器中開啟透過前面的指示所下載的 JSON 檔案。
1. 尋找您想要變更的元素。 您可以尋找 `StringId` 以取得您要尋找的字串，或尋找您要變更的 `Value` 屬性。
1. 以您想要顯示的內容來更新 `Value` 屬性。
1. 對於您想要變更的每個字串，請將 `Override` 變更為 `true`。
1. 儲存檔案，並上傳您的變更。 (您可以在您下載 JSON 檔案的相同位置找到上傳控制項)。

> [!IMPORTANT]
> 如果您需要覆寫字串，請務必將 `Override` 值設為 `true`。 如果該值未變更，則系統會忽略您的輸入。

### <a name="change-extension-attributes"></a>變更擴充屬性

如果您想要變更自訂使用者屬性的字串，或想要將字串新增到 JSON 中，其格式如下︰

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

以自訂使用者屬性的名稱來取代 `<ExtensionAttribute>`。

以要顯示的新字串來取代 `<ExtensionAttributeValue>`。

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>使用 LocalizedCollections 提供值清單

如果您想要提供設定好的回應值清單，您需要建立 `LocalizedCollections` 屬性。 `LocalizedCollections` 是 `Name` 和 `Value` 的配對陣列。 項目的順序即為顯示它們的順序。 若要新增 `LocalizedCollections`，請使用下列格式：

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` 是使用者屬性，而其回應則是這個 `LocalizedCollections` 屬性。
* `Name` 是向使用者顯示的值。
* `Value` 是選取此選項時在宣告中所傳回的內容。

### <a name="upload-your-changes"></a>上傳您的變更

1. 對 JSON 檔案完成變更後，請瀏覽回租用戶。
1. 選取 [使用者流程]，然後按一下您想要啟用翻譯的使用者流程。
1. 選取 [語言]。
1. 選取您想要轉譯成什麼語言。
1. 選取**屬性集合頁面**。
1. 選取資料夾圖示，然後選取要上傳的 JSON 檔案。

這些變更會自動儲存到您的使用者流程。

## <a name="additional-information"></a>其他資訊

### <a name="page-ui-customization-labels-as-overrides"></a>以頁面 UI 自訂標籤作為覆寫

當您啟用語言自訂時，您先前使用頁面 UI 自訂對標籤所進行的編輯會保存在英文 (en) 的 JSON 檔案中。 您可以上傳語言自訂中的語言資源，來繼續變更您的標籤和其他字串。

### <a name="up-to-date-translations"></a>最新的翻譯

Microsoft 致力於提供最新的翻譯來供您使用。 Microsoft 會持續改善翻譯，並讓它們符合您的需求。 Microsoft 會找出全域術語中的錯誤和變更，並製作可在使用者流程中順暢運作的更新。

### <a name="support-for-right-to-left-languages"></a>支援從右至左的語言

Microsoft 目前不支援從右至左的語言。 您可以使用自訂地區設定，並使用 CSS 變更顯示字串的方式來完成此作業。 如果您需要此功能，請在 [Azure 意見反應](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag)上投票支持此功能。

### <a name="social-identity-provider-translations"></a>社交識別提供者的翻譯

Microsoft 為社交登入提供 `ui_locales` OIDC 參數。 但有些社交識別提供者不接受此參數，包括 Facebook 和 Google。

### <a name="browser-behavior"></a>瀏覽器行為

Chrome 和 Firefox 都會要求使用其設定的語言。 如果該語言受到支援，則會顯示在預設語言之前。 Microsoft Edge 目前未要求語言，而是會直接使用預設語言。

## <a name="supported-languages"></a>支援的語言

Azure AD 支援下列語言。 Azure AD 提供使用者流程語言。 多重要素驗證 (MFA} 通知語言是由 [Azure MFA](../authentication/concept-mfa-howitworks.md) 提供。

| Language              | 語言代碼 | 使用者流程         | MFA 通知  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| 阿拉伯文                | ar            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 保加利亞文             | bg            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 孟加拉文                | bn            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 卡達隆尼亞文               | ca            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 捷克文                 | cs            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 丹麥文                | da            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 德文                | de            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 希臘文                 | el            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 英文               | en            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 西班牙文               | es            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 愛沙尼亞文              | et            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 巴斯克文                | eu            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 芬蘭文               | fi            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 法文                | fr            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 加利西亞文              | gl            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 古吉拉特文              | gu            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| Hebrew                | he            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| Hindi                 | hi            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 克羅埃西亞文              | hr            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 匈牙利文             | hu            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 印尼文            | id            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 義大利文               | it            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 日文              | ja            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 哈薩克文                | kk            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 坎那達文               | kn            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 韓文                | ko            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 立陶宛文            | lt            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 拉脫維亞文               | lv            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 馬來亞拉姆文             | ml            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 馬拉地文               | mr            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 馬來文                 | ms            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 挪威文 (巴克摩)      | nb            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 荷蘭文                 | nl            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 挪威文             | 否            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 旁遮普文               | pa            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 波蘭文                | pl            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 葡萄牙文 - 巴西   | pt-br         | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 葡萄牙文 - 葡萄牙 | pt-pt         | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 羅馬尼亞文              | ro            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 俄文               | ru            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 斯洛伐克文                | sk            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 斯洛維尼亞文             | sl            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 塞爾維亞文 (斯拉夫)    | sr-cryl-cs    | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 塞爾維亞文 (拉丁)       | sr-latn-cs    | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 瑞典文               | sv            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 坦米爾文                 | ta            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 泰盧固文                | te            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![X 表示否。](./media/user-flow-customize-language/no.png) |
| 泰文                  | th            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 土耳其文               | tr            | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 烏克蘭文             | uk            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 越南文            | vi            | ![X 表示否。](./media/user-flow-customize-language/no.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 中文 - 簡體  | zh-hans       | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |
| 中文 - 繁體 | zh-hant       | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) | ![綠色核取記號。](./media/user-flow-customize-language/yes.png) |