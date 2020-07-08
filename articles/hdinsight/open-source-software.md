---
title: Azure HDInsight 中的開放原始碼軟體支援
description: Microsoft Azure 可為開放原始碼技術提供一般層級的支援。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: f1e1518cce060374f14f1db7d5f4e594e3de8f85
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086257"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Azure HDInsight 中的開放原始碼軟體支援

Microsoft Azure HDInsight 服務會使用以 Apache Hadoop 為中心的開放原始碼技術環境。 Microsoft Azure 可為開放原始碼技術提供一般層級的支援。 如需詳細資訊，請參閱 [Azure 支援常見問題集](https://azure.microsoft.com/support/faq/)的**支援範圍**一節。 HDInsight 服務針對內建元件提供額外等級的支援。

## <a name="components"></a>元件

HDInsight 服務中有兩種類型的開放原始碼元件可供使用：

### <a name="built-in-components"></a>內建元件

這些元件會預先安裝在 HDInsight 叢集上，並且提供叢集的核心功能。 以下是屬於此類別的元件：

* [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager。
* Hive 查詢語言 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)。
* [Apache Mahout](https://mahout.apache.org/)。

如需完整的叢集元件清單，請參閱[可以搭配 HDInsight 使用的 Apache Hadoop 元件和版本有哪些？](hdinsight-component-versioning.md)

### <a name="custom-components"></a>自訂元件

身為叢集的使用者，您可以安裝或在工作負載中使用社群中可用或您建立的任何元件。

> [!WARNING]  
> 對隨 HDInsight 叢集提供的元件會有完整支援。 Microsoft 支援服務可協助隔離和解決這些元件的相關問題。
>
> 自訂元件則會獲得商務上合理的支援，以協助您進一步針對問題進行疑難排解。 「Microsoft 支援服務」可能能夠解決問題。 或是可能要求您參與可提供該技術深度專業知識的可用開放原始碼技術管道。 您可以使用許多社群網站。 範例為[適用於 HDInsight 的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-hdinsight.html)和 [Stack Overflow](https://stackoverflow.com)。
>
> 此外，Apache 專案在 [Apache 網站](https://apache.org)上也有專案網站。 例如 [Hadoop](https://hadoop.apache.org/)。

## <a name="component-usage"></a>元件使用方式

HDInsight 服務提供數種方式以使用自訂元件。 不論元件在叢集上的使用或安裝方式為何，都適用相同層級的支援。 下表說明自訂元件在 HDInsight 叢集上的最常見使用方式：

|使用量 |描述 |
|---|---|
|作業提交|可將執行或使用自訂元件的 Hadoop 或其他類型的作業提交給叢集。|
|叢集自訂|在叢集建立期間，您可以指定額外設定及安裝在叢集節點上的自訂元件。|
|範例|針對熱門的自訂元件，Microsoft 和其他提供者可能會提供如何在 HDInsight 叢集上使用這些元件的範例。 提供這些範例，但是沒有支援。|

## <a name="next-steps"></a>後續步驟

* [使用指令碼動作來自訂 Azure HDInsight 叢集](./hdinsight-hadoop-customize-cluster-linux.md)
* [開發 HDInsight 的指令碼動作指令碼](hdinsight-hadoop-script-actions-linux.md)
* [使用指令碼動作在 Azure HDInsight 上安全地管理 Python 環境](./spark/apache-spark-python-package-installation.md)
