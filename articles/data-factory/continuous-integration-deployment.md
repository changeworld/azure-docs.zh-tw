---
title: Azure 資料工廠中的持續整合和交付
description: 了解如何使用持續整合和傳遞將一個環境 (開發、測試、生產) 中的 Data Factory 管線移至另一個環境。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 6aad01808ad155b745b614d8de6009386f0d2914
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687952"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure 資料工廠中的持續整合和交付

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概觀

持續整合是儘早自動測試對代碼庫所做的每個更改的做法。持續交付遵循連續集成期間發生的測試,並將更改推送到暫存或生產系統。

在 Azure 資料工廠中,持續整合和交付 (CI/CD) 意味著將資料工廠管道從一個環境(開發、測試、生產)移動到另一個環境。 您可以使用資料工廠 UX 與 Azure 資源管理器範本整合執行 CI/CD。

在數據工廠 UX 中,可以從**ARM 範本**下拉功能表生成資源管理器範本。 當您選擇 **「匯出 ARM 範本**」時,門戶將生成資料工廠的資源管理器範本以及包含所有連接字串和其他參數的設定檔。 然後為每個環境(開發、測試、生產)創建一個配置檔。 所有環境的主要 Resource Manager 範本檔案會保持相同。

有關此功能的九分鐘介紹和演示,請觀看此視頻:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 生命週期

下面是使用 Azure 儲存庫 Git 配置的 Azure 資料工廠中的 CI/CD 生命週期範例概述。 有關如何設定 Git 儲存函式庫的詳細資訊,請參考 Azure[資料工廠中的原始程式碼管理](source-control.md)。

1.  使用 Azure 儲存庫 Git 創建和配置開發數據工廠。 所有開發人員都應具有創作數據工廠資源(如管道和數據集)的許可權。

1.  當開發人員對其功能分支進行更改時,他們會調試其管道運行及其最新更改。 有關如何除錯管執行的詳細資訊,請參閱[使用 Azure 資料工廠進行反覆發程式開發和除錯](iterative-development-debugging.md)。

1.  開發人員對其更改感到滿意後,他們將創建從其功能分支到主分支或協作分支的拉取請求,以便由對等方審核其更改。

1.  批准拉取請求並將更改合併到主分支中后,可以將更改發佈到開發工廠。

1.  當團隊準備將更改部署到測試工廠,然後部署到生產工廠時,團隊將從主分支匯出資源管理器範本。

1.  匯出的資源管理員範本使用不同的參數檔部署到測試工廠和生產工廠。

## <a name="create-a-resource-manager-template-for-each-environment"></a>為每個環境建立 Resource Manager 範本

1. 在**ARM 樣本**清單中,選擇 **「匯出 ARM 範本**」以在開發環境中匯出數據工廠的資源管理器樣本。

   ![匯出資源管理員範本](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 在測試和生產數據工廠中,選擇 **「導入 ARM 範本**」 。。 此動作會將您移至 Azure 入口網站，您可以在此處匯入先前匯出的範本。 編輯**器中選擇"建立您自己的範本**"以開啟資源管理員範本編輯器。

   ![編譯您自己的樣本](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. 選擇 **「載入檔案**」,然後選擇生成的資源管理器範本。 這是位於步驟 1 中匯出的 .zip 檔中的**arm_template.json**檔。

   ![編輯範本](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 在"設置"部分中,輸入配置值,如鏈接的服務認證。 完成後,選擇 **「購買」** 以部署資源管理器範本。

   ![設定部份](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>連接字串

有關如何配置連接字串的資訊,請參閱連接器的文章。 例如，針對 Azure SQL Database，請參閱[使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料](connector-azure-sql-database.md)。 要驗證連接字串,可以在數據工廠 UX 中打開資源的代碼檢視。 在代碼檢視中,將刪除連接字串的密碼或帳戶密鑰部分。 要開啟程式碼檢視,請選擇此處突出顯示的圖示:

![開啟代碼檢視以檢視連接字串](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>使用 Azure 管道版本自動實現連續整合

以下是設置 Azure 管道版本的指南,該版本自動將數據工廠部署到多個環境。

![透過 Azure Pipelines 進行持續整合的圖表](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>需求

-   連結到使用 [Azure 資源管理器服務終結點](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)的 Azure 工作室團隊基礎伺服器或 Azure 儲存庫的訂閱。

-   使用 Azure 儲存庫 Git 整合配置的資料工廠。

-   包含每個環境機密的 [Azure 金鑰保管庫](https://azure.microsoft.com/services/key-vault/)。

### <a name="set-up-an-azure-pipelines-release"></a>設定 Azure Pipelines 發行

1.  在[Azure DevOps](https://dev.azure.com/)中,打開使用數據工廠配置的專案。

1.  在頁面的左側,選擇**管道**,然後選擇 **"釋放**"。

    ![選擇導管,發佈](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  選擇 **'新增管道**',或,如果現有導管,請選擇 **'新增**'**與 '導新管道**。

1.  選擇 **「空作業**範本」。

    ![選擇空白工作](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在 **「階段名稱」** 框中,輸入環境的名稱。

1.  選擇 **'新增專案**',然後選擇與資料工廠一起配置的儲存庫。 選擇**預設分支**的**adf_publish。** 對於**預設版本**,**從預設分支中選擇" 最新"**

    ![新增構件](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  新增 Azure Resource Manager 部署工作：

    a.  在舞台檢視中,選擇 **「查看階段任務**」。。

    ![舞臺檢視](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  建立新的工作。 搜尋**Azure 資源組部署**,然後選擇 **"添加**"。

    c.  在「部署」任務中,選擇目標數據工廠的訂閱、資源組和位置。 如有必要,提供憑據。

    d.  在 **'s'** 列表中,選擇 **'或"更新資源群組**"

    e.  選擇**樣本**框旁邊的省略號按鈕 (**...)。** 瀏覽在本文的每個環境部分的「[建立資源管理員資源管理員」 樣本](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)中使用 **「導入 ARM 範本**」 的 Azure 資源管理員樣本。 在adf_publish分支的<FactoryName>資料夾中查找此檔。

    f.  選擇 **...** 在 **「模板參數」** 框旁邊,以選擇參數檔。 您選擇的檔案將取決於您是建立副本還是正在使用預設檔案 ARMTemplate 參數 ForFactory.json。

    g.  選擇 **...** 在 **「覆蓋範本參數」** 框旁邊,然後輸入目標數據工廠的資訊。 對於來自 Azure 金鑰保管庫的憑據,請在雙引號之間輸入機密的名稱。 例如,如果機密的名稱為 cred1,請輸入此值的 **$(cred1)"。**

    h. 選擇**部署模式**的**漸寫**。

    > [!WARNING]
    > 如果為 **「部署」模式**選擇 **「完成」** 的, 則可能會刪除現有資源,包括「資源管理器」 樣本中未定義的目標資源群組中的所有資源。

    ![資料工廠部署](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  儲存發行管線。

1. 要觸發版本,請選擇 **「創建版本**」。

   ![選擇"創建發佈"](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> 在 CI/CD 方案中,不同環境中的整合執行時 (IR) 類型必須相同。 例如,如果在開發環境中具有自託管 IR,則相同的 IR 還必須在其他環境中(如測試和生產)中採用自託管類型。 同樣,如果要跨多個階段共用集成運行時,則必須將所有環境(如開發、測試和生產)中的自託管連結配置集成運行時。

### <a name="get-secrets-from-azure-key-vault"></a>從 Azure 金鑰保存庫取得機密

如果有要在 Azure 資源管理器範本中傳遞的機密,我們建議您在 Azure 管道發布中使用 Azure 金鑰保管庫。

處理機密的方法有兩種:

1.  將祕密新增至參數檔案。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)。

    建立上載到發佈分支的參數檔的複本。 使用此格式設定要從金鑰保存的參數的值:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    當您使用此方法時，系統會自動從金鑰保存庫提取密碼。

    參數檔案也必須位於發行分支中。

1. 在上一個內式介紹的 Azure 資源管理員部署工作之前加入[Azure 金鑰保存式庫工作](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault):

    1.  在"**任務'** 選項卡上,創建新任務。 搜索**Azure 金鑰保管庫**並添加它。

    1.  在金鑰保管庫任務中,選擇創建密鑰保管庫的訂閱。 如有必要,提供憑據,然後選擇密鑰保管庫。

    ![新增金鑰保存庫工作](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>為 Azure Pipelines 代理程式授與權限

如果未設置正確的許可權,Azure 密鑰保管庫任務可能會失敗,並出現"訪問被拒絕"錯誤。 下載版本的日誌,並找到包含向 Azure 管道代理授予許可權的命令的 .ps1 檔。 您可以直接運行該命令。 或者,可以從檔案複製主體 ID,並在 Azure 門戶中手動添加訪問策略。 `Get`是`List`所需的最低許可權。

### <a name="update-active-triggers"></a>更新使用中的觸發程序

如果您嘗試更新使用中的觸發程序，部署可能會失敗。 要更新活動觸發器,您需要手動停止它們,然後在部署後重新啟動它們。 可以使用 Azure PowerShell 任務執行此操作:

1.  在版本的 **「工作」** 選項卡上,添加**Azure PowerShell**任務。 選擇工作版本 4。* 

1.  選擇工廠正在的訂閱。

1.  選擇**文稿檔路徑**作為腳本類型。 這要求您將 PowerShell 腳本儲存在儲存庫中。 以下 PowerShell 文稿可用於停止觸發器:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

您可以完成類似的步驟(使用`Start-AzDataFactoryV2Trigger`函數)在部署後重新啟動觸發器。

### <a name="sample-pre--and-post-deployment-script"></a>部署前與部署後文稿範例

以下示例腳本可用於在部署之前停止觸發器,並在部署後重新啟動觸發器。 此指令碼也包含可將已移除的資源刪除的程式碼。 將腳本保存在 Azure DevOps git 儲存庫中,並使用版本 4 通過 Azure PowerShell 任務引用它。

運行預部署文稿時,需要在**腳本參數**欄位中指定以下參數的變體。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


執行部署後文稿時,需要在 **「文本參數」** 欄位中指定以下參數的變體。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Azure 電源殼任務](media/continuous-integration-deployment/continuous-integration-image11.png)

下面是可用於部署前和部署後使用的腳本。 它考慮已刪除的資源和資源引用。

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>搭配使用自訂參數與 Resource Manager 範本

如果處於 GIT 模式,則可以覆蓋資源管理器範本中的預設屬性,以設置範本中參數化的屬性和硬編碼屬性。 在這些情況下,您可能希望重寫預設參數化樣本:

* 您使用自動 CI/CD,並希望在資源管理器部署期間更改某些屬性,但默認情況下,這些屬性不會參數化。
* 您的工廠非常大,預設資源管理器範本無效,因為它具有超過最大允許的參數 (256)。

在這些情況下,要覆蓋預設參數化範本,請將其指定為資料工廠 git 整合的根資料夾中的名為**arm-template-參數-定義.json**的檔。 您必須使用該確切的檔名。 數據工廠從 Azure 數據工廠門戶中當前位於哪個分支(而不僅僅是協作分支)讀取此檔。 可以從專用分支創建或編輯檔,通過選擇 UI 中的 **「匯出 ARM 範本**」來測試更改。 然後,您可以將文件合併到協作分支中。 如果未找到檔,則使用預設範本。

> [!NOTE]
> 自定義參數化範本不會更改 ARM 範本參數限制 256。 它允許您選擇和減少參數化屬性的數量。

### <a name="syntax-of-a-custom-parameters-file"></a>自訂參數檔案的語法

以下是創建自定義參數檔時應遵循的一些準則。 該檔由每個實體類型的部分組成:觸發器、管道、連結服務、數據集、集成運行時等。
* 輸入相關實體類型下的屬性路徑。
* 將屬性名稱設置為 `*`指示要參數化其下的所有屬性(僅下到第一個級別,而不是遞歸)。 您還可以為此配置提供異常。
* 將屬性的值設置為字串表示要對屬性進行參數化。 使用格式 `<action>:<name>:<stype>`。
   *  `<action>` 可以是以下字元之一:
      * `=` 表示將當前值保留為參數的預設值。
      * `-` 表示不保留參數的預設值。
      * `|` 是連接字串或密鑰的 Azure 密鑰保管庫中機密的特殊情況。
   * `<name>` 是參數的名稱。 如果為空,則獲取屬性的名稱。 如果值以字元開頭`-`,則名稱將縮短。 例如,`AzureStorage1_properties_typeProperties_connectionString`將縮短`AzureStorage1_connectionString`為 。
   * `<stype>` 是參數的類型。 如果 `<stype>` 為空,則預設類型為`string`。 支援的值: `string` `bool` `number` `object`、`securestring`、 和 。
* 在定義檔中指定數位表示樣本中的匹配屬性是陣列。 數據工廠通過使用在數位的整合式執行時物件中指定的定義對陣列中的所有物件進行遍舍。 第二個物件 (字串) 會變成屬性的名稱，以作為每個反覆項目參數的名稱。
* 定義不能特定於資源實例。 任何定義都適用於該類型的所有資源。
* 預設情況下,所有安全字串(如密鑰保管庫機密)和安全字串(如連接字串、鍵和令牌)均參數化。
 
### <a name="sample-parameterization-template"></a>範例參數化樣本

下面是參數化範本可能是什麼樣子的範例:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
以下是如何構造前面的範本的說明,按資源類型細分。

#### <a name="pipelines"></a>管線
    
* 路徑`activities/typeProperties/waitTimeInSeconds`中的任何屬性都進行了參數化。 管道中具有名稱為代碼`waitTimeInSeconds`級屬性的任何活動(例如`Wait`, 活動)都參數化為數位,並帶有預設名稱。 但它在資源管理器範本中沒有預設值。 它將是資源管理器部署期間的必填輸入。
* 同樣,名為`headers`(例如,`Web`在活動中)的屬性使用`object`類型 (JObject) 進行參數化。 它有一個預設值,該值與源工廠的值相同。

#### <a name="integrationruntimes"></a>整合時間

* 路徑`typeProperties`下的所有屬性都使用其各自的預設值進行參數化。 例如,類型屬性下`IntegrationRuntimes`有兩個屬性`computeProperties`:`ssisProperties`和 。 這兩種屬性類型都是使用各自的預設值和類型 (Object) 創建的。

#### <a name="triggers"></a>觸發程序

* 下`typeProperties`為 參數化了兩個屬性。 第一個是`maxConcurrency`,它指定為具有預設值,並且`string`類型為 。 它有預設參數名稱`<entityName>_properties_typeProperties_maxConcurrency`。
* 屬性`recurrence`也進行了參數化。 在它下,該級別上的所有屬性都指定為字串參數化,具有預設值和參數名稱。 屬性除外,該`interval`屬性參數化為`number`類型 。 參數名稱後置字`<entityName>_properties_typeProperties_recurrence_triggerSuffix`串與 。 同樣,該`freq`屬性是一個字串,並參數化為字串。 但是,`freq`該屬性在沒有預設值的情況下進行參數化。 名稱被縮短和後綴。 例如： `<entityName>_freq` 。

#### <a name="linkedservices"></a>連結服務

* 連結的服務是唯一的。 由於鏈接的服務和數據集類型範圍很廣,因此可以提供特定於類型的自定義。 在此示例中,對於類型`AzureDataLakeStore`的所有連結服務,將應用特定的範本。 對於所有其他(通過`*`),將應用不同的範本。
* 該`connectionString`屬性將參數化`securestring`為值。 它不會有預設值。 它將具有一個縮短的參數名稱,該參數名稱後綴為`connectionString`。
* 該屬性`secretAccessKey`恰好`AzureKeyVaultSecret`是 a(例如,在 Amazon S3 連結服務中)。 它會自動參數化為 Azure 密鑰保管庫密鑰,並從配置的密鑰保管庫提取。 您還可以對密鑰保管庫本身進行參數化。

#### <a name="datasets"></a>資料集

* 儘管特定於類型的自定義可用於數據集,但您可以提供配置,而無需顯式配置\*-level 配置。 在前面的示例中,下`typeProperties`的所有數據集屬性都進行了參數化。

### <a name="default-parameterization-template"></a>預設參數化樣本

下面是當前預設參數化範本。 如果只需要添加幾個參數,則直接編輯此範本可能是個好主意,因為不會丟失現有的參數化結構。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

下面的範例展示如何向預設參數化範本添加單個值。 我們只想為資料磚塊連結服務添加到參數檔的現有 Azure 資料磚塊互動式群集 ID。 請注意,此檔與上一個檔相同,除了`existingClusterId``Microsoft.DataFactory/factories/linkedServices`在的屬性欄位下添加。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>連結的 Resource Manager 範本

如果已為資料工廠設置 CI/CD,則隨著工廠規模越來越大,可能會超出 Azure 資源管理器範本限制。 例如,一個限制是資源管理器範本中的最大資源數。 為了容納大型工廠,同時為工廠生成完整的資源管理器範本,數據工廠現在生成鏈接的資源管理器範本。 使用此功能,整個工廠負載將分解為多個文件,這樣您就不會受到限制的限制。

若設定了 Git,則連結樣本將產生並儲存與adf_publish分支中的完整資源管理器樣本一起儲存在稱為連結樣本的新資料夾中:

![連結的 Resource Manager 範本資料夾](media/continuous-integration-deployment/linked-resource-manager-templates.png)

連結的資源管理器範本通常由主範本和一組連結到主範本的子範本組成。 父範本稱為 ArmTemplate_master.json,子範本使用模式 ArmTemplate_0.json、ArmTemplate_1.json 等命名。 

要使用連結範本而不是完整的資源管理器範本,請更新 CI/CD 任務以指向 ArmTemplate_master.json 而不是 ArmTemplateForFactory.json(完整的資源管理器範本)。 資源管理員還要求將連結的範本上傳到存儲帳戶中,以便 Azure 可以在部署期間訪問它們。 有關詳細資訊,請參閱使用[VSTS 部署連結的資源管理員樣本](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

請記得在在部署工作之前和之後，於 CI/CD 管線中新增 Data Factory 指令碼。

如果未配置 Git,則可以透過**ARM 範本**清單中**的匯出 ARM 範本**存取連結範本。

## <a name="hotfix-production-branch"></a>修補程式生產分支

如果將工廠部署到生產部門,並意識到存在需要馬上修復的 Bug,但無法部署當前協作分支,則可能需要部署修補程式。 此方法稱為快速修復工程或 QFE。

1.    在 Azure DevOps 中,轉到部署到生產中的版本。 查找部署的最後一個提交。

2.    從提交消息中獲取協作分支的提交 ID。

3.    從該提交創建新的修補程式分支。

4.    轉到 Azure 資料工廠 UX 並切換到修補程式分支。

5.    通過使用 Azure 數據工廠 UX,修復 Bug。 測試更改。

6.    驗證修復程式後,選擇 **「匯出 ARM 範本**」以獲取修補程式資源管理器範本。

7.    手動將此生成簽入adf_publish分支。

8.    如果已將發佈管道配置為根據adf_publish簽入自動觸發,則新版本將自動啟動。 否則,手動對發佈進行排隊。

9.    將修補程式版本部署到測試和生產工廠。 此版本包含以前的生產負載以及您在步驟 5 中所做的修復。

10.    將修補程式中的更改添加到開發分支,以便以後的版本不會包含相同的 Bug。

## <a name="best-practices-for-cicd"></a>CI/CD 的最佳做法

如果您使用 Git 整合資料工廠,並且具有將更改從開發移動到測試,然後移動到生產中的 CI/CD 管道,我們建議採用以下最佳做法:

-   **Git 整合** 您只需使用 Git 整合配置開發資料工廠。 測試和生產更改通過 CI/CD 部署,不需要 Git 整合。

-   **Data Factory CI/CD 指令碼**。 在 CI/CD 中的資源管理員部署步驟之前,您需要完成某些工作,例如停止和重新啟動觸發器以及執行清理。 我們建議您在部署前後使用 PowerShell 腳本。 有關詳細資訊,請參閱[更新活動觸發器](#update-active-triggers)。

-   **整合執行時與共享**。 整合執行時不會經常更改,並且在 CI/CD 中的所有階段都類似。 因此,資料工廠希望您在 CI/CD 的所有階段具有相同的名稱和類型的整合執行時。 如果要跨所有階段共用集成運行時,請考慮使用三元工廠來包含共用集成運行時。 您可以在所有環境中使用此共享工廠作為連結整合式執行時類型。

-   **金鑰保存函式庫**。 使用基於 Azure 密鑰保管庫的鏈接服務時,可以通過為不同的環境保留單獨的密鑰保管庫來進一步利用這些服務。 您還可以為每個金鑰保管庫配置單獨的許可權等級。 例如,您可能不希望團隊成員具有生產機密的許可權。 如果您遵循此方法,我們建議您在所有階段都保留相同的機密名稱。 如果保留相同的名稱,則不必跨 CI/CD 環境更改資源管理器範本,因為唯一更改的是密鑰保管庫名稱,這是資源管理器範本參數之一。

## <a name="unsupported-features"></a>不支援的功能

- 根據設計,數據工廠不允許精心挑選提交或選擇性地發佈資源。 發佈將包括在數據工廠中所做的所有更改。

    - 數據工廠實體相互依賴。 例如,觸發器依賴於管道,管道依賴於數據集和其他管道。 選擇性地發佈資源子集可能會導致意外的行為和錯誤。
    - 在需要選擇性發佈時,請考慮使用修補程式。 有關詳細資訊,請參閱[修補程式生產分支](#hotfix-production-branch)。

-   您無法從私人分支發佈。

-   當前無法在 Bitbucket 上承載專案。
