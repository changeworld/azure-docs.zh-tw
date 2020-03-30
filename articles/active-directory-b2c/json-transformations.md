---
title: 用於自訂策略的 JSON 聲明轉換示例
titleSuffix: Azure AD B2C
description: JSON 聲明 Azure 活動目錄 B2C 的標識體驗框架 （IEF） 架構的轉換示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad8fcf578ae1c89856a9d7929af0aec813cb4082
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187588"
---
# <a name="json-claims-transformations"></a>JSON 宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure 活動目錄 B2C（Azure AD B2C） 中使用標識體驗框架架構架構的 JSON 聲明轉換的示例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="generatejson"></a>生成 Json

使用聲明值或常量生成 JSON 字串。 點標記法之後的路徑字串用於指示將資料插入到 JSON 字串的位置。 按點拆分後，任何整數都被解釋為 JSON 陣列的索引，非整數被解釋為 JSON 物件的索引。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 點標記法之後的任何字串 | 字串 | 將聲明值插入到 JSON 的 JsonPath。 |
| InputParameter | 點標記法之後的任何字串 | 字串 | 將不斷值字串值插入到 JSON 的 JsonPath。 |
| OutputClaim | outputClaim | 字串 | 生成的 JSON 字串。 |

下面的示例基於"電子郵件"和"otp"以及常量字串的聲明值生成 JSON 字串。

```XML
<ClaimsTransformation Id="GenerateRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-4c56ffb40fa648b1aa6822283df94f60"/>
    <InputParameter Id="from.email" DataType="string" Value="service@contoso.com"/>
    <InputParameter Id="personalizations.0.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="requestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

以下聲明轉換輸出 JSON 字串聲明，該聲明將成為發送到 SendGrid（協力廠商電子郵件供應商）的請求的主體。 JSON 物件的結構由輸入參數的點符號中的指示和輸入聲明的轉換聲明類型定義。 點標記法中的數位表示陣列。 這些值來自輸入要求的值和輸入參數的"值"屬性。

- 輸入聲明 ：
  - **電子郵件**， 轉換聲明類型**個人化.0.到.0.電子郵件**：" "someone@example.com
  - **otp**， 轉換聲明類型**個人化.0.dynamic_template_data.otp** "346349"
- 輸入參數：
  - **template_id**："d-4c56ffb40fa648b1aa682283df94f60"
  - **從.email**："service@contoso.com"
  - **個人化.0.主題**"Contoso 帳戶電子郵件驗證碼"
- 輸出宣告：
  - **請求正文**： JSON 值

```JSON
{
  "personalizations": [
    {
      "to": [
        {
          "email": "someone@example.com"
        }
      ],
      "dynamic_template_data": {
        "otp": "346349",
        "verify-email" : "someone@example.com"
      },
      "subject": "Contoso account email verification code"
    }
  ],
  "template_id": "d-989077fbba9746e89f3f6411f596fb96",
  "from": {
    "email": "service@contoso.com"
  }
}
```

## <a name="getclaimfromjson"></a>GetClaimFromJson

從 JSON 資料中取得指定的元素。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 字串 | 宣告轉換用來取得項目的 ClaimType。 |
| InputParameter | claimToExtract | 字串 | 要擷取的 JSON 元素名稱。 |
| OutputClaim | extractedClaim | 字串 | 叫用此宣告轉換之後所產生的 ClaimType，元素值指定於 _claimToExtract_ 輸入參數中。 |

在下列範例中，宣告轉換會從 JSON 資料擷取 `emailAddress` 元素：`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputJson**：{"emailAddress": "someone@example.com", "displayName": "Someone"}
- 輸入參數：
    - **claimToExtract**：emailAddress
- 輸出宣告：
  - **提取索賠**：someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

從 JSON 資料中取得指定元素的清單。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | 字串 | 宣告轉換用來取得宣告的 ClaimType。 |
| InputParameter | errorOnMissingClaims | boolean | 指定如果遺漏其中一個宣告，是否要擲回錯誤。 |
| InputParameter | includeEmptyClaims | 字串 | 指定是否要包含空的宣告。 |
| InputParameter | jsonSourceKeyName | 字串 | 元素索引鍵名稱 |
| InputParameter | jsonSourceValueName | 字串 | 元素值名稱 |
| OutputClaim | 集合 | string、int、boolean 及 datetime |要擷取的宣告清單。 宣告的名稱應該等於 _jsonSourceClaim_ 輸入宣告中所指定的宣告名稱。 |

在下列範例中，宣告轉換會從 JSON 資料中擷取下列宣告：email (string)、displayName (string)、membershipNum (int)、active (boolean) 及 birthdate (datetime)。

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```

- 輸入宣告：
  - **jsonSourceClaim**：[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- 輸入參數：
    - **errorOnMissingClaims**：false
    - **includeEmptyClaims**：false
    - **jsonSourceKeyName**：key
    - **jsonSourceValueName**：value
- 輸出宣告：
  - **電子郵件**："someone@example.com"
  - **displayName**："Someone"
  - **membershipNum**：6353399
  - **active**：true
  - **birthdate** 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

從 JSON 資料中取得指定的數值 (long) 元素。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | 字串 | 宣告轉換用來取得宣告的 ClaimType。 |
| InputParameter | claimToExtract | 字串 | 要擷取的 JSON 元素名稱。 |
| OutputClaim | extractedClaim | long | 叫用此 ClaimsTransformation 之後所產生的 ClaimType，元素的值指定於 _claimToExtract_ 輸入參數中。 |

在下列範例中，宣告轉換會從 JSON 資料中擷取 `id` 元素。

```JSON
{
    "emailAddress": "someone@example.com",
    "displayName": "Someone",
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputJson**：{"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- 輸入參數
    - **claimToExtract**：id
- 輸出宣告：
    - **extractedClaim**：6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

從 JSON 資料陣列中取得第一個元素。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | 字串 | 宣告轉換用來從 JSON 陣列中取得項目的 ClaimType。 |
| OutputClaim | extractedClaim | 字串 | 叫用此 ClaimsTransformation 之後所產生的 ClaimType，JSON 陣列中的第一個元素。 |

在下列範例中，宣告轉換會從 JSON 陣列 `["someone@example.com", "Someone", 6353399]` 中擷取第一個元素 (電子郵件地址)。

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **輸入JonClaim：**["，"someone@example.com某人"，6353399]
- 輸出宣告：
  - **提取索賠**：someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

將 XML 資料轉換為 JSON 格式。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | 字串 | 宣告轉換用來將資料從 XML 轉換為 JSON 格式的 ClaimType。 |
| OutputClaim | json | 字串 | 叫用此 ClaimsTransformation 之後所產生的 ClaimType，JSON 格式的資料。 |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

在下列範例中，宣告轉換會將下列 XML 資料轉換為 JSON 格式。

#### <a name="example"></a>範例
輸入宣告：

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

輸出宣告：

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```
