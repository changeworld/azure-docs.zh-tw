---
title: 自訂策略中的電話號碼聲明轉換
titleSuffix: Azure AD B2C
description: Azure AD B2C 中電話號碼聲明轉換的自訂策略引用。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd26b2b475e293a1fda1b007289ba7c3eef35136
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183921"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>在 Azure AD B2C 中定義電話號碼聲明轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure 活動目錄 B2C （Azure AD B2C） 中使用標識體驗框架架構模式的電話號碼聲明轉換的參考和示例。 有關聲明轉換的詳細資訊，請參閱[聲明轉換](claimstransformations.md)。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>轉換電話號碼索賠字串

將`phoneNumber`資料類型轉換為`string`資料類型。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | phoneNumber |  要轉換為字串的 ClaimType。 |
| OutputClaim | 電話數位字串 | 字串 | 叫用此宣告轉換之後所產生的 ClaimType。 |

在此示例中，具有 數值型別的手機PhoneNumber聲明`phoneNumber`將轉換為數值型別的手機電話聲明， 其數值型別為`string`。

```XML
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
  - **電話號碼**： +11234567890 （電話）
- 輸出宣告：
  - **電話編號字串**： +11234567890 （字串）


## <a name="convertstringtophonenumberclaim"></a>轉換StringtoPhone號碼索賠

此聲明轉換驗證電話號碼的格式。 如果它是有效格式，則將其更改為 Azure AD B2C 使用的標準格式。 如果提供的電話號碼不是有效格式，則會返回一條錯誤訊息。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 電話數位字串 | 字串 |  電話號碼的字串聲明。 電話號碼必須採用國際格式，並附有領先的"+"和國家/地區代碼。 如果提供輸入`country`聲明，電話號碼為本地格式（沒有國家/地區代碼）。 |
| InputClaim | country | 字串 | [可選]以 ISO3166 格式（兩個字母的 ISO-3166 國家/地區代碼）對電話號碼的國家/地區代碼的字串聲明。 |
| OutputClaim | outputClaim | phoneNumber | 此聲明轉換的結果。 |

**ConvertStringToPhoneNumberClaim**聲明轉換始終從由[自斷言技術設定檔](self-asserted-technical-profile.md)或[顯示控制項](display-controls.md)調用的[驗證技術設定檔](validation-technical-profile.md)執行。 **UserMessageIfClaims 轉換無效PhoneNumber**自斷言的技術設定檔中繼資料控制向使用者顯示的錯誤訊息。

![錯誤訊息執行路徑圖](./media/phone-authentication/assert-execution.png)

您可以使用此聲明轉換來確保提供的字串聲明是有效的電話號碼。 如果沒有，則會擲回錯誤訊息。 下面的示例檢查**電話String**聲明類型是否確實是一個有效的電話號碼，然後以標準 Azure AD B2C 格式返回電話號碼。 否則，將引發一條錯誤訊息。

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

調用包含此聲明轉換的驗證技術設定檔的自斷言技術設定檔可以定義錯誤訊息。

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
  - **電話號碼字串**： 033 456-7890
  - **國家**/ DK
- 輸出宣告：
  - **輸出索賠**： +450334567890

### <a name="example-2"></a>範例 2

- 輸入宣告：
  - **電話數位字串**： +1 （123） 456-7890
- 輸出宣告：
  - **輸出索賠**： +11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>從電話號碼字串獲取國家號碼和國家代碼

這將從輸入聲明中提取國家/地區代碼和國家號碼，如果提供的電話號碼無效，則可以選擇引發異常。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | 字串 | 電話號碼的字串聲明。 電話號碼必須採用國際格式，並附有領先的"+"和國家/地區代碼。 |
| InputParameter | 引發異常失敗 | boolean | [可選]參數，指示電話號碼無效時是否引發異常。 預設值為 false。 |
| InputParameter | 國家代碼類型 | 字串 | [可選]指示輸出聲明中國家/地區代碼類型的參數。 可用值是 **"調用代碼**"（國家/地區的國際呼叫代碼，例如 +1）或**ISO3166（** 兩個字母的 ISO-3166 國家/地區代碼）。 |
| OutputClaim | 國家編號 | 字串 | 電話號碼的國家號碼的字串聲明。 |
| OutputClaim | countryCode | 字串 | 電話號碼的國家/地區代碼的字串聲明。 |


如果**GetNationalNumberandCountryCodeFromFromNumberString**聲明轉換是從[由自斷言技術設定檔](self-asserted-technical-profile.md)或[顯示控制操作](display-controls.md#display-control-actions)調用的[驗證技術設定檔](validation-technical-profile.md)執行的，則**UserMessageIfPhoneNumberParse 失敗**自斷言技術設定檔中繼資料控制向使用者顯示的錯誤訊息。

![錯誤訊息執行路徑圖](./media/phone-authentication/assert-execution.png)

您可以使用此聲明轉換將完整電話號碼拆分為國家/地區代碼和國家/地區號碼。 如果提供的電話號碼無效，您可以選擇引發錯誤訊息。

下面的示例嘗試將電話號碼拆分為國家號碼和國家/地區代碼。 如果電話號碼有效，電話號碼將被國家號碼覆蓋。 如果電話號碼無效，將不會引發異常，並且電話號碼仍具有其原始值。

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

調用包含此聲明轉換的驗證技術設定檔的自斷言技術設定檔可以定義錯誤訊息。

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
  - **電話 ：**+49 （123） 456-7890
- 輸入參數：
  - **引發異常失敗**： 錯誤
  - **國家代碼類型**： ISO3166
- 輸出宣告：
  - **全國電話**： 1234567890
  - **國家代碼**： DE

### <a name="example-2"></a>範例 2

- 輸入宣告：
  - **電話 ：**+49 （123） 456-7890
- 輸入參數
  - **引發異常失敗**： 錯誤
  - **國家代碼類型**： 呼叫代碼
- 輸出宣告：
  - **全國電話**： 1234567890
  - **國家代碼**： +49
