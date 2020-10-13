---
title: 在自訂原則中 Azure AD SSPR 技術設定檔
titleSuffix: Azure AD B2C
description: Azure AD B2C 中 Azure AD SSPR 技術設定檔的自訂原則參考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85383592"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂原則中定義 Azure AD SSPR 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 可支援驗證電子郵件地址，以進行自助式密碼重設 (SSPR) 。 使用 Azure AD SSPR 技術設定檔來產生和傳送程式碼至電子郵件地址，然後驗證程式代碼。 Azure AD 的 SSPR 技術設定檔也可能會傳回錯誤訊息。 在使用者旅程圖繼續進行之前，驗證技術設定檔會驗證使用者提供的資料。 使用驗證技術設定檔時，錯誤訊息會顯示在自我判斷頁面上。

此技術設定檔：

- 不提供與使用者互動的介面。 相反地，使用者介面會從 [自我](self-asserted-technical-profile.md) 判斷的技術設定檔，或 [顯示控制項](display-controls.md) 做為 [驗證技術設定檔](validation-technical-profile.md)來呼叫。
- 使用 Azure AD SSPR 服務來產生和傳送程式碼至電子郵件地址，然後驗證程式代碼。  
- 透過驗證碼驗證電子郵件地址。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式元件的完整名稱：

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

下列範例顯示 Azure AD SSPR 技術設定檔：

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>傳送電子郵件

此技術設定檔的第一個模式是產生並傳送程式碼。 您可以針對此模式設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送給 Azure AD SSPR 的宣告清單。 您也可以將您的宣告名稱對應至 SSPR 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- |
| emailAddress | 是 | 擁有電子郵件地址之使用者的識別碼。 輸入宣告的 `PartnerClaimType` 屬性必須設定為 `emailAddress` 。 |


**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些專案是用來修改輸入宣告或產生新的，然後再傳送給 Azure AD 的 SSPR 服務。

### <a name="output-claims"></a>輸出宣告

Azure AD SSPR 通訊協定提供者不會傳回任何 **OutputClaims**，因此不需要指定輸出宣告。 但是，只要您設定屬性，就可以包含 Azure AD SSPR 通訊協定提供者未傳回的宣告 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須是 **SendCode**。  |

#### <a name="ui-elements"></a>UI 元素

下列中繼資料可以用來設定傳送 SMS 失敗時所顯示的錯誤訊息。 中繼資料應該在 [自我](self-asserted-technical-profile.md) 判斷技術設定檔中設定。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-ad-sspr)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | 否 | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。 |
| UserMessageIfThrottled| 否 | 如果已對要求進行節流處理，則為使用者錯誤訊息。|


### <a name="example-send-an-email"></a>範例：傳送電子郵件

下列範例顯示用來透過電子郵件傳送程式碼的 Azure AD SSPR 技術設定檔。

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>檢查驗證碼

此技術設定檔的第二種模式是驗證程式代碼。 您可以針對此模式設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送給 Azure AD SSPR 的宣告清單。 您也可以將您的宣告名稱對應至 SSPR 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- | ----------- |
| emailAddress| 是 | 與先前用來傳送程式碼的電子郵件地址相同。 它也可用來尋找電子郵件驗證會話。 輸入宣告的 `PartnerClaimType` 屬性必須設定為 `emailAddress` 。|
| verificationCode  | 是 | 要驗證的使用者所提供的驗證碼。 輸入宣告的 `PartnerClaimType` 屬性必須設定為 `verificationCode` 。 |

**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些元素可用來修改輸入宣告或產生新的宣告，然後再呼叫 Azure AD 的 SSPR 服務。

### <a name="output-claims"></a>輸出宣告

Azure AD SSPR 通訊協定提供者不會傳回任何 **OutputClaims**，因此不需要指定輸出宣告。 但是，只要您設定屬性，就可以包含 Azure AD SSPR 通訊協定提供者未傳回的宣告 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須是 **VerifyCode** |

#### <a name="ui-elements"></a>UI 元素

下列中繼資料可以用來設定程式碼驗證失敗時所顯示的錯誤訊息。 中繼資料應該在 [自我](self-asserted-technical-profile.md) 判斷技術設定檔中設定。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-ad-sspr)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | 當程式碼驗證會話已過期時，要向使用者顯示的訊息。 可能是程式碼已過期，或從未針對指定的識別碼產生程式碼。|
|UserMessageIfInternalError | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。|
|UserMessageIfThrottled | 如果已對要求進行節流處理，則為使用者錯誤訊息。|
|UserMessageIfVerificationFailedNoRetry | 如果使用者提供了不正確程式碼，則會向使用者顯示的訊息，而且不允許使用者提供正確的程式碼。|
|UserMessageIfVerificationFailedRetryAllowed | 如果使用者提供了不正確程式碼，並允許使用者提供正確的程式碼，則會向使用者顯示的訊息。|

### <a name="example-verify-a-code"></a>範例：驗證程式代碼

下列範例顯示用來驗證程式代碼 Azure AD SSPR 技術設定檔。

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```