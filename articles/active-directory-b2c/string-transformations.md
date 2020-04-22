---
title: 自訂策略的字串聲明轉換範例
titleSuffix: Azure AD B2C
description: 字串聲明轉換範例,用於 Azure 活動目錄 B2C 的識別體驗框架 (IEF) 架構。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f08107874598a68fb5ce2a1a8a98b6a81d7b94d4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756787"
---
# <a name="string-claims-transformations"></a>字串宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure 活動目錄 B2C (Azure AD B2C) 中使用標識體驗框架架構架構的字串聲明轉換的範例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

比較兩個宣告，如果根據指定的比較 inputClaim1、inputClaim2 和 stringComparison 它們並不相等，即會擲回例外狀況。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字串 | 要比較的第一個宣告類型。 |
| InputClaim | inputClaim2 | 字串 | 要比較的第二個宣告類型。 |
| InputParameter | stringComparison | 字串 | 字串比較，其中一個值：Ordinal、OrdinalIgnoreCase。 |

**AssertStringClaimsAreEqual**聲明轉換始終從由[自斷言技術配置檔](self-asserted-technical-profile.md)或[DisplayConrtol](display-controls.md)調用的[驗證技術配置檔](validation-technical-profile.md)執行。 自`UserMessageIfClaimsTransformationStringsAreNotEqual`斷言技術配置檔的中繼資料控制向使用者顯示的錯誤訊息。 錯誤訊息可以[在本地化](localization-string-ids.md#claims-transformations-error-messages)。


![AssertStringClaimsAreEqual 執行](./media/string-transformations/assert-execution.png)

您可以使用此宣告轉換來確定兩個 ClaimTypes 具有相同的值。 如果沒有，則會擲回錯誤訊息。 下列範例會檢查 **strongAuthenticationEmailAddress** ClaimType 等於 **email** ClaimType。 否則會擲回錯誤訊息。

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


**login-NonInteractive** 驗證技術設定檔會呼叫 **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** 宣告轉換。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自我判斷技術設定檔會呼叫驗證 **login-NonInteractive** 技術設定檔。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **輸入索賠1**:someone@contoso.com
  - **輸入索賠2**:someone@outlook.com
- 輸入參數：
  - **stringComparison**：ordinalIgnoreCase
- 結果：擲回錯誤

## <a name="changecase"></a>ChangeCase

根據運算子，將所提供宣告的大小寫變更為小寫或大寫字母。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字串 | 要更改的聲明類型。 |
| InputParameter | toCase | 字串 | 下列其中一個值：`LOWER` 或 `UPPER`。 |
| OutputClaim | outputClaim | 字串 | 叫用此宣告轉換之後所產生的 ClaimType。 |

使用此宣告轉換來將任何字串 ClaimType 變更為小寫或大寫字母。

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **電子郵件**:SomeOne@contoso.com
- 輸入參數：
    - **toCase**：LOWER
- 輸出宣告：
  - **電子郵件**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

從轉換中提供的輸入參數創建字串聲明。

| Item | TransformationClaimType | 資料類型 | 注意 |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | 字串 | 要設置的字串。 這個輸入參數支援[字串宣告轉換表示式](string-transformations.md#string-claim-transformations-expressions)。 |
| OutputClaim | createdClaim | 字串 | 叫用此宣告轉換之後所產生的 ClaimType，並含有輸入參數中指定的值。 |

使用此宣告轉換來設定字串 ClaimType 值。

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入參數：
    - **value**：Contoso terms of service...
- 輸出宣告：
    - **createdClaim**：TOS ClaimType 包含 "Contoso terms of service..." 值。

## <a name="compareclaims"></a>CompareClaims

判斷某個字串宣告是否等於另一個。 結果是新的布林值 ClaimType 且含有 `true` 或 `false` 的值。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字串 | 要比較的第一個宣告類型。 |
| InputClaim | inputClaim2 | 字串 | 要比較的第二個宣告類型。 |
| InputParameter | ! 運算子之後 | 字串 | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
| InputParameter | ignoreCase | boolean | 指定這個比較是否應忽略要比較之字串的大小寫。 |
| OutputClaim | outputClaim | boolean | 叫用此宣告轉換之後所產生的 ClaimType。 |

使用此宣告轉換來檢查某個宣告是否等於另一個宣告。 例如，下列宣告轉換會檢查 **email** 宣告的值是否等於 **Verified.Email** 宣告。

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **輸入索賠1**:someone@contoso.com
  - **輸入索賠2**:someone@outlook.com
- 輸入參數：
    - **operator**：NOT EQUAL
    - **忽略Case**: 真
- 輸出宣告：
    - **outputClaim**：true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

判斷宣告值是否等於輸入參數值。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | 字串 | 要比較的宣告類型。 |
| InputParameter | ! 運算子之後 | 字串 | 可能的值：`EQUAL` 或 `NOT EQUAL`。 |
| InputParameter | compareTo | 字串 | 字串比較，其中一個值：Ordinal、OrdinalIgnoreCase。 |
| InputParameter | ignoreCase | boolean | 指定這個比較是否應忽略要比較之字串的大小寫。 |
| OutputClaim | outputClaim | boolean | 叫用此宣告轉換之後所產生的 ClaimType。 |

您可以使用此宣告轉換來檢查某個宣告是否等於您指定的值。 例如，下列宣告轉換會檢查 **termsOfUseConsentVersion** 宣告的值是否等於 `v1`。

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例
- 輸入宣告：
    - **inputClaim1**：v1
- 輸入參數：
    - **compareTo**：V1
    - **operator**：EQUAL
    - **ignoreCase**：true
- 輸出宣告：
    - **outputClaim**：true

## <a name="createrandomstring"></a>CreateRandomString

使用隨機號碼產生器來建立隨機字串。 如果隨機號碼產生器的類型為 `integer`，可能會選擇性地提供種子參數和最大數。 選擇性的字串格式參數允許使用它來將輸出格式化，而選擇性的 base64 參數會指定輸出是否為 base64 編碼的 randomGeneratorType [guid, integer] outputClaim (字串)。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | 字串 | 指定要產生的隨機值：`GUID` (全域唯一識別碼) 或 `INTEGER` (數字)。 |
| InputParameter | stringFormat | 字串 | [選擇性] 將隨機值格式化。 |
| InputParameter | base64 | boolean | [選擇性] 將隨機值轉換為 base64。 如果套用字串格式，則會將字串格式之後的值編碼為 base64。 |
| InputParameter | maximumNumber | int | [選擇性] 僅適用於 `INTEGER` randomGeneratorType。 指定最大數字。 |
| InputParameter | seed  | int | [選擇性] 僅適用於 `INTEGER` randomGeneratorType。 指定隨機值的種子。 注意：相同的種子會產生相同的隨機數字序列。 |
| OutputClaim | outputClaim | 字串 | 叫用此宣告轉換之後將產生的 ClaimType。 隨機值。 |

下列範例會產生全域唯一識別碼。 此宣告轉換可用來建立隨機的 UPN (使用者主體名稱)。

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>範例

- 輸入參數：
    - **randomGeneratorType**：GUID
- 輸出宣告：
    - **outputClaim**：bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

下列範例會產生介於 0 到 1000 之間的整數隨機值。 此值會格式化為 OTP_{隨機值}。

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入參數：
    - **randomGeneratorType**：INTEGER
    - **maximumNumber**：1000
    - **stringFormat**：OTP_{0}
    - **base64**：false
- 輸出宣告：
    - **outputClaim**：OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

根據提供的格式字串來將宣告格式化。 此轉換會使用 C# `String.Format` 方法。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |字串 |做為字串格式 {0} 參數的 ClaimType。 |
| InputParameter | stringFormat | 字串 | 字串格式，包括 {0} 參數。 這個輸入參數支援[字串宣告轉換表示式](string-transformations.md#string-claim-transformations-expressions)。  |
| OutputClaim | outputClaim | 字串 | 叫用此宣告轉換之後所產生的 ClaimType。 |

使用此宣告轉換，利用一個參數 {0} 來將任何字串格式化。 下列範例會建立 **userPrincipalName**。 所有社交識別提供者技術設定檔 (例如 `Facebook-OAUTH`) 會呼叫 **CreateUserPrincipalName** 來產生 **userPrincipalName**。

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：5164db16-3eee-4629-bfda-dcc3326790e9
- 輸入參數：
    - **stringFormat**：cpim_{0}@{RelyingPartyTenantId}
- 輸出宣告：
  - **輸出索賠**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

根據提供的格式字串來將兩個宣告格式化。 此轉換會使用 C# `String.Format` 方法。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |字串 | 做為字串格式 {0} 參數的 ClaimType。 |
| InputClaim | inputClaim | 字串 | 做為字串格式 {1} 參數的 ClaimType。 |
| InputParameter | stringFormat | 字串 | 字串格式，包括 {0} 和 {1} 參數。 這個輸入參數支援[字串宣告轉換表示式](string-transformations.md#string-claim-transformations-expressions)。   |
| OutputClaim | outputClaim | 字串 | 叫用此宣告轉換之後所產生的 ClaimType。 |

使用此宣告轉換，利用兩個參數 ({0} 和 {1}) 來將任何字串格式化。 下列範例會使用指定的格式來建立 **displayName**：

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim1**：Joe
    - **inputClaim2**：Fernando
- 輸入參數：
    - **字串格式**{0}:{1}
- 輸出宣告：
    - **outputClaim**：Joe Fernando

## <a name="getlocalizedstringstransformation"></a>取得本地化字串轉換

將當地語系化字串複製到聲明中。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | 本地化字串的名稱 | 字串 | 呼叫此聲明轉換後生成的聲明類型的清單。 |

要使用「取得本地化字串轉換」聲明轉換:

1. 定義[當地語系化字串](localization.md)並將其與[自斷言的技術配置文件](self-asserted-technical-profile.md)相關聯。
1. 元素`ElementType`必須`LocalizedString`設定為`GetLocalizedStringsTransformationClaimType`。
1. `StringId`是您定義的唯一標識符,並在聲明轉換中稍後使用它。
1. 在聲明轉換中,指定要使用本地化字串設置的聲明清單。 是`ClaimTypeReferenceId`對策略中的「聲明架構」 部分中已定義的聲明類型的引用。 是`TransformationClaimType``LocalizedString`元素`StringId`中 定義的本地化字串的名稱。
1. 在[自斷言的技術配置檔](self-asserted-technical-profile.md)或[顯示控制](display-controls.md)輸入或輸出聲明轉換中,引用您的聲明轉換。

![取得本地化字串轉換](./media/string-transformations/get-localized-strings-transformation.png)

下面的範例查找來自本地化字串的電子郵件主題、正文、代碼訊息和電子郵件的簽名。 這些聲明後來被自定義電子郵件驗證範本使用。

為英語(預設)和西班牙語定義本地化字串。

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

聲明轉換將聲明類型*主體*的`StringId`值與*email_subject*的值設置。

```XML
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸出宣告：
  - **主題**: Contoso 帳號電子郵件驗證碼
  - **消息**:感謝您驗證您的帳戶!
  - **程式碼簡介**: 您的代碼是
  - **簽署**: 真誠


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

從宣告 **Restriction** 集合查詢項目。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | 字串 | 包含要使用 **Restriction** 集合在 **restrictionValueClaim** 宣告中查詢之文字的宣告。  |
| OutputClaim | restrictionValueClaim | 字串 | 包含 **Restriction** 集合的宣告。 叫用宣告轉換之後，此宣告的值會包含所選取項目的值。 |

下列範例會根據錯誤索引鍵查詢錯誤訊息說明。 **ResponseMsg** 宣告會包含要呈現給使用者或傳送到信賴憑證者的錯誤訊息集合。

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
宣告轉換會查詢項目的文字並傳回其值。 如果限制會使用 `<LocalizedCollection>` 進行當地語系化，則宣告轉換會傳回當地語系化的值。

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **mapFromClaim**：B2C_V1_90001
- 輸出宣告：
    - **限制價值聲明**:您不能登錄,因為您是未成年人。

## <a name="lookupvalue"></a>LookupValue

根據另一個宣告的值，從值清單中查詢某個宣告值。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | 字串 | 包含查閱值的宣告 |
| InputParameter | |字串 | InputParameters 的集合。 |
| InputParameter | errorOnFailedLookup | boolean | 控制沒有相符的查閱時是否要傳回錯誤。 |
| OutputClaim | inputParameterId | 字串 | 叫用此宣告轉換之後將產生的 ClaimType。 符合`Id`的值 。 |

下列範例會查詢其中一個 inpuParameters 集合中的網域名稱。 宣告轉換會查詢識別項中的網域名稱，並傳回其值 (應用程式識別碼)。

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputParameterId**：test.com
- 輸入參數：
    - **contoso.com**：13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**：0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**：c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**：false
- 輸出宣告：
    - **outputClaim**：c7026f88-4299-4cdb-965d-3f166464b8a9

輸入`errorOnFailedLookup``true`參數設定為時 **,LookupValue**聲明轉換始終從由[自斷言技術設定檔](self-asserted-technical-profile.md)或[DisplayConrtol](display-controls.md)調用的[驗證技術設定檔](validation-technical-profile.md)執行。 自`LookupNotFound`斷言技術配置檔的中繼資料控制向使用者顯示的錯誤訊息。

![AssertStringClaimsAreEqual 執行](./media/string-transformations/assert-execution.png)

下列範例會查詢其中一個 inpuParameters 集合中的網域名稱。 聲明轉換查找識別碼中的網域名稱並傳回其值(應用程式ID),或引發錯誤訊息。

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **輸入參數Id**:live.com
- 輸入參數：
    - **contoso.com**：13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**：0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**：c7026f88-4299-4cdb-965d-3f166464b8a9
    - **錯誤 中失敗尋找**: 真
- 錯誤：
    - 輸入參數 ID 清單中的輸入聲明值未找到匹配項,錯誤 OnFailed 搜尋為 true。


## <a name="nullclaim"></a>NullClaim

清除指定宣告的值。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | 字串 | 宣告的值設定為 NULL。 |

使用此聲明轉換可以從聲明屬性包中刪除不必要的數據,以便會話 Cookie 變小。 下列範例會移除 `TermsOfService` 宣告類型的值。

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- 輸入宣告：
    - **outputClaim**：歡迎使用 Contoso 應用程式。 如果您繼續瀏覽並使用此網站，表示您同意遵循並遵守下列條款及條件...
- 輸出宣告：
    - **outputClaim**：NULL

## <a name="parsedomain"></a>ParseDomain

取得電子郵件地址的網域部分。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | 字串 | 包含電子郵件地址的 ClaimType。 |
| OutputClaim | 網域 | 字串 | 叫用此宣告轉換之後所產生的 ClaimType - 網域。 |

使用此宣告轉換來剖析使用者 @ 符號之後的網域名稱。 下列宣告轉換示範如何從 **email** 宣告中剖析網域名稱。

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **電子郵件地址**:joe@outlook.com
- 輸出宣告：
    - **domain**：outlook.com

## <a name="setclaimsifregexmatch"></a>設定索賠IfRegex匹配

檢查`claimToMatch`字串聲明和`matchTo`輸入參數是否相等,並將輸出聲明與輸入參數`outputClaimIfMatched`中的值一起設置,以及比較結果輸出聲明,該聲明將設置`true`為`false`或 基於比較結果。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | 字串 | 要比較的宣告類型。 |
| InputParameter | matchTo | 字串 | 要比對的規則運算式。 |
| InputParameter | outputClaimIfMatched | 字串 | 字串相等時要設定的值。 |
| InputParameter | 擷取群組 | boolean | [選擇性的]指定 Regex 匹配是否應提取組值。 可能的值:`true``false`或 (預設值)。 | 
| OutputClaim | outputClaim | 字串 | 如果正規表示式比對,則此輸出的參數包含輸入參數的`outputClaimIfMatched`值 。 或 null,如果沒有匹配。 |
| OutputClaim | 正規表示式比較結果索賠 | boolean | 正則表達式匹配結果輸出聲明類型,該類型應設置為`true`匹配結果`false`或 基於匹配結果。 |
| OutputClaim| 宣告的名稱| 字串 | 如果提取組輸入參數設定為 true,則已呼叫此聲明轉換後生成的聲明類型清單。 聲明類型的名稱必須與 Regex 組名稱匹配。 | 

### <a name="example-1"></a>範例 1

根據電話號碼正則表達式模式檢查提供的電話號碼是否有效。

```XML
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- 輸入宣告：
    - **聲明匹配**:"64854114520"
- 輸入參數：
    - **比賽到**: "{0-9}{4,16}$"
    - **輸出索賠如果匹配**:"是Phone"
- 輸出宣告：
    - **輸出索賠**:"是電話"
    - **regex 比較結果索賠**: 真

### <a name="example-2"></a>範例 2

檢查提供的電子郵件位址是否有效,並返回電子郵件別名。

```XML
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- 輸入宣告：
    - **聲稱匹配**:"emily@contoso.com "
- 輸入參數：
    - **符合到**:`(?&lt;mailAlias&gt;.*)@(.*)$`
    - **輸出索賠如果匹配**:"是電子郵件"
    - **擷取群組**: true
- 輸出宣告：
    - **輸出索賠**:"isEmail"
    - **regex 比較結果索賠**: 真
    - **郵件別名**: 埃米莉
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

檢查宣告字串和 `matchTo` 輸入參數相等，並使用 `stringMatchMsg` 和 `stringMatchMsgCode` 輸入參數中存在的值來設定輸出宣告，以及比較結果輸出宣告，其會根據比較的結果設定為 `true` 或 `false`。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字串 | 要比較的宣告類型。 |
| InputParameter | matchTo | 字串 | 要與 `inputClaim` 比較的字串。 |
| InputParameter | stringComparison | 字串 | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | stringMatchMsg | 字串 | 字串相等時要設定的第一個值。 |
| InputParameter | stringMatchMsgCode | 字串 | 字串相等時要設定的第二個值。 |
| OutputClaim | outputClaim1 | 字串 | 如果字串相等，此輸出宣告會包含 `stringMatchMsg` 輸入參數的值。 |
| OutputClaim | outputClaim2 | 字串 | 如果字串相等，此輸出宣告會包含 `stringMatchMsgCode` 輸入參數的值。 |
| OutputClaim | stringCompareResultClaim | boolean | 比較結果輸出宣告類型，其會根據比較的結果設定為 `true` 或 `false`。 |

您可以使用此宣告轉換來檢查某個宣告是否等於您指定的值。 例如，下列宣告轉換會檢查 **termsOfUseConsentVersion** 宣告的值是否等於 `v1`。 如果是，將值變更為 `v2`。

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：v1
- 輸入參數：
    - **matchTo**：V1
    - **字串比較**: 放大縮小字型功能 放大縮小字型功能
    - **stringMatchMsg**：B2C_V1_90005
    - **stringMatchMsgCode**：TOS 會升級到 v2
- 輸出宣告：
    - **outputClaim1**：B2C_V1_90005
    - **outputClaim2**：TOS 會升級到 v2
    - **stringCompareResultClaim**：true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

檢查宣告字串和 `matchTo` 輸入參數相等，並使用 `outputClaimIfMatched` 輸入參數中存在的值來設定輸出宣告，以及比較結果輸出宣告，其會根據比較的結果設定為 `true` 或 `false`。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | 字串 | 要比較的宣告類型。 |
| InputParameter | matchTo | 字串 | 要與 inputClaim 比較的字串。 |
| InputParameter | stringComparison | 字串 | 可能的值：`Ordinal` 或 `OrdinalIgnoreCase`。 |
| InputParameter | outputClaimIfMatched | 字串 | 字串相等時要設定的值。 |
| OutputClaim | outputClaim | 字串 | 如果字串相等，此輸出宣告會包含 `outputClaimIfMatched` 輸入參數的值。 或者，如果字串不符，則為 Null。 |
| OutputClaim | stringCompareResultClaim | boolean | 比較結果輸出宣告類型，其會根據比較的結果設定為 `true` 或 `false`。 |

例如，下列宣告轉換會檢查 **ageGroup** 宣告的值是否等於 `Minor`。 如果是，會將值傳回到 `B2C_V1_90001`。

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **claimToMatch**：Minor
- 輸入參數：
    - **matchTo**：Minor
    - **字串比較**: 放大縮小字型功能 放大縮小字型功能
    - **outputClaimIfMatched**：B2C_V1_90001
- 輸出宣告：
    - **isMinorResponseCode**：B2C_V1_90001
    - **isMinor**：true


## <a name="stringcontains"></a>StringContains

確定指定的子字串是否發生在輸入聲明中。 結果是新的布林值 ClaimType 且含有 `true` 或 `false` 的值。 `true`如果值參數出現在此字串中,否則,`false`否則 ,

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字串 | 要搜索的聲明類型。 |
|InputParameter|contains|字串|要搜索的值。|
|InputParameter|ignoreCase|字串|指定此比較是否應忽略要比較的字串的情況。|
| OutputClaim | outputClaim | 字串 | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 如果子字串發生在輸入聲明中,則布爾指示器。 |

使用此聲明轉換可檢查字串聲明類型是否包含子字串。 下面的範例,檢查`roles`字串聲明類型是否包含**管理員**的值。

```XML
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **輸入聲明**:"管理員、批准者、編輯"
- 輸入參數：
    - **包含**:"管理員"
    - **忽略Case**: 真
- 輸出宣告：
    - **outputClaim**：true

## <a name="stringsubstring"></a>字串子字串

提取字串聲明類型的部分,從指定位置的字元開始,並返回指定的字元數。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字串 | 包含字串的聲明類型。 |
| InputParameter | startIndex | int | 這個執行個體中子字串之以零為起始的起始字元位置。 |
| InputParameter | 長度 | int | 子字串中的字元數。 |
| OutputClaim | outputClaim | boolean | 等效於此實例中從 startIndex 開始的長度子字串,如果 startIndex 等於此實例的長度為空,則長度為零。 |

例如,獲取電話號碼國家/地區首碼。


```XML
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>範例

- 輸入宣告：
    - **輸入索賠**:"+1644114520"
- 輸入參數：
    - **開始索引**: 0
    - **長度**: 2
- 輸出宣告：
    - **輸出索賠**: "+1"

## <a name="stringreplace"></a>字串取代

搜尋指定值的聲明類型字串,並返回一個新的聲明類型字串,其中當前字串中指定字串的所有匹配項都將替換為另一個指定的字串。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字串 | 包含字串的聲明類型。 |
| InputParameter | oldValue | 字串 | 要搜索的字串。 |
| InputParameter | newValue | 字串 | 要取代的所有符合的字串`oldValue` |
| OutputClaim | outputClaim | boolean | 等效於當前字串的字串,只不過舊值的所有實例都替換為 newValue。 如果在當前實例中找不到舊值,則該方法將返回當前實例不變。 |

例如,透過刪除`-`字元來規範化電話號碼


```XML
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>範例

- 輸入宣告：
    - **輸入索賠**:"+164-411-452-054"
- 輸入參數：
    - **舊值**:"-"
    - **長度**:""
- 輸出宣告：
    - **輸出索賠**:"+164411452054"

## <a name="stringjoin"></a>字串聯結

使用每個元素或成員之間的指定分隔元連接指定字串集合聲明類型的元素。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 包含要串連之字串的集合。 |
| InputParameter | 分隔符號 | 字串 | 用分隔符號的字串, 如逗`,`號 。 |
| OutputClaim | outputClaim | 字串 | 由`inputClaim`字串集合的成員組成的字串,由`delimiter`輸入參數分隔。 |

下面的範例採用使用者角色的字串集合,並將其轉換為逗號分隔符元串。 可以使用此方法在 Azure AD 使用者帳戶中儲存字串集合。 稍後,當您從目錄中讀取帳戶時,使用`StringSplit`將逗號分隔符字串轉換回字串集合。

```XML
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **輸入要求**: [ 管理員' " 作者" > >
- 輸入參數：
  - **分隔符**:""
- 輸出宣告：
  - **輸出聲明**:"管理員、作者、讀者"


## <a name="stringsplit"></a>字串分割

傳回一個字串陣列,該字串陣列包含此實例中的子字串,該子字串由指定字串的元素分隔。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字串 | 包含要拆分的子字串的字串聲明類型。 |
| InputParameter | 分隔符號 | 字串 | 用分隔符號的字串, 如逗`,`號 。 |
| OutputClaim | outputClaim | stringCollection | 元素包含此字串中的子字串的字串集合,該子字串由`delimiter`輸入參數分隔。 |

下面的範例採用使用者角色的逗號分隔符字串,並將其轉換為字串集合。

```XML
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **輸入要求**:"管理員、作者、讀者"
- 輸入參數：
  - **分隔符**:""
- 輸出宣告：
  - **輸出索賠**: [ 管理員' " 作者" " 讀者" |

## <a name="string-claim-transformations-expressions"></a>字串聲明轉換表示式
Azure AD B2C 自訂策略中的聲明轉換表示式提供有關租戶 ID 和技術配置檔 ID 的上下文資訊。

  | 運算是 | 描述 | 範例 |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | 技術配置檔識別碼名稱。 | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | 信賴憑證者原則的租用戶識別碼。 | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | 信任架構的租用戶識別碼。 | your-tenant.onmicrosoft.com |
