---
title: 針對 Azure 自動化 Runbook 的錯誤進行疑難排解
description: 瞭解如何對 Azure 自動化 Runbook 可能會遇到的問題進行故障排除和解決。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b5d326d02587d6b5bd8fd73dcccfefdb13c47d57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500931"
---
# <a name="troubleshoot-errors-with-runbooks"></a>針對 Runbook 的錯誤進行疑難排解

在 Azure 自動化中執行 Runbook 時出錯時，可以使用以下步驟來説明診斷問題。

1. **確保 Runbook 腳本在本地電腦上成功執行。** 

    有關語言參考和學習模組，請參閱[PowerShell 文檔](/powershell/scripting/overview)或[Python 文檔](https://docs.python.org/3/)。 在本地執行腳本可以發現和解決常見錯誤，例如：

      * 缺少模組
      * 語法錯誤
      * 邏輯錯誤

2. **調查運行簿[錯誤流](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)。**

    查看這些特定消息的流，並將其與本文中記錄的錯誤進行比較。

3. **確保您的節點和自動化工作區具有所需的模組。** 

    如果 Runbook 導入任何模組，請使用[導入模組](../shared-resources/modules.md#import-modules)中列出的步驟驗證它們是否可用於您的自動化帳戶。 按照[Azure 自動化 中更新 Azure 模組](..//automation-update-azure-modules.md)中的說明將模組更新到最新版本。 有關詳細資訊，請參閱[故障排除模組](shared-resources.md#modules)。

4. **如果 Runbook 掛起或意外失敗，請執行操作。**

    * [檢查作業狀態](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses)定義 Runbook 狀態和一些可能的原因。
    * 向 Runbook[添加其他輸出](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams)，以標識在暫存 Runbook 之前發生的情況。
    * [處理作業引發的任何異常](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)。

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>方案：運行登錄-AzureRM帳戶以登錄

### <a name="issue"></a>問題

執行 Runbook 時，您會收到以下錯誤：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

當您不使用"運行作為"帳戶或"運行為"帳戶已過期時，可能會發生此錯誤。 請參閱[管理 Azure 自動化作為帳戶運行](https://docs.microsoft.com/azure/automation/manage-runas-account)。

此錯誤有兩個主要原因：

* AzureRM 或 Az 模組有不同的版本。
* 您正在嘗試訪問單獨的訂閱中的資源。

### <a name="resolution"></a>解決方案

如果在更新一個 AzureRM 或 Az 模組後收到此錯誤，則應將所有模組更新為同一版本。

如果您嘗試訪問其他訂閱中的資源，可以按照以下步驟配置許可權。

1. 轉到"自動運行為帳戶"並複製應用程式 ID 和指紋。
  ![複製應用程式 ID 和指紋](../media/troubleshoot-runbooks/collect-app-id.png)
1. 轉到未託管自動化帳戶的訂閱存取控制，並添加新的角色指派。
  ![存取控制](../media/troubleshoot-runbooks/access-control.png)
1. 添加較早收集的應用程式 ID。 選擇"參與者"許可權。
   ![新增角色指派](../media/troubleshoot-runbooks/add-role-assignment.png)
1. 複製訂閱的名稱。
1. 現在，您可以使用以下 Runbook 代碼來測試從自動化帳戶到其他訂閱的許可權。 替換為`"\<CertificateThumbprint\>"`步驟 1 中複製的值。 替換為`"\<SubscriptionName\>"`步驟 4 中複製的值。

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>案例：找不到 Azure 訂用帳戶

### <a name="issue"></a>問題

使用`Select-AzureSubscription`或`Select-AzureRmSubscription`Cmdlet 時，您會收到以下錯誤：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>錯誤

可能發生這個錯誤的原因有：

* 訂閱名稱無效。
* 嘗試獲取訂閱詳細資訊的 Azure 活動目錄使用者未配置為訂閱的管理員。

### <a name="resolution"></a>解決方案

按照以下步驟確定您是否已對 Azure 進行身份驗證，以及是否有權訪問嘗試選擇的訂閱。

1. 為了確保腳本獨立工作，請將其測試到 Azure 自動化之外。
2. 在運行`Select-AzureSubscription`Cmdlet`Add-AzureAccount`之前，請確保腳本運行 Cmdlet。
3. 在 Runbook 的開頭加上 `Disable-AzureRmContextAutosave –Scope Process`。 此 Cmdlet 調用可確保任何憑據僅適用于當前 Runbook 的執行。
4. 如果仍然看到此錯誤訊息，請通過添加`AzureRmContext``Add-AzureAccount`Cmdlet 的參數來修改代碼，然後執行代碼。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>案例：對 Azure 的驗證失敗，因為已啟用多重要素驗證

### <a name="issue"></a>問題

使用 Azure 使用者名稱與密碼向 Azure 進行驗證時，收到下列錯誤：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果您的 Azure 帳戶使用多重要素驗證，您就無法使用 Azure Active Directory 使用者來向 Azure 進行驗證。 相反，您需要使用證書或服務主體進行身份驗證。

### <a name="resolution"></a>解決方案

要將證書與 Azure 經典部署模型 Cmdlet 一起使用，請參閱[創建並添加證書來管理 Azure 服務](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 要將服務主體與 Azure 資源管理器 Cmdlet 一起使用，請參閱[使用 Azure 門戶創建服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)，並使用[Azure 資源管理器對服務主體進行身份驗證](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>方案：在作業流中看到有關get_SerializationSettings方法的錯誤

### <a name="issue"></a>問題

在 Runbook 的作業流中可以看到以下錯誤：

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

### <a name="cause"></a>原因

此錯誤是由在 Runbook 中使用 AzureRM 和 Az 模組 Cmdlet 引起的。 在導入 AzureRM 模組之前導入 Az 模組時，將發生這種情況。

### <a name="resolution"></a>解決方案

不能在同一運行簿中導入和使用 Az 和 AzureRM Cmdlet。 要瞭解有關 Azure 自動化中的 Az Cmdlet 的更多資訊，請參閱[Azure 自動化 中的 Az 模組支援](../az-modules.md)。

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>案例：Runbook 失敗，錯誤為：工作已取消

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

此錯誤可能是因為使用過期的 Azure 模組所造成。

### <a name="resolution"></a>解決方案

您可以通過將 Azure 模組更新到最新版本來解決此錯誤。 

1. 在自動化帳戶中，按一下 **"模組**"，然後按一下 **"更新 Azure 模組**"。 
2. 更新大約需要 15 分鐘。 完成後，重新運行失敗的 Runbook。

要瞭解有關更新模組的詳細資訊，請參閱在[Azure 自動化 中更新 Azure 模組](../automation-update-azure-modules.md)。

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>方案：處理多個訂閱時 Runbook 失敗

### <a name="issue"></a>問題

執行 Runbook 時，Runbook 無法管理 Azure 資源。

### <a name="cause"></a>原因

Runbook 在執行時未使用正確的內容。

### <a name="resolution"></a>解決方案

當 Runbook 調用多個 Runbook 時，訂閱上下文可能會丟失。 為確保訂閱上下文傳遞到 Runbook，請讓用戶端 Runbook 將上下文傳遞給`Start-AzureRmAutomationRunbook``AzureRmContext`參數中的 Cmdlet。 將`Disable-AzureRmContextAutosave`Cmdlet 與`Scope`參數集一`Process`起使用，以確保指定的憑據僅用於當前 Runbook。 有關詳細資訊，請參閱[使用多個訂閱](../automation-runbook-execution.md#working-with-multiple-subscriptions)。

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>方案：術語未識別為 Cmdlet 的名稱、函數、腳本

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

發生此錯誤的原因如下：

* 包含 Cmdlet 的模組不會導入到自動化帳戶中。
* 包含 Cmdlet 的模組已導入，但已過期。

### <a name="resolution"></a>解決方案

執行以下任務之一來解決此錯誤。 

* 有關 Azure 模組，請參閱[如何在 Azure 自動化中更新 Azure PowerShell 模組](../automation-update-azure-modules.md)，瞭解如何在自動化帳戶中更新模組。

* 對於非 Azure 模組，請確保 中的模組導入到自動化帳戶中。

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>方案：運行簿作業啟動嘗試了三次，但每次都無法啟動

### <a name="issue"></a>問題

Runbook 失敗，出現以下錯誤：

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

出現此錯誤的原因是以下問題之一：

* 記憶體限制。 如果作業使用超過 400 MB 的記憶體，則可能會失敗。 分配給沙箱的記憶體的記錄限制位於[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)處。 

* 網路通訊端。 Azure 沙箱限制為 1000 個併發網路通訊端。 請參閱[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。

* 模組不相容。 模組依賴項可能不正確。 在這種情況下，runbook 通常會返回 或`Command not found``Cannot bind parameter`消息。

* 沒有沙箱活動目錄的身份驗證。 Runbook 嘗試調用在 Azure 沙箱中運行的可執行進程或子進程。 不支援使用 Azure 活動目錄身份驗證庫 （ADAL） 配置運行簿以使用 Azure AD 進行身份驗證。

* 異常資料過多。 Runbook 嘗試向輸出流寫入過多的異常資料。

### <a name="resolution"></a>解決方案

* 記憶體限制，網路通訊端。 建議在記憶體限制內工作的方法是拆分多個 Runbook 中的工作負載，處理記憶體中更少的資料，避免從 Runbook 編寫不必要的輸出，並考慮將多少個檢查點寫入 PowerShell 工作流運行簿。 使用清除方法（如`$myVar.clear`）清除變數，並用於`[GC]::Collect`立即運行垃圾回收。 這些動作會減少 Runbook 在執行階段的記憶體使用量。

* 模組不相容。 按照["如何更新 Azure 自動化 中的 Azure PowerShell"模組](../automation-update-azure-modules.md)的步驟更新 Azure 模組。

* 沒有用於沙箱的活動目錄的身份驗證。 使用 Runbook 對 Azure AD 進行身份驗證時，請確保 Azure AD 模組在自動化帳戶中可用。 請務必向"運行為"帳戶授予執行 Runbook 自動執行的任務所需的許可權。

  如果 Runbook 無法調用在 Azure 沙箱中運行的可執行進程或子進程，請使用[混合 Runbook 輔助角色上的 Runbook。](../automation-hrw-run-runbooks.md) 混合輔助角色不受 Azure 沙箱的記憶體和網路限制的限制。

* 異常資料過多。 作業輸出流有 1MB 的限制。 確保 runbook 將調用包含對可執行進程或子進程的調用，`try`使用`catch`和 塊。 如果操作引發異常，則讓代碼將異常的消息寫入自動化變數。 此技術可防止將消息寫入作業輸出流。

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>方案：登錄到 Azure 帳戶失敗

### <a name="issue"></a>問題

使用`Add-AzureAccount`或`Connect-AzureRmAccount`Cmdlet 時，您會收到以下錯誤之一：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果憑據資產名稱無效，則會發生這些錯誤。 如果您用於設置自動化憑據資產的使用者名和密碼無效，也可能發生此類操作。

### <a name="resolution"></a>解決方案

為了判斷錯誤原因，請執行下列步驟：

1. 請確保您沒有任何特殊字元。 這些字元包括用來連線到 Azure 之自動化認證資產名稱中的 `\@` 字元。
2. 檢查是否可以使用存儲在本地 PowerShell ISE 編輯器中的 Azure 自動化憑據中的使用者名和密碼。 在 PowerShell ISE 中運行以下 Cmdlet。

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. 如果身份驗證在本地失敗，則未正確設置 Azure 活動目錄憑據。 請參閱使用[Azure 活動目錄博客文章對 Azure 進行身份驗證](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/)，以便正確設置 Azure 活動目錄帳戶。

4. 如果錯誤看起來是暫時性的，請嘗試向身份驗證常式添加重試邏輯，以使身份驗證更加可靠。

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
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>方案：物件引用未設置為物件的實例

### <a name="issue"></a>問題

使用`Wait`參數調用子 Runbook 時，您會收到以下錯誤，並且輸出流包含一個物件：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

`Start-AzureRmAutomationRunbook`如果流包含物件，則無法正確處理輸出流。

### <a name="resolution"></a>解決方案

建議實現輪詢邏輯，並使用[獲取 AzureRm 自動化作業輸出](/powershell/module/azurerm.automation/get-azurermautomationjoboutput)Cmdlet 檢索輸出。 下面定義了此邏輯的示例。

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
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

使用以下任一解決方案來解決此問題。

* 如果要將複雜物件從一釐米長管道到另一個 Cmdlet，請將這些 Cmdlet 包裝在`InlineScript`活動中。
* 從複雜物件傳遞您需要的名稱或值，而非傳遞整個物件。
* 使用 PowerShell Runbook，而不是 PowerShell 工作流程 Runbook。

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>方案：Runbook 作業失敗，因為已分配的配額超過

### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

當作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。 這個配額會套用至所有類型的作業執行工作。 其中一些任務是測試作業、從門戶啟動作業、使用 Webhook 執行作業，或者安排作業以使用 Azure 門戶或資料中心執行。 要瞭解有關自動化定價的更多資訊，請參閱[自動化定價](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解決方案

如果要每月使用超過 500 分鐘的處理時間，請將訂閱從"免費"層更改為"基本"層。

1. 登入您的 Azure 訂用帳戶。
2. 選擇要升級的自動化帳戶。
3. 按一下 **"設置"，** 然後按一下**定價**。
4. 按一下"在頁面底部**啟用**"可將您的帳戶升級到基本層。

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>案例：執行 Runbook 時，無法辨識 Cmdlet

### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

當 PowerShell 引擎找不到您在 Runbook 中使用的 Cmdlet 時，就會發生此錯誤。 帳戶中可能缺少包含 Cmdlet 的模組，與 Runbook 名稱存在名稱衝突，或者 Cmdlet 也存在於另一個模組中，並且自動化無法解析該名稱。

### <a name="resolution"></a>解決方案

使用以下任一解決方案來解決此問題。

* 請確保輸入了 Cmdlet 名稱。
* 確保 Cmdlet 存在於您的自動化帳戶中，並且沒有衝突。 要驗證 Cmdlet 是否存在，請在編輯模式下打開 Runbook 並搜索要在庫中找到的 Cmdlet 或運行`Get-Command <CommandName>`。 驗證 Cmdlet 對帳戶可用且沒有與其他 Cmdlet 或 Runbook 的名稱衝突後，請將 Cmdlet 添加到畫布，並確保在 Runbook 中使用有效的參數集。
* 如果存在名稱衝突，並且 Cmdlet 在兩個不同的模組中可用，請使用 Cmdlet 的完全限定名稱來解決此問題。 例如，您可以使用 `ModuleName\CmdletName`。
* 如果要在混合輔助角色組中在本地執行 Runbook，請確保模組和 Cmdlet 安裝在承載混合輔助角色的電腦上。

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>方案：長時間運行的運行手冊無法完成

### <a name="issue"></a>問題

您的 Runbook 在執行 3 小時之後顯示為 [已停止] 狀態。 您可能還會收到此錯誤：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

此行為是在 Azure 沙箱中設計，因為 Azure 自動化中的進程[具有公平的共用](../automation-runbook-execution.md#fair-share)監視。 如果進程執行的時間超過三個小時，則公平共用將自動停止運行簿。 超過公平共用時間限制的 Runbook 的狀態因 Runbook 類型而異。 PowerShell 和 Python Runbook 是設定為 [已停止] 狀態。 「PowerShell 工作流程」Runbook 是設定為 [失敗]狀態。

### <a name="cause"></a>原因

Runbook 超過 Azure 沙箱中公平共用允許的 3 小時限制。

### <a name="resolution"></a>解決方案

其中一個建議的解決方案是在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上執行 Runbook。 混合工作角色不受 Azure 沙箱的 3 小時公平共用運行簿限制的限制。 應開發在混合 Runbook 活頁簿上運行的 Runbook，以支援在出現意外的本地基礎結構問題時的重新開機行為。

另一個解決方案是通過創建子 Runbook 來優化[Runbook。](../automation-child-runbooks.md) 如果 Runbook 迴圈多個資源上的同一函數，例如，在多個資料庫上的資料庫操作中，可以將該函數移動到子 Runbook。 每個子運行簿在單獨的進程中並存執行。 此行為可減少完成父代 Runbook 的時間總計。

啟用子 Runbook 案例的 PowerShell Cmdlet 是：

* [啟動-AzureRM自動化運行簿](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook)。 此 Cmdlet 可讓您啟動 Runbook，並將參數傳遞給 Runbook。

* [獲取 AzureRm 自動化作業](/powershell/module/azurerm.automation/get-azurermautomationjob)。 如果在子 Runbook 完成後需要執行一些操作，則此 Cmdlet 允許您檢查每個子級的作業狀態。

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>方案：狀態：調用 Webhook 時有 400 個錯誤請求

### <a name="issue"></a>問題

當您嘗試為 Azure 自動化運行簿調用 Webhook 時，您會收到以下錯誤：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

您嘗試呼叫的 Webhook 已停用或過期。

### <a name="resolution"></a>解決方案

如果 Webhook 已停用，您可以透過 Azure 入口網站重新啟用 Webhook。 如果 Webhook 已過期，則必須刪除並重新創建它。 如果尚未過期，您只能[更新 Webhook](../automation-webhooks.md#renew-webhook)。

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>方案： 429： 請求速率當前太大...

### <a name="issue"></a>問題

您在執行 `Get-AzureRmAutomationJobOutput` Cmdlet 時出現下列錯誤訊息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

從具有許多[詳細流](../automation-runbook-output-and-messages.md#verbose-stream)的 Runbook 檢索作業輸出時，可能會發生此錯誤。

### <a name="resolution"></a>解決方案

執行以下操作之一來解決此錯誤。

* 編輯 Runbook，並減少它所發出的作業資料流數目。

* 減少在執行 Cmdlet 時所要擷取的資料流數目。 為此，可以設置`Stream``Get-AzureRmAutomationJobOutput`Cmdlet 的參數值，以便僅檢索輸出流。 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>方案：PowerShell 作業失敗，出錯：無法調用方法

### <a name="issue"></a>問題

在 Azure 中運行的 Runbook 中啟動 PowerShell 作業時，您將收到以下錯誤訊息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

此錯誤可能表示在 Azure 沙箱中執行的 Runbook 無法在["完整語言"模式下](/powershell/module/microsoft.powershell.core/about/about_language_modes)運行。

### <a name="resolution"></a>解決方案

有兩種方法可以解決此問題。

* 而不是使用`Start-Job`，使用`Start-AzureRmAutomationRunbook`來啟動 Runbook。
* 嘗試在混合 Runbook 工作執行緒上運行 Runbook。

要瞭解有關 Azure 自動化 Runbook 的此行為和其他行為的更多資訊，請參閱[Runbook 行為](../automation-runbook-execution.md#runbook-behavior)。

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>方案：Linux 混合 Runbook 工作人員在簽署 Runbook 時收到密碼提示

### <a name="issue"></a>問題

運行`sudo`Linux 混合 Runbook 輔助角色的命令會檢索密碼的意外提示。

### <a name="cause"></a>原因

Linux 日誌分析代理的**nx 自動化使用者帳戶**在**sudoers**檔中未正確配置。 混合 Runbook 輔助角色需要適當的帳戶許可權和其他資料配置，以便它可以在 Linux Runbook 輔助角色上簽名 Runbook。

### <a name="resolution"></a>解決方案

* 確保混合 Runbook 工作執行緒在電腦上具有 GnuPG （GPG） 可執行檔。

* 驗證**sudoers**檔中**的 nx 自動化使用者帳戶**的配置。 請參閱[在混合 Runbook 工作執行緒上運行 Runbook](../automation-hrw-run-runbooks.md)

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>方案：在 Azure 自動化上的 PnP PowerShell 運行簿中出現 Cmdlet 失敗

### <a name="issue"></a>問題

當 Runbook 將 PnP PowerShell 生成的物件直接寫入 Azure 自動化輸出時，Cmdlet 輸出無法資料流回自動化。

### <a name="cause"></a>原因

此問題最常發生在 Azure 自動化處理調用 PnP PowerShell Cmdlet 的運行簿時，`add-pnplistitem`例如，而不捕獲返回物件。

### <a name="resolution"></a>解決方案

編輯腳本以將任何傳回值分配給變數，以便 Cmdlet 不會嘗試將整個物件寫入標準輸出。 腳本可以將輸出流重定向到 Cmdlet，如下所示。

```azurecli
  $null = add-pnplistitem
```

如果腳本分析 Cmdlet 輸出，腳本必須將輸出存儲在變數中並操作變數，而不是簡單地資料流輸出。

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>並未列出我的問題

以下各節列出了其他常見錯誤，並提供支援文檔以説明您解決問題。

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>混合式 Runbook 背景工作角色沒有執行作業或沒有回應

如果在混合 Runbook 工作執行緒上而不是 Azure 自動化中運行作業，則可能需要[對混合輔助角色本身進行故障排除](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook 失敗，沒有許可權或某些變體

"運行為帳戶"對 Azure 資源的許可權可能與當前帳戶沒有相同的許可權。 確保您的"運行為"帳戶具有訪問腳本中使用的[任何資源的許可權](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="issues-passing-parameters-into-webhooks"></a>將參數傳遞到網鉤的問題

有關將參數傳遞到 Webhook 的説明，請參閱[從 Webhook 啟動 Runbook。](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook)

### <a name="issues-using-az-modules"></a>使用 Az 模組的問題

不支援在同一自動化帳戶中使用 Az 模組和 AzureRM 模組。 有關詳細資訊[，請參閱 Runbook 中的 Az 模組](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="inconsistent-behavior-in-runbooks"></a>Runbook 中不一致的行為

按照[Runbook 執行](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior)中的指南操作，以避免在 Runbook 中出現併發作業、多次創建資源或其他對計時敏感的邏輯的問題。

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook 失敗，錯誤 無許可權、禁止 （403） 或某些變體

"運行為帳戶"對 Azure 資源的許可權可能與當前帳戶沒有相同的許可權。 確保您的"運行為"帳戶具有訪問腳本中使用的[任何資源的許可權](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 已運作，但突然停止

* 確保"運行為"帳戶尚未過期。 請參閱[認證續訂](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)。
* 如果您使用[Webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)啟動 Runbook，請確保 Webhook 尚未過期。

### <a name="passing-parameters-into-webhooks"></a>將參數傳遞至 Webhook

有關將參數傳遞到 Webhook 的説明，請參閱[從 Webhook 啟動 Runbook。](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook)

### <a name="using-az-modules"></a>使用 Az 模組

不支援在同一自動化帳戶中使用 Az 模組和 AzureRM 模組。 請參閱[Runbook 中的 Az 模組](https://docs.microsoft.com/azure/automation/az-modules)。

### <a name="using-self-signed-certificates"></a>使用自簽章憑證

要使用自簽章憑證，請參閱[創建新證書](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)。

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>使用 Runbook 的 Azure 沙箱時拒絕訪問

Azure 沙箱可阻止訪問所有進程外 COM 伺服器。 例如，沙箱應用程式或 Runbook 無法調用 Windows 管理檢測 （WMI） 或 Windows 安裝程式服務 （msiserver.exe）。 有關使用沙箱的詳細資訊，請參閱 Azure 自動化[中的 Runbook 執行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)。

## <a name="recommended-documents"></a>建議的文件

* [在 Azure 自動化中啟動 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure 自動化中的 Runbook 執行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 論壇](https://azure.microsoft.com/support/forums/)從 Azure 專家那裡獲得答案。
* 與[@AzureSupport](https://twitter.com/azuresupport)— 正式的 Microsoft Azure 帳戶連接，通過將 Azure 社區連接到正確的資源（答案、支援和專家），從而改善客戶體驗。
* 如果需要更多協助，您可以提出 Azure 支援事件。 轉到[Azure 支援網站](https://azure.microsoft.com/support/options/)並選擇 **"獲取支援**"。
