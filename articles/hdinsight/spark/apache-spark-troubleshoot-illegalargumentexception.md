---
title: Apache Spark Azure HDInsight 的 IllegalArgumentException 錯誤
description: 適用于 Azure Data Factory 的 Azure HDInsight 中 Apache Spark 活動的 IllegalArgumentException
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 61190a932389b429040d6b643db2dd2732b2c41d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287901"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>案例： IllegalArgumentException Azure HDInsight 中的 Apache Spark 活動

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

當您嘗試在 Azure Data Factory 管線中執行 Spark 活動時，會收到下列例外狀況：

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>原因

如果應用程式 jar 檔案不位於 Spark 叢集的預設/主要儲存體中，Spark 作業將會失敗。

這是在此 bug 中追蹤的 Spark 開放原始碼架構的已知問題： [如果 fs.defaultfs 和應用程式 jar 不同的 url，spark 作業會失敗](https://issues.apache.org/jira/browse/SPARK-22587)。

此問題已在 Spark 2.3.0 中解決。

## <a name="resolution"></a>解決方法

請確定應用程式 jar 儲存在 HDInsight 叢集的預設/主要儲存體上。 如果 Azure Data Factory，請確定 ADF 連結服務指向 HDInsight 預設容器，而不是次要容器。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]