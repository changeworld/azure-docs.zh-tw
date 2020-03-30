---
title: JDBC/ODBC &阿帕奇節儉框架的問題 - Azure HDInsight
description: 無法使用 Azure HDInsight 中的 JDBC/ODBC 和 Apache 節儉軟體框架下載大型資料集
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 23693dcae2f361b88440ec88ca39fd8ed229d85a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894266"
---
# <a name="unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-hdinsight"></a>無法使用 HDInsight 中的 JDBC/ODBC 和 Apache 節儉軟體框架下載大型資料集

本文介紹了在 Azure HDInsight 群集中使用 Apache Spark 元件時，故障排除步驟和可能解決的問題。

## <a name="issue"></a>問題

在 Azure HDInsight 中嘗試使用 JDBC/ODBC 和 Apache Thrift 軟體框架下載大型資料集時，您會收到類似于下面的錯誤訊息：

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>原因

此異常是由序列化過程嘗試使用比允許的緩衝區空間多引起的。 在 Spark 2.0.0`org.apache.spark.serializer.KryoSerializer`中，當通過 Apache Thrift 軟體框架訪問資料時，該類用於序列化物件。 不同的類用於通過網路發送或以序列化形式緩存的資料。

## <a name="resolution"></a>解決方案

增加`Kryoserializer`緩衝區值。 添加名為`spark.kryoserializer.buffer.max`的鍵，並將其設置為`2048`在 的 spark2`Custom spark2-thrift-sparkconf`配置下。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 通過[Azure 社區支援](https://azure.microsoft.com/support/community/)從 Azure 專家那裡獲得答案。

* 與[@AzureSupport](https://twitter.com/azuresupport)- 正式的 Microsoft Azure 帳戶連接 Azure 社區，以將 Azure 社區連接到正確的資源：答案、支援和專家，從而改善客戶體驗。

* 如果需要更多説明，可以從[Azure 門戶](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支援請求。 從功能表列中選擇 **"支援"** 或打開 **"説明 + 支援**中心"。 有關詳細資訊，請查看[如何創建 Azure 支援請求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)。 Microsoft Azure 訂閱中包含對訂閱管理和計費支援的訪問，並且通過[Azure 支援計畫](https://azure.microsoft.com/support/plans/)之一提供技術支援。
