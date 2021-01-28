---
title: 從 Apache Spark Azure HDInsight InvalidClassException 錯誤
description: Apache Spark 作業因 InvalidClassException、類別版本不符而失敗，Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 0b0889ac1e71ce33406e89ead62370a0c0168763
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929261"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark 作業因 InvalidClassException、類別版本不符而失敗，Azure HDInsight

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

您嘗試在 Spark 2.x 叢集中建立 Apache Spark 工作。 它會失敗，並出現類似下列的錯誤：

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>原因

這項錯誤的原因可能是將額外的 jar 新增至設定 `spark.yarn.jars` ，特別是包含不同版本套件的陰影 jar， `commons-lang3` 並引進類別不符的情況。 Spark 2.1/2/3 預設會使用3.5 版 `commons-lang3` 。

> [!TIP]
> 若要為程式庫加上陰影，請將其內容放入您自己的 jar，以變更其套件。 這不同于封裝程式庫，這會將程式庫放入您自己的 jar，而不需要重新封裝。

## <a name="resolution"></a>解決方案

請移除 jar，或重新編譯自訂 jar (AzureLogAppender) 並使用 [maven-陰影-外掛程式](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) 來重新置放類別。

## <a name="next-steps"></a>後續步驟

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]