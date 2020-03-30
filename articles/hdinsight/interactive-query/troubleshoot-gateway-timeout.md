---
title: 在 Azure HDInsight 中運行來自 Apache Ambari Hive 視圖的查詢時的異常
description: 在 Azure HDInsight 中通過 Apache Ambari Hive 視圖運行 Apache Hive 查詢時，需要執行故障排除步驟。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 12/23/2019
ms.openlocfilehash: 809b2e383eb57b730fd76ec2194764178aa810c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895041"
---
# <a name="exception-when-running-queries-from-apache-ambari-hive-view-in-azure-hdinsight"></a>在 Azure HDInsight 中運行來自 Apache Ambari Hive 視圖的查詢時的異常

本文介紹了與 Azure HDInsight 群集交互時問題的故障排除步驟和可能的解決方案。

## <a name="issue"></a>問題

從 Apache Ambari Hive 視圖運行 Apache Hive 查詢時，您間歇性地收到以下錯誤訊息：

```error
Cannot create property 'errors' on string '<!DOCTYPE html PUBLIC '-//W3C//DTD XHTML 1.0 Strict//EN' 'http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'>
<html xmlns='http://www.w3.org/1999/xhtml'>
<head>
<title>IIS 8.5 Detailed Error - 502.3 - Bad Gateway</title>
```

## <a name="cause"></a>原因

閘道超時。

閘道超時值為 2 分鐘。 來自 Ambari Hive 視圖的查詢通過`/hive2`閘道提交到終結點。 成功編譯和接受查詢後，HiveServer 將返回 。 `queryid` 然後，用戶端保留查詢狀態的輪詢。 在此過程中，如果 HiveServer 在 2 分鐘內未返回 HTTP 回應，則 HDI 閘道會向呼叫者引發 502.3 閘道逾時錯誤。 當提交查詢進行處理（更有可能）時，以及獲取狀態調用（不太可能）時，可能會發生錯誤。 使用者可以看到其中任一。

HTTP 處理常式執行緒應該是快速的：準備作業並返回 。 `queryid` 但是，由於多種原因，所有處理常式執行緒可能都很忙，從而導致新查詢和 get 狀態調用超時。

### <a name="responsibilities-of-the-http-handler-thread"></a>HTTP 處理常式執行緒的職責

當用戶端向 HiveServer 提交查詢時，它在前臺執行緒中執行以下操作：

* 解析請求，進行語意驗證
* 獲取鎖
* 如有必要，元存儲查找
* 編譯查詢（DDL 或 DML）
* 準備查詢計劃
* 執行授權（在安全群集中運行所有適用的遊俠策略）

## <a name="resolution"></a>解決方案

一些一般性的建議，你改善的情況：

* 如果使用外部配置單元元存儲，請檢查資料庫指標並確保資料庫未超載。 請考慮縮放元存儲資料庫層。

* 確保平行作業處於打開狀態（這使 HTTP 處理常式執行緒能夠並行運行）。 要驗證該值，請啟動[Apache Ambari](../hdinsight-hadoop-manage-ambari.md)並導航到**Hive** > **Configs** > **高級** > **自訂配置單元網站**。 的值`hive.server2.parallel.ops.in.session`應為`true`。

* 確保群集的 VM SKU 不會太小，無法進行負載。 請考慮在多個群集之間拆分工作。 有關詳細資訊，請參閱[選擇群集類型](../hdinsight-capacity-planning.md#choose-a-cluster-type)。

* 如果群集上安裝了 Ranger，請檢查是否每個查詢需要計算的 Ranger 策略太多。 查找重複或不需要的策略。

* 從 Ambari 檢查**HiveServer2 堆大小**值。 導航到**蜂巢** > **配置** > **設置** > **優化**。 確保該值大於 10 GB。 根據需要進行調整以優化性能。

* 確保蜂巢查詢已調整良好。 有關詳細資訊，請參閱在[Azure HDInsight 中優化 Apache Hive 查詢](../hdinsight-hadoop-optimize-hive-query.md)。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 用於改善客戶體驗的官方 Microsoft Azure 帳戶連接。 將 Azure 社區連接到正確的資源：答案、支援和專家。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
