---
title: Azure 活動目錄 B2C 中的使用者設定檔屬性
description: 瞭解 Azure AD B2C 目錄使用者設定檔支援的使用者資源類型屬性。 瞭解內置屬性、擴展以及屬性如何映射到 Microsoft 圖形。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057298"
---
# <a name="user-profile-attributes"></a>使用者設定檔屬性

Azure 活動目錄 （Azure AD） B2C 目錄使用者設定檔附帶一組內置屬性，如給定名稱、姓氏、城市、郵遞區號和電話號碼。 您可以使用自己的應用程式資料擴展使用者設定檔，而無需外部資料存儲。 大多數可用於 Azure AD B2C 使用者設定檔的屬性也受 Microsoft Graph 的支援。 本文介紹了支援的 Azure AD B2C 使用者設定檔屬性。 它還注意到 Microsoft 圖形不支援的屬性，以及不應與 Azure AD B2C 一起使用的 Microsoft 圖形屬性。

> [!IMPORTANT]
> 您不應使用內置或擴充屬性來存儲敏感的個人資料，例如帳戶憑據、政府標識號、持卡人資料、財務帳號資料、醫療保健資訊或敏感背景資訊。

您還可以與外部系統集成。 例如，可以使用 Azure AD B2C 進行身份驗證，但委託給外部客戶關係管理 （CRM） 或客戶忠誠度資料庫作為客戶資料來源的權威來源。 有關詳細資訊，請參閱[遠端設定檔](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile)解決方案。

下表列出了 Azure AD B2C 目錄使用者設定檔支援[的使用者資源類型](https://docs.microsoft.com/graph/api/resources/user)屬性。 它提供有關每個屬性的以下資訊：

- Azure AD B2C 使用的屬性名稱（後跟括弧中的 Microsoft 圖形名稱（如果不同）
- 屬性資料類型
- 屬性描述
- 如果屬性在 Azure 門戶中可用
- 如果屬性可以在使用者流中使用
- 如果屬性可以在自訂策略 Azure AD[技術設定檔](active-directory-technical-profile.md)中使用，並在哪個部分（&lt;輸入聲明&gt;、&lt;輸出聲明&gt;或&lt;持久聲明&gt;）中使用。

|名稱     |類型     |描述|Azure 入口網站|使用者流程|自訂原則|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|使用者帳戶是啟用還是禁用：如果啟用了該帳戶，**為 true，** 否則**為 false**。|是|否|持久，輸出|
|年齡群組        |String|使用者的年齡組。 可能的值：空、未定義、次要、成人、非成人。|是|否|持久，輸出|
|替代安全Id （[標識](manage-user-accounts-graph-api.md#identities-property)）|String|來自外部標識提供程式的單個使用者標識。|否|否|輸入、持久、輸出|
|替代安全 Id （[標識](manage-user-accounts-graph-api.md#identities-property)）|替代安全 Id 集合|來自外部標識提供程式的使用者標識的集合。|否|否|持久，輸出|
|city            |String|使用者所在的縣市。 最大長度 128。|是|是|持久，輸出|
|同意為次要提供|String|是否為未成年人提供了同意。 允許的值：null、授予、拒絕或不需要。|是|否|持久，輸出|
|country         |String|使用者所在的國家/地區。 示例："美國"或"英國"。 最大長度 128。|是|是|持久，輸出|
|createdDateTime|Datetime|創建使用者物件的日期。 唯讀。|否|否|持久，輸出|
|creationType    |String|如果使用者帳戶是作為 Azure 活動目錄 B2C 租戶的本地帳戶創建的，則該值為本地帳戶或名稱"共存"。 唯讀。|否|否|持久，輸出|
|dateOfBirth     |Date|生日。|否|否|持久，輸出|
|department      |String|使用者工作的部門名稱。 最大長度 64。|是|否|持久，輸出|
|displayName     |String|使用者的顯示名稱。 最大長度 256。|是|是|持久，輸出|
|傳真呼叫<sup>1</sup>|String|使用者的商務傳真機電話號碼。|是|否|持久，輸出|
|givenName       |String|使用者的名字。 最大長度 64。|是|是|持久，輸出|
|jobTitle        |String|使用者的職稱。 最大長度 128。|是|是|持久，輸出|
|immutableId     |String|通常用於從本地活動目錄遷移的使用者的識別碼。|否|否|持久，輸出|
|法律年齡組分類|String|法定年齡組分類。 唯讀，並根據年齡組和同意為次要屬性計算。 允許的值：空、未成年人、父母同意、未成年人家長同意、未成年人無父母同意，非成人和成人。|是|否|持久，輸出|
|法律國家<sup>1</sup>  |String|國家/地區用於法律目的。|否|否|持久，輸出|
|mail            |String|使用者的 SMTP 位址，例如""。bob@contoso.com 唯讀。|否|否|持久，輸出|
|mailNickName    |String|使用者的郵件別名。 最大長度 64。|否|否|持久，輸出|
|手機（手機） |String|使用者的主要行動電話號碼。 最大長度 64。|是|否|持久，輸出|
|netId           |String|淨 ID。|否|否|持久，輸出|
|objectId        |String|一個全域唯一識別碼 （GUID），它是使用者的唯一識別碼。 示例：12345678-9abc-def0-1234-56789abcde。 唯讀，不可改變。|唯讀|是|輸入、持久、輸出|
|otherMails      |字串集合|使用者其他電子郵件地址的清單。 示例：["，"""*。bob@contoso.com Robert@fabrikam.com|是（備用電子郵件）|否|持久，輸出|
|密碼        |String|使用者創建期間本地帳戶的密碼。|否|否|已保存|
|passwordPolicies     |String|密碼原則。 它是一個由用逗號分隔的不同策略名稱組成的字串。 即"禁用密碼過期，禁用強式密碼"。|否|否|持久，輸出|
|物理交付辦公室名稱（辦公地點）|String|使用者工作地點的辦公室位置。 最大長度 128。|是|否|持久，輸出|
|postalCode      |String|使用者郵寄地址的郵遞區號。 具體的郵遞區號依使用者的國家/地區而定。 在美國，此屬性包含了郵遞區號 (ZIP Code)。 最大長度 40。|是|否|持久，輸出|
|preferredLanguage    |String|使用者慣用的語言。 應遵循 ISO 639-1 規範。 示例："en-US"。|否|否|持久，輸出|
|刷新權杖從日期時間有效|Datetime|在此時間之前發出的任何刷新權杖都無效，並且應用程式在使用無效刷新權杖獲取新的訪問權杖時將收到錯誤。 如果發生這種情況，應用程式將需要通過向授權終結點發出請求來獲取新的刷新權杖。 唯讀。|否|否|輸出|
|符號名稱 （[標識](manage-user-accounts-graph-api.md#identities-property)） |String|目錄中任何類型的本地帳戶使用者的唯一登錄名。 使用此選項可獲取具有登錄值的使用者，而不指定本地帳戶類型。|否|否|輸入|
|符號名稱.使用者名 （[標識](manage-user-accounts-graph-api.md#identities-property)） |String|目錄中本地帳戶使用者的唯一使用者名。 使用此選項可以創建或使用特定登錄使用者名的使用者。 在修補程式操作期間僅在持久聲明中指定此名稱將刪除其他類型的符號 InNames。 如果要添加新類型的符號 InNames，還需要保留現有符號 InNames。|否|否|輸入、持久、輸出|
|符號名稱.電話號碼 （[標識](manage-user-accounts-graph-api.md#identities-property)） |String|目錄中本地帳戶使用者的唯一電話號碼。 使用此選項可以創建或獲取具有特定登錄電話號碼的使用者。 在修補程式操作期間僅在持久聲明中指定此名稱將刪除其他類型的符號 InNames。 如果要添加新類型的符號 InNames，還需要保留現有符號 InNames。|否|否|輸入、持久、輸出|
|符號名稱.電子郵件地址 （[身份](manage-user-accounts-graph-api.md#identities-property)）|String|目錄中本地帳戶使用者的唯一電子郵件地址。 使用此選項可以創建或使用特定登錄電子郵件地址的使用者。 在修補程式操作期間僅在持久聲明中指定此名稱將刪除其他類型的符號 InNames。 如果要添加新類型的符號 InNames，還需要保留現有符號 InNames。|否|否|輸入、持久、輸出|
|state           |String|使用者地址所屬的州/省。 最大長度 128。|是|是|持久，輸出|
|streetAddress   |String|使用者工作地點的街道地址。 最大長度 1024。|是|是|持久，輸出|
|增強式驗證替代電話編號<sup>1</sup>|String|使用者的第二個電話號碼，用於多重要素驗證。|是|否|持久，輸出|
|增強式驗證電子郵件地址<sup>1</sup>|String|使用者的 SMTP 位址。 示例："bob@contoso.com此屬性用於使用使用者名策略登錄，以存儲使用者電子郵件地址。 然後，在密碼重設流中使用的電子郵件地址。|是|否|持久，輸出|
|強<sup>身份驗證電話</sup>1|String|使用者的主要電話號碼，用於多重要素驗證。|是|否|持久，輸出|
|surname         |String|使用者的姓氏。 最大長度 64。|是|是|持久，輸出|
|電話號碼（商務電話首次輸入）|String|使用者工作地點的主要電話號碼。|是|否|持久，輸出|
|userPrincipalName    |String|使用者的使用者主要名稱 (UPN)。 UPN 是使用者的網際網路型態登入名稱 (根據網際網路標準 RFC 822)。 域必須存在於租戶的已驗證域集合中。 創建帳戶時需要此屬性。 不可變。|否|否|輸入、持久、輸出|
|usageLocation   |String|基於法律要求而必須檢查服務在國家或地區中的可用性，將獲派授權的使用者需要此項目。 不可為 Null。 雙字母的國碼 (ISO 標準 3166)。 示例："美國"、"JP"和"GB"。|是|否|持久，輸出|
|userType        |String|可用於對目錄中的使用者類型進行分類的字串值。 值必須為"成員"。 唯讀。|唯讀|否|持久，輸出|
|使用者狀態（外部使用者狀態）<sup>2</sup>|String|僅對於 Azure AD B2B 帳戶，指示邀請是"待定接受"還是"已接受"。|否|否|持久，輸出|
|使用者狀態更改 On（外部使用者狀態更改日期時間）<sup>2</sup>|Datetime|顯示使用者狀態屬性的最新更改的時間戳記。|否|否|持久，輸出|
|<sup>1</sup>微軟圖形不支援<br><sup>2</sup>不應與 Azure AD B2C 一起使用||||||


## <a name="extension-attributes"></a>擴充屬性

您通常需要創建自己的屬性，如以下情況：

- 面向客戶的應用程式需要保留像 **"忠誠號**"這樣的屬性。
- 識別提供者擁有必須儲存的唯一使用者識別碼，例如 **uniqueUserGUID**。
- 自訂使用者旅程需要保留使用者的狀態，如**遷移狀態**。

Azure AD B2C 會擴充每個使用者帳戶所儲存的屬性組合。 擴充屬性[擴展](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)目錄中使用者物件的架構。 擴充屬性只能在應用程式物件上註冊，即使它們可能包含使用者的資料。 擴充屬性附加到稱為 b2c 擴展應用程式的應用程式。 不要修改此應用程式，因為它被 Azure AD B2C 用於存儲使用者資料。 您可以在 Azure 活動目錄應用註冊下找到此應用程式。

> [!NOTE]
> - 最多可以將 100 個擴充屬性寫入任何使用者帳戶。
> - 如果刪除了 b2c 擴展應用程式，則這些擴充屬性將連同包含的任何資料從所有使用者中刪除。
> - 如果應用程式刪除了擴充屬性，它將從所有使用者帳戶中刪除，並且刪除這些值。
> - 擴充屬性的基礎名稱以"Extension_" + 應用程式 ID = "*" = 屬性名稱的形式生成。 例如，如果創建擴充屬性"忠誠編號"，並且 b2c 擴展應用程式應用程式 ID 為 831374b3-bd50-41bf-aa54-263ec9e050fc，則基礎擴充屬性名稱將為：extension_831374b3bd5041bfaa54263ec9e050fc_忠誠度號碼。 在運行圖形 API 查詢時使用基礎名稱來創建或更新使用者帳戶。

在架構擴展中定義屬性時，支援以下資料類型：

|屬性類型 |備註  |
|--------------|---------|
|Boolean    | 可能的值：**真**或**假**。 |
|Datetime   | 必須指定採用 ISO 8601 格式。 會儲存在 UTC 中。   |
|整數     | 32 位元值。               |
|String     | 最多 256 個字元。     |

## <a name="next-steps"></a>後續步驟
瞭解有關擴充屬性的更多詳細資訊：
- [結構描述延伸模組](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [使用使用者流定義自訂屬性](user-flow-custom-attributes.md)
- [使用自訂策略定義自訂屬性](custom-policy-custom-attributes.md)
