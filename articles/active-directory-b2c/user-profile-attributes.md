---
title: Azure Active Directory B2C 中的使用者設定檔屬性
description: 了解 Azure AD B2C 目錄使用者設定檔所支援的使用者資源類型屬性。 了解內建屬性、擴充功能，以及屬性如何對應至 Microsoft Graph。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738552"
---
# <a name="user-profile-attributes"></a>使用者設定檔屬性

您的 Azure Active Directory (Azure AD) B2C 目錄使用者設定檔隨附一組內建屬性，包括像是名字、姓氏、城市、郵遞區號和電話號碼等等。 您可以使用自己的應用程式資料來擴充使用者設定檔，而不需要外部資料存放區。 Microsoft Graph 也支援大部分可與 Azure AD B2C 使用者設定檔搭配使用的屬性。 本文描述支援的 Azure AD B2C 使用者設定檔屬性。 同時也會說明 Microsoft Graph 不支援的屬性，以及不應該搭配 Azure AD B2C 使用的 Microsoft Graph 屬性。

> [!IMPORTANT]
> 您不應該使用內建或擴充屬性來儲存機密的個人資料，例如帳號登入資訊、政府身份證編號、持卡人資料、財務帳戶資料、醫療保健資訊或機密背景資訊。

您也可以與外部系統整合。 例如，您可以使用 Azure AD B2C 進行驗證，但委派給外部客戶關係管理 (CRM) 或客戶忠誠度資料庫，作為客戶資料的可信賴來源。 如需詳細資訊，請參閱[遠端設定檔](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)解決方案。

下表列出 Azure AD B2C 目錄使用者設定檔所支援的[使用者資源類型](https://docs.microsoft.com/graph/api/resources/user)屬性， 並提供每個屬性的下列資訊：

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
|alternativeSecurityId ([身分識別](manage-user-accounts-graph-api.md#identities-property))|String|來自外部識別提供者的單一使用者身分識別。|否|否|輸入，保留，輸出|
|alternativeSecurityIds ([身分識別](manage-user-accounts-graph-api.md#identities-property))|替代的 securityId 集合|來自外部識別提供者的使用者身分識別集合。|否|否|保留，輸出|
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
|immutableId     |String|識別碼，通常用於從內部部署 Active Directory 遷移的使用者。|否|否|保留，輸出|
|legalAgeGroupClassification|String|法定年齡群組分類。 唯讀並以 ageGroup 和 consentProvidedForMinor 屬性為計算依據。 允許的值：Null、minorWithOutParentalConsent、minorWithParentalConsent、minorNoParentalConsentRequired、notAdult 和成人。|是|否|保留，輸出|
|legalCountry<sup>1</sup>  |String|適用於法律用途的國家/地區。|否|否|保留，輸出|
|mail            |String|使用者的 SMTP 位址，例如 "bob@contoso.com"。 唯讀。|否|否|保留，輸出|
|mailNickName    |String|使用者的郵件別名。 長度上限為 64。|否|否|保留，輸出|
|行動裝置 (mobilePhone) |String|使用者的主要行動電話號碼。 長度上限為 64。|是|否|保留，輸出|
|netId           |String|網狀 ID。|否|否|保留，輸出|
|objectId        |String|全域唯一識別碼 (GUID)，這是使用者的唯一識別碼。 範例：12345678-9abc-def0-1234-56789abcde。 唯讀、不可變。|唯讀|是|輸入，保留，輸出|
|otherMails      |字串集合|使用者的其他電子郵件地址清單。 範例：["bob@contoso.com", "Robert@fabrikam.com"]。|是 (其他電子郵件)|否|保留，輸出|
|密碼        |String|使用者建立期間的本機帳戶密碼。|否|否|已保存|
|passwordPolicies     |String|密碼原則。 是由不同原則名稱所組成的字串，並以逗號分隔。 例如 "DisablePasswordExpiration, DisableStrongPassword"。|否|否|保留，輸出|
|physicalDeliveryOfficeName (officeLocation)|String|使用者的公司所在位置。 長度上限為 128。|是|否|保留，輸出|
|postalCode      |String|使用者郵寄地址的郵遞區號。 郵遞區號適用於使用者的國家/地區。 在美國地區，此屬性包含郵遞區號。 長度上限為 40。|是|否|保留，輸出|
|preferredLanguage    |String|使用者的慣用語言。 應遵循 ISO 639-1 條款。 例如："en-US"。|否|否|保留，輸出|
|refreshTokensValidFromDateTime|Datetime|在此時間之前發出的任何重新整理權杖都無效，而且當使用不正確重新整理權杖來取得新的存取權杖時，應用程式將會收到錯誤。 如果發生這種情況，應用程式需要向授權端點提出要求，以取得新的重新整理權杖。 唯讀。|否|否|輸出|
|signInNames ([身分識別](manage-user-accounts-graph-api.md#identities-property)) |String|目錄中任何類型之本機帳戶使用者的唯一登入名稱。 使用此功能來取得具有登入值的使用者，而不需要指定本機帳戶類型。|否|否|輸入|
|signInNames.userName ([身分識別](manage-user-accounts-graph-api.md#identities-property)) |String|目錄中本機帳戶使用者的唯一使用者名稱。 使用此功能來建立或取得具有特定登入使用者名稱的使用者。 在修補作業期間單獨在 PersistedClaims 中指定此項，將會移除其他類型的 signInNames。 如果要新增新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入，保留，輸出|
|signInNames.phoneNumber ([身分識別](manage-user-accounts-graph-api.md#identities-property)) |String|目錄中本機帳戶使用者的唯一電話號碼。 使用此功能來建立或取得具有特定登入電話號碼的使用者。 在修補作業期間單獨在 PersistedClaims 中指定此項，將會移除其他類型的 signInNames。 如果要新增新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入，保留，輸出|
|signInNames.emailAddress ([身分識別](manage-user-accounts-graph-api.md#identities-property))|String|目錄中本機帳戶使用者的唯一電子郵件地址。 使用此功能來建立或取得具有特定登入電子郵件地址的使用者。 在修補作業期間單獨在 PersistedClaims 中指定此項，將會移除其他類型的 signInNames。 如果要新增新的 signInNames 類型，您也需要保存現有的 signInNames。|否|否|輸入，保留，輸出|
|state           |String|使用者地址的州或省。 長度上限為 128。|是|是|保留，輸出|
|streetAddress   |String|使用者公司所在位置的街道地址。 長度上限為 1024。|是|是|保留，輸出|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|用於多重要素驗證的使用者次要電話號碼。|是|否|保留，輸出|
|strongAuthenticationEmailAddress<sup>1</sup>|String|使用者的 SMTP 位址。 範例："bob@contoso.com" 此屬性用於以使用者名稱原則登入，以儲存使用者的電子郵件地址。 然後此電子郵件地址會在密碼重設流程中使用。|是|否|保留，輸出|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|用於多重要素驗證的使用者主要電話號碼。|是|否|保留，輸出|
|surname         |String|使用者的姓氏。 長度上限為 64。|是|是|保留，輸出|
|telephoneNumber (businessPhones 的第一個項目)|String|使用者公司所在位置的主要電話號碼。|是|否|保留，輸出|
|userPrincipalName    |String|使用者的使用者主體名稱 (UPN)。 UPN 是依據網際網路標準 RFC 822 的使用者網際網路樣式登入名稱。 網域必須存在於租用戶的已驗證網域集合內。 建立帳戶時需要此屬性。 不可變。|否|否|輸入，保留，輸出|
|usageLocation   |String|根據法律的要求，所有要指派以授權的使用者皆須指定，才能查看該國家/地區的服務供應狀況。 不可為 Null。 兩個字母的國家/區域代碼 (ISO 標準 3166)。 範例："US"、"JP" 和 "GB"。|是|否|保留，輸出|
|userType        |String|字串值可用來在目錄中分類使用者類型。 值必須是「成員」。 唯讀。|唯讀|否|保留，輸出|
|userState (externalUserState)<sup>2</sup>|String|僅供 Azure AD B2B 帳戶使用，指出邀請狀態為待接受或已接受。|否|否|保留，輸出|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|Datetime|顯示 UserState 屬性的最新變更時間戳記。|否|否|保留，輸出|
|<sup>1 </sup>不受 Microsoft Graph 支援<br><sup>2 </sup>不應搭配 Azure AD B2C 一起使用||||||


## <a name="extension-attributes"></a>擴充屬性

您通常需要建立自己的屬性，如下列情況所示：

- 客戶對向的應用程式需要保有 **LoyaltyNumber** 之類的屬性。
- 識別提供者擁有必須儲存的唯一使用者識別碼，例如 **uniqueUserGUID**。
- 自訂使用者旅程圖需要保有使用者的狀態，例如 **migrationStatus**。

Azure AD B2C 會擴充每個使用者帳戶所儲存的屬性組合。 擴充屬性會[擴充目錄中的使用者物件結構描述](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)。 擴充屬性只能在應用程式物件上註冊，即使其可能包含使用者的資料也一樣。 擴充屬性會附加至名為 b2c-extensions-app 的應用程式。 請勿修改此應用程式，因為 Azure AD B2C 會使用此應用程式來儲存使用者資料。 您可以在 Azure Active Directory 應用程式註冊下找到此應用程式。

> [!NOTE]
> - 任何的使用者帳戶中最多可以寫入 100 個擴充屬性。
> - 如果 b2c-extensions-app 應用程式已刪除，則系統會從所有使用者連同其包含的任何資料中移除這些擴充屬性。
> - 如果應用程式刪除某個擴充屬性，系統就會從所有使用者帳戶移除該擴充屬性，並將值刪除。
> - 擴充屬性的基礎名稱會以 "Extension_" + 應用程式識別碼 + "_" + 屬性名稱的格式產生。 例如，如果您建立擴充屬性 LoyaltyNumber，而 b2c-extensions-app 應用程式識別碼為 831374b3-bd50-41bf-aa54-263ec9e050fc，則基礎擴充屬性名稱會是： extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber。 當您執行圖形 API 查詢來建立或更新使用者帳戶時，您會使用基礎名稱。

在結構描述擴充中定義屬性時，支援下列資料類型：

|屬性類型 |備註  |
|--------------|---------|
|Boolean    | 可能的值：**True** 或 **False**。 |
|Datetime   | 必須以 ISO 8601 格式指定。 將以 UTC 格式儲存。   |
|整數    | 32 位元值。               |
|String     | 最多 256 個字元。     |

## <a name="next-steps"></a>後續步驟
深入了解關於擴充屬性：
- [結構描述延伸模組](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [定義使用者流程的自訂屬性](user-flow-custom-attributes.md)
- [定義自訂原則的自訂屬性](custom-policy-custom-attributes.md)
