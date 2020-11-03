---
title: 適用于 Apache Spark thrift 的 RpcTimeoutException-Azure HDInsight
description: 使用 Apache Spark thrift server 處理大型資料集時，您會看到502錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b4b5268bef71bd6b23df6049fd3ac8af973d4385
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287748"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>案例： Azure HDInsight 中 Apache Spark thrift 伺服器的 RpcTimeoutException

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

Spark 應用程式失敗， `org.apache.spark.rpc.RpcTimeoutException` 並出現例外狀況和訊息： `Futures timed out` ，如下列範例所示：

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError` 和 `overhead limit exceeded` 錯誤也會出現在中， `sparkthriftdriver.log` 如下列範例所示：

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>原因

這些錯誤是因為資料處理期間記憶體資源不足所造成。 如果 JAVA 垃圾收集處理常式啟動，可能會導致 Spark 應用程式停止回應。 查詢將會開始時間並停止處理。 此 `Futures timed out` 錯誤表示在嚴重壓力下的叢集。

## <a name="resolution"></a>解決方法

藉由新增更多背景工作節點或增加現有叢集節點的記憶體容量，來增加叢集大小。 您也可以調整資料管線，以減少一次處理的資料量。

會 `spark.network.timeout` 控制所有網路連接的超時時間。 增加網路延遲可能會讓某些重要作業的時間變得更久，但這並不會完全解決問題。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]