---
title: Azure Active Directory B2C 中的使用者設定檔屬性
description: 了解 Azure AD B2C 目錄使用者設定檔所支援的使用者資源類型屬性。 了解內建屬性、擴充功能，以及屬性如何對應至 Microsoft Graph。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/13/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b134c4e9e980104a54f6a96d45445ee114556a5
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178716"
---
# <a name="user-profile-attributes"></a>使用者設定檔屬性

您的 Azure Active Directory (Azure AD) B2C 目錄使用者設定檔隨附一組內建屬性，包括像是名字、姓氏、城市、郵遞區號和電話號碼等等。 您可以使用自己的應用程式資料來擴充使用者設定檔，而不需要外部資料存放區。 

Microsoft Graph 也支援大部分可與 Azure AD B2C 使用者設定檔搭配使用的屬性。 本文描述支援的 Azure AD B2C 使用者設定檔屬性。 同時也會說明 Microsoft Graph 不支援的屬性，以及不應該搭配 Azure AD B2C 使用的 Microsoft Graph 屬性。

> [!IMPORTANT]
> 您不應該使用內建或擴充屬性來儲存機密的個人資料，例如帳號登入資訊、政府身份證編號、持卡人資料、財務帳戶資料、醫療保健資訊或機密背景資訊。

您也可以與外部系統整合。 例如，您可以使用 Azure AD B2C 進行驗證，但委派給外部客戶關係管理 (CRM) 或客戶忠誠度資料庫，作為客戶資料的可信賴來源。 如需詳細資訊，請參閱[遠端設定檔](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)解決方案。

## <a name="azure-ad-user-resource-type"></a>Azure AD 使用者資源類型

下表列出 Azure AD B2C 目錄使用者設定檔所支援的[使用者資源類型](/graph/api/resources/user)屬性， 並提供每個屬性的下列資訊：

- Azure AD B2C 使用的屬性名稱 (如果與 Microsoft Graph 名稱不同，會在括弧中提供 Microsoft Graph 名稱)
- 屬性資料類型
- 屬性描述
- 若 Azure 入口網站中具有該屬性
- 如果屬性可以在使用者流程中使用
- 如果屬性可用於自訂原則 [Azure AD 技術設定檔](active-directory-technical-profile.md)，以及位於哪個區段 (&lt;InputClaims&gt;、&lt;OutputClaims&gt; 或 &lt;PersistedClaims&gt;)

|名稱     |類型     |描述|Azure 入口網站|使用者流程|自訂原則|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|使用者帳戶是否已啟用或停用：如果已啟用帳戶，則為 **True**，否則為 **False**。|是|否|保留，輸出|
|ageGroup        |String|使用者的年齡群組。 可能的值：Null、未定義、未成年、成人、非成人。|是|否|保留，輸出|
|alternativeSecurityId ([身分識別](#identities-attribute))|String|來自外部識別提供者的單一使用者身分識別。|否|否|輸入，保留，輸出|
|alternativeSecurityIds ([身分識別](#identities-attribute))|替代的 securityId 集合|來自外部識別提供者的使用者身分識別集合。|否|否|保留，輸出|
|city            |String|使用者所在的城市。 長度上限為 128。|是|是|保留，輸出|
|consentProvidedForMinor|String|是否已為未成年人提供同意資訊。 允許的值：Null、已授與、已拒絕或不需要。|是|否|保留，輸出|
|country         |String|使用者所在的國家/地區。 範例："US" 或 "UK"。 長度上限為 128。|是|是|保留，輸出|
|createdDateTime|Datetime|建立使用者物件的日期。 唯讀。|否|否|保留，輸出|
|creationType    |String|如果使用者帳戶已建立為 Azure Active Directory B2C 租用戶的本機帳戶，則其值為 LocalAccount 或 nameCoexistence。 唯讀。|否|否|保留，輸出|
|dateOfBirth     |Date|生日。|否|否|保留，輸出|
|department      |String|使用者任職的部門名稱。 長度上限為 64。|是|否|保留，輸出|
|displayName     |String|使用者的顯示名稱。 長度上限為 256。|是|是|保留，輸出|
|facsimileTelephoneNumber<sup>1</sup>|String|使用者的商務傳真機電話號碼。|是|否|保留，輸出|
|givenName       |String|使用者的名字。 長度上限為 64。|是|是|保留，輸出|
|jobTitle        |String|使用者的職稱。 長度上限為 128。|是|是|保留，輸出|
|immutableId     |String|通常用於從內部部署 Active Directory 遷移之使用者的識別碼。|否|否|保留，輸出|
|legalAgeGroupClassification|String|法定年齡群組分類。 唯讀並以 ageGroup 和 consentProvidedForMinor 屬性為計算依據。 允許的值： null、minorWithOutParentalConsent、minorWithParentalConsent、minorNoParentalConsentRequired、notAdult 和成人。|是|否|保留，輸出|
|legalCountry<sup>1</sup>  |String|適用於法律用途的國家/地區。|否|否|保留，輸出|
|mail            |String|使用者的 SMTP 位址，例如 "bob@contoso.com"。 唯讀。|否|否|保留，輸出|
|mailNickName    |String|使用者的郵件別名。 長度上限為 64。|否|否|保留，輸出|
|行動裝置 (mobilePhone) |String|使用者的主要行動電話號碼。 長度上限為 64。|是|否|保留，輸出|
|netId           |String|網狀 ID。|否|否|保留，輸出|
|objectId        |String|全域唯一識別碼 (GUID)，這是使用者的唯一識別碼。 範例：12345678-9abc-def0-1234-56789abcde。 唯讀、不可變。|唯讀|是|輸入，保留，輸出|
|otherMails      |字串集合|使用者的其他電子郵件地址清單。 範例：["bob@contoso.com", "Robert@fabrikam.com"]。|是 (其他電子郵件)|否|保留，輸出|
|密碼        |String|使用者建立期間的本機帳戶密碼。|否|否|已保存|
|passwordPolicies     |String|密碼原則。 是由不同原則名稱所組成的字串，並以逗號分隔。 例如，"DisablePasswordExpiration，DisableStrongPassword"。|否|否|保留，輸出|
|physicalDeliveryOfficeName (officeLocation)|String|使用者的公司所在位置。 長度上限為 128。|是|否|保留，輸出|
|postalCode      |String|使用者郵寄地址的郵遞區號。 郵遞區號適用於使用者的國家/地區。 在美國地區，此屬性包含郵遞區號。 長度上限為 40。|是|否|保留，輸出|
|preferredLanguage    |String|使用者的慣用語言。 應遵循 ISO 639-1 條款。 例如："en-US"。|否|否|保留，輸出|
|refreshTokensValidFromDateTime|Datetime|在此時間之前發出的任何重新整理權杖都無效，而且當使用不正確重新整理權杖來取得新的存取權杖時，應用程式將會收到錯誤。 如果發生這種情況，應用程式需要向授權端點提出要求，以取得新的重新整理權杖。 唯讀。|否|否|輸出|
|signInNames ([身分識別](#identities-attribute)) |String|目錄中任何類型之本機帳戶使用者的唯一登入名稱。 您可以使用此屬性來取得具有登入值的使用者，而不需指定本機帳戶類型。|否|否|輸入|
|signInNames.userName ([身分識別](#identities-attribute)) |String|目錄中本機帳戶使用者的唯一使用者名稱。 使用此屬性來建立或取得具有特定登入使用者名稱的使用者。 在修補作業期間單獨在 PersistedClaims 中指定此項，將會移除其他類型的 signInNames。 如果要新增新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入，保留，輸出|
|signInNames.phoneNumber ([身分識別](#identities-attribute)) |String|目錄中本機帳戶使用者的唯一電話號碼。 您可以使用此屬性來建立或取得具有特定登入電話號碼的使用者。 在修補作業期間單獨指定 PersistedClaims 中的這個屬性將會移除其他類型的 >signinnames.emailaddress。 如果要新增新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入，保留，輸出|
|signInNames.emailAddress ([身分識別](#identities-attribute))|String|目錄中本機帳戶使用者的唯一電子郵件地址。 使用此功能來建立或取得具有特定登入電子郵件地址的使用者。 在修補作業期間單獨指定 PersistedClaims 中的這個屬性將會移除其他類型的 >signinnames.emailaddress。 如果要新增新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入，保留，輸出|
|state           |String|使用者地址的州或省。 長度上限為 128。|是|是|保留，輸出|
|streetAddress   |String|使用者公司所在位置的街道地址。 長度上限為 1024。|是|是|保留，輸出|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|用於多重要素驗證的使用者次要電話號碼。|是|否|保留，輸出|
|strongAuthenticationEmailAddress<sup>1</sup>|String|使用者的 SMTP 位址。 範例："bob@contoso.com" 此屬性用於以使用者名稱原則登入，以儲存使用者的電子郵件地址。 然後此電子郵件地址會在密碼重設流程中使用。|是|否|保留，輸出|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|用於多重要素驗證的使用者主要電話號碼。|是|否|保留，輸出|
|surname         |String|使用者的姓氏。 長度上限為 64。|是|是|保留，輸出|
|telephoneNumber (businessPhones 的第一個項目)|String|使用者公司所在位置的主要電話號碼。|是|否|保留，輸出|
|userPrincipalName    |String|使用者的使用者主體名稱 (UPN)。 UPN 是依據網際網路標準 RFC 822 的使用者網際網路樣式登入名稱。 網域必須存在於租用戶的已驗證網域集合內。 建立帳戶時需要此屬性。 不可變。|否|否|輸入，保留，輸出|
|usageLocation   |String|根據法律的要求，所有要指派以授權的使用者皆須指定，才能查看該國家/地區的服務供應狀況。 不可為 Null。 兩個字母的國家/區域代碼 (ISO 標準 3166)。 範例："US"、"JP" 和 "GB"。|是|否|保留，輸出|
|userType        |String|字串值可用來在目錄中分類使用者類型。 值必須是「成員」。 唯讀。|唯讀|否|保留，輸出|
|userState (externalUserState) <sup>3</sup>|String|僅供 Azure AD B2B 帳戶使用，指出邀請狀態為待接受或已接受。|否|否|保留，輸出|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|顯示 UserState 屬性的最新變更時間戳記。|否|否|保留，輸出|

<sup>1 </sup>不受 Microsoft Graph 支援<br><sup>2 </sup>如需詳細資訊，請參閱 [MFA 電話號碼屬性](#mfa-phone-number-attribute)<br><sup>3 </sup>不應搭配 Azure AD B2C 使用

## <a name="display-name-attribute"></a>顯示名稱屬性

`displayName`是要在使用者的 Azure 入口網站使用者管理中顯示的名稱，而在存取權杖中 Azure AD B2C 會傳回給應用程式。 這是必要屬性。

## <a name="identities-attribute"></a>識別屬性

客戶帳戶（可能是取用者、合作夥伴或公民）可以與這些身分識別類型相關聯：

- **本機** 身分識別：使用者名稱和密碼會儲存在本機的 Azure AD B2C 目錄。 我們通常會將這些身分識別稱為「本機帳戶」。
- 同盟身分識別（也稱為 *社交* 或 *企業* 帳戶）是由同盟身分識別提供者（例如 FACEBOOK、Microsoft、ADFS 或 Salesforce）管理的使用者 **身分識別。**

具有客戶帳戶的使用者可以使用多個身分識別登入。 例如，使用者名稱、電子郵件、員工識別碼、政府識別碼等。 單一帳戶可以有多個身分識別（本機和社交），且具有相同的密碼。

在 Microsoft Graph API 中，本機和同盟身分識別都會儲存在使用者 `identities` 屬性中，其類型為 [objectIdentity] [Graph-objectIdentity]。 `identities`集合代表一組用來登入使用者帳戶的身分識別。 此集合可讓使用者使用其任何相關聯的身分識別來登入使用者帳戶。

| 名稱   | 類型 |Description|
|:---------------|:--------|:----------|
|signInType|字串| 指定目錄中的使用者登入類型。 針對本機帳戶：  `emailAddress` 、 `emailAddress1` 、 `emailAddress2` 、 `emailAddress3` 、  `userName` 或您喜歡的任何其他類型。 社交帳戶必須設定為  `federated` 。|
|簽發者|字串|指定身分識別的簽發者。 若為本機帳戶 (未) signInType `federated` ，則此屬性為本機 B2C 租使用者預設功能變數名稱，例如 `contoso.onmicrosoft.com` 。 針對 **signInType**) 的社交身分識別 ( `federated` ，此值是簽發者的名稱，例如 `facebook.com`|
|issuerAssignedId|字串|指定簽發者指派給使用者的唯一識別碼。 **簽發者** 和 **issuerAssignedId** 的組合在您的租使用者中必須是唯一的。 針對本機帳戶，當 **signInType** 設定為 `emailAddress` 或時 `userName` ，它代表使用者的登入名稱。<br>當 **signInType** 設定為： <ul><li>`emailAddress` (或以類似的開頭 `emailAddress` `emailAddress1`) **issuerAssignedId** 必須是有效的電子郵件地址</li><li>`userName` (或任何其他值) ， **issuerAssignedId** 必須是 [電子郵件地址的有效本機部分](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`， **issuerAssignedId** 代表同盟帳戶的唯一識別碼</li></ul>|

**下列身分** 識別屬性，具有具有登入名稱的本機帳戶身分識別、電子郵件地址，以及社交身分識別。 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

針對同盟身分識別，視身分識別提供者而定， **issuerAssignedId** 是每個應用程式或開發帳戶之指定使用者的唯一值。 使用與社交提供者相同的應用程式識別碼，或相同開發帳戶內的另一個應用程式，設定 Azure AD B2C 原則。 

## <a name="password-profile-property"></a>密碼配置檔案屬性

若為本機身分識別，則需要 **passwordProfile** 屬性，而且會包含使用者的密碼。 `forceChangePasswordNextSignIn`屬性必須設定為 `false` 。

針對同盟 (社交) 身分識別，不需要 **passwordProfile** 屬性。

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>密碼原則屬性

本機帳戶的 Azure AD B2C 密碼原則 () 是以 Azure Active Directory [強式密碼強度](../active-directory/authentication/concept-sspr-policy.md) 原則為基礎。 Azure AD B2C 的註冊或登入和密碼重設原則都需要此強式密碼強度，而且密碼不會過期。

在使用者遷移案例中，如果您想要遷移的帳戶的密碼強度比 Azure AD B2C 強制執行的 [強式密碼強度](../active-directory/authentication/concept-sspr-policy.md) 弱，您可以停用強式密碼需求。 若要變更預設密碼原則，請將 `passwordPolicies` 屬性設定為 `DisableStrongPassword` 。 例如，您可以修改「建立使用者要求」，如下所示：

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA 電話號碼屬性

使用電話進行多重要素驗證 (MFA) 時，會使用行動電話來驗證使用者身分識別。 若 [要](https://docs.microsoft.com/graph/api/authentication-post-phonemethods) 以程式設計方式新增電話號碼，請 [更新](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)、 [取得](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)或 [刪除](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete) 電話號碼，使用 MS 圖形 API [電話驗證方法](https://docs.microsoft.com/graph/api/resources/phoneauthenticationmethod)。

在 Azure AD B2C [自訂原則](custom-policy-overview.md)中，可透過宣告類型取得電話號碼 `strongAuthenticationPhoneNumber` 。

## <a name="extension-attributes"></a>擴充屬性

每個客戶面向的應用程式對於要收集的資訊都有獨特的需求。 您的 Azure AD B2C 租使用者隨附一組內建的資訊，這些資訊會儲存在屬性中，例如指定的名稱、姓氏和郵遞區號。 使用 Azure AD B2C，您可以擴充儲存在每個客戶帳戶中的屬性集。 如需詳細資訊，請參閱 [在 Azure Active Directory B2C 中新增使用者屬性和自訂使用者輸入](configure-user-input.md)

擴充屬性會[擴充目錄中的使用者物件結構描述](/graph/extensibility-overview#schema-extensions)。 擴充屬性只能在應用程式物件上註冊，即使其可能包含使用者的資料也一樣。 擴充屬性會附加至呼叫的應用程式 `b2c-extensions-app` 。 請勿修改此應用程式，因為 Azure AD B2C 會使用此應用程式來儲存使用者資料。 您可以在 Azure Active Directory 應用程式註冊下找到此應用程式。

> [!NOTE]
> - 任何的使用者帳戶中最多可以寫入 100 個擴充屬性。
> - 如果 b2c-extensions-app 應用程式已刪除，則系統會從所有使用者連同其包含的任何資料中移除這些擴充屬性。
> - 如果應用程式刪除某個擴充屬性，系統就會從所有使用者帳戶移除該擴充屬性，並將值刪除。

圖形 API 中的擴充屬性會使用慣例來命名 `extension_ApplicationClientID_AttributeName` ，其中是應用程式 `ApplicationClientID` **(用戶端) 識別碼**， (在應用程式註冊 Azure 入口網站中的 `b2c-extensions-app`   >  **所有應用程式** 中找到。 請注意， **應用程式 (用戶端) 識別碼** ，因為它在延伸模組屬性名稱中表示不包含連字號。 例如：

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

在架構延伸中定義屬性時，支援下列資料類型：

|類型 |備註  |
|--------------|---------|
|Boolean    | 可能的值：**True** 或 **False**。 |
|Datetime   | 必須以 ISO 8601 格式指定。 將以 UTC 格式儲存。   |
|整數    | 32 位元值。               |
|String     | 最多 256 個字元。     |

## <a name="next-steps"></a>後續步驟

深入了解關於擴充屬性：

- [結構描述延伸模組](/graph/extensibility-overview#schema-extensions)
- [定義自訂屬性](user-flow-custom-attributes.md)