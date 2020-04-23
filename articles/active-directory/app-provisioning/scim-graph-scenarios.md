---
title: 使用 SCIM、Microsoft 圖形和 Azure AD 預配服務來預配使用者並使用所需的數據來豐富應用程式 |微軟文件
description: 使用 SCIM 和 Microsoft 圖形一起為使用者提供和豐富應用程式所需的數據。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 134237b66a803abaf07621112e3a4a518a3ae8a7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82087617"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>將 SCIM 和 Microsoft 圖形一起使用,為使用者提供所需的資料並豐富應用程式

**目標受眾:** 本文件面向建構與 Azure AD 整合的應用程式的開發人員。 對於希望整合現有應用程式(如 Zoom、ServiceNow 和 DropBox)的其他應用程式,您可以跳過此內容並查看應用程式特定的[教程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)。 

**常見案例**

> [!div class="checklist"]
> * 在我的應用程式中自動建立使用者
> * 當使用者不再具有存取權限時,自動從我的應用程式中刪除他們
> * 將我的應用程式與多個識別提供者整合以進行預配
> * 使用來自 Microsoft 服務(如 Sharepoint、Outlook 和 Office)的數據豐富應用程式。
> * 在 Azure AD 與活動目錄中自動建立、更新和移除使用者和群組

![SCIM 圖形決策樹](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>方案 1: 在我的應用程式中自動建立使用者
如今,每當有人需要訪問或定期上傳 CSV 檔時,IT 管理員都會在我的應用程式中手動創建使用者帳戶。 這個過程對於客戶來說非常耗時,並減慢了我的應用程式的採用速度。 我只需要基本[使用者](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)資訊,如姓名、電子郵件和用戶原則名稱,以創建使用者。 此外,我的客戶使用各種 IdP,我沒有資源來維護與每個 IdP 的同步引擎和自訂整合。 

**建議**: 支援符合 SCIM[的 /使用者](https://aka.ms/scimreferencecode)終結點。 您的客戶將能夠輕鬆地使用此終結點與 Azure AD 預配服務整合,並在需要存取時自動創建使用者帳戶。 您可以構建終結點一次,它將與所有 IdP 相容,而無需維護同步引擎。 請查看下面的示例請求,瞭解如何創建使用者。

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
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>機制 2: 自動從我的應用程式移除使用者
使用我的應用程式的客戶以安全性為中心,並且具有在員工不再需要帳戶時刪除帳戶的治理要求。 如何自動從應用程式取消預配?

**建議:** 支援符合 SCIM/用戶終結點。 Azure AD 預配服務將發送請求以禁用和刪除使用者不應再具有訪問許可權。 我們建議支援禁用和刪除使用者。 有關禁用和刪除請求的外觀,請參閱下面的示例。 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>配置 3:自動管理應用程式中的群組成員身份
我的應用程式依賴於組來訪問各種資源,客戶希望重用他們在 Azure AD 中具有的組。 如何從 Azure AD 匯入組,並在成員資格更改時更新它們?  

**建議:** 支援符合 SCIM 的 /群組[終結點](https://aka.ms/scimreferencecode)。 Azure AD 預配服務將負責在應用程式中創建組和管理成員身份更新。 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>方案 4:使用來自 Microsoft 服務(如團隊、Outlook 和 OneDrive)的數據豐富我的應用。
我的應用程式內置於 Microsoft Teams 中,並且依賴於消息數據。 此外,我們還在 OneDrive 中存儲使用者檔。 如何使用這些服務和整個 Microsoft 的數據來豐富應用程式?

**建議:**[Microsoft 圖形](https://docs.microsoft.com/graph/)是訪問 Microsoft 數據的切入點。 每個工作負載都使用所需的數據公開 API。 Microsoft 圖形可以與上述方案的[SCIM 預配](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)一起使用。 您可以使用 SCIM 將基本使用者屬性預配到應用程式中,同時調用圖形以獲取所需的任何其他資料。 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>方案 5:跟蹤 Microsoft 服務中的更改,如團隊、Outlook 和 Azure AD。
我需要能夠跟蹤對團隊和 Outlook 消息的更改,並即時回應它們。 如何將這些更改推送到我的應用程式中?

**建議:** Microsoft 圖形為各種資源提供[更改通知](https://docs.microsoft.com/graph/webhooks)和更改跟蹤。 請注意變更通知的以下限制:
- 如果事件接收器確認事件,但由於任何原因未能對事件執行操作,則事件可能會丟失
- 如果事件接收器確認事件,但由於任何原因未能對事件執行操作,則事件可能會丟失
- 更改通知並不總是包含[資源數據](https://docs.microsoft.com/graph/webhooks-with-resource-data)原因,開發人員通常使用更改通知以及同步方案的更改跟蹤。 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>方案 6:在 Azure AD 中預配使用者和組。
我的應用程式創建有關客戶在 Azure AD 中需要的使用者的資訊。 這可能是一個 HR 應用程式,而不是管理招聘、為使用者創建電話號碼的通信應用,或者生成在 Azure AD 中有價值的數據的其他應用。 如何使用該資料填充 Azure AD 中的使用者記錄? 

**推薦**Microsoft 圖形公開 /使用者和 /組終結點,您可以與今天集成以預配使用者到 Azure AD 中。 請注意,Azure 活動目錄不支援將這些使用者重新寫入活動目錄。 

> [!NOTE]
> Microsoft 具有一種調配服務,可從 HR 應用程式(如工作日和成功因素)中提取數據。 這些整合由 Microsoft 構建和管理。 對於將新的 HR 應用程式載入我們的服務,您可以在[UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests)上申請。 

## <a name="related-articles"></a>相關文章

- [檢視同步 Microsoft 圖形文件](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [將自訂 SCIM 應用與 Azure AD 整合](use-scim-to-provision-users-and-groups.md)
