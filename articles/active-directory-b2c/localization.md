---
title: 當地語系化 - Azure 活動目錄 B2C
description: 指定 Azure Active Directory B2C 中自訂原則的 Localization 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e73eae4d66f4ff94a48dfa27e258f8ba8ef87633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126750"
---
# <a name="localization"></a>當地語系化

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Localization** 元素可讓您在使用者旅程圖的適用原則中支援多個地區設定或語言。 原則中的當地語系化支援能夠：

- 設定原則中可支援語言的明確清單，然後選擇預設語言。
- 提供特定語言的字串和集合。

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

**Localization** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 啟用 | 否 | 可能的值：`true` 或 `false`。 |

**Localization** 元素包含下列 XML 元素

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | 支援語言清單。 |
| LocalizedResources | 0:n | 當地語系化資源的清單。 |

## <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| DefaultLanguage | 是 | 用來做為當地語系化資源的預設語言。 |
| MergeBehavior | 否 | 列舉值，這些值是與具有相同識別碼之父代原則中存在的 ClaimType 合併的值。 在覆寫基本原則中指定的宣告時，請使用這個屬性。 可能的值：`Append`、`Prepend` 或 `ReplaceAll`。 `Append` 值指定應該在父代原則中指定的集合結尾後，附加上存在的資料集合。 `Prepend` 值指定應該在父代原則中指定的集合之前，新增存在的資料集合。 `ReplaceAll` 值指定應該忽略父代原則中定義的資料集合，而改用目前原則中定義的資料。 |

### <a name="supportedlanguages"></a>SupportedLanguages

**SupportedLanguages** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | 根據 RFC 5646 顯示符合語言標記的內容 - 識別語言的標記。 |

## <a name="localizedresources"></a>LocalizedResources

**LocalizedResources** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Id | 是 | 用來唯一識別當地語系化資源的識別碼。 |

**LocalizedResources** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | 定義不同文化中的整個集合。 對於不同的文化特性，集合可以有不同數量的項目和不同的字串。 集合範例會包含宣告類型中出現的列舉。 例如在下拉式清單中，會向使用者顯示國家/地區清單。 |
| LocalizedStrings | 0:n | 定義各種文化特性中的所有字串，出現在集合中的字串除外。 |

### <a name="localizedcollections"></a>LocalizedCollections

**LocalizedCollections** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | 支援語言清單。 |

#### <a name="localizedcollection"></a>LocalizedCollection

**LocalizedCollection** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ElementType | 是 | 參考原則檔中的 ClaimType 元素或使用者介面元素。 |
| ElementId | 是 | 此字串包含對 ClaimsSchema 區段中已定義的宣告類型，如果 **ElementType** 設為 ClaimType，則會使用該字串。 |
| TargetCollection | 是 | 目標集合。 |

**LocalizedCollection** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| Item | 0:n | 定義在使用者介面中可供使用者選取的某個宣告選項，例如下拉式清單中的值。 |

**Item** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| Text | 是 | 此選項應會在使用者介面中向使用者顯示的易記顯示字串。 |
| 值 | 是 | 與選取此選項相關聯的字串宣告值。 |
| SelectByDefault | 否 | 指出預設是否應該在 UI 中選取此選項。 可能的值：True 或 False。 |

下列範例顯示 **LocalizedCollections** 元素的用法。 其包含兩個 **LocalizedCollection** 元素，一個適用於英文，另一個適用於西班牙文。 兩者皆會使用英文和西班牙文的項目清單，設定宣告 `Gender` 的**限制**集合。

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

**LocalizedStrings** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | 當地語系化的字串。 |

**LocalizedString** 元素包含下列屬性：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ElementType | 是 | 參考原則中的宣告類型元素或使用者介面元素。 可能的值： `ClaimType` `UxElement`、 `ErrorMessage` `Predicate`、 `GetLocalizedStringsTransformationClaimType`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 `ClaimType` 值用於將其中一個宣告屬性當地語系化，如 StringId 中所指定。 `UxElement` 值用於將其中一個使用者介面元素當地語系化，如 StringId 中所指定。 `ErrorMessage` 值用於將其中一個系統錯誤訊息當地語系化，如 StringId 中所指定。 `Predicate` 值用於將其中一個 [Predicate](predicates.md) 錯誤訊息當地語系化，如 StringId 中所指定。 `InputValidation` 值用於將其中一個 [PredicateValidation](predicates.md) 群組錯誤訊息當地語系化，如 StringId 中所指定。 該`GetLocalizedStringsTransformationClaimType`值用於將當地語系化字串複製到聲明中。 有關詳細資訊，請參閱[獲取當地語系化字串轉換聲明轉換](string-transformations.md#getlocalizedstringstransformation)  | 
| ElementId | 是 | 如果**ElementType**設置為`ClaimType` `Predicate`，或`InputValidation`， 此元素包含對聲明架構部分中已定義的聲明類型的引用。 |
| StringId | 是 | 如果 **ElementType** 設為 `ClaimType`，則此元素會包含對宣告類型之屬性的參考。 可能的值：`DisplayName`、`AdminHelpText` 或 `PatternHelpText`。 `DisplayName` 值用於設定宣告顯示名稱。 `AdminHelpText` 值用於設定宣告使用者的說明文字名稱。 `PatternHelpText` 值用於設定宣告模式說明文字。 如果 **ElementType** 設為 `UxElement`，則此元素會包含使用者介面元素之屬性的參考。 如果 **ElementType** 設為 `ErrorMessage`，則此元素會指定錯誤訊息的識別碼。 如需 `UxElement` 識別碼的完整清單，請參閱[當地語系化字串識別碼](localization-string-ids.md)。|


下列範例列出了當地語系化的註冊頁面。 前三個 **LocalizedString** 值設定的是宣告屬性。 第三個會變更繼續按鈕的值。 最後一個會變更錯誤訊息。

```XML
<LocalizedResources Id="api.selfasserted.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

下列範例顯示 **Predicate** 之當地語系化的 **UserHelpText**，其識別碼為 `IsLengthBetween8And64`。 以及 **PredicateGroup** 之當地語系化的 **UserHelpText**，其識別碼為 **PredicateValidation** 的 `CharacterClasses`，其識別碼為 `StrongPassword`。

```XML
<PredicateValidation Id="StrongPassword">
  <PredicateGroups>
    ...
    <PredicateGroup Id="CharacterClasses">
    ...
    </PredicateGroup>
  </PredicateGroups>
</PredicateValidation>

...

<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  ...
</Predicate>
...


<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>

<LocalizedString ElementType="Predicate" ElementId="IsLengthBetween8And64" StringId="HelpText">The password must be between 8 and 64 characters.</LocalizedString>
```

## <a name="set-up-localization"></a>設定當地語系化

本文說明如何在使用者旅程圖的適用原則中，支援多個地區設定或語言。 當地語系化需要三個步驟：設定支援語言的明確清單、提供特定語言的字串與集合，以及編輯頁面的 ContentDefinition。

### <a name="set-up-the-explicit-list-of-supported-languages"></a>設定支援語言的明確清單

請在 **BuildingBlocks** 元素下，透過支援語言清單新增 **Localization** 元素。 下列範例示範如何定義英文 (預設) 和西班牙文的當地語系化支援：

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="next-steps"></a>後續步驟

有關當地語系化示例，請參閱以下文章：

- [使用 Azure 活動目錄 B2C 中的自訂策略進行語言自訂](custom-policy-localization.md)
- [使用 Azure 活動目錄 B2C 中的使用者流進行語言自訂](user-flow-language-customization.md)
