---
title: 將 Apache Kafka 工作負載遷移至 Azure HDInsight 4。0
description: 瞭解如何將 HDInsight 3.6 上的 Apache Kafka 工作負載遷移至 HDInsight 4.0。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 0a31c6cf32222277e033aacf7d04622c54aef9ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80437003"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>將 Apache Kafka 工作負載遷移至 Azure HDInsight 4。0

Azure HDInsight 4.0 提供最新的開放原始碼元件，並大幅增強效能、連線能力和安全性。 本檔說明如何將 HDInsight 3.6 上的 Apache Kafka 工作負載遷移至 HDInsight 4.0。 將工作負載遷移至 HDInsight 4.0 之後，您可以使用 HDInsight 3.6 上未提供的許多新功能。

## <a name="hdinsight-36-kafka-migration-paths"></a>HDInsight 3.6 Kafka 遷移路徑

HDInsight 3.6 支援兩種版本的 Kafka：1.0.0 和1.1.0。 HDInsight 4.0 支援版本1.1.0 和2.1.0。 根據您想要執行的 Kafka 版本和 HDInsight 版本而定，有多個支援的遷移路徑。 這些路徑會在下方說明，並在下圖中加以說明。

* **在最新版本上執行 Kafka 和 HDInsight （建議選項）**：將 HDInsight 3.6 和 Kafka 1.0.0 或1.1.0 應用程式遷移至具有 Kafka 2.1.0 的 hdinsight 4.0 （以下路徑 D 和 E）。
* **在最新版本上執行 hdinsight，但只在較新的版本上 Kafka**：使用 Kafka 1.1.0 將 hdinsight 3.6 和 Kafka 1.0.0 應用程式遷移至 hdinsight 4.0 （下方的路徑 B）。
* **在最新版本上執行 HDInsight，保留 Kafka 版本**：將 HDInsight 3.6 和 Kafka 1.1.0 應用程式遷移至具有 Kafka 1.1.0 的 hdinsight 4.0 （下方的路徑 C）。
* **在較新的版本上執行 Kafka，保留 HDInsight 版本**：將 Kafka 1.0.0 應用程式遷移至1.1.0 並保持在 HDInsight 3.6 （以下路徑 a）。 請注意，此選項仍然需要部署新的叢集。 不支援在現有叢集上升級 Kafka 版本。 建立具有所需版本的叢集之後，請遷移 Kafka 用戶端以使用新的叢集。

![3.6 上 Apache Kafka 的升級路徑](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Apache Kafka 版本

### <a name="kafka-110"></a>Kafka 1.1。0
  
如果您從 Kafka 1.0.0 遷移至1.1.0，您可以利用下列新功能：

* Kafka 控制器的改進可加速控制關機，讓您可以重新開機訊息代理程式，並更快從問題中復原。 
* [FetchRequests 邏輯](https://issues.apache.org/jira/browse/KAFKA-6254)的改良功能，可讓您在叢集上擁有更多的分割區（因此有更多主題）。 
* Kafka Connect 支援主題的[記錄標頭](https://issues.apache.org/jira/browse/KAFKA-5142)和[正則運算式](https://issues.apache.org/jira/browse/KAFKA-3073)。 

如需完整的更新清單，請參閱[Apache Kafka 1.1 版本](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html)資訊。

### <a name="apache-kafka-210"></a>Apache Kafka 2.1。0

如果您遷移至 Kafka 2.1，您可以利用下列功能：

* 較佳的 broker 復原能力，因為已改善複寫通訊協定。
* KafkaAdminClient API 中的新功能。
* 可設定的配額管理。
* 支援 Zstandard 壓縮。

如需完整的更新清單，請參閱[Apache Kafka 2.0 版本](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html)資訊和[apache Kafka 2.1 版本](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html)資訊。

## <a name="kafka-client-compatibility"></a>Kafka 用戶端相容性

新的 Kafka 訊息代理程式支援較舊的用戶端。 [略過 k-35-正在取得通訊協定版本](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version)引進了動態判斷 Kafka 訊息代理[程式和略過 k-97 功能的機制：改良的 KAFKA 用戶端 RPC 相容性原則](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy)引進了新的相容性原則，並保證 JAVA 用戶端。 在過去，Kafka 用戶端必須與相同版本或更新版本的 broker 進行互動。 現在，較新版本的 JAVA 用戶端和其他支援略過 K-35 （例如）的用戶端 `librdkafka` 會切換回舊版的要求類型，如果無法使用功能，則會擲回適當的錯誤。

![升級 Kafka 用戶端相容性](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

請注意，這並不表示用戶端支援舊版的代理程式。  如需詳細資訊，請參閱[相容性矩陣](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix)。

## <a name="general-migration-process"></a>一般遷移程式

下列遷移指引假設在單一虛擬網路中，部署在 HDInsight 3.6 上的 Apache Kafka 1.0.0 或1.1.0 叢集。 現有的訊息代理程式有一些主題，並且正由生產者和取用者積極使用。

![目前的 Kafka 假設環境](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

若要完成遷移，請執行下列步驟：

1. **部署新的 HDInsight 4.0 叢集和用戶端進行測試。** 部署新的 HDInsight 4.0 Kafka 叢集。 如果可以選取多個 Kafka 叢集版本，建議您選取最新版本。 部署之後，請視需要設定一些參數，並使用與現有環境相同的名稱建立主題。 此外，請視需要設定 TLS 和攜帶您自己的金鑰（BYOK）加密。 然後檢查它是否能與新的叢集正確搭配運作。

    ![部署新的 HDInsight 4.0 叢集](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **切換生產者應用程式的叢集，並等候目前取用者取用所有的佇列資料。** 當新的 HDInsight 4.0 Kafka 叢集準備就緒時，請將現有的生產者目的地切換到新的叢集。 在現有的取用者應用程式耗用現有叢集的所有資料之前，保持原狀。

    ![為生產者應用程式切換叢集](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **切換取用者應用程式上的叢集。** 確認現有的取用者應用程式已完成使用現有叢集的所有資料之後，請將連線切換到新的叢集。

    ![在取用者應用程式上切換叢集](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **視需要移除舊叢集並測試應用程式。** 當交換器完成且正常運作後，請視需要移除舊的 HDInsight 3.6 Kafka 叢集以及測試中使用的產生者和取用者。

## <a name="next-steps"></a>後續步驟

* [Apache Kafka HDInsight 叢集的效能最佳化](apache-kafka-performance-tuning.md)
* [快速入門：在 Azure HDInsight 中使用 Azure 入口網站建立 Apache Kafka 叢集](apache-kafka-get-started.md)
