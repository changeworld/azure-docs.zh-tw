---
title: Azure 自動化 runbook 錯誤進行疑難排解
description: 瞭解如何使用 Azure 自動化 runbook 來疑難排解和解決可能會遇到的問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.custom: has-adal-ref
ms.openlocfilehash: 70f3c52adc10556c358ed75a75fd023ffb21a813
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82855101"
---
# <a name="troubleshoot-runbook-errors"></a>針對 runbook 錯誤進行疑難排解

 本文說明可能會發生的各種 runbook 錯誤，以及解決問題的方式。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需混合式 Runbook 背景工作角色上的 Az module 安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的 Azure 自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="diagnose-runbook-issues"></a>診斷 runbook 問題

當您在 Azure 自動化中的 runbook 執行期間收到錯誤時，您可以使用下列步驟來協助診斷問題：

1. 請確定您的 runbook 腳本可在本機電腦上順利執行。

    如需語言參考和學習模組，請參閱[PowerShell](/powershell/scripting/overview)檔或[Python](https://docs.python.org/3/)檔。在本機執行您的腳本可以探索並解決常見錯誤，例如：

      * 遺失的模組
      * 語法錯誤
      * 邏輯錯誤

1. 調查 runbook[錯誤資料流程](../automation-runbook-output-and-messages.md#runbook-output)。

    查看這些特定訊息的資料流程，並將它們與本文中記載的錯誤進行比較。

1. 請確定您的節點和自動化工作區具有必要的模組。

    如果您的 runbook 匯入任何模組，請使用匯[入模組](../shared-resources/modules.md#import-modules)中的步驟，確認它們可供您的自動化帳戶使用。 遵循[Azure 自動化中更新 Azure PowerShell 模組](../automation-update-azure-modules.md)中的指示，將您的 PowerShell 模組更新為最新版本。 如需詳細的疑難排解資訊，請參閱針對[模組進行疑難排解](shared-resources.md#modules)。

1. 如果您的 runbook 已暫止或非預期地失敗：

    * 如果執行身分帳戶已過期，請[更新憑證](../manage-runas-account.md#cert-renewal)。
    * 如果您嘗試使用過期的 webhook 來啟動 runbook，請[更新 webhook](../automation-webhooks.md#renew-a-webhook) 。
    * [檢查作業狀態](../automation-runbook-execution.md#job-statuses)以判斷目前的 runbook 狀態和問題的一些可能原因。
    * [將額外的輸出新增](../automation-runbook-output-and-messages.md#message-streams)至 runbook，以識別 runbook 暫止之前會發生什麼事。
    * 處理您的作業所擲回的[任何例外](../automation-runbook-execution.md#exceptions)狀況。

1. 如果混合式 Runbook 背景工作角色上的 runbook 作業或環境沒有回應，請執行此步驟。

    如果您是在混合式 Runbook 背景工作角色上執行 runbook，而不是 Azure 自動化，您可能需要針對混合式背景[工作角色本身進行疑難排解](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>案例： Runbook 因沒有許可權或禁止403錯誤而失敗

### <a name="issue"></a>問題

您的 runbook 失敗，但沒有許可權或禁止403錯誤，或對等。

### <a name="cause"></a>原因

執行身分帳戶可能沒有與您目前的自動化帳戶相同的 Azure 資源許可權。 

### <a name="resolution"></a>解決方案

請確定您的執行身分帳戶具有[許可權，可存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)腳本中使用的任何資源。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>案例：登入 Azure 帳戶失敗

### <a name="issue"></a>問題

當您使用`Connect-AzAccount` Cmdlet 時，會收到下列其中一個錯誤：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果認證資產名稱無效，就會發生這些錯誤。 如果您用來設定自動化認證資產的使用者名稱和密碼無效，也可能會發生這些錯誤。

### <a name="resolution"></a>解決方案

若要判斷錯誤的原因，請遵循下列步驟：

1. 請確定您沒有任何特殊字元。 這些字元包括用來連線到 Azure 之自動化認證資產名稱中的 `\@` 字元。
1. 查看您是否可以在本機 PowerShell ISE 編輯器中，使用儲存在 Azure 自動化認證中的使用者名稱和密碼。 在 PowerShell ISE 中執行下列 Cmdlet。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. 如果您的驗證在本機失敗，則尚未正確設定您的 Azure Active Directory （Azure AD）認證。 若要正確設定 Azure AD 帳戶，請參閱[使用 Azure Active Directory 向 Azure 進行驗證](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)的文章。

1. 如果錯誤似乎是暫時性的，請嘗試將重試邏輯新增至您的驗證常式，讓驗證更健全。

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>案例：執行登入-AzureRMAccount 以登入

### <a name="issue"></a>問題

當您執行 runbook 時，會收到下列錯誤：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

當您不是使用執行身分帳戶或執行身分帳戶已過期時，就會發生此錯誤。 如需詳細資訊，請參閱[管理 Azure 自動化執行身分帳戶](https://docs.microsoft.com/azure/automation/manage-runas-account)。

此錯誤有兩個主要原因：

* AzureRM 或 Az 模組有不同的版本。
* 您正嘗試存取不同訂用帳戶中的資源。

### <a name="resolution"></a>解決方案

如果您在更新一個 AzureRM 或 Az 模組之後收到此錯誤，請將所有模組更新為相同的版本。

如果您正嘗試存取另一個訂用帳戶中的資源，請遵循下列步驟來設定許可權：

1. 移至自動化執行身分帳戶，並複製 [**應用程式識別碼**] 和 [**指紋**]。

    ![複製應用程式識別碼和指紋](../media/troubleshoot-runbooks/collect-app-id.png)

1. 移至*未*裝載自動化帳戶的訂用帳戶**存取控制**，並新增新的角色指派。

    ![存取控制](../media/troubleshoot-runbooks/access-control.png)

1. 新增稍早收集的**應用程式識別碼**。 選取 [**參與者**許可權]。

    ![新增角色指派](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 複製訂用帳戶的名稱。

1. 您現在可以使用下列 runbook 程式碼，測試您的自動化帳戶對另一個訂用帳戶的許可權。 取代`"\<CertificateThumbprint\>"`為在步驟1中複製的值。 取代`"\<SubscriptionName\>"`為在步驟4中複製的值。

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>案例：找不到 Azure 訂用帳戶

### <a name="issue"></a>問題

當您使用`Select-AzureSubscription`、 `Select-AzureRMSubscription`或`Select-AzSubscription` Cmdlet 時，收到下列錯誤：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>錯誤

可能發生這個錯誤的原因有：

* 訂用帳戶名稱無效。
* 嘗試取得訂閱詳細資料的 Azure AD 使用者未設定為訂用帳戶的系統管理員。
* Cmdlet 無法使用。

### <a name="resolution"></a>解決方案

請遵循下列步驟來判斷您是否已向 Azure 驗證，並可存取您嘗試選取的訂用帳戶：

1. 若要確定您的腳本獨立運作，請在 Azure 自動化之外進行測試。
1. 執行`Select-*` Cmdlet 之前，請確定您的腳本會執行[disconnect-azaccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) Cmdlet。
1. 在 Runbook 的開頭加上 `Disable-AzContextAutosave –Scope Process`。 這個 Cmdlet 確保所有認證只會套用到目前 Runbook 的執行。
1. 如果您仍然看到錯誤訊息，請新增的`AzContext`參數來`Connect-AzAccount`修改您的程式碼，然後執行程式碼。

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>案例：處理多個訂用帳戶時 Runbook 失敗

### <a name="issue"></a>問題

執行 runbook 時，runbook 無法管理 Azure 資源。

### <a name="cause"></a>原因

Runbook 在執行時未使用正確的內容。

### <a name="resolution"></a>解決方案

當 runbook 叫用多個 runbook 時，可能會遺失訂用帳戶內容。 若要確保訂用帳戶內容會傳遞至 runbook，請讓用戶端 runbook 將內容傳遞給`Start-AzureRmAutomationRunbook` `AzureRmContext`參數中的 Cmdlet。 使用`Disable-AzureRmContextAutosave` Cmdlet 並將`Scope`參數設定為`Process` ，以確保指定的認證僅用於目前的 runbook。 如需詳細資訊，請參閱[訂用帳戶](../automation-runbook-execution.md#subscriptions)。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>案例：對 Azure 的驗證失敗，因為已啟用多重要素驗證

### <a name="issue"></a>問題

使用您的 Azure 使用者名稱和密碼向 Azure 進行驗證時，您會收到下列錯誤：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果您的 Azure 帳戶有多重要素驗證，您就無法使用 Azure Active Directory 使用者來向 Azure 進行驗證。 相反地，您必須使用憑證或服務主體來進行驗證。

### <a name="resolution"></a>解決方案

若要搭配 Azure 傳統部署模型 Cmdlet 使用憑證，請參閱[建立及新增憑證來管理 azure 服務](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 若要使用具有 Azure Resource Manager Cmdlet 的服務主體，請參閱[使用 Azure 入口網站建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)和使用[Azure Resource Manager 驗證服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>案例： Runbook 失敗並出現「已取消工作」錯誤訊息

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

此錯誤可能是因為使用過期的 Azure 模組所造成。

### <a name="resolution"></a>解決方案

若要解決此錯誤，您可以將 Azure 模組更新為最新版本：

1. 在您的自動化帳戶中，選取 [**模組**]，然後選取 [**更新 Azure 模組**]。
1. 更新大約需要15分鐘的時間。 完成後，請重新執行失敗的 runbook。

若要深入瞭解如何更新您的模組，請參閱[更新 Azure 自動化中的 Azure 模組](../automation-update-azure-modules.md)。

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>案例：詞彙無法辨識為 Cmdlet、函數或腳本的名稱

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

發生此錯誤的原因如下：

* 包含 Cmdlet 的模組不會匯入至自動化帳戶。
* 包含 Cmdlet 的模組已匯入，但已過期。

### <a name="resolution"></a>解決方案

執行下列其中一項工作來解決此錯誤：

* 針對 Azure 模組，請參閱[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，以瞭解如何在您的自動化帳戶中更新您的模組。
* 若為非 Azure 模組，請確定模組已匯入您的自動化帳戶。

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>案例： Cmdlet 在 Azure 自動化的 PnP PowerShell runbook 中失敗

### <a name="issue"></a>問題

當 runbook 直接將「PnP」 PowerShell 產生的物件寫入 Azure 自動化的輸出時，Cmdlet 輸出將無法串流回到「自動化」。

### <a name="cause"></a>原因

當 Azure 自動化處理會叫用 PnP PowerShell Cmdlet （例如， `add-pnplistitem`）而不攔截傳回物件的 runbook 時，最常見的情況是發生此問題。

### <a name="resolution"></a>解決方案

編輯您的腳本，將任何傳回值指派給變數，讓 Cmdlet 不會嘗試將整個物件寫入標準輸出。 腳本可以將輸出資料流程重新導向至 Cmdlet，如下所示。

```azurecli
  $null = add-pnplistitem
```

如果您的腳本會剖析 Cmdlet 輸出，腳本必須將輸出儲存在變數中，並操作變數，而不是只串流輸出。

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>案例：執行 Runbook 時，無法辨識 Cmdlet

### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

當 PowerShell 引擎找不到您在 Runbook 中使用的 Cmdlet 時，就會發生此錯誤。 帳戶可能缺少包含 Cmdlet 的模組、與 runbook 名稱發生名稱衝突，或 Cmdlet 也存在於另一個模組中，而且自動化無法解析名稱。

### <a name="resolution"></a>解決方案

使用下列任何解決方案來修正問題：

* 請確定您已正確輸入 Cmdlet 名稱。
* 請確定 Cmdlet 存在於您的自動化帳戶中，而且沒有任何衝突。 若要確認 Cmdlet 是否存在，請在編輯模式中開啟 runbook，然後搜尋您想要在程式庫中尋找的 Cmdlet，或`Get-Command <CommandName>`執行。 在驗證 Cmdlet 可供帳戶使用，而且沒有與其他 Cmdlet 或 runbook 發生名稱衝突的情況下，請將 Cmdlet 新增至畫布。 請確定您在 runbook 中使用的是有效的參數集。
* 如果您有名稱衝突，而且 Cmdlet 可用於兩個不同的模組，請使用 Cmdlet 的完整名稱來解決此問題。 例如，您可以使用 `ModuleName\CmdletName`。
* 如果您是在混合式背景工作角色群組中執行內部部署 runbook，請確定模組和 Cmdlet 已安裝在裝載混合式背景工作角色的電腦上。

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>案例：對 Disconnect-azaccount 的呼叫不正確的物件參考

### <a name="issue"></a>問題

當您使用時`Add-AzAccount`，會收到此錯誤，這是`Connect-AzAccount` Cmdlet 的別名：

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果 runbook 在呼叫`Add-AzAccount`新增自動化帳戶之前未執行適當的步驟，就會發生此錯誤。 其中一個必要步驟的範例是使用執行身分帳戶進行登入。 如需在 runbook 中使用的正確作業，請參閱[Azure 自動化中的 runbook 執行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)。

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>案例：物件參考未設定為物件的實例

### <a name="issue"></a>問題

當您使用`Wait`參數叫用子 runbook，而輸出資料流程包含物件時，您會收到下列錯誤：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果資料流程包含物件， `Start-AzAutomationRunbook`則不會正確處理輸出資料流程。

### <a name="resolution"></a>解決方案

執行輪詢邏輯，並使用[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) Cmdlet 來取出輸出。 此邏輯的範例定義于此處：

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$jobResults | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>案例：Runbook 因還原序列化物件而失敗

### <a name="issue"></a>問題

您的 Runbook 失敗，錯誤為：

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>原因

如果您的 Runbook 是「PowerShell 工作流程」，它會以還原序列化格式儲存複雜物件，以在暫止工作流程時保存 Runbook 狀態。

### <a name="resolution"></a>解決方案

使用下列任何解決方案來修正此問題：

* 如果您要將複雜物件從一個 Cmdlet 傳送到另一個指令程式`InlineScript` ，請將這些 Cmdlet 包裝在活動中。
* 從複雜物件傳遞您需要的名稱或值，而非傳遞整個物件。
* 使用 PowerShell Runbook，而不是 PowerShell 工作流程 Runbook。



## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>案例：呼叫 webhook 時的400不正確的要求狀態

### <a name="issue"></a>問題

當您嘗試叫用 Azure 自動化 runbook 的 webhook 時，您會收到下列錯誤：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

您嘗試呼叫的 Webhook 已停用或過期。 

### <a name="resolution"></a>解決方案

如果已停用 webhook，您可以透過 Azure 入口網站重新啟用它。 如果 webhook 已過期，您必須先刪除再重新建立。 如果尚未過期，您只能[更新 Webhook](../automation-webhooks.md#renew-a-webhook)。 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>案例：429：要求速率目前太大

### <a name="issue"></a>問題

您在執行 `Get-AzAutomationJobOutput` Cmdlet 時出現下列錯誤訊息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

從具有許多[詳細資訊串流](../automation-runbook-output-and-messages.md#verbose-stream)的 runbook 抓取作業輸出時，可能會發生此錯誤。

### <a name="resolution"></a>解決方案

執行下列其中一個動作來解決此錯誤：

* 編輯 Runbook，並減少它所發出的作業資料流數目。
* 減少在執行 Cmdlet 時所要擷取的資料流數目。 若要這樣做，您可以設定[AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0)指令程式`Stream`的參數值，只取得輸出資料流程。 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>案例： Runbook 作業失敗，因為已超過配置的配額

### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

當作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。 這個配額會套用至所有類型的作業執行工作。 其中一些工作是測試作業、從入口網站啟動作業、使用 webhook 執行作業，或使用 Azure 入口網站或您的資料中心排程作業來執行。 若要深入瞭解自動化的定價，請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解決方案

如果您想要每個月使用超過500分鐘的處理，請將您的訂用帳戶從免費層變更為基本層：

1. 登入您的 Azure 訂用帳戶。
1. 選取要升級的自動化帳戶。
1. 選取 [**設定**]，然後選取 [**定價**]。
1. 選取頁面底部的 [**啟用**]，將您的帳戶升級至基本層。

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>案例： Runbook 作業啟動已嘗試三次，但每次都無法啟動

### <a name="issue"></a>問題

您的 runbook 因下列錯誤而失敗：

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

發生此錯誤的原因是下列其中一個問題：

* **記憶體限制。** 如果工作使用超過 400 MB 的記憶體，作業可能會失敗。 配置給沙箱的記憶體已記載限制會在[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)中找到。 
* **網路通訊端。** Azure 沙箱限制為1000個並行網路通訊端。 如需詳細資訊，請參閱[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。
* **模組不相容。** 模組相依性可能不正確。 在此情況下，您的 runbook 通常`Command not found`會`Cannot bind parameter`傳回或訊息。
* **不使用沙箱的 Active Directory 進行驗證。** 您的 runbook 嘗試呼叫在 Azure 沙箱中執行的可執行檔或子進程。 不支援使用 Azure Active Directory Authentication Library （ADAL）來設定 runbook 以向 Azure AD 進行驗證。
* **太多例外狀況資料。** 您的 runbook 嘗試將太多例外狀況資料寫入輸出資料流程。

### <a name="resolution"></a>解決方案

* **記憶體限制，網路通訊端。** 在記憶體限制內工作的建議方式是在多個 runbook 之間分割工作負載、在記憶體中處理較少的資料、避免從 runbook 寫入不必要的輸出，以及考慮將多少檢查點寫入您的 PowerShell 工作流程 runbook。 使用 clear 方法（例如`$myVar.clear`）來清除變數，並使用`[GC]::Collect`立即執行垃圾收集。 這些動作會減少 Runbook 在執行階段的記憶體使用量。
* **模組不相容。** 遵循[如何在 Azure 自動化中更新 Azure PowerShell 模組](../automation-update-azure-modules.md)中的步驟，更新您的 Azure 模組。
* **不使用沙箱的 Active Directory 進行驗證。** 當您使用 runbook 向 Azure AD 進行驗證時，請確定您的自動化帳戶中有可用的 Azure AD 模組。 請務必授與執行身分帳戶必要的許可權，以執行 runbook 所自動化的工作。

  如果您的 runbook 無法呼叫在 Azure 沙箱中執行的可執行檔或子進程，請在[混合式 runbook 背景工作角色](../automation-hrw-run-runbooks.md)上使用 runbook。 混合式背景工作角色不受限於 Azure 沙箱所擁有的記憶體和網路限制。

* **太多例外狀況資料。** 作業輸出資料流程的限制為 1 MB。 確定您的 runbook 會使用`try`和`catch`區塊，將呼叫包含在可執行檔或子進程。 如果作業擲回例外狀況，請讓程式碼將來自例外狀況的訊息寫入自動化變數中。 這項技術可避免將訊息寫入作業輸出資料流程。

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>案例： PowerShell 工作失敗，並出現「無法叫用方法」錯誤訊息

### <a name="issue"></a>問題

當您在 Azure 中執行的 runbook 中啟動 PowerShell 工作時，您會收到下列錯誤訊息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

此錯誤可能表示在 Azure 沙箱中執行的 runbook 無法以[完整語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)執行。

### <a name="resolution"></a>解決方案

有兩種方法可以解決此錯誤：

* 請使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0)來啟動 runbook，而不是使用 [[啟動-作業](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/start-job?view=powershell-7)]。
* 嘗試在混合式 Runbook 背景工作角色上執行 runbook。

若要深入瞭解此行為和 Azure 自動化 runbook 的其他行為，請參閱[Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>案例：長時間執行的 runbook 無法完成

### <a name="issue"></a>問題

您的 runbook 在執行三個小時後會顯示為 [已停止] 狀態。 您也可能會收到此錯誤：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

這種行為是由 Azure 沙箱中的設計，因為 Azure 自動化內的進程有[公平的共用](../automation-runbook-execution.md#fair-share)監視。 如果進程執行時間超過三小時，公平共用會自動停止 runbook。 超過公平共用時間限制的 runbook 狀態會因 runbook 類型而有所不同。 PowerShell 和 Python Runbook 是設定為 [已停止] 狀態。 「PowerShell 工作流程」Runbook 是設定為 [失敗]狀態。

### <a name="cause"></a>原因

Runbook 會在 Azure 沙箱中的公平共用所允許的3小時限制內執行。

### <a name="resolution"></a>解決方案

其中一個建議的解決方案是在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上執行 Runbook。 混合式背景工作角色不受限於 Azure 沙箱所擁有的三小時公平共用 runbook 限制。 應開發在混合式 Runbook 背景工作角色上執行的 runbook，以在發生未預期的本機基礎結構問題時支援重新開機行為。

另一個解決方案是藉由建立[子 runbook](../automation-child-runbooks.md)來將 runbook 優化。 如果您的 runbook 在多個資源上迴圈執行相同的函式，例如在數個資料庫的資料庫作業中，您可以將函式移至子 runbook。 每個子 runbook 會在個別的進程中平行執行。 此行為可減少完成父代 Runbook 的時間總計。

啟用子 Runbook 案例的 PowerShell Cmdlet 是：

* [開始-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0)。 此 Cmdlet 可讓您啟動 Runbook，並將參數傳遞給 Runbook。
* [AzAutomationJob](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0)。 如果有需要在子 runbook 完成後執行的作業，這個 Cmdlet 可讓您檢查每個子系的工作狀態。

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>案例：關於 get_SerializationSettings 方法的作業資料流程中的錯誤

### <a name="issue"></a>問題

您會在 runbook 的作業串流中看到下列錯誤：

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>原因

此錯誤可能是因為在 runbook 中使用不完整的 AzureRM 遷移至 Az 模組所造成。 這種情況可能會導致 Azure 自動化只使用 AzureRM 模組來啟動 runbook 作業，然後只使用 Az 模組啟動另一個作業，這會導致沙箱損毀。

### <a name="resolution"></a>解決方案

我們不建議在相同的 runbook 中使用 Az 和 AzureRM Cmdlet。 若要深入瞭解這些模組的正確用法，請參閱[遷移至 Az 模組](../shared-resources/modules.md#migrating-to-az-modules)。

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>案例：使用適用于 runbook 或應用程式的 Azure 沙箱時拒絕存取

### <a name="issue"></a>問題

當您的 runbook 或應用程式嘗試在 Azure 沙箱中執行時，環境會拒絕存取。

### <a name="cause"></a>原因

發生此問題的原因是 Azure 沙箱會阻止存取所有跨進程的 COM 伺服器。 例如，沙箱化應用程式或 runbook 無法呼叫 Windows Management Instrumentation （WMI）或 Windows Installer 服務（msiserver .exe）。 

### <a name="resolution"></a>解決方案

如需使用 Azure 沙箱的詳細資訊，請參閱[Runbook 執行環境](../automation-runbook-execution.md#runbook-execution-environment)。

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>案例：在 runbook 中使用 Key Vault 時，禁止的狀態碼無效

### <a name="issue"></a>問題

當您嘗試透過 Azure 自動化 runbook 存取 Azure Key Vault 時，您會收到下列錯誤：

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>原因

此問題的可能原因包括：

* 未使用執行身分帳戶。
* 權限不足。

### <a name="resolution"></a>解決方案

#### <a name="not-using-a-run-as-account"></a>未使用執行身分帳戶

遵循[步驟 5-新增驗證以管理 Azure 資源](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell#add-authentication-to-manage-azure-resources)，以確保您使用的是執行身分帳戶來存取 Key Vault。

#### <a name="insufficient-permissions"></a>權限不足

[將許可權新增至 Key Vault](https://docs.microsoft.com/azure/automation/manage-runas-account#add-permissions-to-key-vault) ，以確保您的執行身分帳戶具有足夠的許可權可以存取 Key Vault。

## <a name="recommended-documents"></a>建議的文件

* [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)
* [在 Azure 自動化中啟動 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)

## <a name="next-steps"></a>後續步驟

如果您在這裡沒有看到您的問題，或無法解決您的問題，請嘗試下列其中一個通道以取得更多支援：

* 透過[Azure 論壇](https://azure.microsoft.com/support/forums/)取得 azure 專家的解答。
* 與[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帳戶聯繫，以改善客戶體驗。 Azure 支援服務會將您連線至 Azure 社區，以提供解答、支援及專家。
* 如果需要更多協助，您可以提出 Azure 支援事件。 移至 [ [Azure 支援] 網站](https://azure.microsoft.com/support/options/)，然後選取 [**取得支援**]。
