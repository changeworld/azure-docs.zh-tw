---
title: Azure Data Factory 中的持續整合與傳遞
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
ms.openlocfilehash: dc0da82447b5df0735b16f46298a2f473ee61ea0
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371370"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory 中的持續整合與傳遞

## <a name="overview"></a>概觀

持續整合是一種作法，可以自動且儘早地測試對程式碼基底所做的每項變更。 持續傳遞會遵循持續整合期間所發生的測試，並將變更推送至暫存或生產系統。

在 Azure Data Factory 中，持續整合和傳遞（CI/CD）表示將 Data Factory 管線從一個環境（開發、測試、生產）移至另一個環境。 您可以搭配 Azure Resource Manager 範本使用 Data Factory UX 整合來執行 CI/CD。

在 Data Factory UX 中，您可以從 [ **ARM 範本**] 下拉式功能表產生 Resource Manager 範本。 當您選取 [**匯出 ARM 範本**] 時，入口網站會為 data factory 產生 Resource Manager 範本，以及包含所有連接字串和其他參數的設定檔。 接著，您會為每個環境（開發、測試、生產）建立一個設定檔。 所有環境的主要 Resource Manager 範本檔案會保持相同。

如需這項功能的9分鐘簡介和示範，請觀看這段影片：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD 生命週期

以下是使用 Azure Repos Git 設定之 Azure data factory 中 CI/CD 生命週期的範例總覽。 如需如何設定 Git 儲存機制的詳細資訊，請參閱[Azure Data Factory 中的原始檔控制](source-control.md)。

1.  使用 Azure Repos Git 建立和設定開發資料處理站。 所有開發人員都應該擁有撰寫 Data Factory 資源（例如管線和資料集）的許可權。

1.  當開發人員在其功能分支中進行變更時，他們會以最新的變更來進行管線執行的調試。 如需如何對管線執行進行偵錯工具的詳細資訊，請參閱[使用 Azure Data Factory 的反復式開發和調試](iterative-development-debugging.md)。

1.  當開發人員對其變更感到滿意之後，他們會從其功能分支建立提取要求至主要或共同作業分支，以取得對等的已審核變更。

1.  在提取要求經過核准並在主要分支中合併變更後，變更就可以發行到開發 factory。

1.  當小組準備好要將變更部署到測試處理站，然後再部署到生產工廠時，小組會從主要分支匯出 Resource Manager 範本。

1.  匯出的 Resource Manager 範本會以不同的參數檔案部署到測試處理站和生產工廠。

## <a name="create-a-resource-manager-template-for-each-environment"></a>為每個環境建立 Resource Manager 範本

1. 在 [ **ARM 範本**] 清單中，選取 [**匯出 ARM 範本**]，以在開發環境中匯出 data factory 的 Resource Manager 範本。

   ![匯出 Resource Manager 範本](media/continuous-integration-deployment/continuous-integration-image1.png)

1. 在您的測試和生產資料處理站中，選取 [匯**入 ARM 範本**]。 此動作會將您移至 Azure 入口網站，您可以在此處匯入先前匯出的範本。 **在編輯器中選取 [建立您自己的範本**]，以開啟 [Resource Manager 範本編輯器]。

   ![建立您自己的範本](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. 選取 [**載入**檔案]，然後選取 [產生的 Resource Manager] 範本。 這是在步驟1匯出的 .zip 檔案中的**arm_template json**檔案。

   ![編輯範本](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 在 [設定] 區段中，輸入設定值，例如 [已連結的服務認證]。 當您完成時，請選取 [**購買**] 以部署 Resource Manager 範本。

   ![設定區段](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>連接字串

如需如何設定連接字串的相關資訊，請參閱連接器的文章。 例如，針對 Azure SQL Database，請參閱[使用 Azure Data Factory 將資料複製到 Azure SQL Database 或從該處複製資料](connector-azure-sql-database.md)。 若要驗證連接字串，您可以在 Data Factory UX 中開啟資源的程式碼視圖。 在程式碼視圖中，會移除連接字串的密碼或帳戶金鑰部分。 若要開啟程式碼視圖，請選取此處反白顯示的圖示：

![開啟程式碼視圖以查看連接字串](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>使用 Azure Pipelines 版本自動化持續整合

以下是設定 Azure Pipelines 版本的指南，可將資料處理站的部署自動化到多個環境。

![透過 Azure Pipelines 進行持續整合的圖表](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>需求

-   連結至使用 [Azure Resource Manager 服務端點](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)Visual Studio Team Foundation Server 或 Azure Repos 的 Azure 訂用帳戶。

-   以 Azure Repos Git 整合設定的 data factory。

-    [Azure 金鑰保存庫](https://azure.microsoft.com/services/key-vault/)，其中包含每個環境的密碼。

### <a name="set-up-an-azure-pipelines-release"></a>設定 Azure Pipelines 發行

1.  在[Azure DevOps](https://dev.azure.com/)中，開啟使用您的 data factory 設定的專案。

1.  在頁面左側選取 [**管線**]，然後選取 [**發行**]。

    ![選取管線，版本](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  選取 [**新增管線**]，或者，如果您有現有的管線，請選取 [**新增**]，然後選取 [**新增發行管線**]。

1.  選取 [**空白作業**] 範本。

    ![選取空白作業](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  在 [**階段名稱**] 方塊中，輸入您的環境名稱。

1.  選取 [**新增**成品]，然後選取使用您的 data factory 設定的存放庫。 針對**預設分支**選取 [ **adf_publish** ]。 針對**預設版本**，從 [**預設分支**] 選取 [最新]。

    ![新增構件](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  新增 Azure Resource Manager 部署工作：

    a.  在 [階段] 視圖中，選取 [ **view stage tasks**]。

    ![階段視圖](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  建立新的工作。 搜尋 [ **Azure 資源群組部署**]，然後選取 [**新增**]。

    c.  在 [部署] 工作中，選取 [訂用帳戶]、[資源群組] 和 [目標 data factory 的位置]。 視需要提供認證。

    d.  在 [**動作**] 清單中，選取 [**建立或更新資源群組**]。

    e.  選取 [**範本**] 方塊旁的省略號按鈕（ **...** ）。 在本文的為[每個環境建立 Resource Manager 範本](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)一節中，流覽您使用匯**入 ARM 範本**建立的 Azure Resource Manager 範本。 在 adf_publish 分支的 [<FactoryName>] 資料夾中尋找此檔案。

    f.  選取 **…** 在 [**範本參數**] 方塊旁，選擇參數檔案。 您選擇的檔案將取決於您建立的是複本，還是使用預設的 ARMTemplateParametersForFactory 檔案。

    g.  選取 **…** 在 [覆**寫範本參數**] 方塊旁，輸入目標 data factory 的資訊。 針對來自 Azure Key Vault 的認證，請在雙引號之間輸入密碼的名稱。 例如，如果密碼的名稱是 cred1，請輸入 **"$ （cred1）"** 作為此值。

    h. 針對**部署模式**選取 [**增量**]。

    > [!WARNING]
    > 如果您針對**部署模式**選取 [**完成**]，可能會刪除現有的資源，包括未在 [Resource Manager] 範本中定義的目標資源群組中的所有資源。

    ![Data Factory 的生產部署](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  儲存發行管線。

1. 若要觸發發行，請選取 [**建立發行**]。

   ![選取 [建立發行]](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> 在 CI/CD 案例中，不同環境中的整合執行時間（IR）類型必須相同。 例如，如果您在開發環境中有自我裝載的 IR，相同的 IR 也必須屬於其他環境中的自我裝載類型，例如測試和生產。 同樣地，如果您要跨多個階段共用整合執行時間，則必須在所有環境中將整合執行時間設定為連結的自我裝載，例如開發、測試和生產。

### <a name="get-secrets-from-azure-key-vault"></a>從 Azure Key Vault 取得秘密

如果您有要傳入 Azure Resource Manager 範本的密碼，建議您在 Azure Pipelines 版本中使用 Azure Key Vault。

有兩種方式可處理密碼：

1.  將祕密新增至參數檔案。 如需詳細資訊，請參閱[在部署期間使用 Azure Key Vault 以傳遞安全的參數值](../azure-resource-manager/templates/key-vault-parameter.md)。

    建立要上傳至發佈分支之參數檔案的複本。 使用下列格式，設定您想要從 Key Vault 取得的參數值：

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

1. 在上一節所述的 Azure Resource Manager 部署工作之前，新增[Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)工作：

    1.  在 [工作] 索引標籤上，建立新**的工作。** 搜尋**Azure Key Vault**並加以新增。

    1.  在 [Key Vault] 工作中，選取您在其中建立金鑰保存庫的訂用帳戶。 視需要提供認證，然後選取金鑰保存庫。

    ![新增 Key Vault 工作](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>為 Azure Pipelines 代理程式授與權限

如果未設定正確的許可權，Azure Key Vault 工作可能會失敗並出現「拒絕存取」錯誤。 下載版本的記錄檔，並找出包含命令的 ps1 檔案，以授與 Azure Pipelines 代理程式的許可權。 您可以直接執行命令。 或者，您可以從檔案複製主體識別碼，然後在 Azure 入口網站中手動新增存取原則。 `Get` 和 `List` 是所需的最小許可權。

### <a name="update-active-triggers"></a>更新使用中的觸發程序

如果您嘗試更新使用中的觸發程序，部署可能會失敗。 若要更新作用中的觸發程式，您需要手動停止它們，然後在部署之後重新開機它們。 您可以使用 Azure PowerShell 工作來執行這項作業：

1.  在發行**的 [工作]** 索引標籤上，加入**Azure PowerShell**工作。 選擇 [工作版本 4. *]。 

1.  選取您的 factory 所在的訂用帳戶。

1.  選取 [**指令檔路徑**] 作為腳本類型。 這會要求您將 PowerShell 腳本儲存在存放庫中。 下列 PowerShell 腳本可以用來停止觸發程式：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

您可以完成類似的步驟（使用 `Start-AzDataFactoryV2Trigger` 函式），以在部署之後重新開機觸發程式。

### <a name="sample-pre--and-post-deployment-script"></a>範例部署前和部署後腳本

下列範例腳本可用來在部署之前停止觸發程式，並于之後重新開機。 此指令碼也包含可將已移除的資源刪除的程式碼。 將腳本儲存在 Azure DevOps git 存放庫中，並透過使用第4版的 Azure PowerShell 工作加以參考。

執行預先部署腳本時，您必須在 [**腳本引數**] 欄位中指定下列參數的變化。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


執行部署後腳本時，您必須在 [**腳本引數**] 欄位中指定下列參數的變化。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

    ![Azure PowerShell task](media/continuous-integration-deployment/continuous-integration-image11.png)

以下是可用於預先部署和部署後的腳本。 其適用于已刪除資源和資源參考的帳戶。

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
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
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
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>搭配使用自訂參數與 Resource Manager 範本

如果您是在 GIT 模式中，您可以覆寫 Resource Manager 範本中的預設屬性，以設定範本中參數化的屬性，以及硬式編碼的屬性。 在這些情況下，您可能會想要覆寫預設參數化範本：

* 您會使用自動化的 CI/CD，而您想要在 Resource Manager 部署期間變更某些屬性，但預設不會將屬性參數化。
* 因為預設 Resource Manager 範本的數目超過允許的最大值（256），所以您的 factory 很大。

在這些情況下，若要覆寫預設參數化範本，請在指定為 data factory git 整合根資料夾的資料夾中，建立名為**arm-template-parameters-definition**的檔案。 您必須使用該正確的檔案名。 Data Factory 從您目前在 Azure Data Factory 入口網站中的任何分支讀取此檔案，而不只是從共同作業分支。 您可以從私人分支建立或編輯該檔案，您可以在 UI 中選取 [**匯出 ARM 範本**] 來測試變更。 接著，您可以將檔案合併到共同作業分支。 如果找不到任何檔案，則會使用預設範本。

> [!NOTE]
> 自訂參數化範本不會變更 ARM 範本參數限制256。 它可讓您選擇並減少參數化屬性的數目。

### <a name="syntax-of-a-custom-parameters-file"></a>自訂參數檔案的語法

以下是當您建立自訂參數檔案時所要遵循的一些指導方針。 檔案是由每個實體類型的區段所組成：觸發程式、管線、連結服務、資料集、整合執行時間等等。
* 在相關實體類型下輸入屬性路徑。
* 將屬性名稱設定為 `*` 表示您想要參數化其下的所有屬性（僅限向下到第一個層級，而非以遞迴方式）。 您也可以提供此設定的例外狀況。
* 將屬性的值設定為字串，表示您想要將屬性參數化。 請使用 `<action>:<name>:<stype>`的格式。
   *  `<action>` 可以是下列其中一個字元：
      * `=` 表示保留目前的值做為參數的預設值。
      * `-` 表示不保留參數的預設值。
      * `|` 是針對連接字串或金鑰 Azure Key Vault 的秘密特殊案例。
   * `<name>` 是參數的名稱。 如果是空的，則會接受屬性的名稱。 如果值是以 `-` 字元開頭，則名稱會縮短。 例如，`AzureStorage1_properties_typeProperties_connectionString` 會縮短為 `AzureStorage1_connectionString`。
   * `<stype>` 是參數的類型。 如果 `<stype>` 為空白，則預設類型為 [`string`]。 支援的值： `string`、`bool`、`number`、`object`和 `securestring`。
* 在定義檔中指定陣列，表示範本中的相符屬性是陣列。 Data Factory 會使用陣列的 integration runtime 物件中指定的定義，逐一查看陣列中的所有物件。 第二個物件 (字串) 會變成屬性的名稱，以作為每個反覆項目參數的名稱。
* 定義不能是資源實例特有的。 任何定義都會套用至該類型的所有資源。
* 根據預設，所有安全字串（例如 Key Vault 秘密）和安全字串（例如連接字串、金鑰和權杖）都會參數化。
 
### <a name="sample-parameterization-template"></a>範例參數化範本

以下是參數化範本可能外觀的範例：

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
以下說明如何建立前述範本，並依資源類型細分。

#### <a name="pipelines"></a>管線
    
* 路徑 `activities/typeProperties/waitTimeInSeconds` 中的任何屬性都已參數化。 管線中具有名為 `waitTimeInSeconds` 之程式碼層級屬性的任何活動（例如，`Wait` 活動）都會參數化為數位，且預設名稱為。 但它在 Resource Manager 範本中沒有預設值。 在 Resource Manager 部署期間，這會是必要的輸入。
* 同樣地，名為 `headers` 的屬性（例如，在 `Web` 活動中）會以類型 `object` （JObject）進行參數化。 它具有預設值，這與來源 factory 的值相同。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* 路徑 `typeProperties` 下的所有屬性都會使用其各自的預設值進行參數化。 例如，`IntegrationRuntimes` 類型屬性底下有兩個屬性： `computeProperties` 和 `ssisProperties`。 這兩個屬性類型都會使用其各自的預設值和類型（物件）來建立。

#### <a name="triggers"></a>觸發程序

* 在 `typeProperties`底下，會將兩個屬性參數化。 第一個是 `maxConcurrency`，其指定為具有預設值，且的類型為`string`。 它具有 `<entityName>_properties_typeProperties_maxConcurrency`的預設參數名稱。
* `recurrence` 屬性也已參數化。 在其底下，會指定該層級的所有屬性，以預設值和參數名稱參數化為字串。 例外狀況是 `interval` 屬性，它會參數化為 `number`類型。 參數名稱後面會加上 `<entityName>_properties_typeProperties_recurrence_triggerSuffix`。 同樣地，`freq` 屬性是字串，而且會參數化為字串。 不過，`freq` 屬性會在沒有預設值的情況下參數化。 名稱會縮短並加上尾碼。 例如： `<entityName>_freq` 。

#### <a name="linkedservices"></a>Linkedservices.json 和 datasets.json

* 連結服務是唯一的。 因為已連結的服務和資料集有範圍廣泛的類型，所以您可以提供特定類型的自訂。 在此範例中，針對 `AzureDataLakeStore`類型的所有連結服務，將會套用特定的範本。 對於所有其他人（透過 `*`），將會套用不同的範本。
* `connectionString` 屬性將會參數化為 `securestring` 值。 它不會有預設值。 它會有一個加上尾碼為 `connectionString`的簡短參數名稱。
* `secretAccessKey` 的屬性會是 `AzureKeyVaultSecret` （例如，在 Amazon S3 連結服務中）。 它會自動參數化為 Azure Key Vault 秘密，並從已設定的金鑰保存庫提取。 您也可以將金鑰保存庫本身參數化。

#### <a name="datasets"></a>資料集

* 雖然特定類型的自訂適用于資料集，但您可以提供設定，而不需要明確擁有 \*層級的設定。 在上述範例中，會將 `typeProperties` 下的所有資料集屬性參數化。

### <a name="default-parameterization-template"></a>預設參數化範本

以下是目前的預設參數化範本。 如果您只需要新增幾個參數，則直接編輯此範本可能是很好的主意，因為您將不會遺失現有的參數化結構。

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

下列範例顯示如何將單一值新增至預設參數化範本。 我們只想要將 Databricks 連結服務的現有 Azure Databricks 互動式叢集識別碼新增到參數檔案。 請注意，這個檔案與上一個檔案相同，除了在 `Microsoft.DataFactory/factories/linkedServices`的 [屬性] 欄位下新增 `existingClusterId` 以外。

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

如果您已為資料處理站設定 CI/CD，您可能會超過您的工廠成長時的 Azure Resource Manager 範本限制。 例如，其中一個限制是 Resource Manager 範本中的資源數目上限。 為了在產生 factory 的完整 Resource Manager 範本時容納大型工廠，Data Factory 現在會產生連結的 Resource Manager 範本。 透過這項功能，整個 factory 承載會分成數個檔案，因此您不受限於限制。

如果您已設定 Git，則會產生連結的範本，並在名為 linkedTemplates 的新資料夾中，連同 adf_publish 分支中的完整 Resource Manager 範本一起儲存：

![連結的 Resource Manager 範本資料夾](media/continuous-integration-deployment/linked-resource-manager-templates.png)

連結的 Resource Manager 範本通常包含主要範本，以及連結至主要的一組子範本。 父範本稱為 ArmTemplate_master. json，而子範本會使用 ArmTemplate_0. json、ArmTemplate_1 json 等模式來命名。 

若要使用連結的範本，而不是完整的 Resource Manager 範本，請更新您的 CI/CD 工作，使其指向 ArmTemplate_master 的 json，而不是 ArmTemplateForFactory （完整 Resource Manager 的範本）。 Resource Manager 也會要求您將連結的範本上傳至儲存體帳戶，讓 Azure 在部署期間可以存取它們。 如需詳細資訊，請參閱[使用 VSTS 部署連結的 Resource Manager 範本](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)。

請記得在在部署工作之前和之後，於 CI/CD 管線中新增 Data Factory 指令碼。

如果您未設定 Git，您可以透過**Arm 範本**清單中的 [**匯出 arm] 範本**來存取連結的範本。

## <a name="hotfix-production-branch"></a>修補程式生產分支

如果您將工廠部署到生產環境，併發現需要立即修正的 bug，但是您無法部署目前的共同作業分支，則可能需要部署一個修補程式。 這種方法稱為快速修正程式設計或 QFE。

1.  在 Azure DevOps 中，移至已部署至生產環境的版本。 尋找已部署的最後一個認可。

2.  從認可訊息中，取得共同作業分支的認可識別碼。

3.  從該認可建立新的「修補程式」分支。

4.  移至 Azure Data Factory UX 並切換至 [修補程式] 分支。

5.  藉由使用 Azure Data Factory UX，修正 bug。 測試您的變更。

6.  確認修正之後，請選取 [**匯出 ARM 範本**]，以取得 Resource Manager 範本的 [此修補程式]。

7.  以手動方式將此組建簽入 adf_publish 分支。

8.  如果您已設定發行管線根據 adf_publish 簽入自動觸發，則會自動啟動新的版本。 否則，請手動將發行排到佇列。

9.  將此修補程式版本部署到測試和生產工廠。 此版本包含先前的生產環境承載，以及您在步驟5中所做的修正。

10. 將修正程式中的變更新增至開發分支，讓之後的版本不會包含相同的 bug。

## <a name="best-practices-for-cicd"></a>CI/CD 的最佳做法

如果您使用與資料處理站的 Git 整合，而且具有 CI/CD 管線，可將您的變更從開發移至測試，再到生產環境，我們建議您採用下列最佳作法：

-   **Git 整合**。 您只需要使用 Git 整合來設定您的開發資料處理站。 測試和生產環境的變更會透過 CI/CD 部署，而不需要 Git 整合。

-   **Data Factory CI/CD 指令碼**。 在 CI/CD 的 Resource Manager 部署步驟之前，您必須先完成某些工作，例如停止和重新開機觸發程式，並執行清除作業。 我們建議您在部署前後使用 PowerShell 腳本。 如需詳細資訊，請參閱更新作用中的[觸發](#update-active-triggers)程式。

-   **整合執行時間與共享**。 整合執行時間不會經常變更，而且在 CI/CD 中的所有階段都很類似。 因此 Data Factory 要求您在 CI/CD 的所有階段都擁有相同名稱和類型的整合執行時間。 如果您想要跨所有階段共用整合執行時間，請考慮使用三元處理站，只包含共用的整合執行時間。 您可以在所有環境中使用此共用 factory 作為連結的整合執行時間類型。

-   **Key Vault**。 當您使用以 Azure Key Vault 為基礎的連結服務時，您可以為不同的環境保留個別的金鑰保存庫，以進一步利用它們。 您也可以為每個金鑰保存庫設定不同的許可權等級。 例如，您可能不希望小組成員擁有生產密碼的許可權。 如果您遵循此方法，我們建議您在所有階段保留相同的密碼名稱。 如果您保留相同的名稱，則不需要變更跨 CI/CD 環境的 Resource Manager 範本，因為唯一變更的是金鑰保存庫名稱，也就是其中一個 Resource Manager 範本參數。

## <a name="unsupported-features"></a>不支援的功能

- 根據設計，Data Factory 不允許對認可或選擇性發佈資源進行揀選選擇。 發行將會包含在 data factory 中進行的所有變更。

    - Data factory 實體相依于彼此。 例如，觸發程式取決於管線，而管線則相依于資料集和其他管線。 選擇性發佈資源子集可能會導致未預期的行為和錯誤。
    - 在罕見的情況下，當您需要選擇性發行時，請考慮使用「修補程式」。 如需詳細資訊，請參閱[修補生產分支](#hotfix-production-branch)。

-   您無法從私人分支發佈。

-   您目前無法在 Bitbucket 上裝載專案。
