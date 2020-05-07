---
title: 在下班時間解決方案中針對啟動/停止 Vm 進行疑難排解
description: 本文提供有關在下班時間解決啟動/停止 VM 解決方案的資訊。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 611e8441fab56114ca010d0b555c9ed156ae9d40
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855053"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>疑難排解停機期間啟動/停止 VM 解決方案

本文提供的資訊是關於當您使用 Azure 自動化在下班時間啟動/停止 Vm 解決方案時所發生的問題的疑難排解。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的 Azure 自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>案例：在下班時間啟動/停止 Vm 解決方案無法正確部署

### <a name="issue"></a>問題

當您在下班[時間解決方案中部署啟動/停止 vm](../automation-solution-vm-management.md)時，您會收到下列其中一個錯誤：

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>原因

部署可能會因為下列其中一個原因而失敗：

- 已選取的區域中已經有相同名稱的自動化帳戶。
- 原則不允許在下班時間解決方案中部署啟動/停止 Vm。
- 未`Microsoft.OperationsManagement`註冊`Microsoft.Insights`、或`Microsoft.Automation`資源類型。
- 您的 Log Analytics 工作區已鎖定。
- 您的 AzureRM 模組版本已過期，或在下班時間解決方案中啟動/停止 Vm。

### <a name="resolution"></a>解決方案

針對您問題的可能解決方案，請參閱下列修正：

* 自動化帳戶在 Azure 區域內必須是唯一的，即使它們位於不同的資源群組中也一樣。 檢查目的地區域中的現有自動化帳戶。
* 現有的原則會防止部署啟動/停止 Vm 期間所需的資源解決方案。 移至 Azure 入口網站中的原則指派，並檢查是否有不允許部署此資源的原則指派。 若要深入瞭解，請參閱[RequestDisallowedByPolicy 錯誤](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)。
* 若要部署啟動/停止 Vm 解決方案，您的訂用帳戶必須註冊至下列 Azure 資源命名空間：

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   若要深入瞭解註冊提供者時的錯誤，請參閱[解決資源提供者註冊的錯誤](../../azure-resource-manager/templates/error-register-resource-provider.md)。
* 如果您有 Log Analytics 工作區的鎖定，請移至您在 Azure 入口網站中的工作區，並移除資源的任何鎖定。
* 如果這些解決方法無法解決您的問題，請依照[更新解決方案](../automation-solution-vm-management.md#update-the-solution)中的指示，在下班時間解決方案中重新部署啟動/停止 vm。

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>案例：所有 Vm 皆無法啟動或停止

### <a name="issue"></a>問題

您已設定 [在下班時間啟動/停止 Vm] 解決方案，但它不會啟動或停止所有 Vm。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

- 未正確設定排程。
- 執行身分帳戶可能未正確設定。
- Runbook 可能會遇到錯誤。
- Vm 可能已排除。

### <a name="resolution"></a>解決方案

請參閱下列清單，以取得您問題的潛在解決方案：

* 請確認您已正確設定 [在下班時間啟動/停止 Vm] 解決方案的排程。 若要瞭解如何設定排程，[請參閱排程](../automation-schedules.md)。

* 檢查[作業資料流程](../automation-runbook-execution.md#job-statuses)以尋找是否有任何錯誤。 從下列其中一個 runbook 尋找作業：

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* 請確認您的[執行身分帳戶](../manage-runas-account.md)對您嘗試啟動或停止的 vm 具有適當的許可權。 若要瞭解如何檢查資源的許可權，請參閱[快速入門：使用 Azure 入口網站來查看指派給使用者的角色](../../role-based-access-control/check-access.md)。 您必須提供執行身分帳戶所使用之服務主體的應用程式識別碼。 您可以在 Azure 入口網站中前往您的自動化帳戶來取得此值。 在 [**帳戶設定**] 底下選取 [**執行身分帳戶**]，然後選取適當的執行身分帳戶。

* 如果 Vm 被明確排除，可能就無法啟動或停止。 已排除的 Vm 會在`External_ExcludeVMNames`部署解決方案的自動化帳戶中的變數中設定。 下列範例顯示如何使用 PowerShell 查詢該值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>案例：我的一些 Vm 無法啟動或停止

### <a name="issue"></a>問題

您已設定在下班時間解決啟動/停止 Vm 解決方案，但它不會啟動或停止部分設定的 Vm。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

- 在順序案例中，標記可能遺失或不正確。
- VM 可能已排除。
- 執行身分帳戶在 VM 上可能沒有足夠的許可權。
- VM 可能會有停止啟動或停止的問題。

### <a name="resolution"></a>解決方案

請檢閱下列清單，以取得您的問題或搜尋的地方的潛在解決方案：

* 當您使用 [在下班時間啟動/停止 Vm] 解決方案的[順序案例](../automation-solution-vm-management.md)時，您必須確定您想要啟動或停止的每個 VM 都具有適當的標記。 確保要啟動的 VM 具有 `sequencestart` 標記，並且要停止的 VM 具有 `sequencestop` 標記。 這兩個標記都需要正整數值。 您可以使用類似下列範例查詢來尋找包含標記和其值的所有 VM。

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 如果 Vm 被明確排除，可能就無法啟動或停止。 已排除的 Vm 會在`External_ExcludeVMNames`部署解決方案的自動化帳戶中的變數中設定。 下列範例顯示如何使用 PowerShell 查詢該值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 若要啟動和停止 Vm，自動化帳戶的執行身分帳戶必須具有 VM 的適當許可權。 若要瞭解如何檢查資源的許可權，請參閱[快速入門：使用 Azure 入口網站來查看指派給使用者的角色](../../role-based-access-control/check-access.md)。 您必須提供執行身分帳戶所使用之服務主體的應用程式識別碼。 您可以在 Azure 入口網站中前往您的自動化帳戶來取得此值。 在 [**帳戶設定**] 底下選取 [**執行身分帳戶**]，然後選取適當的執行身分帳戶。
* 如果 VM 在啟動或解除配置時發生問題，VM 本身可能會發生問題。 範例是當 VM 嘗試關閉、停止回應的服務，以及更多的應用程式時所要套用的更新。 移至您的 VM 資源，並檢查**活動記錄**以查看記錄中是否有任何錯誤。 您也可以嘗試登入 VM，以查看事件記錄檔中是否有任何錯誤。 若要深入瞭解 VM 的疑難排解，請參閱針對[Azure 虛擬機器進行疑難排解](../../virtual-machines/troubleshooting/index.yml)。
* 檢查[作業資料流程](../automation-runbook-execution.md#job-statuses)以尋找是否有任何錯誤。 在入口網站中，移至您的自動化帳戶，然後選取 [**進程自動化**] 底下的 [**作業**]。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>案例：我的自訂 runbook 無法啟動或停止我的 Vm

### <a name="issue"></a>問題

您已撰寫自訂 runbook 或從 PowerShell 資源庫下載，且其運作不正常。

### <a name="cause"></a>原因

失敗的原因可能有許多種。 在 [Azure 入口網站中移至您的自動化帳戶，然後選取 [程式**自動化**] 下的 [**作業**]。 從 [作業]**** 頁面上，查看 Runbook 中的作業以檢視任何作業失敗。

### <a name="resolution"></a>解決方案

建議您：

* 使用在下班[時間啟動/停止 vm 解決方案](../automation-solution-vm-management.md)來啟動和停止 Azure 自動化中的 vm。 此解決方案是由 Microsoft 所撰寫。 
* 請注意，Microsoft 不支援自訂 runbook。 您可能會在[runbook 疑難排解](runbooks.md)中找到自訂 runbook 的解決方案。 檢查[作業資料流程](../automation-runbook-execution.md#job-statuses)以尋找是否有任何錯誤。 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>案例： Vm 不會以正確的順序啟動或停止

### <a name="issue"></a>問題

您在解決方案中設定的 VM 無法以正確的順序啟動或停止。

### <a name="cause"></a>原因

此問題是因 Vm 上的標記不正確所造成。

### <a name="resolution"></a>解決方案

請遵循下列步驟，以確保解決方案已正確設定。

1. 請確定要啟動或停止的所有 VM 都具有 `sequencestart` 或 `sequencestop` 標記，具體取決於您的情況。 這些標記的值必須是正整數。 VM 會根據此值，以遞增的順序處理。
1. 請確定要啟動或停止的 VM 的資源群組位於 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 變數中，具體取決於您的情況。
1. 藉由執行`SequencedStartStop_Parent` runbook 並將`WHATIF`參數設定為 True 來測試您的變更，以預覽您的變更。

如需如何使用解決方案依序啟動和停止 Vm 的詳細資訊，請參閱依[序啟動/停止 vm](../automation-solution-vm-management.md)。

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>案例：在下班時間啟動/停止 Vm 作業會失敗，並出現403禁止的錯誤

### <a name="issue"></a>問題

在下班時間解決方案 runbook 中， `403 forbidden`您會發現因啟動/停止 vm 的錯誤而失敗的作業。

### <a name="cause"></a>原因

此問題可能是因為執行身分帳戶設定不正確或已過期所造成。 這也可能是因為執行身分帳戶的 VM 資源許可權不足所導致。

### <a name="resolution"></a>解決方案

若要確認您的執行身分帳戶已正確設定，請移至 Azure 入口網站中的自動化帳戶，然後在 [**帳戶設定**] 底下選取 [**執行身分帳戶**]。 如果執行身分帳戶設定不正確或已過期，則狀態會顯示 [條件]。

如果您的執行身分帳戶設定錯誤，請刪除並重新建立您的執行身分帳戶。 如需詳細資訊，請參閱[管理 Azure 自動化執行身分帳戶](../manage-runas-account.md)。

如果您的執行身分帳戶的憑證已過期，請遵循[自我簽署憑證更新](../manage-runas-account.md#cert-renewal)中的步驟來更新憑證。

如果有遺漏許可權，請參閱[快速入門：使用 Azure 入口網站來查看指派給使用者的角色](../../role-based-access-control/check-access.md)。 您必須提供執行身分帳戶所使用之服務主體的應用程式識別碼。 您可以在 Azure 入口網站中前往您的自動化帳戶來取得此值。 在 [**帳戶設定**] 底下選取 [**執行身分帳戶**]，然後選取適當的執行身分帳戶。

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>案例：我的問題未列于此處

### <a name="issue"></a>問題

當您使用在此頁面上未列出的「在下班時間啟動/停止 Vm」解決方案時，您會遇到問題或非預期的結果。

### <a name="cause"></a>原因

很多時候，使用舊的和過時的解決方案可能會導致錯誤。

> [!NOTE]
> 當您部署解決方案時，會使用匯入您的自動化帳戶的 Azure 模組來測試「停機期間啟動/停止 Vm」解決方案。 此解決方案目前無法與較新版本的 Azure 模組搭配使用。 此限制只會影響您在下班時間解決方案中用來執行 [啟動/停止 Vm] 的自動化帳戶。 您仍然可以在其他自動化帳戶中使用較新版本的 Azure 模組，如[如何在 Azure 自動化中更新 Azure PowerShell 模組](../automation-update-azure-modules.md)中所述。

### <a name="resolution"></a>解決方案

若要解決許多錯誤，請[在下班時間解決方案中移除並更新啟動/停止 vm](../automation-solution-vm-management.md#update-the-solution)。 您也可以檢查[作業資料流程](../automation-runbook-execution.md#job-statuses)，尋找是否有任何錯誤。 

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個通道以取得其他支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶聯繫，以改善客戶體驗。 Azure 支援將 Azure 社區連接到解答、支援及專家。
* 提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。