---
title: 針對 Azure 自動化 Runbook 的錯誤進行疑難排解
description: 瞭解如何使用 Azure 自動化 runbook 來疑難排解和解決可能會遇到的問題。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 71344f954990952856f031829f13273e062b62c5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933159"
---
# <a name="troubleshoot-errors-with-runbooks"></a>針對 Runbook 的錯誤進行疑難排解

當您在 Azure 自動化中執行 runbook 時發生錯誤時，可以使用下列步驟來協助診斷問題。

1. **請確定您的 runbook 腳本在您的本機電腦上執行成功：** 請參閱[PowerShell](/powershell/scripting/overview)檔或[Python](https://docs.python.org/3/)檔，以取得語言參考和學習模組。

   在本機執行您的腳本可以探索並解決常見錯誤，例如：

   - **遺失的模組**
   - **語法錯誤**
   - **邏輯錯誤**

2. **調查**特定訊息的 runbook[錯誤資料流程](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output)，並將其與下面的錯誤進行比較。

3. **請確定您的節點和自動化工作區具有必要的模組：** 如果您的 runbook 匯入任何模組，請使用匯[入模組](../shared-resources/modules.md#import-modules)中列出的步驟，確定您的自動化帳戶可使用它們。 遵循[Azure 自動化中的更新 Azure 模組中](..//automation-update-azure-modules.md)的指示，將您的模組更新為最新版本。 如需詳細的疑難排解資訊，請參閱針對[模組進行疑難排解](shared-resources.md#modules)。

如果您的 Runbook 已暫止或非預期地失敗：

* [檢查作業狀態](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses)會定義 runbook 狀態和一些可能的原因。
* [將額外的輸出新增](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams)至 runbook，以識別 runbook 暫止之前會發生什麼事。
* 處理您的作業所擲回的[任何例外](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions)狀況。

## <a name="login-azurerm"></a>案例：執行登入-AzureRMAccount 以登入

### <a name="issue"></a>問題

執行 runbook 時，您會收到下列錯誤：

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>原因

當您未使用執行身分帳戶或執行身分帳戶已過期時，就會發生此錯誤。 請參閱[管理 Azure 自動化執行身分帳戶](https://docs.microsoft.com/azure/automation/manage-runas-account)。

此錯誤有兩個主要原因：

* 不同版本的 AzureRM 模組。
* 您正嘗試存取不同訂用帳戶中的資源。

### <a name="resolution"></a>解析度

如果您在更新一個 AzureRM 模組之後收到此錯誤，您應該將所有的 AzureRM 模組更新為相同的版本。

如果您嘗試存取另一個訂用帳戶中的資源，您可以遵循下列步驟來設定許可權。

1. 移至自動化執行身分帳戶，並複製 [應用程式識別碼] 和 [指紋]。
  ![複製應用程式識別碼和憑證指紋](../media/troubleshoot-runbooks/collect-app-id.png)
1. 移至未裝載自動化帳戶的訂用帳戶存取控制，然後新增新的角色指派。
  ![存取控制](../media/troubleshoot-runbooks/access-control.png)
1. 新增您在上一個步驟中收集的應用程式識別碼。 選取 [參與者許可權]。
   ![新增角色指派](../media/troubleshoot-runbooks/add-role-assignment.png)
1. 複製訂用帳戶的名稱以進行下一個步驟。
1. 您現在可以使用下列 runbook 程式碼，測試您的自動化帳戶對另一個訂用帳戶的許可權。

    以您在步驟 #1 中複製的值，以及您在步驟\>中複製的 "\<SubscriptionName #4" 值，取代 "\<CertificateThumbprint\>"。

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

## <a name="unable-to-find-subscription"></a>案例：找不到 Azure 訂用帳戶

### <a name="issue"></a>問題

您在使用 `Select-AzureSubscription` 或 `Select-AzureRmSubscription` Cmdlet 時，收到下列錯誤：

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>錯誤

如果發生下列情況，可能就會發生此錯誤：

* 訂用帳戶名稱無效

* 未將嘗試取得訂用帳戶詳細資料的 Azure Active Directory 使用者設定為訂用帳戶的管理員。

### <a name="resolution"></a>解析度

採取下列步驟，以判斷您是否已向 Azure 進行驗證並取得您嘗試選取之訂用帳戶的存取權：

1. 為了確定它能獨立運作，請在 Azure 自動化外部測試您的指令碼。
2. 請務必先執行 `Add-AzureAccount` Cmdlet，再執行 `Select-AzureSubscription` Cmdlet。
3. 在 Runbook 的開頭加上 `Disable-AzureRmContextAutosave –Scope Process`。 這個 Cmdlet 確保所有認證只會套用到目前 Runbook 的執行。
4. 如果您仍然看到此錯誤訊息，請在 `Add-AzureAccount` Cmdlet 之後新增 **AzureRmContext** 參數，以修改您的程式碼，然後執行此程式碼。

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="auth-failed-mfa"></a>案例：對 Azure 的驗證失敗，因為已啟用多重要素驗證

### <a name="issue"></a>問題

使用 Azure 使用者名稱與密碼向 Azure 進行驗證時，收到下列錯誤：

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>原因

如果您的 Azure 帳戶使用多重要素驗證，您就無法使用 Azure Active Directory 使用者來向 Azure 進行驗證。 相反地，您必須使用憑證或服務主體來向 Azure 驗證。

### <a name="resolution"></a>解析度

若要搭配 Azure 傳統部署模型 Cmdlet 使用憑證，請參閱[建立及新增憑證來管理 azure 服務](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx)。 若要使用具有 Azure Resource Manager Cmdlet 的服務主體，請參閱[使用 Azure 入口網站建立服務主體](../../active-directory/develop/howto-create-service-principal-portal.md)和使用[Azure Resource Manager 驗證服務主體](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)。

## <a name="get-serializationsettings"></a>案例：您在作業串流中看到關於 get_SerializationSettings 方法的錯誤

### <a name="issue"></a>問題

您在 Runbook 的作業串流中發現錯誤，出現的訊息如下：

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

此錯誤是由於在 Runbook 中使用 AzureRM 和 Az Cmdlet 所致。 您先匯入 `Az` 再匯入 `AzureRM` 時發生了此錯誤。

### <a name="resolution"></a>解析度

Az 和 AzureRM Cmdlet 無法在相同的 runbook 中匯入和使用。 若要深入瞭解 Azure 自動化中的 Az Cmdlet，請參閱[Azure 自動化中的 az 模組支援](../az-modules.md)。

## <a name="task-was-cancelled"></a>案例：Runbook 失敗，錯誤為：工作已取消

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
Exception: A task was canceled.
```

### <a name="cause"></a>原因

此錯誤可能是因為使用過期的 Azure 模組所造成。

### <a name="resolution"></a>解析度

此錯誤可以透過將 Azure 模組更新至最新版本來解決。

在您的自動化帳戶中，按一下 [模組]，然後按一下 [更新 Azure 模組]。 更新大約需要 15 分鐘，完成之後，請重新執行先前失敗的 Runbook。 若要深入了解如何更新模組，請參閱[更新 Azure 自動化中的 Azure 模組](../automation-update-azure-modules.md)。

## <a name="runbook-auth-failure"></a>案例：處理多個訂用帳戶時 Runbook 失敗

### <a name="issue"></a>問題

執行 runbook 時，runbook 無法管理 Azure 資源。

### <a name="cause"></a>原因

Runbook 在執行時未使用正確的內容。

### <a name="resolution"></a>解析度

在使用多個訂用帳戶時，若叫用 Runbook，可能會遺失訂用帳戶內容。 若要確保訂用帳戶的內容會傳遞至 Runbook，請將 `AzureRmContext` 參數新增至 Cmdlet，並將內容傳遞給它。 也建議您使用 `Disable-AzureRmContextAutosave` Cmdlet 搭配**程序**範圍，以確保您使用的認證只會用於目前的 Runbook。

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

如需詳細資訊，請參閱使用[多個訂用](../automation-runbook-execution.md#working-with-multiple-subscriptions)帳戶。

## <a name="not-recognized-as-cmdlet"></a>案例：詞彙無法辨識為 Cmdlet、函式、腳本的名稱

### <a name="issue"></a>問題

您的 Runbook 失敗，並具有類似下列範例的錯誤：

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>原因

此錯誤可能是因為下列原因而發生：

* 包含 Cmdlet 的模組不會匯入至自動化帳戶。
* 包含 Cmdlet 的模組已匯入，但已過期。

### <a name="resolution"></a>解析度

藉由完成下列其中一項工作，就可以解決此錯誤：

如果模組是 Azure 模組，請參閱[如何更新 Azure 自動化中的 Azure PowerShell 模組](../automation-update-azure-modules.md)，以瞭解如何在您的自動化帳戶中更新您的模組。

如果是個別模組，則請確定已將該模組匯入到您的「自動化帳戶」中。

## <a name="job-attempted-3-times"></a>案例：已嘗試啟動 runbook 作業三次，但每次都無法啟動

### <a name="issue"></a>問題

您的 runbook 因下列錯誤而失敗。

```error
The job was tried three times but it failed
```

### <a name="cause"></a>原因

發生此錯誤的原因是下列其中一個問題。

* 記憶體限制。 如果工作使用超過 400 MB 的記憶體，作業可能會失敗。 配置給沙箱的記憶體已記載限制會在[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)中找到。 

* 網路通訊端。 Azure 沙箱限制為1000個並行網路通訊端。 請參閱[自動化服務限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)。

* 模組不相容。 模組相依性可能不正確。 在此情況下，您的 runbook 通常會傳回「找不到命令」或「無法系結參數」訊息。

* 不使用沙箱的 Active Directory 進行驗證。 您的 runbook 嘗試呼叫在 Azure 沙箱中執行的可執行檔或子進程。 不支援將 runbook 設定為使用 Azure Active Directory Authentication Library （ADAL）向 Azure AD 進行驗證。

* 太多例外狀況資料。 您的 runbook 嘗試將太多例外狀況資料寫入輸出資料流程。

### <a name="resolution"></a>解析度

* 記憶體限制，網路通訊端。 在記憶體限制內工作的建議方式是將工作負載分割到多個 runbook，在記憶體中處理較少的資料，避免從 runbook 寫入不必要的輸出，並考慮將多少檢查點寫入您的 PowerShell 工作流程手冊. 使用 clear 方法（例如 `$myVar.clear`）來清除變數，並使用 `[GC]::Collect` 立即執行垃圾收集。 這些動作會減少 Runbook 在執行階段的記憶體使用量。

* 模組不相容。 遵循[如何在 Azure 自動化中更新 Azure PowerShell 模組](../automation-update-azure-modules.md)中的步驟，更新您的 Azure 模組。

* 不使用適用于沙箱的 ADAL 進行驗證。 向 runbook 驗證 Azure AD 時，請確定您的自動化帳戶中有可用的 Azure AD 模組。 請務必授與執行身分帳戶必要的許可權，以執行 runbook 所自動化的工作。

  如果您的 runbook 無法呼叫在 Azure 沙箱中執行的可執行檔或子進程，請在[混合式 runbook 背景工作角色](../automation-hrw-run-runbooks.md)上使用 runbook。 混合式背景工作角色不受限於 Azure 沙箱所擁有的記憶體和網路限制。

* 太多例外狀況資料。 作業輸出資料流程有1MB 的限制。 確定您的 runbook 將呼叫括住在 try/catch 區塊中的可執行檔或子進程。 如果作業擲回例外狀況，請讓程式碼將來自例外狀況的訊息寫入自動化變數中。 這項技術可避免將訊息寫入作業輸出資料流程。

## <a name="sign-in-failed"></a>案例：登入 Azure 帳戶失敗

### <a name="issue"></a>問題

使用 `Add-AzureAccount` 或 `Connect-AzureRmAccount` Cmdlet 時，您會收到下列其中一個錯誤：

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>原因

如果認證資產名稱無效，就會發生此錯誤。 如果您用來設定自動化認證資產的使用者名稱和密碼無效，可能也會發生此錯誤。

### <a name="resolution"></a>解析度

為了判斷錯誤原因，請執行下列步驟：

1. 確定您不具任何特殊字元。 這些字元包括用來連線到 Azure 之自動化認證資產名稱中的 **\@** 字元。
2. 請確認您可以在本機 PowerShell ISE 編輯器中使用儲存在 Azure 自動化認證的使用者名稱和密碼。 您可以在 PowerShell ISE 中執行下列 Cmdlet，以檢查使用者名稱和密碼是否正確：

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. 如果您的驗證在本機失敗，即表示您尚未正確設定 Azure Active Directory 認證。 請參閱 [Authenticating to Azure using Azure Active Directory (使用 Azure Active Directory 對 Azure 進行驗證)](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) 部落格文章，以正確設定 Azure Active Directory 帳戶。

4. 如果看起來像是暫時性錯誤，請嘗試在您的驗證常式中新增重試邏輯，讓驗證變得更健全。

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

## <a name="child-runbook-object"></a>案例：物件參考未設定為物件的實例

### <a name="issue"></a>問題

使用 `-Wait` 參數叫用子 runbook 時，您會收到下列錯誤，而且輸出資料流程包含物件：

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>原因

已知的問題是，如果 Set-azurermautomationrunbook 包含物件，則不會正確處理輸出資料流程。

### <a name="resolution"></a>解析度

若要解決此問題，建議您執行輪詢邏輯，並使用[以及搭配 get-azurermautomationjoboutput 指令程式](/powershell/module/azurerm.automation/get-azurermautomationjoboutput)來取出輸出。 下列範例中定義了此邏輯的範例。

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

## <a name="fails-deserialized-object"></a>案例：Runbook 因還原序列化物件而失敗

### <a name="issue"></a>問題

您的 Runbook 失敗，錯誤為：

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>原因

如果您的 Runbook 是「PowerShell 工作流程」，它會以還原序列化格式儲存複雜物件，以在暫止工作流程時保存 Runbook 狀態。

### <a name="resolution"></a>解析度

下列三個解決方案的任何一個可以修正此問題：

* 如果您要透過管線將複雜物件從一個 Cmdlet 傳送到另一個 Cmdlet，請將這些 Cmdlet 包裝在 InlineScript 中。
* 從複雜物件傳遞您需要的名稱或值，而非傳遞整個物件。
* 使用 PowerShell Runbook，而不是 PowerShell 工作流程 Runbook。

## <a name="quota-exceeded"></a>案例： Runbook 作業失敗，因為超過已配置的配額

### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>原因

當作業執行超過您的帳戶的 500 分鐘免費配額時，會發生此錯誤。 這個配額會套用至所有類型的作業執行工作。 這其中部分工作可能會測試作業、從入口網站中啟動作業、使用 Webhook 執行作業，或者使用 Azure 入口網站或您的資料中心排程作業來執行。 若要深入了解自動化的價格，請參閱[自動化價格](https://azure.microsoft.com/pricing/details/automation/)。

### <a name="resolution"></a>解析度

如果您每個月想要使用超過 500 分鐘的處理時間，您必須將您的訂用帳戶從免費層變更為基本層。 您可以採取下列步驟，升級至基本層：

1. 登入您的 Azure 訂用帳戶。
2. 選取要升級的自動化帳戶。
3. 依序按一下 [**設定**] 和 [**定價**]。
4. 按一下頁面底端的 [啟用]，將您的帳戶升級至**基本**層。

## <a name="cmdlet-not-recognized"></a>案例：執行 Runbook 時，無法辨識 Cmdlet

### <a name="issue"></a>問題

您的 Runbook 作業失敗，錯誤為：

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>原因

當 PowerShell 引擎找不到您在 Runbook 中使用的 Cmdlet 時，就會發生此錯誤。 這個錯誤可能是因為帳戶中遺漏了包含該 Cmdlet 的模組、與 Runbook 名稱發生名稱衝突，或該 Cmdlet 也存在於另一個模組中而自動化無法解析名稱。

### <a name="resolution"></a>解析度

下列任何一個解決方案都可以修正此問題：

* 確認您已正確輸入 Cmdlet 名稱。
* 請確定 Cmdlet 存在於您的自動化帳戶中，而且沒有任何衝突。 如要確認 Cmdlet 是否存在，請以編輯模式開啟 Runbook，然後搜尋您想要在程式庫中尋找的 Cmdlet，或執行 `Get-Command <CommandName>`。 在驗證 Cmdlet 可供帳戶使用，且與其他 Cmdlet 或 Runbook 沒有名稱衝突之後，請將其新增至畫布，並確定您在 Runbook 中使用有效的參數集。
* 如果有名稱衝突且 Cmdlet 可在兩個不同的模組中使用，您可以使用 Cmdlet 的完整名稱來解決此問題。 例如，您可以使用 **ModuleName\CmdletName**。
* 如果您是以混合式背景工作角色群組身分在內部部署環境中執行 Runbook，則請確定模組和 Cmdlet 是安裝在裝載混合式背景工作角色的機器上。

## <a name="long-running-runbook"></a>案例：長時間執行的 runbook 無法完成

### <a name="issue"></a>問題

您的 Runbook 在執行 3 小時之後顯示為 [已停止] 狀態。 您可能還收到此錯誤：

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

這是 Azure 沙箱中故意設計的行為，原因是 Azure 自動化中會進行處理序「公平共用」監視。 如果執行時間超過三小時，公平共用就會自動停止 Runbook。 超過公平共用時間限制時的 Runbook 狀態會依 Runbook 類型而有所不同。 PowerShell 和 Python Runbook 是設定為 [已停止] 狀態。 「PowerShell 工作流程」Runbook 是設定為 [失敗]狀態。

### <a name="cause"></a>原因

Runbook 會在 Azure 沙箱中的公平共用所允許的3小時限制內執行。

### <a name="resolution"></a>解析度

其中一個建議的解決方案是在[混合式 Runbook 背景工作角色](../automation-hrw-run-runbooks.md)上執行 Runbook。

混合式背景工作角色不受限於 Azure 沙箱所擁有的[公平共用](../automation-runbook-execution.md#fair-share)3 小時 runbook 限制。 應開發在混合式 Runbook 背景工作角色上執行的 runbook，以便在發生未預期的本機基礎結構問題時支援重新開機行為。

另一個選項是藉由建立[子 Runbook](../automation-child-runbooks.md) 將 Runbook 最佳化。 如果您的 Runbook 會在多個資源上重複執行同一個函式，例如在數個資料庫上重複進行某一個資料庫作業，您可以將該函式移到子 Runbook。 每一個 Runbook 會在個別的處理程序中平行執行。 此行為可減少完成父代 Runbook 的時間總計。

啟用子 Runbook 案例的 PowerShell Cmdlet 是：

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) - 此 Cmdlet 可讓您啟動 Runbook，並將參數傳遞給 Runbook

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) - 如果有需要在子 Runbook 完成後執行的作業，此 Cmdlet 可讓您檢查每個子項的作業狀態。

## <a name="expired webhook"></a>案例：狀態：400呼叫 webhook 時的要求不正確

### <a name="issue"></a>問題

在您嘗試叫用 Azure 自動化 Runbook 的 Webhook 時，收到下列錯誤：

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>原因

您嘗試呼叫的 Webhook 已停用或過期。

### <a name="resolution"></a>解析度

如果 Webhook 已停用，您可以透過 Azure 入口網站重新啟用 Webhook。 如果 Webhook 已過期，就必須先刪除 Webhook 再加以重新建立。 如果尚未過期，您只能[更新 Webhook](../automation-webhooks.md#renew-webhook)。

## <a name="429"></a>案例：429：要求速率目前太大 。

### <a name="issue"></a>問題

您在執行 `Get-AzureRmAutomationJobOutput` Cmdlet 時出現下列錯誤訊息：

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>原因

從有許多[詳細資訊資料流](../automation-runbook-output-and-messages.md#verbose-stream)的 Runbook 中擷取作業輸出時，就可能會發生此錯誤。

### <a name="resolution"></a>解析度

有兩種方法可以解決此錯誤：

* 編輯 Runbook，並減少它所發出的作業資料流數目。
* 減少在執行 Cmdlet 時所要擷取的資料流數目。 若要這麼做，您可以指定讓 `Get-AzureRmAutomationJobOutput` Cmdlet 的 `-Stream Output` 參數僅擷取輸出資料流。 

## <a name="cannot-invoke-method"></a>案例： PowerShell 工作失敗，發生錯誤：無法叫用方法

### <a name="issue"></a>問題

在 Azure 中執行的 runbook 中啟動 PowerShell 工作時，您會收到下列錯誤訊息：

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>原因

當您在 Azure 中執行的 runbook 中啟動 PowerShell 工作時，可能會發生此錯誤。 可能會發生此行為，因為在 Azure 沙箱中執行的 runbook 可能無法在[完整語言模式](/powershell/module/microsoft.powershell.core/about/about_language_modes)中執行。

### <a name="resolution"></a>解析度

有兩種方法可以解決此錯誤：

* 使用 `Start-AzureRmAutomationRunbook` 來啟動 runbook，而不是使用 `Start-Job`
* 如果您的 runbook 有此錯誤訊息，請在混合式 Runbook 背景工作角色上執行

若要深入瞭解此行為和 Azure 自動化 Runbook 的其他行為，請參閱[Runbook 行為](../automation-runbook-execution.md#runbook-behavior)。

## <a name="other"></a>我的問題未列于上方

下列各節列出其他常見錯誤，以及支援的檔，以協助您解決問題。

## <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>混合式 Runbook 背景工作角色沒有執行作業或沒有回應

如果您使用混合式背景工作角色來執行作業，而不是 Azure 自動化，您可能需要針對混合式背景[工作角色本身進行疑難排解](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker)。

## <a name="runbook-fails-with-no-permission-or-some-variation"></a>Runbook 因為「沒有權限」或一些變異而失敗

執行身分帳戶可能沒有與您目前帳戶相同的 Azure 資源許可權。 請確定您的執行身分帳戶具有[許可權，可存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)腳本中使用的任何資源。

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 已運作，但突然停止

* 如果 runbook 先前已執行但已停止，請確定[執行身分帳戶](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)尚未過期。
* 如果您使用 webhook 來啟動 runbook，請確定[webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)尚未過期。

## <a name="issues-passing-parameters-into-webhooks"></a>將參數傳遞至 webhook 的問題

如需將參數傳遞至 webhook 的說明，請參閱[從 Webhook 啟動 runbook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)。

## <a name="issues-using-az-modules"></a>使用 Az 模組的問題

不支援在相同的自動化帳戶中，使用 Az 模組和 AzureRM 模組。 如需詳細資訊，請參閱[runbook 中的 Az 模組](https://docs.microsoft.com/azure/automation/az-modules)，以取得更多詳細資料。

## <a name="inconsistent-behavior-in-runbooks"></a>Runbook 中不一致的行為

請遵循[Runbook 執行](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior)中的指導方針，以避免並行作業、資源建立次數，或 runbook 中其他時間緊迫邏輯的問題。

## <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Runbook 失敗，錯誤為沒有許可權、禁止（403）或某些變化

執行身分帳戶可能沒有與您目前帳戶相同的 Azure 資源許可權。 請確定您的執行身分帳戶具有[許可權，可存取](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)腳本中使用的任何資源。

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Runbook 已運作，但突然停止

* 如果 runbook 先前已執行但已停止，請確定執行身分帳戶尚未過期。 請參閱[認證更新](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal)。
* 如果您使用 webhook 來啟動 runbook，請確定 webhook 尚未[過期](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook)。

## <a name="passing-parameters-into-webhooks"></a>將參數傳遞至 Webhook

如需將參數傳遞至 webhook 的說明，請參閱[從 Webhook 啟動 runbook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters)。

## <a name="using-az-modules"></a>使用 Az 模組

不支援在相同的自動化帳戶中，使用 Az 模組和 AzureRM 模組。 請參閱[runbook 中的 Az 模組](https://docs.microsoft.com/azure/automation/az-modules)。

## <a name="using-self-signed-certificates"></a>使用自我簽署憑證

若要使用自我簽署憑證，請參閱[建立新的憑證](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate)。

## <a name="recommended-documents"></a>建議的文件

* [在 Azure 自動化中啟動 Runbook](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Azure 自動化中的 Runbook 執行](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 論壇](https://azure.microsoft.com/support/forums/)獲得由 Azure 專家所提供的解答
* 與 [@AzureSupport](https://twitter.com/azuresupport) 連繫－專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，像是解答、支援及專家等。
* 如果需要更多協助，您可以提出 Azure 支援事件。 請移至 [Azure 支援網站](https://azure.microsoft.com/support/options/)，然後選取 [取得支援]。
