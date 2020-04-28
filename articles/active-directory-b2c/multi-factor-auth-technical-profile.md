---
title: 自訂原則中的 Azure MFA 技術設定檔
titleSuffix: Azure AD B2C
description: Azure AD B2C 中 Azure 多重要素驗證（MFA）技術設定檔的自訂原則參考。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80332811"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂原則中定義 Azure MFA 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）提供使用 Azure 多重要素驗證（MFA）來驗證電話號碼的支援。 使用此技術設定檔來產生程式碼，並將其傳送至電話號碼，然後驗證該程式碼。 Azure MFA 技術設定檔也可能會傳回錯誤訊息。  在使用者旅程圖繼續進行之前，驗證技術設定檔會驗證使用者提供的資料。 使用驗證技術設定檔時，會在自我判斷頁面上顯示錯誤訊息。

此技術設定檔：

- 不提供介面來與使用者互動。 相反地，使用者介面是從[自我](self-asserted-technical-profile.md)判斷技術設定檔中呼叫，或是以[顯示控制項](display-controls.md)做為[驗證技術設定檔](validation-technical-profile.md)。
- 會使用 Azure MFA 服務來產生並傳送程式碼至電話號碼，然後驗證程式代碼。  
- 透過文字訊息驗證電話號碼。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **Handler**屬性必須包含 Azure AD B2C 所使用之通訊協定處理常式元件的完整名稱：

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

下列範例顯示 Azure MFA 技術設定檔：

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>傳送 SMS

此技術設定檔的第一個模式是產生並傳送程式碼。 您可以在此模式中設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Azure MFA 的宣告清單。 您也可以將宣告的名稱對應至 MFA 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- |
| userPrincipalName | 是 | 擁有電話號碼之使用者的識別碼。 |
| phoneNumber | 是 | 要用來傳送 SMS 代碼的電話號碼。 |
| companyName | 否 |SMS 中的公司名稱。 如果未提供，則會使用您的應用程式名稱。 |
| 地區設定 | 否 | SMS 的地區設定。 如果未提供，則會使用使用者的瀏覽器地區設定。 |

**InputClaimsTransformations**元素可能包含**InputClaimsTransformation**元素的集合，這些專案是用來修改輸入宣告，或在傳送至 Azure MFA 服務之前產生新的宣告。

### <a name="output-claims"></a>輸出宣告

Azure MFA 通訊協定提供者不會傳回任何**OutputClaims**，因此不需要指定輸出宣告。 不過，只要您設定了`DefaultValue`屬性，您就可以包含 Azure MFA 識別提供者未傳回的宣告。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須是**OneWaySMS**。  |

#### <a name="ui-elements"></a>UI 元素

您可以使用下列中繼資料來設定傳送 SMS 失敗時所顯示的錯誤訊息。 應該在[自我](self-asserted-technical-profile.md)判斷技術設定檔中設定中繼資料。 可以將錯誤訊息[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfCouldntSendSms | 否 | 如果提供的電話號碼不接受 SMS，則為使用者錯誤訊息。 |
| UserMessageIfInvalidFormat | 否 | 如果提供的電話號碼不是有效的電話號碼，則為使用者錯誤訊息。 |
| UserMessageIfServerError | 否 | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。 |
| UserMessageIfThrottled| 否 | 使用者錯誤訊息（如果要求已節流）。|

### <a name="example-send-an-sms"></a>範例：傳送 SMS

下列範例顯示透過 SMS 傳送程式碼時所使用的 Azure MFA 技術設定檔。

```XML
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

## <a name="verify-code"></a>驗證碼

此技術設定檔的第二個模式是驗證程式代碼。 您可以在此模式中設定下列選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要傳送至 Azure MFA 的宣告清單。 您也可以將宣告的名稱對應至 MFA 技術設定檔中定義的名稱。

| ClaimReferenceId | 必要 | 描述 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| 是 | 與先前用來傳送程式碼的電話號碼相同。 它也可用來尋找電話驗證會話。 |
| verificationCode  | 是 | 要驗證的使用者所提供的驗證碼 |

**InputClaimsTransformations**元素可能包含**InputClaimsTransformation**元素的集合，這些專案是用來修改輸入宣告或產生新的專案，然後才呼叫 Azure MFA 服務。

### <a name="output-claims"></a>輸出宣告

Azure MFA 通訊協定提供者不會傳回任何**OutputClaims**，因此不需要指定輸出宣告。 不過，只要您設定了`DefaultValue`屬性，您就可以包含 Azure MFA 識別提供者未傳回的宣告。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須**驗證** |

#### <a name="ui-elements"></a>UI 元素

下列中繼資料可用來設定程式碼驗證失敗時所顯示的錯誤訊息。 應該在[自我](self-asserted-technical-profile.md)判斷技術設定檔中設定中繼資料。 可以將錯誤訊息[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| UserMessageIfMaxAllowedCodeRetryReached| 否 | 使用者錯誤訊息（如果使用者嘗試驗證碼太多次）。 |
| UserMessageIfServerError | 否 | 如果伺服器發生內部錯誤，則為使用者錯誤訊息。 |
| UserMessageIfThrottled| 否 | 如果要求已節流，則為使用者錯誤訊息。|
| UserMessageIfWrongCodeEntered| 否| 如果輸入要驗證的程式碼錯誤，則會顯示使用者錯誤訊息。|

### <a name="example-verify-a-code"></a>範例：驗證程式代碼

下列範例顯示用來驗證程式代碼的 Azure MFA 技術設定檔。

```XML
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
