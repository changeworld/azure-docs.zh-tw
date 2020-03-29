---
title: 自訂策略的一般聲明轉換示例
titleSuffix: Azure AD B2C
description: Azure 活動目錄 B2C 的標識體驗框架 （IEF） 架構的一般聲明轉換示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188540"
---
# <a name="general-claims-transformations"></a>一般宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure 活動目錄 B2C （Azure AD B2C） 中使用標識體驗框架架構模式的一般聲明轉換的示例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="copyclaim"></a>複製索賠

將聲明的值複製到另一個。 兩個聲明必須來自同一類型。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 字串，int | 要複製的聲明類型。 |
| OutputClaim | outputClaim | 字串，int | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

使用此聲明轉換將值從字串或數位聲明複製到另一個聲明。 下面的示例將外部電子郵件聲明值複製到電子郵件聲明。

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
    - **輸入索賠**：bob@contoso.com
- 輸出宣告：
    - **輸出索賠**：bob@contoso.com

## <a name="doesclaimexist"></a>DoesClaimExist

檢查 **inputClaim** 是否存在，並據以將 **outputClaim** 設定為 True 或 False。

| Item | TransformationClaimType | 資料類型 | 注意 |
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
  - **輸入索賠**：someone@contoso.com
- 輸出宣告：
  - **outputClaim**：true

## <a name="hash"></a>雜湊

使用 salt 和祕密，針對提供的純文字進行雜湊處理。 使用的散列演算法是 SHA-256。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | 字串 | 要加密的輸入宣告 |
| InputClaim | salt | 字串 | Salt 參數。 您可以使用 `CreateRandomString` 宣告轉換來建立隨機值。 |
| InputParameter | randomizerSecret | 字串 | 指向現有的 Azure AD B2C**策略鍵**。 要創建新的策略鍵：在 Azure AD B2C 租戶中，在 **"管理**"下，選擇**標識體驗框架**。 選擇**策略鍵**以查看租戶中可用的金鑰。 選取 [加入]****。 針對 [選項]****，選取 [手動]****。 提供名稱（首碼*B2C_1A_* 可能自動添加）。 在 **"機密**文本"框中，輸入要使用的任何機密，如 1234567890。 針對 [金鑰使用方法]**** 選取 [簽章]****。 選取 [建立]****。 |
| OutputClaim | 雜湊 | 字串 | 叫用此宣告轉換之後所產生的 ClaimType。 設定於 `plaintext` inputClaim 中的宣告。 |

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
  - **純文字**：MyPass@word1
  - **salt**：487624568
  - **randomizerSecret**：B2C_1A_AccountTransformSecret
- 輸出宣告：
  - **outputClaim**：CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =
