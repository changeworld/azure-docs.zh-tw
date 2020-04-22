---
title: 本地化 - Azure 活動目錄 B2C
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
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
| ElementType | 是 | 可能的值:[請表示您](#claimsprovider)[所使用的](#claimtype)字串,[錯誤訊息](#errormessage),[取得本地化字串轉換的聲明型態](#getlocalizedstringstransformationclaimtype),[並請輸入](#predicate)[此問題](#inputvalidation), 或[UxElement](#uxelement).   | 
| ElementId | 是 | 如果**ElementType**`ClaimType``Predicate`設定為`InputValidation`,或, 此元素包含對聲明架構部分中已定義的聲明類型的引用。 |
| StringId | 是 | 如果 **ElementType** 設為 `ClaimType`，則此元素會包含對宣告類型之屬性的參考。 可能的值：`DisplayName`、`AdminHelpText` 或 `PatternHelpText`。 `DisplayName` 值用於設定宣告顯示名稱。 `AdminHelpText` 值用於設定宣告使用者的說明文字名稱。 `PatternHelpText` 值用於設定宣告模式說明文字。 如果 **ElementType** 設為 `UxElement`，則此元素會包含使用者介面元素之屬性的參考。 如果 **ElementType** 設為 `ErrorMessage`，則此元素會指定錯誤訊息的識別碼。 如需 `UxElement` 識別碼的完整清單，請參閱[當地語系化字串識別碼](localization-string-ids.md)。|

## <a name="elementtype"></a>ElementType

元素類型引用要當地語系化的策略中的聲明類型、聲明轉換或使用者介面元素。

| 要本地端的元素 | ElementType | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| 識別提供者名稱 |`ClaimsProvider`| | 宣告交換元素的識別碼|
| 宣告類型屬性|`ClaimType`|宣告類型的名稱| 要當地語系化的聲明的屬性。 可能的值: `AdminHelpText` `DisplayName``PatternHelpText`、`UserHelpText`與 。|
|錯誤訊息|`ErrorMessage`||錯誤訊息的識別碼 |
|將本地化字串複製到宣告|`GetLocalizedStringsTra nsformationClaimType`||輸出宣告的名稱|
|謂詞使用者訊息|`Predicate`|謂詞的名稱| 要當地語系化的謂詞的屬性。 可能的值: `HelpText`.|
|謂片語使用者訊息|`InputValidation`|謂詞驗證元素的 ID。|謂片語元素的 ID。 謂片語必須是元素 Id 中定義的謂詞驗證元素的子級。|
|使用者介面元素 |`UxElement` | | 要當地語系化的使用者介面元素的 ID。|

## <a name="examples"></a>範例

### <a name="claimsprovider"></a>ClaimsProvider

聲明提供程式值用於當地語系化其中一個聲明提供程式顯示名稱。 

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

下面的範例展示如何當地語系化聲明提供程式的顯示名稱。

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>ClaimType

聲明類型值用於本地化聲明屬性之一。 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

下面的範例展示如何本地化電子郵件聲明類型的顯示名稱、使用者幫助文本和 PatternHelpText 屬性。

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

ErrorMessage 值用於當地語系化其中一個系統錯誤消息。 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

下面的範例展示如何當地語系化使用者Messageif,如果聲稱存在已存在錯誤消息。


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>取得本地化字串轉換索賠類型

"獲取本地化字串轉換聲明類型"值用於將本地化字串複製到聲明中。 有關詳細資訊,請參閱[獲取本地化字串轉換聲明轉換](string-transformations.md#getlocalizedstringstransformation)


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

下面的範例展示如何當地語系化「獲取本地化字串轉換」聲明轉換的輸出聲明。

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicate

謂詞值用於當地語系化其中一個[謂詞](predicates.md)錯誤消息。 

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

下面的範例展示如何當地語系化謂詞説明文本。

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>輸入驗證

輸入驗證值用於當地語系化[一個謂詞驗證](predicates.md)組錯誤消息。 

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

下面的範例展示如何當地語系化謂詞驗證組幫助文本。

```XML
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

UxElement 值用於當地語系化其中一個用戶介面元素。 下面的範例展示如何當地語系化"繼續"和"取消"按鈕。

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## <a name="next-steps"></a>後續步驟

有關當地語系化範例,請參閱以下文章:

- [使用 Azure 的目錄 B2C 中的自訂策略進行語言自訂](custom-policy-localization.md)
- [使用 Azure 的目錄 B2C 的使用者串流進行語言自訂](user-flow-language-customization.md)
