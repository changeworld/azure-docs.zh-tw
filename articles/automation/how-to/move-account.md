---
title: 將 Azure 自動化帳戶移至另一個訂用帳戶
description: 本文說明如何將自動化帳戶移至另一個訂用帳戶。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8454c5a5bb5b44d2a60ae0095a9b82a19ed27c8d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896641"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>將 Azure 自動化帳戶移至另一個訂用帳戶

Azure 自動化可讓您將一些資源移到新的資源群組或訂用帳戶。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 REST API 來移動資源。 若要了解程序，請參閱[將資源移至新的資源群組或訂用帳戶](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

自動化帳戶是您可移動的其中一項資源。 在本文中，您將了解如何將自動化帳戶移至另一個資源或訂用帳戶。 移動自動化帳戶的整體步驟如下：

1. 停用您的功能。
2. 取消您的工作區連結。
3. 移動自動化帳戶。
4. 刪除後重新建立執行身分帳戶。
5. 重新啟用您的功能。

## <a name="remove-features"></a>移除功能

若要取消您的工作區與自動化帳戶的連結，您必須移除工作區中的功能資源：

- 變更追蹤與詳細目錄
- 更新管理
- 於下班時間開始/停止 VM

1. 在 Azure 入口網站中找出資源群組。
2. 尋找每個功能，然後在 [刪除資源] 頁面上選取 [刪除]。

    ![從 Azure 入口網站刪除功能資源的螢幕擷取畫面](../media/move-account/delete-solutions.png)

如果您想要的話，可以使用 [Remove-AzResource](/powershell/module/Az.Resources/Remove-AzResource) Cmdlet 來刪除資源：

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-startstop-vms-during-off-hours"></a>移除停機期間啟動/停止 VM 的警示規則

針對「停機期間啟動/停止 VM」，您也必須移除此功能所建立的警示規則。

1. 在 Azure 入口網站中，移至您的資源群組，然後選取 [監視] > [警示] > [管理警示規則]。

   ![[警示] 頁面的螢幕擷取畫面，其中顯示 [管理警示規則] 的選取項目](../media/move-account/alert-rules.png)

2. 在 [規則] 頁面上，您應會看到該資源群組中所設定的警示清單。 此功能會建立下列規則：

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 一次選取一個規則，然後選取 [刪除] 將其移除。

    ![[規則] 頁面的螢幕擷取畫面，要求確認刪除所選的規則](../media/move-account/delete-rules.png)

    > [!NOTE]
    > 如果您在 [規則] 頁面上看不到任何警示規則，請將 [狀態] 欄位變更為 [已停用] 以顯示停用的警示。 

4. 當您移除警示規則時，您必須移除為「停機期間啟動/停止 VM」通知所建立的動作群組。 在 Azure 入口網站中，選取 [監視] > [警示] > [管理動作群組]。

5. 選取 [StartStop_VM_Notification]。 

6. 在動作群組頁面上，選取 [刪除]。

    ![[動作群組] 頁面的螢幕擷取畫面](../media/move-account/delete-action-group.png)

如果您想要的話，可以使用 [Remove-AzActionGroup](/powershell/module/az.monitor/remove-azactiongroup) Cmdlet 來刪除您的動作群組：

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消您的工作區連結

您現在可以取消您的工作區連結：

1. 在 Azure 入口網站中，選取 [自動化帳戶] > [相關資源] > [連結的工作區]。 

2. 選取 [取消工作區連結]，以取消工作區與自動化帳戶的連結。

    ![取消工作區與自動化帳戶的連結的螢幕擷取畫面](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移動自動化帳戶

您現在可以移動自動化帳戶及其 Runbook。 

1. 在 Azure 入口網站中，瀏覽至您的自動化帳戶的資源群組。 選取 [移動] > [移至另一個訂用帳戶]。

    ![[資源群組] 頁面的螢幕擷取畫面，移至另一個訂用帳戶](../media/move-account/move-resources.png)

2. 選取資源群組中您想要移動的資源。 確保您包含自動化帳戶、Runbook 和 Log Analytics 工作區資源。

## <a name="re-create-run-as-accounts"></a>重新建立執行身分帳戶

[執行身分帳戶](../manage-runas-account.md)可在 Azure Active Directory 中建立服務主體，以向 Azure 資源進行驗證。 當您變更訂用帳戶時，自動化帳戶便不再使用現有的執行身分帳戶。 若要重新建立執行身分帳戶：

1. 移至您在新訂用帳戶中的自動化帳戶，然後選取 [帳戶設定] 之下的 [執行身分帳戶]。 您會看到 [執行身分帳戶] 現在顯示為 [不完整]。

    ![執行身分帳戶的螢幕擷取畫面，其中顯示 [不完整]](../media/move-account/run-as-accounts.png)

2. 藉由選取 [屬性] 頁面上的 [刪除]，一次刪除一個執行身分帳戶。 

    > [!NOTE]
    > 如果您沒有建立或檢視執行身分帳戶的權限，您會看到下列訊息：`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.` 如需詳細資訊，請參閱[設定執行身分帳戶所需的權限](../manage-runas-account.md#permissions)。

3. 刪除執行身分帳戶之後，選取 [Azure 執行身分帳戶] 之下的 [建立]。 

4. 在 [新增 Azure 執行身分帳戶] 頁面上，選取 [建立]，以建立執行身分帳戶和服務主體。 

5. 使用 Azure 傳統執行身分帳戶重複上述步驟。

## <a name="enable-features"></a>啟用功能

重新建立執行身分帳戶之後，您必須重新啟用您在移動前移除的功能：

1. 若要開啟「變更追蹤和清查」，請在您的自動化帳戶中選取 [變更追蹤和清查]。 選擇您所移至的 Log Analytics 工作區，然後選取 [啟用]。

2. 針對更新管理重複步驟 1。

    ![在已移動的自動化帳戶中重新啟用功能的螢幕擷取畫面](../media/move-account/reenable-solutions.png)

3. 如果您已連線到現有的 Log Analytics 工作區，則會顯示已啟用您功能的機器。 若要開啟「停機期間啟動/停止 VM」功能，您必須將其重新啟用。 在 [相關資源] 之下，選取 [啟動/停止 VM] > [深入了解並啟用解決方案] > [建立] 來開始部署。

4. 在 [新增解決方案] 頁面上，選擇您的 Log Analytics 工作區和自動化帳戶。

    ![[新增解決方案] 功能表的螢幕擷取畫面](../media/move-account/add-solution-vm.png)

5. 如[停機期間啟動/停止 VM 概觀](../automation-solution-vm-management.md)所述設定功能。

## <a name="verify-the-move"></a>確認移動

當移動完成時，請確認下列功能已啟用。 

|功能|測試|疑難排解|
|---|---|---|
|Runbook|Runbook 可以成功執行並連線至 Azure 資源。|[對 Runbook 進行疑難排解](../troubleshoot/runbooks.md)
|原始檔控制|您可以在原始檔控制存放庫上執行手動同步處理。|[原始檔控制整合](../source-control-integration.md)|
|變更追蹤和清查|確認您看到來自電腦的目前清查資料。|[針對變更追蹤進行疑難排解](../troubleshoot/change-tracking.md)|
|更新管理|確認您看到您的電腦，而且其狀況良好。</br>執行測試軟體更新部署。|[針對更新管理進行疑難排解](../troubleshoot/update-management.md)|
|共用資源|確認您看到所有共用的資源，例如[認證](../shared-resources/credentials.md)和[變數](../shared-resources/variables.md)。|

## <a name="next-steps"></a>後續步驟

若要了解如何在 Azure 中移動資源，請參閱 [在 Azure 中移動資源](../../azure-resource-manager/management/move-support-resources.md)。
