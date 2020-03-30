---
title: 在 Azure 監視器中移動日誌分析工作區 |微軟文檔
description: 瞭解如何將日誌分析工作區移動到其他訂閱或資源組。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: 9213ddf034e725f6e31c9280d47bd13e4703b3f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659487"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>將日誌分析工作區移動到不同的訂閱或資源組

在本文中，您將學習將 Log Analytics 工作區移動到同一區域中的另一個資源組或訂閱的步驟。 可以瞭解有關通過 Azure 門戶、PowerShell、Azure CLI 或 REST API 移動 Azure 資源的更多資訊。 將[資源移動到新資源組或訂閱](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 

> [!IMPORTANT]
> 不能將工作區移動到其他區域。

## <a name="verify-active-directory-tenant"></a>驗證活動目錄租戶
工作區源和目標訂閱必須存在於同一 Azure 活動目錄租戶中。 使用 Azure PowerShell 驗證兩個訂閱是否具有相同的租戶 ID。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>工作區移動注意事項
工作區中安裝的託管解決方案將隨日誌分析工作區移動操作一起移動。 連接的代理將保持連接狀態，並在移動後將資料發送到工作區。 由於移動操作要求從工作區到任何自動化帳戶沒有連結，因此必須刪除依賴于該連結的解決方案。

必須先刪除的解決方案，然後才能取消連接您的自動化帳戶：

- 更新管理
- 變更追蹤
- 於下班時間開始/停止 VM


### <a name="delete-in-azure-portal"></a>在 Azure 入口網站中刪除
使用以下過程使用 Azure 門戶刪除解決方案：

1. 打開安裝任何解決方案的資源組的功能表。
2. 選擇要刪除的解決方案。
3. 按一下 **"刪除資源**"，然後按一下"**刪除**"以確認要刪除的資源。

![刪除方案](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>使用 PowerShell 刪除

要使用 PowerShell 刪除解決方案，請使用["刪除-AzResource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) Cmdlet"，如以下示例所示：

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules"></a>刪除警報規則
對於 **"開始/停止 VM"** 解決方案，還需要刪除解決方案創建的警報規則。 在 Azure 門戶中使用以下過程來刪除這些規則。

1. 打開 **"監視器"** 功能表，然後選擇 **"警報**"。
2. 按一下 **"管理警報規則**"。
3. 選擇以下三個警報規則，然後按一下 **"刪除**"。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![刪除規則](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>取消連結自動化帳戶
使用以下過程使用 Azure 門戶從工作區取消連結自動化帳戶：

1. 打開 **"自動化帳戶"** 功能表，然後選擇要刪除的帳戶。
2. 在功能表的 **"相關資源**"部分中，選擇 **"連結工作區**"。 
3. 按一下 **"取消連結工作區**"以從自動化帳戶取消連結工作區。

    ![取消連結工作區](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>移動工作區

### <a name="azure-portal"></a>Azure 入口網站
使用以下過程使用 Azure 門戶移動工作區：

1. 打開**日誌分析工作區功能表**，然後選擇工作區。
2. 在 **"概述"** 頁中，按一下 **"資源組**"或"**訂閱**"旁邊的**更改**。
3. 將打開一個新頁面，其中列出了與工作區相關的資源。 選擇要移動到與工作區相同的目標訂閱和資源組的資源。 
4. 選擇目標**訂閱****和資源組**。 如果要將工作區移動到同一訂閱中的另一個資源組，則看不到 **"訂閱"** 選項。
5. 按一下"**確定**"可移動工作區和所選資源。

    ![入口網站](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
要使用 PowerShell 移動工作區，請使用[移動-AzResource，](/powershell/module/AzureRM.Resources/Move-AzureRmResource)如以下示例所示：

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```



> [!IMPORTANT]
> 移動操作後，應重新配置已刪除的解決方案和自動化帳戶連結，以使工作區回到其以前的狀態。


## <a name="next-steps"></a>後續步驟
- 有關資源支援移動的清單，請參閱[移動資源操作支援](../../azure-resource-manager/management/move-support-resources.md)。
