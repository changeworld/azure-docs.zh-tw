---
title: 使用 Apache Pig
titleSuffix: Azure HDInsight
description: 了解如何在 HDInsight 上搭配 Apache Hadoop 使用 Pig。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 8fd42169ab6ccb144215e1b6ad2ece09ff55c07f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87072829"
---
# <a name="use-apache-pig-with-apache-hadoop-on-hdinsight"></a>在 HDInsight 上搭配 Apache Hadoop 使用 Apache Pig

瞭解如何搭配 HDInsight 使用 [Apache Pig](https://pig.apache.org/) 。

Apache Pig 是一個平台，可使用名為 *Pig Latin* 的程序性語言建立 Apache Hadoop 的程式。 若要建立 *MapReduce* 解決方案，Pig 是 Java 的替代選項，而且也已包含在 Azure HDInsight 中。 使用下表了解可搭配 HDInsight 使用 Pig 的各種方式︰

## <a name="why-use-apache-pig"></a><a id="why"></a>為何要使用 Apache Pig

在 Hadoop 中使用 MapReduce 處理資料的其中一項挑戰是，只藉由使用 map 和 reduce 函數實作處理邏輯。 如果是複雜的處理，您經常必須將處理分成多個鏈結在一起的 MapReduce 作業，才能獲得想要的結果。

Pig 可讓您將處理定義為一系列轉換，使資料流過以產生所需的輸出。

Pig Latin 語言可讓您從原始輸入描述資料流 (經過一或多個轉換後) 以產生所需的輸出。 Pig Latin 程式遵循此一般模式：

* **載入**：讀取要從檔案系統操作的資料。

* **轉換**：操縱資料。

* 傾印**或儲存**：將資料輸出至畫面，或儲存資料以供處理。

### <a name="user-defined-functions"></a>使用者自訂函數

Pig Latin 也支援使用者定義函數 (UDF)，此函數讓您可用叫用外部元件，這些元件會實作很難以 Pig Latin 模型化的邏輯。

如需 Pig Latin 的詳細資訊，請參閱 [Pig Latin 參考手冊 1](https://archive.cloudera.com/cdh/3/pig/piglatin_ref1.html) (英文) 和 [Pig Latin 參考手冊 2](https://archive.cloudera.com/cdh/3/pig/piglatin_ref2.html) (英文)。

## <a name="example-data"></a><a id="data"></a>範例資料

HDInsight 提供各種範例資料集，儲存在 `/example/data` 和 `/HdiSamples` 目錄中。 這些目錄位於您的叢集預設儲存體中。 本文件中的 Pig 範例使用 `/example/data/sample.log` 中的 *log4j* 檔案。

檔案中的每一筆記錄均由一列欄位組成，包括以 `[LOG LEVEL]` 欄位來顯示類型和嚴重性，例如：

```output
2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937
```

在上一個範例中，記錄層級是「錯誤」。

> [!NOTE]  
> 您也可以使用 [Apache Log4j](https://en.wikipedia.org/wiki/Log4j) 記錄工具產生 log4j 檔案，然後將該檔案上傳至 Blob。 如需指示，請參閱 [將資料上傳至 HDInsight](hdinsight-upload-data.md) 。 如需關於如何搭配 HDInsight 使用 Azure 儲存體 Blob 的詳細資訊，請參閱 [搭配 HDInsight 使用 Azure Blob 儲存體](hdinsight-hadoop-use-blob-storage.md)。

## <a name="example-job"></a><a id="job"></a>範例作業

以下 Pig Latin 作業會從 HDInsight 叢集的預設儲存體載入 `sample.log` 檔案。 然後該工作會執行一系列轉換，進而產生輸入資料中每個記錄層級出現次數的計數。 結果會寫入 STDOUT。

```output
LOGS = LOAD 'wasb:///example/data/sample.log';
LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
RESULT = order FREQUENCIES by COUNT desc;
DUMP RESULT;
```

下圖顯示每個轉換如何處理資料的摘要。

![轉換的圖形化表示][image-hdi-pig-data-transformation]

## <a name="run-the-pig-latin-job"></a><a id="run"></a>執行 Pig Latin 工作

HDInsight 可以使用各種方法執行 Pig Latin 工作。 請使用下表決定適合您的方法，然後跟著連結逐項閱讀介紹。

## <a name="pig-and-sql-server-integration-services"></a>Pig 和 SQL Server Integration Services

您可以使用 SQL Server Integration Services (SSIS) 執行 Pig 作業。 適用於 SSIS 的 Azure Feature Pack 中提供下列元件可搭配 HDInsight 上的 Pig 工作使用。

* [Azure HDInsight Pig 工作][pigtask]

* [Azure 訂用帳戶連線管理員][connectionmanager]

在[這裡][ssispack]深入了解適用於 SSIS 的 Azure Feature Pack。

## <a name="next-steps"></a><a id="nextsteps"></a>後續步驟

現在您已學會如何搭配 HDInsight 使用 Pig，接著請使用下列連結來探索 Azure HDInsight 的其他使用方式。

* [將資料上傳至 HDInsight](hdinsight-upload-data.md)
* [搭配 HDInsight 使用 Apache Hive](./hadoop/hdinsight-use-hive.md)
* [搭配 HDInsight 使用 Apache Sqoop](hdinsight-use-sqoop.md)
* [搭配 HDInsight 使用 MapReduce 工作](./hadoop/hdinsight-use-mapreduce.md)

[apachepig-home]: https://pig.apache.org/
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: https://curl.haxx.se/
[pigtask]: https://msdn.microsoft.com/library/mt146781(v=sql.120).aspx
[connectionmanager]: https://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: https://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]:../hdinsight-use-hive.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: /powershell/azure/

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx


[image-hdi-pig-data-transformation]: ./media/use-pig/hdi-data-transformation.gif
