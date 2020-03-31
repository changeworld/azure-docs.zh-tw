---
title: Runbook 輸入參數
description: Runbook 輸入參數可讓您將資料傳遞至剛啟動的 Runbook，以增加 Runbook 的彈性。 本文說明在 Runbook 中使用輸入參數的不同案例。
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 17be351d4af3d277242af70ea96e8735a5f68bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329080"
---
# <a name="runbook-input-parameters"></a>Runbook 輸入參數

Runbook 輸入參數允許在啟動時將資料傳遞給 Runbook，從而提高了 Runbook 的靈活性。 這些參數允許針對特定方案和環境將 Runbook 操作作為目標。 本文介紹了 Runbook 中輸入參數的配置和使用。

>[!NOTE]
>本文已更新為使用新的 Azure PowerShell Az 模組。 AzureRM 模組在至少 2020 年 12 月之前都還會持續收到錯誤 (Bug) 修正，因此您仍然可以持續使用。 若要深入了解新的 Az 模組和 AzureRM 的相容性，請參閱[新的 Azure PowerShell Az 模組簡介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有關混合 Runbook 輔助角色上的 Az 模組安裝說明，請參閱[安裝 Azure PowerShell 模組](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 對於自動化帳戶，可以使用["如何更新 Azure 自動化 中的 Azure PowerShell"模組](automation-update-azure-modules.md)將模組更新到最新版本。

## <a name="configuring-input-parameters"></a>配置輸入參數

您可以為 PowerShell、PowerShell 工作流、圖形和 Python Runbook 配置輸入參數。 一個 Runbook 可以使用具有不同資料類型的多個參數，或完全不使用參數。 輸入參數可以是必需的，也可以是可選的，也可以對可選參數使用預設值。

啟動 Runbook 時，會將值分配給 Runbook 的輸入參數。 可以從 Azure 門戶、Web 服務或 PowerShell 啟動 Runbook。 您也可以啟動一個 Runbook 做為在另一個 Runbook 中以內嵌方式呼叫的子 Runbook。

### <a name="configure-input-parameters-in-powershell-runbooks"></a>在 PowerShell Runbook 中設定輸入參數

Azure 自動化中的 PowerShell 和 PowerShell 工作流運行簿支援通過以下屬性定義的輸入參數。 

| **屬性** | **描述** |
|:--- |:--- |
| 類型 |必要。 對參數值預期的資料類型。 任何 .NET 類型皆有效。 |
| 名稱 |必要。 參數名稱。 此名稱在 Runbook 中必須是唯一的，必須以字母開頭，並且只能包含字母、數位或底線。 |
| 強制性 |選擇性。 布林值指定參數是否需要值。 如果將此值設置為**true，** 則在啟動 Runbook 時必須提供值。 如果將此值設置為**false**，則值是可選的。 如果不為 **"強制"** 屬性指定值，PowerShell 預設情況下認為輸入參數是可選的。 |
| 預設值 |選擇性。 在 Runbook 啟動時沒有傳入輸入值時用於參數的值。 Runbook 可以為任何參數設置預設值。 |

Windows PowerShell 支援的輸入參數屬性比上面列出的屬性多，例如驗證、別名和參數集。 但是，Azure 自動化目前僅支援列出的輸入參數屬性。

例如，讓我們在 PowerShell 工作流運行簿中查看參數定義。 此定義具有以下常規形式，其中多個參數用逗號分隔。

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

現在，讓我們為 PowerShell 工作流運行簿配置輸入參數，該運行簿輸出有關虛擬機器的詳細資訊，即單個 VM 或資源組中的所有 VM。 此 Runbook 有兩個參數，如以下螢幕截圖所示：虛擬機器的名稱 （*VMName*） 和資源組的名稱 （*資源組名稱*）。

![自動化 PowerShell 工作流程](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

在此參數定義中，輸入參數是類型字串的簡單參數。

請注意，PowerShell 和 PowerShell 工作流運行簿支援所有簡單類型和複雜類型，例如用於輸入參數**的物件**或**PS憑據**。 如果 Runbook 具有物件輸入參數，則必須使用具有名稱值對的 PowerShell 雜湊表來傳遞值。 例如，Runbook 中具有以下參數。

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

在這種情況下，您可以將以下值傳遞給參數。

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> 如果不將值傳遞給具有 null 預設值的可選 String 參數，則該參數的值為空字串而不是**null**。

### <a name="configure-input-parameters-in-graphical-runbooks"></a>在圖形化 Runbook 中設定輸入參數

為了說明圖形 Runbook 的輸入參數配置，讓我們創建一個 Runbook，用於輸出有關虛擬機器的詳細資訊，其中包括單個 VM 或資源組中的所有 VM。 有關詳細資訊，請參閱[我的第一個圖形運行簿](automation-first-runbook-graphical.md)。

圖形運行簿使用這些主要 Runbook 活動：

* 配置 Azure 運行作為帳戶，以便使用 Azure 進行身份驗證。 
* [獲取 AVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm?view=azps-3.5.0) Cmdlet 以獲取 VM 屬性的定義。
* 使用[寫入輸出](/powershell/module/microsoft.powershell.utility/write-output)活動輸出 VM 名稱。 

**Get-AzVM**活動定義兩個輸入：VM 名稱和資源組名稱。 由於每次 Runbook 啟動時這些名稱可能不同，因此必須向 Runbook 添加輸入參數以接受這些輸入。 請參閱[Azure 自動化 中的圖形創作](automation-graphical-authoring-intro.md)。

按照以下步驟配置輸入參數。

1. 從**Runbook**頁面中選擇圖形運行簿，然後按一下"**編輯**"。
2. 在圖形編輯器中，按一下 **"輸入"和"輸出**"按鈕，然後**添加輸入**以打開 Runbook 輸入參數窗格。

   ![自動化圖形化 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. "輸入"和"輸出"控制項顯示為 Runbook 定義的輸入參數清單。 在這裡，您可以添加新的輸入參數或編輯現有輸入參數的配置。 要為 Runbook 添加新參數，請按一下"**添加輸入**"以打開**Runbook 輸入參數**邊欄選項卡，您可以在其中使用 Azure[自動化 中的圖形創作中](automation-graphical-authoring-intro.md)定義的屬性配置參數。

    ![加入新的輸入](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 創建兩個參數，具有要由**Get-AzVM**活動使用的以下屬性，然後按一下"**確定**"。

   * 參數 1：
        * **名稱** -- **VM 名稱**
        * **類型**-- 字串
        * **強制** -- **否**

   * 參數 2：
        * **命名** -- **資源組名稱**
        * **類型**-- 字串
        * **強制** -- **否**
        * **預設值** -- **自訂**
        * 自訂預設值 -- 包含 VM 的資源組的名稱

5. 查看輸入和輸出控制項中的參數。 
6. 再次按一下 **"確定**"，然後按一下"**保存**"。
7. 按一下 **"發佈**"以發佈 Runbook。

### <a name="configure-input-parameters-in-python-runbooks"></a>在 Python Runbook 中設定輸入參數

與 PowerShell、PowerShell 工作流和圖形運行簿不同，Python Runbook 不採用具名引數。 Runbook 編輯器將所有輸入參數解析為參數值陣列。 您可以通過將**sys**模組導入 Python 腳本，然後使用**sys.argv**陣列來訪問陣列。 請務必注意，陣列`sys.argv[0]`的第一個元素是腳本的名稱。 因此，第一個實際輸入參數是*sys.argv[1]。*

如需在 Python Runbook 中使用輸入參數的範例，請參閱[我在 Azure 自動化中的第一個 Python Runbook](automation-first-runbook-textual-python2.md)。

## <a name="assigning-values-to-input-parameters-in-runbooks"></a>在 Runbook 中為輸入參數分配值

本節介紹將值傳遞給 Runbook 中的輸入參數的幾種方法。 您可以在以下時間分配參數值：

* [啟動 Runbook](#start-a-runbook-and-assign-parameters)
* [測試 Runbook](#test-a-runbook-and-assign-parameters)
* [連結運行簿的計畫](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [建立 Runbook 的 Webhook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>啟動 Runbook 並指派參數

運行簿可以通過多種方式啟動：通過 Azure 門戶、Webhook、使用 PowerShell Cmdlet、REST API 或 SDK。 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>使用 Azure 門戶啟動已發佈的 Runbook 並分配參數

在 Azure 門戶中[啟動 Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal)時，**將打開"開始運行簿"** 邊欄選項卡，您可以輸入已創建的參數的值。

![使用入口網站啟動](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

在輸入框下方的標籤中，您可以看到已設置為定義參數屬性的屬性，例如，必需或可選的類型、預設值。 參數名稱旁邊的説明氣球還定義了對參數輸入值做出決策所需的關鍵資訊。 

> [!NOTE]
> 字串參數支援字串類型的空值。 在輸入參數文字方塊中輸入 **[EmptyString]** ，會傳遞空字串給參數。 此外，字串參數不支援 Null。 如果不將任何值傳遞給字串參數，PowerShell 會將其解釋為 Null。

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>使用 PowerShell Cmdlet 啟動已發佈的 Runbook 並分配參數

* **Azure 資源管理器 Cmdlet：** 可以使用["開始-Az自動化 Runbook"](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.5.0
)啟動在資源組中創建的自動化 Runbook。

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
> 當您使用 PowerShell Cmdlet 啟動 Runbook 時，將使用值**PowerShell**創建預設參數*Microsoft 應用程式管理啟動。* 您可以在"作業詳細資訊"窗格中查看此參數。  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>使用 SDK 啟動運行簿並分配參數

* **Azure 資源管理器方法：** 您可以使用程式設計語言的 SDK 啟動 Runbook。 以下 C# 程式碼片段用於在您的自動化帳戶中啟動 Runbook。 您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ResourceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。  

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

* **Azure 傳統部署模型方法：** 您可以使用程式設計語言的 SDK 來啟動 Runbook。 以下 C# 程式碼片段用於在您的自動化帳戶中啟動 Runbook。 您可以在我們的 [GitHub 儲存機制](https://github.com/Azure/azure-sdk-for-net/blob/master/src/ServiceManagement/Automation/Automation.Tests/TestSupport/AutomationTestBase.cs)中檢視完整的程式碼。

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

   要啟動此方法，請創建一個字典來存儲 Runbook 參數*VMName* *和資源 GroupName*及其值。 然後啟動 Runbook。 以下 C# 程式碼片段用於呼叫上面定義的方法。

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>使用 REST API 啟動運行簿並分配參數

可以使用具有以下請求 URI 的**PUT**方法創建和啟動使用 Azure 自動化 REST API 的 Runbook 作業：`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

在要求 URI 中，取代下列參數：

* *訂閱 ID*：Azure 訂閱 ID。  
* *資源組名稱*：自動化帳戶的資源組的名稱。
* *自動化帳戶名稱*：在指定的雲服務中託管的自動化帳戶的名稱。  
* *作業名稱*： 作業的 GUID。 PowerShell 中的 GUID 可以使用`[GUID]::NewGuid().ToString()*`創建。

要將參數傳遞給 Runbook 作業，請使用請求正文。 它採用以下資訊，以 JSON 格式提供：

* Runbook 名稱： 必要。 作業要啟動之 Runbook 的名稱。  
* Runbook 參數： 選擇性。 參數清單的字典（名稱、值）格式，其中名稱為字串類型，值可以是任何有效的 JSON 值。

如果要啟動之前使用*VMName*和資源*GroupName*作為參數創建的**獲取 AzureVMText_runbook，** 請使用以下 JSON 格式作為請求正文。

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

如果成功建立作業，則會傳回 HTTP 狀態碼 201。 有關回應標頭和回應正文的詳細資訊，請參閱使用 REST [API 創建 Runbook 作業](/rest/api/automation/job/create)。

### <a name="test-a-runbook-and-assign-parameters"></a>測試 Runbook 並指派參數

使用測試選項[測試 Runbook 的草稿版本](automation-testing-runbook.md)時，將打開 **"測試**"頁。 使用此頁面可配置已創建的參數的值。

![測試並指派參數](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>將排程連結至 Runbook，並指派參數

您可以[將排程連結](automation-schedules.md)至您的 Runbook，以在特定時間啟動 Runbook。 您會在建立排程時指派輸入參數，且 Runbook 經由排程啟動時，會使用這些值。 在提供所有必需參數值之前，無法保存計畫。

![排程並指派參數](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>建立 Runbook 的 Webhook，並指派參數

您可以為您的 Runbook 建立 [Webhook](automation-webhooks.md) ，並設定 Runbook 輸入參數。 在提供所有必需參數值之前，無法保存 Webhook。

![建立 Webhook 並指派參數](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

使用 Webhook 執行 Runbook 時，將發送預定義的輸入參數*[WebhookData](automation-webhooks.md)* 以及您定義的輸入參數。 

![WebhookData 參數](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="passing-a-json-object-to-a-runbook"></a>將 JSON 物件傳遞到 Runbook

這適用於在 JSON 檔案中儲存您要傳送至 Runbook 的資料。 例如，您可以創建一個 JSON 檔，該檔包含要傳遞給 Runbook 的所有參數。 為此，必須將 JSON 代碼轉換為字串，然後將字串轉換為 PowerShell 物件，然後再將其傳遞到 Runbook。

本節使用一個示例，其中 PowerShell 腳本調用[Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.5.0)來啟動 PowerShell 運行簿，將 JSON 檔的內容傳遞到 Runbook。 PowerShell 運行簿通過從 JSON 物件檢索 VM 的參數來啟動 Azure VM。

### <a name="create-the-json-file"></a>建立 JSON 檔案

在文字檔中鍵入以下代碼，並將其另存為`test.json`本地電腦上的某處。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>建立 runbook

在 Azure 自動化中創建新的 PowerShell 運行簿，名為**Test-Json。** 請參閱[我的第一個 PowerShell 運行簿](automation-first-runbook-textual-powershell.md)。

若要接受 JSON 資料，Runbook 必須採用物件作為輸入參數。 然後，Runbook 可以使用 JSON 檔中定義的屬性。

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

1. 如圖所示登錄到 Azure。 之後，系統會提示您輸入 Azure 憑據。

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >對於 PowerShell 運行簿，**添加 Az 帳戶**和添加**AzureRM帳戶**是**Connect-AzAccount**的別名。 請注意，這些別名不適用於圖形運行簿。 圖形運行簿只能使用**Connect-AzAccount**本身。

1. 獲取保存的 JSON 檔的內容並將其轉換為字串。 `JsonPath` 是您儲存 JSON 檔案的路徑。

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. 將 的`$json`字串內容轉換為 PowerShell 物件。

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. 為**啟動-AzAutomationRunbook**的參數創建雜湊表。 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   請注意，您將*參數*的值設置為包含 JSON 檔中的值的 PowerShell 物件。
1. 啟動 Runbook。

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>後續步驟

* 如需以不同方式啟動 Runbook 的詳細資訊，請參閱 [啟動 Runbook](automation-starting-a-runbook.md)。
* 若要編輯文字 Runbook，請參閱 [編輯文字 Runbook](automation-edit-textual-runbook.md)。
* 若要編輯圖形化 Runbook，請參閱 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。
