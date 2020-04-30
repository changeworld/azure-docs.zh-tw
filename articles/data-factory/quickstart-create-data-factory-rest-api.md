---
title: 使用 REST API 建立 Azure 資料處理站
description: 建立 Azure 資料處理站，以將資料從 Azure Blob 儲存體中的某個位置複製到另一個位置。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 06/10/2019
ms.author: jingwang
ms.openlocfilehash: b50217a3a8aeda03996183bf1dc82a0be1f485ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81419097"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>快速入門：使用 REST API 建立 Azure 資料處理站和管線

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [目前的版本](quickstart-create-data-factory-rest-api.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory 是雲端式資料整合服務，可讓您在雲端建立資料驅動工作流程，以便協調及自動進行資料移動和資料轉換。 使用 Azure Data Factory，您可以建立和排程資料驅動工作流程 (稱為管線)，這類工作流程可以從不同資料存放區內嵌資料，使用計算服務 (例如 Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics 和 Azure Machine Learning) 來處理/轉換資料，以及將輸出資料發佈至資料存放區 (例如 Azure SQL 資料倉儲)，以供商業智慧 (BI) 應用程式使用。

本快速入門說明如何使用 REST API 來建立 Azure 資料處理站。 在此資料處理站中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure 訂用帳戶**。 如果您沒有訂用帳戶，則可以建立[免費試用](https://azure.microsoft.com/pricing/free-trial/)帳戶。
* **Azure 儲存體帳戶**。 您需要使用 Blob 儲存體作為**來源**和**接收**資料存放區。 如果您沒有 Azure 儲存體帳戶，請參閱[建立儲存體帳戶](../storage/common/storage-account-create.md)一文，按照步驟來建立帳戶。
* 在 Blob 儲存體中建立一個 **Blob 容器**，在容器中建立一個輸入**資料夾**，然後上傳一些檔案到資料夾中。 您可以使用 [Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)之類的工具連線到 Azure Blob 儲存體，建立 Blob 容器，上傳輸入檔，以及驗證輸出檔。
* 安裝 **Azure PowerShell**。 遵循[如何安裝並設定 Azure PowerShell](/powershell/azure/install-Az-ps) 中的指示。 本快速入門會使用 PowerShell 來叫用 REST API 呼叫。
* 請遵循[此指示](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**在 Azure Active Directory 中建立應用程式**。 記下這些值，您在稍後的步驟中會用到：**應用程式識別碼**、**clientSecrets** 和**租用戶識別碼**。 將應用程式指派給「**參與者**」角色。

## <a name="set-global-variables"></a>設定全域變數

1. 啟動 **PowerShell**。 保持開啟 Azure PowerShell，直到本快速入門結束為止。 如果您關閉並重新開啟，則需要再次執行這些命令。

    執行下列命令，並輸入您用來登入 Azure 入口網站的使用者名稱和密碼：

    ```powershell
    Connect-AzAccount
    ```
    執行下列命令以檢視此帳戶的所有訂用帳戶：

    ```powershell
    Get-AzSubscription
    ```
    執行下列命令以選取您要使用的訂用帳戶。 以您的 Azure 訂用帳戶識別碼取代 **SubscriptionId**：

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. 以您自己的值使用取代預留位置後，請執行下列命令，以設定在稍後步驟中會用到的全域變數。

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $clientSecrets = "<your clientSecrets for the application>"
    $subscriptionId = "<your subscription ID to create the factory>"
    $resourceGroupName = "<your resource group to create the factory>"
    $factoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>使用 Azure AD 進行驗證

執行下列命令以驗證 Azure Active Directory (AAD)：

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $clientSecrets)
$result = $AuthContext.AcquireTokenAsync("https://management.core.windows.net/", $cred).GetAwaiter().GetResult()
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

## <a name="create-a-data-factory"></a>建立 Data Factory

執行以下命令以建立資料處理站：

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

請注意下列幾點：

* Azure Data Factory 的名稱在全域必須是唯一的。 如果發生下列錯誤，請變更名稱，並再試一次。

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* 如需目前可使用 Data Factory 的 Azure 區域清單，請在下列頁面上選取您感興趣的區域，然後展開 [分析]  以找出 [Data Factory]  ：[依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/)。 資料處理站所使用的資料存放區 (Azure 儲存體、Azure SQL Database 等) 和計算 (HDInsight 等) 可位於其他區域。

以下是範例回應：

```json
{  
    "name":"<dataFactoryName>",
    "identity":{  
        "type":"SystemAssigned",
        "principalId":"<service principal ID>",
        "tenantId":"<tenant ID>"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{  
        "provisioningState":"Succeeded",
        "createTime":"2019-09-03T02:10:27.056273Z",
        "version":"2018-06-01"
    },
    "eTag":"\"0200c876-0000-0100-0000-5d6dcb930000\"",
    "location":"East US",
    "tags":{  

    }
}
```
## <a name="create-linked-services"></a>建立連結的服務

您在資料處理站中建立的連結服務會將您的資料存放區和計算服務連結到資料處理站。 在此快速入門中，您只需要建立一個 Azure 儲存體連結服務作為複製來源與接收存放區，在此範例中名為 "AzureStorageLinkedService"。

執行下列命令來建立名稱為 **AzureStorageLinkedService** 的連結服務：

執行命令前，以 Azure 儲存體帳戶的名稱和金鑰取代 &lt;accountName&gt; 和 &lt;accountKey&gt;。

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{  
    "name":"AzureStorageLinkedService",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

以下是範例輸出：

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":"AzureStorageLinkedService",
    "type":"Microsoft.DataFactory/factories/linkedservices",
    "properties":{  
        "annotations":[  

        ],
        "type":"AzureBlobStorage",
        "typeProperties":{  
            "connectionString":"DefaultEndpointsProtocol=https;AccountName=<accountName>;"
        }
    },
    "etag":"07011a57-0000-0100-0000-5d6e14a20000"
}
```
## <a name="create-datasets"></a>建立資料集

您可以定義資料集來代表要從來源複製到接收的資料。 在此範例中，您會建立兩個資料集：InputDataset 和 OutputDataset。 其會參考您在前一節中建立的 Azure 儲存體連結服務。 輸入資料集代表輸入資料夾中的來源資料。 在輸入資料集定義中，您可以指定 Blob 容器 (adftutorial)、資料夾 (input) 以及包含來源資料的檔案 (emp.txt)。 此輸出資料集代表已複製到目的地的資料。 在輸出資料集定義中，您可以指定要將資料複製過去的 Blob 容器 (adftutorial)、資料夾 (output) 和檔案。

**建立 InputDataset**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/InputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"InputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "fileName":"emp.txt",
                "folderPath":"input",
                "container":"adftutorial"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

以下是範例輸出：

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/InputDataset",
    "name":"InputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; fileName=emp.txt; folderPath=input; container=adftutorial}"
        }
    },
    "etag":"07011c57-0000-0100-0000-5d6e14b40000"
}
```
**建立 OutputDataset**

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/datasets/OutputDataset?api-version=${apiVersion}"
$body = @"
{  
    "name":"OutputDataset",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":{  
                "type":"AzureBlobStorageLocation",
                "folderPath":"output",
                "container":"adftutorial"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

以下是範例輸出：

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/OutputDataset",
    "name":"OutputDataset",
    "type":"Microsoft.DataFactory/factories/datasets",
    "properties":{  
        "linkedServiceName":{  
            "referenceName":"AzureStorageLinkedService",
            "type":"LinkedServiceReference"
        },
        "annotations":[  

        ],
        "type":"Binary",
        "typeProperties":{  
            "location":"@{type=AzureBlobStorageLocation; folderPath=output; container=adftutorial}"
        }
    },
    "etag":"07013257-0000-0100-0000-5d6e18920000"
}
```
## <a name="create-pipeline"></a>建立管線

在此範例中，此管線包含一個活動，並使用兩個參數，輸入的 Blob 路徑和輸出 Blob 路徑。 這些參數的值是在觸發/執行管線時設定。 複製活動指的是在前一個步驟中建立作為輸入和輸出的同一 Blob 資料集。 將該資料集用作輸入資料集時，即會指定輸入路徑。 並且，將該資料集用作輸出資料集時，即會指定輸出路徑。

```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
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
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

以下是範例輸出：

```json
{  
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":"Adfv2QuickStartPipeline",
    "type":"Microsoft.DataFactory/factories/pipelines",
    "properties":{  
        "activities":[  
            "@{name=CopyFromBlobToBlob; type=Copy; dependsOn=System.Object[]; policy=; userProperties=System.Object[]; typeProperties=; inputs=System.Object[]; outputs=System.Object[]}"
        ],
        "annotations":[  

        ]
    },
    "etag":"07012057-0000-0100-0000-5d6e14c00000"
}
```

## <a name="create-pipeline-run"></a>建立管線執行

在此步驟中，您會以來源和接收 Blob 路徑的實際值，設定在管線中指定的 **inputPath** 和 **outputPath** 參數的值，然後觸發管線執行。 回應主體中傳回的管線執行識別碼會在稍後的監視 API 中用到。

將 **inputPath** 和 **outputPath** 的值以您的來源和接收 Blob 路徑取代，以在儲存檔案之前往返複製資料。


```powershell
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

以下是範例輸出：

```json
{  
    "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc"
}
```

## <a name="monitor-pipeline"></a>監視管線

1. 執行下列程式碼以持續檢查管線執行狀態，直到完成複製資料為止。

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```

    以下是範例輸出：

    ```json
    {  
        "runId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "debugRunId":null,
        "runGroupId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
        "pipelineName":"Adfv2QuickStartPipeline",
        "parameters":{  

        },
        "invokedBy":{  
            "id":"2bb3938176ee43439752475aa12b2251",
            "name":"Manual",
            "invokedByType":"Manual"
        },
        "runStart":"2019-09-03T07:22:47.0075159Z",
        "runEnd":"2019-09-03T07:22:57.8862692Z",
        "durationInMs":10878,
        "status":"Succeeded",
        "message":"",
        "lastUpdated":"2019-09-03T07:22:57.8862692Z",
        "annotations":[  

        ],
        "runDimension":{  

        },
        "isLatest":true
    }
    ```

2. 執行下列指令碼來取出複製活動執行詳細資料，例如，讀取/寫入資料的大小。

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.DataFactory/factories/${factoryName}/pipelineruns/${runId}/queryActivityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```
    以下是範例輸出：

    ```json
    {  
        "value":[  
            {  
                "activityRunEnd":"2019-09-03T07:22:56.6498704Z",
                "activityName":"CopyFromBlobToBlob",
                "activityRunStart":"2019-09-03T07:22:49.0719311Z",
                "activityType":"Copy",
                "durationInMs":7577,
                "retryAttempt":null,
                "error":"@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}",
                "activityRunId":"32951886-814a-4d6b-b82b-505936e227cc",
                "iterationHash":"",
                "input":"@{source=; sink=; enableStaging=False}",
                "linkedServiceName":"",
                "output":"@{dataRead=20; dataWritten=20; filesRead=1; filesWritten=1; sourcePeakConnections=1; sinkPeakConnections=1; copyDuration=4; throughput=0.01; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (Central US); usedDataIntegrationUnits=4; usedParallelCopies=1; executionDetails=System.Object[]}",
                "userProperties":"",
                "pipelineName":"Adfv2QuickStartPipeline",
                "pipelineRunId":"04a2bb9a-71ea-4c31-b46e-75276b61bafc",
                "status":"Succeeded",
                "recoveryStatus":"None",
                "integrationRuntimeNames":"defaultintegrationruntime",
                "executionDetails":"@{integrationRuntime=System.Object[]}"
            }
        ]
    }
    ```
## <a name="verify-the-output"></a>驗證輸出

使用 Azure 儲存體總管來檢查檔案是否已從 "inputPath" 複製到 "outputPath" (如您在建立管線執行時所指定)。

## <a name="clean-up-resources"></a>清除資源
您有兩種方式可以清除您在本快速入門中建立的資源。 您可以刪除 [Azure 資源群組](../azure-resource-manager/management/overview.md)，其中包括資源群組中的所有資源。 如果您想要讓其他資源保持不變，請僅刪除您在本教學課程中建立的資料處理站。

執行下列命令來刪除整個資源群組：
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

執行下列命令來僅刪除資料處理站：

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>後續步驟
在此範例中的管線會將資料從 Azure Blob 儲存體中的一個位置複製到其他位置。 瀏覽[教學課程](tutorial-copy-data-dot-net.md)以了解使用 Data Factory 的更多案例。
