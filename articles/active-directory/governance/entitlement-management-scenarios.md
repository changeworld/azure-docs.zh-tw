---
title: 授權管理中的常見方案 - Azure AD
description: 瞭解 Azure 活動目錄授權管理中常見方案應遵循的高級步驟。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190546"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD 授權管理中的常見方案

有幾種方法可以為組織配置授權管理。 但是，如果您剛剛開始，瞭解管理員、目錄擁有者、訪問包管理器、核准者和要求者的常見方案會很有説明。

## <a name="delegate"></a>委派

### <a name="administrator-delegate-management-of-resources"></a>管理員：委託管理資源

1. [觀看視頻：從 IT 委派到部門經理](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [將使用者委派為目錄建立者角色](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>目錄建立者：委託管理資源

- [[建立新的目錄]](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>目錄擁有者：委託管理資源

1. [將共同擁有者添加到目錄](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [將資源添加到目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>目錄擁有者：訪問包的委託管理

1. [觀看視頻：從目錄擁有者委派訪問包管理器](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [委派使用者訪問包管理器角色](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>管理組織中使用者的存取權限

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>訪問包管理器：允許組織中的員工請求訪問資源

1. [建立新的存取套件](entitlement-management-access-package-create.md#start-new-access-package)
1. [添加組、團隊、應用程式或 SharePoint 網站以訪問包](entitlement-management-access-package-create.md#resource-roles)
1. [添加請求策略以允許目錄中的使用者請求訪問](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [指定過期設置](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>要求者：請求訪問資源

1. [登錄到"我的訪問"門戶](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查找訪問包
1. [請求訪問](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>審批人：批准對資源的請求

1. ["我的訪問"門戶中的打開請求](entitlement-management-request-approve.md#open-request)
1. [批准或拒絕訪問請求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>要求者：查看您已有權訪問的資源

1. [登錄到"我的訪問"門戶](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看活動訪問包

## <a name="govern-access-for-users-outside-your-organization"></a>管理組織外部使用者的存取權限

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>管理員：與外部合作夥伴組織協作

1. [瞭解訪問對外部使用者的工作原理](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [查看外部使用者的設置](entitlement-management-external-users.md#settings-for-external-users)
1. [向外部組織添加連接](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>訪問包管理器：與外部合作夥伴組織協作

1. [建立新的存取套件](entitlement-management-access-package-create.md#start-new-access-package)
1. [添加組、團隊、應用程式或 SharePoint 網站以訪問包](entitlement-management-access-package-resources.md#add-resource-roles)
1. [添加請求策略，以允許不在目錄中的使用者請求訪問](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [指定過期設置](entitlement-management-access-package-create.md#lifecycle)
1. [複製連結以請求訪問包](entitlement-management-access-package-settings.md)
1. 將連結發送給外部合作夥伴連絡人合作夥伴，以便與其使用者共用

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>要求者：以外部使用者身份請求訪問資源

1. 查找從連絡人收到的訪問包連結
1. [登錄到"我的訪問"門戶](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [請求訪問](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>審批人：批准對資源的請求

1. ["我的訪問"門戶中的打開請求](entitlement-management-request-approve.md#open-request)
1. [批准或拒絕訪問請求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>要求者：查看您已有權訪問的資源

1. [登錄到"我的訪問"門戶](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看活動訪問包

## <a name="day-to-day-management"></a>日常管理

### <a name="access-package-manager-update-the-resources-for-a-project"></a>訪問包管理器：更新專案的資源

1. [觀看視頻：日常管理：情況已經改變](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 打開訪問包
1. [添加或刪除組、團隊、應用程式或 SharePoint 網站](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>訪問包管理器：更新專案的持續時間

1. [觀看視頻：日常管理：情況已經改變](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 打開訪問包
1. [打開生命週期設置](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [更新過期設置](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>訪問包管理器：更新專案訪問審批方式

1. [觀看視頻：日常管理：情況已經改變](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [打開請求和審批設置的現有策略](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [更新審批設置](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>訪問包管理器：更新專案人員

1. [觀看視頻：日常管理：情況已經改變](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [刪除不再需要存取權限的使用者](entitlement-management-access-package-assignments.md)
1. [打開請求和審批設置的現有策略](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [添加需要存取權限的使用者](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>訪問包管理器：將特定使用者直接分配給訪問包

1. [如果使用者需要不同的生命週期設置，則向訪問包添加新策略](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [直接將特定使用者分配給訪問包](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>分配和報告

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>管理員：查看誰分配到訪問包

1. 打開訪問包
1. [查看分配](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [存檔報告和日誌](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>管理員：查看分配給使用者的資源

1. [查看使用者的訪問包](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [查看使用者的資源配置](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>程式管理

您還可以使用 Microsoft 圖形管理訪問包、目錄、策略、請求和分配。  具有委派`EntitlementManagement.ReadWrite.All`許可權的應用程式具有適當角色的使用者可以調用[授權管理 API](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)。

## <a name="next-steps"></a>後續步驟

- [委派與角色](entitlement-management-delegate.md)
- [請求流程和電子郵件通知](entitlement-management-process.md)
