---
title: 開發 SCIM 終結點，用於從 Azure AD 預配到應用
description: 跨域身份管理系統 （SCIM） 可標準化自動使用者預配。 瞭解如何開發 SCIM 終結點、將 SCIM API 與 Azure 活動目錄集成，並開始將使用者和組自動預配到雲應用程式中。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297670"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>使用 Azure 活動目錄（Azure AD）構建 SCIM 終結點並配置使用者預配

作為應用程式開發人員，可以使用跨域標識管理系統 （SCIM） 使用者管理 API 在應用程式和 Azure AD 之間自動預配使用者和組。 本文介紹如何構建 SCIM 終結點並與 Azure AD 預配服務集成。 SCIM 規範提供了用於預配的通用使用者架構。 當與 SAML 或 OpenID 連接等聯合標準結合使用時，SCIM 為管理員提供了基於標準的端到端訪問管理解決方案。

SCIM 是兩個終結點的標準化定義：/Users 終結點和 /組終結點。 它使用常見的 REST 謂詞創建、更新和刪除物件，以及為組名稱、使用者名、名字、姓氏和電子郵件等常見屬性創建、更新和刪除的預先定義的架構。 提供 SCIM 2.0 REST API 的應用可以減少或消除使用專有使用者管理 API 的痛苦。 例如，任何符合 SCIM 用戶端都知道如何將 JSON 物件的 HTTP POST 製作到 /Users 終結點以創建新的使用者條目。 符合 SCIM 標準的應用無需針對相同的基本操作需要稍微不同的 API，而是可以立即利用預先存在的用戶端、工具和代碼。 

![從 Azure AD 預配到具有 SCIM 的應用](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0（RFC [7642、7643、7644）](https://tools.ietf.org/html/rfc7642)中[7643](https://tools.ietf.org/html/rfc7643)定義的用於管理的標準使用者[7644](https://tools.ietf.org/html/rfc7644)物件架構和休息 API 允許標識提供程式和應用程式更輕鬆地相互集成。 構建 SCIM 終結點的應用程式開發人員可以與任何符合 SCIM 的用戶端集成，而無需執行自訂工作。

自動預配到應用程式需要構建 SCIM 終結點並將其與 Azure AD SCIM 用戶端集成。 執行以下步驟，開始將使用者和組預配到應用程式中。 
    
  * **[第 1 步：設計使用者和組架構。](#step-1-design-your-user-and-group-schema)** 確定應用程式需要的物件和屬性，並確定它們如何映射到 Azure AD SCIM 實現支援的使用者和組架構。

  * **[第 2 步：瞭解 Azure AD SCIM 實現。](#step-2-understand-the-azure-ad-scim-implementation)** 瞭解 Azure AD SCIM 用戶端是如何實現的，並為您的 SCIM 協定請求處理和回應建模。

  * **[步驟 3：構建 SCIM 終結點。](#step-3-build-a-scim-endpoint)** 終結點必須與 SCIM 2.0 相容才能與 Azure AD 預配服務集成。 作為一個選項，您可以使用 Microsoft 通用語言基礎結構 （CLI） 庫和代碼示例來構建終結點。 這些示例僅供參考和測試;我們建議不要對生產應用進行編碼，以依賴它們。

  * **[步驟 4：將 SCIM 終結點與 Azure AD SCIM 用戶端集成。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 如果您的組織正在使用實現 Azure AD 支援的 SCIM 2.0 設定檔的協力廠商應用程式，則可以立即開始自動預配和取消預配使用者和組。

  * **[步驟 5：將應用程式發佈到 Azure AD 應用程式庫。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 使客戶能夠輕鬆發現您的應用程式並輕鬆配置預配。 

![將 SCIM 終結點與 Azure AD 集成的步驟](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>第 1 步：設計使用者和組架構

每個應用程式都需要不同的屬性來創建使用者或組。 通過標識應用程式所需的物件（使用者、組）和屬性（名稱、經理、職務等）來啟動集成。 SCIM 標準定義了用於管理使用者和組的架構。 核心使用者架構只需要三個屬性 **：id（** 服務提供者定義的識別碼）、外部**Id（** 用戶端定義的識別碼）和**元**（由服務提供者維護的唯讀中繼資料）。 所有其他屬性都是可選的。 除了核心使用者架構外，SCIM 標準還定義了企業使用者擴展和用於擴展使用者架構以滿足應用程式需求的模型。 例如，如果應用程式需要使用者的經理，則可以使用企業使用者架構收集使用者的經理和核心架構來收集使用者的電子郵件。 要設計架構，請按照以下步驟操作：
  1. 列出應用程式所需的屬性。 將您的要求分解為身份驗證所需的屬性（例如登錄名和電子郵件）、管理使用者生命週期所需的屬性（例如狀態/活動）以及特定應用程式工作所需的其他屬性（例如管理器、標記）會很有説明。
  2. 檢查這些屬性是否已在核心使用者架構或企業使用者架構中定義。 如果需要並且不在核心或企業使用者架構中涵蓋任何屬性，則需要定義使用者架構的擴展，該擴展涵蓋所需的屬性。 在下面的示例中，我們向使用者添加了一個擴展，以允許在使用者上預配"標記"。 最好從核心和企業使用者架構開始，然後擴展到其他自訂架構。  
  3. 將 SCIM 屬性對應到 Azure AD 中的使用者屬性。 如果在 SCIM 終結點中定義的一個屬性在 Azure AD 使用者架構上沒有明確的對應項，則大多數租戶上很可能資料根本不存儲在使用者物件上。 請考慮此屬性是否可以為創建使用者而可選。 如果屬性對於應用程式正常工作至關重要，請指導租戶管理員擴展其架構或使用擴充屬性，如下所示的"標記"屬性。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表 1：概述所需的屬性 
| 第 1 步：確定應用所需的屬性| 第 2 步：將應用要求映射到 SCIM 標準| 步驟 3：將 SCIM 屬性對應到 Azure AD 屬性|
|--|--|--|
|登錄名|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|名稱.姓氏|lastName|
|工作郵件|電子郵件[鍵入 eq"工作"]值|Mail|
|manager|manager|manager|
|tag|urn：ietf：參數：scim：架構：擴展：2.0：自訂擴展：標記|extensionAttribute1|
|status|作用中|已虛刪除（計算值未存儲在使用者上）|

上面定義的架構將使用下面的 Json 負載表示。 請注意，除了應用程式所需的屬性外，JSON 表示形式還包括所需的"id"、"外部 Id"和"元"屬性。

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>表 2：預設使用者屬性對應
然後，可以使用下表瞭解應用程式所需的屬性如何映射到 Azure AD 和 SCIM RFC 中的屬性。 您可以[自訂](customize-application-attributes.md)屬性在 Azure AD 和 SCIM 終結點之間映射的方式。 請注意，您不需要同時支援使用者和組或下面顯示的所有屬性。 它們是 Azure AD 中屬性通常映射到 SCIM 協定中屬性的參考。 

| Azure Active Directory 使用者 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |作用中 |
|department|urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：部門|
| displayName |displayName |
|employeeId|urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：員工編號|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn：ietf：參數：scim：架構：擴展：企業：2.0：使用者：經理 |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>表 3：預設組屬性對應

| Azure Active Directory 群組 | urn：ietf：參數：scim：schema：核心：2.0：組 |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC 中定義了多個終結點。 您可以開始使用 /User 終結點，然後從那裡展開。 使用自訂屬性或架構頻繁更改時，/Schema 終結點非常有用。 它使用戶端能夠自動檢索最新的架構。 /Bulk 終結點在支援組時特別有用。 下表描述了 SCIM 標準中定義的各種終結點。 使用自訂屬性或架構頻繁更改時，/Schema 終結點非常有用。 它使用戶端能夠自動檢索最新的架構。 /Bulk 終結點在支援組時特別有用。 下表描述了 SCIM 標準中定義的各種終結點。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表 4：確定要開發的結束點
|端點|DESCRIPTION|
|--|--|
|/User|對使用者物件執行 CRUD 操作。|
|/Group|對組物件執行 CRUD 操作。|
|/服務提供者配置|提供有關支援的 SCIM 標準功能的詳細資訊，例如支援的資源和身份驗證方法。|
|/資源類型|指定有關每個資源的中繼資料|
|/架構|每個用戶端和服務提供者支援的屬性集可能有所不同。 一個服務提供者可能包括"名稱"、"標題"和"電子郵件"，而另一個服務提供者則使用"名稱"、"標題"和"電話號碼"。 架構終結點允許發現支援的屬性。|
|/體積|批量操作允許您在單個操作中對大量資源物件執行操作（例如，更新大型組的成員身份）。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>第 2 步：瞭解 Azure AD SCIM 實現
> [!IMPORTANT]
> Azure AD SCIM 實作行為的上次更新日期為 2018 年 12 月 18 日。 如需已變更內容的資訊，請參閱 [Azure AD 使用者佈建服務的 SCIM 2.0 通訊協定合規性](application-provisioning-config-problem-scim-compatibility.md)。

如果要構建支援 SCIM 2.0 使用者管理 API 的應用程式，本節將詳細介紹如何實現 Azure AD SCIM 用戶端。 它還演示如何對 SCIM 協定請求處理和回應建模。 實現 SCIM 終結點後，可以通過按照上一節中描述的過程對其進行測試。

在[SCIM 2.0 協定規範](http://www.simplecloud.info/#Specification)中，您的應用程式必須滿足以下要求：

* 支援根據 SCIM 協定 的第[3.3](https://tools.ietf.org/html/rfc7644#section-3.3)節創建使用者，也可以選擇組。  
* 支援根據[SCIM 協定的第 3.5.2 節](https://tools.ietf.org/html/rfc7644#section-3.5.2)修改具有 PATCH 請求的使用者或組。  
* 支援檢索前面創建的使用者或組的已知資源，如[SCIM 協定的第 3.4.1 節](https://tools.ietf.org/html/rfc7644#section-3.4.1)。  
* 支援查詢使用者或組，如[SCIM 協定的第 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2)節。  `id`預設情況下，使用者由其`username``externalid`和 檢索和查詢，並且組由`displayName`查詢。  
* 根據 SCIM 協定的第 3.4.2 節，支援按 ID 和管理器查詢使用者。  
* 根據 SCIM 協定的第 3.4.2 節，支援按 ID 和成員查詢組。  
* 接受單個承載權杖，以便對應用程式進行 Azure AD 的身份驗證和授權。

在實現 SCIM 終結點時，請遵循以下一般準則，以確保與 Azure AD 相容：

* `id`是所有資源的必要屬性。 返回資源的每個回應應確保每個資源都具有此屬性，但成員為零除外`ListResponse`。
* 對查詢/篩選器請求的回應應始終為`ListResponse`。
* 組是可選的，但僅當 SCIM 實現支援 PATCH 請求時才受支援。
* 不必將整個資源包含在 PATCH 回應中。
* Microsoft Azure AD 僅使用以下運算子：  
    - `eq`
    - `and`
* 不需要對 SCIM 中的結構元素（尤其是 在 中`op`https://tools.ietf.org/html/rfc7644#section-3.5.2定義的 PATCH 操作值）進行區分大小寫的匹配。 Azure AD 將"op"的值表示為`Add``Replace`和`Remove`。
* Microsoft Azure AD 發出獲取隨機使用者和組的請求，以確保終結點和憑據有效。 它還作為[Azure 門戶](https://portal.azure.com)中的**測試連接**流的一部分完成。 
* 可以查詢資源的屬性應設置為[Azure 門戶](https://portal.azure.com)中的應用程式中的匹配屬性。 有關詳細資訊，請參閱[自訂使用者預配屬性對應](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>使用者預配和取消預配

下圖顯示了 Azure 活動目錄發送到 SCIM 服務以管理應用程式標識存儲中使用者的生命週期的消息。  

![顯示使用者預配和取消預配序列](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*使用者預配和取消預配序列*

### <a name="group-provisioning-and-deprovisioning"></a>組預配和取消預配

組預配和取消預配是可選的。 實現並啟用後，下圖將顯示 Azure AD 發送到 SCIM 服務以管理應用程式標識存儲中的組的生命週期的消息。  這些消息與有關使用者的消息有兩種不同：

* 檢索組的請求指定成員屬性將從回應請求提供的任何資源中排除。  
* 要求判斷參考屬性是否具有特定值，會是有關成員屬性的要求。  

![顯示組預配和取消預配序列](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*組預配和取消預配順序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 協定請求和回應
本節提供 Azure AD SCIM 用戶端發出的 SCIM 請求示例以及預期回應的示例。 為獲得最佳效果，應編寫應用代碼以以此格式處理這些請求，併發出預期的回應。

> [!IMPORTANT]
> 要瞭解 Azure AD 使用者預配服務如何以及何時發出下面描述的操作，請參閱[預配週期部分：預配的工作原理中的初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。 [How provisioning works](how-provisioning-works.md)

[使用者操作](#user-operations)
  - [創建使用者](#create-user)（[請求](#request) / [回應](#response)）
  - [獲取使用者](#get-user)（[請求](#request-1) / [回應](#response-1)）
  - [通過查詢獲取使用者](#get-user-by-query)（[請求](#request-2) / [回應](#response-2)）
  - [通過查詢獲取使用者 - 零結果](#get-user-by-query---zero-results)（[請求](#request-3)
/ [回應](#response-3)）
  - [更新使用者 [多值屬性]](#update-user-multi-valued-properties) （[請求](#request-4) /  [回應](#response-4)）
  - [更新使用者 [單值屬性]](#update-user-single-valued-properties) （[請求](#request-5)
/ [回應](#response-5)） 
  - [禁用使用者](#disable-user)（[請求](#request-14) / 
[回應](#response-14)）
  - [刪除使用者](#delete-user)（[請求](#request-6) / 
[回應](#response-6)）


[集團運營](#group-operations)
  - [創建組](#create-group)（[請求](#request-7) / [回應](#response-7)）
  - [獲取組](#get-group)（[請求](#request-8) / [回應](#response-8)）
  - [按顯示名稱獲取組](#get-group-by-displayname)（[請求](#request-9) / [回應](#response-9)）
  - [更新組 [非成員屬性]](#update-group-non-member-attributes) （[請求](#request-10)/
 [回應](#response-10)）
  - [更新組 [添加成員]](#update-group-add-members) （[請求](#request-11) /
[回應](#response-11)）
  - [更新組 [刪除成員]](#update-group-remove-members) （[請求](#request-12) /
[回應](#response-12)）
  - [刪除組](#delete-group)（[請求](#request-13) /
[回應](#response-13)）

### <a name="user-operations"></a>使用者操作

* 使用者可以按`userName`或`email[type eq "work"]`屬性查詢。  

#### <a name="create-user"></a>建立使用者

###### <a name="request"></a>要求

*POST /使用者*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>回應

*HTTP/1.1 201 已創建*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>獲取使用者

###### <a name="request"></a><a name="request-1"></a>請求
*GET /使用者/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>回應（找到使用者）
*HTTP/1。1 200 [確定]*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>要求
*GET /使用者/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>回應（找不到使用者。 請注意，詳細資訊不是必需的，僅狀態。

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>通過查詢獲取使用者

##### <a name="request"></a><a name="request-2"></a>請求

*GET /使用者？篩檢程式_使用者名 eq"Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>回應

*HTTP/1。1 200 [確定]*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>通過查詢獲取使用者 - 零結果

##### <a name="request"></a><a name="request-3"></a>請求

*GET /使用者？篩選器_使用者名 eq"不存在的使用者"*

##### <a name="response"></a><a name="response-3"></a>回應

*HTTP/1。1 200 [確定]*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>更新使用者 [多值屬性]

##### <a name="request"></a><a name="request-4"></a>請求

*PATCH /使用者/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>回應

*HTTP/1。1 200 [確定]*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>更新使用者 [單值屬性]

##### <a name="request"></a><a name="request-5"></a>請求

*PATCH /使用者/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>回應

*HTTP/1。1 200 [確定]*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>禁用使用者

##### <a name="request"></a><a name="request-14"></a>請求

*PATCH /使用者/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>回應

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>刪除使用者

##### <a name="request"></a><a name="request-6"></a>請求

*刪除 /使用者/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>回應

*HTTP/1.1 204 無內容*

### <a name="group-operations"></a>集團運營

* 應始終使用空成員清單創建組。
* 屬性可以查詢`displayName`組。
* 對組 PATCH 請求的更新應在回應中生成*HTTP 204 無內容*。 返回具有所有成員清單的正文是不可取的。
* 沒有必要支援返回組的所有成員。

#### <a name="create-group"></a>建立群組

##### <a name="request"></a><a name="request-7"></a>請求

*POST /組 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>回應

*HTTP/1.1 201 已創建*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>取得群組

##### <a name="request"></a><a name="request-8"></a>請求

*GET /組/40734ae655284ad3abcc？排除屬性_成員 HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>回應
*HTTP/1。1 200 [確定]*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>按顯示名稱獲取組

##### <a name="request"></a><a name="request-9"></a>請求
*GET /組？排除屬性_成員&篩選器*顯示名稱 eq "顯示名稱" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>回應

*HTTP/1。1 200 [確定]*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>更新組 [非成員屬性]

##### <a name="request"></a><a name="request-10"></a>請求

*PATCH /組/fa2ce2670934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>回應

*HTTP/1.1 204 無內容*

### <a name="update-group-add-members"></a>更新組 [添加成員]

##### <a name="request"></a><a name="request-11"></a>請求

*PATCH /組/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>回應

*HTTP/1.1 204 無內容*

#### <a name="update-group-remove-members"></a>更新組 [刪除成員]

##### <a name="request"></a><a name="request-12"></a>請求

*PATCH /組/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>回應

*HTTP/1.1 204 無內容*

#### <a name="delete-group"></a>刪除群組

##### <a name="request"></a><a name="request-13"></a>請求

*刪除 /組/cdb1ce1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>回應

*HTTP/1.1 204 無內容*

### <a name="security-requirements"></a>安全性需求
**TLS 協定版本**

唯一可接受的 TLS 協定版本是 TLS 1.2 和 TLS 1.3。 不允許其他版本的 TLS。 不允許使用 SSL 版本。 
- RSA 金鑰必須至少為 2，048 位。
- ECC 鍵必須至少為 256 位，使用經批准的橢圓曲線生成


**鍵長度**

所有服務都必須使用使用足夠長度的加密金鑰生成的 X.509 憑證，這意味著：

**密碼套房**

所有服務都必須配置為按照下面指定的確切順序使用以下密碼套件。 請注意，如果您只有 RSA 證書，則安裝 ECDSA 密碼套件沒有任何效果。 </br>

TLS 1.2 密碼套房最小酒吧：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>第 3 步：構建 SCIM 終結點

現在，您已經設計了架構並瞭解了 Azure AD SCIM 實現，您可以開始開發 SCIM 終結點。 您可以依賴 SCIM 社區發佈的許多開源 SCIM 庫，而不是從頭開始，完全獨立構建實現。

Azure AD 預配團隊發佈的開源 .NET Core[參考代碼](https://aka.ms/SCIMReferenceCode)是能夠快速啟動開發的資源之一。 生成 SCIM 終結點後，將需要對其進行測試。您可以使用作為參考代碼一部分提供的[郵遞員測試](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)的集合，或運行[上面](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)提供的示例請求/回應。  

   > [!Note]
   > 參考代碼旨在説明您開始構建 SCIM 終結點，並提供"AS IS"。 歡迎社區提供捐助，説明構建和維護代碼。

該解決方案由兩個專案組成，_即微軟.SCIM_和_微軟.SCIM.WebHostSample_。

_Microsoft.SCIM_專案是定義符合 SCIM 規範的 Web 服務的元件的庫。 它聲明介面_Microsoft.SCIM.IProvider，_ 請求被轉換為對提供程式方法的調用，這些方法將被程式設計為在標識存儲上運行。

![細分：轉換為對提供程式方法的調用的請求](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft.SCIM.WebHostSample_專案是基於_空_範本的視覺化工作室ASP.NET核心 Web 應用程式。 這允許將示例代碼部署為獨立代碼、託管在容器中或 Internet 資訊服務中。 它還實現了_Microsoft.SCIM.IProvider_介面，將類保留在記憶體中作為示例標識存儲。

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>構建自訂 SCIM 終結點

SCIM 服務必須具有 HTTP 位址和伺服器身份驗證憑證，根憑證授權單位是以下名稱之一：

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK 包括可在開發期間使用的 HTTPS 開發證書，該證書作為初次執行體驗的一部分安裝。 根據運行ASP.NET核心 Web 應用程式的方式，它將偵聽其他埠：

* 微軟.SCIM.網路主機示例：https://localhost:5001
* IIS 快遞：https://localhost:44359/

有關 ASP.NET 核心中的 HTTPS 的詳細資訊，請使用以下連結：[在 ASP.NET核心中強制實施 HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>處理端點驗證

來自 Azure Active Directory 的要求包括 OAuth 2.0 持有人權杖。 接收請求的任何服務都應將頒發者驗證為預期的 Azure 活動目錄租戶的 Azure 活動目錄。

在權杖中，頒發者由 iss 聲明標識，如`"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`。 在此示例中，聲明值的基本位址`https://sts.windows.net`， 將 Azure 活動目錄標識為頒發者， 而相對位址段_cbb1a5ac-f33b-45fa-f37db0fed422_， 是頒發權杖的 Azure 活動目錄租戶的唯一識別碼。

權杖的受眾將是庫中應用程式的應用程式範本 ID，在單個租戶中註冊的每個應用程式都可能會收到與 SCIM 請求相同的`iss`聲明。 庫中每個應用程式的應用程式範本 ID 各不相同，請與庫[ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com)應用程式有關的應用程式範本 ID 的問題聯繫。 所有自訂應用的應用程式範本 ID 為_8adf8e6e-67b2-4cf2-a259-e3dc5476c621_。

在示例代碼中，使用 Microsoft.AspNetCore.身份驗證.JwtBearer 包對請求進行身份驗證。 以下代碼強制強制使用 Azure Active Directory 為指定租戶發出的無記名權杖對對服務的任何終結點的請求進行身份驗證：

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

使用提供的[郵遞員測試](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)並使用本地主機執行本地調試也需要承載權杖。 示例代碼使用 ASP.NET 酷環境在開發階段更改身份驗證選項，並啟用使用自簽名權杖。

有關 ASP.NET 核心中的多個環境的詳細資訊，請使用以下連結：[在 ASP.NET核心中使用多個環境](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

以下代碼強制強制對服務的任何終結點的請求使用使用自訂金鑰簽名的承載權杖進行身份驗證：

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

向權杖控制器發送 GET 請求以獲取有效的無記名權杖，方法_GenerateJSONWebToken_負責創建一個權杖，匹配為開發配置的參數：

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>處理使用者預配和取消預配

***示例 1.查詢匹配使用者的服務***

Azure Active Directory 會查詢服務是否有 externalId 屬性值與 Azure AD 中使用者的 mailNickname 屬性值相符的使用者。 查詢表示為超文字傳輸協議 （HTTP） 請求，例如，其中 jyoung 是 Azure 活動目錄中使用者的郵件昵稱的示例。

>[!NOTE]
> 這只是一個示例。 並非所有使用者都將具有 mailNickname 屬性，並且使用者擁有的值在目錄中可能並非唯一。 此外，用於匹配的屬性（在本例中為外部 Id）在[Azure AD 屬性對應中](customize-application-attributes.md)可配置。

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

在示例代碼中，請求將轉換為對服務提供者的 QueryAsync 方法的調用。 以下是該方法的簽章： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

在依例查詢中，對於具有外部 Id 屬性給定值的使用者，傳遞給 QueryAsync 方法的參數的值為：

* parameters.AlternateFilters.Count: 1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***示例 2.預配使用者***

如果具有與使用者的郵件昵稱屬性值匹配的外部 Id 屬性值的使用者對 Web 服務的查詢的回應不返回任何使用者，則 Azure Active Directory 請求該服務預配與該使用者對應的使用者在 Azure 活動目錄中。  以下是這類要求的範例： 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

在示例代碼中，請求將轉換為對服務提供者的 CreateAsync 方法的調用。 以下是該方法的簽章： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

在預配使用者的請求中，資源參數的值是 Microsoft.SCIM.Core2EnterpriseUser 類的實例，該類在 Microsoft.SCIM.Schemas 庫中定義。  如果預配使用者的請求成功，則該方法的實現應返回 Microsoft.SCIM.Core2EnterpriseUser 類的實例，其中識別碼屬性的值設置為新預配的唯一識別碼使用者。  

***示例 3.查詢使用者的目前狀態*** 

為了更新已知存在於前端為 SCIM 之身分識別存放區中的使用者，Azure Active Directory 會以類似下方的要求向服務要求該使用者的目前狀態，來繼續執行： 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

在示例代碼中，請求將轉換為對服務提供程式的檢索 Async 方法的調用。 以下是該方法的簽章： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

在擷取使用者目前狀態之要求的範例中，提供作為參數引數值的物件具有的屬性值如下所示： 
  
* 識別碼："54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***示例 4.查詢要更新的引用屬性的值*** 

如果要更新引用屬性，則 Azure Active Directory 會查詢服務，以確定服務前面的標識存儲中的引用屬性的當前值是否已與 Azure Active 中該屬性的值匹配目錄。 對於使用者，以這種方式可查詢目前值的唯一屬性會是管理員屬性。 下面是一個請求的示例，用於確定使用者物件的管理器屬性當前是否具有特定值：在示例代碼中，請求將轉換為對服務提供者的 QueryAsync 方法的調用。 提供物件的屬性值作為參數引數的值，如下所示： 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

在這裡，索引 x 的值可以是 0，索引 y 的值可以是 1，或者 x 的值可以是 1，y 的值可以是 0，具體取決於篩選器查詢參數的運算式的順序。   

***示例 5.從 Azure AD 請求 SCIM 服務以更新使用者*** 

以下是由 Azure Active Directory 對 SCIM 服務發出要求來更新使用者的範例： 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

在示例代碼中，請求將轉換為對服務提供程式的 UpdateAsync 方法的調用。 以下是該方法的簽章： 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

在更新使用者之要求的範例中，提供作為修補程式引數值的物件具有這些屬性值： 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier："urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

***示例 6.取消預配使用者***

要從 SCIM 服務前面的標識存儲中取消預配使用者，Azure AD 會發送請求，例如：

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

在示例代碼中，請求將轉換為對服務提供程式的 DeleteAsync 方法的調用。 以下是該方法的簽章： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

作為資源識別碼參數的值提供的物件在取消預配使用者的請求示例中具有這些屬性值： 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>第 4 步：將 SCIM 終結點與 Azure AD SCIM 用戶端集成

Azure AD 可以配置為自動將分配的使用者和組預配到實現[SCIM 2.0 協定](https://tools.ietf.org/html/rfc7644)的特定設定檔的應用程式。 設定檔的細節記錄在[步驟 2：瞭解 Azure AD SCIM 實現](#step-2-understand-the-azure-ad-scim-implementation)。

洽詢應用程式提供者，或參閱應用程式提供者文件中的相關陳述，以了解是否符合這些需求。

> [!IMPORTANT]
> Azure AD SCIM 實現構建在 Azure AD 使用者預配服務之上，該服務旨在讓使用者在 Azure AD 和目標應用程式之間保持同步，並實現一組非常特定的標準操作。 瞭解 Azure AD SCIM 用戶端的行為非常重要。 有關詳細資訊，請參閱預配週期部分：[預配的工作原理](how-provisioning-works.md)中[的初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。

### <a name="getting-started"></a>開始使用

支援本文所述 SCIM 設定檔的應用程式，可使用 Azure AD 應用程式庫中的「不在資源庫內的應用程式」功能連線到 Azure Active Directory。 連線之後，Azure AD 會每隔 40 分鐘執行一次同步處理程序，此程序會為指派的使用者和群組查詢應用程式的 SCIM 端點，並根據指派詳細資料加以建立或修改。

**若要連接支援 SCIM 的應用程式：**

1. 登錄到 Azure[活動目錄門戶](https://aad.portal.azure.com)。 請注意，您可以通過註冊[開發人員程式](https://developer.microsoft.com/office/dev-program)獲得使用 P2 許可證的 Azure 活動目錄的免費試用版
2. 從左側窗格中選擇**企業應用程式**。 將顯示所有已配置應用的清單，包括從庫中添加的應用。
3. 選擇 **= 新應用程式** > **所有** > **非庫應用程式**。
4. 輸入應用程式的名稱，然後選擇 **"添加"** 以創建應用物件。 新應用將添加到企業應用程式清單中，並打開到其應用管理螢幕。

   ![螢幕截圖顯示 Azure AD 應用程式庫](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 應用程式庫*

5. 在應用管理螢幕中，選擇左側面板中的**預配**。
6. 在 [佈建模式]**** 功能表上，選取 [自動]****。

   ![示例：Azure 門戶中的應用預配頁](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *在 Azure 門戶中配置預配*

7. 在 [租用戶 URL]**** 欄位中，輸入應用程式 SCIM 端點的 URL。 範例： `https://api.contoso.com/scim/`
8. 如果 SCIM 端點需要來自非 Azure AD 簽發者的 OAuth 持有人權杖，那麼便將所需的 OAuth 持有人權杖複製到選擇性 [祕密權杖]**** 欄位。 如果此欄位留空，Azure AD 包含從 Azure AD 為每個請求發出的 OAuth 承載權杖。 應用程式若使用 Azure AD 作為識別提供者，便可以驗證此 Azure AD 簽發的權杖。 
   > [!NOTE]
   > ***不***建議將此欄位留空並依賴于 Azure AD 生成的權杖。 此選項主要用於測試目的。
9. 選擇 **"測試連接**"可嘗試 Azure 活動目錄連接到 SCIM 終結點。 如果嘗試失敗，將顯示錯誤資訊。  

    > [!NOTE]
    > [測試連線]**** 會查詢 SCIM 端點中是否有不存在的使用者，並使用隨機 GUID 作為 Azure AD 設定中選取的比對屬性。 預期的正確回應是 HTTP 200 OK 和空白的 SCIM ListResponse 訊息。

10. 如果嘗試連接到應用程式成功，請選擇 **"保存**"以保存管理員憑據。
11. 在 **"映射"** 部分中，有兩組可選[的屬性對應](customize-application-attributes.md)：一組用於使用者物件，一組為組物件。 選取其中一個以檢閱從 Azure Active Directory 同步處理至應用程式的屬性。 選取為 [比對]**** 屬性的屬性會用來比對應用程式中的使用者和群組以進行更新作業。 選取 [儲存]**** 認可任何變更。

    > [!NOTE]
    > 您可以選擇性地藉由停用「群組」對應以停用同步處理群組物件。

12. 在 [設定]**** 底下，[範圍]**** 欄位會定義要同步的使用者和群組。 選擇 **"僅同步僅分配的使用者和組**（推薦）"以僅同步在"**使用者和組**"選項卡中分配的使用者和組。
13. 配置完成後，將**預配狀態**設置為**On**。
14. 選擇 **"保存"** 以啟動 Azure AD 預配服務。
15. 如果僅同步分配的使用者和組（建議），請確保選擇 **"使用者和組**"選項卡並分配要同步的使用者或組。

初始週期開始後，您可以在左側面板中選擇**預配日誌**以監視進度，顯示應用上的預配服務完成的所有操作。 如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始週期執行的時間比以後同步長，只要服務在運行時，大約每 40 分鐘發生一次同步。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>步驟 5：將應用程式發佈到 Azure AD 應用程式庫

如果要構建將由多個租戶使用的應用程式，則可以使其在 Azure AD 應用程式庫中可用。 這將使組織能夠輕鬆發現應用程式並配置預配。 在 Azure AD 庫中發佈應用並使預配可供他人使用非常簡單。 請在 [這裡](../develop/howto-app-gallery-listing.md)查明步驟。 Microsoft 將與您合作，將您的應用程式集成到我們的庫中，測試您的終結點，併發布載入[文檔](../saas-apps/tutorial-list.md)供客戶使用。 

### <a name="gallery-onboarding-checklist"></a>畫廊入職檢查表
按照下面的檢查表操作，確保您的應用程式快速上載，並且客戶具有流暢的部署體驗。 資訊將在您入用時從您這裡收集。 
> [!div class="checklist"]
> * 支援[SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation)使用者和組終結點（只需要一個，但建議同時使用兩個終結點）
> * 每個租戶每秒至少支援 25 個請求（必需）
> * 建立工程和支援連絡人，指導客戶在畫廊後入職（必需）
> * 3 應用程式的非過期測試憑據（必需）
> * 支援 OAuth 授權代碼授予或長壽命權杖，如下所述（必需）
> * 建立工程和支援聯繫點，支援客戶在畫廊入職後（必需）
> * 支援使用單個 PATCH 更新多個組成員身份（推薦） 
> * 公開記錄 SCIM 終結點（推薦） 
> * [支援架構發現](https://tools.ietf.org/html/rfc7643#section-6)（推薦）


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>在應用程式庫中預配連接器的授權
SCIM 規範未定義特定于 SCIM 的身份驗證和授權方案。 它依賴于現有行業標準的使用。 Azure AD 預配用戶端支援函式庫中應用程式的兩種授權方法。 

|授權方法|優點|缺點|支援|
|--|--|--|--|
|使用者名和密碼（Azure AD 不推薦或不支援）|易於實施|不安全 -[你的Pa$$word並不重要](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|支援函式庫應用的逐案使用。 不支援非庫應用。|
|長壽命無記名權杖|長壽命權杖不需要使用者存在。 在設置預配時，管理員可以輕鬆使用它們。|如果不使用不安全的方法（如電子郵件），則很難與管理員共用長期存在的權杖。 |支援函式庫和非庫應用。 |
|OAuth 授權代碼授予|訪問權杖的壽命比密碼短得多，並且具有長期承載權杖所沒有的自動刷新機制。  在初始授權期間，必須存在真正的使用者，從而增加一定程度的責任。 |要求使用者在場。 如果使用者離開組織，權杖將無效，需要再次完成授權。|支援函式庫應用。 正在支援非庫應用。|
|OAuth 用戶端憑據授予|訪問權杖的壽命比密碼短得多，並且具有長期承載權杖所沒有的自動刷新機制。 授權代碼授予和用戶端憑據授予都會創建相同類型的訪問權杖，因此在這些方法之間移動對 API 是透明的。  預配可以完全自動化，無需使用者交互即可靜默請求新權杖。 ||庫和非庫應用不受支援。 支援在我們的積壓中。|

[!NOTE] 不建議將權杖欄位留空在 Azure AD 預配配置配置自訂應用 UI 中。 生成的權杖主要用於測試目的。

**OAuth 授權代碼授予流：** 預配服務支援[授權代碼授予](https://tools.ietf.org/html/rfc6749#page-24)。 在庫中提交發佈應用的請求後，我們的團隊將與您合作收集以下資訊：
*  授權 URL：用戶端通過使用者代理重定向從資源擁有者處獲取授權的 URL。 使用者被重定向到此 URL 以授權訪問。 
*  權杖交換 URL：用戶端用於交換訪問權杖的授權授予的 URL，通常使用用戶端身份驗證。
*  用戶端 ID：授權伺服器向註冊用戶端頒發用戶端識別碼，該識別碼是表示用戶端提供的註冊資訊的唯一字串。  用戶端識別碼不是機密，但用戶端識別碼不是機密。它暴露給資源擁有者，**不能**單獨用於用戶端身份驗證。  
*  用戶端金鑰：用戶端金鑰是授權伺服器生成的機密。 它應該是只有授權伺服器知道的唯一值。 

請注意，由於用戶端機密的暴露，不支援 OAuth v1。 支援 OAuth v2。  

最佳做法（建議但不是必需的）：
* 支援多個重定向 URL。 管理員可以從"portal.azure.com"和"aad.portal.azure.com"配置預配。 支援多個重定向 URL 將確保使用者可以授權從任一門戶進行訪問。
* 支援多個機密，以確保順利續訂機密，而不會停機。 

**長期生活的 OAuth 承載代幣：** 如果應用程式不支援 OAuth 授權代碼授予流，則還可以生成比管理員可用於設置預配集成的長壽命 OAuth 承載權杖。 權杖應為永久，否則在權杖過期時將[隔離](application-provisioning-quarantine-status.md)預配作業。 此權杖的大小必須低於 1KB。  

有關其他身份驗證和授權方法，請讓我們知道[使用者語音](https://aka.ms/appprovisioningfeaturerequest)。

### <a name="gallery-go-to-market-launch-check-list"></a>畫廊上市啟動檢查清單
為了説明提高我們聯合整合的意識和需求，我們建議您更新現有文檔並擴大行銷管道中的集成。  以下是一組清單活動，我們建議您完成以支援啟動

* **銷售和客戶支援就緒。** 確保您的銷售和支援小組瞭解並可以與集成功能對話。 向銷售和支援小組簡要介紹情況，為他們提供常見問題解答，並將集成到銷售材料中。 
* **博客文章和/或電子報。** 製作一篇博客文章或電子報，描述聯合整合、益處以及如何開始。 [示例：Imprivata 和 Azure 活動目錄電子報](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **社交媒體。** 利用 Twitter、Facebook 或LinkedIn等社交媒體，向客戶推廣集成。 請務必包括@AzureAD，以便我們可以轉推您的帖子。 [示例： Imprivata 推特帖子](https://twitter.com/azuread/status/1123964502909779968)
* **行銷網站。** 創建或更新行銷頁面（例如集成頁面、合作夥伴頁面、定價頁面等），以包括聯合集成的可用性。 [示例：平板集成頁面](https://pingboard.com/org-chart-for)、[智慧表集成頁面](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)[、Monday.com定價頁面](https://monday.com/pricing/) 
* **技術文檔。** 創建有關客戶如何開始操作的説明中心文章或技術文檔。 [示例：特使 + 微軟 Azure 活動目錄集成。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **客戶溝通。** 通過客戶溝通（每月通訊、電子郵件活動、產品發佈說明）提醒客戶新集成。 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>允許 Azure AD 預配服務使用的 IP 位址發出 SCIM 請求

某些應用允許其應用的入站流量。 為了使 Azure AD 預配服務按預期運行，必須允許使用的 IP 位址。 如需每個服務標籤/區域的 IP 位址清單，請參閱 JSON 檔案 - [Azure IP 範圍和服務標籤 – 公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。 您可以根據需要將這些 IP 下載和程式設計到防火牆中。 Azure AD 預配的預留 IP 範圍可以在"AzureActiveDirectoryDomain 服務"下找到。

## <a name="related-articles"></a>相關文章

* [自動將使用者預配和取消預配到 SaaS 應用](user-provisioning.md)
* [自訂使用者佈建的屬性對應](customize-application-attributes.md)
* [為屬性對應編寫運算式](functions-for-customizing-application-data.md)
* [使用者預配的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帳戶佈建通知](user-provisioning.md)
* [有關如何集成 SaaS 應用的教程清單](../saas-apps/tutorial-list.md)
