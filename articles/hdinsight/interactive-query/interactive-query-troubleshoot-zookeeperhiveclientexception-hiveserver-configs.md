---
title: Apache Hive Zeppelin 解譯器錯誤-Azure HDInsight
description: Apache Zeppelin Hive JDBC 解譯器指向 Azure HDInsight 中的錯誤 URL
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 2d6e660459d9f350cc5c63fb8d312bcbcb300a64
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930746"
---
# <a name="scenario-apache-hive-zeppelin-interpreter-gives-a-zookeeper-error-in-azure-hdinsight"></a>案例： Apache Hive Zeppelin 解譯器在 Azure HDInsight 中提供 Zookeeper 錯誤

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

在 Apache Hive LLAP 叢集上，Zeppelin 解譯器會在嘗試執行查詢時提供下列錯誤訊息：

```
java.sql.SQLException: org.apache.hive.jdbc.ZooKeeperHiveClientException: Unable to read HiveServer2 configs from ZooKeeper
```

## <a name="cause"></a>原因

Zeppelin Hive JDBC 解譯器指向錯誤的 URL。

## <a name="resolution"></a>解決方案

1. 流覽至 [Hive 元件摘要]，並將 [Hive JDBC Url] 複製到剪貼簿。

1. 巡覽到 `https://clustername.azurehdinsight.net/zeppelin/#/interpreter`

1. 編輯 JDBC 設定：將 hive. url 值更新為在步驟1中複製的 Hive JDBC URL

1. 儲存，然後重試查詢

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]