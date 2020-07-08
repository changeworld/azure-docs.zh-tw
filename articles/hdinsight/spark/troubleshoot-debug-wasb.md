---
title: 在 Azure HDInsight 中的 Debug WASB 檔案作業
description: 說明與 Azure HDInsight 叢集互動時，問題的疑難排解步驟和可能的解決方法。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "77470712"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>在 Azure HDInsight 中的 Debug WASB 檔案作業

有時候，您可能會想要瞭解 WASB 驅動程式所啟動的作業 Azure 儲存體。 針對用戶端，WASB 驅動程式會在**DEBUG**層級為每個檔案系統作業產生記錄。 WASB 驅動程式會使用 log4j 來控制記錄層級，而預設值為**INFO**層級。 如需 Azure 儲存體的伺服器端分析記錄，請參閱[Azure 儲存體分析記錄](../../storage/common/storage-analytics-logging.md)。

產生的記錄檔看起來會像這樣：

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>開啟檔案作業的 WASB debug 記錄檔

1. 從網頁瀏覽器流覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs` ，其中 `CLUSTERNAME` 是您 Spark 叢集的名稱。

1. 流覽至**advanced spark2-log4j-properties**。

    1. `log4j.appender.console.Threshold=INFO`將修改為 `log4j.appender.console.Threshold=DEBUG` 。

    1. 加入 `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`。

1. 流覽至**Advanced livy2-log4j-properties**。

    加入 `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`。

1. 儲存變更。

## <a name="additional-logging"></a>其他記錄

上述記錄檔應該提供檔案系統作業的高階瞭解。 如果上述記錄檔仍未提供有用的資訊，或者您想要調查 blob 儲存體 api 呼叫，請將新增 `fs.azure.storage.client.logging=true` 至 `core-site` 。 此設定將會啟用 wasb 儲存體驅動程式的 java sdk 記錄檔，並會列印每次對 blob 儲存體伺服器的呼叫。 請在調查後移除設定，因為它可能會快速填滿磁片，而且可能會使進程變慢。

如果後端是以 Azure Data Lake 為基礎，則請針對元件使用下列 log4j 設定（例如 spark/tez/hdfs）：

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

尋找記錄檔中的記錄檔 `/var/log/adl/adl.log` 。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 連線至 [@AzureSupport](https://twitter.com/azuresupport) - 這是用來改善客戶體驗的官方 Microsoft Azure 帳戶。 將 Azure 社群連線到正確的資源：解答、支援和專家。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
