---
title: Runbook 輸入參數
description: Runbook 輸入參數可讓您將資料傳遞至剛啟動的 Runbook，以增加 Runbook 的彈性。 本文說明在 Runbook 中使用輸入參數的不同案例。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 3741adbab6bcbc68f266c331e3056013afc0105e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80656037"
---
# <a name="runbook-input-parameters"></a>Runbook 輸入參數

Runbook 輸入參數會在啟動時允許資料傳遞給它，藉此增加 runbook 的彈性。 這些參數可讓 runbook 動作以特定案例和環境為目標。 本文說明在 runbook 中設定和使用輸入參數的方式。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 如需有關混合式 Runbook 背景工作角色的 Az 模組安裝指示，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 針對您的自動化帳戶，您可以使用[如何更新 Azure 自動化中的 Azure PowerShell 模組](automation-update-azure-modules.md)，將模組更新為最新版本。

## <a name="configuring-input-parameters"></a>設定輸入參數

您可以設定 PowerShell、PowerShell 工作流程、圖形化和 Python runbook 的輸入參數。 一個 Runbook 可以使用具有不同資料類型的多個參數，或完全不使用參數。 輸入參數可以是強制性或選擇性的，而且您可以使用選擇性參數的預設值。

當您啟動 runbook 時，您可以為其指派輸入參數值。 您可以從 Azure 入口網站、web 服務或 PowerShell 啟動 runbook。 您也可以啟動一個 Runbook 做為在另一個 Runbook 中以內嵌方式呼叫的子 Runbook。

### <a name="configure-input-parameters-in-powershell-runbooks"></a>在 PowerShell Runbook 中設定輸入參數

Azure 自動化中的 PowerShell 和 PowerShell 工作流程 runbook 支援透過下列屬性定義的輸入參數。 

| **屬性** | **說明** |
|:--- |:--- |
| 類型 |必要。 對參數值預期的資料類型。 任何 .NET 類型皆有效。 |
| Name |必要。 參數名稱。 此名稱在 runbook 內必須是唯一的，且必須以字母開頭，且只能包含字母、數位或底線字元。 |
| 強制性 |選擇性。 指定參數是否需要值的布林值。 如果您將此設定為 True，則在 runbook 啟動時必須提供值。 如果您將此設定為 False，則值為選擇性。 如果您未指定`Mandatory`屬性的值，則 PowerShell 預設會將輸入參數視為選擇性。 |
| 預設值 |選擇性。 當 runbook 啟動時未傳入輸入值時，用於參數的值。 Runbook 可以設定任何參數的預設值。 |

Windows PowerShell 支援的輸入參數屬性比上面所列的更多，例如驗證、別名和參數集。 不過，Azure 自動化目前僅支援列出的輸入參數屬性。

例如，讓我們看一下 PowerShell 工作流程 runbook 中的參數定義。 此定義具有下列一般形式，其中多個參數會以逗號分隔。

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

現在，讓我們設定 PowerShell 工作流程 runbook 的輸入參數，以輸出虛擬機器的詳細資料，也就是單一 VM 或資源群組內的所有 Vm。 此 runbook 有兩個參數，如下列螢幕擷取畫面所示：虛擬機器的名稱（`VMName`）和資源群組的名稱（`resourceGroupName`）。

![自動化 PowerShell 工作流程](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

在此參數定義中，輸入參數是字串類型的簡單參數。

請注意，PowerShell 和 PowerShell 工作流程 runbook 支援所有簡單類型和複雜類型， `Object`例如`PSCredential`輸入參數的或。 如果您的 runbook 有物件輸入參數，您必須使用具有名稱/值組的 PowerShell 雜湊表來傳入值。 例如，您在 runbook 中具有下列參數。

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

在此情況下，您可以將下列值傳遞給參數。

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> 當您未將值傳遞給具有 null 預設值的選擇性字串參數時，參數的值會是空字串，而不是 Null。

### <a name="configure-input-parameters-in-graphical-runbooks"></a>在圖形化 Runbook 中設定輸入參數

為了說明圖形化 runbook 的輸入參數設定，讓我們建立 runbook 來輸出虛擬機器的詳細資料，也就是單一 VM 或資源群組中的所有 Vm。 如需詳細資訊，請參閱[我的第一個圖形化 runbook](automation-first-runbook-graphical.md)。

圖形化 runbook 會使用下列主要的 runbook 活動：

* 設定 Azure 執行身分帳戶以向 Azure 進行驗證。 
* 取得 VM 屬性的[update-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) Cmdlet 定義。
* 使用[寫入輸出](/powershell/module/microsoft.powershell.utility/write-output)活動來輸出 VM 名稱。 

`Get-AzVM`活動會定義兩個輸入，也就是 VM 名稱和資源組名。 由於每次 runbook 啟動時，這些名稱可能會不同，因此您必須將輸入參數新增至 runbook，以接受這些輸入。 請參閱[Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。

請遵循下列步驟來設定輸入參數。

1. 從 [Runbook] 頁面中選取圖形化 runbook，然後按一下 [**編輯**]。
2. 在圖形化編輯器中，按一下 [**輸入和輸出**] 按鈕，然後**新增 [輸入**] 以開啟 [Runbook 輸入參數] 窗格。

   ![自動化圖形化 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. 輸入和輸出控制項會顯示為 runbook 定義的輸入參數清單。 在這裡，您可以加入新的輸入參數，或編輯現有輸入參數的設定。 若要為 runbook 新增新參數，請按一下 [**新增輸入**] 以開啟 [ **runbook 輸入參數**] 分頁，您可以在其中使用 Azure 自動化中[圖形化編寫](automation-graphical-authoring-intro.md)中定義的屬性來設定參數。

    ![加入新的輸入](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 建立具有下列屬性的兩個參數，以供`Get-AzVM`活動使用，然後按一下 **[確定]**。

   * 參數1：
        * **名稱** -- **VMName**
        * **類型**--字串
        * **強制** -- **否**

   * 參數 2：
        * **名稱** -- **resourceGroupName**
        * **類型**--字串
        * **強制** -- **否**
        * **預設值** -- **Custom**
        * 自訂預設值--包含 Vm 之資源群組的名稱

5. 在輸入和輸出控制項中，查看參數。 
6. 再次按一下 **[確定]** ，然後按一下 [**儲存**]。
7. 按一下 [**發佈**] 以發佈您的 runbook。

### <a name="configure-input-parameters-in-python-runbooks"></a>在 Python Runbook 中設定輸入參數

不同于 PowerShell、PowerShell 工作流程和圖形化 runbook，Python runbook 不會採用具名引數。 Runbook editor 會將所有輸入參數剖析為引數值的陣列。 您可以藉由將`sys`模組匯入 Python 腳本，然後使用`sys.argv`陣列來存取陣列。 請務必注意，陣列`sys.argv[0]`的第一個元素是腳本的名稱。 因此，第一個實際的輸入`sys.argv[1]`參數是。

如需在 Python Runbook 中使用輸入參數的範例，請參閱[我在 Azure 自動化中的第一個 Python Runbook](automation-first-runbook-textual-python2.md)。

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>將值指派給 runbook 中的輸入參數

本節說明將值傳遞至 runbook 中輸入參數的數種方式。 當您執行下列動作時，可以指派參數值：

* [啟動 Runbook](#start-a-runbook-and-assign-parameters)
* [測試 Runbook](#test-a-runbook-and-assign-parameters)
* [連結 runbook 的排程](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [建立 Runbook 的 Webhook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>啟動 Runbook 並指派參數

Runbook 的啟動方式有許多種：透過 Azure 入口網站、使用 webhook、使用 PowerShell Cmdlet、REST API，或使用 SDK。 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>使用 Azure 入口網站啟動已發佈的 runbook，並指派參數

當您在 Azure 入口網站中[啟動 runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal)時，[**啟動 runbook** ] 分頁隨即開啟，您可以為已建立的參數輸入值。

![使用入口網站啟動](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

在 [輸入] 方塊下方的標籤中，您可以看到已設定為定義參數屬性的屬性，例如 [強制] 或 [選擇性]、[類型]、[預設值]。 參數名稱旁邊的說明提示氣球也會定義進行參數輸入值決策所需的重要資訊。 

> [!NOTE]
> 字串參數支援空的字串類型值。 在`[EmptyString]` [輸入參數] 方塊中輸入，會將空字串傳遞給參數。 此外，字串參數不支援 Null。 如果您未將任何值傳遞至字串參數，PowerShell 會將它解釋為 Null。

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>使用 PowerShell Cmdlet 啟動已發佈的 runbook 並指派參數

* **Azure Resource Manager Cmdlet：** 您可以使用[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)，啟動在資源群組中建立的自動化 runbook。

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure 傳統部署模型 Cmdlet：** 您可以使用 [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure/start-azureautomationrunbook) 啟動在預設資源群組中建立的自動化 Runbook。
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> 當您使用 PowerShell Cmdlet 啟動 runbook 時，會使用值`MicrosoftApplicationManagementStartedBy` `PowerShell`建立預設參數。 您可以在 [作業詳細資料] 窗格中查看此參數。  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>使用 SDK 啟動 runbook 並指派參數

* **Azure Resource Manager 方法：** 您可以使用程式設計語言的 SDK 來啟動 runbook。 以下 C# 程式碼片段用於在您的自動化帳戶中啟動 Runbook。 您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。

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

   若要啟動此方法，請建立字典來儲存 runbook 參數`VMName`和`resourceGroupName`及其值。 然後啟動 Runbook。 以下 C# 程式碼片段用於呼叫上面定義的方法。

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>使用 REST API 啟動 runbook 並指派參數

您可以使用`PUT`方法搭配下列要求 URI 來建立及啟動具有 Azure 自動化 REST API 的 runbook 作業：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

在要求 URI 中，取代下列參數：

* `subscriptionId`：您的 Azure 訂用帳戶識別碼。  
* `resourceGroupName`：自動化帳戶的資源組名。
* `automationAccountName`：在指定的雲端服務中裝載之自動化帳戶的名稱。  
* `jobName`：作業的 GUID。 您可以使用`[GUID]::NewGuid().ToString()*`來建立 PowerShell 中的 guid。

若要將參數傳遞至 runbook 作業，請使用要求主體。 它會採用 JSON 格式提供的下列資訊：

* Runbook 名稱： 必要。 作業要啟動之 Runbook 的名稱。  
* Runbook 參數： 選擇性。 參數清單的字典（名稱、值）格式，其中名稱的類型為 String，而 value 可以是任何有效的 JSON 值。

如果您想要啟動先前**Get-AzureVMTextual**使用`VMName`和`resourceGroupName`作為參數建立的 GET-AZUREVMTEXTUAL runbook，請使用下列 JSON 格式做為要求主體。

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

如果成功建立作業，則會傳回 HTTP 狀態碼 201。 如需回應標頭和回應主體的詳細資訊，請參閱[使用 REST API 建立 runbook 作業](/rest/api/automation/job/create)。

### <a name="test-a-runbook-and-assign-parameters"></a>測試 Runbook 並指派參數

當您使用測試選項[測試 runbook 的草稿版本](automation-testing-runbook.md)時，會開啟 [測試] 頁面。 使用此頁面來設定您已建立之參數的值。

![測試並指派參數](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>將排程連結至 Runbook，並指派參數

您可以[將排程連結](automation-schedules.md)至您的 Runbook，以在特定時間啟動 Runbook。 您會在建立排程時指派輸入參數，且 Runbook 經由排程啟動時，會使用這些值。 在提供所有必要的參數值之前，您無法儲存排程。

![排程並指派參數](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>建立 Runbook 的 Webhook，並指派參數

您可以為您的 Runbook 建立 [Webhook](automation-webhooks.md) ，並設定 Runbook 輸入參數。 必須提供所有必要參數值，才能儲存 webhook。

![建立 Webhook 並指派參數](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

當您使用 webhook 執行 runbook 時，會傳送預先定義的輸入`[WebhookData](automation-webhooks.md)`參數，以及您定義的輸入參數。 

![WebhookData 參數](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>將 JSON 物件傳遞至 runbook

這適用於在 JSON 檔案中儲存您要傳送至 Runbook 的資料。 例如，您可以建立 JSON 檔案，其中包含您要傳遞至 runbook 的所有參數。 若要這樣做，您必須將 JSON 程式碼轉換成字串，然後將字串轉換為 PowerShell 物件，再將它傳遞至 runbook。

本節會使用範例，其中 PowerShell 腳本會呼叫[AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)來啟動 PowerShell runbook，並將 JSON 檔案的內容傳遞至 runbook。 PowerShell runbook 會藉由從 JSON 物件抓取 VM 的參數來啟動 Azure VM。

### <a name="create-the-json-file"></a>建立 JSON 檔案

在文字檔中輸入下列程式碼，並在本機電腦上的某個位置將它儲存為**test. json** 。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>建立 runbook

在 Azure 自動化中，建立名為**Test-Json**的新 PowerShell runbook。 請參閱[我的第一個 PowerShell runbook](automation-first-runbook-textual-powershell.md)。

若要接受 JSON 資料，Runbook 必須採用物件作為輸入參數。 然後，runbook 可以使用 JSON 檔案中定義的屬性。

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

1. 如所示，登入 Azure。 之後，系統會提示您輸入您的 Azure 認證。

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >針對 PowerShell Runbook，`Add-AzAccount` 和 `Add-AzureRMAccount` 是 `Connect-AzAccount` 的別名。 請注意，這些別名無法供圖形化 runbook 使用。 圖形化 runbook 只能使用`Connect-AzAccount`其本身。

1. 取得已儲存 JSON 檔案的內容，並將它轉換成字串。 `JsonPath`指出您儲存 JSON 檔案的路徑。

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. 將的字串內容`$json`轉換成 PowerShell 物件。

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 建立的參數雜湊表`Start-AzAutomationRunbook`。 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   請注意，您要將 `Parameters` 的值設定為 PowerShell 物件，其中包含 JSON 檔案中的值。
1. 啟動 runbook。

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>後續步驟

* 如需以不同方式啟動 Runbook 的詳細資訊，請參閱 [啟動 Runbook](automation-starting-a-runbook.md)。
* 若要編輯文字 Runbook，請參閱 [編輯文字 Runbook](automation-edit-textual-runbook.md)。
* 若要編輯圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
