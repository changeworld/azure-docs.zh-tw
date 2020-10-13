---
title: 檢視服務提供者活動
description: 客戶可以透過 Azure 委派的資源管理來查看已記錄的活動，以查看服務提供者所執行的動作。
ms.date: 10/12/2020
ms.topic: how-to
ms.openlocfilehash: 8ec9d49de953a0fed3a5afc669e9297148aadf32
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974306"
---
# <a name="view-service-provider-activity"></a>檢視服務提供者活動

有 [Azure Lighthouse](../overview.md) 委派訂用帳戶的客戶可以 [查看 Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md) 資料，以查看所採取的所有動作。 這可讓客戶完全掌握服務提供者透過 [Azure 委派的資源管理](../concepts/azure-delegated-resource-management.md)所執行的作業，以及客戶本身 Azure Active Directory (Azure AD) 租使用者中的使用者所執行的作業。

> [!TIP]
> 我們也提供 Azure 原則內建原則定義，以 [限制對特定管理](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Lighthouse/AllowCertainManagingTenantIds_Deny.json) 租使用者的委派，以及將 [範圍委派給管理租](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json)使用者。 如需詳細資訊，請參閱 [在您的環境中審核委派](view-manage-service-providers.md#audit-delegations-in-your-environment)。

## <a name="view-activity-log-data"></a>檢視活動記錄資料

您可以從 Azure 入口網站中的 [**監視**] 功能表[查看活動記錄](../../azure-monitor/platform/activity-log.md#view-the-activity-log)。 若要將結果限制為特定的訂用帳戶，請使用篩選器來選取特定的訂用帳戶。 您也可以透過程式設計方式來 [查看和取得活動記錄事件](../../azure-monitor/platform/activity-log.md#view-the-activity-log) 。

> [!NOTE]
> 服務提供者租使用者中的使用者可以在客戶租使用者中查看委派訂用帳戶的活動記錄結果（如果他們被授與「 [讀者](../../role-based-access-control/built-in-roles.md#reader) 」角色 (或另一個內建角色），而該訂用帳戶會在該訂用帳戶上線至 Azure Lighthouse 時) 。

在活動記錄中，您會看到作業的名稱和其狀態，以及執行的日期和時間。 依資料行 **起始的事件** 會顯示執行作業的使用者，無論是在服務提供者租使用者中的使用者是透過 Azure Lighthouse，還是客戶本身租使用者中的使用者。 請注意，會顯示使用者的名稱，而不是使用者已指派給該訂用帳戶的租使用者或角色。

記錄的活動可在過去90天的 Azure 入口網站中使用。 若要瞭解如何儲存此資料超過90天，請參閱 [在 Log Analytics 工作區中收集和分析 Azure 活動記錄](../../azure-monitor/platform/activity-log.md)。

> [!NOTE]
> 服務提供者中的使用者會出現在活動記錄中，但這些使用者及其角色指派不會顯示在 **存取控制 (IAM) ** 或透過 api 來抓取角色指派資訊時。

## <a name="set-alerts-for-critical-operations"></a>設定重要作業的警示

為了留意服務提供者 (或您自己的租使用者) 中的使用者所執行的重要作業，我們建議您建立 [活動記錄警示](../../azure-monitor/platform/activity-log-alerts.md)。 例如，您可能想要追蹤訂用帳戶的所有系統管理動作，或在刪除特定資源群組中的任何虛擬機器時通知您。 當您建立警示時，它們會包含客戶本身租使用者中的使用者以及任何管理租使用者中的使用者所執行的動作。

如需詳細資訊，請參閱 [建立和管理活動記錄警示](../../azure-monitor/platform/alerts-activity-log.md)。

## <a name="create-log-queries"></a>建立記錄查詢

您可以建立查詢來分析已記錄的活動，或是專注于特定的專案。 例如，可能會需要您針對在訂用帳戶上執行的所有系統管理層級動作報告。 您可以建立查詢，只篩選這些動作，並依使用者、日期或其他值來排序結果。

如需詳細資訊，請參閱 [Azure 監視器中的記錄查詢總覽](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure 監視器](../../azure-monitor/index.yml)。
- 瞭解如何在 Azure 入口網站中 [查看和管理服務提供者供應](view-manage-service-providers.md) 專案。
