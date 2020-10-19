---
title: 啟用一次性密碼 (OTP) 驗證
titleSuffix: Azure AD B2C
description: 瞭解如何使用 Azure AD B2C 自訂原則來設定一次性密碼 (OTP) 案例。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6978afc802bddd536c56fcb4e06a40ccc58867fe
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172670"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂原則中定義一次性密碼技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 提供管理單次密碼之產生和驗證的支援。 使用技術設定檔來產生程式碼，然後稍後再確認該程式碼。

單次密碼技術設定檔也會在程式碼驗證期間傳回錯誤訊息。 使用 **驗證技術設定檔**，設計與一次性密碼的整合。 驗證技術設定檔會呼叫一次性密碼技術設定檔來驗證程式代碼。 在使用者旅程圖繼續進行之前，驗證技術設定檔會驗證使用者提供的資料。 使用驗證技術設定檔時，錯誤訊息會顯示在自我判斷頁面上。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式元件的完整名稱：

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

下列範例會顯示一次性密碼技術設定檔：

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>產生程式碼

此技術設定檔的第一種模式是產生程式碼。 以下是可在此模式中設定的選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含一份要傳送給單次密碼通訊協定提供者所需的宣告清單。 您也可以將您的宣告名稱對應至以下定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- |
| 識別碼 (identifier) | 是 | 識別稍後需要驗證程式代碼之使用者的識別碼。 它通常用來作為程式碼傳遞目的地的識別碼，例如電子郵件地址或電話號碼。 |

**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些專案可用來修改輸入宣告或產生新的輸入宣告，再傳送給單次密碼通訊協定提供者。

### <a name="output-claims"></a>輸出宣告

**OutputClaims**元素包含一次性密碼通訊協定提供者所產生的宣告清單。 您也可以將您的宣告名稱對應至以下定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- |
| otpGenerated | 是 | 由 Azure AD B2C 管理其會話的已產生程式碼。 |

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

您可以使用下列設定來設定程式碼產生模式：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | 否 | 程式碼到期前的時間（以秒為單位）。 最小值： `60` ;最大值： `1200` ;預設值： `600` 。 每次提供程式碼 (使用相同的程式碼或) 新的程式碼時 `ReuseSameCode` ，就會延長程式碼到期時間。  |
| CodeLength | 否 | 程式碼的長度。 預設值為 `6`。 |
| CharacterSet | 否 | 針對正則運算式中使用的程式碼字元集。 例如，`a-z0-9A-Z`。 預設值為 `0-9`。 字元集必須在指定的集合中包含至少10個不同的字元。 |
| NumRetryAttempts | 否 | 在程式碼被視為無效之前的驗證嘗試次數。 預設值為 `5`。 |
| NumCodeGenerationAttempts | 否 | 每個識別碼的程式碼產生嘗試次數上限。 如果未指定，預設值為10。 |
| 作業 | 是 | 要執行的作業。 可能的值： `GenerateCode` 。 |
| ReuseSameCode | 否 | 是否應該提供相同的程式碼，而不是在指定的程式碼尚未過期時產生新的程式碼，而且仍然有效。 預設值為 `false`。  |



### <a name="example"></a>範例

下列範例 `TechnicalProfile` 會用來產生程式碼：

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
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

## <a name="verify-code"></a>檢查驗證碼

此技術設定檔的第二種模式是驗證程式代碼。 以下是可在此模式中設定的選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含一份要傳送給單次密碼通訊協定提供者所需的宣告清單。 您也可以將您的宣告名稱對應至以下定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- |
| 識別碼 (identifier) | 是 | 識別先前產生程式碼之使用者的識別碼。 它通常用來作為程式碼傳遞目的地的識別碼，例如電子郵件地址或電話號碼。 |
| otpToVerify | 是 | 使用者所提供的驗證碼。 |

**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些專案可用來修改輸入宣告或產生新的輸入宣告，再傳送給單次密碼通訊協定提供者。

### <a name="output-claims"></a>輸出宣告

此通訊協定提供者的程式碼驗證期間未提供任何輸出宣告。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

下列設定可用於程式碼驗證模式：

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 要執行的作業。 可能的值： `VerifyCode` 。 |


### <a name="ui-elements"></a>UI 元素

下列中繼資料可以用來設定程式碼驗證失敗時所顯示的錯誤訊息。 中繼資料應該在 [自我](self-asserted-technical-profile.md) 判斷技術設定檔中設定。 錯誤訊息可以[當地語系化](localization-string-ids.md#one-time-password-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | 否 | 當程式碼驗證會話已過期時，要向使用者顯示的訊息。 這是程式碼已過期，或從未針對指定的識別碼產生程式碼。 |
| UserMessageIfMaxRetryAttempted | 否 | 當使用者超過允許的驗證嘗試次數上限時，要向使用者顯示的訊息。 |
| UserMessageIfMaxNumberOfCodeGenerated | 否 | 當程式碼產生超過允許的嘗試次數上限時，要向使用者顯示的訊息。 |
| UserMessageIfInvalidCode | 否 | 如果使用者提供了不正確程式碼，要向使用者顯示的訊息。 |
| UserMessageIfVerificationFailedRetryAllowed | 否 | 如果使用者提供了不正確程式碼，則會向使用者顯示的訊息，並允許使用者提供正確的程式碼。  |
|UserMessageIfSessionConflict|否| 如果無法驗證程式代碼，要向使用者顯示的訊息。|

### <a name="example"></a>範例

下列範例 `TechnicalProfile` 是用來驗證程式代碼：

```xml
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

請參閱下列文章，以取得使用單次密碼技術設定檔與自訂電子郵件驗證的範例：

- Azure Active Directory B2C ([Mailjet](custom-email-mailjet.md)、 [SendGrid](custom-email-sendgrid.md)) 的自訂電子郵件驗證

