---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 5e7bdce3a195d8171fef1963f2e389bfeec5fffb
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91571890"
---
Azure Data Factory 支援下列可個別或與其他活動鏈結而新增至管線的轉換活動。

| 資料轉換活動 | 計算環境 |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop 串流](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Machine Learning 活動︰批次執行和更新資源](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [預存程序](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL、Azure Synapse Analytics 或 SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] 或 Azure Batch |

> [!NOTE]
> 您可以使用 MapReduce 活動，在 HDInsight Spark 叢集上執行 Spark 程式。 如需詳細資訊，請參閱 [從 Azure Data Factory 叫用 Spark 程式](../articles/data-factory/v1/data-factory-spark.md) 。
> 您可以建立自訂活動，以便在已安裝 R 的 HDInsight 叢集上執行 R 指令碼。 請參閱 [使用 Azure Data Factory 執行 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)。
> 
> 

