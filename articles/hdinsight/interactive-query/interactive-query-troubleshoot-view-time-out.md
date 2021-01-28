---
title: 從查詢結果 Apache Hive 查看時間-Azure HDInsight
description: 在 Azure HDInsight 中提取查詢結果時 Apache Hive View 次
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 474fa5e084acfa508a4391075b3c78d96b01aa46
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930733"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>案例：在 Azure HDInsight 中提取查詢結果時 Apache Hive View 次

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

從 Apache Hive 視圖執行特定查詢時，可能會發生下列錯誤：

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>原因

Hive View default timeout 值可能不適合您正在執行的查詢。 指定的時間週期太短，無法讓 Hive 視圖提取查詢結果。

## <a name="resolution"></a>解決方案

藉由在中設定下列屬性，以增加 Apache Ambari Hive 的查看超時 `/etc/ambari-server/conf/ambari.properties` 。

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive.<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

的值 `HIVE_VIEW_INSTANCE_NAME` 可在 Hive VIEW URL 的結尾取得。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]