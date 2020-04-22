---
title: 將 Azure 自動化帳戶移到其他訂閱
description: 本文介紹如何將自動化帳戶移動到其他訂閱。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2dbe7dc171b6e0ec81c99a460a4f997eeb9e27a5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681884"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>將 Azure 自動化帳戶移到其他訂閱

Azure 自動化允許您將某些資源移動到新的資源組或訂閱。 您可以通過 Azure 門戶、PowerShell、Azure CLI 或 REST API 行動資源。 要瞭解有關該過程的更多資訊,請參閱[將資源移動到新資源群組或訂閱](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

Azure 自動化帳戶是可以移動的資源之一。 在本文中,您將學習將自動化帳戶移動到其他資源或訂閱。 移動自動化帳戶的進階步驟包括:

1. 刪除您的解決方案。
2. 取消連結工作區。
3. 移動自動化帳戶。
4. 刪除並重新創建"以運行為帳戶"。"帳戶。
5. 重新啟用解決方案。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="remove-solutions"></a>移除解決方案

要從自動化帳戶取消連結工作區,必須從工作區中刪除這些解決方案:

- 變更追蹤與詳細目錄
- 更新管理
- 於下班時間啟動/停止虛擬機器

1. 在 Azure 入口網站中找出資源群組。
2. 尋找每個解決方案,然後單擊「刪除資源」頁上的 **「刪除**」。

    ![從 Azure 門戶中移除解決方案](../media/move-account/delete-solutions.png)

    如果您願意,可以使用[「刪除-AzResource](https://docs.microsoft.com/powershell/module/Az.Resources/Remove-AzResource?view=azps-3.7.0) cmdlet」刪除解決方案:

    ```azurepowershell-interactive
    $workspaceName = <myWorkspaceName>
    $resourceGroupName = <myResourceGroup>
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
    Remove-AzResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
    ```

### <a name="remove-alert-rules-for-the-startstop-vms-during-off-hours-solution"></a>移除工作時間解決方案期間啟動/ 停止 VM 的警示規則

對於在非工作時間解決方案中啟動/停止 VM,還需要刪除解決方案創建的警報規則。

1. 在 Azure 門戶中,轉到資源組並選擇 **「監視** > **警報** > **管理警報規則**」。。

![顯示「管理警報」規則選擇的警示頁](../media/move-account/alert-rules.png)

2. 在「規則」頁上,您應該看到該資源組中配置的警報的清單。 解決方案建立以下規則:

    * AutoStop_VM_Child
    * ScheduledStartStop_Parent
    * SequencedStartStop_Parent

3. 一次選擇一個規則,然後按下 **「刪除**」以刪除它們。

    ![規則頁面要求確認選取規則的刪除](../media/move-account/delete-rules.png)

    > [!NOTE]
    > 如果在"規則"頁上看不到任何警報規則,則將 **「狀態**」欄位更改為「已禁用」以顯示已禁用的警報,因為您可能已禁用這些警報。

4. 刪除警報規則時,必須刪除在非工作時間解決方案通知期間為開始/停止 VM 創建的操作組。 在 Azure 門戶中,選擇 **「監視** > **警報** > **管理操作組**」。

5. 選擇**StartStop_VM_Notification**。 

6. 在操作組頁上,選擇 **「刪除**」。

    ![操作群組頁面](../media/move-account/delete-action-group.png)

    如果您願意,可以使用[「刪除-AzActionGroup](https://docs.microsoft.com/powershell/module/az.monitor/remove-azactiongroup?view=azps-3.7.0) cmdlet」刪除操作組:

    ```azurepowershell-interactive
    Remove-AzActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
    ```

## <a name="unlink-your-workspace"></a>取消連結工作區

現在,您可以取消連結工作區:

1. 在 Azure 門戶中,選擇 **「自動化帳戶** > **相關資源** > **連結工作區**」。 

2. 選擇 **「取消連結工作區**」以從自動化帳戶取消連結工作區。

    ![從自動化帳戶取消連結工作區](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移動您的自動化帳戶

您現在可以移動自動化帳戶及其運行簿。 

1. 在 Azure 門戶中,流覽到自動化帳戶的資源組。 選擇 **「移動到** > **其他訂閱**」。

    ![資源組頁面,移到其他訂閱](../media/move-account/move-resources.png)

2. 選擇要移動的資源群組中的資源。 確保您包括自動化帳戶、執行簿和日誌分析工作區資源。

## <a name="recreate-run-as-accounts"></a>重新創建"作為帳戶運行"

[在](../manage-runas-account.md)Azure 活動目錄中創建服務主體以使用 Azure 資源進行身份驗證。 更改訂閱時,自動化帳戶不再使用現有的"運行為樣"帳戶。 要重新建立「以帳戶身份執行」:

1. 轉到新訂閱中的自動化帳戶,然後選擇「**在帳戶設置**下**作為帳戶運行**」。。 您將看到「運行為帳戶」現在顯示為不完整。

    ![執行帳號不完整](../media/move-account/run-as-accounts.png)

2. 使用"屬性"頁上的 **"刪除**"按鈕,一次刪除"作為帳戶運行"帳戶一個。 

    > [!NOTE]
    > 如果您沒有建立或檢視「執行為帳戶」的權限,請參閱以下訊息:`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`要瞭解設定「執行為「帳戶所需的權限,請參閱[設定」作為執行帳戶「所需的權限](../manage-runas-account.md#permissions)。

3. 刪除"以"身份運行帳戶"後,選擇"**在** **Azure 運行為帳戶**下創建"。 

4. 在"添加 Azure 運行為帳戶"頁上,選擇 **"創建**"以建立"以"以"身份運行"帳戶和服務主體。 

5. 使用 Azure 經典運行作為帳戶重複上述步驟。

## <a name="enable-solutions"></a>啟用解決方案

重新建立「執行為帳戶」後,必須重新啟用在移動之前刪除的解決方案: 

1. 要打開"更改跟蹤和庫存"解決方案,請在自動化帳戶中選擇"更改跟蹤和庫存"。 選擇您移動的日誌分析工作區,然後選擇 **"啟用**"。

2. 對更新管理解決方案重複步驟 1。

    ![在行動的自動化帳戶中重新啟用解決方案](../media/move-account/reenable-solutions.png)

3. 連接現有日誌分析工作區後,使用解決方案隨機操作的電腦可見。 要在非工作時間打開"開始/停止 VM"解決方案,必須重新部署解決方案。 在 **「相關資源**」下,選擇 **「開始/停止 VM」** > **以瞭解有關詳細資訊並啟用解決方案** > **「創建**」以啟動部署。

4. 在"添加解決方案"頁上,選擇日誌分析工作區和自動化帳戶。

    ![新增解決方案選單](../media/move-account/add-solution-vm.png)

5. 配置解決方案,如[Azure 自動化中非工作時間解決方案中的開始/停止 VM 中](../automation-solution-vm-management.md)所述。

## <a name="verify-the-move"></a>驗證移動

移動完成後,驗證以下功能是否已啟用。 

|功能|測試|疑難排解|
|---|---|---|
|Runbook|Runbook 可以成功執行並連接到 Azure 資源。|[針對 Runbook 進行疑難排解](../troubleshoot/runbooks.md)
|原始檔控制|您可以在原始程式碼管理儲存庫上執行手動同步。|[原始檔控制整合](../source-control-integration.md)|
|變更追蹤與庫存|驗證您是否從電腦中看到當前庫存數據。|[容錯排除變更追蹤](../troubleshoot/change-tracking.md)|
|更新管理|驗證您看到電腦是否正常。</br>運行測試軟體更新部署。|[容錯排除更新管理](../troubleshoot/update-management.md)|
|共用資源|驗證是否看到所有共享資源,如[憑據](../shared-resources/credentials.md)、[變數](../shared-resources/variables.md)等。|

## <a name="next-steps"></a>後續步驟

要瞭解有關在 Azure 中移動資源的更多資訊,請參閱[在 Azure 中移動資源](../../azure-resource-manager/management/move-support-resources.md)。
