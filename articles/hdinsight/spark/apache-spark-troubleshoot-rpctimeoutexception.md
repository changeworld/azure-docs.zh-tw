---
title: RpcTimeout 阿帕奇火花節儉例外 - Azure HDInsight
description: 使用 Apache Spark 節儉伺服器處理大型資料集時，您會看到 502 個錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894272"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>方案：Azure HDInsight 中 Apache Spark 節儉伺服器的 RpcTimeout 例外

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

Spark 應用程式失敗，`org.apache.spark.rpc.RpcTimeoutException`但有一個異常消息`Futures timed out`和消息： ，如以下示例所示：

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`錯誤`overhead limit exceeded`也可能出現在`sparkthriftdriver.log`以下示例中：

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

這些錯誤是由資料處理過程中缺少記憶體資源引起的。 如果 JAVA 垃圾回收過程啟動，則可能導致 Spark 應用程式掛起。 查詢將開始超時並停止處理。 該`Futures timed out`錯誤表示群集處於嚴重應力之下。

## <a name="resolution"></a>解決方案

通過增加輔助節點或增加現有叢集節點的記憶體容量來增加群集大小。 您還可以調整資料管道以減少一次處理的資料量。

控制`spark.network.timeout`所有網路連接的超時。 增加網路超時可能允許更多時間完成某些關鍵操作，但這不能完全解決問題。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
