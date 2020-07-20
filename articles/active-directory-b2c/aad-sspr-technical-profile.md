---
title: Azure AD 自訂原則中的 SSPR 技術設定檔
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85383592"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂原則中定義 Azure AD SSPR 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）提供驗證自助式密碼重設（SSPR）之電子郵件地址的支援。 使用 Azure AD SSPR 技術設定檔來產生程式碼，並將其傳送至電子郵件地址，然後確認程式碼。 Azure AD SSPR 技術設定檔可能也會傳回錯誤訊息。 在使用者旅程圖繼續進行之前，驗證技術設定檔會驗證使用者提供的資料。 使用驗證技術設定檔時，會在自我判斷頁面上顯示錯誤訊息。

此技術設定檔：

- 不提供介面來與使用者互動。 相反地，使用者介面是從[自我](self-asserted-technical-profile.md)判斷技術設定檔中呼叫，或是以[顯示控制項](display-controls.md)做為[驗證技術設定檔](validation-technical-profile.md)。
- 會使用 Azure AD SSPR 服務來產生程式碼，並將其傳送至電子郵件地址，然後驗證程式代碼。  
- 透過驗證碼驗證電子郵件地址。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **Handler**屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式元件的完整名稱：

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

此技術設定檔的第一個模式是產生並傳送程式碼。 您可以在此模式中設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Azure AD SSPR 的宣告清單。 您也可以將宣告的名稱對應至 SSPR 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 說明 |
| --------- | -------- | ----------- |
| emailAddress | Yes | 擁有電子郵件地址之使用者的識別碼。 輸入宣告的 `PartnerClaimType` 屬性必須設定為 `emailAddress` 。 |


**InputClaimsTransformations**元素可能包含**InputClaimsTransformation**元素的集合，這些專案是用來修改輸入宣告或產生新的專案，然後再傳送至 Azure AD SSPR 服務。

### <a name="output-claims"></a>輸出宣告

Azure AD SSPR 通訊協定提供者不會傳回任何**OutputClaims**，因此不需要指定輸出宣告。 不過，只要您設定了屬性，就可以包含 Azure AD SSPR 通訊協定提供者未傳回的宣告 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| 操作 | Yes | 必須是**SendCode**。  |

#### <a name="ui-elements"></a>UI 元素

您可以使用下列中繼資料來設定傳送 SMS 失敗時所顯示的錯誤訊息。 應該在[自我](self-asserted-technical-profile.md)判斷技術設定檔中設定中繼資料。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-ad-sspr)。

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | No | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。 |
| UserMessageIfThrottled| No | 使用者錯誤訊息（如果要求已節流）。|


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

## <a name="verify-code"></a>驗證碼

此技術設定檔的第二個模式是驗證程式代碼。 您可以在此模式中設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Azure AD SSPR 的宣告清單。 您也可以將宣告的名稱對應至 SSPR 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 說明 |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Yes | 與先前用來傳送程式碼的電子郵件地址相同。 它也可用來尋找電子郵件驗證會話。 輸入宣告的 `PartnerClaimType` 屬性必須設定為 `emailAddress` 。|
| verificationCode  | Yes | 要驗證之使用者所提供的驗證碼。 輸入宣告的 `PartnerClaimType` 屬性必須設定為 `verificationCode` 。 |

**InputClaimsTransformations**元素可能包含**InputClaimsTransformation**元素的集合，這些專案是用來修改輸入宣告或產生新的，然後才呼叫 Azure AD SSPR 服務。

### <a name="output-claims"></a>輸出宣告

Azure AD SSPR 通訊協定提供者不會傳回任何**OutputClaims**，因此不需要指定輸出宣告。 不過，只要您設定了屬性，就可以包含 Azure AD SSPR 通訊協定提供者未傳回的宣告 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
| 操作 | Yes | 必須是**VerifyCode** |

#### <a name="ui-elements"></a>UI 元素

下列中繼資料可用來設定程式碼驗證失敗時所顯示的錯誤訊息。 應該在[自我](self-asserted-technical-profile.md)判斷技術設定檔中設定中繼資料。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-ad-sspr)。

| 屬性 | 必要 | 說明 |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | 如果程式碼驗證會話已過期，要向使用者顯示的訊息。 可能是程式碼已過期，或從未針對指定的識別碼產生代碼。|
|UserMessageIfInternalError | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。|
|UserMessageIfThrottled | 使用者錯誤訊息（如果要求已節流）。|
|UserMessageIfVerificationFailedNoRetry | 如果使用者提供了不正確程式碼，就會向使用者顯示訊息，而且不允許使用者提供正確的程式碼。|
|UserMessageIfVerificationFailedRetryAllowed | 當使用者提供了不正確程式碼，並允許使用者提供正確的程式碼時，要向使用者顯示的訊息。|

### <a name="example-verify-a-code"></a>範例：驗證程式代碼

下列範例顯示用來驗證程式代碼的 Azure AD SSPR 技術設定檔。

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