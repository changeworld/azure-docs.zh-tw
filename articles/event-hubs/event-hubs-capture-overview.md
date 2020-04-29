---
title: 擷取串流事件 - Azure 事件中樞 | Microsoft Docs
description: 本文將概述可讓您透過 Azure 事件中樞擷取事件串流的擷取功能。
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: c166f4cace6a8cc25b36a84f4614033801e69a51
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265008"
---
# <a name="capture-events-through-azure-event-hubs-in-azure-blob-storage-or-azure-data-lake-storage"></a>透過 Azure 事件中樞在 Azure Blob 儲存體或 Azure Data Lake Storage 中擷取事件
Azure 事件中樞可讓您在[Azure Blob 儲存體](https://azure.microsoft.com/services/storage/blobs/)中事件中樞，或您選擇的[Azure Data Lake Storage Gen 1 或 gen 2](https://azure.microsoft.com/services/data-lake-store/)帳戶中，自動捕捉串流資料，並增加了指定時間或大小間隔的彈性。 設定擷取的作業很快，因此執行時不需要系統管理成本，而且它可以針對事件中樞的[輸送量單位](event-hubs-scalability.md#throughput-units)自動進行調整。 事件中樞擷取是將串流資料載入至 Azure 的最簡單方式，並可讓您專注於處理資料而非擷取資料。

事件中樞擷取可讓您在相同資料流上處理即時和批次型的管線。 這表示您可以建置會隨時間配合需求成長的解決方案。 不論您現在是要建置著眼於未來即時處理的批次型系統，或想要為現有即時解決方案新增有效率的冷路徑，事件中樞擷取都可以讓使用串流資料變得更簡單。


## <a name="how-event-hubs-capture-works"></a>事件中樞擷取的運作方式

事件中樞是用於輸入遙測的時間保留持久緩衝區，類似於分散式記錄。 在事件中樞調整大小的關鍵是 [資料分割取用者模型](event-hubs-scalability.md#partitions)。 每個資料分割都是獨立的資料區段，可獨立取用。 根據可設定的保留期限，此資料會隨時間而過時。 因此，給定的事件中樞永遠不會「太滿」。

事件中樞 Capture 可讓您指定自己的 Azure Blob 儲存體帳戶和容器，或是用來儲存已捕獲資料的 Azure Data Lake Storage 帳戶。 這些帳戶可以和事件中樞位於相同區域，也可以位於另一個區域，從而新增至事件中樞擷取功能的彈性。

擷取的資料會以 [Apache Avro][Apache Avro] 格式寫入，此為精簡、快速、二進位的格式，可使用內嵌結構描述提供豐富的資料結構。 此格式廣泛運用在 Hadoop 生態系統、串流分析和 Azure Data Factory。 關於使用 Avro 的詳細資訊可在本文稍後看到。

### <a name="capture-windowing"></a>擷取範圍

事件中樞擷取可讓您設定要控制擷取的範圍。 此範圍是具有「先者勝出原則」的最小大小和時間組態，這表示所遇到的第一個觸發條件會導致擷取作業。 如果您有一個 15 分鐘 100 MB 的擷取範圍，且傳送速率為每秒 1 MB，則大小範圍會比時間範圍更早觸發。 每個分割區都會獨立擷取，並在擷取時寫入已完成的區塊 Blob，而且會以遇到擷取間隔的時間命名。 儲存體命名慣例如下︰

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

請注意，日期值會以零填補；檔名範例可能是：

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

如果您的 Azure 儲存體 blob 暫時無法使用，事件中樞 Capture 會保留您的資料，以供您的事件中樞上所設定的資料保留期間使用，然後在您的儲存體帳戶再次可用時，再填滿資料。

### <a name="scaling-to-throughput-units"></a>調整至輸送量單位

事件中樞的流量會受到 [輸送量單位](event-hubs-scalability.md#throughput-units)控制。 單一輸送量單位可允許每秒 1 MB 或每秒 1000 個事件的輸入，輸出則為此數量的兩倍。 標準事件中樞可以設定 1-20 個輸送量單位，您可以透過增加配額的[支援要求][support request]購買更多單位。 超過所購買輸送量單位的使用量將遭到節流。 事件中樞擷取會直接從內部的事件中樞儲存體複製資料，略過輸送量單位輸出配額，並將輸出節省下來以供串流分析或 Spark 等其他處理讀取器使用。

一旦設定，事件中樞擷取會在您傳送第一個事件時自動執行，並且持續執行。 為了讓下游處理更輕鬆地知道處理程序正在運作，事件中樞會在沒有資料時寫入空白檔案。 這個程序會提供可預測的頻率和標記，以供饋送給批次處理器。

## <a name="setting-up-event-hubs-capture"></a>設定事件中樞擷取

您可以使用 [Azure 入口網站](https://portal.azure.com)或使用 Azure Resource Manager 範本，在建立事件中樞時設定擷取功能。 如需詳細資訊，請參閱下列文章：

- [使用 Azure 入口網站啟用事件中樞 Capture](event-hubs-capture-enable-through-portal.md)
- [使用 Azure Resource Manager 範本建立含有一個事件中樞的事件中樞命名空間並啟用擷取](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


## <a name="exploring-the-captured-files-and-working-with-avro"></a>瀏覽擷取檔案並使用 Avro

事件中樞擷取會以 Avro 格式建立檔案，如設定之時間範圍內所指定。 您可以在任何工具 (例如 [Azure 儲存體總管][Azure Storage Explorer]) 檢視這些檔案。 您可以在本機下載檔案，以對其進行處理。

事件中樞擷取所產生的檔案會有下列 Avro 結構描述︰

![Avro 結構描述][3]

瀏覽 Avro 檔案的簡易方式是使用 Apache 所提供的 [Avro Tools][Avro Tools] jar。 您也可以使用適用於輕量型 SQL 驅動體驗的 [Apache Drill][Apache Drill] 或 [Apache Spark][Apache Spark]，對內嵌資料執行複雜分散式處理。 

### <a name="use-apache-drill"></a>使用 Apache Drill

[Apache Drill][Apache Drill] 是「適用於巨量資料探索的開放原始碼 SQL 查詢引擎」，可以查詢結構化和半結構化資料。 引擎可以獨立節點或大型叢集形式執行，以獲得絕佳的效能。

Azure Blob 儲存體的原生支援可以使用，使得在 Avro 檔案中查詢資料變得容易，如文件中所述：

[Apache 演練： Azure Blob 儲存體外掛程式][Apache Drill: Azure Blob Storage Plugin]

若要輕鬆地查詢擷取的檔案，您可以透過容器建立和執行已啟用 Apache Drill 的 VM，以存取 Azure Blob 儲存體：

https://github.com/yorek/apache-drill-azure-blob

在大規模串流存放庫中可以使用完整端對端範例：

[大規模串流：事件中樞 Capture]

### <a name="use-apache-spark"></a>使用 Apache Spark

[Apache Spark][Apache Spark] 是進行大規模資料處理的整合分析引擎。 它支援不同的語言 (包括 SQL)，而且可以輕鬆地存取 Azure Blob 儲存體。 有幾個選項可讓您在 Azure 中執行 Apache Spark，而且每個都能輕鬆存取 Azure Blob 儲存體：

- [HDInsight： Azure 儲存體中的位址檔案][HDInsight: Address files in Azure storage]
- [Azure Databricks： Azure Blob 儲存體][Azure Databricks: Azure Blob Storage]
- [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/spark-job) 

### <a name="use-avro-tools"></a>使用 Avro Tools

[Avro Tools][Avro Tools] 是以 jar 套件形式提供。 下載 jar 檔案之後，您可以執行下列命令來查看特定 Avro 檔案的結構描述︰

```shell
java -jar avro-tools-1.9.1.jar getschema <name of capture file>
```

此命令會傳回

```json
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

您也可以使用 Avro Tools 將檔案轉換成 JSON 格式，並執行其他處理。

若要執行更進階的處理，請下載並安裝您所選平台適用的 Avro。 在本文撰寫當下，已有適用於 C、C++、C\#、Java、NodeJS、Perl、PHP、Python 和 Ruby 的實作。

Apache Avro 已完成適用於 [Java][Java] 和 [Python][Python] 的快速入門指南。 您也可以閱讀[開始使用事件中樞擷取](event-hubs-capture-python.md)一文。

## <a name="how-event-hubs-capture-is-charged"></a>事件中樞擷取的收費方式

事件中樞擷取的計量方式類似輸送量單位，屬於每小時的費用。 其費用與命名空間所購買的輸送量單位數目成正比。 當輸送量單位增加和減少時，事件中樞擷取也會增加和減少以提供相符的效能。 計量會串聯地發生。 如需定價詳細資訊，請參閱[事件中樞定價](https://azure.microsoft.com/pricing/details/event-hubs/)。 

請注意，Capture 不會耗用輸出配額，因為它會分開計費。 

## <a name="integration-with-event-grid"></a>事件格線整合 

您可以使用事件中樞命名空間作為其來源，建立 Azure 事件格線訂用帳戶。 下列教學課程會示範如何使用事件中樞作為來源、Azure Functions 應用程式作為接收，建立事件格線訂用帳戶：[使用事件格線和 Azure Functions 將擷取的事件中樞資料處理並移轉至 SQL 資料倉儲](store-captured-data-data-warehouse.md)。

## <a name="next-steps"></a>後續步驟
事件中樞擷取是將資料載入 Azure 中最簡單的方式。 使用 Azure Data Lake、Azure Data Factory 和 Azure HDInsight，即可使用您選擇的工具和平台，以您需要的規模執行批次處理和其他分析。

瞭解如何使用 Azure 入口網站和 Azure Resource Manager 範本來啟用這項功能：

- [使用 Azure 入口網站啟用事件中樞擷取](event-hubs-capture-enable-through-portal.md)
- [使用 Azure Resource Manager 範本來啟用事件中樞 Capture](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)


[Apache Avro]: https://avro.apache.org/
[Apache Drill]: https://drill.apache.org/
[Apache Spark]: https://spark.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: https://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: https://www.apache.org/dist/avro/stable/java/avro-tools-1.9.2.jar
[Java]: https://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: https://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[HDInsight: Address files in Azure storage]:https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-blob-storage
[Azure Databricks: Azure Blob Storage]:https://docs.databricks.com/spark/latest/data-sources/azure/azure-storage.html
[Apache Drill: Azure Blob Storage Plugin]:https://drill.apache.org/docs/azure-blob-storage-plugin/
[大規模串流：事件中樞 Capture]:https://github.com/yorek/streaming-at-scale/tree/master/event-hubs-capture
