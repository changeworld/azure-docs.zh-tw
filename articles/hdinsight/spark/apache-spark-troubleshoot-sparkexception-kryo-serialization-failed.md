---
title: JDBC/ODBC & Apache Thrift 架構的問題-Azure HDInsight
description: 無法在 Azure HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 軟體架構下載大型資料集
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894266"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>無法在 HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 軟體架構下載大型資料集

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和問題的可能解決方法。

## <a name="issue"></a>問題

嘗試在 Azure HDInsight 中使用 JDBC/ODBC 和 Apache Thrift 軟體架構下載大型資料集時，您會收到類似如下的錯誤訊息：

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>原因

此例外狀況是因為序列化程式嘗試使用超過允許的緩衝區空間所造成。 在 Spark 2.0.0 中，當`org.apache.spark.serializer.KryoSerializer`透過 Apache Thrift software framework 存取資料時，會使用類別來序列化物件。 將透過網路傳送或以序列化形式快取的資料，會使用不同的類別。

## <a name="resolution"></a>解決方法

增加`Kryoserializer`緩衝區值。 新增名為`spark.kryoserializer.buffer.max`的索引鍵，並`2048`將它設定為`Custom spark2-thrift-sparkconf`下 spark2 config 中的。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過[Azure 社區支援](https://azure.microsoft.com/support/community/)取得 azure 專家的解答。

* 連接[@AzureSupport](https://twitter.com/azuresupport) -官方 Microsoft Azure 帳戶，藉由將 Azure 社區連接至適當的資源來改善客戶體驗：解答、支援及專家。

* 如果您需要更多協助，您可以從[Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援要求。 從功能表列選取 [**支援**]，或開啟 [說明 **+ 支援**] 中樞。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 您的 Microsoft Azure 訂用帳戶包含訂用帳戶管理和帳單支援的存取權，而技術支援則透過其中一項[Azure 支援方案](https://azure.microsoft.com/support/plans/)提供。
