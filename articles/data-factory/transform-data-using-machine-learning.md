---
title: 建立預測性資料管線
description: 瞭解如何使用 Azure Data Factory 中的 Azure Machine Learning Studio (傳統) 批次執行活動建立預測管線。
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/16/2020
ms.openlocfilehash: 50ef97bca0a5359c49ba2f18b1ec789ab076350a
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637729"
---
# <a name="create-a-predictive-pipeline-using-azure-machine-learning-studio-classic-and-azure-data-factory"></a>使用 Azure Machine Learning Studio (傳統) 和 Azure Data Factory 建立預測管線

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-azure-ml-batch-execution-activity.md)
> * [目前的版本](transform-data-using-machine-learning.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Machine Learning Studio (傳統) ](https://azure.microsoft.com/documentation/services/machine-learning/) 可讓您建立、測試及部署預測性分析解決方案。 從高階觀點而言，由下列三個步驟完成這個動作：

1. **建立訓練實驗** 。 您可以使用 Azure Machine Learning Studio (傳統) 來執行此步驟。 Azure Machine Learning Studio (傳統) 是一個共同作業的視覺化開發環境，可讓您使用定型資料來定型及測試預測性分析模型。
2. **將其轉換為評分實驗** 。 一旦您的模型已使用現有資料訓練，並做好使用該模型為新資料評分的準備之後，您準備並簡化用於評分實驗。
3. **將其部署為 Web 服務** 。 只要按一下，您就可以將評分實驗當做 Azure Web 服務發佈。 您可以透過此 Web 服務端點將資料傳送給您的模型，並從模型接收結果預測。

### <a name="data-factory-and-azure-machine-learning-studio-classic-together"></a>Data Factory 和 Azure Machine Learning Studio 一起 (傳統) 
Azure Data Factory 可讓您輕鬆地建立管線，使用已發佈的 [Azure Machine Learning Studio (傳統) ](https://azure.microsoft.com/documentation/services/machine-learning) web 服務進行預測性分析。 使用 Azure Data Factory 管線中的 **批次執行活動** ，您可以叫用 Azure Machine Learning Studio (傳統) web 服務來對批次中的資料進行預測。

經過一段時間之後，就必須使用新的輸入資料集重新定型 Azure Machine Learning Studio 中的預測模型 (傳統) 評分實驗。 您可以透過執行下列步驟，從 Data Factory 管線重新訓練模型：

1. 將訓練實驗 (而非預設實驗) 發佈為 Web 服務。 您可以在 Azure Machine Learning Studio (傳統) 中執行此步驟，就像在先前的案例中將預測實驗公開為 web 服務一樣。
2. 使用 Azure Machine Learning Studio (傳統) 批次執行活動來叫用用於訓練實驗的 web 服務。 基本上，您可以使用 Azure Machine Learning Studio (傳統) 批次執行活動來叫用定型 web 服務和評分 web 服務。

當您完成重新訓練之後，請使用 **Azure Machine Learning Studio (傳統) 更新資源活動** ，將評分 web 服務 (以 web 服務公開的預測性實驗，) 為新定型的模型。 如需詳細資料，請參閱[使用更新資源活動更新模型](update-machine-learning-models.md)一文。

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (傳統) 連結服務

您可以建立 **Azure Machine Learning studio (傳統)** 連結服務，將 Azure Machine Learning Studio (傳統) Web 服務連結至 Azure data factory。 Azure Machine Learning Studio (傳統) 批次執行活動和 [更新資源活動](update-machine-learning-models.md)使用連結的服務。

```JSON
{
    "type" : "linkedServices",
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "URL to Azure ML Predictive Web Service",
            "apiKey": {
                "type": "SecureString",
                "value": "api key"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

如需了解 JSON 定義中的屬性說明，請參閱[計算連結服務](compute-linked-services.md)一文。

Azure Machine Learning Studio (傳統) 針對您的預測實驗支援傳統 Web 服務和新的 Web 服務。 您可以從 Data Factory 選擇正確的服務。 若要取得建立 Azure Machine Learning Studio (傳統) 連結服務所需的資訊，請移至 https://services.azureml.net ，其中會列出所有 (新的) Web 服務和傳統 Web 服務。 按一下您要存取的 Web 服務，然後按一下 [取用]  頁面。 為 **apiKey** 屬性複製 **主索引鍵** ，並為 **mlEndpoint** 屬性複製 **批次要求** 。

![Azure Machine Learning Studio (傳統) Web 服務](./media/transform-data-using-machine-learning/web-services.png)

## <a name="azure-machine-learning-studio-classic-batch-execution-activity"></a>Azure Machine Learning Studio (傳統) 批次執行活動

下列 JSON 程式碼片段會定義 Azure Machine Learning Studio (傳統) 批次執行活動。 活動定義具有您稍早建立的 Azure Machine Learning Studio (傳統) 連結服務的參考。

```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "<web service input name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path1"
            },
            "<web service input name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path2"
            }
        },
        "webServiceOutputs": {
            "<web service output name 1>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path3"
            },
            "<web service output name 2>": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"path4"
            }
        },
        "globalParameters": {
            "<Parameter 1 Name>": "<parameter value>",
            "<parameter 2 name>": "<parameter 2 value>"
        }
    }
}
```

| 屬性          | 描述                              | 必要 |
| :---------------- | :--------------------------------------- | :------- |
| NAME              | 管線中的活動名稱     | 是      |
| description       | 說明活動用途的文字。  | 否       |
| type              | 針對 Data Lake Analytics 的 U-SQL 活動，活動類型為 **AzureMLBatchExecution** 。 | 是      |
| linkedServiceName | Azure Machine Learning Studio (傳統) 連結服務的連結服務。 若要深入了解此已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。 | 是      |
| webServiceInputs  | 索引鍵、值組，對應 Azure Machine Learning Studio (傳統) Web 服務輸入的名稱。 索引鍵必須符合已發佈的 Azure Machine Learning Studio (傳統) Web 服務中定義的輸入參數。 值是指定輸入 Blob 位置的 Azure 儲存體連結服務和 FilePath 屬性組。 | 否       |
| webServiceOutputs | 索引鍵、值組，對應 Azure Machine Learning Studio (傳統) Web 服務輸出的名稱。 索引鍵必須符合已發佈的 Azure Machine Learning Studio (傳統) Web 服務中定義的輸出參數。 值是指定輸出 Blob 位置的 Azure 儲存體連結服務和 FilePath 屬性組。 | 否       |
| globalParameters  | 要傳遞給 Azure Machine Learning Studio (傳統) 批次執行服務端點的索引鍵、值組。 金鑰必須符合發佈的 Azure Machine Learning Studio (傳統) web 服務中定義的 web 服務參數名稱。 值會在 Azure Machine Learning Studio (傳統) 批次執行要求的 GlobalParameters 屬性中傳遞 | 否       |

### <a name="scenario-1-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>案例 1：使用 Web 服務輸入/輸出 (參考 Azure Blob 儲存體中的資料) 的實驗

在此案例中，Azure Machine Learning Studio (傳統) Web 服務會使用 Azure blob 儲存體中檔案的資料進行預測，並將預測結果儲存在 blob 儲存體中。 下列 JSON 使用 AzureMLBatchExecution 活動定義 Data Factory 管線。 Azure 部落格儲存體中的輸入和輸出資料使用 LinkedName 和 FilePath 來引用。 在輸入和輸出的範例連結服務不同的情況下，您可以針對每個輸入/輸出使用不同的連結服務，讓 Data Factory 能夠挑選正確的檔案，並傳送至 Azure Machine Learning Studio (傳統) Web 服務。

> [!IMPORTANT]
> 在您的 Azure Machine Learning Studio 中 (傳統) 實驗、web 服務輸入和輸出埠，以及全域參數的預設名稱 ( "input1"，"input2" ) 您可以自訂。 您用於 webServiceInputs、webServiceOutputs 及 globalParameters 設定的名稱必須與實驗中的名稱完全相同。 您可以在 Azure Machine Learning Studio (傳統) 端點的 [批次執行說明] 頁面上查看範例要求承載，以確認預期的對應。


```JSON
{
    "name": "AzureMLExecutionActivityTemplate",
    "description": "description",
    "type": "AzureMLBatchExecution",
    "linkedServiceName": {
        "referenceName": "AzureMLLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "webServiceInputs": {
            "input1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in1.csv"
            },
            "input2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService1",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest/input/in2.csv"
            }
        },
        "webServiceOutputs": {
            "outputName1": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out1.csv"
            },
            "outputName2": {
                "LinkedServiceName":{
                    "referenceName": "AzureStorageLinkedService2",
                    "type": "LinkedServiceReference"
                },
                "FilePath":"amltest2/output/out2.csv"
            }
        }
    }
}
```
### <a name="scenario-2-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>案例 2：使用讀取器/寫入器模組參考各種儲存體資料的實驗
建立 Azure Machine Learning Studio (傳統) 實驗的另一個常見案例是使用匯入資料和輸出資料模組。 「匯入資料」模組是用來將資料載入實驗，而「輸出資料」模組則是用於儲存您的實驗資料。 如需「匯入資料」和「輸出資料」模組的詳細資料，請參閱 MSDN 文件庫上的[匯入資料](/azure/machine-learning/studio-module-reference/import-data)和[輸出資料](/azure/machine-learning/studio-module-reference/export-data)主題。

使用「匯入資料」和「輸出資料」模組時，較好的做法是針對這些模組的每一個屬性，使用 Web 服務參數。 這些 Web 參數可讓您在執行階段設定值。 例如，您可以透過使用 Azure SQL Database：XXX.database.windows.net 的「匯入資料」模組，建立實驗。 部署 web 服務之後，您會想要啟用 web 服務的取用者，以指定另一個稱為的邏輯 SQL server `YYY.database.windows.net` 。 您可以使用 Web 服務參數來設定此值。

> [!NOTE]
> Web 服務的輸入和輸出與 Web 服務參數不同。 在第一個案例中，您已瞭解如何針對 Azure Machine Learning Studio (傳統) Web 服務來指定輸入和輸出。 在此案例中，您會傳遞 Web 服務的參數，以對應至「匯入資料」/「輸出資料」模組的屬性。

讓我們看看使用 Web 服務參數的案例。 您已部署 Azure Machine Learning Studio (傳統) web 服務，其使用讀取器模組，從 Azure Machine Learning Studio (傳統)  (所支援的其中一個資料來源讀取資料 Azure SQL Database 例如：) 。 批次執行之後，會使用寫入器模組寫入結果 (Azure SQL Database)。  實驗中沒有定義任何 Web 服務的輸入和輸出。 在此情況下，我們建議您為讀取器和寫入器模組設定相關 Web 服務參數。 此組態允許在使用 AzureMLBatchExecution 活動時設定讀取器/寫入器模組。 如以下活動 JSON 所示，在 **globalParameters** 區段中指定 Web 服務參數。

```JSON
"typeProperties": {
    "globalParameters": {
        "Database server name": "<myserver>.database.windows.net",
        "Database name": "<database>",
        "Server user account name": "<user name>",
        "Server user account password": "<password>"
    }
}
```

> [!NOTE]
> Web 服務參數區分大小寫，因此，請確定您在活動 JSON 中所指定的名稱符合 Web 服務所公開的名稱。

當您完成重新訓練之後，請使用 **Azure Machine Learning Studio (傳統) 更新資源活動** ，將評分 web 服務 (以 web 服務公開的預測性實驗，) 為新定型的模型。 如需詳細資料，請參閱[使用更新資源活動更新模型](update-machine-learning-models.md)一文。

## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料：

* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [Pig 活動](transform-data-using-hadoop-pig.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Hadoop 串流活動](transform-data-using-hadoop-streaming.md)
* [Spark 活動](transform-data-using-spark.md)
* [.NET 自訂活動](transform-data-using-dotnet-custom-activity.md)
* [預存程序活動](transform-data-using-stored-procedure.md)