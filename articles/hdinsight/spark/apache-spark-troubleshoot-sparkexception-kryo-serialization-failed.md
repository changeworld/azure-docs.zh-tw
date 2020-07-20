---
title: JDBC/ODBC 與 Apache Thrift 架構的問題 - Azure HDInsight
description: 無法在 Azure HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 軟體架構下載大型資料集
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/14/2020
ms.openlocfilehash: a8dcd6ae844810213ed6706002cdb9a31de94f60
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653594"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>無法在 HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 軟體架構下載大型資料集

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

嘗試在 Azure HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 軟體架構下載大型資料集時，您會收到類似如下的錯誤訊息：

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>原因

此例外狀況是由於序列化流程嘗試使用超過允許的緩衝區空間所造成。 在 Spark 2.0.0 中，透過 Apache Spark 軟體架構存取資料時，會針對序列化物件使用類別 `org.apache.spark.serializer.KryoSerializer`。 透過網路傳送或以序列化形式快取的資料，會使用不同的類別。

## <a name="resolution"></a>解決方案

增加 `Kryoserializer` 緩衝區值。 在 `Custom spark2-thrift-sparkconf` 下的 spark2 組態中，新增名為 `spark.kryoserializer.buffer.max` 的索引鍵，並將其設為 `2047`。 重新啟動所有受影響的元件。

> [!IMPORTANT]
> `spark.kryoserializer.buffer.max` 的值必須小於 2048。 不支援小數值。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列中選取 [支援] 或開啟 [說明 + 支援] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您可透過 Microsoft Azure 訂閱來存取訂閱管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
