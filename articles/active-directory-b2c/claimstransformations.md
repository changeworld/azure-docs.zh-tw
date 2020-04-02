---
title: ClaimsTransformations - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C 的識別體驗架構結構描述中 ClaimsTransformations 元素的定義。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2a919996d00f8ef3fa00109944b60d53b63d95ff
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529135"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ClaimsTransformations** 元素包含一份宣告轉換函式清單，可用於使用者旅程圖中以作為[自訂原則](custom-policy-overview.md)的一部分。 宣告轉換會將指定的宣告轉換成另一個。 在宣告轉換中，您會指定轉換方法，例如，將項目新增到字串集合，或變更字串的大小寫。

若要包含可在使用者旅程圖中使用的宣告轉換函式清單，必須在原則的 BuildingBlocks 區段下方宣告 ClaimsTransformations XML 元素。

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

**宣告轉換**元素包含以下屬性:

| 屬性 |必要 | 描述 |
| --------- |-------- | ----------- |
| Id |是 | 用來唯一識別宣告轉換的識別碼。 此識別碼會從原則中的其他 XML 元素參考。 |
| TransformationMethod | 是 | 要在宣告轉換中使用的轉換方法。 每個宣告轉換都有自己的值。 如需可用值的完整清單，請參閱[宣告轉換參考](#claims-transformations-reference)。 |

## <a name="claimstransformation"></a>ClaimsTransformation

**ClaimsTransformation** 元素包含下列元素：

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| 元素 | 發生次數 | 描述 |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | **InputClaim** 元素的清單，會指定要取得以作為宣告轉換輸入的宣告類型。 這些元素中的每一個都會參考已經定義於原則 ClaimsSchema 區段中的 ClaimType。 |
| InputParameters | 0:1 | **InputParameter** 元素的清單，可提供來作為宣告轉換的輸入。
| OutputClaims | 0:1 | **OutputClaim** 元素的清單，會指定要在叫用 ClaimsTransformation 之後產生的宣告類型。 這些元素中的每一個都會參考已經定義於 ClaimsSchema 區段中的 ClaimType。 |

### <a name="inputclaims"></a>InputClaims

**InputClaims** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 預期的輸入宣告類型。 |

#### <a name="inputclaim"></a>InputClaim

**InputClaim** 元素包含下列屬性：

| 屬性 |必要 | 描述 |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |是 | 對已經定義於原則 ClaimsSchema 區段中之 ClaimType 的參考。 |
| TransformationClaimType |是 | 要參考轉換宣告類型的識別碼。 每個宣告轉換都有自己的值。 如需可用值的完整清單，請參閱[宣告轉換參考](#claims-transformations-reference)。 |

### <a name="inputparameters"></a>InputParameters

**InputParameters** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | 預期的輸入參數。 |

#### <a name="inputparameter"></a>InputParameter

| 屬性 | 必要 |描述 |
| --------- | ----------- |----------- |
| Id | 是 | 識別碼，其為對宣告轉換方法之參數的參考。 每個宣告轉換方法都有自己的值。 如需可用值的完整清單，請參閱宣告轉換表。 |
| DataType | 是 | 參數的資料類型，例如字串、布林值、整數或日期時間，其會以自訂原則 XML 結構描述中的每個 DataType 列舉為依據。 此類型可用來正確地執行算術運算。 每個宣告轉換都有自己的值。 如需可用值的完整清單，請參閱[宣告轉換參考](#claims-transformations-reference)。 |
| 值 | 是 | 要逐字傳遞到轉換的值。 某些值是任意的，它們其中有一些是您從宣告轉換方法中選取的。 |

### <a name="outputclaims"></a>OutputClaims

**OutputClaims** 元素包含下列元素：

| 元素 | 發生次數 | 描述 |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 預期的輸出宣告類型。 |

#### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 元素包含下列屬性：

| 屬性 |必要 | 描述 |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | 是 | 對已經定義於原則 ClaimsSchema 區段中之 ClaimType 的參考。
| TransformationClaimType | 是 | 要參考轉換宣告類型的識別碼。 每個宣告轉換都有自己的值。 如需可用值的完整清單，請參閱[宣告轉換參考](#claims-transformations-reference)。 |

如果輸入宣告和輸出宣告的類型一樣 (字串或布林值)，則您可以使用相同的輸入宣告作為輸出宣告。 在此情況下，宣告轉換會使用輸出值來變更輸入宣告。

## <a name="example"></a>範例

例如，您可能會儲存使用者所接受之服務條款的最後一個版本。 當您更新服務條款時，可以要求使用者接受新版本。 在下列範例中，**HasTOSVersionChanged** 宣告轉換會比較 **TOSVersion** 宣告值與 **LastTOSAcceptedVersion** 宣告值，然後傳回布林值的 **TOSVersionChanged** 宣告。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>宣告轉換參考

如需宣告轉換的範例，請參閱下列參考頁面：

- [布林](boolean-transformations.md)
- [日期](date-transformations.md)
- [整數](integer-transformations.md)
- [JSON](json-transformations.md)
- [電話號碼](phone-number-claims-transformations.md)
- [一般](general-transformations.md)
- [社交帳戶](social-transformations.md)
- [字串](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

