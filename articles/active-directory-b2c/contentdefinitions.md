---
title: ContentDefinitions
titleSuffix: Azure AD B2C
description: 指定 Azure Active Directory B2C 中自訂原則的 ContentDefinitions 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b55199ec2684ab7b95ce4e4988b19814c27b2cc3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374794"
---
# <a name="contentdefinitions"></a>ContentDefinitions

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

您可以自訂任何[自我判斷技術設定檔](self-asserted-technical-profile.md)的外觀與風格。 Azure Active Directory B2C （Azure AD B2C）會在客戶的瀏覽器中執行程式碼，並使用稱為跨原始資源分享（CORS）的新式方法。

若要自訂使用者介面，請使用帶有自訂 HTML 內容的 **ContentDefinition** 元素，指定 URL。 在自我判斷技術設定檔或 **OrchestrationStep** 中，請指向該內容定義識別碼。 內容定義可能包含 **LocalizedResourcesReferences** 元素，該元素指定要載入之當地語系化資源的清單。 Azure AD B2C 會合併使用者介面元素與從您 URL 載入的 HTML 內容，然後對使用者顯示此頁面。

**ContentDefinitions** 元素包含了可在使用者旅程圖中使用之 HTML5 範本的 URL。 HTML5 頁面 URI 用於指定的使用者介面步驟。 例如，登入或註冊、密碼重設或錯誤網頁。 可以藉由覆寫該 HTML5 檔案的 LoadUri 來修改外觀與風格。 可以根據您的需求來建立新的內容定義。 此元素可能針對 [Localization](localization.md) 元素中指定的當地語系化識別碼，包含當地語系化的資源參考。

下列範例顯示內容定義識別碼和當地語系化資源的定義：

```XML
<ContentDefinition Id="api.localaccountsignup">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Local account sign up page</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    ...
```

**LocalAccountSignUpWithLogonEmail** 自我判斷技術設定檔的中繼資料包含設為 **的內容定義識別碼**ContentDefinitionReferenceId`api.localaccountsignup`

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    ...
  </Metadata>
  ...
```

## <a name="contentdefinition"></a>ContentDefinition

**ContentDefinition** 元素包含下列屬性：

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 內容定義的識別碼。 此值是本頁面後面的**內容定義識別碼**區段中指定的值。 |

**ContentDefinition** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| LoadUri | 1:1 | 此字串包含內容定義之 HTML5 頁面的 URL。 |
| RecoveryUri | 1:1 | 此字串包含 HTML 頁面的 URL，以顯示與內容定義相關的錯誤。 |
| DataUri | 1:1 | 此字串包含 HTML 檔案的相對 URL，該檔案為步驟提供了要叫用的使用者體驗。 |
| 中繼資料 | 0:1 | 包含內容定義使用之中繼資料的機碼值組的集合。 |
| LocalizedResourcesReferences | 0:1 | 當地語系化資源參考的集合。 使用此元素以自訂使用者介面的當地語系化與宣告屬性。 |

### <a name="datauri"></a>DataUri

**DataUri** 元素用於指定頁面識別碼。 Azure AD B2C 使用頁面識別碼以載入並初始化 UI 元素與用戶端 JavaScript。 值的格式為 `urn:com:microsoft:aad:b2c:elements:page-name:version`。 下表列出您可以使用的頁面識別碼。

| 頁面識別碼 | 描述 |
| ----- | ----------- |
| `globalexception` | 發生例外狀況或錯誤時，會顯示錯誤頁面。 |
| `providerselection`, `idpselection` | 列出使用者可以在登入期間選擇的識別提供者。  |
| `unifiedssp` | 顯示一份表單，可供以電子郵件地址或使用者名稱為基礎的本機帳戶進行登入。 此值也提供 [讓我保持登入功能] 和 [忘記密碼？] 連結。 |
| `unifiedssd` | 顯示一份表單，可供以電子郵件地址或使用者名稱為基礎的本機帳戶進行登入。 |
| `multifactor` | 在註冊或登入期間，藉由使用簡訊或語音，驗證電話號碼。 |
| `selfasserted` | 顯示從使用者收集資料的表單。 例如，可讓使用者建立或更新其設定檔。 |

### <a name="select-a-page-layout"></a>選取頁面配置

您可以在 `elements` 與頁面類型之間插入 `contract`，以啟用[JavaScript 用戶端程式代碼](javascript-samples.md)。 例如： `urn:com:microsoft:aad:b2c:elements:contract:page-name:version` 。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

`DataUri` 的[版本](page-layout.md)部分會針對原則中的使用者介面元素，指定包含 HTML、CSS 和 JavaScript 的內容套件。 如果您想要啟用 JavaScript 用戶端程式代碼，則您的 JavaScript 所依據的元素必須是不可變的。 如果它們不是固定的，任何變更都可能會在使用者頁面上造成非預期的行為。 若要避免這些問題，請強制使用頁面配置，並指定頁面配置版本。 這麼做可確保您根據 JavaScript 的所有內容定義都是不可變的。 即使您不想要啟用 JavaScript，仍然需要為頁面指定頁面配置版本。

下列範例顯示 `selfasserted` 版本 `1.2.0`的**DataUri** ：

```xml
<ContentDefinition Id="api.localaccountpasswordreset">
<LoadUri>~/tenant/templates/AzureBlue/selfAsserted.cshtml</LoadUri>
<RecoveryUri>~/common/default_page_error.html</RecoveryUri>
<DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
<Metadata>
    <Item Key="DisplayName">Local account change password page</Item>
</Metadata>
</ContentDefinition>
```

#### <a name="migrating-to-page-layout"></a>遷移至頁面配置

值的格式必須包含 [`contract`： _urn： com： microsoft： aad： b2c：元素：**合約**:p 的年齡-名稱：版本_] 這個字。 若要在使用舊**DataUri**值的自訂原則中指定頁面配置，請使用下表來遷移至新的格式。

| 舊的 DataUri 值 | 新的 DataUri 值 |
| ----------------- | ----------------- |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:globalexception:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:idpselection:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:multifactor:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:selfasserted:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssd:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssd:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |
| `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` | `urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0` |


### <a name="metadata"></a>中繼資料

**Metadata** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| 項目 | 0:n | 與內容定義相關的中繼資料。 |

**Metadata** 元素的 **Item** 元素包含下列屬性：

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| Key | 是 | 中繼資料索引鍵。  |

#### <a name="metadata-keys"></a>中繼資料索引鍵

內容定義支援下列中繼資料專案：

| Key | 必要項 | 描述 |
| --------- | -------- | ----------- |
| DisplayName | 否 | 包含內容定義名稱的字串。 |

### <a name="localizedresourcesreferences"></a>LocalizedResourcesReferences

**LocalizedResourcesReferences** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| LocalizedResourcesReference | 1:n | 內容定義的當地語系化資源參考清單。 |

**LocalizedResourcesReference**元素包含下列屬性：

| 屬性 | 必要項 | 描述 |
| --------- | -------- | ----------- |
| 語言 | 是 | 此字串針對每個 RFC 5646 - 標記的原則包含支援的語言，以進行識別語言。 |
| LocalizedResourcesReferenceId | 是 | **LocalizedResources** 元素的識別碼。 |

下列範例顯示的是註冊或登入內容定義，其參考英文、法文和西班牙文的當地語系化：

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Signin and Signup</Item>
  </Metadata>
  <LocalizedResourcesReferences MergeBehavior="Prepend">
    <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
    <LocalizedResourcesReference Language="fr" LocalizedResourcesReferenceId="api.signuporsignin.rf" />
    <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
</LocalizedResourcesReferences>
</ContentDefinition>
```

若要了解如何將當地語系化支援新增至您的內容定義，請參閱[當地語系化](localization.md)。

## <a name="content-definition-ids"></a>內容定義識別碼

**ContentDefinition** 元素的識別碼屬性會指定與內容定義相關的頁面類型。 此元素會定義自訂 HTML5/CSS 範本即將套用的內容。 下表說明身分識別體驗架構所能辨識之內容定義識別碼的集合，以及這些識別碼的相關頁面類型。 您可以使用任意識別碼建立自己的內容定義。

| ID | 預設範本 | 描述 |
| -- | ---------------- | ----------- |
| api.error | [exception.cshtml](https://login.microsoftonline.com/static/tenant/default/exception.cshtml) | **錯誤頁面** - 發生例外狀況或錯誤時，會顯示錯誤頁面。 |
| api.idpselections | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **識別提供者選取頁面** - 列出使用者可以在登入期間選擇的識別提供者。 這些選項通常是企業識別提供者、社交識別提供者 (如 Facebook 和 Google+) 或本機帳戶。 |
| api.idpselections.signup | [idpSelector.cshtml](https://login.microsoftonline.com/static/tenant/default/idpSelector.cshtml) | **適用於註冊的識別提供者選取項目** - 列出使用者可以在註冊期間選擇的識別提供者。 這些選項通常是企業識別提供者、社交識別提供者 (如 Facebook 和 Google+) 或本機帳戶。 |
| api.localaccountpasswordreset | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **忘記密碼頁面** - 顯示一份表單，使用者必須填妥表單，然後才能開始密碼重設。 |
| **api.localaccountsignin** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本機帳戶登入頁面** - 顯示一份表單，可供以電子郵件地址或使用者名稱為基礎的本機帳戶進行登入。 此表單可以包含文字輸入方塊和密碼輸入方塊。 |
| **api.localaccountsignup** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **本機帳戶註冊頁面** - 顯示一份表單，可供以電子郵件地址或使用者名稱為基礎的本機帳戶進行註冊。 此表單可以包含各種輸入控制項，例如文字輸入方塊、密碼輸入方塊、選項按鈕、單選下拉式清單方塊和多選核取方塊。 |
| **api.phonefactor** | [multifactor-1.0.0.cshtml](https://login.microsoftonline.com/static/tenant/default/multifactor-1.0.0.cshtml) | **多重要素驗證頁面** - 在註冊或登入期間，藉由使用簡訊或語音，驗證電話號碼。 |
| **api.selfasserted** | [selfasserted. cshtml](https://login.microsoftonline.com/static/tenant/default/selfAsserted.cshtml) | **社交帳戶註冊頁面** - 顯示一份表單，使用者在使用社交識別提供者的現有帳戶時，必須填妥此表單。 此頁面類似於上述的社交帳戶註冊頁面，但密碼輸入欄位除外。 |
| **api.selfasserted.profileupdate** | [updateprofile. cshtml](https://login.microsoftonline.com/static/tenant/default/updateProfile.cshtml) | **設定檔更新頁面** - 顯示一份表單，使用者可用來更新其設定檔。 此頁面類似於社交帳戶註冊頁面，但密碼輸入欄位除外。 |
| **api.signuporsignin** | [統一的 cshtml](https://login.microsoftonline.com/static/tenant/default/unified.cshtml) | **統一的註冊或登入頁面** - 處理使用者註冊或登入程序。 使用者可以使用企業識別提供者、社交識別提供者 (如 Facebook 或 Google+) 或本機帳戶。 |

## <a name="next-steps"></a>後續步驟

如需使用內容定義自訂使用者介面的範例，請參閱：

[使用自訂原則來自訂應用程式的使用者介面](custom-policy-ui-customization.md)
