---
title: 針對 Azure 自動化 Runbook 問題進行疑難排解
description: 此文章說明如何針對 Azure 自動化 Runbook 問題進行疑難排解及解決問題。
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: automation
ms.custom: has-adal-ref
ms.openlocfilehash: 1cbb5be8c1a4045b218c0e6bf5ac7ed0b901aa80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87904797"
---
# <a name="troubleshoot-runbook-issues"></a>針對 Runbook 問題進行疑難排解

 此文章描述可能發生的 Runbook 問題及解決方法。 如需一般資訊，請參閱 [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)。

## <a name="diagnose-runbook-issues"></a>診斷 Runbook 問題

當您在 Azure 自動化中執行 Runbook 期間收到錯誤時，您可以使用下列步驟來協助診斷問題：

1. 確認您的 Runbook 指令碼可在本機電腦上順利執行。

    如需語言參考和學習課程模組，請參閱 [PowerShell 文件](/powershell/scripting/overview)或 [Python 文件](https://docs.python.org/3/) \(英文\)。在本機執行您的指令碼，可以探索並解決常見錯誤，例如：

      * 遺失模組
      * 語法錯誤
      * 邏輯錯誤

1. 調查 Runbook [錯誤資料流](../automation-runbook-output-and-messages.md#runbook-output)。

    查看這些資料流中的特定訊息，並將其與此文章中記載的錯誤進行比較。

1. 確認您的節點與自動化工作區具有必要模組。

    如果您的 Runbook 會匯入任何模組，請使用[匯入模組](../shared-resources/modules.md#import-modules)中的步驟來確認這些模組可供您的自動化帳戶使用。 遵循[更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)中的指示，將您的 PowerShell 模組更新為最新版本。 如需更多疑難排解資訊，請參閱[針對模組進行疑難排解](shared-resources.md#modules)。

1. 如果您的 Runbook 已暫止或非預期地失敗：

    * 如果執行身分帳戶已過期，請[更新憑證](../manage-runas-account.md#cert-renewal)。
    * 如果您正嘗試使用過期的 Webhook 來啟動 Runbook，請[更新 Webhook](../automation-webhooks.md#renew-a-webhook)。
    * [檢查作業狀態](../automation-runbook-execution.md#job-statuses)，來判斷目前的 Runbook 狀態及問題的一些可能原因。
    * [新增其他輸出](../automation-runbook-output-and-messages.md#monitor-message-streams)到 Runbook，以識別暫止 Runbook 之前發生什麼事。
    * [處理作業擲回的任何例外狀況](../automation-runbook-execution.md#exceptions)。

1. 如果混合式 Runbook 背景工作角色上的 Runbook 作業或環境沒有回應，請執行此步驟。

    如果您正在混合式 Runbook 背景工作角色上 (而不是在 Azure 自動化中) 執行 Runbook，可能就需要[對混合式背景工作角色本身進行疑難排解](hybrid-runbook-worker.md) \(部分機器翻譯\)。

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>案例：Runbook 因為「沒有權限」或「禁止」403 錯誤而失敗

### <a name="issue"></a>問題

您的 Runbook 因為「沒有權限」或「禁止」403 錯誤或對等錯誤而失敗。

### <a name="cause"></a>原因

執行身分帳戶對於 Azure 資源可能沒有與您目前自動化帳戶相同的權限。 

### <a name="resolution"></a>解決方案

確定您的執行身分帳戶[有權存取您指令碼中所使用的任何資源](../../role-based-access-control/role-assignments-portal.md) \(部分機器翻譯\)。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>案例：登入 Azure 帳戶失敗

### <a name="issue"></a>問題

當您使用 `Connect-AzAccount` Cmdlet 時，會收到下列其中一個錯誤：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果認證資產名稱無效，就會發生這些錯誤。 如果您用來設定自動化認證資產的使用者名稱與密碼無效，可能也會發生這些錯誤。

### <a name="resolution"></a>解決方案

若要判斷發生錯誤的原因，請遵循下列步驟：

1. 確定您不具任何特殊字元。 這些字元包括用來連線到 Azure 之自動化認證資產名稱中的 `\@` 字元。
1. 檢查以查看您是否可以使用儲存於本機 PowerShell ISE 編輯器之 Azure 自動化認證中的使用者名稱與密碼。 在 PowerShell ISE 中，執行下列 Cmdlet。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. 如果您的驗證在本機失敗，表示您尚未正確設定 Azure Active Directory (Azure AD) 認證。 若要正確設定 Azure AD 帳戶，請參閱 [使用 Azure Active Directory 向 Azure 進行驗證](../automation-use-azure-ad.md)的文章。

1. 如果此錯誤似乎是暫時性的，請嘗試將重試邏輯新增至您的驗證常式，讓驗證變得更強固。

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>案例：執行 Login-AzureRmAccount 進行登入

### <a name="issue"></a>問題

當您執行 Runbook 時，收到下列錯誤：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

如果您未使用執行身分帳戶，或執行身分帳戶已過期，則可能會發生此錯誤。 如需詳細資訊，請參閱[管理 Azure 自動化執行身分帳戶](../manage-runas-account.md) \(部分機器翻譯\)。

此錯誤有兩個主要原因：

* 有不同版本的 AzureRM 或 Az 模組。
* 您正嘗試存取不同訂用帳戶中的資源。

### <a name="resolution"></a>解決方案

如果您在更新一個 AzureRM 或 Az 模組之後收到此錯誤，則需將您的所有模組更新為相同版本。

如果您正嘗試存取另一個訂用帳戶中的資源，請遵循下列步驟來設定權限：

1. 移至自動化執行身分帳戶，然後複製**應用程式識別碼**和**指紋**。

    ![複製應用程式識別碼和指紋](../media/troubleshoot-runbooks/collect-app-id.png)

1. 移至訂用帳戶的**存取控制**，自動化帳戶「不會」裝載於其中，並新增角色指派。

    ![存取控制](../media/troubleshoot-runbooks/access-control.png)

1. 新增先前收集到的**應用程式識別碼**。 選取 [參與者] 權限。

    ![新增角色指派](../media/troubleshoot-runbooks/add-role-assignment.png)

1. 複製訂用帳戶的名稱。

1. 您現在可以使用下列 Runbook 程式碼，來測試您的自動化帳戶對另一個訂用帳戶的權限。 將 `"\<CertificateThumbprint\>"` 取代為在步驟 1 中複製的值。 將 `"\<SubscriptionName\>"` 取代為在步驟 4 中複製的值。

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

您在使用 `Select-AzureSubscription`、`Select-AzureRMSubscription` 或 `Select-AzSubscription` Cmdlet 時，收到下列錯誤：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>錯誤

此錯誤可能會在下列情況中發生：

* 訂用帳戶名稱無效。
* 未將嘗試取得訂用帳戶詳細資料的 Azure AD 使用者設定為訂用帳戶的管理員。
* 無法使用此 Cmdlet。

### <a name="resolution"></a>解決方案

遵循下列步驟，以判斷您是否已向 Azure 進行驗證，並取得您嘗試選取之訂用帳戶的存取權：

1. 為了確定您的指令碼能夠獨立運作，請在 Azure 自動化外部進行測試。
1. 執行 `Select-*` Cmdlet 之前，先確定您的指令碼會執行 [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0) \(英文\) Cmdlet。
1. 在 Runbook 的開頭加上 `Disable-AzContextAutosave –Scope Process`。 這個 Cmdlet 確保所有認證只會套用到目前 Runbook 的執行。
1. 如果您仍然看到錯誤訊息，可藉由新增 `Connect-AzAccount` 的 `AzContext` 參數來修改您的程式碼，然後執行程式碼。

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>案例：Runbook 在處理多個子訂用帳戶時發生失敗

### <a name="issue"></a>問題

執行 Runbook 時，此 Runbook 無法管理 Azure 資源。

### <a name="cause"></a>原因

Runbook 在執行時未使用正確的內容。

### <a name="resolution"></a>解決方案

當某個 Runbook 叫用多個 Runbook 時，可能會遺失訂用帳戶內容。 為了確保訂用帳戶的內容會傳遞至 Runbook，必須讓用戶端 Runbook 將內容放在 `AzureRmContext` 參數中傳遞到 `Start-AzureRmAutomationRunbook` Cmdlet。 使用 `Disable-AzureRmContextAutosave` Cmdlet 搭配設定為 `Process` 的 `Scope` 參數，以確保指定的認證僅用於目前的 Runbook。 如需詳細資訊，請參閱[訂用帳戶](../automation-runbook-execution.md#subscriptions)。

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

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>案例：向 Azure 進行的驗證因為已啟用多重要素驗證而失敗

### <a name="issue"></a>問題

當您使用 Azure 使用者名稱與密碼來向 Azure 進行驗證時，收到下列錯誤：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果您的 Azure 帳戶使用多重要素驗證，您就無法使用 Azure Active Directory 使用者來向 Azure 進行驗證。 您必須改為使用憑證或服務主體來驗證。

### <a name="resolution"></a>解決方案

若要使用傳統的執行身份帳戶搭配 Azure 傳統部署模型 Cmdlet，請參閱 [建立傳統執行帳戶以管理 azure 服務](../automation-create-standalone-account.md#create-a-classic-run-as-account)。 若要搭配 Azure Resource Manager Cmdlet 使用服務主體，請參閱[使用 Azure 入口網站來建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)和[使用 Azure Resource Manager 來驗證服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>案例：Runbook 失敗並出現「工作已取消」錯誤訊息

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

此錯誤可能是因為使用過期的 Azure 模組所造成。

### <a name="resolution"></a>解決方案

您可以藉由將 Azure 模組更新為最新版本來解決此錯誤：

1. 在您的自動化帳戶中，選取 [模組]，然後選取 [更新 Azure 模組]。
1. 更新大約需要 15 分鐘。 完成後，重新執行失敗的 Runbook。

若要深入了解如何更新模組，請參閱[更新 Azure 自動化中的 Azure 模組](../automation-update-azure-modules.md)。

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>案例：無法將字詞辨識為 Cmdlet、函式或指令碼的名稱

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

此錯誤的發生原因如下：

* 未將包含 Cmdlet 的模組匯入到自動化帳戶。
* 已匯入包含 Cmdlet 的模組，但該模組已過期。

### <a name="resolution"></a>解決方案

執行下列其中一個工作來解決此錯誤：

* 針對 Azure 模組，請參閱[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，以了解如何在您的自動化帳戶中更新模組。
* 針對非 Azure 模組，則需確定已將該模組匯入到您的自動化帳戶中。

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>案例：Cmdlet 在 Azure 自動化的 PnP PowerShell Runbook 中失敗

### <a name="issue"></a>問題

當 Runbook 直接將 PnP PowerShell 產生的物件寫入至 Azure 自動化輸出時，無法將 Cmdlet 輸出串流處理回到自動化。

### <a name="cause"></a>原因

此問題最常發生在 Azure 自動化處理會叫用 PnP PowerShell Cmdlet (例如 `add-pnplistitem`) 的 Runbook，而不會攔截傳回物件。

### <a name="resolution"></a>解決方案

編輯您的指令碼，以將任何傳回值指派給變數，讓 Cmdlet 不會嘗試將整個物件寫入至標準輸出。 指令碼可以將輸出資料流重新導向至 Cmdlet，如下所示。

```azurecli
  $null = add-pnplistitem
```

如果您的指令碼會剖析 Cmdlet 輸出，指令碼就必須將輸出儲存於變數中，並操作變數，而不只是串流處理輸出。

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

當 PowerShell 引擎找不到您在 Runbook 中使用的 Cmdlet 時，就會發生此錯誤。 這可能是因為帳戶中沒有包含該 Cmdlet 的模組、與 Runbook 名稱發生名稱衝突，或該 Cmdlet 也存在於另一個模組中，而且自動化無法解析名稱。

### <a name="resolution"></a>解決方案

使用下列任一個解決方案來修正問題：

* 確定您已正確輸入 Cmdlet 名稱。
* 確認 Cmdlet 存在於您的自動化帳戶中且沒有發生衝突。 若要確認 Cmdlet 是否存在，請以編輯模式開啟 Runbook，然後搜尋您想要在程式庫中尋找的 Cmdlet，或執行 `Get-Command <CommandName>`。 在驗證 Cmdlet 可供帳戶使用，且未與其他 Cmdlet 或 Runbook 發生名稱衝突之後，將該 Cmdlet 新增至畫布。 請確定您正在 Runbook 中使用有效的參數集。
* 如果發生名稱衝突且 Cmdlet 可在兩個不同的模組中使用，則可使用 Cmdlet 的完整名稱來解決此問題。 例如，您可以使用 `ModuleName\CmdletName`。
* 如果您是在混合式背景工作角色群組中以內部部署方式執行 Runbook，則需確認模組和 Cmdlet 均安裝於裝載混合式背景工作角色的機器上。

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>案例：呼叫 Add-AzAccount 時物件參考不正確

### <a name="issue"></a>問題

當您使用 `Add-AzAccount` (即 `Connect-AzAccount` Cmdlet 的別名) 時，收到此錯誤：

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果 Runbook 在呼叫 `Add-AzAccount` 以新增自動化帳戶之前並未執行適當的步驟，就會發生此錯誤。 必要步驟的範例之一就是使用執行身分帳戶進行登入。 如需在 Runbook 中使用的正確作業，請參閱 [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md) \(部分機器翻譯\)。

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>案例：未將物件參考設定為物件的執行個體

### <a name="issue"></a>問題

您會在搭配 `Wait` 參數叫用子 Runbook，且輸出資料流包含物件時，收到下列錯誤：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

如果資料流包含物件，`Start-AzAutomationRunbook` 就不會正確處理輸出資料流。

### <a name="resolution"></a>解決方案

實作輪詢邏輯，並使用 [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) \(英文\) Cmdlet 來擷取輸出。 這裡定義了此邏輯的範例：

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
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
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

使用下列任一個解決方案來修正此問題：

* 如果您要透過管線將複雜物件從一個 Cmdlet 傳送到另一個，則需將這些 Cmdlet 包裝於 `InlineScript` 活動中。
* 從複雜物件傳遞您需要的名稱或值，而非傳遞整個物件。
* 使用 PowerShell Runbook，而不是 PowerShell 工作流程 Runbook。

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>案例：呼叫 Webhook 時出現「400 不正確的要求」狀態

### <a name="issue"></a>問題

當您嘗試叫用 Azure 自動化 Runbook 的 Webhook 時，收到下列錯誤：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

您嘗試呼叫的 Webhook 已停用或過期。 

### <a name="resolution"></a>解決方案

如果 Webhook 已停用，您可以透過 Azure 入口網站重新啟用。 如果 Webhook 已過期，則您必須先刪除後再重新建立。 如果尚未過期，您只能[更新 Webhook](../automation-webhooks.md#renew-a-webhook)。 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>案例：429：目前的要求率太大

### <a name="issue"></a>問題

您在執行 `Get-AzAutomationJobOutput` Cmdlet 時出現下列錯誤訊息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

從具備許多[詳細資訊資料流](../automation-runbook-output-and-messages.md#monitor-verbose-stream)的 Runbook 中擷取作業輸出時，可能就會發生此錯誤。

### <a name="resolution"></a>解決方案

執行下列其中一個動作來解決此錯誤：

* 編輯 Runbook，並減少它所發出的作業資料流數目。
* 減少在執行 Cmdlet 時所要擷取的資料流數目。 若要這樣做，您可以設定 [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.7.0) \(英文\) Cmdlet 的 `Stream` 參數值，以便僅擷取輸出資料流。 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>案例：Runbook 作業因為超過已配置的配額而失敗

### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

當作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。 這個配額會套用至所有類型的作業執行工作。 這其中的部分工作是測試作業、從入口網站中啟動作業、使用 Webhook 執行作業，或者使用 Azure 入口網站或您的資料中心排程要執行的作業。 若要深入了解自動化的價格，請參閱[自動化價格](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解決方案

如果您每個月都想要使用超過 500 分鐘的處理時間，請將您的訂用帳戶從免費層變更為基本層：

1. 登入您的 Azure 訂用帳戶。
1. 選取要升級的自動化帳戶。
1. 選取 [設定]，然後選取 [定價]。
1. 選取頁面底部的 [啟用]，以將您的帳戶升級至基本層。

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>案例： Runbook 輸出資料流程大於 1 MB

### <a name="issue"></a>問題

您在 Azure 沙箱中執行的 runbook 會失敗，並出現下列錯誤：

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>原因

因為您的 runbook 嘗試將太多例外狀況資料寫入輸出資料流程，所以會發生此錯誤。

### <a name="resolution"></a>解決方案

作業輸出資料流程有 1 MB 的限制。 確定您的 Runbook 會使用 `try` 和 `catch` 區塊，來括住對可執行檔或子處理序的呼叫。 如果作業擲回例外狀況，則讓程式碼將來自例外狀況的訊息寫入到自動化變數中。 此技術可避免將訊息寫入到作業輸出資料流。 針對執行中的混合式 Runbook 背景工作角色，會顯示截斷為 1 MB 的輸出資料流程，而不會出現任何錯誤訊息。

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>案例：已嘗試啟動 Runbook 作業三次，但無法每次都順利啟動

### <a name="issue"></a>問題

您的 Runbook 失敗，並發生下列錯誤：

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

此錯誤是因為下列其中一個問題而引發：

* **記憶體限制。** 作業若使用超過 400 MB 的記憶體，可能就會失敗。 配置給沙箱的記憶體限制記載於[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)中。 

* **網路通訊端。** Azure 沙箱會限制為 1,000 個並行網路通訊端。 如需詳細資訊，請參閱[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。

* **模組不相容。** 模組相依性可能不正確。 在此案例中，您的 Runbook 通常會傳回 `Command not found` 或 `Cannot bind parameter` 訊息。

* **未針對沙箱向 Active Directory 進行驗證。** 您的 Runbook 已嘗試呼叫在 Azure 沙箱中執行的可執行檔或子處理序。 不支援使用 Azure Active Directory 驗證程式庫 (ADAL) 來設定 Runbook 以向 Azure AD 進行驗證。

### <a name="resolution"></a>解決方案

* **記憶體限制，網路通訊端。** 建議在記憶體限制內運作的方式是，在多個 Runbook 之間分割工作負載、不要在記憶體中處理太多資料、避免寫入來自您 Runbook 的不必要輸出，以及考慮寫入至您 PowerShell 工作流程 Runbook 的檢查點數目。 使用 clear 方法 (例如 `$myVar.clear`) 來清除變數，並使用 `[GC]::Collect` 立即執行記憶體回收。 這些動作會減少 Runbook 在執行階段的記憶體使用量。

* **模組不相容。** 遵循[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)步驟來更新 Azure 模組。

* **未針對沙箱向 Active Directory 進行驗證。** 當您使用 Runbook 以向 Azure AD 進行驗證時，確認您的自動化帳戶中有可用的 Azure AD 模組。 請務必為執行身分帳戶授與必要權限，以執行 Runbook 自動化的工作。

  如果您的 Runbook 無法呼叫在 Azure 沙箱中執行的可執行檔或子處理序，則需在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上使用 Runbook。 混合式背景工作角色不會受限於 Azure 沙箱所受到的記憶體和網路限制。

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>案例：PowerShell 作業失敗且出現「無法叫用方法」錯誤訊息

### <a name="issue"></a>問題

當您在 Azure 內執行的 Runbook 中啟動 PowerShell 作業時，收到下列錯誤訊息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

此錯誤可能表示在 Azure 沙箱中執行的 Runbook 無法以[完整語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)執行。

### <a name="resolution"></a>解決方案

有兩種方法可以解決此錯誤：

* 不使用 [Start-Job](/powershell/module/microsoft.powershell.core/start-job?view=powershell-7) \(英文\)，而是改為使用 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) \(英文\) 來啟動 Runbook。
* 嘗試在混合式 Runbook 背景工作角色上執行 Runbook。

若要深入了解此行為和 Azure 自動化 Runbook 的其他行為，請參閱 [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>案例：長時間執行的 Runbook 無法完成

### <a name="issue"></a>問題

您的 Runbook 在執行三小時之後顯示為「已停止」狀態。 您可能也會收到此錯誤：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

這是 Azure 沙箱中故意設計的行為，原因是 Azure 自動化中會進行處理序的[公平共用](../automation-runbook-execution.md#fair-share)監視。 如果某個處理序執行時間超過三小時，公平共用就會自動停止 Runbook。 超過公平共用時間限制的 Runbook 狀態會依 Runbook 類型而有所不同。 PowerShell 和 Python Runbook 均設定為「已停止」狀態。 PowerShell 工作流程 Runbook 會設定為「失敗」狀態。

### <a name="cause"></a>原因

Runbook 的執行時間已超過 Azure 沙箱中公平共用所允許的三小時限制。

### <a name="resolution"></a>解決方案

其中一個建議的解決方案是在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上執行 Runbook。 混合式背景工作角色不會受限於 Azure 沙箱所受到的三小時公平共用 Runbook 限制。 開發在混合式 Runbook 背景工作角色上執行的 Runbook 時，應使其支援重新啟動行為，以免發生非預期的本機基礎結構問題。

另一個解決方案是藉由建立[子 Runbook](../automation-child-runbooks.md) 來將 Runbook 最佳化。 例如，如果您的 Runbook 會在數個資料庫上的某個資料庫作業中，針對數個資源重複執行同一個函式，則您可以將該函式移到子 Runbook。 每個子 Runbook 都會在個別處理序中平行執行。 此行為可減少完成父代 Runbook 的時間總計。

啟用子 Runbook 案例的 PowerShell Cmdlet 是：

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) \(英文\)。 此 Cmdlet 可讓您啟動 Runbook，並將參數傳遞給 Runbook。
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob?view=azps-3.7.0) \(英文\)。 如果有需要在子 Runbook 完成後執行的作業，此 Cmdlet 可讓您檢查每個子項的作業狀態。

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>案例：作業資料流中有關 get_SerializationSettings 方法的錯誤

### <a name="issue"></a>問題

您在 Runbook 的作業資料流中看到下列錯誤：

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

此錯誤可能是因為在 Runbook 中，使用從 AzureRM 到 Az 模組的不完整移轉所造成。 此情況可能導致 Azure 自動化只使用 AzureRM 模組來啟動 Runbook 作業，接著只使用 Az 模組來啟動另一個作業，而這會導致沙箱損毀。

### <a name="resolution"></a>解決方案

我們不建議在同一個 Runbook 中使用 Az 和 AzureRM Cmdlet。 若要深入了解模組的正確用法，請參閱[移轉至 Az 模組](../shared-resources/modules.md#migrate-to-az-modules)。

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>案例：使用適用於 Runbook 或應用程式的 Azure 沙箱時存取遭拒

### <a name="issue"></a>問題

當您的 Runbook 或應用程式嘗試在 Azure 沙箱中執行時，環境會拒絕存取。

### <a name="cause"></a>原因

發生此問題的原因是，Azure 沙箱會阻止存取所有外部處理序 COM 伺服器。 例如，沙箱化的應用程式或 Runbook 無法呼叫 Windows Management Instrumentation (WMI) 或 Windows Installer 服務 (msiserver.exe)。 

### <a name="resolution"></a>解決方案

如需使用 Azure 沙箱的詳細資訊，請參閱 [Runbook 執行環境](../automation-runbook-execution.md#runbook-execution-environment)。

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>案例：在 Runbook 中使用 Key Vault 時，「禁止」狀態碼無效

### <a name="issue"></a>問題

當您嘗試透過 Azure 自動化 Runbook 存取 Azure Key Vault 時，收到下列錯誤：

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>原因

發生此問題的可能原因如下：

* 未使用執行身分帳戶。
* 權限不足。

### <a name="resolution"></a>解決方案

#### <a name="not-using-a-run-as-account"></a>未使用執行身分帳戶

遵循[步驟 5 - 加入驗證來管理 Azure 資源](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources)，以確保您會使用執行身分帳戶來存取 Key Vault。

#### <a name="insufficient-permissions"></a>權限不足

[將權限新增至 Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) \(部分機器翻譯\)，以確保您的執行身分帳戶具有足夠的權限可存取 Key Vault。

## <a name="recommended-documents"></a>建議的文件

* [Azure 自動化中的 Runbook 執行](../automation-runbook-execution.md)
* [在 Azure 自動化中啟動 Runbook](../start-runbooks.md)

## <a name="next-steps"></a>後續步驟

如果您在這裡看不到您的問題，或者無法解決您的問題，請嘗試下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答。
* 聯繫 [@AzureSupport](https://twitter.com/azuresupport)，這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 Azure 支援會讓您與 Azure 社群聯繫，以獲得解答、支援及專家。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
