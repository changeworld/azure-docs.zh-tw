---
title: 使用 Hadoop Streaming 活動來轉換資料
description: 說明如何使用 Azure Data Factory 中的 Hadoop 資料流活動，藉由在 Hadoop 叢集上執行 Hadoop 資料流程式來轉換資料。
author: nabhishek
ms.author: abnarain
manager: shwang
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 5acfef94a98f105a7cc09c5b72b65e8c228ed87d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: zh-TW
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844604"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>使用 Azure Data Factory 中的 Hadoop 資料流活動轉換資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-hadoop-streaming-activity.md)
> * [目前的版本](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Data Factory [管線](concepts-pipelines-activities.md)中的 HDInsight 資料流活動會在您[自己](compute-linked-services.md#azure-hdinsight-linked-service)或[隨選](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 叢集上執行 Hadoop 資料流程式。 本文是根據 [資料轉換活動](transform-data.md) 一文，它呈現資料轉換和支援的轉換活動的一般概觀。

如果您是 Azure Data Factory 的新手，請在閱讀本文之前先閱讀 [Azure Data Factory 簡介](introduction.md)，以及研習[教學課程：轉換資料](tutorial-transform-data-spark-powershell.md)。 

## <a name="json-sample"></a>JSON 範例
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>語法詳細資料

| 屬性          | 描述                              | 必要 |
| ----------------- | ---------------------------------------- | -------- |
| NAME              | 活動的名稱                     | 是      |
| description       | 說明活動用途的文字 | 否       |
| type              | 針對 Hadoop 資料流活動，活動類型是 HDInsightStreaming | 是      |
| linkedServiceName | 參考 HDInsight 叢集註冊為 Data Factory 中的連結服務。 若要深入了解此已連結的服務，請參閱[計算已連結的服務](compute-linked-services.md)一文。 | 是      |
| mapper            | 指定對應程式可執行檔的名稱 | 是      |
| reducer           | 指定減壓器可執行檔的名稱 | 是      |
| 結合子          | 指定結合子可執行檔的名稱 | 否       |
| fileLinkedService | Azure 儲存體已連結的服務用來儲存要執行之對應程式、結合子和減壓器的參考。 這裡僅支援 **[Azure Blob 儲存體](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** 和 **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** 的連結服務。 如果您未指定這項連結服務，則會使用 HDInsight 已連結的服務中定義的 Azure 儲存體已連結的服務。 | 否       |
| filePath          | 提供 fileLinkedService 引用之 Azure 儲存體中儲存的對應程式、結合子和減壓器程式的路徑陣列。 路徑會區分大小寫。 | 是      |
| input             | 指定對應程式輸入檔案的 WASB 路徑。 | 是      |
| output            | 指定減壓器輸出檔案的 WASB 路徑。 | 是      |
| getDebugInfo      | 指定何時將記錄檔複製到 HDInsight 叢集所使用 (或) scriptLinkedService 所指定的 Azure 儲存體。 允許的值：None、Always 或 Failure。 預設值：無。 | 否       |
| 引數         | 指定 Hadoop 作業的引數陣列。 引數會以命令列引數的方式傳遞給每項工作。 | 否       |
| 定義           | 指定參數作為機碼/值組，以供在 Hive 指令碼內參考。 | 否       | 

## <a name="next-steps"></a>後續步驟
請參閱下列文章，其說明如何以其他方式轉換資料： 

* [U-SQL 活動](transform-data-using-data-lake-analytics.md)
* [Hive 活動](transform-data-using-hadoop-hive.md)
* [Pig 活動](transform-data-using-hadoop-pig.md)
* [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)
* [Spark 活動](transform-data-using-spark.md)
* [.NET 自訂活動](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning 批次執行活動](transform-data-using-machine-learning.md)
* [預存程序活動](transform-data-using-stored-procedure.md)
