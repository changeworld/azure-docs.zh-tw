---
title: 自訂原則的一般宣告轉換範例
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C 的 Identity Experience Framework （IEF）架構的一般宣告轉換範例。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d9730168764f0ba683a246f9ac224c13d3bf31
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982801"
---
# <a name="general-claims-transformations"></a>一般宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供在 Azure Active Directory B2C （Azure AD B2C）中使用 Identity Experience Framework 架構之一般宣告轉換的範例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="copyclaim"></a>CopyClaim

將宣告的值複製到另一個。 這兩個宣告必須來自相同的類型。

| 項目 | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | string、int | 要複製的宣告類型。 |
| OutputClaim | outputClaim | string、int | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

使用此宣告轉換，將字串或數值宣告中的值複製到另一個宣告。 下列範例會將 externalEmail 宣告值複製到電子郵件宣告。

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**：bob@contoso.com
- 輸出宣告：
    - **outputClaim**：bob@contoso.com 

## <a name="doesclaimexist"></a>DoesClaimExist

檢查 **inputClaim** 是否存在，並據以將 **outputClaim** 設定為 True 或 False。

| 項目 | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |任意 | 必須驗證其存在的輸入宣告。 |
| OutputClaim | outputClaim | boolean | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

使用此宣告轉換來檢查某個宣告是否存在或包含任何值。 傳回值是布林值，會指出宣告是否存在。 下列範例會檢查電子郵件地址是否存在。

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **inputClaim**：someone@contoso.com
- 輸出宣告：
  - **outputClaim**：true

## <a name="hash"></a>雜湊

使用 salt 和祕密，針對提供的純文字進行雜湊處理。 使用的雜湊演算法是 SHA-256。

| 項目 | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | 要加密的輸入宣告 |
| InputClaim | salt | string | Salt 參數。 您可以使用 `CreateRandomString` 宣告轉換來建立隨機值。 |
| InputParameter | randomizerSecret | string | 指向現有的 Azure AD B2C**原則金鑰**。 若要建立新的原則金鑰：在您的 Azure AD B2C 租使用者的 [**管理**] 底下，選取 [ **Identity Experience Framework**]。 選取 [原則] [**金鑰**] 以查看您的租使用者中可用的金鑰。 選取 [新增]。 針對 [選項]，選取 [手動]。 提供名稱（可能會自動新增*B2C_1A_* 前置詞）。 在 [**密碼**] 文字方塊中，輸入您想要使用的任何密碼，例如1234567890。 針對 [金鑰使用方法] 選取 [簽章]。 選取 [建立]。 |
| OutputClaim | 雜湊 | string | 叫用此宣告轉換之後所產生的 ClaimType。 設定於 `plaintext` inputClaim 中的宣告。 |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
  - **plaintext**：MyPass@word1
  - **salt**：487624568
  - **randomizerSecret**：B2C_1A_AccountTransformSecret
- 輸出宣告：
  - **outputClaim**：CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =
