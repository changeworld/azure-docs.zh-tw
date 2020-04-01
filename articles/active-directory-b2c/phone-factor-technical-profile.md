---
title: 在自訂策略中定義電話因子技術設定檔
titleSuffix: Azure AD B2C
description: 在 Azure 活動目錄 B2C 中的自訂策略中定義電話因子技術配置檔。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437457"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure 活動目錄 B2C 自訂策略中定義電話因子技術設定檔

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活動目錄 B2C(Azure AD B2C)支援註冊和驗證電話號碼。 此技術設定檔:

- 提供使用者介面,以便與使用者進行交互以驗證或註冊電話號碼。
- 支援電話呼叫和簡訊以驗證電話號碼。
- 支援多個電話號碼。 用戶可以選擇要驗證的一個電話號碼。  
- 返回聲明,指示使用者是否提供了新電話號碼。 可以使用此聲明來決定是否應將電話號碼保留到 Azure AD B2C 使用者配置檔。  
- 使用[內容定義](contentdefinitions.md)來控制外觀。

## <a name="protocol"></a>通訊協定

**Protocol** 元素的 **Name** 屬性必須設定為 `Proprietary`。 **處理程式**屬性必須包含 Azure AD B2C 用於電話因子的協定處理程式程式集的完全限定名稱:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

下面的範例顯示了用於註冊和驗證的電話因子技術配置檔:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>輸入宣告轉譯

InputClaims 轉換元素可能包含用於修改輸入聲明或生成新聲明的輸入聲明轉換的集合。 以下輸入聲明轉換生成一個`UserId`聲明,該聲明稍後用於輸入聲明集合。

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>輸入宣告

InputClaims 元素必須包含以下聲明。 您還可以將索賠的名稱映射到電話因子技術配置檔中定義的名稱。 

|  資料類型| 必要 | 描述 |
| --------- | -------- | ----------- | 
| 字串| 是 | 使用者的唯一識別碼。 宣告名稱或合作夥伴索賠類型必須設定為`UserId`。 此聲明不應包含個人可識別資訊。|
| 字串| 是 | 聲明類型的清單。 每個聲明包含一個電話號碼。 如果任何輸入聲明不包含電話號碼,將詢問用戶註冊並驗證新電話號碼。 已驗證的電話號碼將作為輸出聲明返回。 如果其中一個輸入聲明包含電話號碼,則要求用戶驗證它。 如果多個輸入聲明包含電話號碼,則要求用戶選擇並驗證其中一個電話號碼。 |

下面的示例演示了使用多個電話號碼。 有關詳細資訊,請參閱[範例原則](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa)。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>輸出宣告

「輸出索賠」元素包含電話因子技術配置檔返回的聲明清單。

|  資料類型| 必要 | 描述 |
|  -------- | ----------- |----------- |
| boolean | 是 | 指示使用者是否已輸入新電話號碼。 宣告名稱或合作夥伴索賠類型必須設定為`newPhoneNumberEntered`|
| 字串| 是 | 已驗證的電話號碼。 宣告名稱或合作夥伴索賠類型必須設定為`Verified.OfficePhone`。|

OutputClaims 轉換元素可能包含用於修改輸出聲明或生成新聲明的輸出聲明轉換元素的集合。

## <a name="cryptographic-keys"></a>密碼編譯金鑰

不使用 **CryptographicKeys** 元素。


## <a name="metadata"></a>中繼資料

| 屬性 | 必要 | 描述 |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | 是 | 與此技術設定檔相關聯的[內容定義](contentdefinitions.md)識別碼。 |
| 手動電話號碼輸入允許| 否 | 指定是否允許用戶手動輸入電話號碼。 可能的值:`true``false`或 (預設值)。|
| 設定.認證模式 | 否 | 驗證電話號碼的方法。 可能的值: `sms` `phone``mixed`、 或 (預設值)。|
| 設定.自動撥號| 否| 指定技術設定檔是自動撥號還是自動發送 SMS。 可能的值:`true``false`或 (預設值)。 自動撥號要求將`setting.authenticationMode`中繼資料`sms`設定為`phone`或 。 輸入聲明集合必須具有單個電話號碼。 |

### <a name="ui-elements"></a>UI 元素

電話因子認證頁面使用者介面元素可以[當地語系化](localization-string-ids.md#azure-mfa-error-messages)。

## <a name="next-steps"></a>後續步驟

- 使用 MFA 初學者包檢查[社交和本地帳戶](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)。
