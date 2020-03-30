---
title: 將 Azure 自動化帳戶移動到其他訂閱
description: 本文介紹如何將自動化帳戶移動到其他訂閱
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/11/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1aa759a2984764169eb28935e095d0f7c0f90c08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969836"
---
# <a name="move-your-azure-automation-account-to-another-subscription"></a>將 Azure 自動化帳戶移動到其他訂閱

Azure 使您能夠將某些資源移動到新資源組或訂閱。 您可以通過 Azure 門戶、PowerShell、Azure CLI 或 REST API 移動資源。 要瞭解有關該過程的更多資訊，請參閱[將資源移動到新資源組或訂閱](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。

Azure 自動化帳戶是可移動的資源之一。 在本文中，您將瞭解將自動化帳戶移動到其他資源或訂閱的步驟。

移動自動化帳戶的高級步驟包括：

1. 刪除您的解決方案。
2. 取消連結工作區。
3. 移動自動化帳戶。
4. 刪除並重新創建"以運行為帳戶"。"帳戶。
5. 重新啟用解決方案。

## <a name="remove-solutions"></a>刪除解決方案

要從自動化帳戶取消連結工作區，必須從工作區中刪除這些解決方案：
- **變更追蹤與詳細目錄**
- **更新管理**
- **在非工作時間啟動/停止 VM**

在資源組中，查找每個解決方案並選擇 **"刪除**"。 在 **"刪除資源"** 頁上，確認要刪除的資源，然後選擇 **"刪除**"。

![從 Azure 門戶中刪除解決方案](../media/move-account/delete-solutions.png)

您可以使用["刪除 AzureRmResource](/powershell/module/azurerm.resources/remove-azurermresource) Cmdlet"完成相同的任務，如以下示例所示：

```azurepowershell-interactive
$workspaceName = <myWorkspaceName>
$resourceGroupName = <myResourceGroup>
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "ChangeTracking($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Updates($workspaceName)" -ResourceGroupName $resourceGroupName
Remove-AzureRmResource -ResourceType 'Microsoft.OperationsManagement/solutions' -ResourceName "Start-Stop-VM($workspaceName)" -ResourceGroupName $resourceGroupName
```

### <a name="additional-steps-for-startstop-vms"></a>啟動/停止 VM 的其他步驟

對於 **"開始/停止 VM"** 解決方案，還需要刪除解決方案創建的警報規則。

在 Azure 門戶中，轉到資源組並選擇 **"監視** > **警報** > **管理警報規則**"。

![顯示"管理警報"規則選擇的警報頁](../media/move-account/alert-rules.png)

在 **"規則"** 頁上，您應該看到該資源組中配置的警報的清單。 **啟動/停止 VM**解決方案創建三個警報規則：

* AutoStop_VM_Child
* ScheduledStartStop_Parent
* SequencedStartStop_Parent

選擇這三個警報規則，然後選擇 **"刪除**"。 此操作將刪除這些警報規則。

![規則頁請求確認所選規則的刪除](../media/move-account/delete-rules.png)

> [!NOTE]
> 如果在 **"規則"** 頁上看不到任何警報規則，則更改**狀態**以顯示 **"已禁用**"警報，因為您可能已禁用這些警報。

刪除警報規則後，刪除為**開始/停止 VM**解決方案通知創建的操作組。

在 Azure 門戶中，選擇 **"監視** > **警報** > **管理操作組**"。

從清單中選擇**StartStop_VM_Notification。** 在操作組頁上，選擇 **"刪除**"。

![操作組頁面，選擇刪除](../media/move-account/delete-action-group.png)

同樣，您可以將 PowerShell 與[刪除 AzureRmActionGroup](/powershell/module/azurerm.insights/remove-azurermactiongroup) Cmdlet 一起使用來刪除操作組，如以下示例所示：

```azurepowershell-interactive
Remove-AzureRmActionGroup -ResourceGroupName <myResourceGroup> -Name StartStop_VM_Notification
```

## <a name="unlink-your-workspace"></a>取消連結工作區

在 Azure 門戶中，選擇 **"自動化帳戶** > **相關資源** > **連結工作區**"。 選擇 **"取消連結工作區**"以從自動化帳戶取消連結工作區。

![從自動化帳戶取消連結工作區](../media/move-account/unlink-workspace.png)

## <a name="move-your-automation-account"></a>移動您的自動化帳戶

刪除以前的專案後，可以繼續刪除自動化帳戶及其運行簿。 在 Azure 門戶中，流覽到自動化帳戶的資源組。 選擇 **"移動到** > **其他訂閱**"。

![資源組頁面，移動到其他訂閱](../media/move-account/move-resources.png)

選擇要移動的資源組中的資源。 確保您包括**您的自動化帳戶****、Runbook**和**日誌分析工作區**資源。

移動完成後，還需要執行其他步驟，以使一切正常工作。

## <a name="re-create-run-as-accounts"></a>重新創建"作為帳戶運行"

[在](../manage-runas-account.md)Azure 活動目錄中創建服務主體以使用 Azure 資源進行身份驗證。 更改訂閱時，自動化帳戶不再使用現有的"運行為樣"帳戶。

轉到新訂閱中的自動化帳戶，然後選擇"**在帳戶設置**下**作為帳戶運行**"。 您將看到"運行為帳戶"現在顯示為不完整。

![運行帳戶不完整](../media/move-account/run-as-accounts.png)

選擇每個"以"身份運行"科目。 在 **"屬性**"頁上，選擇 **"刪除**"以刪除"運行為帳戶"。

> [!NOTE]
> 如果您沒有創建或查看"運行為帳戶"的許可權，您將看到以下消息：`You do not have permissions to create an Azure Run As account (service principal) and grant the Contributor role to the service principal.`要瞭解配置"運行即"帳戶所需的許可權，請參閱[配置"作為運行帳戶"所需的許可權](../manage-runas-account.md#permissions)。

刪除"運行為帳戶"後，選擇在**Azure 運行為帳戶**下**創建**。 在"**添加 Azure 運行為帳戶"** 頁上，選擇 **"創建**"以創建"以"以"身份運行"帳戶和服務主體。 使用**Azure 經典運行作為帳戶**重複上述步驟。

## <a name="enable-solutions"></a>啟用解決方案

重新創建"運行為帳戶"後，您將重新啟用在移動之前刪除的解決方案。 要打開 **"更改跟蹤"和"庫存**"和 **"更新管理**"，請在自動化帳戶中選擇相應的功能。 選擇您移動的日誌分析工作區，然後選擇 **"啟用**"。

![在移動的自動化帳戶中重新啟用解決方案](../media/move-account/reenable-solutions.png)

連接現有日誌分析工作區後，使用解決方案隨機操作的電腦將可見。

要在非工作時間解決方案期間打開 **"開始/停止 VM"，** 您需要重新部署解決方案。 在 **"相關資源**"下，選擇 **"開始/停止 VM"** > **以瞭解有關詳細資訊並啟用解決方案** > **"創建**"以啟動部署。

在 **"添加解決方案**"頁上，選擇日誌分析工作區和自動化帳戶。

![添加解決方案功能表](../media/move-account/add-solution-vm.png)

有關配置解決方案的詳細說明，請參閱[Azure 自動化 中的非工作時間解決方案中的開始/停止 VM。](../automation-solution-vm-management.md)

## <a name="post-move-verification"></a>移動後驗證

移動完成後，請檢查以下應驗證的工作清單：

|功能|測試|故障排除連結|
|---|---|---|
|Runbook|Runbook 可以成功運行並連接到 Azure 資源。|[排除運行簿故障](../troubleshoot/runbooks.md)
|原始檔控制|您可以在原始程式碼管理回購上運行手動同步。|[原始程式碼管理集成](../source-control-integration.md)|
|更改跟蹤和庫存|驗證從電腦中看到當前庫存資料。|[故障排除更改跟蹤](../troubleshoot/change-tracking.md)|
|更新管理|驗證您看到您的機器，並且它們是否正常。</br>運行測試軟體更新部署。|[故障排除更新管理](../troubleshoot/update-management.md)|
|共用資源|驗證是否看到所有共用資源，如[憑據](../shared-resources/credentials.md)、[變數](../shared-resources/variables.md)等。|

## <a name="next-steps"></a>後續步驟

要瞭解有關在 Azure 中移動資源的更多資訊，請參閱[在 Azure 中移動資源](../../azure-resource-manager/management/move-support-resources.md)。
