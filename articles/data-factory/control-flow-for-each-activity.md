---
title: Azure Data Factory 中的 ForEach 活動
description: For Each 活動定義管線中重複的控制流程。 它用於反覆查詢集合，並執行指定的活動。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: 35d61e896a395c3044a51780fef72d54c211a31f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417185"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Azure Data Factory 中的 ForEach 活動
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ForEach 活動定義管線中重複的控制流程。 此活動用來逐一查看整個集合，然後以迴圈執行指定的活動。 此活動的迴圈實作與程式設計語言中的 Foreach 迴圈結構相似。

## <a name="syntax"></a>語法
本文稍後將說明這些屬性。 項目屬性為集合，而集合中的每個項目則使用 `@item()` 表示，如下列語法所示：  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>類型屬性

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
名稱 | for-each 活動的名稱。 | String | 是
type | 必須設定為 **ForEach** | String | 是
isSequential | 指定應該循序或以平行方式執行迴圈。  以平行方式可一次執行最多 20 個迴圈反覆項目。 例如，如果您的 ForEach 活動會反覆查詢 10 個不同來源和接收資料集的複製活動，且 **isSequential** 設為 False，則所有複本會都執行一次。 預設值是 False。 <br/><br/> 如果 isSequential 設定為 False，請確認有正確的設定可執行多個可執行檔。 否則，應謹慎使用這個屬性，以避免引發寫入衝突。 如需詳細資訊，請參閱[平行執行](#parallel-execution)一節。 | 布林值 | 否。 預設值是 False。
batchCount | 批次計數，用於控制平行執行的數目 (當 isSequential 設定為 false 時)。 這是最高的並行限制，但每個活動不一定會以這個數位執行 | 整數 (最大值 50) | 否。 預設值為 20。
項目 | 傳回要反覆查詢之 JSON 陣列的運算式。 | 運算式 (傳回 JSON 陣列) | 是
活動 | 要執行的活動。 | 活動清單 | 是

## <a name="parallel-execution"></a>平行執行
如果 **isSequential** 設為 false，活動會以平行方式逐一查看，並行的反覆項目數最多為 20。 此設定應謹慎使用。 如果並行的反覆項目會寫入相同資料夾的不同檔案，這種方法是正常的。 如果並行的反覆項目會同時寫入相同的檔案，這種方法很可能會導致錯誤。 

## <a name="iteration-expression-language"></a>反覆項目運算式語言
在 ForEach 活動中，為屬性**項目**提供可反覆查詢的陣列。 使用 `@item()` 反覆查詢 ForEach 活動中的單一列舉。 例如，如果**項目**是陣列：[1, 2, 3]，`@item()` 在第一個反覆項目中會傳回 1，在第二個反覆項目中會傳回 2，在第三個反覆項目中會傳回 3。

## <a name="iterating-over-a-single-activity"></a>反覆查詢單一活動
**案例：** 從 Azure Blob 中相同的來源檔案複製到 Azure Blob 中的多個目的地檔案。

### <a name="pipeline-definition"></a>管線定義

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Blob 資料集定義

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>執行參數值

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>反覆查詢多個活動
可反覆查詢 ForEach 活動中的多個活動 (例如：複製和 Web 活動)。 在此案例中，我們建議您將多個活動提取至不同的管線。 然後，您可以在管線中使用 [ExecutePipeline 活動](control-flow-execute-pipeline-activity.md)，以 ForEach 活動叫用不同的管線來搭配多個活動。 


### <a name="syntax"></a>語法

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>範例
**案例：** 使用執行管線活動反覆查詢 ForEach 活動內的 InnerPipeline。 內部管線使用參數化的結構描述定義複製。

#### <a name="master-pipeline-definition"></a>主要管線定義

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>內部管線定義

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>來源資料集定義

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>接收資料集定義

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>主要管線參數
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>彙總輸出

若要匯總__foreach__活動的輸出，請利用_變數_和_附加變數_活動。

首先，在管線中宣告 `array` _variable_。 然後在每個 __foreach__ 迴圈內叫用 _Append Variable_ 活動。 接著，也可以從您的陣列中擷取彙總。

## <a name="limitations-and-workarounds"></a>限制和因應措施

以下是 ForEach 活動和建議因應措施的一些限制。

| 限制 | 因應措施 |
|---|---|
| 您無法將 ForEach 迴圈內嵌在其他 ForEach 迴圈 (或 Until 迴圈) 內。 | 設計兩個層級的管線，其中外部管線具有外部 ForEach 迴圈，會逐一查看具有巢狀迴圈的內部管線。 |
| ForEach 活動的平行處理 `batchCount` 上限為 50，最多可處理 100,000 個項目。 | 設計兩個層級的管線，其中外部管線具有 ForEach 活動，會逐一查看內部管線。 |
| | |

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [取得中繼資料活動](control-flow-get-metadata-activity.md)
- [查閱活動](control-flow-lookup-activity.md)
- [網路活動](control-flow-web-activity.md)
