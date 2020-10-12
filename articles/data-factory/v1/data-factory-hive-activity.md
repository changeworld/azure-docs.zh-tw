---
title: 使用 Hive 活動轉換資料-Azure
description: 瞭解如何使用 Azure data factory v1 中的 Hive 活動，在隨選/您自己的 HDInsight 叢集上執行 Hive 查詢。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 41f570f93e95e9801b08c06cacc0423b1bf3b8e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91252778"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>使用 Azure Data Factory 中的 Hive 活動轉換資料 
> [!div class="op_single_selector" title1="轉換活動"]
> * [Hive 活動](data-factory-hive-activity.md) 
> * [Pig 活動](data-factory-pig-activity.md)
> * [MapReduce 活動](data-factory-map-reduce.md)
> * [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
> * [Spark 活動](data-factory-spark.md)
> * [Machine Learning 批次執行活動](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 更新資源活動](data-factory-azure-ml-update-resource-activity.md)
> * [預存程序活動](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活動](data-factory-usql-activity.md)
> * [.NET 自訂活動](data-factory-use-custom-activities.md)

> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱[在 Data Factory 中使用 Hive 活動來轉換資料](../transform-data-using-hadoop-hive.md)。

Data Factory [管線](data-factory-create-pipelines.md)中的 HDInsight Hive 活動會在[您自己](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或[隨選的](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 架構 HDInsight 叢集上執行 Hive 查詢。 本文是根據 [資料轉換活動](data-factory-data-transformation-activities.md) 一文，它呈現資料轉換和支援的轉換活動的一般概觀。

> [!NOTE] 
> 如果您是 Azure Data Factory 的新手，請在閱讀本文章之前閱讀 [Azure Data Factory 簡介](data-factory-introduction.md)，以及研習教學課程：[建置您的第一個資料管線](data-factory-build-your-first-pipeline.md)。 

## <a name="syntax"></a>語法

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>語法詳細資料
| 屬性 | 描述 | 必要 |
| --- | --- | --- |
| NAME |活動的名稱 |是 |
| description |說明活動用途的文字 |否 |
| type |HDinsightHive |是 |
| 輸入 |Hive 活動所耗用的輸入 |否 |
| 輸出 |Hive 活動所耗用的輸出 |是 |
| linkedServiceName |參考 HDInsight 叢集註冊為 Data Factory 中的連結服務 |是 |
| 指令碼 |指定 Hive 指令碼內嵌 |否 |
| scriptPath |在 Azure Blob 儲存體中儲存 Hive 指令碼，並提供檔案的路徑。 使用 'script' 或 'scriptPath' 屬性。 兩者無法同時使用。 檔案名稱有區分大小寫。 |否 |
| 定義 |在使用 'hiveconf' 的 Hive 指令碼內指定參數做為參考的金鑰/值組 |否 |

## <a name="example"></a>範例
我們來看看遊戲記錄分析的範例，您想要識別使用者花多少時間在玩貴公司開發的遊戲。 

下列記錄檔是範例遊戲記錄檔，以逗號 (`,`) 分隔，並包含下列欄位 – ProfileID、SessionStart、Duration、SrcIPAddress 和 GameType。

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

用來處理此資料的 **Hive 指令碼** ：

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

若要在 Data Factory 管線中執行此 Hive 指令碼，您需要執行下列動作

1. 建立連結服務以註冊[您自己的 HDInsight 計算叢集](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)或設定[隨選 HDInsight 計算叢集](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)。 讓我們將此連結服務命名為 "HDInsightLinkedService"。
2. 建立 [連結服務](data-factory-azure-blob-connector.md) 以設定裝載資料之 Azure Blob 儲存體的連接。 讓我們來呼叫此連結服務 "StorageLinkedService"
3. 建立指向輸入和輸出資料的 [資料集](data-factory-create-datasets.md) 。 讓我們來呼叫輸入資料集 "HiveSampleIn" 和輸出資料集 "HiveSampleOut"
4. 將 Hive 查詢作為檔案複製到步驟 #2 中設定的 Azure Blob 儲存體。 如果裝載資料的儲存體和裝載此查詢檔案的儲存體不同，請建立個別的 Azure 儲存體連結服務並在活動中參考它。 使用 **scriptPath** 指定 Hive 查詢檔案的路徑，並使用 **scriptLinkedService** 指定包含指令碼檔案的 Azure 儲存體。 
   
   > [!NOTE]
   > 您也可以使用 **script** 屬性，在活動定義中以內嵌方式提供 Hive 指令碼。 不建議使用此方法，因為必須逸出 JSON 文件的指令碼中的所有特殊字元，而且可能造成偵錯問題。 最佳做法是遵循步驟 #4。
   > 
   > 
5. 建立具有 HDInsightHive 活動的管線。 活動會處理/轉換資料。

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. 部署管線。 如需詳細資料，請參閱〈 [建立管線](data-factory-create-pipelines.md) 〉文章。 
7. 使用資料處理站監視和管理檢視來監視管線。 如需詳細資料，請參閱〈 [監視及管理 Data Factory 管線](data-factory-monitor-manage-pipelines.md) 〉文章。 

## <a name="specifying-parameters-for-a-hive-script"></a>指定 Hive 指令碼的參數
在此範例中，每天都會將遊戲記錄擷取到 Azure Blob 儲存體，並儲存在使用日期和時間分割的資料夾。 您想要參數化 Hive 指令碼，在執行階段期間以動態方式傳遞輸入資料夾位置，並且產生使用日期和時間分割的輸出。

若要使用參數化的 Hive 指令碼，請執行下列動作

* 定義 **defines**中的參數。

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* 在 Hive 指令碼中，參考使用 **${hiveconf:parameterName}** 的參數。 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>另請參閱
* [Pig 活動](data-factory-pig-activity.md)
* [MapReduce 活動](data-factory-map-reduce.md)
* [Hadoop 串流活動](data-factory-hadoop-streaming-activity.md)
* [叫用 Spark 程式](data-factory-spark.md)
* [叫用 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

