---
title: Azure HDInsight 的版本資訊
description: Azure HDInsight 的最新版本資訊。 取得 Hadoop、Spark、R Server、Hive 等的開發秘訣和詳細資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ef243d5b151f95a00e22ac7636a46b93090ccce3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006529"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight 版本資訊

本文提供有關**最新** Azure HDInsight 版本更新的資訊。 如需有關較早版本的詳細資訊，請參閱 [HDInsight 版本資訊封存](hdinsight-release-notes-archive.md)。

## <a name="summary"></a>摘要

Azure HDInsight 是最受企業客戶歡迎的其中一項服務，可供 Azure 上的開放原始碼分析使用。

## <a name="release-date-07132020"></a>發行日期：07/13/2020

此發行適用於 HDInsight 3.6 和 4.0。 在數天內，所有區域都可以使用 HDInsight 發行版本。 此處的發行日期為第一個區域發行日期。 如果您沒有看到下列變更，請在數天內等待發行在您的區域中上線。

## <a name="new-features"></a>新功能
### <a name="support-for-customer-lockbox-for-microsoft-azure"></a>支援 Microsoft Azure 的客戶加密箱
Azure HDInsight 現在支援 Azure 客戶加密箱。 它提供一個介面，讓客戶可以審查及核准或拒絕客戶資料存取要求。 當 Microsoft 工程師需要在支援要求期間存取客戶資料時，會使用此檔案。 如需詳細資訊，請參閱[Microsoft Azure 的客戶加密箱](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview)。

### <a name="service-endpoint-policies-for-storage"></a>儲存體的服務端點原則
客戶現在可以使用 HDInsight 叢集子網上的服務端點原則（SEP）。 深入瞭解[Azure 服務端點原則](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)。

## <a name="deprecation"></a>淘汰
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>淘汰 HDInsight 3.6 Spark 叢集中的 Spark 2.1 和 2.2
從 1 2020 年7月開始，客戶無法在 HDInsight 3.6 上使用 Spark 2.1 和2.2 建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 3.6 上移至 Spark 2.3，以避免潛在的系統/支援中斷。
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>淘汰 HDInsight 4.0 Spark 叢集中的 Spark 2.3
從 1 2020 年7月開始，客戶將無法使用 Spark 2.3 on HDInsight 4.0 來建立新的 Spark 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Spark 2.4，以避免潛在的系統/支援中斷。
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>淘汰 HDInsight 4.0 Kafka 叢集中的 Kafka 1.1
自 2020 年 7 月 1 日開始，客戶將無法在 HDInsight 4.0 上使用 Kafka 1.1 建立新的 Kafka 叢集。 現有的叢集將會以現狀執行，不再有 Microsoft 支援。 請考慮在 2020 年 6 月 30 日前於 HDInsight 4.0 上移至 Kafka 2.1，以避免潛在的系統/支援中斷。

## <a name="behavior-changes"></a>行為變更
您不需要注意任何行為變更。

## <a name="upcoming-changes"></a>即將推出的變更
即將發行的版本中將會發生下列變更。 

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>能夠為 Spark、Hadoop 和 ML 服務選取不同的 Zookeeper SKU
目前 HDInsight 不支援變更 Spark、Hadoop 和 ML 服務叢集類型的 Zookeeper SKU。 它會使用適用于 Zookeeper 節點的 A2_v2/A2 SKU，而不會向客戶收取費用。 在即將推出的版本中，客戶將能夠視需要變更 Spark、Hadoop 和 ML 服務的 Zookeeper SKU。 具有 A2_v2/A2 以外 SKU 的 Zookeeper 節點將會收費。 預設 SKU 仍會 A2_V2/A2，而且免費。

## <a name="bug-fixes"></a>錯誤修正
HDInsight 會持續改善叢集的可靠性和效能。 
### <a name="fixed-hive-warehouse-connector-issue"></a>已修正 Hive 倉儲連接器問題
先前版本中的 Hive 倉儲連接器可用性發生問題。 已修正此問題。 

### <a name="fixed-zeppelin-notebook-truncates-leading-zeros-issue"></a>已修正 Zeppelin 筆記本截斷前置零問題
Zeppelin 不正確地截斷資料表輸出中的前置零，以取得字串格式。 我們已在此版本中修正此問題。

## <a name="component-version-change"></a>元件版本變更
此發行版本沒有任何元件版本變更。 您可以在[本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)檔中找到 hdinsight 4.0 和 hdinsight 3.6 的目前元件版本。
