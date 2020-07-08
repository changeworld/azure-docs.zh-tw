---
title: 開發 SCIM 端點，將使用者從 Azure AD 佈建至應用程式
description: 跨網域身分識別管理系統 (SCIM) 可將自動使用者佈建標準化。 了解如何開發 SCIM 端點、整合您的 SCIM API 與 Azure Active Directory，並開始自動將使用者和群組佈建至您的雲端應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 03/07/2020
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: b08509bed6b26cb56caebd4dc47fc3b7ac84ce27
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85117313"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>建置 SCIM 端點並設定使用 Azure AD 的使用者佈建

身為應用程式開發人員，您可以使用跨網域身分識別管理系統 (SCIM) 使用者管理 API，在您的應用程式與 Azure AD 之間啟用使用者和群組的自動佈建。 本文說明如何建置 SCIM 端點，並與 Azure AD 佈建服務整合。 SCIM 規格提供一般的使用者佈建結構描述。 與 SAML 或 OpenID Connect 等同盟標準搭配使用時，SCIM 可為管理員提供端對端、以標準為基礎的存取管理解決方案。

SCIM 是兩個端點的標準化定義：/Users 端點和 /Groups 端點。 它會使用一般 REST 動詞來建立、更新和刪除物件，以及針對一般屬性 (例如群組名稱、使用者名稱、名字、姓氏和電子郵件) 預先定義的結構描述。 提供 SCIM 2.0 REST API 的應用程式，可減輕或消除使用專屬使用者管理 API 的麻煩。 例如，任何符合規範的 SCIM 用戶端都知道如何對 /Users 端點進行 JSON 物件的 HTTP POST，以建立新的使用者項目。 符合 SCIM 標準的應用程式可以立即運用既有的用戶端、工具和程式碼，而無須使用略為不同的 API 來執行相同的基本動作。 

![使用 SCIM 從 Azure AD 佈建至應用程式](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

定義於 SCIM 2.0 中用於管理的標準使用者物件結構描述和 REST API (RFC [7642](https://tools.ietf.org/html/rfc7642)、[7643](https://tools.ietf.org/html/rfc7643)、[7644](https://tools.ietf.org/html/rfc7644))，可讓識別提供者和應用程式更容易互相整合。 建置 SCIM 端點的應用程式開發人員可與任何符合 SCIM 規範的用戶端整合，而無須執行自訂工作。

要自動佈建至應用程式，必須建置 SCIM 端點，並將其與 Azure AD SCIM 用戶端整合。 請執行下列步驟，開始將使用者和群組佈建至您的應用程式。 
    
  * **[步驟 1：設計您的使用者和群組結構描述。](#step-1-design-your-user-and-group-schema)** 識別您的應用程式所需的物件和屬性，並確認如何將其對應至 Azure AD SCIM 實作所支援的使用者和群組結構描述。

  * **[步驟 2：了解 Azure AD SCIM 實作。](#step-2-understand-the-azure-ad-scim-implementation)** 了解如何 Azure AD SCIM 用戶端的實作方式，並建立 SCIM 通訊協定要求處理和回應的模型。

  * **[步驟 3：建置 SCIM 端點。](#step-3-build-a-scim-endpoint)** 端點必須與 SCIM 2.0 相容，才能與 Azure AD 佈建服務整合。 您也可以選擇使用 Microsoft 通用語言基礎結構 (CLI) 程式庫和程式碼範例來建置您的端點。 這些範例僅供參考和測試之用；建議您在為生產應用程式編碼時不要依賴這些範例。

  * **[步驟 4︰整合您的 SCIM 端點與 Azure AD SCIM 用戶端。](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** 如果您的組織使用第三方應用程式，且其實作的 SCIM 2.0 設定檔受 Azure AD 支援，則可立即開始自動佈建和取消佈建使用者和群組。

  * **[步驟 5：將您的應用程式發佈至 Azure AD 應用程式庫。](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** 讓客戶輕鬆探索您的應用程式及設定佈建。 

![整合 SCIM 端點與 Azure AD 的步驟](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>步驟 1:設計您的使用者和群組結構描述

每個應用程式分別需要不同的屬性來建立使用者或群組。 藉由識別應用程式所需的物件 (使用者、群組) 和屬性 (名稱、管理員、職稱等) 開始進行整合。 SCIM 標準定義了用來管理使用者和群組的結構描述。 核心使用者結構描述只需要三個屬性：**id** (服務提供者定義的識別碼)、**externalId** (用戶端定義的識別碼)，以及 **meta** (服務提供者維護的唯讀中繼資料)。 其他所有屬性都是選擇性的。 除了核心使用者結構描述以外，SCIM 標準也會定義企業使用者延伸模組，以及用來擴充使用者結構描述以符合應用程式需求的模型。 例如，如果您的應用程式需要使用者的管理員，您可以使用企業使用者結構描述來收集使用者的管理員，並使用核心結構描述來收集使用者的電子郵件。 若要設計您的結構描述，請遵循下列步驟：
  1. 列出您的應用程式所需的屬性。 將您的需求細分為驗證所需的屬性 (例如 loginName 和 email)、管理使用者的生命週期所需的屬性 (例如狀態/作用中)，以及特定應用程式運作所需的其他屬性 (例如管理員、標籤)，可能會有幫助。
  2. 檢查這些屬性是否已定義於核心使用者結構描述或企業使用者結構描述中。 若有任何您需要但未涵蓋於核心或企業使用者結構描述中的屬性，您就必須定義使用者結構描述的延伸模組以涵蓋您所需的屬性。 在下列範例中，我們為使用者新增了延伸模組，以允許佈建使用者的「標籤」。 建議您先從核心和企業使用者結構描述開始著手，然後再擴充至其他自訂結構描述。  
  3. 將 SCIM 屬性對應至 Azure AD 中的使用者屬性。 如果您在 SCIM 端點中定義的某個屬性在 Azure AD 使用者結構描述上沒有明確的對應項目，則資料很有可能完全不會儲存在大部分租用戶的使用者物件上。 請考量此屬性在建立使用者時是否可以是選擇性的。 如果此屬性對於應用程式的運作至關重要，請引導租用戶管理員擴充其結構描述或使用延伸模組屬性，如以下所示的「標籤」屬性。

### <a name="table-1-outline-the-attributes-that-you-need"></a>表格 1：概述您所需的屬性 
| 步驟 1:確認您的應用程式所需的屬性| 步驟 2:將應用程式需求對應至 SCIM 標準| 步驟 3：將 SCIM 屬性對應至 Azure AD 屬性|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|Emails[type eq “work”].value|Mail|
|manager|manager|manager|
|tag|urn:ietf:params:scim:schemas:extension:2.0:CustomExtension:tag|extensionAttribute1|
|status|作用中|isSoftDeleted (未儲存使用者的計算值)|

上述已定義的結構描述會使用下列 Json 承載來表示。 請注意，除了應用程式所需的屬性以外，JSON 表示法還包含必要的 "id"、"externalId" 和 "meta" 屬性。

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
接著，您可以使用下表來了解應用程式所需的屬性如何對應至 Azure AD 和 SCIM RFC 中的屬性。 您可以[自訂](customize-application-attributes.md)在 Azure AD 與 SCIM 端點之間對應屬性的方式。 請注意，您不需要同時支援使用者和群組或所有屬性，如下所示。 在考量如何將 Azure AD 中的屬性經常對應至 SCIM 通訊協定中的屬性時，可加以參考。 

| Azure Active Directory 使用者 | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |作用中 |
|department|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>表 3：預設預設屬性對應

| Azure Active Directory 群組 | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |externalId |
| proxyAddresses |emails[type eq "other"].Value |

SCIM RFC 中定義了數個端點。 您可以從 /User 端點開始著手，然後再向外擴充。 當您使用自訂屬性時，或結構描述經常變更時，/Schemas 端點會很有幫助。 此端點可讓用戶端自動取得最新的結構描述。 /Bulk 端點在支援群組時特別有用。 下表說明 SCIM 標準中定義的各種端點。 當您使用自訂屬性時，或結構描述經常變更時，/Schemas 端點會很有幫助。 此端點可讓用戶端自動取得最新的結構描述。 /Bulk 端點在支援群組時特別有用。 下表說明 SCIM 標準中定義的各種端點。 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>表 4：決定您想要開發的端點
|端點|DESCRIPTION|
|--|--|
|/User|對使用者物件執行 CRUD 作業。|
|/Group|對群組物件執行 CRUD 作業。|
|/ServiceProviderConfig|提供支援之 SCIM 標準功能的詳細資料，例如，支援的資源和驗證方法。|
|/ResourceTypes|指定每個資源的相關中繼資料|
|/Schemas|每個用戶端和服務提供者所支援的屬性集可能有所不同。 某個服務提供者可能包含 “name”、“title” 和 “emails”，另一個服務提供者則使用 “name”、“title” 和 “phoneNumbers”。 結構描述端點可讓您探索支援的屬性。|
|/Bulk|大量作業可讓您在單一作業中對大型資源物件集合執行作業 (例如，更新大型群組的成員資格)。|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>步驟 2:了解 Azure AD SCIM 實作
> [!IMPORTANT]
> Azure AD SCIM 實作行為的上次更新日期為 2018 年 12 月 18 日。 如需已變更內容的資訊，請參閱 [Azure AD 使用者佈建服務的 SCIM 2.0 通訊協定合規性](application-provisioning-config-problem-scim-compatibility.md)。

如果您要建立支援 SCIM 2.0 使用者管理 API 的應用程式，本節將詳細說明如何實作 Azure AD SCIM 用戶端。 此外也會說明如何建立 SCIM 通訊協定要求處理和回應的模型。 在您實作 SCIM 端點之後，您可以依照上一節說明的程序加以測試。

在 [SCIM 2.0 通訊協定規格](http://www.simplecloud.info/#Specification)中，您的應用程式必須符合下列需求：

* 支援根據 [SCIM 通訊協定 3.3 小節](https://tools.ietf.org/html/rfc7644#section-3.3)建立使用者及選擇性建立群組的作業。  
* 支援根據 [SCIM 通訊協定 3.5.2 小節](https://tools.ietf.org/html/rfc7644#section-3.5.2)修改具有 PATCH 要求的使用者或群組的作業。  
* 支援根據 [SCIM 通訊協定 3.4.1 小節](https://tools.ietf.org/html/rfc7644#section-3.4.1)為先前建立的使用者或群組擷取已知資源的作業。  
* 支援根據 [SCIM 通訊協定 3.4.2 小節](https://tools.ietf.org/html/rfc7644#section-3.4.2)查詢使用者或群組的作業。  依預設會按 `id` 擷取使用者，並按 `username` 和 `externalid` 加以查詢，以及按 `displayName` 查詢群組。  
* 支援根據 SCIM 通訊協定 3.4.2 小節，依識別碼和管理員查詢使用者的作業。  
* 支援根據 SCIM 通訊協定 3.4.2 小節，依識別碼和成員查詢群組的作業。  
* 接受以單一持有人權杖對應用程式進行 Azure AD 的驗證和授權。

在執行 SCIM 端點時，請遵循下列一般指導方針，以確保與 Azure AD 的相容性：

* `id` 是所有資源的必要屬性。 傳回資源的每個回應均應確保每個資源都具有此屬性，但不含成員的 `ListResponse` 除外。
* 查詢/篩選要求的回應應一律為 `ListResponse`。
* 群組是選擇性的，但只有在 SCIM 實作支援 PATCH 要求時，才受到支援。
* 您不需要在 PATCH 回應中包含整個資源。
* Microsoft Azure AD 只會使用下列運算子：  
    - `eq`
    - `and`
* 對於 SCIM 中的結構元素請勿要求大小寫須相符 (特別是 `op` 作業值)，如 https://tools.ietf.org/html/rfc7644#section-3.5.2 中所定義。 Azure AD 會以 `Add`、`Replace` 和 `Remove` 的形式發出 'op' 的值。
* Microsoft Azure AD 會要求提取隨機的使用者和群組，以確保端點和認證都是有效的。 此作業也可在 [Azure 入口網站](https://portal.azure.com)的**測試連線**流程中完成。 
* 資源可供查詢的屬性，應在 [Azure 入口網站](https://portal.azure.com)中設定為應用程式的比對屬性。 如需詳細資訊，請參閱[自訂使用者佈建屬性對應](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>使用者佈建和取消佈建

下圖顯示 Azure Active Directory 會傳送至 SCIM 服務的訊息，以管理您應用程式的身分識別存放區中的使用者生命週期。  

![顯示使用者佈建和取消佈建順序](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*使用者佈建和解除佈建順序*

### <a name="group-provisioning-and-deprovisioning"></a>群組佈建和取消佈建

群組佈建和取消佈建是選擇性的。 在實作並啟用時，下圖顯示 Azure AD 會傳送至 SCIM 服務的訊息，以管理您應用程式的身分識別存放區中的群組生命週期。  這些訊息與使用者的訊息有兩方面的不同：

* 擷取群組的要求會指定將成員屬性從回應要求中提供的任何資源中排除。  
* 要求判斷參考屬性是否具有特定值，會是有關成員屬性的要求。  

![顯示群組佈建和取消佈建順序](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*群組佈建和解除佈建順序*

### <a name="scim-protocol-requests-and-responses"></a>SCIM 通訊協定要求和回應
本節提供 Azure AD SCIM 用戶端發出的範例 SCIM 要求，以及預期的回應範例。 為了獲得最佳結果，您應撰寫應用程式程式碼，以按此格式處理這些要求，並發出預期的回應。

> [!IMPORTANT]
> 若要了解 Azure AD 使用者佈建服務發出下列作業的方式和時間，請參閱[佈建週期：初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)一節 (位於[佈建運作方式](how-provisioning-works.md)中)。

[使用者作業](#user-operations)
  - [建立使用者](#create-user) ([要求](#request) / [回應](#response))
  - [取得使用者](#get-user) ([要求](#request-1) / [回應](#response-1))
  - [依查詢取得使用者](#get-user-by-query) ([要求](#request-2) / [回應](#response-2))
  - [依查詢取得使用者 - 零個結果](#get-user-by-query---zero-results) ([要求](#request-3)
/ [回應](#response-3))
  - [更新使用者 [多重值屬性]](#update-user-multi-valued-properties) ([要求](#request-4) /  [回應](#response-4))
  - [更新使用者 [單一值屬性]](#update-user-single-valued-properties) ([要求](#request-5)
/ [回應](#response-5)) 
  - [停用使用者](#disable-user) ([要求](#request-14) / 
[回應](#response-14))
  - [刪除使用者](#delete-user) ([要求](#request-6) / 
[回應](#response-6))


[群組作業](#group-operations)
  - [建立群組](#create-group) ([要求](#request-7) / [回應](#response-7))
  - [取得群組](#get-group) ([要求](#request-8) / [回應](#response-8))
  - [依 displayName 取得群組](#get-group-by-displayname) ([要求](#request-9) / [回應](#response-9))
  - [更新群組 [非成員屬性]](#update-group-non-member-attributes) ([要求](#request-10)/
 [回應](#response-10))
  - [更新群組 [新增成員]](#update-group-add-members) ([要求](#request-11) /
[回應](#response-11))
  - [更新群組 [移除成員]](#update-group-remove-members) ([要求](#request-12) /
[回應](#response-12))
  - [刪除群組](#delete-group) ([要求](#request-13) /
[回應](#response-13))

### <a name="user-operations"></a>使用者作業

* 可依 `userName` 或 `email[type eq "work"]` 屬性來查詢使用者。  

#### <a name="create-user"></a>建立使用者

###### <a name="request"></a>要求

*POST /Users*
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

###### <a name="request"></a><a name="request-1"></a>要求
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>回應 (找到使用者)
*HTTP/1.1 200 OK*
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
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>回應 (找不到使用者。 請注意，不一定要提供詳細資料，只需要狀態。)

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

##### <a name="request"></a><a name="request-2"></a>要求

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>回應

*HTTP/1.1 200 OK*
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

#### <a name="get-user-by-query---zero-results"></a>依查詢取得使用者 - 零個結果

##### <a name="request"></a><a name="request-3"></a>要求

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response"></a><a name="response-3"></a>回應

*HTTP/1.1 200 OK*
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

##### <a name="request"></a><a name="request-4"></a>要求

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
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

*HTTP/1.1 200 OK*
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

##### <a name="request"></a><a name="request-5"></a>要求

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

*HTTP/1.1 200 OK*
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

##### <a name="request"></a><a name="request-14"></a>要求

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="request"></a><a name="request-6"></a>要求

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>回應

*HTTP/1.1 204 沒有內容*

### <a name="group-operations"></a>群組作業

* 群組應一律以空的成員清單建立。
* 可依 `displayName` 屬性來查詢群組。
* 群組 PATCH 要求的更新應會在回應中產生 *HTTP 204 沒有內容*。 不建議傳回列出所有成員的本文。
* 不需要支援傳回群組的所有成員。

#### <a name="create-group"></a>建立群組

##### <a name="request"></a><a name="request-7"></a>要求

*POST /Groups HTTP/1.1*
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

##### <a name="request"></a><a name="request-8"></a>要求

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>回應
*HTTP/1.1 200 OK*
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

#### <a name="get-group-by-displayname"></a>依 displayName 取得群組

##### <a name="request"></a><a name="request-9"></a>要求
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>回應

*HTTP/1.1 200 OK*
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

##### <a name="request"></a><a name="request-10"></a>要求

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
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

*HTTP/1.1 204 沒有內容*

### <a name="update-group-add-members"></a>更新群組 [新增成員]

##### <a name="request"></a><a name="request-11"></a>要求

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 沒有內容*

#### <a name="update-group-remove-members"></a>更新群組 [移除成員]

##### <a name="request"></a><a name="request-12"></a>要求

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 沒有內容*

#### <a name="delete-group"></a>刪除群組

##### <a name="request"></a><a name="request-13"></a>要求

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>回應

*HTTP/1.1 204 沒有內容*

### <a name="security-requirements"></a>安全性需求
**TLS 通訊協定版本**

可接受的 TLS 通訊協定版本只有 TLS 1.2 和 TLS 1.3。 不允許其他 TLS 版本。 不允許任何版本的 SSL。 
- RSA 金鑰必須至少有 2,048 位元。
- ECC 金鑰必須至少有 256 位元，並使用已核准的橢圓曲線產生


**金鑰長度**

所有服務都必須使用以長度足夠的密碼編譯金鑰產生的 X.509 憑證，亦即：

**加密套件**

所有服務都必須設定為使用下列加密套件，並且依循以下指定的確切順序。 請注意，如果您只有一個 RSA 憑證，則安裝的 ECDSA 加密套件不會有任何作用。 </br>

TLS 1.2 加密套件的最低標準：

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>IP 範圍
Azure AD 布建服務目前可在任何 Azure IP 範圍下 opperate。 工作正在進行中，以合併服務操作所在的一組 IP 範圍。 一旦合併 IP 範圍清單之後，就會更新此檔。 

## <a name="step-3-build-a-scim-endpoint"></a>步驟 3：建置 SCIM 端點

現在，您已設計結構描述並了解 Azure AD SCIM 實作，接著即可開始開發您的 SCIM 端點。 您可以利用 SCIM 社群所發佈的一些開放原始碼 SCIM 程式庫，而不要從頭開始完全靠自己建置實作。

開放原始碼 .NET Core [參考程式碼](https://aka.ms/SCIMReferenceCode)由 Azure AD 佈建小組所發佈，這項資源可讓您立即開始進行開發。 建置 SCIM 端點後，您會想要加以測試。您可以使用在參考程式碼中提供的 [postman 測試](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)集合，或透過[上方](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)提供的範例要求/回應來執行。  

   > [!Note]
   > 參考程式碼以「原狀」提供，目的是要協助您開始建置 SCIM 端點。 歡迎社群供稿，以協助建置和維護程式碼。

解決方案是由兩個專案所組成：_Microsoft.SCIM_ 和 _Microsoft.SCIM.WebHostSample_。

_Microsoft.SCIM_ 專案是一個程式庫，會定義符合 SCIM 規格的 Web 服務元件。 此專案會宣告 _Microsoft.SCIM.IProvider_ 介面，要求會轉譯為對提供者方法的呼叫，而這些方法會設計為程式在身分識別存放區上運作。

![明細：要求轉譯為對提供者方法的呼叫](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

_Microsoft.SCIM.WebHostSample_ 專案是一個 Visual Studio ASP.NET Core Web 應用程式，以_空白_範本為基礎。 此專案可讓範例程式碼獨立部署，裝載於容器中或在 Internet Information Services 內。 此外也會實作 _Microsoft.SCIM.IProvider_ 介面，將類別保存在記憶體中作為範例身分識別存放區。

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

### <a name="building-a-custom-scim-endpoint"></a>建置自訂 SCIM 端點

SCIM 服務必須具有 HTTP 位址，而其伺服器驗證憑證的根憑證授權單位是下列其中一個名稱：

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

.NET Core SDK 包含可在開發期間使用的 HTTPS 開發憑證，此憑證會在第一次執行期間進行安裝。 ASP.NET Core Web 應用程式根據其執行方式，會接聽不同的連接埠：

* Microsoft.SCIM.WebHostSample： https://localhost:5001
* IIS Express： https://localhost:44359/

若要進一步了解 ASP.NET Core 中的 HTTPS，請使用下列連結：[在 ASP.NET Core 中強制執行 HTTPS](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>處理端點驗證

來自 Azure Active Directory 的要求包括 OAuth 2.0 持有人權杖。 接收要求的任何服務均應為驗證簽發者為預期 Azure Active Directory 租用戶的 Azure Active Directory。

在權杖中，會用 iss 宣告來識別簽發者，例如 `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`。 在此範例中，宣告值的基礎位址 `https://sts.windows.net` 會將 Azure Active Directory 識別為簽發者，而相對位址區段 _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_ 則是獲得權杖的 Azure Active Directory 租用戶的唯一識別碼。

權杖的適用對象是應用程式在資源庫中的應用程式範本識別碼，在單一租用戶中註冊的每個應用程式，可能會收到與 SCIM 要求相同的 `iss` 宣告。 所有自訂應用程式的應用程式範本識別碼都是 _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_。 Azure AD 佈建服務所產生的權杖僅供測試之用。 請勿在生產環境中加以使用。

在範例程式碼中，會使用 Microsoft.AspNetCore.Authentication.JwtBearer 套件來驗證要求。 下列程式碼會強制使用 Azure Active Directory 針對指定的租用戶發出的持有人權杖，對任何服務端點的要求進行驗證：

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

使用提供的 [postman 測試](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint)及使用 localhost 執行本機偵錯時，也必須要有持有人權杖。 範例程式碼會使用 ASP.NET Core 環境在開發階段變更驗證選項，並啟用自我簽署權杖。

若要進一步了解 ASP.NET Core 中的多個環境，請使用下列連結：[在 ASP.NET Core 中使用多個環境](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

下列程式碼會強制使用以自訂金鑰簽署的持有人權杖來驗證對任何服務端點的要求：

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

請將 GET 要求傳送至權杖控制器以取得有效的持有人權杖，_GenerateJSONWebToken_ 方法會負責建立與針對開發而設定的參數相符的權杖：

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>處理使用者的佈建和取消佈建

***範例 1.查詢服務中是否有相符的使用者***

Azure Active Directory 會查詢服務是否有 externalId 屬性值與 Azure AD 中使用者的 mailNickname 屬性值相符的使用者。 查詢會以類似於此範例的超文字傳輸通訊協定 (HTTP) 要求表示，其中，jyoung 是 Azure Active Directory 中使用者的 mailNickname 範例。

>[!NOTE]
> 這只是範例。 並非所有使用者都有 mailNickname 屬性，且使用者的值在目錄中可能不是唯一的。 此外，用於比對的屬性 (在此案例中為 externalId) 可在 [Azure AD 屬性對應](customize-application-attributes.md)中進行設定。

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

在範例程式碼中，要求會轉譯為對服務提供者的 QueryAsync 方法的呼叫。 以下是該方法的簽章： 

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

在範例查詢中，針對具有給定 externalId 屬性值的使用者，傳至 QueryAsync 方法的引數值為：

* parameters.AlternateFilters.Count：1
* parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
* parameters.AlternateFilters.ElementAt(0).ComparisonOperator：ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"

***範例 2.佈建使用者***

如果向 Web 服務查詢是否有 externalId 屬性值與使用者的 mailNickname 值相符的使用者時，回應未傳回任何使用者，Azure Active Directory 就會要求服務佈建與 Azure Active Directory 中的使用者對應的使用者。  以下是這類要求的範例： 

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

在範例程式碼中，要求會轉譯為對服務提供者的 CreateAsync 方法的呼叫。 以下是該方法的簽章： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

在佈建使用者的要求中，資源引數的值會是 Microsoft.SCIM.Core2EnterpriseUser 類別的執行個體 (定義於 Microsoft.SCIM.Schemas 程式庫中)。  如果佈建使用者的要求成功，則方法的實作應會傳回 Microsoft.SCIM.Core2EnterpriseUser 類別的執行個體，並將識別碼屬性的值設定為新佈建之使用者的唯一識別碼。  

***範例 3.查詢使用者的目前狀態*** 

為了更新已知存在於前端為 SCIM 之身分識別存放區中的使用者，Azure Active Directory 會以類似下方的要求向服務要求該使用者的目前狀態，來繼續執行： 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

在範例程式碼中，要求會轉譯為對服務提供者的 RetrieveAsync 方法的呼叫。 以下是該方法的簽章： 

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

***範例 4.查詢要更新之參考屬性的值*** 

如果要更新某個參考屬性，Azure Active Directory 就會查詢服務，以判斷以該服務作為前端之身分識別存放區中參考屬性目前的值，是否已經與 Azure Active Directory 中該屬性的值相符。 對於使用者，以這種方式可查詢目前值的唯一屬性會是管理員屬性。 下列範例說明要判斷使用者物件的管理員屬性目前是否具有某個值的要求：在範例程式碼中，要求會轉譯為對服務提供者的 QueryAsync 方法的呼叫。 提供物件的屬性值作為參數引數的值，如下所示： 
  
* parameters.AlternateFilters.Count：2
* parameters.AlternateFilters.ElementAt(x).AttributePath：「識別碼」
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator：ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue："54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator：ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue："2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0)：「識別碼」
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

在此處，索引 x 的值可能會是 0，而索引 y 的值可能是 1，或 x 值可能是 1 而 y 的值可能是 0，視篩選查詢參數運算式的順序而定。   

***範例 5.Azure AD 對 SCIM 服務發出以更新使用者的要求*** 

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

在範例程式碼中，要求會轉譯為對服務提供者的 UpdateAsync 方法的呼叫。 以下是該方法的簽章： 

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
  
* ResourceIdentifier.Identifier："54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier："urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest as PatchRequest2).Operations.Count：1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName：OperationName.Add
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count：1
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value：2819c223-7f76-453a-919d-413861904646

***範例 6.取消佈建使用者***

若要將使用者從前端為 SCIM 服務的身分識別存放區中取消佈建，Azure AD 會傳送像以下的要求：

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

在範例程式碼中，要求會轉譯為對服務提供者的 DeleteAsync 方法的呼叫。 以下是該方法的簽章： 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

提供作為 resourceIdentifier 引數值的物件，在要取消佈建使用者之要求的範例中，會具有這些屬性值： 

* ResourceIdentifier.Identifier："54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>步驟 4：整合您的 SCIM 端點與 Azure AD SCIM 用戶端

Azure AD 可設定為將已指派的使用者和群組自動佈建至實作 [SCIM 2.0 通訊協定](https://tools.ietf.org/html/rfc7644)之特定設定檔的應用程式。 設定檔的詳細資訊列載於[步驟2：了解 Azure AD SCIM 實作](#step-2-understand-the-azure-ad-scim-implementation)。

洽詢應用程式提供者，或參閱應用程式提供者文件中的相關陳述，以了解是否符合這些需求。

> [!IMPORTANT]
> Azure AD SCIM 實作建置於 Azure AD 使用者佈建服務之上，其設計目的是要讓使用者在 Azure AD 與目標應用程式之間保持同步，並實作一組非常明確的標準作業。 請務必了解這些行為，以了解 Azure AD SCIM 用戶端的行為。 如需詳細資訊，請參閱[佈建週期：初始和增量](how-provisioning-works.md#provisioning-cycles-initial-and-incremental)一節 (位於[佈建運作方式](how-provisioning-works.md)中)。

### <a name="getting-started"></a>開始使用

支援本文所述 SCIM 設定檔的應用程式，可使用 Azure AD 應用程式庫中的「不在資源庫內的應用程式」功能連線到 Azure Active Directory。 連線之後，Azure AD 會每隔 40 分鐘執行一次同步處理程序，此程序會為指派的使用者和群組查詢應用程式的 SCIM 端點，並根據指派詳細資料加以建立或修改。

**若要連接支援 SCIM 的應用程式：**

1. 登入 [Azure Active Directory 入口網站](https://aad.portal.azure.com)。 請注意，您可以註冊[開發人員計劃](https://developer.microsoft.com/office/dev-program)，以取得具有 P2 授權的 Azure Active Directory 免費試用版
2. 在左側窗格中，選取 [企業應用程式]。 此時會顯示所有已設定的應用程式清單，包括已從資源庫新增的應用程式。
3. 選取 [+ 新增應用程式] > [全部] > [非資源庫應用程式]。
4. 輸入您的應用程式名稱，然後選取 [新增] 以建立應用程式物件。 新的應用程式會新增至企業應用程式清單，並在開啟後進入其應用程式管理畫面。

   ![顯示 Azure AD 應用程式庫的螢幕擷取畫面](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Azure AD 應用程式庫*

5. 在應用程式管理畫面中，選取左側面板中的 [佈建]。
6. 在 [佈建模式] 功能表上，選取 [自動]。

   ![範例：應用程式在 Azure 入口網站中的 [佈建] 頁面](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *在 Azure 入口網站中設定佈建*

7. 在 [租用戶 URL] 欄位中，輸入應用程式 SCIM 端點的 URL。 範例： `https://api.contoso.com/scim/`
8. 如果 SCIM 端點需要來自非 Azure AD 簽發者的 OAuth 持有人權杖，那麼便將所需的 OAuth 持有人權杖複製到選擇性 [祕密權杖] 欄位。 如果將此欄位保留空白，則 Azure AD 會在每個要求包含從 Azure AD 簽發的 OAuth 持有人權杖。 應用程式若使用 Azure AD 作為識別提供者，便可以驗證此 Azure AD 簽發的權杖。 
   > [!NOTE]
   > ***不***建議將此欄位保留空白並使用 Azure AD 所產生的權杖。 此選項主要供測試之用。
9. 選取 [測試連線]，讓 Azure Active Directory 嘗試連線至 SCIM 端點。 如果嘗試失敗，將會顯示錯誤資訊。  

    > [!NOTE]
    > [測試連線] 會查詢 SCIM 端點中是否有不存在的使用者，並使用隨機 GUID 作為 Azure AD 設定中選取的比對屬性。 預期的正確回應是 HTTP 200 OK 和空白的 SCIM ListResponse 訊息。

10. 如果嘗試連線至應用程式成功，請選取 [儲存] 以儲存管理員認證。
11. 在 [對應] 區段中，有兩組可選取的[屬性對應](customize-application-attributes.md)：一個用於使用者物件，一個用於群組物件。 選取其中一個以檢閱從 Azure Active Directory 同步處理至應用程式的屬性。 選取為 [比對] 屬性的屬性會用來比對應用程式中的使用者和群組以進行更新作業。 選取 [儲存] 認可任何變更。

    > [!NOTE]
    > 您可以選擇性地藉由停用「群組」對應以停用同步處理群組物件。

12. 在 [設定] 底下，[範圍] 欄位會定義要同步的使用者和群組。 選取 [僅同步處理指派的使用者和群組] (建議選項)，僅同步處理 [使用者和群組] 索引標籤中指派的使用者和群組。
13. 設定完成後，請將 [佈建狀態] 設定為 [開啟]。
14. 選取 [儲存] 以啟動 Azure AD 佈建服務。
15. 如果僅同步處理指派的使用者和群組 (建議選項)，請務必選取 [使用者和群組] 索引標籤，並指派您要同步處理的使用者或群組。

初始週期開始後，您可以在左側面板中選取 [佈建記錄] 以監視進度，其中會顯示佈建服務對您的應用程式執行的所有動作。 如需如何讀取 Azure AD 佈建記錄的詳細資訊，請參閱[關於使用者帳戶自動佈建的報告](check-status-user-account-provisioning.md)。

> [!NOTE]
> 初始週期執行時會比後續的同步處理耗時；在服務執行期間，大約每 40 分鐘會執行一次同步處理。

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>步驟 5：將您的應用程式發佈至 Azure AD 應用程式庫

如果您要建立將由多個租用戶使用的應用程式，您可以在 Azure AD 應用程式庫中加以提供。 這可讓組織輕鬆地探索應用程式和設定佈建。 在 Azure AD 資源庫中發佈您的應用程式並供其他人佈建，是很簡單的作業。 請在 [這裡](../develop/howto-app-gallery-listing.md)查明步驟。 Microsoft 會與您合作，將您的應用程式整合到我們的資源庫中、測試您的端點，以及發行上線[文件](../saas-apps/tutorial-list.md)供客戶使用。 

### <a name="gallery-onboarding-checklist"></a>資源庫上線檢查清單
請依照下列檢查清單操作，以確保您的應用程式可快速上線，且客戶會有順暢的部署體驗。 當您上線至資源庫時，系統將會向您收集資訊。 
> [!div class="checklist"]
> * 支援 [SCIM 2.0 ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) 使用者和群組端點 (只需要一個，但建議兩者都使用)
> * 支援每個租用戶每秒至少有 25 個要求 (必要)
> * 建立工程和支援連絡人，以便在資源庫上線後引導客戶 (必要)
> * 應用程式有 3 個未過期的測試認證可使用 (必要)
> * 支援 OAuth 授權碼授與或長時間存留的權杖，如下所述 (必要)
> * 建立工程和支援連絡點，以便在資源庫上線後支援客戶 (必要)
> * 支援使用單一 PATCH 更新多個群組成員資格 (建議) 
> * 公開記錄您的 SCIM 端點 (建議) 
> * [支援結構描述探索](https://tools.ietf.org/html/rfc7643#section-6) (建議)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>在應用程式庫中佈建連接器的授權
SCIM 規格並未定義 SCIM 特有的驗證和授權機制。 其採用現有的業界標準。 Azure AD 佈建用戶端對於資源庫中的應用程式支援兩種授權方法。 

|授權方法|優點|缺點|支援|
|--|--|--|--|
|使用者名稱和密碼 (Azure AD 不建議使用或加以支援)|易於實作|不安全 - [您的 Pa$$word 無關緊要](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|視個案支援資源庫應用程式。 不支援非資源庫應用程式。|
|長時間存留的持有人權杖|長時間存留的權杖不需要有使用者存在。 在設定佈建時，管理員很容易就能使用這些權杖。|若未使用不安全的方法 (例如電子郵件)，長時間存留的權杖可能難以與管理員共用。 |支援資源庫和非資源庫應用程式。 |
|OAuth 授權碼授與|存取權杖的存留期遠比密碼短，且具有長期持有人權杖所沒有的自動化重新整理機制。  在初始授權期間必須有實際的使用者存在，因而增加了一層責任。 |必須要有使用者存在。 如果使用者離職，權杖就會失效，而必須重新完成授權。|支援資源庫應用程式。 即將支援非資源庫應用程式。|
|OAuth 用戶端認證授與|存取權杖的存留期遠比密碼短，且具有長期持有人權杖所沒有的自動化重新整理機制。 授權碼授與和用戶端認證授與會建立相同類型的存取權杖，因此，這些方法的切換對 API 而言是透明的。  佈建可以完全自動化，且可以無訊息方式要求新的權杖，而不需要使用者介入。 ||不支援資源庫和非資源庫應用程式。 我們已將支援列入待處理項目中。|

> [!NOTE]
> 不建議您在 Azure AD 佈建設定自訂應用程式 UI 中將 [權杖] 欄位保留為空白。 產生的權杖主要供測試之用。

**OAuth 授權碼授與流程：** 佈建服務支援[授權碼授與](https://tools.ietf.org/html/rfc6749#page-24)。 當您提交要在資源庫中發佈應用程式的要求之後，我們的小組會與您合作收集下列資訊：
*  授權 URL：用戶端透過使用者代理程式重新導向對資源擁有者取得授權時所使用的 URL。 使用者會重新導向至此 URL 以授與存取權。 請注意，此 URL 目前無法針對個別的租用戶進行設定。
*  權杖交換 URL：用戶端用來交換存取權杖的授權 (通常是透過用戶端驗證) 的 URL。 請注意，此 URL 目前無法針對個別的租用戶進行設定。
*  用戶端識別碼：授權伺服器會向已註冊的用戶端發出用戶端識別碼，這是一個唯一字串，代表用戶端所提供的註冊資訊。  用戶端識別碼不是秘密，而會向資源擁有者公開，且**不得**單獨用於用戶端驗證。  
*  用戶端密碼：用戶端密碼是由授權伺服器產生的秘密。 此秘密應為只有授權伺服器才知道的唯一值。 

請注意，由於用戶端密碼會公開，因此不支援 OAuth v1。 OAuth v2 則受支援。  

最佳做法 (建議使用，但非必要)：
* 支援多個重新導向 URL。 管理員可從 "portal.azure.com" 和 "aad.portal.azure.com" 設定佈建。 支援多個重新導向 URL 可確保使用者可從任一入口網站授與存取權。
* 支援多個秘密，以確保可順暢更新秘密，而不需要停機。 

**長時間存留的 OAuth 持有人權杖：** 如果您的應用程式不支援 OAuth 授權碼授與流程，您也可以產生長時間存留的 OAuth 持有人權杖，且其存留期可比管理員可用來設定佈建整合的權杖還要久。 此權杖應該是永久的，否則，當權杖過期時，佈建作業將遭到[隔離](application-provisioning-quarantine-status.md)。 此權杖的大小必須低於 1KB。  

如需其他驗證和授權方法的相關資訊，請在 [UserVoice](https://aka.ms/appprovisioningfeaturerequest) 上告訴我們。

### <a name="gallery-go-to-market-launch-check-list"></a>資源庫上市投入檢查清單
為了協助您了解我們的聯合整合並帶動其需求，建議您更新現有的建，並在您的行銷通路中詳細說明此整合。  建議您完成以下這一系列的檢查清單活動，以支援上市投入

* **銷售和客戶支援整備程度。** 請確定您的銷售和支援小組熟知整合功能，且有能力加以宣傳。 對您的銷售和支援小組進行簡報，為其提供常見問題集，並將整合納入您的行銷資料中。 
* **部落格文章和/或新聞稿。** 製作一篇部落格文章或新聞稿，說明聯合整合的性質、優點，以及如何開始使用。 [範例：Imprivata 和 Azure Active Directory 新聞稿](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **社交媒體。** 利用您的社交媒體 (例如 Twitter、Facebook 或 LinkedIn)，向您的客戶推廣整合。 請務必納入 @AzureAD，好讓我們轉推您的貼文。 [範例：Imprivata Twitter 貼文](https://twitter.com/azuread/status/1123964502909779968)
* **行銷網站。** 建立或更新您的行銷頁面 (例如整合頁面、合作夥伴頁面、定價頁面等)，將聯合整合的可用性納入其中。 [範例：Pingboard 整合頁面](https://pingboard.com/org-chart-for)、[Smartsheet 整合頁面](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad)、[Monday.com 定價頁面](https://monday.com/pricing/) 
* **技術文件。** 撰寫說明中心文章或技術文件，向客戶解說應從何著手。 [範例：Envoy + Microsoft Azure Active Directory 整合。](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **客戶通訊。** 透過您的客戶通訊 (每月電子報、電子郵件行銷活動、產品版本資訊)，提醒客戶有新的整合。 

## <a name="related-articles"></a>相關文章

* [SaaS 應用程式的自動化使用者佈建和解除佈建](user-provisioning.md)
* [自訂使用者佈建的屬性對應](customize-application-attributes.md)
* [撰寫屬性對應的運算式](functions-for-customizing-application-data.md)
* [適用於使用者佈建的範圍篩選器](define-conditional-rules-for-provisioning-user-accounts.md)
* [帳戶佈建通知](user-provisioning.md)
* [如何整合 SaaS 應用程式的教學課程清單](../saas-apps/tutorial-list.md)
