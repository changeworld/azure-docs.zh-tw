---
title: Azure HDInsight 中的 Apache Hive LLAP 查詢效能不佳
description: Apache Hive LLAP 中的查詢執行速度比 Azure HDInsight 的預期慢。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288875"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>案例： Azure HDInsight 中 Apache Hive LLAP 查詢的效能不佳

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

預設叢集設定未充分調整您的工作負載。 Hive LLAP 中的查詢執行速度比預期慢。

## <a name="cause"></a>原因

這可能是因為各種原因而發生。

## <a name="resolution"></a>解決方法

LLAP 已針對牽涉到聯結和匯總的查詢進行優化。 下列查詢在互動式 Hive 叢集中的執行效果不佳：

```
select * from table where column = "columnvalue"
```

若要改善 Hive LLAP 中的點查詢效能，請設定下列設定：

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

您也可以使用下列設定變更來增加 LLAP 快取的使用量，以改善效能：

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]