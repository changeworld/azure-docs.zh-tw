---
title: 在管線中使用自訂活動
description: 了解如何建立自訂活動，並在 Azure 資料處理站管線中使用這些活動。
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 74e381a9ad32acdaa8cbb719824d74ca6d339f30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84019957"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>在 Azure 資料處理站管線中使用自訂活動

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-use-custom-activities.md)
> * [目前的版本](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

您可以在 Azure Data Factory 管線中使用兩種活動。

- [資料移動活動](copy-activity-overview.md)，可在[支援的來源與接收資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)之間移動資料。
- 使用計算服務（例如 Azure HDInsight、Azure Batch 和 Azure Machine Learning）來轉換資料的[資料轉換活動](transform-data.md)。

若要將資料移入/移出 Data Factory 不支援的資料存放區，或者以 Data Factory 不支援的方式轉換/處理資料，您可以利用自己的資料移動或轉換邏輯建立**自訂活動**，然後在管線中使用活動。 自訂活動會在虛擬機器的 **Azure Batch** 集區上執行自訂程式碼邏輯。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您不熟悉 Azure Batch 服務，請參閱下列文章：

* [Azure Batch 基本知識](../batch/batch-technical-overview.md) ，以取得 Azure Batch 服務的概觀。
* [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) Cmdlet 可建立 Azure Batch 帳戶 (或) [Azure 入口網站](../batch/batch-account-create-portal.md)，以使用 Azure 入口網站建立 Azure Batch 帳戶。 如需使用此 Cmdlet 的詳細指示，請參閱[使用 PowerShell 管理 Azure Batch 帳戶](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)一文。
* [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) Cmdlet 可建立 Azure Batch 集區。

## <a name="azure-batch-linked-service"></a>Azure Batch 已連結的服務

下列 JSON 會定義範例 Azure Batch 已連結的服務。 如需詳細資訊，請參閱 [Azure Data Factory 支援的計算環境](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 若要深入了解 Azure Batch 已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。

## <a name="custom-activity"></a>自訂活動

下列 JSON 片段會定義具有範例自訂活動的管線。 活動定義具有對 Azure Batch 已連結的服務之參考。

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

在此範例中，helloworld.exe 是自訂應用程式，儲存在 resourceLinkedService 中使用之 Azure 儲存體帳戶的 customactv2/helloworld 資料夾。 自訂活動會提交此自訂應用程式以在 Azure Batch 上執行。 您可以取代在 Azure Batch 集區節點之目標作業系統上執行之任何偏好應用程式的命令。

下表描述此活動特有的屬性之名稱和描述。

| 屬性              | 說明                              | 必要 |
| :-------------------- | :--------------------------------------- | :------- |
| NAME                  | 管線中的活動名稱     | 是      |
| description           | 說明活動用途的文字。  | 否       |
| type                  | 針對自訂活動，活動類型是**自訂**。 | 是      |
| linkedServiceName     | Azure Batch 的已連結的服務。 若要深入了解此已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。  | Yes      |
| 命令               | 要執行的自訂應用程式命令。 如果應用程式已經可以在 Azure Batch 集區節點上使用，則可以略過 resourceLinkedService 和 folderPath。 例如，您可以將命令指定為 `cmd /c dir`，該命令原生受 Windows Batch 集區節點支援。 | Yes      |
| resourceLinkedService | 對儲存體帳戶 (自訂應用程式儲存所在) 的 Azure 儲存體已連結的服務 | 否 &#42;       |
| folderPath            | 自訂應用程式及其所有相依項目的資料夾路徑<br/><br/>如果您將相依性儲存在子資料夾中 (也就是 folderPath** 下的階層式資料夾結構)，當您將檔案複製到 Azure Batch 時，目前的資料夾結構會遭到壓平合併。 也就是所有檔案會複製到沒有子資料夾的單一資料夾中。 若要解決這個問題行為，請考慮壓縮檔案並複製壓縮的檔案，然後在所需位置中以自訂程式碼來將其解壓縮。 | 否 &#42;       |
| referenceObjects      | 現有已連結的服務和資料集的陣列。 參考的已連結的服務和資料集會傳遞至 JSON 格式的自訂應用程式，讓您的自訂程式碼可以參考 Data Factory 的資源 | No       |
| extendedProperties    | 使用者定義的屬性，可以傳遞至 JSON 格式的自訂應用程式，讓您的自訂程式碼可以參考其他屬性 | No       |
| retentionTimeInDays | 針對自訂活動提交的檔案保留時間。 預設值為30天。 | No |

&#42; 必須同時指定或同時省略 `resourceLinkedService` 和 `folderPath` 屬性。

> [!NOTE]
> 如果您在自訂活動中將連結服務傳遞為 referenceObjects，則傳遞已啟用 Azure Key Vault 的連結服務是很好的安全性作法（因為它不包含任何安全字串），而且會直接從程式碼中的 Key Vault 使用秘密名稱提取認證。 您可以在[這裡](https://github.com/nabhishek/customactivity_sample/tree/linkedservice)找到參考已啟用 AKV 的已連結服務的範例，從 Key Vault 抓取認證，然後存取程式碼中的儲存體。

## <a name="custom-activity-permissions"></a>自訂活動權限

自訂活動會將 Azure Batch 自動使用者帳戶設定為*具有工作範圍 (預設的自動使用者規格) 的非系統管理員存取權*。 您無法變更自動使用者帳戶的權限等級。 如需詳細資訊，請參閱[在 Batch 中的使用者帳戶執行工作 | 自動使用者帳戶](../batch/batch-user-accounts.md#auto-user-accounts)。

## <a name="executing-commands"></a>執行命令

您可以使用自訂活動直接執行命令。 下列範例會在目標 Azure Batch 集區的節點上執行 "echo hello world" 命令，並將輸出列印至 stdout。

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>傳遞物件和屬性

這個範例會示範如何使用 referenceObjects 和 extendedProperties 將 Data Factory 物件和使用者定義屬性傳遞至自訂應用程式。

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

當活動執行時，referenceObjects 和 extendedProperties 會儲存在部署至與 SampleApp.exe 相同執行資料夾的下列檔案：

- `activity.json`

  儲存 extendedProperties 和自訂活動的屬性。

- `linkedServices.json`

  儲存 referenceObjects 屬性中定義之已連結的服務的陣列。

- `datasets.json`

  儲存 referenceObjects 屬性中定義之資料集的陣列。

下列範例程式碼示範 SampleApp.exe 如何從 JSON 檔案存取必要的資訊：

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>擷取執行輸出

您可以使用下列 PowerShell 命令啟動管線的執行：

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

管線正在執行時，您可以使用下列命令檢查執行的輸出：

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

您的自訂應用程式的 **stdout** 和 **stderr** 會儲存至您在使用工作的 GUID 建立 Azure Batch 已連結的服務時，定義的 Azure 儲存體已連結的服務中的 **adfjobs** 容器。 您可以從活動執行輸出取得詳細路徑，如下列程式碼片段所示：

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

如果您想要在下游活動中取用 stdout.txt 的內容，可以在 "\@activity('MyCustomActivity').output.outputs[0]" 運算式中取得 stdout.txt 檔案的路徑。

> [!IMPORTANT]
> - activity.json、linkedServices.json 和 datasets.json 會儲存在 Batch 工作的執行階段資料夾。 在此範例中，上的 activity.js、上的 linkedServices.js和上的 datasets.js會儲存在 `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` path 中。 您必須視需要個別加以清除。
> - 如果已連結的服務使用自我裝載整合執行階段，機密資訊 (例如金鑰或密碼) 就會由自我裝載整合執行階段進行加密，以確保認證會保留在客戶定義的私人網路環境中。 某些機密欄位由您的自訂應用程式以這樣的方式參考時，可能會遺失。 視需要在 extendedProperties 中使用 SecureString，而不是使用已連結的服務參考。

## <a name="pass-outputs-to-another-activity"></a>將輸出傳遞到其他活動

您可以將自訂活動中程式碼的自訂值傳回到 Azure Data Factory。 您可以從應用程式中將它們寫入到 `outputs.json`執行此動作。 Data Factory 會複製 `outputs.json` 的內容，並將其附加至活動輸出以作為 `customOutput` 屬性的值 （大小限制為 2 MB）。如果您想要使用 `outputs.json` 下游活動中的內容，您可以使用運算式來取得值 `@activity('<MyCustomActivity>').output.customOutput` 。

## <a name="retrieve-securestring-outputs"></a>擷取 SecureString 輸出

在 Data Factory 使用者介面上的 [監視] 索引標籤中，指定為 SecureString** 類型的敏感屬性值 (如本文部分範例中所示) 都會加上遮罩。  但是，在實際的管線執行中，SecureString** 屬性會在 `activity.json` 檔案中序列化為純文字形式的 JSON。 例如：

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

此序列化不是真正的安全，而且也不是以安全為目的。 其目的是要提示 Data Factory 對 [監視] 索引標籤中的值加上遮罩。

若要從自訂活動中存取 SecureString** 類別的屬性，請讀取您 .EXE 所在資料夾中的 `activity.json` 檔案、將 JSON 還原序列化，然後存取 JSON 屬性 (extendedProperties => [propertyName] => value)。

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a> 比較 v2 自訂活動和第 1 版 (自訂) DotNet 活動

在 Azure Data Factory 第1版中，您可以藉由建立 .NET 類別庫專案，並使用可實作為介面方法的類別，來執行（自訂） DotNet 活動 `Execute` `IDotNetActivity` 。 (自訂) DotNet 活動之 JSON 承載中的已連結服務、資料集及擴充屬性，都會以強型別物件的形式傳遞至執行方法。 如需第 1 版行為的詳細資料，請參閱[第 1 版中的 (自訂) DotNet](v1/data-factory-use-custom-activities.md)。 由於這項執行，您的第1版 DotNet 活動程式碼必須以 .NET Framework 4.5.2 為目標。 第 1 版 DotNet 活動也必須在 Windows 型 Azure Batch 集區節點上執行。

在 Azure Data Factory V2 自訂活動中，您不需要執行 .NET 介面。 您現在可以在將之編譯為可執行檔的情況下，直接執行命令、指令碼，以及自己的自訂程式碼。 若要設定此實作，您需要一併指定 `Command` 屬性和 `folderPath` 屬性。 自訂活動會將可執行檔及其相依性上傳至 `folderpath`，並為您執行命令。

定義於 Data Factory V2 自訂活動之 JSON 承載中的已連結服務、資料集(定義於 referenceObjects) 及擴充屬性，可以 JSON 檔案的形式由可執行檔加以存取。 您可以使用 JSON 序列化程式存取所需的屬性，如之前的 SampleApp.exe 程式碼範例中所示。

利用 Data Factory V2 自訂活動中所引進的變更，您可以使用慣用語言來撰寫自訂程式碼邏輯，然後在 Azure Batch 支援的 Windows 和 Linux 作業系統上執行它。

下表說明 Data Factory V2 自訂活動和 Data Factory 第 1 版 (自訂) DotNet 活動之間的差異：

|差異      | 自訂活動      | 第 1 版 (自訂) DotNet 活動      |
| ---- | ---- | ---- |
|定義自訂邏輯的方式      |提供可執行檔      |藉由執行 .NET DLL      |
|自訂邏輯的執行環境      |Windows 或 Linux      |Windows （.NET Framework 4.5.2）      |
|執行指令碼      |支援直接執行指令碼 (例如，Windows VM 上的 "cmd /c echo hello world")      |需要 .NET DLL 中的實作為      |
|需要資料集      |選擇性      |需要資料集來鏈結活動並傳遞資訊      |
|將來自活動的資訊傳遞至自訂邏輯      |透過 ReferenceObjects (LinkedServices 和資料集) 和 ExtendedProperties (自訂屬性)      |透過 ExtendedProperties (自訂屬性)、輸入和輸出資料集      |
|擷取自訂邏輯中的資訊      |剖析與可執行檔儲存於相同資料夾的 activity.json、linkedServices.json 和 datasets.json      |透過 .NET SDK （.NET 框架4.5.2）      |
|記錄      |直接寫入 STDOUT      |在 .NET DLL 中執行記錄器      |

如果您的現有 .NET 程式碼是針對第1版（自訂） DotNet 活動所撰寫，您必須修改程式碼，才能使用目前版本的自訂活動。 遵循下列高階指導方針來更新程式碼：

  - 將專案從 .NET 類別庫變更為主控台應用程式。
  - 使用 `Main` 方法啟動您的應用程式。 已不再需要 `IDotNetActivity` 介面的 `Execute` 方法。
  - 使用 JSON 序列化程式來讀取和剖析已連結的服務、資料集和活動，而不是作為強型別物件。 將必要屬性的值傳遞給您主要的自訂程式碼邏輯。 請參閱之前的 SampleApp.exe 程式碼作為範例。
  - 不再支援記錄器物件。 您可以將可執行檔的輸出列印到主控台並儲存至 stdout.txt。
  - 不再需要 Microsoft.Azure.Management.DataFactories NuGet 套件。
  - 編譯您的程式碼，將可執行檔及其相依性上傳至 Azure 儲存體，並在 `folderPath` 屬性中定義路徑。

如需如何將 Data Factory 第 1 版文章[在 Azure Data Factory 管線中使用自訂活動](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)中所述的端對端 DLL 和管線範例重新撰寫為 Data Factory 自訂活動的完整範例，請參閱 [Data Factory 自訂活動範例](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample) \(英文\)。

## <a name="auto-scaling-of-azure-batch"></a>Azure Batch 的自動調整

您也可以建立具有 **自動調整** 功能的 Azure Batch 集區。 例如，您可以用 0 專用 VM 和依據暫止工作數目自動調整的公式，建立 Azure Batch 集區。

這裡的範例公式會有下列行為：當集區一開始建立時，它會以 1 部 VM 啟動。 $PendingTasks 計量會定義執行中 + 作用中 (已排入佇列) 狀態的工作數目。 公式會尋找過去 180 秒內的平均擱置中工作數目，並據以設定 TargetDedicated。 它會確保 TargetDedicated 一律不會超過 25 部 VM。 因此，當提交新工作時，集區會自動成長而且工作會完成，VM 會依序成為可用，而且自動調整規模功能會壓縮那些 VM。 您可以視需要調整 startingNumberOfVMs 及 maxNumberofVMs。

自動調整公式：

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

如需詳細資訊，請參閱 [自動調整 Azure Batch 集區中的計算節點](../batch/batch-automatic-scaling.md) 。

如果集區使用預設的 [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx)，Batch 服務在執行自訂活動之前，可能需要 15-30 分鐘的時間準備 VM。 如果集區使用不同的 autoScaleEvaluationInterval，Batch 服務可能需要 autoScaleEvaluationInterval + 10 分鐘。

## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料：

* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [Pig 活動](transform-data-using-hadoop-pig.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 串流活動](transform-data-using-hadoop-streaming.md)
* [Spark 活動](transform-data-using-spark.md)
* [Machine Learning 批次執行活動](transform-data-using-machine-learning.md)
* [預存程序活動](transform-data-using-stored-procedure.md)
