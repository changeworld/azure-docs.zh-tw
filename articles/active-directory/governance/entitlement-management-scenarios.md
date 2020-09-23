---
title: 權利管理的常見案例-Azure AD
description: 瞭解在 Azure Active Directory 權利管理的常見案例中，您應遵循的高階步驟。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8a8f76ce7d46c0a44dd86ef1490c1c74a7992a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90979503"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD 權利管理的常見案例

有幾種方式可讓您為組織設定權利管理。 但是，如果您剛開始使用，瞭解系統管理員、類別目錄擁有者、存取套件管理員、核准者和要求者的常見案例，是很有説明的。

## <a name="delegate"></a>代理人

### <a name="administrator-delegate-management-of-resources"></a>系統管理員：委派資源管理

1. [觀賞影片：從 IT 到部門經理的委派](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [將使用者委派給目錄建立者角色](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>目錄建立者：委派資源管理

- [[建立新的目錄]](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>目錄擁有者：委派資源管理

1. [將共同擁有者新增至目錄](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [將資源新增至目錄](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>目錄擁有者：委派存取套件的管理

1. [觀看影片：從目錄擁有者委派至存取套件管理員](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [委派使用者以存取套件管理員角色](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>管理組織中的使用者存取權

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>存取套件管理員：允許您組織中的員工要求存取資源

1. [建立新的存取套件](entitlement-management-access-package-create.md#start-new-access-package)
1. [新增群組、小組、應用程式或 SharePoint 網站以存取套件](entitlement-management-access-package-create.md#resource-roles)
1. [新增要求原則以允許您目錄中的使用者要求存取權](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [指定到期設定](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>要求者：要求存取資源

1. [登入我的存取權入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 尋找存取套件
1. [要求存取](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>核准者：核准對資源的要求

1. [在我的存取權入口網站中開啟要求](entitlement-management-request-approve.md#open-request)
1. [核准或拒絕存取要求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>要求者：查看您已有存取權的資源

1. [登入我的存取權入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看主動式存取套件

## <a name="govern-access-for-users-outside-your-organization"></a>管理組織外部使用者的存取權

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>系統管理員：與外部夥伴組織共同作業

1. [瞭解外部使用者存取權的運作方式](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [檢查外部使用者的設定](entitlement-management-external-users.md#settings-for-external-users)
1. [將連接新增至外部組織](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>存取套件管理員：與外部夥伴組織共同作業

1. [建立新的存取套件](entitlement-management-access-package-create.md#start-new-access-package)
1. [新增群組、小組、應用程式或 SharePoint 網站以存取套件](entitlement-management-access-package-resources.md#add-resource-roles)
1. [新增要求原則以允許不在您目錄中的使用者要求存取權](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [指定到期設定](entitlement-management-access-package-create.md#lifecycle)
1. [複製連結以要求存取套件](entitlement-management-access-package-settings.md)
1. 將連結傳送給外部夥伴連絡人合作夥伴，以與其使用者共用

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>要求者：以外部使用者的形式要求存取資源

1. 尋找您從連絡人收到的存取套件連結
1. [登入我的存取權入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [要求存取](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>核准者：核准對資源的要求

1. [在我的存取權入口網站中開啟要求](entitlement-management-request-approve.md#open-request)
1. [核准或拒絕存取要求](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>要求者：查看您已有存取權的資源

1. [登入我的存取權入口網站](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 查看主動式存取套件

## <a name="day-to-day-management"></a>日常管理

### <a name="access-package-manager-update-the-resources-for-a-project"></a>存取套件管理員：更新專案的資源

1. [觀賞影片：日常管理：專案已變更](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 開啟存取套件
1. [新增或移除群組、小組、應用程式或 SharePoint 網站](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>存取套件管理員：更新專案的持續時間

1. [觀賞影片：日常管理：專案已變更](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 開啟存取套件
1. [開啟生命週期設定](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [更新到期設定](entitlement-management-access-package-lifecycle-policy.md#lifecycle) 

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>存取套件管理員：更新如何核准專案的存取權

1. [觀賞影片：日常管理：專案已變更](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [開啟現有的要求設定原則](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [更新核准設定](entitlement-management-access-package-approval-policy.md#change-approval-settings-of-an-existing-access-package)

### <a name="access-package-manager-update-the-people-for-a-project"></a>存取套件管理員：更新專案的人員

1. [觀賞影片：日常管理：專案已變更](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [移除不再需要存取的使用者](entitlement-management-access-package-assignments.md)
1. [開啟現有的要求設定原則](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [新增需要存取權的使用者](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>存取套件管理員：直接將特定使用者指派給存取套件

1. [如果使用者需要不同的生命週期設定，請將新的原則新增至存取套件](entitlement-management-access-package-request-policy.md#open-an-existing-access-package-and-add-a-new-policy-of-request-settings)
1. [直接將特定使用者指派給存取套件](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>指派和報表

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>系統管理員：查看具有存取套件指派的人員

1. 開啟存取套件
1. [查看指派](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [封存報表和記錄](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>系統管理員：查看指派給使用者的資源

1. [查看使用者的存取套件](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [查看使用者的資源指派](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>以程式設計方式管理

您也可以使用 Microsoft Graph 來管理存取套件、類別目錄、原則、要求和指派。  具有具有委派許可權之應用程式的適當角色中的使用者 `EntitlementManagement.ReadWrite.All` 可以呼叫 [權利管理 API](https://docs.microsoft.com/graph/tutorial-access-package-api?view=graph-rest-beta )。

## <a name="next-steps"></a>下一步

- [委派與角色](entitlement-management-delegate.md)
- [要求進程和電子郵件通知](entitlement-management-process.md)