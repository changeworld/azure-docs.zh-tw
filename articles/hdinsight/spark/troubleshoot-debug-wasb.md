---
title: 在 Azure HDInsight 中調試 WASB 檔操作
description: 描述與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470712"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>在 Azure HDInsight 中調試 WASB 檔操作

有時，您可能希望瞭解 WASB 驅動程式從 Azure 存儲開始的操作。 對於用戶端，WASB 驅動程式在**DEBUG**級別為每個檔案系統操作生成日誌。 WASB 驅動程式使用 log4j 來控制日誌記錄級別，預設值為**INFO**級別。 有關 Azure 存儲伺服器端分析日誌，請參閱[Azure 存儲分析日誌記錄](../../storage/common/storage-analytics-logging.md)。

生成的日誌將類似于：

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>打開用於檔操作的 WASB 調試日誌

1. 從 Web 瀏覽器導航到`https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`，Spark 群集的名稱在哪裡`CLUSTERNAME`。

1. 導航到**高級 spark2-log4j 屬性**。

    1. 修改`log4j.appender.console.Threshold=INFO`為`log4j.appender.console.Threshold=DEBUG`。

    1. 加入 `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`。

1. 導航到**高級 livy2-log4j 屬性**。

    加入 `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`。

1. 儲存變更。

## <a name="additional-logging"></a>其他記錄

上述日誌應提供對檔案系統操作的高級瞭解。 如果上述日誌仍未提供有用的資訊，或者要調查 blob 存儲 api 調用，請添加到`fs.azure.storage.client.logging=true`。 `core-site` 此設置將啟用 wasb 存儲驅動程式的 java sdk 日誌，並將每個調用列印到 blob 存儲伺服器。 調查後刪除該設置，因為它可能會快速填滿磁片，並可能減慢進程。

如果後端基於 Azure 資料湖，則對元件使用以下 log4j 設置（例如，火花/tez/hdfs）：

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

查找日誌中的日誌`/var/log/adl/adl.log`。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
