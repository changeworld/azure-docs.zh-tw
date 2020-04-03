---
title: 容毀損並停止 VM - Azure 自動化
description: 本文提供有關在 Azure 自動化中啟動和停止 VM 的故障排除資訊。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 73a9680cc570179c47b527a4844488da69193cb3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586088"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>疑難排解停機期間啟動/停止 VM 解決方案

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>方案:啟動/停止 VM 解決方案無法正確部署

### <a name="issue"></a>問題

您部署[停機期間啟動/停止 VM 解決方案](../automation-solution-vm-management.md)時，會收到下列錯誤的其中一個：

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

1. 在選取區域中已經有自動化帳戶使用相同的名稱。
2. 有不允許部署啟動/停止 VM 解決方案的原則。
3. 未註冊 `Microsoft.OperationsManagement`、`Microsoft.Insights` 或 `Microsoft.Automation` 資源類型。
4. 您的 Log Analytics 工作區已鎖定。
5. 您有過時的 AzureRM 模組或開始/ 停止解決方案。

### <a name="resolution"></a>解決方案

請檢閱下列清單，以取得您的問題或搜尋的地方的潛在解決方案：

1. 自動化帳戶在 Azure 區域中必須是唯一的，即使位於不同的資源群組。 檢查目標區域中現有的自動化帳戶。
2. 現有的原則會防止部署啟動/停止 VM 解決方案所需的資源。 請移至您在 Azure 入口網站中的原則指派，並檢查您是否有不允許這項資源部署的原則指派。 若要深入了解，請參閱 [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)。
3. 若要部署啟動/停止 VM 解決方案，您的訂用帳戶必須註冊下列 Azure 資源命名空間：
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   請參閱[解決資源提供者註冊的錯誤](../../azure-resource-manager/templates/error-register-resource-provider.md)，深入了解註冊提供者時的錯誤。
4. 如果您有 Log Analytics 工作區的鎖定，請移至您在 Azure 入口網站中的工作區，並移除資源的任何鎖定。
5. 如果上述解決方案不能解決問題,請按照[「更新解決方案」](../automation-solution-vm-management.md#update-the-solution)下的說明重新部署"開始/停止"解決方案。

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>方案:所有 VM 無法啟動/ 停止

### <a name="issue"></a>問題

您已設定啟動/停止 VM 解決方案，但它不會啟動或停止所有已設定的 VM。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

1. 未正確設定排程
2. 可能未正確設定執行身分帳戶
3. Runbook 可能遇到錯誤
4. 可能已排除 VM

### <a name="resolution"></a>解決方案

請檢閱下列清單，以取得您的問題或搜尋的地方的潛在解決方案：

* 請檢查您是否已正確設定啟動/停止 VM 解決方案的排程。 若要了解如何設定排程，請參閱[排程](../automation-schedules.md)一文。

* 檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 在入口網站中，移至 [自動化帳戶]，然後在 [程序自動化]**** 下選取 [作業]****。 從 [作業]**** 頁面，尋找下列其中一個 Runbook 中的作業：

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* 驗證您的[執行身分帳戶](../manage-runas-account.md)是否具有您嘗試啟動或停止的 VM 的適當權限。 要瞭解如何檢查資源的權限,請參閱[快速入門:查看使用 Azure 門戶分配給使用者的角色](../../role-based-access-control/check-access.md)。 您需要為「執行為帳戶」所使用的服務主體提供應用程式 ID。 您可以透過移至 Azure 入口網站中的 [自動化帳戶]，在 [帳戶設定]**** 下選取 [執行身分帳戶]****，然後按一下適當的執行身分帳戶來擷取此值。

* 如果明確地排除 VM，則可能無法啟動或停止它們。 已排除部署解決方案之自動化帳戶中 **External_ExcludeVMNames** 變數中設定的 VM。 下列範例示範如何使用 PowerShell 查詢該值。

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>配置:我的某些 VM 無法啟動或停止

### <a name="issue"></a>問題

您已設定啟動/停止 VM 解決方案，但它不會啟動或停止部分已設定的 VM。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

1. 如果使用序列案例，則標記可能遺失或不正確
2. 可能會排除 VM
3. 執行身分帳戶可能沒有足夠的 VM 權限
4. VM 可以有一些項目阻止它啟動或停止

### <a name="resolution"></a>解決方案

請檢閱下列清單，以取得您的問題或搜尋的地方的潛在解決方案：

* 在使用停機期間啟動/停止 VM 解決方案的[順序案例](../automation-solution-vm-management.md)時，必須確保要啟動或停止的每個 VM 都具有正確的標記。 確保要啟動的 VM 具有 `sequencestart` 標記，並且要停止的 VM 具有 `sequencestop` 標記。 這兩個標記都需要正整數值。 您可以使用類似下列範例查詢來尋找包含標記和其值的所有 VM。

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 如果明確地排除 VM，則可能無法啟動或停止它們。 已排除部署解決方案之自動化帳戶中 **External_ExcludeVMNames** 變數中設定的 VM。 下列範例示範如何使用 PowerShell 查詢該值。

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 若要啟動和停止 VM，自動化帳戶的執行身分帳戶必須具有 VM 的適當權限。 要瞭解如何檢查資源的權限,請參閱[快速入門:查看使用 Azure 門戶分配給使用者的角色](../../role-based-access-control/check-access.md)。 您需要為「執行為帳戶」所使用的服務主體提供應用程式 ID。 您可以透過移至 Azure 入口網站中的 [自動化帳戶]，在 [帳戶設定]**** 下選取 [執行身分帳戶]****，然後按一下適當的執行身分帳戶來擷取此值。

* 如果 VM 在啟動或解除配置時出現問題，這種行為可能是由 VM 本身的問題引起的。 部分範例或潛在的問題是，在嘗試關閉、服務停止回應等時會套用更新。 瀏覽至您的 VM 資源並檢查**活動記錄**，以查看記錄中是否有任何錯誤。 您還可以嘗試登入 VM，以查看事件記錄中是否有任何錯誤。 要瞭解有關 VM 故障排除的資訊,請參閱[對 Azure 虛擬機進行故障排除](../../virtual-machines/troubleshooting/index.yml)

* 檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 在入口網站中，移至 [自動化帳戶]，然後在 [程序自動化]**** 下選取 [作業]****。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>配置:我的自訂 Runbook 無法啟動或停止 VM

### <a name="issue"></a>問題

您已撰寫自訂 Runbook 或從 PowerShell 資源庫中下載了一個，但它無法正常運作。

### <a name="cause"></a>原因

失敗的原因可能是許多項目其中一個。 在入口網站中，移至 [自動化帳戶]，然後在 [程序自動化]**** 下選取 [作業]****。 從 [作業]**** 頁面上，查看 Runbook 中的作業以檢視任何作業失敗。

### <a name="resolution"></a>解決方案

建議使用[停機期間啟動/停止 VM 解決方案](../automation-solution-vm-management.md)來啟動和停止 Azure 自動化中的 VM。 此解決方案是由 Microsoft 所撰寫。 Microsoft 不支援自訂的 Runbook。 您可以透過造訪 [Runbook 疑難排解](runbooks.md)文章找到自訂 Runbook 的解決方案。 本文提供了所有類型 Runbook 的一般指引和疑難排解。 檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 在入口網站中，移至 [自動化帳戶]，然後在 [程序自動化]**** 下選取 [作業]****。

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>方案:VM 不會以正確的順序啟動或停止

### <a name="issue"></a>問題

您在解決方案中設定的 VM 無法以正確的順序啟動或停止。

### <a name="cause"></a>原因

這是由 VM 上不正確的標記引起的。

### <a name="resolution"></a>解決方案

請執行下列步驟以確保已正確設定解決方案。

1. 請確定要啟動或停止的所有 VM 都具有 `sequencestart` 或 `sequencestop` 標記，具體取決於您的情況。 這些標記的值必須是正整數。 VM 會根據此值，以遞增的順序處理。
2. 請確定要啟動或停止的 VM 的資源群組位於 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 變數中，具體取決於您的情況。
3. 透過在 WHATIF 參數設為 True 的情況下執行 `SequencedStartStop_Parent` Runbook來測試變更，以預覽您所做的變更。

如需如何使用解決方案按順序啟動和停止 VM 的詳細資訊和其他說明，請參閱[按順序啟動及停止 VM](../automation-solution-vm-management.md)。

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>方案:以 403 禁止狀態啟動/停止 VM 作業失敗

### <a name="issue"></a>問題

您可以在停機期間啟動/停止 VM 解決方案 Runbook 中找到因出現 `403 forbidden` 錯誤而失敗的作業。

### <a name="cause"></a>原因

此問題可能是由未正確設定或已過期的執行身分帳戶所引起。 這也可能是因為自動化帳戶的執行身分帳戶對 VM 資源的權限不足。

### <a name="resolution"></a>解決方案

若要檢查您的執行身分帳戶是否已正確設定，請移至 Azure 入口網站中的 [自動化帳戶]，然後在 [帳戶設定]**** 下選取 [執行身分帳戶]****。 這裡您會看到執行身分帳戶的狀態，如果執行身分帳戶設定不正確或已過期，狀態將會顯示。

如果「運行作為「帳戶配置錯誤,則應刪除並重新創建」運行為「帳戶。 請參考[管理 Azure 自動化作為帳號執行](../manage-runas-account.md)。

如果執行身分帳戶的憑證已過期，請遵循[自我簽署憑證](../manage-runas-account.md#cert-renewal)中所列的步驟來更新憑證。

該問題可能是由於缺少權限所引起。 要瞭解如何檢查資源的權限,請參閱[快速入門:查看使用 Azure 門戶分配給使用者的角色](../../role-based-access-control/check-access.md)。 您需要為「執行為帳戶」所使用的服務主體提供應用程式 ID。 您可以透過移至 Azure 入口網站中的 [自動化帳戶]，在 [帳戶設定]**** 下選取 [執行身分帳戶]****，然後按一下適當的執行身分帳戶來擷取此值。

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>方案:上面未列出我的問題

### <a name="issue"></a>問題

當使用停機期間啟動/停止 VM 解決方案時，您遇到此頁面未列出的問題或意外結果。

### <a name="cause"></a>原因

很多時候，使用舊的和過時的解決方案可能會導致錯誤。

> [!NOTE]
> 在非工作時間解決方案期間啟動/停止 VM 已使用部署解決方案時導入到自動化帳戶中的 Azure 模組進行測試。 該解決方案當前不適用於較新版本的 Azure 模組。 這僅影響用於在非工作時間解決方案期間運行開始/停止 VM 的自動化帳戶。 您仍然可以在其他自動化帳戶中使用較新版本的 Azure 模組,如如何在 Azure[自動化中更新 Azure PowerShell 模組](../automation-update-azure-modules.md)中所述

### <a name="resolution"></a>解決方案

若要解決許多錯誤，建議移除並更新解決方案。 若要了解如何更新解決方案，請參閱[更新停機期間啟動/停止 VM 解決方案](../automation-solution-vm-management.md#update-the-solution)。 此外,您可以檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 在入口網站中，移至 [自動化帳戶]，然後在 [程序自動化]**** 下選取 [作業]****。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帳戶連接,通過將 Azure 社區連接到正確的資源(答案、支援和專家),從而改善客戶體驗。
* 如果需要更多協助，您可以提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。
