---
title: 針對 Azure 自動化停機期間啟動/停止 VM 問題進行疑難排解
description: 本文說明如何疑難排解並解決在使用停機期間啟動/停止 VM 功能期間所引發的問題。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 53a7e113d64ea4cf7018d51a44f9488342f1470f
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715625"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>針對停機期間啟動/停止 VM 問題進行疑難排解

本文提供在您的 VM 上部署 Azure 自動化停機期間啟動/停止 VM 功能時若引發問題，針對其進行疑難排解和解決的相關資訊。 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>案例：停機期間啟動/停止 VM 無法適當地部署

### <a name="issue"></a>問題

部署[停機期間啟動/停止 VM](../automation-solution-vm-management.md) 時，您收到下列其中一個錯誤：

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

部署可能由於下列其中一個原因而失敗：

- 在選取區域中已經有自動化帳戶使用相同的名稱。
- 原則不允許停機期間啟動/停止 VM 的部署。
- 未註冊 `Microsoft.OperationsManagement`、`Microsoft.Insights` 或 `Microsoft.Automation` 資源類型。
- 您的 Log Analytics 工作區遭到鎖定。
- 您有過時的 AzureRM 模組版本，或停機期間啟動/停止 VM 功能。

### <a name="resolution"></a>解決方案

請檢閱下列修正以取得可能的解決方式：

* 自動化帳戶在 Azure 區域中必須是唯一的，即使位於不同的資源群組。 檢查目標區域中現有的自動化帳戶。
* 現有的原則會防止部署停機期間啟動/停止 VM 所需的資源。 請移至您在 Azure 入口網站中的原則指派，並檢查您是否有不允許這項資源部署的原則指派。 若要深入了解，請參閱 [RequestDisallowedByPolicy 錯誤](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)。
* 若要部署停機期間啟動/停止 VM，您的訂用帳戶必須註冊下列 Azure 資源命名空間：

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   若要深入了解註冊提供者時的錯誤，請參閱[解決資源提供者註冊的錯誤](../../azure-resource-manager/templates/error-register-resource-provider.md)。
* 如果您有 Log Analytics 工作區的鎖定，請移至您在 Azure 入口網站中的工作區，並移除資源的任何鎖定。
* 如果這些解決方法未解決您的問題，請遵循[更新功能](../automation-solution-vm-management.md#update-the-feature)下的指示，以重新部署停機期間啟動/停止 VM。

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>案例：所有 VM 無法啟動或停止

### <a name="issue"></a>問題

您已設定停機期間啟動/停止 VM，但其未啟動或停止所有 VM。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

- 未正確設定排程。
- 可能未正確設定執行身分帳戶。
- Runbook 可能遇到錯誤。
- 可能已排除 VM。

### <a name="resolution"></a>解決方案

請檢閱下列清單以取得可能的解決方式：

* 檢查您是否已正確設定停機期間啟動/停止 VM 的排程。 若要了解如何設定排程，請參閱[排程](../automation-schedules.md)。

* 檢查[作業資料流](../automation-runbook-execution.md#job-statuses)以尋找任何錯誤。 尋找下列其中一個 Runbook 中的作業：

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* 驗證您的[執行身分帳戶](../manage-runas-account.md)是否對您嘗試啟動或停止的 VM 具有適當權限。 若要了解如何檢查資源的權限，請參閱[快速入門：使用 Azure 入口網站來檢視指派給使用者的角色](../../role-based-access-control/check-access.md)。 您需要為執行身分帳戶使用的服務主體提供應用程式識別碼。 您可以在 Azure 入口網站中移至您的自動化帳戶來擷取此值。 選取 [帳戶設定] 下的 [執行身分帳戶]，然後選取適當的執行身分帳戶。

* 如果明確地排除 VM，則可能無法啟動或停止它們。 已排除的 VM 設定在自動化帳戶的 `External_ExcludeVMNames` 變數中，而功能部署至這個自動化帳戶。 下列範例示範如何使用 PowerShell 查詢該值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>案例：我的部分虛擬機器無法啟動或停止

### <a name="issue"></a>問題

您已設定停機期間啟動/停止 VM，但其未啟動或停止某些已設定的 VM。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

- 在序列情節中，標籤可能遺失或不正確。
- 可能會排除 VM。
- 執行身分帳戶可能沒有足夠的 VM 權限。
- VM 可能有阻止其啟動或停止的問題。

### <a name="resolution"></a>解決方案

請檢閱下列清單以取得可能的解決方式：

* 在使用停機期間啟動/停止 VM 的[序列情節](../automation-solution-vm-management.md)時，您必須確保要啟動或停止的每個 VM 都具有正確的標籤。 確保要啟動的 VM 具有 `sequencestart` 標記，並且要停止的 VM 具有 `sequencestop` 標記。 這兩個標記都需要正整數值。 您可以使用類似下列範例查詢來尋找包含標記和其值的所有 VM。

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 如果明確地排除 VM，則可能無法啟動或停止它們。 已排除的 VM 設定在自動化帳戶的 `External_ExcludeVMNames` 變數中，而功能部署至這個自動化帳戶。 下列範例示範如何使用 PowerShell 查詢該值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 若要啟動和停止 VM，自動化帳戶的執行身分帳戶必須具有 VM 的適當權限。 若要了解如何檢查資源的權限，請參閱[快速入門：使用 Azure 入口網站來檢視指派給使用者的角色](../../role-based-access-control/check-access.md)。 您需要為執行身分帳戶使用的服務主體提供應用程式識別碼。 您可以在 Azure 入口網站中移至您的自動化帳戶來擷取此值。 選取 [帳戶設定] 下的 [執行身分帳戶]，然後選取適當的執行身分帳戶。
* 如果 VM 在啟動或解除配置時出現問題，VM 本身可能有問題。 範例是當 VM 嘗試關閉、服務停止回應，以及其他情況時所要套用的更新。 移至您的 VM 資源並檢查**活動記錄**，以查看記錄中是否有任何錯誤。 您還可以嘗試登入 VM，以查看事件記錄檔中是否有任何錯誤。 若要深入了解 VM 的疑難排解，請參閱[針對 Azure 虛擬機器進行疑難排解](../../virtual-machines/troubleshooting/index.yml)。
* 檢查[作業資料流](../automation-runbook-execution.md#job-statuses)以尋找任何錯誤。 在入口網站中，移至 [自動化帳戶]，然後在 [程序自動化] 下選取 [作業]。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>案例：我的自訂 Runbook 無法啟動或停止我的 VM

### <a name="issue"></a>問題

您已撰寫自訂 Runbook 或從 PowerShell 資源庫中下載了一個，但它無法正常運作。

### <a name="cause"></a>原因

可能有多個失敗原因。 在入口網站中，移至 [自動化帳戶]，然後在 [程序自動化] 下選取 [作業]。 從 [作業] 頁面上，查看 Runbook 中的作業以檢視任何作業失敗。

### <a name="resolution"></a>解決方案

建議您：

* 使用[停機期間啟動/停止 VM](../automation-solution-vm-management.md) 來啟動及停止 Azure 自動化中的 VM。 
* 請注意，Microsoft 不支援自訂 Runbook。 您可能會在[針對 Runbook 問題進行疑難排解](runbooks.md)中找到自訂 Runbook 的解決方案。 檢查[作業資料流](../automation-runbook-execution.md#job-statuses)以尋找任何錯誤。 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>案例：VM 無法以正確的順序啟動或停止

### <a name="issue"></a>問題

您已針對功能啟用的 VM 未以正確的順序啟動或停止。

### <a name="cause"></a>原因

這個問題是由 VM 上不正確的標記引起的。

### <a name="resolution"></a>解決方案

請遵循下列步驟，以確保正確啟用此功能：

1. 確定要啟動或停止的所有 VM 都具有 `sequencestart` 或 `sequencestop` 標籤，取決於您的情況。 這些標記的值必須是正整數。 VM 會根據此值，以遞增的順序處理。
1. 確定要啟動或停止的 VM 的資源群組位於 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 變數中，取決於您的情況。
1. 透過在 `WHATIF` 參數設為 True 的情況下執行 **SequencedStartStop_Parent** Runbook 來測試變更，以預覽您所做的變更。

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>案例：停機期間啟動/停止 VM 作業失敗，出現 403 禁止錯誤

### <a name="issue"></a>問題

您可以在停機期間啟動/停止 VM Runbook 中找到因出現 `403 forbidden` 錯誤而失敗的作業。

### <a name="cause"></a>原因

此問題可能是由未正確設定或已過期的執行身分帳戶所引起。 這也可能是因為執行身分帳戶對 VM 資源的權限不足。

### <a name="resolution"></a>解決方案

若要驗證您的執行身分帳戶是否已正確設定，請移至 Azure 入口網站中的 [自動化帳戶]，然後在 [帳戶設定] 下選取 [執行身分帳戶]。 如果執行身分帳戶未適當地設定或過期，則狀態會顯示情況。

如果您的執行身分帳戶設定錯誤，請刪除並重新建立執行身分帳戶。 如需詳細資訊，請參閱[管理 Azure 自動化執行身分帳戶](../manage-runas-account.md)。

如果執行身分帳戶的憑證已過期，請遵循[自我簽署憑證](../manage-runas-account.md#cert-renewal)中的步驟來更新憑證。

如果遺漏權限，請參閱 [快速入門：使用 Azure 入口網站來檢視指派給使用者的角色](../../role-based-access-control/check-access.md)。 您必須為執行身分帳戶使用的服務主體提供應用程式識別碼。 您可以在 Azure 入口網站中移至您的自動化帳戶來擷取此值。 選取 [帳戶設定] 下的 [執行身分帳戶]，然後選取適當的執行身分帳戶。

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>案例：這裡未列出我的問題

### <a name="issue"></a>問題

當使用停機期間啟動/停止 VM 時，您遇到此頁面未列出的問題或意外結果。

### <a name="cause"></a>原因

很多時候，使用舊的和過時的功能版本可能會導致錯誤。

> [!NOTE]
> 在 VM 上部署停機期間啟動/停止 VM 功能時，已使用匯入至自動化帳戶的 Azure 模組來測試此功能。 此功能目前不適用於較新版本的 Azure 模組。 此限制只會影響您用來執行停機期間啟動/停止 VM 的自動化帳戶。 您仍然可以在其他自動化帳戶中使用較新版本的 Azure 模組，如[更新 Azure PowerShell 模組](../automation-update-azure-modules.md)中所述。

### <a name="resolution"></a>解決方案

若要解決許多錯誤，請移除並[更新停機期間啟動/停止 VM](../automation-solution-vm-management.md#update-the-feature)。 您也可以檢查[作業資料流](../automation-runbook-execution.md#job-statuses)以尋找任何錯誤。 

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個管道以取得其他支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 連線至 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援會將 Azure 社群連線到解答、支援及專家。
* 提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。