---
title: Azure Active Directory B2C 中的使用者設定檔屬性
description: 瞭解 Azure AD B2C directory 使用者設定檔所支援的使用者資源類型屬性。 瞭解內建屬性、延伸模組，以及屬性如何對應至 Microsoft Graph。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e921f0a40f53b1d08831047d1cb89ca26de41402
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80057298"
---
# <a name="user-profile-attributes"></a>使用者設定檔屬性

您的 Azure Active Directory （Azure AD） B2C 目錄使用者設定檔隨附內建的屬性集，例如名字、姓氏、城市、郵遞區號和電話號碼。 您可以使用自己的應用程式資料來擴充使用者設定檔，而不需要外部資料存放區。 Microsoft Graph 也支援可與 Azure AD B2C 使用者設定檔搭配使用的大部分屬性。 本文描述支援的使用者設定檔屬性 Azure AD B2C。 它也會說明 Microsoft Graph 不支援的屬性，以及不應該搭配 Azure AD B2C 使用的 Microsoft Graph 屬性。

> [!IMPORTANT]
> 您不應該使用內建或擴充屬性來儲存機密的個人資料，例如帳號憑證、政府標識號、持卡人資料、財務帳戶資料、醫療保健資訊或機密背景資訊。

您也可以與外部系統整合。 例如，您可以使用 Azure AD B2C 進行驗證，但將其委派給外部客戶關係管理（CRM）或客戶忠誠度資料庫，做為客戶資料的授權來源。 如需詳細資訊，請參閱[遠端設定檔](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)解決方案。

下表列出 Azure AD B2C directory 使用者設定檔所支援的[使用者資源類型](https://docs.microsoft.com/graph/api/resources/user)屬性。 它會提供有關每個屬性的下列資訊：

- Azure AD B2C 使用的屬性名稱（後面接著括弧中的 Microsoft Graph 名稱，如果不同的話）
- 屬性資料類型
- 屬性描述
- 如果屬性可在 Azure 入口網站中使用
- 如果屬性可以在使用者流程中使用
- 如果屬性可用於自訂原則[Azure AD 技術設定檔](active-directory-technical-profile.md)，以及在哪一個區段中&lt;（&gt;InputClaims &lt;、&gt;OutputClaims 或&lt;PersistedClaims&gt;）

|名稱     |類型     |描述|Azure 入口網站|使用者流程|自訂原則|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|使用者帳戶是否已啟用或停用：如果已啟用帳戶，**則為 true** ，否則為**false**。|是|否|保存，輸出|
|年齡群組        |String|使用者的年齡群組。 可能的值： null、Undefined、小調、成人、NotAdult。|是|否|保存，輸出|
|alternativeSecurityId （[身分](manage-user-accounts-graph-api.md#identities-property)識別）|String|來自外部識別提供者的單一使用者身分識別。|否|否|輸入、保存、輸出|
|alternativeSecurityIds （[身分](manage-user-accounts-graph-api.md#identities-property)識別）|替代的 securityId 集合|來自外部身分識別提供者之使用者身分識別的集合。|否|否|保存，輸出|
|city            |String|使用者所在的縣市。 最大長度128。|是|是|保存，輸出|
|consentProvidedForMinor|String|是否已為次要提供同意。 允許的值： null、已授與、拒絕或 notRequired。|是|否|保存，輸出|
|country         |String|使用者所在的國家/地區。 範例： "US" 或 "UK"。 最大長度128。|是|是|保存，輸出|
|createdDateTime|Datetime|建立使用者物件的日期。 唯讀。|否|否|保存，輸出|
|creationType    |String|如果使用者帳戶已建立為 Azure Active Directory B2C 租使用者的本機帳戶，則其值為 LocalAccount 或 nameCoexistence。 唯讀。|否|否|保存，輸出|
|dateOfBirth     |Date|生日。|否|否|保存，輸出|
|department      |String|使用者工作的部門名稱。 最大長度64。|是|否|保存，輸出|
|displayName     |String|使用者的顯示名稱。 最大長度256。|是|是|保存，輸出|
|facsimileTelephoneNumber<sup>1</sup>|String|使用者的商務傳真機電話號碼。|是|否|保存，輸出|
|givenName       |String|使用者的名字。 最大長度64。|是|是|保存，輸出|
|jobTitle        |String|使用者的職稱。 最大長度128。|是|是|保存，輸出|
|immutableId     |String|識別碼，通常用於從內部部署 Active Directory 遷移的使用者。|否|否|保存，輸出|
|legalAgeGroupClassification|String|法律年齡群組分類。 [唯讀] 和 [計算依據] ageGroup 和 consentProvidedForMinor 屬性。 允許的值： null、minorWithOutParentalConsent、minorWithParentalConsent、minorNoParentalConsentRequired、notAdult 和成人。|是|否|保存，輸出|
|legalCountry<sup>1</sup>  |String|適用于法律用途的國家/地區。|否|否|保存，輸出|
|mail            |String|使用者的 SMTP 位址，例如 "bob@contoso.com"。 唯讀。|否|否|保存，輸出|
|mailNickName    |String|使用者的郵件別名。 最大長度64。|否|否|保存，輸出|
|mobile （mobilePhone） |String|使用者的主要行動電話號碼。 最大長度64。|是|否|保存，輸出|
|netId           |String|Net ID。|否|否|保存，輸出|
|objectId        |String|全域唯一識別碼（GUID），這是使用者的唯一識別碼。 範例： 12345678-9abc-def0-9abc-def0-1234-56789abcde。 唯讀、不可變。|唯讀|是|輸入、保存、輸出|
|otherMails      |字串集合|使用者其他電子郵件地址的清單。 範例： ["bob@contoso.com"，"Robert@fabrikam.com"]。|是（替代電子郵件）|否|保存，輸出|
|密碼        |String|使用者建立期間的本機帳戶密碼。|否|否|已保存|
|passwordPolicies     |String|密碼的原則。 它是由不同原則名稱所組成的字串，並以逗號分隔。 亦即 "DisablePasswordExpiration，DisableStrongPassword"。|否|否|保存，輸出|
|physicalDeliveryOfficeName （officeLocation）|String|使用者工作地點的辦公室位置。 最大長度128。|是|否|保存，輸出|
|postalCode      |String|使用者郵寄地址的郵遞區號。 具體的郵遞區號依使用者的國家/地區而定。 在美國，此屬性包含了郵遞區號 (ZIP Code)。 最大長度40。|是|否|保存，輸出|
|preferredLanguage    |String|使用者慣用的語言。 應遵循 ISO 639-1 程式碼。 範例： "en-us"。|否|否|保存，輸出|
|refreshTokensValidFromDateTime|Datetime|在此時間之前發出的任何重新整理權杖都無效，而且當使用不正確重新整理權杖來取得新的存取權杖時，應用程式將會收到錯誤。 如果發生這種情況，應用程式將需要向授權端點提出要求，以取得新的重新整理權杖。 唯讀。|否|否|輸出|
|signInNames （[身分](manage-user-accounts-graph-api.md#identities-property)識別） |String|目錄中任何類型之本機帳戶使用者的唯一登入名稱。 使用此功能來取得具有登入值的使用者，而不需要指定本機帳戶類型。|否|否|輸入|
|signInNames.[userName （身分](manage-user-accounts-graph-api.md#identities-property)識別） |String|目錄中本機帳戶使用者的唯一使用者名稱。 使用此功能來建立或取得具有特定登入使用者名稱的使用者。 在修補作業期間單獨在 PersistedClaims 中指定此項，將會移除其他類型的 signInNames。 如果您想要加入新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入、保存、輸出|
|signInNames.[phoneNumber （身分](manage-user-accounts-graph-api.md#identities-property)識別） |String|目錄中本機帳戶使用者的唯一電話號碼。 使用此功能來建立或取得具有特定登入電話號碼的使用者。 在修補作業期間單獨在 PersistedClaims 中指定此項，將會移除其他類型的 signInNames。 如果您想要加入新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入、保存、輸出|
|signInNames.[emailAddress （身分](manage-user-accounts-graph-api.md#identities-property)識別）|String|目錄中本機帳戶使用者的唯一電子郵件地址。 使用此功能來建立或取得具有特定登入電子郵件地址的使用者。 在修補作業期間單獨在 PersistedClaims 中指定此項，將會移除其他類型的 signInNames。 如果您想要加入新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入、保存、輸出|
|State           |String|使用者地址所屬的州/省。 最大長度128。|是|是|保存，輸出|
|streetAddress   |String|使用者工作地點的街道地址。 最大長度1024。|是|是|保存，輸出|
|Users.strongauthentication AlternativePhoneNumber<sup>1</sup>|String|使用者的次要電話號碼，用於多重要素驗證。|是|否|保存，輸出|
|strongAuthenticationEmailAddress<sup>1</sup>|String|使用者的 SMTP 位址。 範例： "bob@contoso.com" 此屬性用於以使用者名稱原則登入，用來儲存使用者的電子郵件地址。 然後在密碼重設流程中使用電子郵件地址。|是|否|保存，輸出|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|使用者的主要電話號碼，用於多重要素驗證。|是|否|保存，輸出|
|surname         |String|使用者的姓氏。 最大長度64。|是|是|保存，輸出|
|telephoneNumber （businessPhones 的第一個專案）|String|使用者工作地點的主要電話號碼。|是|否|保存，輸出|
|userPrincipalName    |String|使用者的使用者主要名稱 (UPN)。 UPN 是使用者的網際網路型態登入名稱 (根據網際網路標準 RFC 822)。 網域必須存在於租使用者的已驗證網域集合中。 建立帳戶時，需要此屬性。 不可變。|否|否|輸入、保存、輸出|
|usageLocation   |String|基於法律要求而必須檢查服務在國家或地區中的可用性，將獲派授權的使用者需要此項目。 不可為 Null。 雙字母的國碼 (ISO 標準 3166)。 範例： "US"、"JP" 和 "GB"。|是|否|保存，輸出|
|userType        |String|可以用來分類目錄中使用者類型的字串值。 值必須是成員。 唯讀。|唯讀|否|保存，輸出|
|userState （externalUserState）<sup>2</sup>|String|僅適用于 Azure AD B2B 帳戶，指出邀請為 PendingAcceptance 或已接受。|否|否|保存，輸出|
|userStateChangedOn （externalUserStateChangeDateTime）<sup>2</sup>|Datetime|顯示 UserState 屬性最新變更的時間戳記。|否|否|保存，輸出|
|<sup>1</sup>Microsoft Graph 不支援<br><sup>2</sup>不能與 Azure AD B2C 搭配使用||||||


## <a name="extension-attributes"></a>擴充屬性

您通常需要建立自己的屬性，如下列情況所示：

- 客戶面向的應用程式需要保存**loyaltynumber 傳送**之類的屬性。
- 識別提供者擁有必須儲存的唯一使用者識別碼，例如 **uniqueUserGUID**。
- 自訂使用者旅程圖必須保存使用者狀態，例如**migrationStatus**。

Azure AD B2C 會擴充每個使用者帳戶所儲存的屬性組合。 延伸模組屬性會延伸目錄中使用者物件的[架構](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)。 擴充屬性只能在應用程式物件上註冊，即使它們可能包含使用者的資料也一樣。 擴充屬性會附加至名為 b2c-extensions-應用程式的應用程式。 請勿修改此應用程式，因為它是用來儲存使用者資料的 Azure AD B2C。 您可以在 Azure Active Directory 應用程式註冊下找到此應用程式。

> [!NOTE]
> - 最多100延伸模組屬性可以寫入任何使用者帳戶。
> - 如果 b2c 延伸模組應用程式已刪除，則會從所有使用者連同其包含的任何資料中移除這些延伸模組屬性。
> - 如果應用程式刪除擴充屬性，則會將它從所有使用者帳戶中移除，並刪除這些值。
> - 擴充屬性的基礎名稱會以 "Extension_" + 應用程式識別碼 + "_" + 屬性名稱的格式產生。 例如，如果您建立擴充屬性 Loyaltynumber 傳送，而 b2c-延伸模組-應用程式識別碼是831374b3-bd50-41bf-aa54-263ec9e050fc，則基礎延伸模組屬性名稱會是： extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber。 當您執行圖形 API 查詢來建立或更新使用者帳戶時，您會使用基礎名稱。

在架構延伸中定義屬性時，支援下列資料類型：

|屬性類型 |備註  |
|--------------|---------|
|Boolean    | 可能的值： **true**或**false**。 |
|Datetime   | 必須指定採用 ISO 8601 格式。 會儲存在 UTC 中。   |
|整數    | 32 位元值。               |
|String     | 最多 256 個字元。     |

## <a name="next-steps"></a>後續步驟
深入瞭解延伸模組屬性：
- [結構描述延伸模組](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [使用使用者流程定義自訂屬性](user-flow-custom-attributes.md)
- [使用自訂原則定義自訂屬性](custom-policy-custom-attributes.md)
