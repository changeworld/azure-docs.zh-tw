---
title: 從 Apache Spark Azure HDInsight InvalidClassException 錯誤
description: Apache Spark 作業失敗，InvalidClassException，類別版本不符，Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: be50f8716835b0842f854842e5340b0bb8594136
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75894356"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark 作業失敗，InvalidClassException，類別版本不符，Azure HDInsight

本文說明在 Azure HDInsight 叢集中使用 Apache Spark 元件時，疑難排解步驟和可能的解決方案。

## <a name="issue"></a>問題

您嘗試在 Spark 2.x 叢集中建立 Apache Spark 作業。 它會失敗並出現類似下列的錯誤：

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

此錯誤的原因可能是將額外的 jar 新增至設定 `spark.yarn.jars` ，特別是包含不同版本 `commons-lang3` 套件並引進類別不相符的陰影 jar。 根據預設，Spark 2.1/2/3 會使用3.5 版 `commons-lang3` 。

> [!TIP]
> 若要為文件庫加上陰影，請將其內容放入您自己的 jar 中，變更其套件。 這與封裝程式庫不同，因為它會將程式庫放入您自己的 jar，而不需要重新封裝。

## <a name="resolution"></a>解決方案

請移除 jar，或重新編譯自訂的 jar （AzureLogAppender），並使用[maven-陰影-外掛程式](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html)來重新放置類別。

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。
