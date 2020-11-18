---
title: Apache Spark & Apache Kafka 與 Cosmos DB Azure HDInsight
description: 了解如何使用「Apache Spark 結構化串流」從 Apache Kafka 讀取資料，然後儲存至 Azure Cosmos DB。 在此範例中，您使用 Jupyter Notebook 從 HDInsight 上的 Spark 串流資料。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/18/2019
ms.openlocfilehash: 387eb4f4c73b2103a7461c0d06c4d0e0562ec9db
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842459"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>搭配 Apache Kafka 和 Azure Cosmos DB 使用 Apache Spark 結構化串流

了解如何使用 [Apache Spark](https://spark.apache.org/) [結構化串流](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)從 Azure HDInsight 上的 [Apache Kafka](https://kafka.apache.org/) 讀取資料，然後將資料儲存至 Azure Cosmos DB。

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是全域散發的多模型資料庫。 此範例使用 SQL API 資料庫模型。 如需詳細資訊，請參閱[歡迎使用 Azure Cosmos DB](../cosmos-db/introduction.md) 文件。

Spark 結構化串流是建置在 Spark SQL 上的串流處理引擎。 它允許您進行與靜態資料批次計算相同的串流計算。 如需有關結構化串流的詳細資訊，請參閱 Apache.org 的[結構化串流程式設計手冊](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) \(英文\)。

> [!IMPORTANT]  
> 此範例使用 HDInsight 3.6 上的 Spark 2.2。
>
> 本文件中的步驟建立 Azure 資源群組，其中包含 HDInsight 上的 Spark 和 HDInsight 叢集上的 Kafka。 這兩個叢集都位於 Azure 虛擬網路中，可讓 Spark 叢集直接與 Kafka 叢集通訊。
>
> 當您完成本文件中的步驟時，請記得刪除叢集，以避免產生過多的費用。

## <a name="create-the-clusters"></a>建立叢集

Apache Kafka on HDInsight 不提供透過公用網際網路存取 Kafka 訊息代理程式。 任何 Kafka 相關項目必須位於與 Kafka 叢集中節點相同的 Azure 虛擬網路。 例如，Kafka 和 Spark 叢集均位於 Azure 虛擬網路中。 下圖顯示叢集之間的通訊流動方式︰

![Azure 虛擬網路中的 Spark 和 Kafka 叢集圖表](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka 服務僅限於虛擬網路內的通訊。 叢集上的其他服務 (例如 SSH 和 Ambari) 可以透過網際網路存取。 如需有關適用於 HDInsight 的公用連接埠詳細資訊，請參閱 [HDInsight 所使用的連接埠和 URI](hdinsight-hadoop-port-settings-for-services.md)。

雖然您可以手動建立 Azure 虛擬網路、Kafka 和 Spark 叢集，但使用 Azure Resource Manager 範本更輕鬆。 使用下列步驟將 Azure 虛擬網路、Kafka 和 Spark 叢集部署到 Azure 訂用帳戶。

1. 使用以下按鈕，在 Azure 入口網站中登入 Azure 並開啟範本。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Azure Resource Manager 範本位於此專案的 GitHub 存放庫 ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)) 。

    此範本會建立下列資源：

   * HDInsight 3.6 叢集上的 Kafka。

   * HDInsight 3.6 叢集上的 Spark。

   * Azure 虛擬網路，其中包含 HDInsight 叢集。 此範本建立的虛擬網路使用 10.0.0.0/16 位址空間。

   * Azure Cosmos DB SQL API 資料庫。

    > [!IMPORTANT]  
    > 此範例中使用的結構化串流 Notebook 需要 HDInsight 3.6 上的 Spark。 如果您在 HDInsight 上使用較早版本的 Spark，當使用 Notebook 時會收到錯誤。

1. 使用下列資訊來填入 [自訂部署] 區段上的項目︰

    |屬性 |值 |
    |---|---|
    |訂用帳戶|選取 Azure 訂用帳戶。|
    |資源群組|建立群組或選取現有的群組。 此群組包含 HDInsight 叢集。|
    |Cosmos DB 帳戶名稱|此值是作為 Cosmos DB 帳戶的名稱使用。 名稱只能包含小寫字母、數字及連字號 (-) 字元。 其長度必須介於 3 到 31 個字元之間。|
    |基底叢集名稱|此值會作為 Spark 和 Kafka 叢集的基底名稱。 例如，輸入 **myhdi** 以建立名為 __spark-myhdi__ 的 Spark 叢集，以及名為 **kafka-myhdi** 的 Kafka 叢集。|
    |叢集版本|HDInsight 叢集版本。 此範例使用 HDInsight 3.6 進行測試，而且可能無法用於其他叢集類型。|
    |叢集登入使用者名稱|Spark 和 Kafka 叢集的系統管理員使用者名稱。|
    |叢集登入密碼|Spark 和 Kafka 叢集的系統管理員使用者密碼。|
    |SSH 使用者名稱|要為 Spark 和 Kafka 叢集建立的 SSH 使用者。|
    |SSH 密碼|Spark 和 Kafka 叢集的 SSH 使用者密碼。|

    ![HDInsight 自訂部署值](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. 讀取 **條款及條件**，然後選取 [我同意上方所述的條款及條件]。

1. 最後，選取 [購買]。 最多需要 45 分鐘才能建立叢集、虛擬網路與 Cosmos DB 帳戶。

## <a name="create-the-cosmos-db-database-and-collection"></a>建立 Cosmos DB 資料庫和集合

此文件中使用的專案會將資料儲存在 Cosmos DB 中。 執行程式碼之前，您必須先在您的 Cosmos DB 執行個體中建立「資料庫」與「集合」 。 您也必須擷取用於向 Cosmos DB 驗證要求的文件端點與「索引鍵」。

執行此動作的其中一種方式是使用 [Azure CLI](/cli/azure/)。 下列指令碼將會建立名為 `kafkadata` 的資料庫與名為 `kafkacollection` 的集合。 接著，它會傳回主索引鍵。

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

文件端點與主索引鍵資訊類似下列文字：

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> 儲存端點與索引鍵值，因為在 Jupyter Notebooks 中需要它們。

## <a name="get-the-notebooks"></a>取得 Notebook

本檔所述範例的程式碼可在中取得 [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) 。

## <a name="upload-the-notebooks"></a>上傳 Notebook

使用下列步驟，將專案中的 Notebook 上傳到您 HDInsight 叢集上的 Spark：

1. 在網頁瀏覽器中，連線到您 Spark 叢集上的 Jupyter Notebook。 在下列 URL 中，將 `CLUSTERNAME`取代為您的 __Spark__ 叢集名稱：

    ```http
    https://CLUSTERNAME.azurehdinsight.net/jupyter
    ```

    出現提示時，輸入您在建立叢集時所使用的叢集登入 (admin) 和密碼。

2. 從頁面右上角，使用 [上傳] 按鈕將 __Stream-taxi-data-to-kafka.ipynb__ 檔案上傳到叢集。 選取 [開啟] 以開始上傳。

3. 在 Notebook 清單中尋找 __Stream-taxi-data-to-kafka.ipynb__ 項目，然後選取其旁邊的 [上傳] 按鈕。

4. 重複步驟 1-3 以上傳 __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ Notebook。

## <a name="load-taxi-data-into-kafka"></a>將 taxi 資料載入至 Kafka

上傳檔案之後，請選取 [Stream-taxi-data-to-kafka.ipynb] 項目來開啟 Notebook。 請依照 Notebook 中的步驟，將資料載入至 Kafka。

## <a name="process-taxi-data-using-spark-structured-streaming"></a>使用 Spark 結構化串流來處理 taxi 資料

從 [Jupyter Notebook](https://jupyter.org/) 首頁，選取 __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ 項目。 請依照 Notebook 中的步驟，使用「Spark 結構化串流」將資料從 Kafka 串流至 Azure Cosmos DB。

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何使用 Apache Spark 結構化串流，請參閱下列檔，以深入瞭解如何使用 Apache Spark、Apache Kafka 和 Azure Cosmos DB：

* [如何搭配 Apache Kafka 使用 Apache Spark 串流 (DStream) ](hdinsight-apache-spark-with-kafka.md)。
* [開始使用 Jupyter Notebook 與 HDInsight 上的 Apache Spark](spark/apache-spark-jupyter-spark-sql.md)
* [歡迎使用 Azure Cosmos DB](../cosmos-db/introduction.md)
