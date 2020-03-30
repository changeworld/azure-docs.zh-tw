---
title: 在自訂策略中定義電話因數技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure 活動目錄 B2C 中的自訂策略中定義電話因數技術設定檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332662"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure 活動目錄 B2C 自訂策略中定義電話因數技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C（Azure AD B2C）支援註冊和驗證電話號碼。 此技術設定檔：

- 提供與使用者交互的使用者介面。
- 使用內容定義來控制外觀。
- 支援電話和短信來驗證電話號碼。
- 支援多個電話號碼。 使用者可以選擇要驗證的一個電話號碼。  
- 如果提供電話號碼，則電話因數使用者介面要求使用者驗證電話號碼。 如果未提供，則要求使用者註冊新電話號碼。
- 返回聲明，指示使用者是否提供了新電話號碼。 可以使用此聲明來決定是否應將電話號碼保留到 Azure AD 使用者設定檔。  

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理常式**屬性必須包含 Azure AD B2C 用於電話因數的協定處理常式程式集的完全限定名稱：`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的示例顯示了用於註冊和驗證的電話因數技術設定檔：

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>輸入宣告

InputClaims 元素必須包含以下聲明。 您還可以將索賠的名稱映射到電話因數技術設定檔中定義的名稱。 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

下面的示例演示了使用多個電話號碼。 有關詳細資訊，請參閱[示例策略](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

InputClaims轉換元素可能包含一個輸入聲明轉換元素的集合，這些元素用於修改輸入聲明或生成新聲明，然後再將其呈現到電話因數頁。

## <a name="output-claims"></a>輸出宣告

"輸出索賠"元素包含電話因數技術設定檔返回的聲明清單。

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

OutputClaimsTransformations 元素可能含有 OutputClaimsTransformation 的集合，用於修改輸出宣告或產生新的輸出宣告。

## <a name="cryptographic-keys"></a>密碼編譯金鑰

不使用 **CryptographicKeys** 元素。


## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 是 | 與此技術設定檔相關聯的[內容定義](contentdefinitions.md)識別碼。 |
| 手動電話號碼輸入允許| 否 | 指定是否允許使用者手動輸入電話號碼。 可能的值：`true`或`false`（預設值）。|

### <a name="ui-elements"></a>UI 元素

電話因數認證頁面使用者介面元素可以[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

## <a name="next-steps"></a>後續步驟

- 使用 MFA 初學者包檢查[社交和本地帳戶](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)。

