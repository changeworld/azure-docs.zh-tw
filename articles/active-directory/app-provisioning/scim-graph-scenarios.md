---
title: 使用 SCIM、Microsoft Graph 和 Azure AD 來布建使用者，並使用資料充實應用程式
description: 使用 SCIM 和 Microsoft Graph 一起布建使用者，並使用其所需的資料豐富您的應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: ba8dec19443bbafa300144b1aa42b3973cce9e6d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655147"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>使用 SCIM 和 Microsoft Graph 一起布建使用者，並使用所需的資料豐富您的應用程式

**目標物件：** 本文的目標是要讓開發人員建立要與 Azure Active Directory (Azure AD) 整合的應用程式。 如果您想要使用已與 Azure AD （例如 Zoom、ServiceNow 和 DropBox）整合的應用程式，您可以跳過這篇文章，並檢查應用程式特定的 [教學](../saas-apps/tutorial-list.md) 課程，或複習布建 [服務的運作方式](./how-provisioning-works.md)。

**常見案例**

Azure AD 提供現成可用的布建服務，以及可在其上建立應用程式的可擴充平臺。 決策樹會概述開發人員如何使用 [SCIM](https://aka.ms/scimoverview) 和 [Microsoft Graph](/graph/overview) 來自動化布建。 

> [!div class="checklist"]
> * 在我的應用程式中自動建立使用者
> * 當使用者不再需要存取時，自動從應用程式移除使用者
> * 整合我的應用程式與多個身分識別提供者以進行布建
> * 使用來自 Microsoft 服務的資料（例如，小組、Outlook 和 Office）來擴充我的應用程式。
> * 在 Azure AD 和 Active Directory 中自動建立、更新及刪除使用者和群組

![SCIM 圖決策樹](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>案例1：在我的應用程式中自動建立使用者
現今，IT 系統管理員會以手動方式建立使用者帳戶或定期將 CSV 檔案上傳至我的應用程式來布建使用者。 此程式對客戶來說相當耗時，而且會讓應用程式的採用速度變慢。 我只需要基本的使用者資訊，例如名稱、電子郵件和 userPrincipalName 以建立使用者。 

**建議**： 
* 如果您的客戶使用不同的 Idp，而且您不想要維護同步處理引擎來與每一個同步處理引擎整合，請支援符合 SCIM 規範的 [/Users](https://aka.ms/scimreferencecode) 端點。 您的客戶將能夠輕鬆地使用此端點來與 Azure AD 布建服務整合，並在需要存取時自動建立使用者帳戶。 您可以建立端點一次，而且它會與所有 Idp 相容。 請參閱下面的範例要求，以瞭解如何使用 SCIM 建立使用者。
* 如果您需要在 Azure AD 中的使用者物件上找到使用者資料，以及在 Microsoft 提供其他資料，請考慮建立使用者布建的 SCIM 端點，並呼叫 Microsoft Graph 來取得其餘資料。 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```

## <a name="scenario-2-automatically-remove-users-from-my-app"></a>案例2：自動從我的應用程式移除使用者
使用「我的應用程式」的客戶會專注于安全性，並具有治理需求，可在員工不再需要時移除帳戶。 如何自動解除布建我的應用程式？

**建議：** 支援符合 SCIM 規範的/Users 端點。 Azure AD 布建服務會在使用者不再需要存取權時，傳送停用和刪除的要求。 建議您同時支援停用和刪除使用者。 請參閱下列範例，以瞭解停用和刪除要求的外觀。 

停用使用者
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
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
刪除使用者
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>案例3：在我的應用程式中自動管理群組成員資格
我的應用程式依賴群組來存取各種資源，而客戶想要重複使用 Azure AD 中的群組。 如何從 Azure AD 匯入群組，並在成員資格變更時讓它們保持更新？  

**建議：** 支援符合 SCIM 規範的/Groups [端點](https://aka.ms/scimreferencecode)。 Azure AD 布建服務將負責建立群組，並在您的應用程式中管理成員資格更新。 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>案例4：使用來自 Microsoft 服務的資料（例如小組、Outlook 和 OneDrive）來擴充我的應用程式
我的應用程式內建在 Microsoft 小組中，並且依賴訊息資料。 此外，我們會將使用者的檔案儲存在 OneDrive 中。 如何使用這些服務和 Microsoft 之間的資料來擴充我的應用程式？

**建議：**[Microsoft Graph](/graph/)是您存取 Microsoft 資料的進入點。 每個工作負載都會以您所需的資料公開 Api。 Microsoft graph 可用於上述案例的 [SCIM](./use-scim-to-provision-users-and-groups.md) 布建。 您可以使用 SCIM，在呼叫圖形時將基本的使用者屬性布建到您的應用程式，以取得您所需的任何其他資料。 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>案例5：追蹤 Microsoft 服務中的變更，例如小組、Outlook 和 Azure AD
我必須能夠追蹤小組和 Outlook 訊息的變更，並即時對其做出反應。 如何才能將這些變更推送至我的應用程式？

**建議：** Microsoft Graph 提供各種資源的 [變更通知](/graph/webhooks) 和 [變更追蹤](/graph/delta-query-overview) 。 請注意變更通知的下列限制：
- 如果事件接收器確認某個事件，但因為任何原因而無法採取動作，則可能會遺失事件。
- 接收變更的順序並不一定是按時間排列的。
- 基於上述原因，變更通知不一定會包含 [資源資料](/graph/webhooks-with-resource-data) ，開發人員通常會使用變更通知以及同步處理案例的變更追蹤。 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>案例6：在 Azure AD 中布建使用者和群組
我的應用程式會在 Azure AD 中建立客戶所需使用者的相關資訊。 這可能是 HR 應用程式，而不是管理雇用、為使用者建立電話號碼的通訊應用程式，或是產生資料的一些其他應用程式，在 Azure AD 中都很有用。 如何? 以該資料填入 Azure AD 中的使用者記錄？ 

**建議** Microsoft graph 會公開/Users 和/Groups 端點，您可以與今日整合以將使用者布建至 Azure AD。 請注意，Azure Active Directory 不支援將這些使用者寫回 Active Directory。 

> [!NOTE]
> Microsoft 提供的布建服務會從 Workday 和 SuccessFactors 這類 HR 應用程式提取資料。 這些整合是由 Microsoft 所建立和管理。 若要將新的 HR 應用程式上架到我們的服務，您可以在 [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)上要求。 

## <a name="related-articles"></a>相關文章

- [查看同步處理 Microsoft Graph 檔](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [整合自訂 SCIM 應用程式與 Azure AD](use-scim-to-provision-users-and-groups.md)