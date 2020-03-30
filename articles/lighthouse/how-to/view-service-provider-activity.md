---
title: 檢視服務提供者活動
description: 客戶可以查看記錄的活動，以查看服務提供者通過 Azure 委派的資源管理執行的操作。
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649631"
---
# <a name="view-service-provider-activity"></a>檢視服務提供者活動

已委派 Azure 委派資源管理訂閱的客戶可以查看[Azure 活動日誌](../../azure-monitor/platform/platform-logs-overview.md)資料以查看所有執行的操作。 這使客戶能夠全面瞭解服務提供者通過 Azure 委派資源管理執行的操作，以及客戶自己的 Azure 活動目錄 （Azure AD） 租戶中使用者執行的操作。

> [!TIP]
> 我們還提供了 Azure 策略內置策略定義，用於審核作用域委派給管理租戶。 有關詳細資訊，請參閱[環境中的審核授權](view-manage-service-providers.md#audit-delegations-in-your-environment)。

## <a name="view-activity-log-data"></a>查看活動日誌資料

您可以在 Azure 門戶中的 **"監視器"** 功能表[中查看活動日誌](../../azure-monitor/platform/activity-log-view.md)。 要將結果限制為特定訂閱，請使用篩選器選擇特定訂閱。 您還可以以程式設計方式[查看和檢索活動日誌事件](../../azure-monitor/platform/activity-log-view.md)。

> [!NOTE]
> 服務提供者租戶中的使用者可以查看客戶租戶中委派訂閱的活動日誌結果，如果該訂閱已註冊用於 Azure 委派的資源管理，則他們被授予[Reader](../../role-based-access-control/built-in-roles.md#reader)角色（或包含 Reader 存取權限的另一個內置角色）。

在活動日誌中，您將看到操作的名稱及其狀態，以及執行操作的日期和時間。 **由列啟動的事件**顯示執行該操作的使用者，無論是服務提供者租戶中通過 Azure 委派的資源管理執行的使用者，還是客戶自己的租戶中的使用者。 請注意，將顯示使用者的名稱，而不是租戶或已為該訂閱分配的使用者的角色。

記錄的活動在過去 90 天內在 Azure 門戶中可用。 要瞭解如何存儲此資料超過 90 天，請參閱[在日誌分析工作區中收集和分析 Azure 活動日誌](../../azure-monitor/platform/activity-log-collect.md)。

> [!NOTE]
> 服務提供者的使用者將顯示在活動日誌中，但這些使用者及其角色指派未顯示在**存取控制 （IAM）** 中或通過 API 檢索角色指派資訊時。

## <a name="set-alerts-for-critical-operations"></a>為關鍵操作設置警報

為了瞭解服務提供者（或您自己的租戶中的使用者）正在執行的關鍵操作，我們建議創建[活動日誌警報](../../azure-monitor/platform/activity-log-alerts.md)。 例如，您可能希望跟蹤訂閱的所有管理操作，或者在刪除特定資源組中的任何虛擬機器時收到通知。 創建警報時，這些警報將包括客戶自己的租戶中的使用者以及任何管理租戶執行的操作。

有關詳細資訊，請參閱[創建和管理活動日誌警報](../../azure-monitor/platform/alerts-activity-log.md)。

## <a name="create-log-queries"></a>創建日誌查詢

您可以創建查詢來分析記錄的活動或關注特定專案。 例如，審核可能要求您報告對訂閱執行的所有管理級操作。 您可以創建查詢以僅篩選這些操作，並按使用者、日期或其他值對結果進行排序。

有關詳細資訊，請參閱[Azure 監視器 中的日誌查詢概述](../../azure-monitor/log-query/log-query-overview.md)。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[Azure 監視器](../../azure-monitor/index.yml)的更多。
- 瞭解如何在 Azure 門戶中[查看和管理服務提供者產品/服務](view-manage-service-providers.md)。