---
title: 使用 Apache Kafka 的 Apache Spark 串流 - Azure HDInsight
description: 了解如何使用 DStreams 以 Apache Spark 串流方式將資料送入或送出 Apache Kafka。 在此範例中，您使用 Jupyter Notebook 從 HDInsight 上的 Spark 串流資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: e2f4bbe6157e1affa4975f6dcee2ad0f1d98a71b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86086087"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>在 HDInsight 上使用 Apache Kafka 的 Apache Spark 串流 (DStream) 範例

瞭解如何使用[Apache Spark](https://spark.apache.org/) ，使用[DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html)將資料傳入或傳出[Apache Kafka](https://kafka.apache.org/) on HDInsight。 這個範例會使用在 Spark 叢集上執行的 [Jupyter Notebook](https://jupyter.org/)。

> [!NOTE]  
> 本文件中的步驟建立 Azure 資源群組，其中包含 HDInsight 上的 Spark 和 HDInsight 叢集上的 Kafka。 這兩個叢集都位於 Azure 虛擬網路中，可讓 Spark 叢集直接與 Kafka 叢集通訊。
>
> 當您完成本文件中的步驟時，請記得刪除叢集，以避免產生過多的費用。

> [!IMPORTANT]  
> 此範例會使用 DStreams，這是較舊的 Spark 串流技術。 如需使用較新 Spark 串流功能的範例，請參閱[使用 Apache Kafka 的 Spark 結構化串流](hdinsight-apache-kafka-spark-structured-streaming.md)文件。

## <a name="create-the-clusters"></a>建立叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 訊息代理程式。 任何 Kafka 相關項目必須位於與 Kafka 叢集中節點相同的 Azure 虛擬網路。 例如，Kafka 和 Spark 叢集均位於 Azure 虛擬網路中。 下圖顯示叢集之間的通訊流動方式︰

![Azure 虛擬網路中的 Spark 和 Kafka 叢集圖表](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka 本身受限於虛擬網路內的通訊，但叢集上的 SSH 和 Ambari 等其他服務可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。

雖然您可以手動建立 Azure 虛擬網路、Kafka 和 Spark 叢集，但使用 Azure Resource Manager 範本更輕鬆。 使用下列步驟將 Azure 虛擬網路、Kafka 和 Spark 叢集部署到 Azure 訂用帳戶。

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager 範本位於 **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** 。

    > [!WARNING]  
    > 若要保證 Kafka 在 HDInsight 上的可用性，您的叢集必須包含至少三個背景工作角色節點。 此範本會建立包含三個背景工作角色節點的 Kafka 叢集。

    此範本會為 Kafka 和 Spark 建立 HDInsight 3.6 叢集。

1. 使用下列資訊來填入 [自訂部署]**** 區段上的項目︰

    |屬性 |值 |
    |---|---|
    |資源群組|建立群組或選取現有的群組。|
    |Location|選取靠近您的地理位置。|
    |基底叢集名稱|此值會作為 Spark 和 Kafka 叢集的基底名稱。 例如，輸入 **hdistreaming** 可建立名為 __spark-hdistreaming__ 的 Spark 叢集，以及名為 **kafka-hdistreaming** 的 Kafka 叢集。|
    |叢集登入使用者名稱|Spark 和 Kafka 叢集的系統管理員使用者名稱。|
    |叢集登入密碼|Spark 和 Kafka 叢集的系統管理員使用者密碼。|
    |SSH 使用者名稱|要為 Spark 和 Kafka 叢集建立的 SSH 使用者。|
    |SSH 密碼|Spark 和 Kafka 叢集的 SSH 使用者密碼。|

    ![HDInsight 自訂部署參數](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. 讀取**條款及條件**，然後選取 [我同意上方所述的條款及條件]。

1. 最後，選取 [購買]****。 大約需要 20 分鐘的時間來建立叢集。

資源建立後，摘要頁面會隨即出現。

![VNet 及叢集的資源群組摘要](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> 請注意，HDInsight 叢集的名稱是 **spark-BASENAME** 和 **kafka-BASENAME**，其中 BASENAME 是您提供給範本的名稱。 連接到叢集時，您會在稍後步驟中使用這些名稱。

## <a name="use-the-notebooks"></a>使用 Notebook

本檔所述範例的程式碼可在中取得 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) 。

## <a name="delete-the-cluster"></a>選取叢集

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

因為本文件中的步驟會在相同的 Azure 資源群組中建立兩個叢集，您可以在 Azure 入口網站中刪除資源群組。 刪除群組，即可移除依循本文件建立的所有資源、Azure 虛擬網路，以及叢集所使用的儲存體帳戶。

## <a name="next-steps"></a>接下來的步驟

在此範例中，您已了解如何使用 Spark 來讀取和寫入至 Kafka。 使用下列連結來探索使用 Kafka 的其他方式︰

* [開始使用 Apache Kafka on HDInsight](kafka/apache-kafka-get-started.md)
* [使用 MirrorMaker 在 HDInsight 上建立 Apache Kafka 複本](kafka/apache-kafka-mirroring.md)
* [在 HDInsight 上搭配使用 Apache Storm 與 Apache Kafka](hdinsight-apache-storm-with-kafka.md)
