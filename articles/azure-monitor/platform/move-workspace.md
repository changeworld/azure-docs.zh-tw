---
title: 移動 Azure 監視器中的 Log Analytics 工作區 |Microsoft Docs
description: 瞭解如何將 Log Analytics 工作區移至另一個訂用帳戶或資源群組。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/13/2019
ms.openlocfilehash: d59fb0dc39103119edbc4096b506c588c38cece4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282865"
---
# <a name="move-a-log-analytics-workspace-to-different-subscription-or-resource-group"></a>將 Log Analytics 工作區移至不同的訂用帳戶或資源群組

在本文中，您將瞭解將 Log Analytics 工作區移到相同區域中的另一個資源群組或訂用帳戶的步驟。 您可以深入瞭解如何透過 Azure 入口網站、PowerShell、Azure CLI 或 REST API 來移動 Azure 資源。 [將資源移至新的資源群組或訂用](../../azure-resource-manager/management/move-resource-group-and-subscription.md)帳戶。 

> [!IMPORTANT]
> 您無法將工作區移到不同的區域。

## <a name="verify-active-directory-tenant"></a>確認 Active Directory 租使用者
工作區來源和目的地訂用帳戶必須存在於相同的 Azure Active Directory 租使用者中。 使用 Azure PowerShell 來確認兩個訂用帳戶都有相同的租使用者識別碼。

``` PowerShell
(Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
(Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
```

## <a name="workspace-move-considerations"></a>工作區移動考慮
在工作區中安裝的受控解決方案會隨著 Log Analytics 工作區移動作業而移動。 連線的代理程式會保持連線，並在移動之後繼續將資料傳送到工作區。 因為移動作業需要來自工作區的連結服務，所以必須移除依賴該連結的解決方案，以允許工作區移動。

在您可以取消連結自動化帳戶之前，必須先移除的方案：

- 更新管理
- 變更追蹤
- 於下班時間開始/停止 VM
- Azure 資訊安全中心

>[!IMPORTANT]
> **Azure Sentinel 客戶**
> - 在工作區部署之後，Azure Sentinel **目前即不支援**將該工作區移至其他資源群組或訂用帳戶。 
> - 如果您已移動工作區，請停用 **Analytics** 下的所有作用中規則，然後在五分鐘後重新啟用。 不過在大部分的情況下，這種做法應該是有效的，但請注意此方法不受支援，而且您需自行承擔風險。
> 
> **警示**
> - 所有警示都必須在移動之後重新建立，因為這些許可權是以工作區的 Azure 資源識別碼為基礎，而且會隨著工作區移動而變更。 

### <a name="delete-solutions-in-azure-portal"></a>在 Azure 入口網站中刪除解決方案
使用下列程式，利用 Azure 入口網站移除解決方案：

1. 開啟已安裝任何解決方案之資源群組的功能表。
2. 選取要移除的解決方案。
3. 按一下 [ **刪除資源** ]，然後按一下 [ **刪除**] 以確認要移除的資源。

![刪除方案](media/move-workspace/delete-solutions.png)

### <a name="delete-using-powershell"></a>使用 PowerShell 刪除

若要使用 PowerShell 移除解決方案，請使用 [get-azresource](/powershell/module/az.resources/remove-azresource?view=azps-2.8.0) Cmdlet，如下列範例所示：

``` PowerShell
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates(<workspace-name>)" -ResourceGroupName <resource-group-name>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM(<workspace-name>)" -ResourceGroupName <resource-group-name>
```

### <a name="remove-alert-rules-for-startstop-vms-solution"></a>移除啟動/停止 Vm 解決方案的警示規則
若要移除 **啟動/停止 vm** 解決方案，您也必須移除解決方案所建立的警示規則。 使用 Azure 入口網站中的下列程式移除這些規則。

1. 開啟 [ **監視** ] 功能表，然後選取 [ **警示**]。
2. 按一下 [ **管理警示規則**]。
3. 選取下列三個警示規則，然後按一下 [ **刪除**]。

   - AutoStop_VM_Child
   - ScheduledStartStop_Parent
   - SequencedStartStop_Parent

    ![刪除規則](media/move-workspace/delete-rules.png)

## <a name="unlink-automation-account"></a>取消連結自動化帳戶
使用下列程式，利用 Azure 入口網站將自動化帳戶與工作區取消連結：

1. 開啟 [ **自動化帳戶** ] 功能表，然後選取要移除的帳戶。
2. 在功能表的 [ **相關資源** ] 區段中，選取 [ **連結的工作區**]。 
3. 按一下 [ **取消連結工作區** ]，將工作區與您的自動化帳戶取消連結。

    ![取消連結工作區](media/move-workspace/unlink-workspace.png)

## <a name="move-your-workspace"></a>移動您的工作區

### <a name="azure-portal"></a>Azure 入口網站
使用下列程式，利用 Azure 入口網站來移動您的工作區：

1. 開啟 **Log Analytics 工作區** 功能表，然後選取您的工作區。
2. 在 [**總覽**] 頁面中，按一下 [**資源群組**或**訂**用帳戶] 旁的 [**變更**]。
3. 新頁面隨即開啟，其中包含與工作區相關的資源清單。 選取要移至相同目的地訂用帳戶和資源群組的資源，以作為工作區。 
4. 選取目的地 **訂** 用帳戶和 **資源群組**。 如果您要將工作區移到相同訂用帳戶中的另一個資源群組，您將不會看到 **訂** 用帳戶選項。
5. 按一下 **[確定]** 以移動工作區和選取的資源。

    ![螢幕擷取畫面會顯示 Log Analytics 工作區中的 [總覽] 窗格，其中包含變更資源群組和訂用帳戶名稱的選項。](media/move-workspace/portal.png)

### <a name="powershell"></a>PowerShell
若要使用 PowerShell 移動您的工作區，請使用 [get-azresource](/powershell/module/AzureRM.Resources/Move-AzureRmResource) ，如下列範例所示：

``` PowerShell
Move-AzResource -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup01/providers/Microsoft.OperationalInsights/workspaces/MyWorkspace" -DestinationSubscriptionId "00000000-0000-0000-0000-000000000000" -DestinationResourceGroupName "MyResourceGroup02"
```

> [!IMPORTANT]
> 移動作業之後，您應該重新設定移除的解決方案和自動化帳戶連結，讓工作區回到先前的狀態。


## <a name="next-steps"></a>後續步驟
- 如需支援移動的資源清單，請參閱 [移動資源的作業支援](../../azure-resource-manager/management/move-support-resources.md)。
