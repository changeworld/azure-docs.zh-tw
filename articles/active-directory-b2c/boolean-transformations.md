---
title: 布林聲明自訂策略的轉換示例
titleSuffix: Azure AD B2C
description: 布林聲明 Azure 活動目錄 B2C 的標識體驗框架 （IEF） 架構的轉換示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ea3b273070702144d5296d07cb8712da044819a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471960"
---
# <a name="boolean-claims-transformations"></a>布林值宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure 活動目錄 B2C（Azure AD B2C） 中使用標識體驗框架架構架構的布林聲明轉換的示例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="andclaims"></a>AndClaims

執行兩個布林值 inputClaim 的 And 運算，並使用運算的結果來設定 outputClaim。

| Item  | TransformationClaimType  | 資料類型  | 注意 |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | 要評估的第一個 ClaimType。 |
| InputClaim | inputClaim2  | boolean | 要評估的第二個 ClaimType。 |
|OutputClaim | outputClaim | boolean | 叫用此宣告轉換之後將產生的 ClaimType (True 或 False)。 |

下列宣告轉換示範如何 And 兩個布林值 ClaimType：`isEmailNotExist` 和 `isSocialAccount`。 如果這兩個輸入宣告的值均為 `true`，就會將輸出宣告 `presentEmailSelfAsserted` 設定為 `true`。 在協調流程步驟中，只有在社交帳戶電子郵件為空白時，才能使用先決條件來預設自我判斷頁面。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim1**：true
    - **inputClaim2**：false
- 輸出宣告：
    - **outputClaim**：false


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

檢查兩個宣告的布林值相等，如果不相等，則會擲回例外狀況。

| Item | TransformationClaimType  | 資料類型  | 注意 |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | 要進行判斷的 ClaimType。 |
| InputParameter |valueToCompareTo | boolean | 要比較的值 (True 或 False)。 |

**AssertBooleanClaimIsEqualToValue** 宣告轉換一律會從[驗證技術設定檔](validation-technical-profile.md)執行，其會透過[自我判斷技術設定檔](self-asserted-technical-profile.md)來呼叫。 **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** 自我判斷技術設定檔中繼資料會控制技術設定檔要呈現給使用者的錯誤訊息。 錯誤訊息可以[當地語系化](localization-string-ids.md#claims-transformations-error-messages)。

![AssertStringClaimsAreEqual 執行](./media/boolean-transformations/assert-execution.png)

下列宣告轉換示範如何使用 `true` 值來檢查布林值 ClaimType 的值。 如果 `accountEnabled` ClaimType 的值是 False，就會擲回錯誤訊息。

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


`login-NonInteractive` 驗證技術設定檔會呼叫 `AssertAccountEnabledIsTrue` 宣告轉換。
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自我判斷技術設定檔會呼叫驗證 **login-NonInteractive** 技術設定檔。

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：false
    - **valueToCompareTo**：true
- 結果：擲回錯誤

## <a name="comparebooleanclaimtovalue"></a>比較布林索賠值

檢查聲明的布林值等於`true`或`false`，並返回壓縮的結果。

| Item | TransformationClaimType  | 資料類型  | 注意 |
| ---- | ------------------------ | ---------- | ----- |
| InputClaim | inputClaim | boolean | 要進行判斷的 ClaimType。 |
| InputParameter |valueToCompareTo | boolean | 要比較的值 (True 或 False)。 |
| OutputClaim | 比較結果 | boolean | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |


下列宣告轉換示範如何使用 `true` 值來檢查布林值 ClaimType 的值。 如果`IsAgeOver21Years`聲明類型的值等於`true`，則聲明轉換將返回，否則`true``false`將返回 。

```XML
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
      <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：false
- 輸入參數：
    - **valueToCompareTo**：true
- 輸出宣告：
    - **比較結果**： 假



## <a name="notclaims"></a>NotClaims

執行布林值 inputClaim 的 Not 運算，並使用運算的結果來設定 outputClaim。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | 要運算的宣告。 |
| OutputClaim | outputClaim | boolean | 叫用此 ClaimsTransformation 之後所產生的 ClaimType (True 或 False)。 |

使用此宣告轉換來執行宣告上的邏輯否定。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：false
- 輸出宣告：
    - **outputClaim**：true

## <a name="orclaims"></a>OrClaims

執行兩個布林值 inputClaim 的 Or 運算，並使用運算的結果來設定 outputClaim。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | 要評估的第一個 ClaimType。 |
| InputClaim | inputClaim2 | boolean | 要評估的第二個 ClaimType。 |
| OutputClaim | outputClaim | boolean | 叫用此 ClaimsTransformation 之後將產生的 ClaimType (True 或 False)。 |

下列宣告轉換示範如何 `Or` 兩個布林值 ClaimType。 在協調流程步驟中，只有在其中一個宣告的值為 `true` 時，才能使用先決條件來預設自我判斷頁面。

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim1**：true
    - **inputClaim2**：false
- 輸出宣告：
    - **outputClaim**：true
