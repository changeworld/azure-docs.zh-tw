---
title: 自訂原則的日期宣告轉換範例
description: Azure Active Directory B2C 的 Identity Experience Framework （IEF）架構的日期宣告轉換範例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eaf58b964517162ee7f7eb925e1e64830eedc087
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85202546"
---
# <a name="date-claims-transformations"></a>日期宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供在 Azure Active Directory B2C （Azure AD B2C）中使用 Identity Experience Framework 架構的日期宣告轉換的範例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

確認某個日期和時間宣告 (字串資料類型) 是否晚於第二個日期和時間宣告 (字串資料類型)，並擲回例外狀況。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | leftOperand | 字串 | 第一個宣告的類型，應該晚於第二個宣告。 |
| InputClaim | rightOperand | 字串 | 第二個宣告的類型，應該早於第一個宣告。 |
| InputParameter | AssertIfEqualTo | boolean | 指定當左運算元等於右運算元時，是否應該傳遞這個判斷提示。 |
| InputParameter | AssertIfRightOperandIsNotPresent | boolean | 指定當右運算元遺失時，是否應該傳遞這個判斷提示。 |
| InputParameter | TreatAsEqualIfWithinMillseconds | int | 指定若要將兩個日期時間視為相等，彼此之間所允許相隔的毫秒數 (例如，為了考慮時鐘誤差因素)。 |

**AssertDateTimeIsGreaterThan** 宣告轉換一律會從[驗證技術設定檔](validation-technical-profile.md)執行，其會透過[自我判斷技術設定檔](self-asserted-technical-profile.md)來呼叫。 **DateTimeGreaterThan** 自我判斷技術設定檔中繼資料會控制技術設定檔要呈現給使用者的錯誤訊息。 錯誤訊息可以[當地語系化](localization-string-ids.md#claims-transformations-error-messages)。

![AssertStringClaimsAreEqual 執行](./media/date-transformations/assert-execution.png)

下列範例會比較 `currentDateTime` 宣告與 `approvedDateTime` 宣告。 如果 `currentDateTime` 晚於 `approvedDateTime`，則會擲回錯誤。 兩個值若相差不到 5 分鐘 (30000 毫秒)，轉換會將兩者視為相等。

```xml
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

`login-NonInteractive` 驗證技術設定檔會呼叫 `AssertApprovedDateTimeLaterThanCurrentDateTime` 宣告轉換。
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

自我判斷技術設定檔會呼叫驗證 **login-NonInteractive** 技術設定檔。

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **leftOperand**： 2020-03-01T15：00： 00.0000000 z
    - **rightOperand**： 2020-03-01T14：00： 00.0000000 z
- 結果：擲回錯誤

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

將**日期** ClaimType 轉換為**日期時間** ClaimType。 宣告轉換會轉換時間格式，並對日期新增 12:00:00 AM。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | date | 要轉換的 ClaimType。 |
| OutputClaim | outputClaim | dateTime | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

下列範例會示範如何將宣告 `dateOfBirth` (日期資料類型) 轉換為另一個宣告 `dateOfBirthWithTime` (日期時間資料類型)。

```xml
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：2020-15-03
- 輸出宣告：
    - **outputClaim**： 2020-15-03T00：00： 00.0000000 z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

將日期**時間**claimtype 轉換成**日期**ClaimType。 宣告轉換會從日期移除時間格式。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | dateTime | 要轉換的 ClaimType。 |
| OutputClaim | outputClaim | date | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

下列範例示範如何將宣告 `systemDateTime` （dateTime 資料類型）轉換為另一個宣告 `systemDate` （date 資料類型）。

```xml
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputClaim**： 2020-15-03T11：34： 22.0000000 z
- 輸出宣告：
  - **outputClaim**：2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

取得目前的 UTC 日期和時間，並將值新增至 ClaimType。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | currentDateTime | dateTime | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

```xml
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

* 輸出宣告：
    * **currentDateTime**： 2020-15-03T11：40： 35.0000000 z

## <a name="datetimecomparison"></a>DateTimeComparison

判斷某個日期時間是晚於、早於還是等於另一個日期時間。 其結果是新的布林值 ClaimType，且其值為 `true` 或 `false`。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | dateTime | 第一個 dateTime，比較早於或晚於第二個 dateTime。 Null 值會擲回例外狀況。 |
| InputClaim | secondDateTime | dateTime | 第二個 dateTime，用來比較早於或晚於第一個 dateTime。 Null 值會被視為目前的 datetTime。 |
| InputParameter | ! 運算子之後 | 字串 | 下列值之一：相同、晚於或早於。 |
| InputParameter | timeSpanInSeconds | int | 將時間範圍新增至第一個日期時間。 |
| OutputClaim | result | boolean | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

使用此宣告轉換來判斷兩個 ClaimTypes 之間是等於、晚於還是早於。 例如，您可能會儲存上一次使用者接受服務條款 (TOS) 的時間。 3 個月後，您便可要求使用者再次存取 TOS。
若要執行宣告轉換，您必須先取得目前的日期時間，以及使用者上一次接受 TOS 的時間。

```xml
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **firstDateTime**： 2020-01-01T00：00： 00.100000 z
    - **secondDateTime**： 2020-04-01T00：00： 00.100000 z
- 輸入參數：
    - **運算子**：晚於
    - **timeSpanInSeconds**：7776000 (90 天)
- 輸出宣告：
    - **結果**：true
