---
title: 啟用一次性密碼 （OTP） 驗證
titleSuffix: Azure AD B2C
description: 瞭解如何使用 Azure AD B2C 自訂策略設置一次性密碼 （OTP） 方案。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bd5fed45332c73c633db1137bdc23aea66fd3403
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332771"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂策略中定義一次性密碼技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C（Azure AD B2C）支援管理一次性密碼的生成和驗證。 使用技術設定檔生成代碼，然後稍後驗證該代碼。

一次性密碼技術設定檔還可以在代碼驗證期間返回錯誤訊息。 使用**驗證技術設定檔**設計與一次性密碼的集成。 驗證技術設定檔調用一次性密碼技術設定檔以驗證代碼。 在使用者旅程圖繼續進行之前，驗證技術設定檔會驗證使用者提供的資料。 使用驗證技術設定檔時，自斷言頁上將顯示一條錯誤訊息。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 使用的協定處理常式程式集的完全限定名稱：

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

下面的示例顯示了一次性密碼技術設定檔：

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>產生程式碼

此技術設定檔的第一種模式是生成代碼。 以下是可為此模式配置的選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含發送到一次性密碼協定提供程式所需的聲明清單。 您還可以將聲明的名稱映射到下面定義的名稱。

| 索賠參考Id | 必要 | 描述 |
| --------- | -------- | ----------- |
| 識別碼 (identifier) | 是 | 標識以後需要驗證代碼的使用者的識別碼。 它通常用作代碼傳遞到的目標的識別碼，例如電子郵件地址或電話號碼。 |

**InputClaims轉換**元素可能包含**一個輸入聲明轉換**元素的集合，這些元素用於修改輸入聲明或在發送到一次性密碼協定提供程式之前生成新聲明。

### <a name="output-claims"></a>輸出宣告

**OutputClaims**元素包含由一次性密碼協定提供程式生成的聲明清單。 您還可以將聲明的名稱映射到下面定義的名稱。

| 索賠參考Id | 必要 | 描述 |
| --------- | -------- | ----------- |
| 奧特普生成 | 是 | 其會話由 Azure AD B2C 管理的生成的代碼。 |

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

以下設置可用於配置代碼生成模式：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 代碼過期秒數 | 否 | 代碼過期前以秒為單位。 最小： `60`;最大值： `1200`;預設值： `600`. |
| 代碼長度 | 否 | 代碼的長度。 預設值是 `6`。 |
| CharacterSet | 否 | 代碼的字元集，格式化為在正則運算式中使用。 例如： `a-z0-9A-Z` 。 預設值是 `0-9`。 字元集必須在指定的集中至少包含 10 個不同的字元。 |
| 數位重試次數 | 否 | 在代碼被視為無效之前的驗證嘗試次數。 預設值是 `5`。 |
| 作業 | 是 | 要執行的作業。 可能的值： `GenerateCode`. |
| 重複使用同一代碼 | 否 | 是否應提供重複代碼，而不是在給定代碼尚未過期且仍然有效時生成新代碼。 預設值是 `false`。 |

### <a name="example"></a>範例

以下示例`TechnicalProfile`用於生成代碼：

```XML
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>驗證碼

此技術設定檔的第二種模式是驗證代碼。 以下是可為此模式配置的選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含發送到一次性密碼協定提供程式所需的聲明清單。 您還可以將聲明的名稱映射到下面定義的名稱。

| 索賠參考Id | 必要 | 描述 |
| --------- | -------- | ----------- |
| 識別碼 (identifier) | 是 | 標識以前生成代碼的使用者的識別碼。 它通常用作代碼傳遞到的目標的識別碼，例如電子郵件地址或電話號碼。 |
| 奧特托驗證 | 是 | 使用者提供的驗證碼。 |

**InputClaims轉換**元素可能包含**一個輸入聲明轉換**元素的集合，這些元素用於修改輸入聲明或在發送到一次性密碼協定提供程式之前生成新聲明。

### <a name="output-claims"></a>輸出宣告

在驗證此協定提供程式期間，沒有提供輸出聲明。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

以下設置可用於代碼驗證模式：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 要執行的作業。 可能的值： `VerifyCode`. |


### <a name="ui-elements"></a>UI 元素

以下中繼資料可用於配置代碼驗證失敗時顯示的錯誤訊息。 中繼資料應在[自斷言](self-asserted-technical-profile.md)的技術設定檔中配置。 錯誤訊息可以[當地語系化](localization-string-ids.md#one-time-password-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 使用者消息如果會話不存在 | 否 | 如果代碼驗證會話已過期，則向使用者顯示的消息。 要麼是代碼已過期，要麼從未為給定識別碼生成代碼。 |
| 使用者留言如果最大重試嘗試 | 否 | 如果使用者超過允許的最大驗證嘗試，則要向使用者顯示的消息。 |
| 使用者消息如果無效代碼 | 否 | 如果使用者提供了無效代碼，則要向使用者顯示的消息。 |
|使用者消息如果會話衝突|否| 如果無法驗證代碼，要向使用者顯示的消息。|

### <a name="example"></a>範例

以下示例`TechnicalProfile`用於驗證代碼：

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>後續步驟

有關將一次性密碼技術設定檔與自訂電子郵件驗證一起使用的示例，請參閱以下文章：

- [Azure 活動目錄 B2C 中的自訂電子郵件驗證](custom-email.md)

