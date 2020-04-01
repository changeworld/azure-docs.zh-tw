---
title: 將阿帕契卡夫卡工作負載遷移到 Azure HDInsight 4.0
description: 瞭解如何在 HDInsight 3.6 上將 Apache Kafka 工作負載遷移到 HDInsight 4.0。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437003"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>將阿帕契卡夫卡工作負載遷移到 Azure HDInsight 4.0

Azure HDInsight 4.0 提供最新的開源元件,在性能、連接性和安全性方面有顯著增強。 本文件介紹如何在 HDInsight 3.6 上將 Apache Kafka 工作負載遷移到 HDInsight 4.0。 將工作負載遷移到 HDInsight 4.0 後,您可以使用 HDInsight 3.6 上不可用的許多新功能。

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 卡夫卡遷移路徑

HDInsight 3.6 支援卡夫卡的兩個版本:1.0.0 和 1.1.0。 HDInsight 4.0 支援版本 1.1.0 和 2.1.0。 根據 Kafka 的哪個版本以及要運行的 HDInsight 版本,有多個受支援的遷移路徑。 下面將介紹這些路徑,並在下圖中說明。

* **在最新版本(推薦)上運行 Kafka 和 HDInsight:** 將 HDInsight 3.6 和 Kafka 1.0 或 1.1.0 應用程式遷移到 HDInsight 4.0,卡夫卡 2.1.0(路徑 D 和 E 如下)。
* **在最新版本上運行 HDInsight,但卡夫卡僅在較新版本上運行 HDInsight:** 將 HDInsight 3.6 和卡夫卡 1.0.0 應用程式遷移到 HDInsight 4.0,卡夫卡 1.1.0(下面的路徑 B)。
* **在最新版本上運行 HDInsight,保留卡夫卡版本**:將 HDInsight 3.6 和卡夫卡 1.1.0 應用程式遷移到 HDInsight 4.0,卡夫卡 1.1.0(路徑 C 如下)。
* **在較新版本上運行卡夫卡,保留 HDInsight 版本**:將 Kafka 1.0.0 應用程式遷移到 1.1.0,並保留 HDInsight 3.6(下面的路徑 A)。 請注意,此選項仍然需要部署新群集。 不支援在現有群集上升級 Kafka 版本。 使用所需的版本創建群集后,遷移 Kafka 用戶端以使用新群集。

![3.6 上阿帕奇卡夫卡的升級路徑](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>阿帕契卡夫卡版本

### <a name="kafka-110"></a>卡夫卡 1.1.0
  
如果您從 Kafka 1.0.0 移至 1.1.0,則可以利用以下新功能:

* Kafka 控制器的改進可加快受控關機,因此您可以重新啟動代理並更快地從問題中恢復。 
* [Fetch請求邏輯](https://issues.apache.org/jira/browse/KAFKA-6254)的改進,使您能夠在群集上擁有更多的分區(因此會有更多的主題)。 
* Kafka Connect 支援主題[的記錄標頭](https://issues.apache.org/jira/browse/KAFKA-5142)與[正規表示式](https://issues.apache.org/jira/browse/KAFKA-3073)。 

有關更新的完整清單,請參閱[Apache Kafka 1.1 發行說明](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)。

### <a name="apache-kafka-210"></a>阿帕奇卡夫卡 2.1.0

如果您移到 Kafka 2.1,則可以利用以下功能:

* 由於改進的複製協定,代理恢復能力更好。
* KafkaAdminClient API 中的新功能。
* 可配置配額管理。
* 支援 Z 標準壓縮。

有關更新的完整清單,請參閱[Apache Kafka 2.0 發行說明](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html)與[Apache Kafka 2.1 發行說明](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)。

## <a name="kafka-client-compatibility"></a>卡夫卡客戶端相容性

新的卡夫卡經紀人支援老客戶。 [KIP-35 - 檢索協定版本](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version)引入了一種動態確定 Kafka 代理和[KIP-97 功能的機制:改進的 Kafka 用戶端 RPC 相容性策略](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy)引入了新的相容性策略和 Java 客戶端的保證。 以前,Kafka 客戶端必須與同一版本或較新版本的代理進行交互。 現在,Java 用戶端和支援 KIP-35 的其他用戶端的較新`librdkafka`版本(如 )可能會回落到較舊的請求類型,或者如果功能不可用,則引發適當的錯誤。

![升級卡夫卡客戶端相容性](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

請注意,這並不意味著客戶端支援較舊的代理。  有關詳細資訊,請參閱[相容性矩陣](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)。

## <a name="general-migration-process"></a>一般移至程序

以下遷移指南假定在單個虛擬網路中部署在 HDInsight 3.6 上的 Apache Kafka 1.0.0 或 1.1.0 群集。 現有的經紀人有一些主題,並正在積極使用生產者和消費者。

![目前卡夫卡假定環境](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

要完成移轉,執行以下步驟:

1. **部署新的 HDInsight 4.0 群集和客戶端進行測試。** 部署新的 HDInsight 4.0 卡夫卡群集。 如果可以選擇多個 Kafka 群集版本,建議選擇最新版本。 部署後,根據需要設置一些參數,並創建與現有環境同名的主題。 此外,根據需要設置 TLS 並自帶密鑰 (BYOK) 加密。 然後檢查它與新群集是否正常工作。

    ![部署新的 HDInsight 4.0 叢集](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **切換生產者應用程式的群集,並等待當前消費者使用所有佇列數據。** 當新的 HDInsight 4.0 Kafka 群集準備就緒時,將現有生產者目標切換到新群集。 保持現有使用者應用消耗現有群集中的所有數據為止。

    ![為生產者應用切換叢集](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **在使用者應用程式上切換群集。** 確認現有使用者應用程式已完成使用現有群集的所有數據后,將連接切換到新群集。

    ![在使用者應用上切換叢集](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **根據需要刪除舊的群集和測試應用程式。** 一旦交換機完成並正常工作,根據需要刪除舊的 HDInsight 3.6 Kafka 叢集以及測試中使用的生產者和消費者。

## <a name="next-steps"></a>後續步驟

* [Apache Kafka HDInsight 叢集的效能最佳化](apache-kafka-performance-tuning.md)
* [快速入門:使用 Azure 門戶在 Azure HDInsight 中建立 Apache Kafka 叢集](apache-kafka-get-started.md)
