---
title: 自訂原則中的電話號碼宣告轉換
titleSuffix: Azure AD B2C
description: Azure AD B2C 中電話號碼宣告轉換的自訂原則參考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e175a81efc1ab0950c1fda314efb206ff97a2b7f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85385377"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>在 Azure AD B2C 中定義電話號碼宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

此文章提供在 Azure Active Directory B2C (Azure AD B2C) 中，使用 Identity Experience Framework 結構描述電話號碼宣告轉換的參考與範例。 如需一般宣告轉換的詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

將 `phoneNumber` 資料類型轉換成 `string` 資料類型。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  要轉換為字串的 ClaimType。 |
| OutputClaim | phoneNumberString | 字串 | 叫用此宣告轉換之後所產生的 ClaimType。 |

在此範例中，數值類型為 `phoneNumber` 的 cellPhoneNumber 宣告會轉換成數值類型為 `string` 的行動電話宣告。

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **phoneNumber**: +11234567890 (phoneNumber)
- 輸出宣告：
  - **phoneNumberString**: +11234567890 (string)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

此宣告轉換會驗證電話號碼的格式。 如果是有效的格式，請將其變更為 Azure AD B2C 所使用的標準格式。 如果所提供的電話號碼格式不正確，則會傳回錯誤訊息。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | 字串 |  電話號碼的字串宣告。 電話號碼必須是國際格式，請在前方加上「+」和「國家/地區」代碼來完成。 如果提供 `country` 輸入宣告，則電話號碼會是當地格式 (不含國家/地區代碼)。 |
| InputClaim | country | 字串 | [選擇性] 電話號碼的國家/地區代碼字串宣告採用 ISO3166 格式 (兩個字母的 ISO-3166 國家/地區代碼)。 |
| OutputClaim | outputClaim | phoneNumber | 此宣告轉換的結果。 |

**ConvertStringToPhoneNumberClaim** 宣告轉換一律會從[驗證技術設定檔](validation-technical-profile.md)執行，該設定檔會透過[自我判斷技術設定檔](self-asserted-technical-profile.md)或[顯示控制項](display-controls.md)來呼叫。 **UserMessageIfClaimsTransformationInvalidPhoneNumber** 自我判斷技術設定檔中繼資料會控制要呈現給使用者的錯誤訊息。

![錯誤訊息執行路徑的圖表](./media/phone-authentication/assert-execution.png)

您可以使用此宣告轉換來確保提供的字串宣告是有效的電話號碼。 如果沒有，則會擲回錯誤訊息。 下列範例會確認 **phoneString** ClaimType 確實是有效的電話號碼，然後傳回標準 Azure AD B2C 格式的電話號碼。 否則會擲回錯誤訊息。

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

自我判斷技術設定檔呼叫的驗證技術設定檔若包含此宣告轉換，則自我判斷技術設定檔可以定義錯誤訊息。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>範例 1

- 輸入宣告：
  - **phoneNumberString**:033 456-7890
  - **country**:DK
- 輸出宣告：
  - **outputClaim**: +450334567890

### <a name="example-2"></a>範例 2

- 輸入宣告：
  - **phoneNumberString**: +1 (123) 456-7890
- 輸出宣告：
  - **outputClaim**: +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

這會從輸入宣告中擷取國家/地區代碼和國家/地區號碼，並在提供的電話號碼無效時選擇性地擲回例外狀況。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | 字串 | 電話號碼的字串宣告。 電話號碼必須是國際格式，請在前方加上「+」和「國家/地區」代碼來完成。 |
| InputParameter | throwExceptionOnFailure | boolean | [選擇性] 指出當電話號碼無效時，是否擲回例外狀況的參數。 預設值為 false。 |
| InputParameter | countryCodeType | 字串 | [選擇性] 此參數會指出輸出宣告中的國家/地區代碼類型。 可用的值為 **CallingCode** (國家/地區的國際電話代碼，例如 + 1)，或 **ISO3166** (兩個字母的 ISO-3166 國家/地區代碼)。 |
| OutputClaim | nationalNumber | 字串 | 電話號碼中國家/地區號碼的字串宣告。 |
| OutputClaim | countryCode | 字串 | 電話號碼中國家/地區代碼的字串宣告。 |


如果 **GetNationalNumberAndCountryCodeFromPhoneNumberString** 宣告轉換是從[自我判斷技術設定檔](self-asserted-technical-profile.md)或[顯示控制項動作](display-controls.md#display-control-actions)所呼叫的[驗證技術設定檔](validation-technical-profile.md)中執行，則 **UserMessageIfPhoneNumberParseFailure** 自我判斷技術設定檔中繼資料會控制呈現給使用者的錯誤訊息。

![錯誤訊息執行路徑的圖表](./media/phone-authentication/assert-execution.png)

您可以使用此宣告轉換，將完整的電話號碼分割成國家/地區代碼和國家/地區號碼。 如果提供的電話號碼無效，您可以選擇擲回錯誤訊息。

下列範例會嘗試將電話號碼分割成國家/地區號碼和國家/地區代碼。 如果電話號碼有效，該電話號碼將會由國家/地區號碼覆寫。 如果電話號碼無效，將不會擲回例外狀況，而且電話號碼仍會有其原始值。

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

自我判斷技術設定檔呼叫的驗證技術設定檔若包含此宣告轉換，則自我判斷技術設定檔可以定義錯誤訊息。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>範例 1

- 輸入宣告：
  - **phoneNumber**: +49 (123) 456-7890
- 輸入參數：
  - **throwExceptionOnFailure**: false
  - **countryCodeType**:ISO3166
- 輸出宣告：
  - **nationalNumber**:1234567890
  - **countryCode**:DE

### <a name="example-2"></a>範例 2

- 輸入宣告：
  - **phoneNumber**: +49 (123) 456-7890
- 輸入參數
  - **throwExceptionOnFailure**: false
  - **countryCodeType**:CallingCode
- 輸出宣告：
  - **nationalNumber**:1234567890
  - **countryCode**: +49
