---
title: 自訂策略中的 Azure MFA 技術設定檔
titleSuffix: Azure AD B2C
description: Azure AD B2C 中 Azure 多重要素驗證 （MFA） 技術設定檔的自訂策略引用。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332811"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自訂策略中定義 Azure MFA 技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C （Azure AD B2C） 支援使用 Azure 多重要素驗證 （MFA） 驗證電話號碼。 使用此技術設定檔生成代碼並將其發送到電話號碼，然後驗證代碼。 Azure MFA 技術設定檔也可能返回錯誤訊息。  在使用者旅程圖繼續進行之前，驗證技術設定檔會驗證使用者提供的資料。 使用驗證技術設定檔時，自斷言頁上將顯示一條錯誤訊息。

此技術設定檔：

- 不提供與使用者交互的介面。 相反，使用者介面是從[自斷言](self-asserted-technical-profile.md)的技術設定檔或[顯示控制項](display-controls.md)作為[驗證技術設定檔](validation-technical-profile.md)調用的。
- 使用 Azure MFA 服務生成代碼並將其發送到電話號碼，然後驗證代碼。  
- 通過短信驗證電話號碼。

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 使用的協定處理常式程式集的完全限定名稱：

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

下面的示例顯示了 Azure MFA 技術設定檔：

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>傳送簡訊

此技術設定檔的第一種模式是生成代碼併發送代碼。 可以為此模式配置以下選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要發送到 Azure MFA 的聲明清單。 您還可以將索賠的名稱映射到 MFA 技術設定檔中定義的名稱。

| 索賠參考Id | 必要 | 描述 |
| --------- | -------- | ----------- |
| userPrincipalName | 是 | 擁有電話號碼的使用者的識別碼。 |
| phoneNumber | 是 | 要向其發送 SMS 代碼的電話號碼。 |
| companyName | 否 |SMS 中的公司名稱。 如果未提供，則使用應用程式的名稱。 |
| 地區設定 | 否 | 短信地區設定。 如果未提供，則使用使用者的瀏覽器地區設定。 |

**InputClaims 轉換**元素可能包含**一個輸入聲明轉換**元素的集合，這些元素用於在發送到 Azure MFA 服務之前修改輸入聲明或生成新聲明。

### <a name="output-claims"></a>輸出宣告

Azure MFA 協定提供程式不返回任何**輸出聲明**，因此無需指定輸出聲明。 但是，只要設置`DefaultValue`屬性，就可以包括 Azure MFA 標識提供程式不會返回的聲明。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須是**OnewaySMS。**  |

#### <a name="ui-elements"></a>UI 元素

以下中繼資料可用於配置發送 SMS 失敗時顯示的錯誤訊息。 中繼資料應在[自斷言](self-asserted-technical-profile.md)的技術設定檔中配置。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 使用者留言如果無法傳送簡訊 | 否 | 如果提供的電話號碼不接受 SMS，則使用者錯誤訊息。 |
| 使用者消息如果無效格式 | 否 | 如果提供的電話號碼不是有效的電話號碼，則使用者錯誤訊息。 |
| 使用者消息如果伺服器錯誤 | 否 | 如果伺服器遇到內部錯誤，則使用者錯誤訊息。 |
| 使用者消息，如果被限制| 否 | 如果請求已被限制，則使用者錯誤訊息。|

### <a name="example-send-an-sms"></a>示例：傳送簡訊

下面的示例顯示了一個 Azure MFA 技術設定檔，用於通過 SMS 發送代碼。

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

此技術設定檔的第二種模式是驗證代碼。 可以為此模式配置以下選項。

### <a name="input-claims"></a>輸入宣告

**InputClaims**元素包含要發送到 Azure MFA 的聲明清單。 您還可以將索賠的名稱映射到 MFA 技術設定檔中定義的名稱。

| 索賠參考Id | 必要 | 描述 |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| 是 | 與以前用於發送代碼的電話號碼相同。 它還用於查找電話驗證會話。 |
| 驗證碼  | 是 | 要驗證的使用者提供的驗證碼 |

**InputClaims 轉換**元素可能包含**一個輸入聲明轉換**元素的集合，這些元素用於在調用 Azure MFA 服務之前修改輸入聲明或生成新聲明。

### <a name="output-claims"></a>輸出宣告

Azure MFA 協定提供程式不返回任何**輸出聲明**，因此無需指定輸出聲明。 但是，只要設置`DefaultValue`屬性，就可以包括 Azure MFA 標識提供程式不會返回的聲明。

**OutputClaimsTransformations** 元素可能含有 **OutputClaimsTransformation** 的集合，用於修改輸出宣告或產生新的輸出宣告。

### <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 作業 | 是 | 必須**驗證** |

#### <a name="ui-elements"></a>UI 元素

以下中繼資料可用於配置代碼驗證失敗時顯示的錯誤訊息。 中繼資料應在[自斷言](self-asserted-technical-profile.md)的技術設定檔中配置。 錯誤訊息可以[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| 使用者留言如果最大允許代碼重新到達| 否 | 如果使用者多次嘗試驗證碼，則出現使用者錯誤訊息。 |
| 使用者消息如果伺服器錯誤 | 否 | 如果伺服器遇到內部錯誤，則使用者錯誤訊息。 |
| 使用者消息，如果被限制| 否 | 如果請求被限制，則使用者錯誤訊息。|
| 使用者留言如果輸入了錯誤代碼| 否| 如果為驗證輸入的代碼錯誤，則使用者錯誤訊息。|

### <a name="example-verify-a-code"></a>示例：驗證代碼

下面的示例顯示了用於驗證代碼的 Azure MFA 技術設定檔。

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
