---
title: 使用 Azure Data Factory 在 Blob 儲存體中複製資料
description: 使用 PowerShell 建立 Azure 資料處理站，以將資料從 Azure Blob 儲存體中的某個位置複製到另一個位置。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 04/10/2020
ms.author: jingwang
ms.openlocfilehash: 1377743fbaefdb812f18768307421fdae637ed54
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637576"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>快速入門：使用 PowerShell 建立 Azure 資料處理站

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [目前的版本](quickstart-create-data-factory-powershell.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本快速入門說明如何使用 PowerShell 來建立 Azure 資料處理站。 在此資料處理站中建立的管線會將資料從 Azure Blob 儲存體中的一個資料夾 **複製** 到其他資料夾。 如需如何使用 Azure Data Factory **轉換** 資料的教學課程，請參閱 [教學課程︰使用 Spark 轉換資料](transform-data-using-spark.md)。

> [!NOTE]
> 本文不提供 Data Factory 服務的詳細簡介。 如需 Azure Data Factory 服務簡介，請參閱 [Azure Data Factory 簡介](introduction.md)。

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

依照[如何安裝和設定 Azure PowerShell](/powershell/azure/install-Az-ps)中的指示，安裝最新的 Azure PowerShell 模組。

#### <a name="log-in-to-powershell"></a>登入 PowerShell

1. 在您的電腦上啟動 **PowerShell** 。 讓 PowerShell 保持開啟，直到本快速入門結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。

2. 執行下列命令，並輸入您用來登入 Azure 入口網站的相同 Azure 使用者名稱和密碼：

    ```powershell
    Connect-AzAccount
    ```

3. 執行下列命令以檢視此帳戶的所有訂用帳戶：

    ```powershell
    Get-AzSubscription
    ```

4. 如果您發現您的帳戶與多個訂用帳戶相關聯，請執行下列命令來選取您需要使用的訂用帳戶。 以您的 Azure 訂用帳戶識別碼取代 **SubscriptionId** ：

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>建立 Data Factory

1. 定義資源群組名稱的變數，以便稍後在 PowerShell 命令中使用。 將下列命令文字複製到 PowerShell，以雙引號指定 [Azure 資源群組](../azure-resource-manager/management/overview.md)的名稱，然後執行命令。 例如： `"ADFQuickStartRG"` 。

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    如果資源群組已經存在，您可能不想覆寫它。 將不同的值指派給 `$ResourceGroupName` 變數，然後執行一次命令

2. 若要建立 Azure 資源群組，請執行下列命令：

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    如果資源群組已經存在，您可能不想覆寫它。 將不同的值指派給 `$ResourceGroupName` 變數，然後執行一次命令。

3. 定義 Data Factory 名稱的變數。 

    > [!IMPORTANT]
    >  將資料處理站名稱更新為全域唯一的。 例如，ADFTutorialFactorySP1127。

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. 若要建立 Data Factory，請從 $ResGrp 變數使用 Location 和 ResourceGroupName 屬性來執行下列 **Set-AzDataFactoryV2** Cmdlet：

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

請注意下列幾點：

* Azure Data Factory 的名稱在全域必須是唯一的。 如果發生下列錯誤，請變更名稱，並再試一次。

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* 若要建立 Data Factory 執行個體，您用來登入 Azure 的使用者帳戶必須為 **參與者** 或 **擁有者** 角色，或是 Azure 訂用帳戶的 **管理員** 。

* 如需目前可使用 Data Factory 的 Azure 區域清單，請在下列頁面上選取您感興趣的區域，然後展開 [分析]  以找出 [Data Factory]  ：[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。


## <a name="create-a-linked-service"></a>建立連結的服務

在資料處理站中建立的連結服務，會將您的資料存放區和計算服務連結到資料處理站。 在本快速入門中，您要建立連結 Azure 儲存體的服務，可用來作為來源和接收的存放區。 連結的服務具有連線資訊，可供 Data Factory 服務在執行階段中用來連線。

>[!TIP]
>在本快速入門中，您會使用「帳戶金鑰」  作為資料存放區的驗證類型，但可以選擇其他支援的驗證方法：SAS URI  、服務主體  ，以及受控識別  (如有需要)。 如需詳細資訊，請參閱[本文](./connector-azure-blob-storage.md#linked-service-properties)的對應章節。
>若要安全地儲存資料存放區的秘密，也建議您使用 Azure Key Vault。 如需詳細說明，請參閱[本文](./store-credentials-in-key-vault.md)。

1. 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用以下內容建立名為 **AzureStorageLinkedService.json** 的 JSON 檔案：(如果 ADFv2QuickStartPSH 資料夾尚未存在，請加以建立)。

    > [!IMPORTANT]
    > 儲存檔案前，以 Azure 儲存體帳戶的名稱和金鑰取代 &lt;accountName&gt; 和 &lt;accountKey&gt;。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
    ```

    如果使用 [記事本]，請在 [另存新檔]  對話方塊的 [存檔類型]  欄位中選取 [所有檔案]  。 否則，它可能會將 `.txt` 副檔名新增至檔案。 例如： `AzureStorageLinkedService.json.txt` 。 如果您在 [檔案總管] 中建立檔案，然後在 [記事本] 中開啟該檔案，您可能不會看到 `.txt` 副檔名，因為預設已設定 [隱藏已知檔案類型的副檔名]  選項。 先移除 `.txt` 副檔名，再繼續下一個步驟。

2. 在 **PowerShell** 中，切換到 **ADFv2QuickStartPSH** 資料夾。

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. 執行 **Set-AzDataFactoryV2LinkedService** Cmdlet 來建立連結服務： **AzureStorageLinkedService** 。

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    以下是範例輸出：

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

## <a name="create-datasets"></a>建立資料集

在此程序中，您會建立兩個資料集： **InputDataset** 和 **OutputDataset** 。 這些資料集的類型為 **二進位** 。 其會參考您在前一節中建立的 Azure 儲存體連結服務。
輸入資料集代表輸入資料夾中的來源資料。 在輸入資料集定義中，您可以指定 Blob 容器 ( **adftutorial** )、資料夾 ( **input** ) 以及包含來源資料的檔案 ( **emp.txt** )。
此輸出資料集代表已複製到目的地的資料。 在輸出資料集定義中，您可以指定要將資料複製過去的 Blob 容器 ( **adftutorial** )、資料夾 ( **output** ) 和檔案。 
1. 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用下列內容建立名為 **InputDataset.json** 的 JSON 檔案：

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "fileName": "emp.txt",
                    "folderPath": "input",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

2. 若要建立資料集： **InputDataset** ，請執行 **Set-AzDataFactoryV2Dataset** Cmdlet。

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "InputDataset" `
        -DefinitionFile ".\InputDataset.json"
    ```

    以下是範例輸出：

    ```console
    DatasetName       : InputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```

3. 重複前述步驟，以建立輸出資料集。 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用下列內容建立名為 **OutputDataset.json** 的 JSON 檔案：

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "folderPath": "output",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

4. 執行 **Set-AzDataFactoryV2Dataset** Cmdlet 來建立 **OutDataset** 。

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "OutputDataset" `
        -DefinitionFile ".\OutputDataset.json"
    ```

    以下是範例輸出：

    ```console
    DatasetName       : OutputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```
## <a name="create-a-pipeline"></a>建立管線

在此程序中，您會建立管線，其中含有使用輸入和輸出資料集的複製活動。 複製活動會將資料從您在輸入資料集設定中指定的檔案，複製到您在輸出資料集設定中指定的檔案。  

1. 在 **C:\ADFv2QuickStartPSH** 資料夾中，使用下列內容建立名為 **Adfv2QuickStartPipeline.json** 的 JSON 檔案：

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 30,
                        "secureOutput": false,
                        "secureInput": false
                    },
                    "userProperties": [],
                    "typeProperties": {
                        "source": {
                            "type": "BinarySource",
                            "storeSettings": {
                                "type": "AzureBlobStorageReadSettings",
                                "recursive": true
                            }
                        },
                        "sink": {
                            "type": "BinarySink",
                            "storeSettings": {
                                "type": "AzureBlobStorageWriteSettings"
                            }
                        },
                        "enableStaging": false
                    },
                    "inputs": [
                        {
                            "referenceName": "InputDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ],
            "annotations": []
        }
    }
    ```

2. 若要建立管線： **Adfv2QuickStartPipeline** ，請執行 **Set-AzDataFactoryV2Pipeline** Cmdlet。

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>建立管線執行

在此步驟中，您會建立管線執行。

執行 **Invoke-AzDataFactoryV2Pipeline** Cmdlet 來建立管線執行。 Cmdlet 會傳回管線執行識別碼，方便後續監視。

  ```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline `
    -DataFactoryName $DataFactory.DataFactoryName `
    -ResourceGroupName $ResGrp.ResourceGroupName `
    -PipelineName $DFPipeLine.Name 
```

## <a name="monitor-the-pipeline-run"></a>監視管道執行

1. 執行下列 PowerShell 程式碼以持續檢查管線執行狀態，直到完成複製資料為止。 在 PowerShell 視窗中複製/貼上下列指令碼，然後按 ENTER。

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }
    ```

    以下是管線執行的範例輸出：

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    RunId             : 00000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 8/27/2019 7:23:07 AM
    Parameters        : {}
    RunStart          : 8/27/2019 7:22:56 AM
    RunEnd            : 8/27/2019 7:23:07 AM
    DurationInMs      : 11324
    Status            : Succeeded
    Message           : 
    ```
2. 執行下列指令碼來取出複製活動執行詳細資料，例如，讀取/寫入資料的大小。

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. 確認您看到的輸出類似下列活動執行結果範例輸出：

    ```console
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    ActivityRunId     : 00000000-0000-0000-0000-000000000000
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink, enableStaging}
    Output            : {dataRead, dataWritten, filesRead, filesWritten...}
    LinkedServiceName :
    ActivityRunStart  : 8/27/2019 7:22:58 AM
    ActivityRunEnd    : 8/27/2019 7:23:05 AM
    DurationInMs      : 6828
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 20
    "dataWritten": 20
    "filesRead": 1
    "filesWritten": 1
    "sourcePeakConnections": 1
    "sinkPeakConnections": 1
    "copyDuration": 4
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (Central US)"
    "usedDataIntegrationUnits": 4
    "usedParallelCopies": 1
    "executionDetails": [
      {
        "source": {
          "type": "AzureBlobStorage"
        },
        "sink": {
          "type": "AzureBlobStorage"
        },
        "status": "Succeeded",
        "start": "2019-08-27T07:22:59.1045645Z",
        "duration": 4,
        "usedDataIntegrationUnits": 4,
        "usedParallelCopies": 1,
        "detailedDurations": {
          "queuingDuration": 3,
          "transferDuration": 1
        }
      }
    ]
    
    Activity 'Error' section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "CopyFromBlobToBlob"
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 瀏覽[教學課程](tutorial-copy-data-dot-net.md)以了解使用 Data Factory 的更多案例。