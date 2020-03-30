---
title: 自訂策略的整數聲明轉換示例
titleSuffix: Azure AD B2C
description: 對於 Azure 活動目錄 B2C 的標識體驗框架 （IEF） 架構，整數聲明轉換示例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ece25e95bbbe9f1a1fa591c29ea9ffda0fefd369
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187673"
---
# <a name="integer-claims-transformations"></a>整數宣告轉換

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文提供了在 Azure 活動目錄 B2C（Azure AD B2C） 中使用標識體驗框架架構架構的整數聲明轉換的示例。 如需詳細資訊，請參閱 [ClaimsTransformations](claimstransformations.md)。

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

將 Lng 資料類型轉換為字串資料類型。

| Item | TransformationClaimType | 資料類型 | 注意 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 要轉換為字串的 ClaimType。 |
| OutputClaim | outputClaim | 字串 | 叫用此 ClaimsTransformation 之後所產生的 ClaimType。 |

在此範例中，值類型為 Long 的 `numericUserId` 宣告會轉換為值類型為 String (字串) 的 `UserId` 宣告。

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>範例

- 輸入宣告：
    - **inputClaim**: 12334 (long)
- 輸出宣告：
    - **outputClaim**: "12334" (string)

