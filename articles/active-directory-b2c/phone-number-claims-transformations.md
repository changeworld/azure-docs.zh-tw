---
title: 自訂原則中的電話號碼宣告轉換
titleSuffix: Azure AD B2C
description: Azure AD B2C 中電話號碼宣告轉換的自訂原則參考。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5beef98f03c52ca022a7ab8047d3b392755c0bf
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212195"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>在 Azure AD B2C 中定義電話號碼宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供在 Azure Active Directory B2C （Azure AD B2C）中使用 Identity Experience Framework 架構的電話號碼宣告轉換的參考和範例。 如需一般宣告轉換的詳細資訊，請參閱[ClaimsTransformations](claimstransformations.md)。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

此宣告會驗證電話號碼的格式。 如果它是有效的格式，請將它變更為 Azure AD B2C 所使用的標準格式。 如果所提供的電話號碼格式不正確，則會傳回錯誤訊息。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | 字串 |  電話號碼的字串宣告。 電話號碼必須是國際格式，並以前置的「+」和「國家/地區代碼」完成。 如果提供輸入宣告 `country`，則電話號碼會是本機格式（不含國家/地區代碼）。 |
| InputClaim | country | 字串 | 選擇性電話號碼的國家/地區代碼的字串宣告（採用 ISO3166 格式）（兩個字母的 ISO-3166 國家/地區代碼）。 |
| OutputClaim | outputClaim | phoneNumber | 此宣告轉換的結果。 |

**ConvertStringToPhoneNumberClaim**宣告轉換一律會從[自我判斷技術配置](self-asserted-technical-profile.md)檔或[顯示控制項](display-controls.md)所呼叫的[驗證技術設定檔](validation-technical-profile.md)執行。 **UserMessageIfClaimsTransformationInvalidPhoneNumber**自我判斷技術設定檔中繼資料會控制呈現給使用者的錯誤訊息。

![錯誤訊息執行路徑的圖表](./media/phone-authentication/assert-execution.png)

您可以使用此宣告轉換來確保提供的字串宣告是有效的電話號碼。 如果沒有，則會擲回錯誤訊息。 下列範例會檢查**phoneString** ClaimType 是否確實是有效的電話號碼，然後傳回標準 Azure AD B2C 格式的電話號碼。 否則會擲回錯誤訊息。

```XML
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

呼叫包含此宣告轉換之驗證技術設定檔的自我判斷技術設定檔，可以定義錯誤訊息。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>範例 1

- 輸入宣告：
  - **phoneNumberString**： 045 456-7890
  - **國家/地區**：深色
- 輸出宣告：
  - **outputClaim**： + 450546148120

### <a name="example-2"></a>範例 2

- 輸入宣告：
  - **phoneNumberString**： + 1 （123）456-7890
- 輸出宣告： 
  - **outputClaim**： + 11234567890

## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

這會從輸入宣告中解壓縮國家/地區代碼和國家/地區號碼，並在提供的電話號碼無效時選擇性地擲回例外狀況。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | 字串 | 電話號碼的字串宣告。 電話號碼必須是國際格式，並以前置的「+」和「國家/地區代碼」完成。 |
| InputParameter | throwExceptionOnFailure | boolean | 選擇性參數，指出當電話號碼無效時，是否擲回例外狀況。 預設值為 false。 |
| InputParameter | countryCodeType | 字串 | 選擇性參數，指出輸出宣告中的國家（地區）代碼類型。 可用的值為**CallingCode** （國家/地區的國際電話程式碼，例如 + 1）或**ISO3166** （兩個字母的 ISO-3166 國家/地區代碼）。 |
| OutputClaim | nationalNumber | 字串 | 電話號碼國家/地區號碼的字串宣告。 |
| OutputClaim | countryCode | 字串 | 電話號碼的國家（地區）代碼的字串宣告。 |


如果**GetNationalNumberAndCountryCodeFromPhoneNumberString**宣告轉換是從[自我判斷技術配置](self-asserted-technical-profile.md)檔或[顯示控制項動作](display-controls.md#display-control-actions)所呼叫的[驗證技術設定檔](validation-technical-profile.md)執行，則**UserMessageIfPhoneNumberParseFailure**自我判斷技術設定檔中繼資料會控制呈現給使用者的錯誤訊息。

![錯誤訊息執行路徑的圖表](./media/phone-authentication/assert-execution.png)

您可以使用此宣告轉換，將完整的電話號碼分割成國家代碼和國家/地區號碼。 如果提供的電話號碼無效，您可以選擇擲回錯誤訊息。

下列範例會嘗試將電話號碼分割成國家/地區代碼。 如果電話號碼有效，電話號碼將會由國家/地區號碼覆寫。 如果電話號碼無效，將不會擲回例外狀況，而且電話號碼仍會有其原始值。

```XML
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

呼叫包含此宣告轉換之驗證技術設定檔的自我判斷技術設定檔，可以定義錯誤訊息。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>範例 1

- 輸入宣告：
  - **phoneNumber**： + 49 （123）456-7890
- 輸入參數：
  - **throwExceptionOnFailure**： false
  - **countryCodeType**： ISO3166
- 輸出宣告：
  - **nationalNumber**：1234567890
  - **countryCode**： DE

### <a name="example-2"></a>範例 2

- 輸入宣告：
  - **phoneNumber**： + 49 （123）456-7890
- 輸入參數
  - **throwExceptionOnFailure**： false
  - **countryCodeType**： CallingCode
- 輸出宣告：
  - **nationalNumber**：1234567890
  - **countryCode**： + 49
