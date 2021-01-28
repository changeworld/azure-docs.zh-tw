---
title: 將 Apache Kafka 工作負載遷移至 Azure HDInsight 4。0
description: 瞭解如何將 HDInsight 3.6 上的 Apache Kafka 工作負載遷移至 HDInsight 4.0。
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 3967a5d96c35e4bac88dcd9a6c1fa95b78a6b2b1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939115"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>將 Apache Kafka 工作負載遷移至 Azure HDInsight 4。0

Azure HDInsight 4.0 提供最新的開放原始碼元件，並大幅增強效能、連線能力和安全性。 本檔說明如何將 HDInsight 3.6 上的 Apache Kafka 工作負載遷移至 HDInsight 4.0。 將工作負載遷移至 HDInsight 4.0 之後，您可以使用 HDInsight 3.6 上未提供的許多新功能。

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka 遷移路徑

HDInsight 3.6 支援兩種版本的 Kafka：1.0.0 和1.1.0。 HDInsight 4.0 支援版本1.1.0 和2.1.0。 根據 Kafka 的版本以及您想要執行的 HDInsight 版本，有多個支援的遷移路徑。 下圖將說明這些路徑，如下圖所示。

* **在最新版本上執行 Kafka 和 HDInsight (建議的)**：將 hdinsight 3.6 和 Kafka 1.0.0 或1.1.0 應用程式遷移至具有 Kafka 2.1.0 的 hdinsight 4.0 (路徑 D 和 E 以下) 。
* **請在最新版本上執行 hdinsight，但只 Kafka 較新的版本**：將 hdinsight 3.6 和 Kafka 1.0.0 應用程式遷移到) 下方的 Kafka 1.1.0 (路徑 B 的 hdinsight 4.0。
* **在最新版本上執行 HDInsight、保留 Kafka 版本**：將 hdinsight 3.6 和 Kafka 1.1.0 應用程式遷移至 hdinsight 4.0，並使用 Kafka 1.1.0 (路徑 C) 下方。
* **在較新的版本上執行 Kafka、保留 HDInsight 版本**：將 Kafka 1.0.0 應用程式遷移至1.1.0，並保持在 HDInsight 3.6 (路徑 a 以下) 。 請注意，這個選項仍然需要部署新的叢集。 不支援在現有的叢集上升級 Kafka 版本。 使用您想要的版本建立叢集之後，請遷移您的 Kafka 用戶端以使用新的叢集。

![3.6 上的 Apache Kafka 升級路徑](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka 版本

### <a name="kafka-110"></a>Kafka 1.1。0
  
如果您從 Kafka 1.0.0 遷移至1.1.0，您可以利用下列新功能：

* Kafka 控制器加速受控制關機的改進，讓您可以重新開機訊息代理程式，並以更快的速度從問題中復原。 
* [FetchRequests 邏輯](https://issues.apache.org/jira/browse/KAFKA-6254)中的增強功能，可讓您 (更多資料分割，因此在叢集上) 更多主題。 
* Kafka Connect 支援主題的 [記錄標頭](https://issues.apache.org/jira/browse/KAFKA-5142) 和 [正則運算式](https://issues.apache.org/jira/browse/KAFKA-3073) 。 

如需更新的完整清單，請參閱 [Apache Kafka 1.1 版本](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)資訊。

### <a name="apache-kafka-210"></a>Apache Kafka 2.1。0

如果您遷移至 Kafka 2.1，您可以利用下列功能：

* 更好的 broker 復原，因為複寫通訊協定已改進。
* KafkaAdminClient API 中的新功能。
* 可設定的配額管理。
* 支援 Zstandard 壓縮。

如需更新的完整清單，請參閱 [Apache Kafka 2.0 版本](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) 資訊和 [apache Kafka 2.1 版本](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)資訊。

## <a name="kafka-client-compatibility"></a>Kafka 用戶端相容性

新的 Kafka 訊息代理程式支援較舊的用戶端。 [略過 k-35-取得通訊協定版本](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) 引進了一種機制，可動態判斷 Kafka 訊息代理 [程式和略過 k-97 的功能：改良的 KAFKA 用戶端 RPC 相容性原則](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) 引進了新的相容性原則，並保證 JAVA 用戶端。 先前，Kafka 用戶端必須與相同版本或較新版本的代理程式互動。 現在，較新版本的 JAVA 用戶端和其他支援略過 K-35 （例如）的用戶端 `librdkafka` 會切換回較舊的要求類型，如果功能無法使用，則會擲回適當的錯誤。

![升級 Kafka 用戶端相容性](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

請注意，這並不表示用戶端支援較舊的訊息代理程式。  如需詳細資訊，請參閱 [相容性比較表](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)。

## <a name="general-migration-process"></a>一般遷移流程

下列遷移指引假設在單一虛擬網路中，部署在 HDInsight 3.6 上的 Apache Kafka 1.0.0 或1.1.0 叢集。 現有的 broker 有一些主題，並且正在由生產者和取用者主動使用。

![目前的 Kafka 假設環境](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

若要完成遷移，請執行下列步驟：

1. **部署新的 HDInsight 4.0 叢集和用戶端以進行測試。** 部署新的 HDInsight 4.0 Kafka 叢集。 如果可以選取多個 Kafka 叢集版本，建議您選取最新版本。 部署之後，請視需要設定一些參數，並使用與現有環境相同的名稱建立主題。 此外，請視需要設定 TLS 和自備金鑰 (BYOK) 加密。 然後檢查它是否能與新的叢集正常搭配運作。

    ![部署新的 HDInsight 4.0 叢集](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **切換生產者應用程式的叢集，並等候目前的取用者取用所有佇列資料。** 當新的 HDInsight 4.0 Kafka 叢集準備就緒時，請將現有的生產者目的地切換至新的叢集。 讓它保持原狀，直到現有的取用者應用程式取用現有叢集的所有資料為止。

    ![切換生產者應用程式的叢集](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **切換取用者應用程式上的叢集。** 確認現有的取用者應用程式已完成取用現有叢集的所有資料之後，請將連接切換至新的叢集。

    ![切換取用者應用程式上的叢集](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **視需要移除舊叢集並測試應用程式。** 切換完成且正常運作之後，請視需要移除舊的 HDInsight 3.6 Kafka 叢集以及測試中所使用的產生者和取用者。

## <a name="next-steps"></a>後續步驟

* [Apache Kafka HDInsight 叢集的效能最佳化](apache-kafka-performance-tuning.md)
* [快速入門：在 Azure HDInsight 中使用 Azure 入口網站建立 Apache Kafka 叢集](apache-kafka-get-started.md)
