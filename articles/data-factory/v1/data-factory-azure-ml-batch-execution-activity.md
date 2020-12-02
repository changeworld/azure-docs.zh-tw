---
title: 使用 Azure Data Factory 建立預測性資料管線
description: '說明如何使用 Azure Data Factory 和 Azure Machine Learning Studio (傳統來建立預測管線) '
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.openlocfilehash: 481b801d481f32ef84279be2d8bd6089670a01b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496514"
---
# <a name="create-predictive-pipelines-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>使用 Azure Machine Learning Studio (傳統) 和 Azure Data Factory 建立預測管線

> [!div class="op_single_selector" title1="轉換活動"]
> * [Hive 活動](data-factory-hive-activity.md)
> * [Pig 活動](data-factory-pig-activity.md)
> * [MapReduce 活動](data-factory-map-reduce.md)
> * [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
> * [Spark 活動](data-factory-spark.md)
> * [Azure Machine Learning Studio (傳統版) 批次執行活動](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning Studio (傳統版) 更新資源活動](data-factory-azure-ml-update-resource-activity.md)
> * [預存程序活動](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活動](data-factory-usql-activity.md)
> * [.NET 自訂活動](data-factory-use-custom-activities.md)

## <a name="introduction"></a>簡介
> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱[在 Data Factory 中使用機器學習來轉換資料](../transform-data-using-machine-learning.md)。


### <a name="azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (傳統) 
[Azure Machine Learning Studio (傳統) ](https://azure.microsoft.com/documentation/services/machine-learning/) 可讓您建立、測試及部署預測性分析解決方案。 從高階觀點而言，由下列三個步驟完成這個動作：

1. **建立訓練實驗**。 您可以使用 Azure Machine Learning Studio (傳統) 來執行此步驟。 Studio (傳統) 是一個共同作業的視覺化開發環境，可讓您使用定型資料來定型及測試預測性分析模型。
2. **將其轉換為評分實驗**。 一旦您的模型已使用現有資料訓練，並做好使用該模型為新資料評分的準備之後，您準備並簡化用於評分實驗。
3. **將其部署為 Web 服務**。 只要按一下，您就可以將評分實驗當做 Azure Web 服務發佈。 您可以透過此 Web 服務端點將資料傳送給您的模型，並從模型接收結果預測。

### <a name="azure-data-factory"></a>Azure Data Factory
Data Factory 是以雲端為基礎的資料整合服務，可協調及自動化資料的 **移動** 和 **轉換** 。 您可以使用 Azure Data Factory 建立資料整合方案，以從各種資料存放區內嵌資料、轉換/處理資料，並將結果資料發佈至資料存放區。

Data Factory 服務可讓您建立資料管線，以移動和轉換資料，然後依指定的排程 (每小時、每天、每週等) 執行管線。 它也提供豐富的視覺效果來顯示資料管線之間的歷程和相依性，並可透過單一整合檢視監視您所有的資料管線，以輕鬆地找出問題並設定監視警示。

請參閱 [Azure Data Factory 簡介](data-factory-introduction.md)和[建置您的第一個管線](data-factory-build-your-first-pipeline.md)文章，快速地開始使用 Azure Data Factory 服務。

### <a name="data-factory-and-machine-learning-studio-classic-together"></a>Data Factory 和 Machine Learning Studio 一起 (傳統) 
Azure Data Factory 可讓您輕鬆地建立管線，使用已發佈的 [Azure Machine Learning Studio (傳統) ][azure-machine-learning] web 服務進行預測性分析。 使用 Azure Data Factory 管線中的 **批次執行活動** ，您可以叫用 Studio (傳統) web 服務來對批次中的資料進行預測。 如需詳細資訊，請參閱使用批次執行活動一節叫用 Azure Machine Learning Studio (傳統) web 服務。

經過一段時間之後，就必須使用新的輸入資料集重新定型 Studio 中的預測模型， (傳統的) 評分實驗。 您可以執行下列步驟，以從 Data Factory 管線重新定型 Studio (傳統) 模型：

1. 將訓練實驗 (而非預設實驗) 發佈為 Web 服務。 您可以在 Studio (傳統) 中執行此步驟，就像在先前的案例中將預測實驗公開為 web 服務一樣。
2. 使用 Studio (傳統) 批次執行活動來叫用用於定型實驗的 web 服務。 基本上，您可以使用 Studio (傳統) 批次執行活動來叫用定型 web 服務和評分 web 服務。

當您完成重新訓練之後，請使用 **Azure Machine Learning Studio (傳統) 更新資源活動**，將評分 web 服務 (以 web 服務公開的預測性實驗，) 為新定型的模型。 如需詳細資料，請參閱[使用更新資源活動更新模型](data-factory-azure-ml-update-resource-activity.md)一文。

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>使用批次執行活動來叫用 Web 服務
您可以使用 Azure Data Factory 來協調資料移動和處理，然後使用 Studio (傳統) 來執行批次執行。 以下是最上層的步驟︰

1. 建立 Azure Machine Learning Studio (傳統) 連結服務。 您需要下列值︰

   1. **要求 URI** 。 您可以按一下 Web 服務頁面中的 **批次執行** 連結，即可找到要求 URI。
   2. 已發行之 Studio (傳統) web 服務的 **API 金鑰**。 按一下已發佈的 Web 服務，即可找到 API 金鑰。
   3. 使用 **AzureMLBatchExecution** 活動。

      ![Machine Learning Studio (傳統) 儀表板](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![批次 URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>案例：使用 Web 服務輸入/輸出 (參考 Azure Blob 儲存體中的資料) 的實驗
在此案例中，Studio (傳統) Web 服務會使用 Azure blob 儲存體中檔案的資料進行預測，並將預測結果儲存在 blob 儲存體中。 下列 JSON 使用 AzureMLBatchExecution 活動定義 Data Factory 管線。 此活動以 **DecisionTreeInputBlob** 資料集做為輸入，以 **DecisionTreeResultBlob** 資料集做為輸出。 **DecisionTreeInputBlob** 會做為輸入以使用 **webServiceInput** JSON 屬性傳遞至 Web 服務。 **DecisionTreeResultBlob** 會做為輸出以使用 **webServiceOutputs** JSON 屬性傳遞至 Web 服務。

> [!IMPORTANT]
> 如果 Web 服務接受多個輸入，請使用 **webServiceInputs** 屬性，而不要使用 **webServiceInput**。 如需如何使用 webServiceInputs 屬性的範例，請參閱 [Web 服務需要多個輸入](#web-service-requires-multiple-inputs) 一節。
>
> **webServiceInput** /) **>typeproperties** 中 webServiceInput **webServiceInputs** 和 **>webserviceoutputs** (屬性所參考的資料集也必須包含在活動 **輸入** 和 **輸出** 中。
>
> 在您的 Studio (傳統) 實驗中，web 服務輸入和輸出埠及全域參數都有預設名稱 ( "input1"，"input2" ) 您可以自訂。 您用於 webServiceInputs、webServiceOutputs 及 globalParameters 設定的名稱必須與實驗中的名稱完全相同。 您可以在 Studio (傳統) 端點的 [批次執行說明] 頁面上查看範例要求承載，以確認預期的對應。
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> 只有當輸入及輸出屬於 AzureMLBatchExecution 活動時，才可以當做參數傳遞至 Web 服務。 例如，在上面的 JSON 片段中，DecisionTreeInputBlob 是 AzureMLBatchExecution 活動的輸入，其透過 webServiceInput 參數傳遞至 Web 服務做為輸入。
>
>

### <a name="example"></a>範例
此範例使用 Azure 儲存體來存放輸入和輸出資料。

建議您在瀏覽此範例之前，先瀏覽[透過 Data Factory 建立第一個管線][adf-build-1st-pipeline]教學課程。 在此範例中使用 Data Factory 編輯器來建立 Data Factory 構件 (連結服務、資料集、管線)。

1. 為您的 **Azure 儲存體** 建立 **連結服務**。 如果輸入和輸出檔案在不同的儲存體帳戶中，您就需要兩個連結服務。 以下是 JSON 範例：

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. 建立 **輸入** Azure Data Factory **資料集**。 與某些其他 Data Factory 資料集不同的是，這些資料集必須同時包含 **folderPath** 和 **fileName** 值。 您可以使用資料分割，讓每個批次執行 (每一個資料配量) 處理或產生唯一的輸入和輸出檔案。 您可能需要包含某個上游活動，以將輸入轉換成 CSV 檔案格式，並將它放在每個配量的儲存體帳戶中。 在此情況下，您不需包含下列範例所示的 **external** 及 **externalData** 設定，而您的 DecisionTreeInputBlob 會是不同活動的輸出資料集。

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    您的輸入 csv 檔案必須要有資料行標題資料列。 如果使用 [複製活動] 建立 csv 或將其移至 Blob 儲存體，則接收屬性 **blobWriterAddHeader** 應該設為 **true**。 例如：

    ```JSON
    sink:
    {
        "type": "BlobSink",
        "blobWriterAddHeader": true
    }
    ```

    如果 csv 檔案沒有標頭資料列，您可能會看到下列錯誤： **活動中的錯誤：讀取字串時發生錯誤。非預期的權杖：StartObject。路徑 ''、行 1、位置 1**。
3. 建立 **輸出** Azure Data Factory **資料集**。 此範例使用資料分割來為每一個配量執行建立一個唯一輸出路徑。 如果沒有資料分割，活動就會覆寫檔案。

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. 建立 **AzureMLLinkedService** 類型的 **連結服務**，並提供 API 金鑰和模型批次執行 URL。

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. 最後，撰寫一個包含 **AzureMLBatchExecution** 活動的管線。 在執行階段，管線會執行下列步驟︰

   1. 從輸入資料集取得輸入檔案的位置。
   2. 叫用 Studio (傳統) 批次執行 API
   3. 將批次執行輸出複製到輸出資料集中指定的 Blob。

      > [!NOTE]
      > AzureMLBatchExecution 活動可以有零個或多個輸入，以及一個或多個輸出。
      >
      >

      ```JSON
      {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
      }
      ```

      **開始** 和 **結束** 日期時間都必須是 [ISO 格式](https://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 **結束** 時間是選用項目。 如果您未指定 **end** 屬性的值，則會計算為 **「開始 + 48 小時」。** 若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。 如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](/previous-versions/azure/dn835050(v=azure.100)) 。

      > [!NOTE]
      > 您可自行選擇是否指定 AzureMLBatchExecution 活動的輸入。
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>案例：使用讀取器/寫入器模組參考各種儲存體資料的實驗
建立 Studio (傳統) 實驗的另一個常見案例是使用讀取器和寫入器模組。 讀取器模組是用來將資料載入實驗，而寫入器模組則是用於儲存您的實驗資料。 如需讀取器和寫入器模組的詳細資料，請參閱 MSDN Library 上的[讀取器](/azure/machine-learning/studio-module-reference/import-data)和[寫入器](/azure/machine-learning/studio-module-reference/export-data)主題。

使用讀取器和寫入器模組時，較好的做法是針對這些讀取器/寫入器模組的每一個屬性，使用 Web 服務參數。 這些 Web 參數可讓您在執行階段設定值。 例如，建立實驗時，您可以利用讀取器模組使用 Azure SQL Database：XXX.database.windows.net。 部署 web 服務之後，您會想要啟用 web 服務的取用者，以指定另一部名為 YYY.database.windows.net 的邏輯 SQL 伺服器。 您可以使用 Web 服務參數來設定此值。

> [!NOTE]
> Web 服務的輸入和輸出與 Web 服務參數不同。 在第一個案例中，您已瞭解如何針對 Studio (傳統) Web 服務指定輸入和輸出。 在此案例中，您會傳遞 Web 服務的參數，以對應至讀取器/寫入器模組的屬性。
>
>

讓我們看看使用 Web 服務參數的案例。 您有一個已部署的 Studio (傳統) web 服務，其使用讀取器模組從 Studio 所支援的其中一個資料來源讀取資料 (傳統)  (例如： Azure SQL Database) 。 批次執行之後，會使用寫入器模組寫入結果 (Azure SQL Database)。  實驗中沒有定義任何 Web 服務的輸入和輸出。 在此情況下，我們建議您為讀取器和寫入器模組設定相關 Web 服務參數。 此組態允許在使用 AzureMLBatchExecution 活動時設定讀取器/寫入器模組。 如以下活動 JSON 所示，在 **globalParameters** 區段中指定 Web 服務參數。

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

您也可以使用 [Data Factory 函式](data-factory-functions-variables.md) 傳遞 Web 服務參數的值，如下列範例所示：

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web 服務參數區分大小寫，因此，請確定您在活動 JSON 中所指定的名稱符合 Web 服務所公開的名稱。
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>使用讀取器模組讀取 Azure Blob 中多個檔案的資料
具有 Pig 和 Hive 等活動的巨量資料管線可以產生沒有副檔名的一個或多個輸出檔案。 例如，當您指定外部 Hive 資料表時，外部 Hive 資料表的資料可以儲存在 Azure Blob 儲存體中，並命名為：000000_0。 您可以在實驗中使用讀取器模組讀取多個檔案，並將該模組用於預測。

在 Studio (傳統) 實驗中使用讀取器模組時，您可以指定 Azure Blob 做為輸入。 Azure Blob 儲存體中的檔案可以是在 HDInsight 上執行的 Pig 和 Hive 指令碼所產生的輸出檔 (範例：000000_0)。 讀取器模組可讓您藉由設定 **容器、目錄或 Blob 的路徑** 來讀取檔案 (沒有副檔名)。 **容器路徑** 指向容器，**目錄/Blob** 則指向包含檔案的資料夾，如下圖所示。 星號 (也就是 \*) **會指定容器/資料夾中的所有檔案 (也就是 data/aggregateddata/year=2014/month-6/\*)** 將讀取為實驗的一部分。

![Azure Blob 屬性](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>範例
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>AzureMLBatchExecution 活動使用 Web 服務參數的管線

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure Machine Learning Studio (classic) model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

在上述 JSON 範例中：

* 部署的 Studio (傳統) Web 服務會使用讀取器和寫入器模組，從 Azure SQL Database 讀取/寫入資料。 此 Web 服務會公開下列 4 個參數：資料庫伺服器名稱、資料庫名稱、伺服器使用者帳戶名稱和伺服器使用者帳戶密碼。
* **開始** 和 **結束** 日期時間都必須是 [ISO 格式](https://en.wikipedia.org/wiki/ISO_8601)。 例如：2014-10-14T16:32:41Z。 **結束** 時間是選用項目。 如果您未指定 **end** 屬性的值，則會計算為 **「開始 + 48 小時」。** 若要無限期地執行管線，請指定 **9999-09-09** 做為 **end** 屬性的值。 如需 JSON 屬性的詳細資料，請參閱 [JSON 指令碼參考](/previous-versions/azure/dn835050(v=azure.100)) 。

### <a name="other-scenarios"></a>其他案例
#### <a name="web-service-requires-multiple-inputs"></a>Web 服務需要多個輸入
如果 Web 服務接受多個輸入，請使用 **webServiceInputs** 屬性，而不要使用 **webServiceInput**。 **webServiceInputs** 所參考的資料集也必須包含在活動的 **inputs** 中。

在您的 Azure Machine Learning Studio (傳統) 實驗中，web 服務輸入和輸出埠及全域參數都有預設名稱 ( "input1"，"input2" ) 您可以自訂。 您用於 webServiceInputs、webServiceOutputs 及 globalParameters 設定的名稱必須與實驗中的名稱完全相同。 您可以在 Studio (傳統) 端點的 [批次執行說明] 頁面上查看範例要求承載，以確認預期的對應。

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>Web 服務不需要輸入
Azure Machine Learning Studio (傳統) 批次執行 web 服務可以用來執行任何可能不需要任何輸入的工作流程，例如 R 或 Python 腳本。 或者，您也可以使用不會公開任何 GlobalParameters 的讀取器模組來設定實驗。 在此情況下，AzureMLBatchExecution 活動的設定如下：

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>Web 服務不需要輸入/輸出
Azure Machine Learning Studio (傳統) 批次執行 web 服務可能未設定任何 Web 服務輸出。 在此範例中，沒有任何 Web 服務輸入或輸出，也不會設定任何 GlobalParameters。 仍會在活動本身設定輸出，但不會將它指定為 webServiceOutput。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>Web 服務會使用讀取器和寫入器，而且只有在其他活動皆成功時，才會執行活動
Azure Machine Learning Studio (傳統) web 服務讀取器和寫入器模組可能會設定為執行，或不使用任何 GlobalParameters。 但是，您可能想要在管線中內嵌服務呼叫來使用資料集相依性，只有在完成一些上游處理之後，才會叫用該服務。 您也可以在使用此方法完成批次執行之後觸發一些其他動作。 在此情況下，您可以使用活動輸入和輸出來表達相依性，而不需將它們任一個命名為 Web 服務輸入或輸出。

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**心得** 如下︰

* 如果您的實驗端點使用 webServiceInput：它可透過 Blob 資料集來表示，並且會包含於活動輸入以及 webServiceInput 屬性中。 否則，即會省略 webServiceInput 屬性。
* 如果您的實驗端點使用 webServiceOutput：它們可透過 Blob 資料集來表示，並且會包含於活動輸出以及 webServicepOutputs 屬性中。 活動輸出和 webServiceOutputs 會以此實驗中的每個輸出名稱來對應。 否則，即會省略 webServiceOutputs 屬性。
* 如果您的實驗端點會公開 globalParameter，則會在活動 globalParameters 屬性中提供它們以做為金鑰值組。 否則，即會省略 globalParameters 屬性。 金鑰會區分大小寫。 [Azure Data Factory 函式](data-factory-functions-variables.md) 可能會在值中使用。
* 您可以將額外的資料集包含於活動輸入和輸出屬性中，而不需在活動的 typeProperties 中加以參考。 這些資料集會使用配量相依性來管理執行，但其他的則會被 AzureMLBatchExecution 活動所忽略。


## <a name="updating-models-using-update-resource-activity"></a>使用更新資源活動來更新模型
當您完成重新訓練之後，請使用 **Azure Machine Learning Studio (傳統) 更新資源活動**，將評分 web 服務 (以 web 服務公開的預測性實驗，) 為新定型的模型。 如需詳細資料，請參閱[使用更新資源活動更新模型](data-factory-azure-ml-update-resource-activity.md)一文。

### <a name="reader-and-writer-modules"></a>讀取器和寫入器模組
使用 Azure SQL 讀取器和寫入器，是常見的 Web 服務參數使用案例。 讀取器模組可用來將資料從 Studio 以外的資料管理服務載入至實驗 (傳統) 。 寫入器模組是指將實驗中的資料儲存到 Studio (傳統) 的資料管理服務。

如需 Azure Blob/Azure SQL 讀取器/寫入器的詳細資料，請參閱 MSDN Library 上的[讀取器](/azure/machine-learning/studio-module-reference/import-data)和[寫入器](/azure/machine-learning/studio-module-reference/export-data)主題。 上一節中的範例已使用 Azure Blob 讀取器與 Azure Blob 寫入器。 本節討論如何使用 Azure SQL 讀取器和 Azure SQL 寫入器。

## <a name="frequently-asked-questions"></a>常見問題集
**問：** 我擁有巨量資料管線所產生的多個檔案。 我可以使用 AzureMLBatchExecution 活動來處理所有檔案嗎？

**答：** 是。 如需詳細資料，請參閱 **使用讀取器模組讀取 Azure Blob 中多個檔案的資料** 一節。

## <a name="azure-machine-learning-studio-classic-batch-scoring-activity"></a>Azure Machine Learning Studio (傳統) 批次評分活動
如果您使用 **>azuremlbatchscoring** 活動與 Azure Machine Learning Studio (傳統) 整合，建議您使用最新的 **AzureMLBatchExecution** 活動。

2015 年 8 月發行的 Azure SDK 和 Azure PowerShell 中導入了 AzureMLBatchExecution 活動。

如果您想要繼續使用 AzureMLBatchScoring 活動，請繼續閱讀本節。

### <a name="azure-machine-learning-studio-classic-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>Azure Machine Learning Studio (傳統) 批次評分活動（使用 Azure 儲存體進行輸入/輸出）

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>Web 服務參數
若要指定 Web 服務參數的值，請將 **typeProperties** 區段新增至管線 JSON 中的 **AzureMLBatchScoringActivity** 區段，如下列範例所示：

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
您也可以使用 [Data Factory 函式](data-factory-functions-variables.md) 傳遞 Web 服務參數的值，如下列範例所示：

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> Web 服務參數區分大小寫，因此，請確定您在活動 JSON 中所指定的名稱符合 Web 服務所公開的名稱。
>
>

## <a name="see-also"></a>另請參閱
* [Azure 部落格文章：開始使用 Azure Data Factory 和 Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: https://azure.microsoft.com/services/machine-learning/