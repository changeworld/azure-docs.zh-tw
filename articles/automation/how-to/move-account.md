---
title: 將您的 Azure 自動化帳戶移至另一個訂用帳戶
description: 本文說明如何將您的自動化帳戶移至另一個訂用帳戶。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bfb2f2d1d0f6a0d11784847344cd3dbcafdb0959
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82900986"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>將您的 Azure 自動化帳戶移至另一個訂用帳戶

Azure 自動化可讓您將一些資源移到新的資源群組或訂用帳戶。 您可以透過 Azure 入口網站、PowerShell、Azure CLI 或 REST API 來移動資源。 若要深入瞭解此程式，請參閱[將資源移至新的資源群組或訂用](../../azure-resource-manager/management/move-resource-group-and-subscription.md)帳戶。

自動化帳戶是您可以移動的其中一個資源。 在本文中，您將瞭解如何將自動化帳戶移至另一個資源或訂用帳戶。 移動自動化帳戶的高階步驟如下：

1. 移除您的解決方案。
2. 取消連結您的工作區。
3. 移動自動化帳戶。
4. 刪除並重新建立執行身分帳戶。
5. 重新啟用您的解決方案。

>[!NOTE]
>在本文中，您會使用 Azure PowerShell Az 模組。 您仍然可以使用 AzureRM 模組。 若要深入瞭解 Az 模組和 AzureRM 相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="remove-solutions"></a>移除解決方案

若要取消您的工作區與自動化帳戶的連結，您必須從工作區中移除這些解決方案：

- 變更追蹤與詳細目錄
- 更新管理
- 於下班時間啟動/停止虛擬機器

1. 在 Azure 入口網站中找出資源群組。
2. 尋找每個解決方案，然後在 [**刪除資源**] 頁面上選取 [**刪除**]。

    ![從 Azure 入口網站刪除解決方案的螢幕擷取畫面](../media/move-account/delete-solutions.png)

如果您想要的話，可以使用[get-azresource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) Cmdlet 來刪除解決方案：

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>移除「于下班時間啟動/停止 Vm」解決方案的警示規則

針對此解決方案，您也需要移除解決方案所建立的警示規則。

1. 在 [Azure 入口網站中，移至您的資源群組，然後選取 [**監視** > ] [**警示** > ] [**管理警示規則**]。

   ![[警示] 頁面的螢幕擷取畫面，其中顯示管理警示規則的選取範圍](../media/move-account/alert-rules.png)

2. 在 [**規則**] 頁面上，您應該會看到該資源群組中所設定的警示清單。 解決方案會建立下列規則：

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 一次選取一個規則，然後選取 [**刪除**] 將它們移除。

    ![[規則] 頁面的螢幕擷取畫面，要求已選取規則的刪除確認](../media/move-account/delete-rules.png)

    > [!NOTE]
    > 如果您沒有在 [**規則**] 頁面上看到任何警示規則，請將 [**狀態**] 欄位變更為 [**停用**] 以顯示已停用的警示 您可能已停用它們。

4. 當您移除警示規則時，您必須移除針對「于下班時間啟動/停止 Vm」解決方案通知所建立的動作群組。 在 [Azure 入口網站中，選取 [**監視** > ] [**警示** > ] [**管理動作群組**]。

5. 選取 [ **StartStop_VM_Notification**]。 

6. 在 [動作群組] 頁面上，選取 [**刪除**]。

    ![[動作群組] 頁面的螢幕擷取畫面](../media/move-account/delete-action-group.png)

如果您想要的話，可以使用[AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) Cmdlet 來刪除動作群組：

```azurepowershell-interactive
Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消連結您的工作區

現在您可以取消連結您的工作區：

1. 在 [Azure 入口網站中，選取 [**自動化帳戶** > **相關資源** > ] [**連結的工作區**]。 

2. 選取 [**取消連結工作區**]，將工作區從您的自動化帳戶取消連結。

    ![從自動化帳戶取消連結工作區的螢幕擷取畫面](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移動您的自動化帳戶

您現在可以移動自動化帳戶及其 runbook。 

1. 在 [Azure 入口網站中，流覽至您的自動化帳戶的資源群組。 選取 [**移** > **至另一個訂**用帳戶]。

    ![[資源群組] 頁面的螢幕擷取畫面，移至另一個訂用帳戶](../media/move-account/move-resources.png)

2. 選取您想要移動的資源群組中的資源。 請確定您包含自動化帳戶、runbook 和 Log Analytics 工作區資源。

## <a name="re-create-run-as-accounts"></a>重新建立執行身分帳戶

[執行身分帳戶](../manage-runas-account.md)會在 Azure Active Directory 中建立服務主體，以向 Azure 資源進行驗證。 當您變更訂閱時，自動化帳戶不再使用現有的執行身分帳戶。 若要重新建立執行身分帳戶：

1. 在新的訂用帳戶中移至您的自動化帳戶，然後在 [**帳戶設定**] 下選取 [**執行身分帳戶**]。 您會看到 [執行身分帳戶] 現在顯示為 [未完成]。

    ![執行身分帳戶的螢幕擷取畫面，顯示 [不完整]](../media/move-account/run-as-accounts.png)

2. 藉由選取 [**屬性**] 頁面上的 [**刪除**]，一次刪除一個執行身分帳戶。 

    > [!NOTE]
    > 如果您沒有建立或查看執行身分帳戶的許可權，您會看到下列訊息： `You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`如需詳細資訊，請參閱[設定執行身分帳戶所需的許可權](../manage-runas-account.md#permissions)。

3. 刪除執行身分帳戶之後，請選取 [ **Azure 執行身分帳戶**] 底下的 [**建立**]。 

4. 在 [**新增 Azure 執行身分帳戶**] 頁面上，選取 [**建立**] 以建立執行身分帳戶和服務主體。 

5. 使用 Azure 傳統執行身分帳戶重複上述步驟。

## <a name="enable-solutions"></a>啟用解決方案

重新建立執行身分帳戶之後，您必須重新啟用在移動之前移除的解決方案： 

1. 若要開啟「變更追蹤和清查」解決方案，請在您的自動化帳戶中選取 [**變更追蹤和清查**]。 選擇您要移至的 Log Analytics 工作區，然後選取 [**啟用**]。

2. 針對「更新管理」方案，重複步驟1。

    ![在移動的自動化帳戶中重新啟用解決方案的螢幕擷取畫面](../media/move-account/reenable-solutions.png)

3. 當您連線到現有的 Log Analytics 工作區時，會顯示與您的解決方案上架的機器。 若要開啟「在下班時間啟動/停止 Vm」解決方案，您必須重新部署解決方案。 在 [**相關資源**] 底下，選取 [**啟動/停止 vm** > ]**[深入瞭解]，並啟用解決方案** > [**建立**] 以開始部署。

4. 在 [**新增解決方案**] 頁面上，選擇您的 Log Analytics 工作區和自動化帳戶。

    ![[新增方案] 功能表的螢幕擷取畫面](../media/move-account/add-solution-vm.png)

5. 依照 Azure 自動化中的在下班[時間啟動/停止 vm 解決方案](../automation-solution-vm-management.md)中所述，設定解決方案。

## <a name="verify-the-move"></a>驗證移動

當移動完成時，請確認下列功能已啟用。 

|功能|測試|疑難排解|
|---|---|---|
|Runbook|Runbook 可以成功執行並聯機至 Azure 資源。|[針對 Runbook 進行疑難排解](../troubleshoot/runbooks.md)
|原始檔控制|您可以在原始檔控制儲存機制上執行手動同步處理。|[原始檔控制整合](../source-control-integration.md)|
|變更追蹤和清查|確認您看到來自電腦的目前清查資料。|[針對變更追蹤進行疑難排解](../troubleshoot/change-tracking.md)|
|更新管理|確認您看到您的電腦，且其狀況良好。</br>執行測試軟體更新部署。|[針對更新管理進行疑難排解](../troubleshoot/update-management.md)|
|共用資源|確認您看到所有共用的資源，例如[認證](../shared-resources/credentials.md)和[變數](../shared-resources/variables.md)。|

## <a name="next-steps"></a>後續步驟

若要深入瞭解如何在 Azure 中移動資源，請參閱[在 azure 中移動資源](../../azure-resource-manager/management/move-support-resources.md)。
