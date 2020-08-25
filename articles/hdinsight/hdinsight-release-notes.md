---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: ba3c7304b0ce52cb720a9e2ac7ae6cf1b5ffeeea
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751466"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼分析使用。

## <a name="release-date-08092020"></a>發行日期：08/09/2020

此版本僅適用于 HDInsight 4.0。 在數天內，所有區域都可以使用 HDInsight 發行版本。 此處的發行日期為第一個區域發行日期。 如果您沒有看到以下變更，請在數天內等待發行在您的區域中。

## <a name="new-features"></a>新功能
### <a name="support-for-sparkcruise"></a>對 SparkCruise 的支援
SparkCruise 是適用于 Spark 的自動計算重複使用系統。 它會根據過去的查詢工作負載，選取要具體化的一般子運算式。 SparkCruise 會具體化這些子運算式作為查詢處理的一部分，而計算重複使用會自動套用到背景中。 您可以從 SparkCruise 受益，而不需要對 Spark 程式碼進行任何修改。
 
### <a name="support-hive-view-for-hdinsight-40"></a>支援 HDInsight 4.0 的 Hive 視圖
Apache Ambari Hive View 的設計目的是協助您從網頁瀏覽器撰寫、優化和執行 Hive 查詢。 從這個版本開始，HDInsight 4.0 叢集原本就支援 Hive View。 不適用於現有的叢集。 您需要卸載再重新建立叢集，才能取得內建的 Hive 視圖。
 
### <a name="support-tez-view-for-hdinsight-40"></a>支援 HDInsight 4.0 的 Tez View
Apache Tez View 可用來追蹤和偵測 Hive Tez 作業的執行。 從這個版本開始，HDInsight 4.0 原本就支援 Tez View。 不適用於現有的叢集。 您必須卸載並重新建立叢集，才能取得內建的 Tez 視圖。

## <a name="deprecation"></a>淘汰
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>淘汰 HDInsight 3.6 Spark 叢集中的 Spark 2.1 和 2.2
從 1 2020 年7月開始，客戶無法使用 Spark 2.1 和 2.2 on HDInsight 3.6 建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 3.6 上移至 Spark 2.3，以避免潛在的系統/支援中斷。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>淘汰 HDInsight 4.0 Spark 叢集中的 Spark 2.3
從 1 2020 年7月開始，客戶無法使用 Spark 2.3 on HDInsight 4.0 來建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Spark 2.4，以避免潛在的系統/支援中斷。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>淘汰 HDInsight 4.0 Kafka 叢集中的 Kafka 1.1
自 2020 年 7 月 1 日開始，客戶將無法在 HDInsight 4.0 上使用 Kafka 1.1 建立新的 Kafka 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Kafka 2.1，以避免潛在的系統/支援中斷。

## <a name="behavior-changes"></a>行為變更
### <a name="ambari-stack-version-change"></a>Ambari stack 版本變更
從這個版本開始，Ambari 版本會從2.x 變更為4.1。 您可以從 Ambari UI 取得 Ambari 版本的相關 >。

## <a name="upcoming-changes"></a>即將推出的變更
不需要注意任何即將推出的重大變更。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會持續改善叢集的可靠性和效能。 

以下是 Hive 的 JIRAs：
* [HIVE-23619](https://issues.apache.org/jira/browse/HIVE-23619)
* [HIVE-21223](https://issues.apache.org/jira/browse/HIVE-21223)
* [HIVE-22599](https://issues.apache.org/jira/browse/HIVE-22599)
* [HIVE-22121](https://issues.apache.org/jira/browse/HIVE-22121)
* [HIVE-22136](https://issues.apache.org/jira/browse/HIVE-22136)
* [HIVE-18786](https://issues.apache.org/jira/browse/HIVE-18786)

以下是適用于 HBase 的 JIRAs：
* [HBASE-21458](https://issues.apache.org/jira/browse/HBASE-21458)
* [HBASE-24208](https://issues.apache.org/jira/browse/HBASE-24208)
* [HBASE-24205](https://issues.apache.org/jira/browse/HBASE-24205)

## <a name="component-version-change"></a>元件版本變更
此發行版本沒有任何元件版本變更。 您可以在 [本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)檔中找到 hdinsight 4.0 和 hdinsight 3.6 目前的元件版本。

## <a name="known-issues"></a>已知問題

嘗試從 Azure 入口網站建立 Azure HDInsight 叢集並使用公開金鑰的 SSH 驗證類型時，當使用者按一下 [ **審核 + 建立**] 時，就會發生錯誤。 入口網站中的錯誤為「不得包含來自 SSH 使用者名稱的三個連續字元」。 正在解決此問題;但是，如果您遇到此錯誤，因應措施是使用 ARM 範本建立叢集。 
