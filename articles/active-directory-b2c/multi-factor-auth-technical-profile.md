---
title: Azure MFA 自訂原則中的技術設定檔
titleSuffix: Azure AD B2C
description: 適用于 Azure Multi-Factor Authentication (MFA 的自訂原則參考) Azure AD B2C 中的技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 71040f831ed7a64f2bc7be7f3a75218976fc2559
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385938"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂原則中定義 Azure MFA 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 使用 Azure Multi-Factor Authentication (MFA) 提供驗證電話號碼的支援。 您可以使用此技術設定檔來產生程式碼，並將其傳送至電話號碼，然後驗證程式代碼。 Azure MFA 技術設定檔也可能會傳回錯誤訊息。  在使用者旅程圖繼續進行之前，驗證技術設定檔會驗證使用者提供的資料。 使用驗證技術設定檔時，錯誤訊息會顯示在自我判斷頁面上。

此技術設定檔：

- 不提供與使用者互動的介面。 相反地，使用者介面會從 [自我](self-asserted-technical-profile.md) 判斷的技術設定檔，或 [顯示控制項](display-controls.md) 做為 [驗證技術設定檔](validation-technical-profile.md)來呼叫。
- 使用 Azure MFA 服務來產生程式碼，並將其傳送至電話號碼，然後驗證程式代碼。  
- 透過文字訊息驗證電話號碼。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式元件的完整名稱：

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

下列範例顯示 Azure MFA 技術設定檔：

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>傳送 SMS

此技術設定檔的第一個模式是產生並傳送程式碼。 您可以針對此模式設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Azure MFA 的宣告清單。 您也可以將您的宣告名稱對應至 MFA 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- |
| userPrincipalName | 是 | 擁有電話號碼之使用者的識別碼。 |
| phoneNumber | 是 | 要傳送 SMS 代碼的電話號碼。 |
| companyName | 否 |SMS 中的公司名稱。 如果未提供，則會使用您的應用程式名稱。 |
| locale | 否 | SMS 的地區設定。 如果未提供，則會使用使用者的瀏覽器地區設定。 |

**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些元素可用來修改輸入宣告或產生新的宣告，然後再傳送至 Azure MFA 服務。

### <a name="output-claims"></a>輸出宣告

Azure MFA 通訊協定提供者不會傳回任何 **OutputClaims**，因此不需要指定輸出宣告。 但是，只要您設定屬性，就可以包含 Azure MFA 識別提供者未傳回的宣告 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須是 **OneWaySMS**。  |

#### <a name="ui-elements"></a>UI 元素

下列中繼資料可以用來設定傳送 SMS 失敗時所顯示的錯誤訊息。 中繼資料應該在 [自我](self-asserted-technical-profile.md) 判斷技術設定檔中設定。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | 否 | 如果提供的電話號碼不接受 SMS，則會顯示使用者錯誤訊息。 |
| UserMessageIfInvalidFormat | 否 | 如果提供的電話號碼不是有效的電話號碼，則為使用者錯誤訊息。 |
| UserMessageIfServerError | 否 | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。 |
| UserMessageIfThrottled| 否 | 如果已對要求進行節流處理，則為使用者錯誤訊息。|

### <a name="example-send-an-sms"></a>範例：傳送 SMS

下列範例顯示用來透過 SMS 傳送程式碼的 Azure MFA 技術設定檔。

```xml
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>檢查驗證碼

此技術設定檔的第二種模式是驗證程式代碼。 您可以針對此模式設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Azure MFA 的宣告清單。 您也可以將您的宣告名稱對應至 MFA 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| 是 | 與先前用來傳送程式碼的電話號碼相同。 它也可用來尋找電話驗證會話。 |
| verificationCode  | 是 | 要驗證的使用者所提供的驗證碼 |

**InputClaimsTransformations**元素可以包含**InputClaimsTransformation**專案的集合，這些元素可用來修改輸入宣告或產生新的宣告，然後再呼叫 Azure MFA 服務。

### <a name="output-claims"></a>輸出宣告

Azure MFA 通訊協定提供者不會傳回任何 **OutputClaims**，因此不需要指定輸出宣告。 但是，只要您設定屬性，就可以包含 Azure MFA 識別提供者未傳回的宣告 `DefaultValue` 。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須 **驗證** |

#### <a name="ui-elements"></a>UI 元素

下列中繼資料可以用來設定程式碼驗證失敗時所顯示的錯誤訊息。 中繼資料應該在 [自我](self-asserted-technical-profile.md) 判斷技術設定檔中設定。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| 否 | 當使用者嘗試驗證碼太多次時，使用者的錯誤訊息。 |
| UserMessageIfServerError | 否 | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。 |
| UserMessageIfThrottled| 否 | 如果要求已節流，則為使用者錯誤訊息。|
| UserMessageIfWrongCodeEntered| 否| 如果輸入要驗證的程式碼錯誤，則為使用者錯誤訊息。|

### <a name="example-verify-a-code"></a>範例：驗證程式代碼

下列範例顯示用來驗證程式代碼 Azure MFA 技術設定檔。

```xml
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
