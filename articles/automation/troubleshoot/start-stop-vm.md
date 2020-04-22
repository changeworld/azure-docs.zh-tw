---
title: 在非工作時間解決方案中對啟動/停止 VM 進行故障排除
description: 本文提供有關對啟動/停止 VM 解決方案進行故障排除的資訊。
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679155"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>疑難排解停機期間啟動/停止 VM 解決方案

本文提供有關在非工作時間使用啟動/停止 VM 時出現的疑難解答問題的資訊。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明,請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶,可以使用[「如何更新 Azure 自動化 中的 Azure PowerShell」模組](../automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>方案:在非工作時間啟動/停止 VM 解決方案無法正確部署

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

部署可能會失敗,原因如下:

1. 所選區域中已有具有相同名稱的自動化帳戶。
2. 策略是不允許在非工作時間解決方案期間部署開始/停止 VM。
3. 未`Microsoft.OperationsManagement`註冊`Microsoft.Insights`、`Microsoft.Automation`或資源類型。
4. 日誌分析工作區已鎖定。
5. 您具有 AzureRM 模組的過時版本,或在非工作時間解決方案期間啟動/停止 VM。

### <a name="resolution"></a>解決方案

檢視以下修補程式,以找到問題的潛在解決方案:

* 自動化帳戶在 Azure 區域中必須是唯一的，即使位於不同的資源群組。 檢查目標區域中的現有自動化帳戶。
* 現有策略可防止部署"開始/停止 VM"在非工作時間解決方案期間所需的資源。 請移至您在 Azure 入口網站中的原則指派，並檢查您是否有不允許這項資源部署的原則指派。 若要深入了解，請參閱 [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md)。
* 要部署「開始/停止 VM」解決方案,需要將訂閱註冊到以下 Azure 資源命名空間:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   請參閱[解決資源提供程式註冊的錯誤](../../azure-resource-manager/templates/error-register-resource-provider.md),以瞭解有關註冊提供程式時的錯誤的更多資訊。
* 如果您有 Log Analytics 工作區的鎖定，請移至您在 Azure 入口網站中的工作區，並移除資源的任何鎖定。
* 如果上述解決方案不能解決問題,請按照[「更新解決方案」](../automation-solution-vm-management.md#update-the-solution)下的說明重新部署"開始/停止"解決方案。

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>配置:所有 VM 無法啟動或停止

### <a name="issue"></a>問題

您已配置了在非工作時間解決方案中的啟動/停止 VM,但它不會啟動或停止所有 VM。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

1. 計劃配置不正確。
2. "運行為"帳戶可能未正確配置。
3. Runbook 可能遇到錯誤。
4. VM 可能已被排除。

### <a name="resolution"></a>解決方案

檢視以下清單,檢視您的問題的潛在解決方案:

* 檢查您是否在非工作時間解決方案期間正確配置了"開始/停止 VM"的計畫。 若要了解如何設定排程，請參閱[排程](../automation-schedules.md)一文。

* 檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 從以下執行簿之一尋找工作:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* 驗證您的[「運行為「帳戶](../manage-runas-account.md)對嘗試啟動或停止的 VM 具有適當的許可權。 要瞭解如何檢查資源的權限,請參閱[快速入門:查看使用 Azure 門戶分配給使用者的角色](../../role-based-access-control/check-access.md)。 您需要為運行 As 帳戶所使用的服務主體提供應用程式 ID。 您可以通過訪問 Azure 門戶中的自動化帳戶、選擇「在**帳戶設置****下作為帳戶運行」** 以及單擊相應的"以「帳戶運行」來檢索此值。

* 如果明確地排除 VM，則可能無法啟動或停止它們。 排除的 VM`External_ExcludeVMNames`設置在部署解決方案的自動化帳戶中的變數中。 下面的範例展示如何使用PowerShell查詢該值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>配置:我的某些 VM 無法啟動或停止

### <a name="issue"></a>問題

您已配置了在非工作時間解決方案中的開始/停止 VM,但它不會啟動或停止配置的某些 VM。

### <a name="cause"></a>原因

這個錯誤可能是下列其中一項原因所造成：

1. 在序列方案中,標記可能丟失或不正確。
2. 可能會排除 VM。
3. "運行為帳戶"可能沒有足夠的許可權。
4. VM 可能有阻止其啟動或停止的問題。

### <a name="resolution"></a>解決方案

請檢閱下列清單，以取得您的問題或搜尋的地方的潛在解決方案：

* 在非工作時間解決方案中使用"開始/停止 VM"[的序列方案](../automation-solution-vm-management.md)時,必須確保要啟動或停止的每個 VM 都具有正確的標記。 確保要啟動的 VM 具有 `sequencestart` 標記，並且要停止的 VM 具有 `sequencestop` 標記。 這兩個標記都需要正整數值。 您可以使用類似下列範例查詢來尋找包含標記和其值的所有 VM。

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* 如果 VM 被顯式排除,則可能無法啟動或停止它們。 排除的 VM`External_ExcludeVMNames`設置在部署解決方案的自動化帳戶中的變數中。 下面的範例展示如何使用PowerShell查詢該值。

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* 要啟動和停止 VM,自動化帳戶的「運行為帳戶」必須對 VM 具有適當的許可權。 要瞭解如何檢查資源的權限,請參閱[快速入門:查看使用 Azure 門戶分配給使用者的角色](../../role-based-access-control/check-access.md)。 您需要為運行 As 帳戶所使用的服務主體提供應用程式 ID。 您可以通過存取 Azure 門戶中的自動化帳戶、在**帳戶設置**下選擇 **「作為帳戶運行」** 並按一下相應的" 以「身份執行帳戶」 來檢索此值。

* 如果 VM 在啟動或取消分配時出現問題,則 VM 本身可能存在問題。 例如,當 VM 嘗試關閉、服務掛起等時,將應用更新。 瀏覽至您的 VM 資源並檢查**活動記錄**，以查看記錄中是否有任何錯誤。 您可能還會嘗試登錄到 VM 以查看事件日誌中是否存在任何錯誤。 要瞭解有關 VM 故障排除的資訊,請參閱[對 Azure 虛擬機進行故障排除](../../virtual-machines/troubleshooting/index.yml)

* 檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 在門戶中,轉到您的自動化帳戶,並在 **「過程自動化**」下選擇**作業**。

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>配置:我的自訂 Runbook 無法啟動或停止 VM

### <a name="issue"></a>問題

您已撰寫自訂 Runbook 或從 PowerShell 資源庫中下載了一個，但它無法正常運作。

### <a name="cause"></a>原因

失敗的原因可能有很多。 跳到 Azure 門戶中的自動化帳戶,並在 **「過程自動化**」 下選擇**工作**。 從 [作業] 頁面上，查看 Runbook 中的作業以檢視任何作業失敗。

### <a name="resolution"></a>解決方案

建議:

* 在[非工作時間使用"開始/停止 VM"解決方案](../automation-solution-vm-management.md)在 Azure 自動化中啟動和停止 VM。 此解決方案是由 Microsoft 所撰寫。 

* 請注意,Microsoft 不支援自定義 Runbook。 您可能會從 Runbook 故障排除中找到自訂[Runbook 的解決方案](runbooks.md)。 檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>方案:VM 不會以正確的順序啟動或停止

### <a name="issue"></a>問題

您在解決方案中設定的 VM 無法以正確的順序啟動或停止。

### <a name="cause"></a>原因

此問題是由 VM 上的標記不正確引起的。

### <a name="resolution"></a>解決方案

請執行下列步驟以確保已正確設定解決方案。

1. 請確定要啟動或停止的所有 VM 都具有 `sequencestart` 或 `sequencestop` 標記，具體取決於您的情況。 這些標記的值必須是正整數。 VM 會根據此值，以遞增的順序處理。
2. 請確定要啟動或停止的 VM 的資源群組位於 `External_Start_ResourceGroupNames` 或 `External_Stop_ResourceGroupNames` 變數中，具體取決於您的情況。
3. 通過將`WHATIF`參數設置為 True`SequencedStartStop_Parent`的執行 Runbook 來預覽更改,即可測試更改。
4. 有關使用解決方案按順序啟動和停止 VM 的更多資訊,請參閱[按順序開始/停止 VM。](../automation-solution-vm-management.md)

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>方案:在下班時間作業期間啟動/停止 VM 失敗,出現 403 個禁止錯誤

### <a name="issue"></a>問題

在非工作時間解決方案執行簿期間`403 forbidden`,您會發現"開始/停止 VM"出錯的作業。

### <a name="cause"></a>原因

此問題可能由配置不當或過期的"運行作為"帳戶引起。 可能還因為運行為帳戶對 VM 資源的許可權不足。

### <a name="resolution"></a>解決方案

要驗證「運行為「帳戶是否正確配置,請轉到 Azure 門戶中的自動化帳戶,然後選擇」在**帳戶設置**下**作為帳戶運行」。** 如果"運行"帳戶配置不正確或已過期,則狀態將顯示此情況。

如果「運行作為「帳戶配置錯誤,則應刪除並重新創建」運行為「帳戶。 請參考[管理 Azure 自動化作為帳號執行](../manage-runas-account.md)。

如果您的「運行即「帳戶的證書已過期,請參閱[自簽名證書續訂](../manage-runas-account.md#cert-renewal)中的步驟以續訂證書。

如果缺少權限,請參閱[快速入門:查看使用 Azure 門戶分配給使用者的角色](../../role-based-access-control/check-access.md)。 您必須為運行 As 帳戶所使用的服務主體提供應用程式 ID。 您可以通過訪問 Azure 門戶中的自動化帳戶、選擇「在**帳戶設置****下作為帳戶運行」** 以及單擊相應的"以「帳戶運行」來檢索此值。

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>方案:上面未列出我的問題

### <a name="issue"></a>問題

在非工作時間使用未在此頁面上列出的「開始/停止 VM」解決方案時,您會遇到問題或意外結果。

### <a name="cause"></a>原因

很多時候，使用舊的和過時的解決方案可能會導致錯誤。

> [!NOTE]
> 在非工作時間啟動/停止 VM 解決方案已使用部署解決方案時導入到自動化帳戶中的 Azure 模組進行了測試。 該解決方案當前不適用於 Azure 模組的較新版本。 這僅影響用於在非工作時間解決方案期間運行開始/停止 VM 的自動化帳戶。 您仍然可以在其他自動化帳戶中使用較新版本的 Azure 模組,如如何在 Azure[自動化中更新 Azure PowerShell 模組](../automation-update-azure-modules.md)中所述

### <a name="resolution"></a>解決方案

為了解決許多錯誤,建議在[非工作時間刪除並更新開始/停止 VM 解決方案](../automation-solution-vm-management.md#update-the-solution)。 此外,您可以檢查[作業流](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal)以查找任何錯誤。 

## <a name="next-steps"></a>後續步驟

如果您在上面看不到問題或無法解決問題,請嘗試以下管道之一以獲取其他支援:

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)連接 ,官方 Microsoft Azure 帳戶通過將 Azure 社區連接到正確的資源(答案、支援和專家)來改善客戶體驗。
* 提出 Azure 支援事件。 跳到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **「取得支援**」。