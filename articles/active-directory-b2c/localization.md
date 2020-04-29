---
title: 當地語系化-Azure Active Directory B2C
description: 指定 Azure Active Directory B2C 中自訂原則的 Localization 元素。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 94ff7ddda41f2df2634d927a7dbf8a5a0d4fc1d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681406"
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
| 項目 | 0:n | 定義在使用者介面中可供使用者選取的某個宣告選項，例如下拉式清單中的值。 |

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
| ElementType | 是 | 可能的值[： ClaimsProvider](#claimsprovider)、 [ClaimType](#claimtype)、 [ErrorMessage](#errormessage)、GetLocalizedStringsTransformationClaimType [、述](#predicate)詞、 [InputValidation](#inputvalidation)或[UxElement](#uxelement)。 [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype)   | 
| ElementId | 是 | 如果**ElementType**設為`ClaimType`、 `Predicate`或`InputValidation`，則這個元素會包含已在 ClaimsSchema 區段中定義之宣告類型的參考。 |
| StringId | 是 | 如果 **ElementType** 設為 `ClaimType`，則此元素會包含對宣告類型之屬性的參考。 可能的值：`DisplayName`、`AdminHelpText` 或 `PatternHelpText`。 `DisplayName` 值用於設定宣告顯示名稱。 `AdminHelpText` 值用於設定宣告使用者的說明文字名稱。 `PatternHelpText` 值用於設定宣告模式說明文字。 如果 **ElementType** 設為 `UxElement`，則此元素會包含使用者介面元素之屬性的參考。 如果 **ElementType** 設為 `ErrorMessage`，則此元素會指定錯誤訊息的識別碼。 如需 `UxElement` 識別碼的完整清單，請參閱[當地語系化字串識別碼](localization-string-ids.md)。|

## <a name="elementtype"></a>ElementType

要當地語系化之原則中的宣告類型、宣告轉換或使用者介面元素的 ElementType 參考。

| 要當地語系化的元素 | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| 識別提供者名稱 |`ClaimsProvider`| | ClaimsExchange 元素的識別碼|
| 宣告類型屬性|`ClaimType`|宣告類型的名稱| 要當地語系化之宣告的屬性。 可能的值`AdminHelpText`： `DisplayName`、 `PatternHelpText`、和`UserHelpText`。|
|錯誤訊息|`ErrorMessage`||錯誤訊息的識別碼 |
|將當地語系化的字串複製到宣告中|`GetLocalizedStringsTra nsformationClaimType`||輸出宣告的名稱|
|述詞使用者訊息|`Predicate`|述詞的名稱| 要當地語系化之述詞的屬性。 可能的值`HelpText`：。|
|述詞群組使用者訊息|`InputValidation`|PredicateValidation 元素的識別碼。|PredicateGroup 元素的識別碼。 述詞群組必須是述詞驗證元素的子系，如 ElementId 中所定義。|
|使用者介面元素 |`UxElement` | | 要當地語系化之使用者介面元素的識別碼。|

## <a name="examples"></a>範例

### <a name="claimsprovider"></a>ClaimsProvider

ClaimsProvider 值是用來將其中一個宣告提供者顯示名稱當地語系化。 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

下列範例顯示如何將宣告提供者的顯示名稱當地語系化。

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

ClaimType 值是用來將其中一個宣告屬性當地語系化。 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

下列範例顯示如何將電子郵件宣告類型的 DisplayName、UserHelpText 和 PatternHelpText 屬性當地語系化。

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

ErrorMessage 值是用來將其中一個系統錯誤訊息當地語系化。 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

下列範例顯示如何將 UserMessageIfClaimsPrincipalAlreadyExists 錯誤訊息當地語系化。


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

GetLocalizedStringsTransformationClaimType 值是用來將當地語系化的字串複製到宣告中。 如需詳細資訊，請參閱[GetLocalizedStringsTransformation 宣告轉換](string-transformations.md#getlocalizedstringstransformation)


```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

下列範例顯示如何將 GetLocalizedStringsTransformation 宣告轉換的輸出宣告當地語系化。

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicate

述詞值是用來將其中一個述[詞錯誤訊息](predicates.md)當地語系化。 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

下列範例示範如何將述詞解說文字當地語系化。

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

InputValidation 值是用來將其中一個[PredicateValidation](predicates.md)群組錯誤訊息當地語系化。 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

下列範例示範如何將述詞驗證群組的解說文字當地語系化。

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

UxElement 值是用來將其中一個使用者介面專案當地語系化。 下列範例顯示如何將 [繼續] 和 [取消] 按鈕當地語系化。

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>後續步驟

如需當地語系化範例，請參閱下列文章：

- [在 Azure Active Directory B2C 中使用自訂原則進行語言自訂](custom-policy-localization.md)
- [在 Azure Active Directory B2C 中使用使用者流程進行語言自訂](user-flow-language-customization.md)
