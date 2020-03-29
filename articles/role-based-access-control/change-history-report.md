---
title: 查看 Azure RBAC 更改的活動日誌
description: 查看過去 90 天內 Azure 資源對 Azure 資源進行的活動日誌，這些存取控制 （Azure RBAC） 更改。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161756"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>查看 Azure RBAC 更改的活動日誌

有時，您需要有關基於 Azure 角色的存取控制 （Azure RBAC） 更改的資訊，例如用於審核或故障排除目的。 每當有人更改訂閱中的角色指派或角色定義時，這些更改將記錄在 Azure[活動日誌](../azure-monitor/platform/platform-logs-overview.md)中。 您可以查看活動日誌以查看過去 90 天內的所有 Azure RBAC 更改。

## <a name="operations-that-are-logged"></a>記錄的作業

以下是在活動日誌中記錄的 Azure RBAC 相關操作：

- 建立角色指派
- 刪除角色指派
- 建立或更新自訂角色定義
- 刪除自訂角色定義

## <a name="azure-portal"></a>Azure 入口網站

開始使用最簡單的方式是使用 Azure 入口網站檢視活動記錄。 以下螢幕截圖顯示了活動日誌中角色指派操作的示例。 它還包括將日誌下載為 CSV 檔的選項。

![使用入口網站的活動記錄 - 螢幕擷取畫面](./media/change-history-report/activity-log-portal.png)

入口網站中的活動記錄有數個篩選條件。 下面是與 Azure RBAC 相關的篩選器：

| Filter | 值 |
| --------- | --------- |
| 事件類別目錄 | <ul><li>管理</li></ul> |
| 作業 | <ul><li>建立角色指派</li><li>刪除角色指派</li><li>建立或更新自訂角色定義</li><li>刪除自訂角色定義</li></ul> |

有關活動日誌的詳細資訊，請參閱[查看活動日誌以監視對資源的操作](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)。

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

若要使用 Azure PowerShell 檢視活動記錄，請使用 [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) 命令。

此命令列出過去 7 天在訂用帳戶中的所有角色指派變更：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

此命令列出過去 7 天在資源群組中的所有角色定義變更：

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

此命令列出過去 7 天在訂用帳戶中的所有角色指派及角色定義變更，並且以清單顯示結果：

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 檢視活動記錄，請使用 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令。

此命令列出從 2 月 27 日開始的資源組中的活動日誌，展望 7 天：

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

此命令列出從 2 月 27 日開始的授權資來源提供者的活動日誌，展望 7 天：

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure 監視器記錄

[Azure 監視器日誌](../log-analytics/log-analytics-overview.md)是另一個工具，可用於收集和分析所有 Azure 資源的 Azure RBAC 更改。 Azure 監視器日誌具有以下優點：

- 撰寫複雜的查詢和邏輯
- 整合警示、Power BI 和其他工具
- 儲存更長保留期限的資料
- 交互參考其他記錄，例如安全性、虛擬機器和自訂

開始使用的基本步驟如下：

1. [創建日誌分析工作區](../azure-monitor/learn/quick-create-workspace.md)。

1. 為您的工作區[設定活動記錄分析解決方案](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。

1. [檢視活動記錄](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution)。 導航到活動日誌分析解決方案概述頁面的快速方法是按一下 **"日誌"** 選項。

   ![門戶中的 Azure 監視器日誌選項](./media/change-history-report/azure-log-analytics-option.png)

1. 可以選擇使用[Azure 監視器日誌分析](../azure-monitor/log-query/get-started-portal.md)來查詢和查看日誌。 有關詳細資訊，請參閱[開始使用 Azure 監視器日誌查詢](../azure-monitor/log-query/get-started-queries.md)。

以下查詢會傳回由目標資源提供者所組織的新角色指派：

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

以下查詢會傳回以圖表顯示的角色指派變更：

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![使用進階分析入口網站的活動記錄 - 螢幕擷取畫面](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>後續步驟
* [檢視活動記錄中的事件](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [使用 Azure 活動記錄監視訂用帳戶活動](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
