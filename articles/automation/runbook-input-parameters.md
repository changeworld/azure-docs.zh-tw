---
title: 在 Azure 自動化中設定 Runbook 輸入參數
description: 此文章說明如何設定 Runbook 輸入參數，以允許在 Runbook 啟動時對其傳遞資料。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: c996c51583d81905e7853323166407e38ae79225
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830033"
---
# <a name="configure-runbook-input-parameters"></a>設定 Runbook 輸入參數

Runbook 輸入參數可讓您在 Runbook 啟動時對其傳遞資料，以增加 Runbook 的彈性。 這些參數可讓 Runbook 動作以特定情節和環境作為目標。 此文章描述輸入參數在 Runbook 中的設定和使用方式。

您可以設定 PowerShell、PowerShell 工作流程、圖形化和 Python Runbook 的輸入參數。 一個 Runbook 可以使用具有不同資料類型的多個參數，或完全不使用參數。 輸入參數可以是強制性或選擇性的，而且您可以針對選擇性參數使用預設值。

您在啟動 Runbook 時，指派其輸入參數值。 您可以從 Azure 入口網站、Web 服務或 PowerShell 啟動 Runbook。 您也可以啟動一個 Runbook 做為在另一個 Runbook 中以內嵌方式呼叫的子 Runbook。

### <a name="configure-input-parameters-in-powershell-runbooks"></a>在 PowerShell Runbook 中設定輸入參數

Azure 自動化中的 PowerShell 和 PowerShell 工作流程 Runbook 支援透過下列屬性定義的輸入參數。 

| **屬性** | **說明** |
|:--- |:--- |
| 類型 |必要。 對參數值預期的資料類型。 任何 .NET 類型皆有效。 |
| 名稱 |必要。 參數名稱。 此名稱這在 Runbook 中必須是唯一的，而且只能包含字母、數字或底線字元。 |
| 強制性 |選擇性。 指定參數是否為必要值的布林值。 如果您將此項目設定為 True，則在 Runbook 啟動時必須提供其值。 如果您將此項目設定為 false，則該值是選擇性的。 如果您未指定 `Mandatory` 屬性的值，則 PowerShell 會根據預設將輸入參數視為選擇性。 |
| 預設值 |選擇性。 如果在 Runbook 啟動時未傳遞輸入值所將用於參數的值。 Runbook 可以設定任何參數的預設值。 |

Windows PowerShell 所支援的輸入參數屬性比上面所列那些多，例如，驗證、別名和參數集。 不過，Azure 自動化目前僅支援列出的輸入參數屬性。

例如，讓我們看一下 PowerShell 工作流程 Runbook 中的參數定義。 此定義具有下列一般形式，其中多個參數會以逗號分隔。

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

現在，讓我們為輸出虛擬機器 (可以是單一 VM 或資源群組內的所有 VM) 相關詳細資料的 PowerShell 工作流程 Runbook 設定輸入參數。 如以下螢幕擷取畫面所示，此 Runbook 有兩個參數：虛擬機器的名稱 (`VMName`) 和資源群組的名稱 (`resourceGroupName`)。

![自動化 PowerShell 工作流程](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

在此參數定義中，輸入參數是字串類型的簡單參數。

請注意，PowerShell 和 PowerShell 工作流程 Runbook 支援所有簡單類型和複雜類型，例如，輸入參數的 `Object` 或 `PSCredential`。 如果您的 Runbook 有物件輸入參數，則必須使用具有名稱-值組的 PowerShell 雜湊表來傳入值。 例如，如果您在 Runbook 中有下列參數。

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

在此情況下，您可以將下列值傳遞至參數。

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> 當您未將值傳遞給有 Null 預設值的選擇性字串參數時，參數的值會是空字串，而不是 Null。

### <a name="configure-input-parameters-in-graphical-runbooks"></a>在圖形化 Runbook 中設定輸入參數

為了說明圖形化 Runbook 的輸入參數設定，讓我們建立 Runbook 來輸出虛擬機器 (單一 VM 或資源群組中所有 VM) 的詳細資料。 如需詳細資訊，請參閱[我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)。

圖形化 Runbook 會使用這些主要 Runbook 活動：

* 設定 Azure 執行身分帳戶以向 Azure 進行驗證。 
* [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) Cmdlet 的定義，可取得 VM 屬性。
* 使用 [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) 活動來輸出 VM 名稱。 

`Get-AzVM` 活動會定義兩個輸入：VM 名稱和資源群組名稱。 由於每次 Runbook 啟動時，這些名稱可能會不同，因此您必須將輸入參數新增至 Runbook，以接受這些輸入。 請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。

請遵循這些步驟來設定輸入參數。

1. 從 [Runbook] 頁面中選取圖形化 Runbook，然後按一下 [編輯]。
2. 在圖形化編輯器中，按一下 [輸入及輸出] 按鈕，然後按一下 [新增輸入] 以開啟 [Runbook 輸入參數] 窗格。

   ![自動化圖形化 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. [輸入及輸出] 控制項會顯示為 Runbook 定義的輸入參數清單。 在這裡，您可以新增輸入參數，或編輯現有輸入參數的設定。 若要為 Runbook 新增參數，請按一下 [新增輸入] 以開啟 [Runbook 輸入參數] 刀鋒視窗，您可以在那裡使用 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)中所定義的屬性來設定參數。

    ![加入新的輸入](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 使用下列屬性，建立將由 `Get-AzVM` 活動使用的兩個參數，然後按一下 [確定]。

   * 參數 1：
        * **名稱** -- **VMName**
        * **類型** -- 字串
        * **強制** -- **否**

   * 參數 2：
        * **名稱** -- **resourceGroupName**
        * **類型** -- 字串
        * **強制** -- **否**
        * **預設值** -- **自訂**
        * 自訂預設值 - VM 所在資源群組的名稱

5. 在 [輸入及輸出] 控制項中檢閱參數。 
6. 再次按一下 [確定]，然後按一下 [儲存]。
7. 按一下 [發佈] 來發佈您的 Runbook。

### <a name="configure-input-parameters-in-python-runbooks"></a>在 Python Runbook 中設定輸入參數

Python Runbook 與 PowerShell、PowerShell 工作流程及圖形化 Runbook 不同，其不使用具名參數。 Runbook 編輯器會將所有輸入參數剖析為引數值的陣列。 若要存取陣列，您可以將 `sys` 模組匯入 Python 指令碼，接著再使用 `sys.argv` 陣列。 請務必注意，陣列的第一個元素 (`sys.argv[0]`) 是指令碼的名稱。 因此第一個實際輸入參數是 `sys.argv[1]`。

如需在 Python Runbook 中使用輸入參數的範例，請參閱[我在 Azure 自動化中的第一個 Python Runbook](automation-first-runbook-textual-python2.md)。

## <a name="assign-values-to-input-parameters-in-runbooks"></a>將值指派給 Runbook 中的輸入參數

此節描述將值傳遞至 Runbook 中輸入參數的數種方式。 當您執行下列動作時，可以指派參數值：

* [啟動 Runbook](#start-a-runbook-and-assign-parameters)
* [測試 Runbook](#test-a-runbook-and-assign-parameters)
* [連結 Runbook 的排程](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [建立 Runbook 的 Webhook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>啟動 Runbook 並指派參數

Runbook 有多種啟動方式：透過 Azure 入口網站、透過 Webhook、透過 PowerShell Cmdlet、REST API 或 SDK。 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>使用 Azure 入口網站啟動已發佈的 Runbook，並指派參數

當您在 Azure 入口網站中[啟動 Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) 時，[啟動 Runbook] 刀鋒視窗隨即開啟，而您可以為所建立的參數輸入值。

![使用入口網站啟動](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

在輸入方塊下的標籤中，您可以查看所設定的屬性 (property)，以定義參數屬性 (attribute)，例如，強制或選擇性、類型、預設值。 參數名稱旁邊的說明提示氣球也會定義進行參數輸入值決策所需的重要資訊。 

> [!NOTE]
> 字串參數支援空的字串類型值。 在輸入參數文字方塊中輸入 `[EmptyString]`，會傳遞空字串給參數。 此外，字串類型參數不支援 Null。 若未傳遞任何值給字串參數，PowerShell 會將其解譯為 Null。

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>使用 PowerShell Cmdlet 啟動已發佈的 Runbook，並指派參數

* **Azure Resource Manager Cmdlet：** 您可以使用 [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
) \(英文\) 啟動在資源群組中建立的自動化 Runbook。

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure 傳統部署模型 Cmdlets：** 您可以使用 [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook) 啟動在預設資源群組中建立的自動化 Runbook。
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> 當您使用 PowerShell Cmdlet 啟動 Runbook 時，將建立值為 `PowerShell` 的預設參數 `MicrosoftApplicationManagementStartedBy`。 您可以在 [作業詳細資料] 窗格中檢視此參數。  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>使用 SDK 啟動 Runbook，並指派參數

* **Azure Resource Manager 方法：** 您可以使用程式設計語言的 SDK 來啟動 Runbook。 以下 C# 程式碼片段用於在您的自動化帳戶中啟動 Runbook。 您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Azure 傳統部署模型方法：** 您可以使用程式設計語言的 SDK 來啟動 Runbook。 以下 C# 程式碼片段用於在您的自動化帳戶中啟動 Runbook。 您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   若要啟動此方法，請建立字典來儲存 Runbook 參數 `VMName`、`resourceGroupName`，以及其值。 然後啟動 Runbook。 以下 C# 程式碼片段用於呼叫上面定義的方法。

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>使用 REST API 啟動 Runbook，並指派參數

您可以使用 Azure 自動化 REST API 來建立及啟動 Runbook 作業，方法是使用下列要求 URI 搭配 `PUT` 方法：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

在要求 URI 中，取代下列參數：

* `subscriptionId`:您的 Azure 訂用帳戶識別碼。  
* `resourceGroupName`:自動化帳戶的資源群組名稱。
* `automationAccountName`:裝載在指定的雲端服務內的自動化帳戶名稱。  
* `jobName`:作業的 GUID。 您可以使用 `[GUID]::NewGuid().ToString()*` 來建立 PowerShell 中的 GUID。

若要將參數傳遞至 Runbook 作業，請使用要求本文。 其會接受以 JSON 格式提供的資訊：

* Runbook 名稱：必要。 作業要啟動之 Runbook 的名稱。  
* Runbook 參數：選擇性。 參數清單的字典；清單必須採用 (名稱, 值) 格式，其中的名稱應為字串類型，而值可以是任何有效的 JSON 值。

如果您想要啟動先前以 `VMName` 和 `resourceGroupName` 作為參數所建立的 **Get-AzureVMTextual** Runbook，請使用下列 JSON 格式的要求本文。

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

如果成功建立作業，則會傳回 HTTP 狀態碼 201。 如需關於回應標頭和回應本文的詳細資訊，請參閱[使用 REST API 建立 Runbook 作業](/rest/api/automation/job/create)。

### <a name="test-a-runbook-and-assign-parameters"></a>測試 Runbook 並指派參數

當您使用測試選項[測試 Runbook 的草稿版本](automation-testing-runbook.md)時，將會開啟 [測試] 頁面。 請使用此頁面為您建立的參數設定值。

![測試並指派參數](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>將排程連結至 Runbook，並指派參數

您可以[將排程連結](automation-schedules.md)至您的 Runbook，以在特定時間啟動 Runbook。 您會在建立排程時指派輸入參數，且 Runbook 經由排程啟動時，會使用這些值。 必須提供所有必要參數值，才能儲存排程。

![排程並指派參數](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>建立 Runbook 的 Webhook，並指派參數

您可以為您的 Runbook 建立 [Webhook](automation-webhooks.md) ，並設定 Runbook 輸入參數。 必須提供所有必要參數值，才能儲存 Webhook。

![建立 Webhook 並指派參數](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

當您使用 Webhook 執行 Runbook 時，會隨著您定義的輸入參數傳送預先定義的輸入參數 `[WebhookData](automation-webhooks.md)`。 

![WebhookData 參數](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>將 JSON 物件傳遞至 Runbook

這適用於在 JSON 檔案中儲存您要傳送至 Runbook 的資料。 例如，您可以建立 JSON 檔案，其中包含您要傳送至 Runbook 的所有參數。 若要這樣做，您必須將 JSON 程式碼轉換為字串，然後先將字串轉換為 PowerShell 物件，再將其傳送至 Runbook。

在此節使用的範例中，PowerShell 指令碼會呼叫 [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0) 以啟動 PowerShell Runbook，並將 JSON 檔案內容傳遞至 Runbook。 PowerShell Runbook 會啟動 Azure VM，方法是從 JSON 物件擷取 VM 的參數。

### <a name="create-the-json-file"></a>建立 JSON 檔案

在文字檔中輸入下列程式碼，然後在本機電腦上的某處將其儲存為 **test.json**。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>建立 runbook

在 Azure 自動化中建立名為 **Test-Json** 的新 PowerShell Runbook。 請參閱[我的第一個 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。

若要接受 JSON 資料，Runbook 必須採用物件作為輸入參數。 然後 Runbook 可以使用 JSON 檔案中定義的屬性。

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

在您的自動化帳戶中儲存並發佈此 Runbook。

### <a name="call-the-runbook-from-powershell"></a>從 PowerShell 呼叫 Runbook

您現在可以使用 Azure PowerShell，從本機電腦呼叫 Runbook。 

1. 登入 Azure，如所示。 之後，系統會提示您輸入 Azure 認證。

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 請注意，圖形化 Runbook 無法使用這些別名。 圖形化 Runbook 只能使用 `Connect-AzAccount` 本身。

1. 取得所儲存 JSON 檔案的內容，並將其轉換成字串。 `JsonPath` 指出您儲存 JSON 檔案的路徑。

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. 將 `$json` 的字串內容轉換為 PowerShell 物件。

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 建立 `Start-AzAutomationRunbook` 參數的雜湊表。 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   請注意，您要將 `Parameters` 的值設定為 PowerShell 物件，其中包含 JSON 檔案中的值。
1. 啟動 Runbook。

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>後續步驟

* 若要準備文字式 Runbook，請參閱[在 Azure 自動化中編輯文字式 Runbook](automation-edit-textual-runbook.md)。
* 若要準備圖形化 Runbook，請參閱[在 Azure 自動化中製作圖形化 Runbook](automation-graphical-authoring-intro.md)。
