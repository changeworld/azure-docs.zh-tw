---
title: 查看 Azure RBAC 變更的活動記錄
description: 查看 azure 角色型存取控制的活動記錄， (Azure RBAC) 過去90天對 Azure 資源的變更。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 3e4d75c59d0e3c97b0900dd72a27d4afbf4015c9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492313"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>查看 Azure RBAC 變更的活動記錄

有時，您需要 azure 角色型存取控制 (Azure RBAC) 變更的相關資訊，例如基於審核或疑難排解用途。 每當有人變更訂用帳戶內的角色指派或角色定義時，變更就會記錄在 [Azure 活動記錄](../azure-monitor/platform/platform-logs-overview.md)中。 您可以查看活動記錄，以查看過去90天的所有 Azure RBAC 變更。

## <a name="operations-that-are-logged"></a>記錄的作業

以下是在活動記錄中記錄的 Azure RBAC 相關作業：

- 建立角色指派
- 刪除角色指派
- 建立或更新自訂角色定義
- 刪除自訂角色定義

## <a name="azure-portal"></a>Azure 入口網站

最簡單的開始方式就是使用 Azure 入口網站檢視活動記錄。 下列螢幕擷取畫面顯示活動記錄中的角色指派作業範例。 它也包含將記錄下載為 CSV 檔案的選項。

![使用入口網站的活動記錄 - 螢幕擷取畫面](./media/change-history-report/activity-log-portal.png)

入口網站中的活動記錄有數個篩選條件。 以下是與 Azure RBAC 相關的篩選準則：

| Filter | 值 |
| --------- | --------- |
| 事件類別目錄 | <ul><li>系統管理</li></ul> |
| 作業 | <ul><li>建立角色指派</li><li>刪除角色指派</li><li>建立或更新自訂角色定義</li><li>刪除自訂角色定義</li></ul> |

如需有關活動記錄的詳細資訊，請參閱 [View 活動記錄以監視資源上的動作](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)。

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
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

如果您使用服務主體來建立角色指派，呼叫端屬性將會是物件識別碼。 您可以使用 [>get-azadserviceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) 來取得服務主體的相關資訊。

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 檢視活動記錄，請使用 [az monitor activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) 命令。

此命令會列出資源群組中的活動記錄，從2月27日起，期待七天：

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

此命令會列出2月27日的授權資源提供者的活動記錄，並持續七天：

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Azure 監視器記錄

[Azure 監視器記錄](../azure-monitor/log-query/log-query-overview.md) 是另一項工具，可用來收集並分析所有 azure 資源的 azure RBAC 變更。 Azure 監視器記錄有下列優點：

- 撰寫複雜的查詢和邏輯
- 整合警示、Power BI 和其他工具
- 儲存更長保留期限的資料
- 交互參考其他記錄，例如安全性、虛擬機器和自訂

開始使用的基本步驟如下：

1. [建立 Log Analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)。

1. 為您的工作區[設定活動記錄分析解決方案](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution)。

1. [檢視活動記錄](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution)。 您可以透過 [ **記錄** ] 選項，快速流覽至 [活動記錄分析的解決方案總覽] 頁面。

   ![入口網站中的 Azure 監視器記錄] 選項](./media/change-history-report/azure-log-analytics-option.png)

1. 選擇性地使用 [Azure 監視器 Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) 來查詢和查看記錄。 如需詳細資訊，請參閱 [開始使用 Azure 監視器記錄查詢](../azure-monitor/log-query/get-started-queries.md)。

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
* [檢視活動記錄中的事件](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [使用 Azure 活動記錄監視訂用帳戶活動](../azure-monitor/platform/platform-logs-overview.md)