---
title: 請求來自阿帕奇火花應用的BodyTooLarge錯誤 - Azure HDInsight
description: 本機 Azure 檔案系統 ...請求BodyTooLarge顯示在 Azure HDInsight 中 Apache Spark 流式處理應用的日誌中
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894391"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"本機 Azure 檔案系統...請求BodyTooLarge"出現在 Apache Spark 流式處理應用日誌中 HDInsight 中

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

錯誤：`NativeAzureFileSystem ... RequestBodyTooLarge`顯示在 Apache Spark 流應用的驅動程式日誌中。

## <a name="cause"></a>原因

Spark 事件日誌檔可能達到 WASB 的檔長度限制。

在 Spark 2.3 中，每個 Spark 應用生成一個 Spark 事件日誌檔。 當應用運行時，Spark 流式處理應用的 Spark 事件日誌檔將繼續增長。 今天，WASB 上的檔具有 50000 塊限制，預設塊大小為 4 MB。 因此，在預設配置中，最大檔案大小為 195 GB。 但是，Azure 存儲將最大塊大小增加到 100 MB，這實際上將單個檔限制增加到 4.75 TB。 如需詳細資訊，請參閱 [Blob 儲存體的延展性和效能目標](../../storage/blobs/scalability-targets.md)。

## <a name="resolution"></a>解決方案

此錯誤有三種可用解決方案：

* 將塊大小增加到高達 100 MB。 在 Ambari UI 中，修改`fs.azure.write.request.size`HDFS 配置`Custom core-site`屬性（或在部分中創建）。 將屬性設置為較大的值，例如：33554432。 保存更新的配置並重新啟動受影響的元件。

* 定期停止並重新提交火花流作業。

* 使用 HDFS 存儲 Spark 事件日誌。 在群集縮放或 Azure 升級期間，將 HDFS 用於存儲可能會導致 Spark 事件資料丟失。

    1. 對`spark.eventlog.dir`Ambari `spark.history.fs.logDirectory` UI 進行更改，並通過 Ambari UI 進行更改：

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. 在 HDFS 上創建目錄：

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. 通過 Ambari UI 重新開機所有受影響的服務。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
