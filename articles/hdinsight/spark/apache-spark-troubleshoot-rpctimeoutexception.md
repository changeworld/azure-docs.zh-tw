---
title: 適用于 Apache Spark thrift 的 RpcTimeoutException-Azure HDInsight
description: 使用 Apache Spark thrift server 處理大型資料集時，您會看到502錯誤
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: fdf8a6d36d6b9d112ef5b3ea50991ac7d98fcbae
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547260"
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

## <a name="resolution"></a>解決方案

藉由新增更多背景工作節點或增加現有叢集節點的記憶體容量，來增加叢集大小。 您也可以調整資料管線，以減少一次處理的資料量。

會 `spark.network.timeout` 控制所有網路連接的超時時間。 增加網路延遲可能會讓某些重要作業的時間變得更久，但這並不會完全解決問題。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。