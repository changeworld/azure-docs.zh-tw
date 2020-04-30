---
title: 自訂原則的 StringCollection 宣告轉換範例
titleSuffix: Azure AD B2C
description: StringCollection Azure Active Directory B2C 的 Identity Experience Framework （IEF）架構的宣告轉換範例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/21/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cac7e6feb632456b63b97ead057f9ecaf49322ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729707"
---
# <a name="stringcollection-claims-transformations"></a>StringCollection 宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供在 Azure Active Directory B2C （Azure AD B2C）中使用 Identity Experience Framework 架構之字串集合宣告轉換的範例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="additemtostringcollection"></a>AddItemToStringCollection

將字串宣告加入至新的唯一值 stringCollection 宣告。

| 項目 | TransformationClaimType | 資料類型 | 備忘錄 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | 字串 | 要新增至輸出宣告的 ClaimType。 |
| InputClaim | collection | stringCollection | [選擇性] 如果指定，宣告轉換就會複製此集合中的項目，並將項目新增至輸出集合宣告的結尾。 |
| OutputClaim | collection | stringCollection | 叫用此宣告轉換之後所產生的 ClaimType，並使用輸入宣告中指定的值。 |

使用此宣告轉換來將字串新增至新的或現有的 stringCollection。 它通常用於 **AAD-UserWriteUsingAlternativeSecurityId** 技術設定檔。 建立新的社交帳戶之前，**CreateOtherMailsFromEmail** 宣告轉換會讀取 ClaimType，並將值新增至 **otherMails** ClaimType。

下列宣告轉換會將 **email** ClaimType 新增至 **otherMails** ClaimType。

```XML
<ClaimsTransformation Id="CreateOtherMailsFromEmail" TransformationMethod="AddItemToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **集合**： ["someone@outlook.com"]
  - **專案**： "admin@contoso.com"
- 輸出宣告：
  - **collection**： ["someone@outlook.com"，"admin@contoso.com"]

## <a name="addparametertostringcollection"></a>AddParameterToStringCollection

將字串參數新增至新的唯一值 stringCollection 宣告。

| 項目 | TransformationClaimType | 資料類型 | 備忘錄 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | [選擇性] 如果指定，宣告轉換就會複製此集合中的項目，並將項目新增至輸出集合宣告的結尾。 |
| InputParameter | item | 字串 | 要新增至輸出宣告的值。 |
| OutputClaim | collection | stringCollection | 叫用此宣告轉換之後所產生的 ClaimType，並含有輸入參數中指定的值。 |

使用此宣告轉換來將字串值新增至新的或現有的 stringCollection。 下列範例會將常數的電子郵件地址 (admin@contoso.com) 新增至 **otherMails** 宣告。

```XML
<ClaimsTransformation Id="SetCompanyEmail" TransformationMethod="AddParameterToStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="item" DataType="string" Value="admin@contoso.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **集合**： ["someone@outlook.com"]
- 輸入參數
  - **專案**： "admin@contoso.com"
- 輸出宣告：
  - **collection**： ["someone@outlook.com"，"admin@contoso.com"]

## <a name="getsingleitemfromstringcollection"></a>GetSingleItemFromStringCollection

從提供的字串集合中取得第一個項目。

| 項目 | TransformationClaimType | 資料類型 | 備忘錄 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 宣告轉換用來取得項目的 ClaimType。 |
| OutputClaim | extractedItem | 字串 | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 集合中的第一個項目。 |

下列範例會讀取 **otherMails** 宣告，並將第一個項目傳回到 **email** 宣告。

```XML
<ClaimsTransformation Id="CreateEmailFromOtherMails" TransformationMethod="GetSingleItemFromStringCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="otherMails" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedItem" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **collection**： ["someone@outlook.com"，"someone@contoso.com"]
- 輸出宣告：
  - **extractedItem**： "someone@outlook.com"


## <a name="stringcollectioncontains"></a>StringCollectionContains

檢查 StringCollection 宣告類型是否包含元素

| 項目 | TransformationClaimType | 資料類型 | 備忘錄 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | 要搜尋的宣告類型。 |
|InputParameter|item|字串|要搜尋的值。|
|InputParameter|ignoreCase|字串|指定這個比較是否應忽略要比較之字串的大小寫。|
| OutputClaim | outputClaim | boolean | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 如果集合包含這類字串，則為布林值指標 |

下列範例會檢查`roles` stringCollection 宣告類型是否包含**admin**的值。

```XML
<ClaimsTransformation Id="IsAdmin" TransformationMethod="StringCollectionContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="item" DataType="string" Value="Admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

- 輸入宣告：
    - **inputClaim**： ["reader"，"author"，"admin"]
- 輸入參數：
    - **專案**： "Admin"
    - **ignoreCase**： "true"
- 輸出宣告：
    - **outputClaim**： "true"

## <a name="stringcollectioncontainsclaim"></a>StringCollectionContainsClaim

檢查 StringCollection 宣告類型是否包含宣告值。

| 項目 | TransformationClaimType | 資料類型 | 備忘錄 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | collection | stringCollection | 要搜尋的宣告類型。 |
| InputClaim | item|字串| 包含要搜尋之值的宣告類型。|
|InputParameter|ignoreCase|字串|指定這個比較是否應忽略要比較之字串的大小寫。|
| OutputClaim | outputClaim | boolean | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 如果集合包含這類字串，則為布林值指標 |

下列範例會檢查`roles` stringCollection 宣告類型是否包含宣告類型的值`role` 。

```XML
<ClaimsTransformation Id="HasRequiredRole" TransformationMethod="StringCollectionContainsClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="collection" />
    <InputClaim ClaimTypeReferenceId="role" TransformationClaimType="item" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hasAccess" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

- 輸入宣告：
    - **集合**： ["reader"，"author"，"admin"]
    - **專案**： "Admin"
- 輸入參數：
    - **ignoreCase**： "true"
- 輸出宣告：
    - **outputClaim**： "true"
