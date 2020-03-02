---
title: 開發 SCIM 端點，以從 Azure AD 將使用者布建至應用程式
description: 跨網域身分識別管理（SCIM）的系統會標準化自動使用者布建。 瞭解如何開發 SCIM 端點、整合您的 SCIM API 與 Azure Active Directory，並開始自動將使用者和群組布建到您的雲端應用程式。
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
ms.date: 03/01/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2fda5d1bdd00a601df363bd930e5f2f6d610c7f
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208707"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>建立 SCIM 端點，並使用 Azure Active Directory （Azure AD）來設定使用者布建

身為應用程式開發人員，您可以使用跨網域身分識別管理（SCIM）使用者管理 API 的系統，在您的應用程式與 Azure AD 之間，自動布建使用者和群組。 本文說明如何建立 SCIM 端點，並與 Azure AD 布建服務整合。 SCIM 規格提供一般使用者架構來進行布建。 搭配 SAML 或 OpenID Connect 等同盟標準使用時，SCIM 會為系統管理員提供端對端且以標準為基礎的存取管理解決方案。

SCIM 是兩個端點的標準化定義：/Users 端點和/Groups 端點。 它會使用一般 REST 動詞來建立、更新和刪除物件，以及針對一般屬性（例如組名、使用者名稱、名字、姓氏和電子郵件）預先定義的架構。 提供 SCIM 2.0 REST API 的應用程式可以減少或消除使用專屬使用者管理 API 的痛苦。 例如，任何符合規範的 SCIM 用戶端都知道如何對/Users 端點進行 JSON 物件的 HTTP POST，以建立新的使用者專案。 符合 SCIM standard 的應用程式不需要稍微不同的 API，而是可以立即利用既有的用戶端、工具和程式碼。 

![使用 SCIM 從 Azure AD 布建至應用程式](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM 2.0 （RFC [7642](https://tools.ietf.org/html/rfc7642)， [7643](https://tools.ietf.org/html/rfc7643)， [7644](https://tools.ietf.org/html/rfc7644)）中定義之管理的標準使用者物件架構和 rest api，可讓身分識別提供者和應用程式更輕鬆地彼此整合。 建立 SCIM 端點的應用程式開發人員可以與任何 SCIM 相容的用戶端整合，而不需要執行自訂工作。

自動布建至應用程式時，必須建立 SCIM 端點，並將其與相容的 Azure AD SCIM 進行整合。 請執行下列步驟，以開始將使用者和群組布建到您的應用程式。 
    
  * **[步驟1：設計您的使用者和群組架構。](#step-1-design-your-user-and-group-schema)** 識別您的應用程式所需的物件和屬性，並判斷它們如何對應至 Azure AD SCIM 執行所支援的使用者和群組架構。

  * **[步驟2：瞭解 Azure AD SCIM 的執行。](#step-2-understand-the-azure-ad-scim-implementation)** 瞭解如何實行 Azure AD SCIM 用戶端，並建立 SCIM 通訊協定要求處理和回應的模型。

  * **[步驟3：建立 SCIM 端點。](#step-3-build-a-scim-endpoint)** 端點必須是 SCIM 2.0 相容，才能與 Azure AD 布建服務整合。 您也可以選擇使用 Microsoft 通用語言基礎結構（CLI）程式庫和程式碼範例來建立您的端點。 這些範例僅供參考和測試之用;我們建議您不要編碼您的生產應用程式，以依賴它們。

  * **[步驟4：將您的 SCIM 端點與 Azure AD SCIM 用戶端整合。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 如果您的組織使用協力廠商應用程式來執行 Azure AD 支援的 SCIM 2.0 設定檔，您可以立即開始自動布建和取消布建使用者和群組。

  * **[步驟5：將您的應用程式發行至 Azure AD 應用程式資源庫。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 讓客戶輕鬆探索您的應用程式，並輕鬆地設定布建。 

![整合 SCIM 端點與 Azure AD 的步驟](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>步驟1：設計您的使用者和群組架構

每個應用程式都需要不同的屬性來建立使用者或群組。 藉由識別應用程式所需的物件（使用者、群組）和屬性（名稱、管理員、作業標題等）來開始整合。 SCIM standard 定義了用於管理使用者和群組的架構。 核心使用者架構只需要三個屬性：**識別碼**（服務提供者定義的識別碼）、 **externalId** （用戶端定義的識別碼）和**中繼**（由服務提供者維護的唯讀中繼資料）。 所有其他屬性都是選擇性的。 除了核心使用者架構外，SCIM 標準也會定義企業使用者擴充功能，以及擴充使用者架構以符合您應用程式需求的模型。 例如，如果您的應用程式需要使用者的管理員，您可以使用企業使用者架構來收集使用者的管理員和核心架構，以收集使用者的電子郵件。 若要設計您的架構，請遵循下列步驟：
  1. 列出您的應用程式所需的屬性。 將您的需求細分為驗證所需的屬性（例如 loginName 和電子郵件）、管理使用者的生命週期所需的屬性（例如狀態/作用中），以及特定應用程式工作所需的其他屬性（例如，管理員、標記），會很有説明。
  2. 檢查是否已在核心使用者架構或企業使用者架構中定義這些屬性。 如果您需要且不涵蓋在核心或企業使用者架構中的任何屬性，您將需要定義涵蓋所需屬性的使用者架構延伸。 在下列範例中，我們已為使用者新增延伸模組，以允許在使用者上布建「標記」。 最好只從核心和企業使用者架構開始著手，然後再展開至其他自訂架構。  
  3. 將 SCIM 屬性對應至 Azure AD 中的使用者屬性。 如果您在 SCIM 端點中定義的其中一個屬性沒有 Azure AD 使用者架構的明確對應項，則資料很有可能不會儲存在大部分租使用者上的 user 物件上。 請考慮此屬性是否可做為建立使用者的選擇性。 如果屬性對您的應用程式有效，請引導租使用者系統管理員擴充其架構，或使用延伸模組屬性，如下所示的「標記」屬性。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表1：概述您需要的屬性 
| 步驟1：判斷您的應用程式所需的屬性| 步驟2：將應用程式需求對應至 SCIM standard| 步驟3：將 SCIM 屬性對應至 Azure AD 屬性|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|姓名 lastName|lastName|
|workMail|電子郵件 [類型 eq "work"]。值|郵件|
|manager|manager|manager|
|tag (標記)|urn： ietf： params： scim：架構：擴充功能：2.0： CustomExtension：標記|extensionAttribute1|
|status|作用中|isSoftDeleted （計算的值未儲存在使用者上）|

上述定義的架構會使用下列 Json 承載來表示。 請注意，除了應用程式所需的屬性之外，JSON 標記法還包含必要的 "id"、"externalId" 和 "meta" 屬性。

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

### <a name="table-2-default-user-attribute-mapping"></a>表2：預設使用者屬性對應
接著，您可以使用下表來瞭解您的應用程式所需的屬性如何對應至 Azure AD 中的屬性和 SCIM RFC。 您可以[自訂](customize-application-attributes.md)AZURE AD 與 SCIM 端點之間的屬性對應方式。 請注意，您不需要同時支援使用者和群組或所有屬性，如下所示。 這些參考會說明 Azure AD 中的屬性通常如何對應至 SCIM 通訊協定中的屬性。 

| Azure Active Directory 使用者 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |作用中 |
|department|urn： ietf： params： scim：架構：擴充功能： enterprise：2.0： User：部門|
| displayName |displayName |
|employeeId|urn： ietf： params： scim：架構： extension： enterprise：2.0： User： employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| 郵件 |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn： ietf： params： scim：架構：擴充功能： enterprise：2.0： User： manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>表3：預設群組屬性對應

| Azure Active Directory 群組 | urn： ietf： params： scim：架構： core：2.0： Group |
| --- | --- |
| displayName |displayName |
| 郵件 |emails[type eq "work"].value |
| mailNickname |displayName |
| 成員 |成員 |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC 中定義了數個端點。 您可以從/User 端點開始著手，然後從該處展開。 當您使用自訂屬性時，或您的架構經常變更時，/Schemas 端點會很有説明。 它可讓用戶端自動取得最新的架構。 /Bulk 端點在支援群組時特別有用。 下表描述 SCIM 標準中定義的各種端點。 當您使用自訂屬性時，或您的架構經常變更時，/Schemas 端點會很有説明。 它可讓用戶端自動取得最新的架構。 /Bulk 端點在支援群組時特別有用。 下表描述 SCIM 標準中定義的各種端點。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表4：判斷您想要開發的端點
|端點|描述|
|--|--|
|/User|在使用者物件上執行 CRUD 作業。|
|/Group|對群組物件執行 CRUD 作業。|
|/ServiceProviderConfig|提供支援之 SCIM 標準功能的詳細資料，例如支援的資源和驗證方法。|
|/ResourceTypes|指定每個資源的相關中繼資料|
|/Schemas|每個用戶端和服務提供者所支援的屬性集可能有所不同。 雖然一個服務提供者可能包括「名稱」、「標題」和「電子郵件」，但另一個服務提供者會使用「名稱」、「標題」和「phoneNumbers」。 架構端點可讓您探索支援的屬性。|
|/Bulk|大量作業可讓您在單一作業中執行大型資源物件集合的作業（例如，更新大型群組的成員資格）。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>步驟2：瞭解 Azure AD SCIM 的執行
> [!IMPORTANT]
> Azure AD SCIM 實作行為的上次更新日期為 2018 年 12 月 18 日。 如需已變更內容的資訊，請參閱 [Azure AD 使用者佈建服務的 SCIM 2.0 通訊協定合規性](application-provisioning-config-problem-scim-compatibility.md)。

如果您要建立支援 SCIM 2.0 使用者管理 API 的應用程式，本節將詳細說明如何執行 Azure AD SCIM 用戶端。 它也會說明如何建立 SCIM 通訊協定要求處理和回應的模型。 在您完成 SCIM 端點之後，您可以遵循上一節所述的程式來測試它。

在[SCIM 2.0 通訊協定規格](http://www.simplecloud.info/#Specification)中，您的應用程式必須符合下列需求：

* 支援根據[SCIM 通訊協定](https://tools.ietf.org/html/rfc7644#section-3.3)的第3.3 節，建立使用者和選擇性地群組。  
* 支援依據[SCIM 通訊協定的區段 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2)，修改具有修補程式要求的使用者或群組。  
* 支援針對稍早建立的使用者或群組（根據[SCIM 通訊協定的每節 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)）來抓取已知的資源。  
* 支援查詢使用者或群組，如[SCIM 通訊協定的](https://tools.ietf.org/html/rfc7644#section-3.4.2)每節3.4.2。  根據預設，使用者會由其 `id` 抓取，並由其 `username` 和 `externalid`查詢，而且會由 `displayName`查詢群組。  
* 依照 SCIM 通訊協定的每節3.4.2，支援依 ID 和 manager 來查詢使用者。  
* 支援依 ID 和 by 成員查詢群組，如同 SCIM 通訊協定的每節3.4.2。  
* 接受單一持有人權杖，以便驗證和授權應用程式的 Azure AD。

在執行 SCIM 端點時，請遵循下列一般指導方針，以確保與 Azure AD 的相容性：

* `id` 是所有資源的必要屬性。 傳回資源的每個回應都應該確保每個資源都有這個屬性，但不含成員的 `ListResponse` 除外。
* 查詢/篩選要求的回應應一律為 `ListResponse`。
* 群組是選擇性的，但只有在 SCIM 的執行支援修補程式要求時才支援。
* 您不需要在 PATCH 回應中包含整個資源。
* Microsoft Azure AD 只會使用下列運算子：  
    - `eq`
    - `and`
* 在 SCIM 的結構元素中，不需要區分大小寫的相符專案，特別是在 https://tools.ietf.org/html/rfc7644#section-3.5.2中定義的修補 `op` 作業值。 Azure AD 會發出 ' op ' 的值做為 `Add`、`Replace`和 `Remove`。
* Microsoft Azure AD 會要求提取隨機的使用者和群組，以確保端點和認證都是有效的。 它也會在[Azure 入口網站](https://portal.azure.com)的**測試連接**流程中完成。 
* 可以查詢資源的屬性應該設定為[Azure 入口網站](https://portal.azure.com)中應用程式的相符屬性。 如需詳細資訊，請參閱[自訂使用者](customize-application-attributes.md)布建屬性對應

### <a name="user-provisioning-and-deprovisioning"></a>使用者布建和取消布建

下圖顯示 Azure Active Directory 傳送至 SCIM 服務的訊息，以管理使用者在應用程式身分識別存放區中的生命週期。  

![顯示使用者布建和取消布建的順序](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*使用者布建和取消布建順序*

### <a name="group-provisioning-and-deprovisioning"></a>群組布建和取消布建

群組布建和解除布建是選擇性的。 當執行並啟用時，下圖顯示 Azure AD 傳送至 SCIM 服務的訊息，以管理應用程式身分識別存放區中的群組生命週期。  這些訊息與使用者的訊息有兩種不同之處：

* 取得群組的要求指定要從為回應要求所提供的任何資源中排除成員屬性。  
* 要求判斷參考屬性是否具有特定值，會是有關成員屬性的要求。  

![顯示群組布建和取消布建順序](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*群組布建和取消布建順序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 通訊協定要求和回應
本節提供 Azure AD SCIM 用戶端發出的範例 SCIM 要求，以及預期的回應範例。 為了獲得最佳結果，您應該撰寫應用程式的程式碼，以此格式處理這些要求，併發出預期的回應。

> [!IMPORTANT]
> 若要瞭解 Azure AD 使用者布建服務發出下列作業的方式和時間，請參閱布建迴圈一節：布建的[運作方式](how-provisioning-works.md)（[初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)）。

[使用者作業](#user-operations)
  - [建立使用者](#create-user)（[要求](#request) / [回應](#response)）
  - [取得使用者](#get-user)（[要求](#request-1) / [回應](#response-1)）
  - [依查詢取得使用者](#get-user-by-query)（[要求](#request-2) / [回應](#response-2)）
  - [依查詢取得使用者-零結果](#get-user-by-query---zero-results)（[要求](#request-3)
/ [回應](#response-3)）
  - [更新使用者 [多重值屬性]](#update-user-multi-valued-properties) （[要求](#request-4) /  [回應](#response-4)）
  - [更新使用者 [單一值屬性]](#update-user-single-valued-properties) （[要求](#request-5)
/ [回應](#response-5)） 
  - [停用使用者](#disable-user)（[要求](#request-14) / 
[回應](#response-14)）
  - [刪除使用者](#delete-user)（[要求](#request-6) / 
[回應](#response-6)）


[群組作業](#group-operations)
  - [建立群組](#create-group)（[要求](#request-7) / [回應](#response-7)）
  - [取得群組](#get-group)（[要求](#request-8) / [回應](#response-8)）
  - [取得 Group By displayName](#get-group-by-displayname) （[要求](#request-9) / [回應](#response-9)）
  - [更新群組 [非成員屬性]](#update-group-non-member-attributes) （[要求](#request-10)/
 [回應](#response-10)）
  - [更新群組 [新增成員]](#update-group-add-members) （[要求](#request-11) /
[回應](#response-11)）
  - [更新群組 [移除成員]](#update-group-remove-members) （[要求](#request-12) /
[回應](#response-12)）
  - [刪除群組](#delete-group)（[要求](#request-13) /
[回應](#response-13)）

### <a name="user-operations"></a>使用者作業

* `userName` 或 `email[type eq "work"]` 屬性可以查詢使用者。  

#### <a name="create-user"></a>建立使用者

###### <a name="request"></a>要求

*張貼/Users*
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

*HTTP/1.1 201 已建立*
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

#### <a name="get-user"></a>取得使用者

###### <a name="request-1"></a>邀請
*取得/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>回應（找到使用者）
*HTTP/1.1 200 正常*
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
*取得/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>回應（找不到使用者。 請注意，這不是必要的詳細資料，只是狀態。）

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>依查詢取得使用者

##### <a name="request-2"></a>邀請

*GET/Users？ filter = userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>回應

*HTTP/1.1 200 正常*
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

#### <a name="get-user-by-query---zero-results"></a>依查詢取得使用者-零結果

##### <a name="request-3"></a>邀請

*GET/Users？ filter = userName eq "不存在的使用者"*

##### <a name="response-3"></a>回應

*HTTP/1.1 200 正常*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>更新使用者 [多重值屬性]

##### <a name="request-4"></a>邀請

*PATCH/Users/6764549bef60420686bc HTTP/1。1*
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

##### <a name="response-4"></a>回應

*HTTP/1.1 200 正常*
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

#### <a name="update-user-single-valued-properties"></a>更新使用者 [單一值屬性]

##### <a name="request-5"></a>邀請

*PATCH/Users/5171a35d82074e068ce2 HTTP/1。1*
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

##### <a name="response-5"></a>回應

*HTTP/1.1 200 正常*
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

### <a name="disable-user"></a>停用使用者

##### <a name="request-14"></a>邀請

*PATCH/Users/5171a35d82074e068ce2 HTTP/1。1*
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

##### <a name="response-14"></a>回應

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

##### <a name="request-6"></a>邀請

*刪除/Users/5171a35d82074e068ce2 HTTP/1。1*

##### <a name="response-6"></a>回應

*HTTP/1.1 204 沒有內容*

### <a name="group-operations"></a>群組作業

* 群組一定要以空的成員清單來建立。
* `displayName` 屬性可以查詢群組。
* 對群組修補程式要求的更新，應該會在回應中產生*HTTP 204 沒有內容*。 不建議以所有成員的清單傳回主體。
* 不需要支援傳回群組的所有成員。

#### <a name="create-group"></a>建立群組

##### <a name="request-7"></a>邀請

*POST/Groups HTTP/1。1*
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

##### <a name="response-7"></a>回應

*HTTP/1.1 201 已建立*
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

##### <a name="request-8"></a>邀請

*GET/Groups/40734ae655284ad3abcc？ excludedAttributes = 成員 HTTP/1。1*

##### <a name="response-8"></a>回應
*HTTP/1.1 200 正常*
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

#### <a name="get-group-by-displayname"></a>取得依 displayName 分組

##### <a name="request-9"></a>邀請
*GET/Groups？ excludedAttributes = 成員 & filter = displayName eq "displayName" HTTP/1。1*

##### <a name="response-9"></a>回應

*HTTP/1.1 200 正常*
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

#### <a name="update-group-non-member-attributes"></a>更新群組 [非成員屬性]

##### <a name="request-10"></a>邀請

*PATCH/Groups/fa2ce26709934589afc5 HTTP/1。1*
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

##### <a name="response-10"></a>回應

*HTTP/1.1 204 沒有內容*

### <a name="update-group-add-members"></a>更新群組 [加入成員]

##### <a name="request-11"></a>邀請

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1。1*
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

##### <a name="response-11"></a>回應

*HTTP/1.1 204 沒有內容*

#### <a name="update-group-remove-members"></a>更新群組 [移除成員]

##### <a name="request-12"></a>邀請

*PATCH/Groups/a99962b9f99d4c4fac67 HTTP/1。1*
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

##### <a name="response-12"></a>回應

*HTTP/1.1 204 沒有內容*

#### <a name="delete-group"></a>刪除群組

##### <a name="request-13"></a>邀請

*刪除/Groups/cdb1ce18f65944079d37 HTTP/1。1*

##### <a name="response-13"></a>回應

*HTTP/1.1 204 沒有內容*

### <a name="security-requirements"></a>安全性需求
**TLS 通訊協定版本**

唯一可接受的 TLS 通訊協定版本為 TLS 1.2 和 TLS 1.3。 不允許其他 TLS 版本。 不允許任何版本的 SSL。 
- RSA 金鑰必須至少有2048位。
- ECC 金鑰必須至少有256位，並使用已核准的橢圓曲線產生


**金鑰長度**

所有服務都必須使用足夠長度的密碼編譯金鑰所產生的 x.509 憑證，亦即：

**加密套件**

所有服務都必須依照以下指定的確切順序，設定為使用下列加密套件。 請注意，如果您只有一個 RSA 憑證，則安裝的 ECDSA 加密套件不會有任何作用。 </br>

TLS 1.2 加密套件最小橫條：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>步驟3：建立 SCIM 端點

既然您已經 desidned 您的架構，並瞭解 Azure AD 的 SCIM 執行，就可以開始開發您的 SCIM 端點。 您可以依賴 SCIM commuinty 所發佈的許多開放原始碼 SCIM 程式庫，而不是從頭開始並完全建立完整的執行。  
Azure AD 布建小組發佈的開放原始碼 .NET Core[參考程式碼](https://aka.ms/SCIMReferenceCode)，就是一種可讓您開始進行開發的資源。 建立 SCIM 端點之後，您會想要測試它。您可以使用在參考程式碼中提供的[postman 測試](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)集合，或透過[上述](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)提供的範例要求/回應來執行。  

注意：參考程式碼的目的是要協助您開始建立 SCIM 端點，並以「原樣」提供。 我們歡迎您參與社區的貢獻，協助建立和維護程式碼。 

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>步驟4：將您的 SCIM 端點與 Azure AD SCIM 用戶端整合

Azure AD 可以設定為將已指派的使用者和群組自動布建至應用程式，以執行[SCIM 2.0 通訊協定](https://tools.ietf.org/html/rfc7644)的特定設定檔。 設定檔的詳細資訊記載于[步驟2：瞭解 AZURE AD SCIM 的執行](#step-2-understand-the-azure-ad-scim-implementation)。

洽詢應用程式提供者，或參閱應用程式提供者文件中的相關陳述，以了解是否符合這些需求。

> [!IMPORTANT]
> Azure AD SCIM 實建置於 Azure AD 的使用者布建服務之上，其設計目的是要讓使用者在 Azure AD 與目標應用程式之間保持同步，並實行一組非常特定的標準作業。 請務必瞭解這些行為，以瞭解 Azure AD SCIM 用戶端的行為。 如需詳細資訊，請參閱布建迴圈一節：布建的[運作方式](how-provisioning-works.md)中的[初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)。

### <a name="getting-started"></a>開始使用

支援本文所述 SCIM 設定檔的應用程式，可使用 Azure AD 應用程式庫中的「不在資源庫內的應用程式」功能連線到 Azure Active Directory。 連線之後，Azure AD 會每隔 40 分鐘執行一次同步處理程序，此程序會為指派的使用者和群組查詢應用程式的 SCIM 端點，並根據指派詳細資料加以建立或修改。

**若要連接支援 SCIM 的應用程式：**

1. 登入[Azure Active Directory 入口網站](https://aad.portal.azure.com)。 請注意，藉由註冊[開發人員計畫](https://developer.microsoft.com/office/dev-program)，您可以透過 P2 授權取得 Azure Active Directory 的免費試用版
2. 從左窗格中選取 [**企業應用程式**]。 隨即會顯示所有已設定的應用程式清單，包括從資源庫新增的應用程式。
3. 選取 [ **+ 新增應用程式**] > **所有** > 不在資源**庫的應用程式**中。
4. 輸入應用程式的 [名稱]，然後選取 [**新增**] 以建立應用程式物件。 新應用程式會新增至企業應用程式清單，並開啟至其 [應用程式管理] 畫面。

   ![螢幕擷取畫面顯示 Azure AD 應用程式資源庫](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 應用程式庫*

5. 在 [應用程式管理] 畫面中 **，選取左面板中的 [** 布建]。
6. 在 [佈建模式] 功能表上，選取 [自動]。

   ![範例： Azure 入口網站中的應用程式布建頁面](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *在 Azure 入口網站中設定布建*

7. 在 [租用戶 URL] 欄位中，輸入應用程式 SCIM 端點的 URL。 範例： https://api.contoso.com/scim/
8. 如果 SCIM 端點需要來自非 Azure AD 簽發者的 OAuth 持有人權杖，那麼便將所需的 OAuth 持有人權杖複製到選擇性 [祕密權杖] 欄位。 如果此欄位保留空白，Azure AD 包括從 Azure AD 發出的 OAuth 持有人權杖與每個要求。 應用程式若使用 Azure AD 作為識別提供者，便可以驗證此 Azure AD 簽發的權杖。 
   > [!NOTE]
   > ***不***建議將此欄位保留空白，並且依賴 Azure AD 所產生的權杖。 此選項主要是供測試之用。
9. 選取 [**測試連接**]，讓 Azure Active Directory 嘗試連線到 SCIM 端點。 如果嘗試失敗，則會顯示錯誤資訊。  

    > [!NOTE]
    > [測試連線] 會查詢 SCIM 端點中是否有不存在的使用者，並使用隨機 GUID 作為 Azure AD 設定中選取的比對屬性。 預期的正確回應是 HTTP 200 OK 和空白的 SCIM ListResponse 訊息。

10. 如果嘗試連線到應用程式成功，則選取 [**儲存**] 以儲存管理員認證。
11. **在 [對應**] 區段中，有兩組可選取的[屬性](customize-application-attributes.md)對應：一個用於使用者物件，另一個用於群組物件。 選取其中一個以檢閱從 Azure Active Directory 同步處理至應用程式的屬性。 選取為 [比對] 屬性的屬性會用來比對應用程式中的使用者和群組以進行更新作業。 選取 [儲存] 認可任何變更。

    > [!NOTE]
    > 您可以選擇性地藉由停用「群組」對應以停用同步處理群組物件。

12. 在 [設定] 底下，[範圍] 欄位會定義要同步的使用者和群組。 選取 [**只同步指派的使用者和群組**（建議選項）]，只同步 [**使用者和群組**] 索引標籤中指派的使用者和群組。
13. 完成設定之後，請將 [布建**狀態**] 設為 [**開啟**]。
14. 選取 [**儲存**] 以啟動 Azure AD 布建服務。
15. 如果只同步已指派的使用者和群組（建議選項），請務必選取 [**使用者和群組**] 索引標籤，並指派您想要同步的使用者或群組。

初始迴圈開始之後，您可以在左面板中選取 [布建**記錄**] 來監視進度，其中會顯示應用程式上的布建服務所執行的所有動作。 如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始週期比之後的同步處理花費更多時間執行，只要服務正在執行，這大約每40分鐘就會發生一次。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>步驟5：將您的應用程式發佈至 Azure AD 應用程式庫

如果您要建立的應用程式將由多個租使用者使用，您可以在 Azure AD 應用程式資源庫中使用它。 這可讓組織輕鬆地探索應用程式並設定布建。 在 Azure AD 資源庫中發佈您的應用程式，並將布建提供給其他人很容易。 請在 [這裡](../develop/howto-app-gallery-listing.md)查明步驟。 Microsoft 會與您合作，將您的應用程式整合到我們的資源庫、測試您的端點，以及發行登入[檔](../saas-apps/tutorial-list.md)供客戶使用。 

### <a name="gallery-onboarding-checklist"></a>圖庫上架檢查清單
請遵循下列檢查清單，以確保您的應用程式上架快速，而且客戶有順暢的部署體驗。 當您上架至資源庫時，系統會向您收集資訊。 
> [!div class="checklist"]
> * 支援[SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation)使用者和群組端點（只需要一個），但建議兩者都使用）
> * 每個租使用者至少支援25個要求（必要）
> * 建立工程和支援連絡人，以引導客戶張貼庫上線（必要）
> * 3應用程式的非過期測試認證（必要）
> * 支援 OAuth 授權碼授與或長時間的權杖，如下所述（必要）
> * 建立小組的工程和支援點，以支援客戶在後置庫上線（必要）
> * 支援使用單一修補程式來更新多個群組成員資格（建議） 
> * 公開記錄您的 SCIM 端點（建議） 
> * [支援架構探索](https://tools.ietf.org/html/rfc7643#section-6)（建議）


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>在應用程式資源庫中布建連接器的授權
SCIM 規格不會定義驗證和授權的 SCIM 特定配置。 它依賴現有的業界標準。 Azure AD 布建用戶端對資源庫中的應用程式支援兩種授權方法。 

|授權方法|優點|缺點|支援|
|--|--|--|--|
|使用者名稱和密碼（Azure AD 不建議或支援）|易於執行|不安全-[您的 Pa $ $word 不重要](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|支援以案例為基礎的資源庫應用程式。 不支援非資源庫應用程式。|
|長時間存留的持有人權杖|長時間執行的權杖不需要使用者存在。 在設定布建時，系統管理員很容易就能使用這些資源。|長時間存留的權杖可能難以與系統管理員共用，而不需要使用不安全的方法，例如電子郵件。 |支援資源庫和非資源庫應用程式。 |
|OAuth 授權碼授與|存取權杖的存留期比密碼短，而且具有長期持有人權杖沒有的自動化重新整理機制。  實際的使用者必須存在於初始授權期間，因而增加了一層責任。 |需要使用者存在。 如果使用者離開組織，權杖將會無效，而且必須再次完成授權。|支援資源庫應用程式。 目前正在支援非資源庫應用程式。|
|OAuth 用戶端認證授與|存取權杖的存留期比密碼短，而且具有長期持有人權杖沒有的自動化重新整理機制。 授權碼授與和用戶端認證授與會建立相同類型的存取權杖，因此在這些方法之間的移動對 API 而言是透明的。  布建可以完全自動化，而且可以無訊息的方式要求新的權杖，而不需要使用者介入。 ||不支援資源庫和非資源庫應用程式。 我們的待處理專案中有支援。|

[!NOTE] 不建議您在 Azure AD 布建設定自訂應用程式 UI 中，將 [權杖] 欄位保留空白。 產生的權杖主要適用于測試用途。

**OAuth 授權碼授與流程：** 布建服務支援[授權碼授](https://tools.ietf.org/html/rfc6749#page-24)與。 提交您在資源庫中發佈應用程式的要求之後，我們的小組會與您一起收集下列資訊：
*  授權 URL：用戶端用來透過使用者代理程式重新導向取得資源擁有者授權的 URL。 使用者會重新導向至此 URL 以授與存取權。 
*  權杖交換 URL：用戶端的 URL，用來交換存取權杖的授權授與，通常是透過用戶端驗證。
*  用戶端識別碼：授權伺服器會向已註冊的用戶端發出用戶端識別碼，這是代表用戶端所提供之註冊資訊的唯一字串。  用戶端識別碼不是秘密;它會公開給資源擁有者，而且**不得**單獨用於用戶端驗證。  
*  用戶端密碼：用戶端密碼是由授權伺服器產生的秘密。 它應該是只有授權伺服器才知道的唯一值。 

請注意，因為公開用戶端密碼，所以不支援 OAuth v1。 支援 OAuth v2。  

最佳做法（建議使用，但非必要）：
* 支援多個重新導向 Url。 系統管理員可以從 "portal.azure.com" 和 "aad.portal.azure.com" 設定布建。 支援多個重新導向 Url 可確保使用者可以從任一入口網站授與存取權。
* 支援多個秘密，以確保順暢地更新秘密，而不需要停機。 

**長期的 OAuth 持有人權杖：** 如果您的應用程式不支援 OAuth 授權碼授與流程，您也可以產生長時間的 OAuth 持有人權杖，而不是系統管理員可用來設定布建整合。 權杖應該是永久的，否則當令牌過期時，將會[隔離](application-provisioning-quarantine-status.md)布建作業。 此權杖的大小必須低於1KB。  

如需其他驗證和授權方法，請在[UserVoice](https://aka.ms/appprovisioningfeaturerequest)上讓我們知道。

### <a name="gallery-go-to-market-launch-check-list"></a>圖庫進入市場的上市檢查清單
為了協助您瞭解我們的聯合整合的認知和需求，建議您更新現有的檔，並在您的行銷通路中擴展整合。  以下是我們建議您完成以支援啟動的一組檢查清單活動

* **銷售和客戶支援就緒。** 確保您的銷售和支援小組知道，並可與整合功能交談。 簡述您的銷售和支援小組，提供他們常見問題，並將整合納入您的銷售材料。 
* **Blog 文章和/或按發行。** 製作一篇 blog 文章，或按發行來描述聯合整合、優點以及如何開始使用。 [範例： Imprivata 和 Azure Active Directory 按發行](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **社交媒體。** 利用您的社交媒體，例如 Twitter、Facebook 或 LinkedIn，將整合推廣給您的客戶。 請務必包含 @AzureAD，讓我們可以轉推您的文章。 [範例： Imprivata Twitter 文章](https://twitter.com/azuread/status/1123964502909779968)
* **行銷網站。** 建立或更新您的行銷頁面（例如整合頁面、合作夥伴頁面、定價頁面等）以包含聯合整合的可用性。 [範例： Pingboard 整合頁面](https://pingboard.com/org-chart-for)、 [Smartsheet 整合頁面](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)、 [Monday.com 定價頁面](https://monday.com/pricing/) 
* **技術檔。** 建立說明中心文章或技術檔，以瞭解客戶可以如何開始著手。 [範例： Envoy + Microsoft Azure Active Directory 整合。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **客戶通訊。** 透過您的客戶通訊（每月電子報、電子郵件行銷活動、產品版本資訊），警示客戶新整合。 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>允許 Azure AD 布建服務所使用的 IP 位址進行 SCIM 要求

某些應用程式允許輸入流量進入其應用程式。 為了讓 Azure AD 布建服務如預期般運作，必須允許使用的 IP 位址。 如需每個服務標籤/區域的 IP 位址清單，請參閱 JSON 檔案 - [Azure IP 範圍和服務標籤 – 公用雲端](https://www.microsoft.com/download/details.aspx?id=56519)。 您可以視需要下載這些 Ip 並將其程式設計至您的防火牆。 您可以在 "AzureActiveDirectoryDomainServices" 下找到 Azure AD 布建的保留 IP 範圍。

## <a name="related-articles"></a>相關文章

* [自動化 SaaS 應用程式的使用者布建和解除布建](user-provisioning.md)
* [自訂使用者佈建的屬性對應](customize-application-attributes.md)
* [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
* [使用者布建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帳戶佈建通知](user-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
