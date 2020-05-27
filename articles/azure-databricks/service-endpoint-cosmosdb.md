---
title: 教學課程 - 使用 Cosmos DB 端點實作 Azure Databricks
description: 本教學課程說明如何在有服務端點啟用 Cosmos DB 的虛擬網路中實作 Azure Databricks。
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: e18c2b0f03f9ac2155c441580d62d6085581de12
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779604"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>教學課程：使用 Cosmos DB 端點實作 Azure Databricks

本教學課程說明如何在有服務端點啟用 Cosmos DB 的環境中實作 VNet 插入的 Databricks。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 在虛擬網路中建立 Azure Databricks 工作區
> * 建立 Cosmos DB 服務端點
> * 建立 Cosmos DB 帳戶並匯入資料
> * 建立 Azure Databricks 叢集
> * 從 Azure Databricks Notebook 查詢 Cosmos DB

## <a name="prerequisites"></a>Prerequisites

開始之前，請執行下列作業：

* [在虛擬網路中建立 Azure Databricks 工作區](quickstart-create-databricks-workspace-vnet-injection.md)。

* 下載 [Spark 連接器](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar)。

* 從 [NOAA 美國國家環境資訊中心](https://www.ncdc.noaa.gov/stormevents/)下載範例資料。 選取狀態或區域，然後選取 [搜尋]。 在下一頁上，接受預設值並選取 [搜尋]。 然後選取頁面左側的 [CSV 下載] 以下載結果。

* 下載 Azure Cosmos DB 資料移轉工具[預先編譯的二進位檔](https://aka.ms/csdmtool)。

## <a name="create-a-cosmos-db-service-endpoint"></a>建立 Cosmos DB 服務端點

1. 在您將 Azure Databricks 工作區部署到虛擬網路後，請在 [Azure 入口網站](https://portal.azure.com)中瀏覽至虛擬網路。 請注意透過 Databricks 部署所建立的公用和私人子網路。

   ![虛擬網路子網路](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. 選取 [public-subnet] 並建立 Cosmos DB 服務端點。 然後**儲存**。
   
   ![新增 Cosmos DB 服務端點](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>建立 Cosmos DB 帳戶

1. 開啟 Azure 入口網站。 在畫面的左上方，選取 [建立資源] > [資料庫] > [Azure Cosmos DB]。

2. 使用下列設定，填寫 [基本資訊] 索引標籤上的 [執行個體詳細資料]：

   |設定|值|
   |-------|-----|
   |訂用帳戶|*您的訂用帳戶*|
   |資源群組|*您的資源群組*|
   |帳戶名稱|db-vnet-service-endpoint|
   |API|Core (SQL)|
   |Location|美國西部|
   |異地備援|停用|
   |多重區域寫入|啟用|

   ![新增 Cosmos DB 服務端點](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. 選取 [網路] 索引標籤，然後設定您的虛擬網路。 

   a. 選擇您建立作為必要條件的虛擬網路，然後選取 public-subnet。 請注意，public-subnet 具有「遺漏 'Microsoft AzureCosmosDB' 端點」附註。 這是因為您只在 public-subnet 上啟用 Cosmos DB 服務端點。

   b. 確定您已啟用 [允許從 Azure 入口網站存取]。 此設定可讓您從 Azure 入口網站存取您的 Cosmos DB 帳戶。 如果此選項設定為 [拒絕]，您會在嘗試存取您的帳戶時收到錯誤。 

   > [!NOTE]
   > 在此教學課程中未必如此，但如果您想要從本機電腦存取您的 Cosmos DB 帳戶您也可以啟用 [允許從我的 IP 存取]。 例如，如果您使用 Cosmos DB SDK 連線到您的帳戶，您需要啟用此設定。 若已停用，您會收到「拒絕存取」錯誤。

   ![Cosmos DB 帳戶網路設定](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. 選取 [檢閱 + 建立]，然後選取 [建立] 在虛擬網路內部建立 Cosmos DB 帳戶。

5. 建立您的 Cosmos DB 帳戶後，請瀏覽至 [設定] 之下的 [金鑰]。 複製主要連接字串，並將它儲存在文字編輯器中以供稍後使用。

    ![Cosmos DB 帳戶金鑰頁面](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. 選取 [資料總管] 和 [新增集合]，將新的資料庫和集合新增至您的 Cosmos DB 帳戶。

    ![新的 Cosmos DB 集合](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>將資料上傳至 Cosmos DB

1. 開啟圖形化介面版本的 [Cosmos DB 資料移轉工具](https://aka.ms/csdmtool)**Dtui.exe**。

    ![Cosmos DB 資料移轉工具](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. 在 [來源資訊] 索引標籤上，選取 [匯入來源] 下拉式清單中的 [CSV 檔案]。 然後選取 [新增檔案]，以及新增您下載作為必要條件的 Storm 資料 CSV。

    ![Cosmos DB 資料移轉工具來源資訊](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. 在 [目標資訊] 索引標籤上，輸入您的連接字串。 連接字串格式為 `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`。 您在上一節中儲存的主要連接字串會包含 AccountEndpoint 和 AccountKey。 將 `Database=<your database name>` 附加到連接字串的結尾，然後選取 [驗證]。 然後，新增集合名稱和分割區索引鍵。

    ![Cosmos DB 資料移轉工具目標資訊](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. 選取 [下一步]，直到到達 [摘要] 頁面為止。 然後，選取 [匯入]。

## <a name="create-a-cluster-and-add-library"></a>建立叢集和新增程式庫

1. 在 [Azure 入口網站](https://portal.azure.com)中瀏覽至您的 Azure Databricks 服務，然後選取 [啟動工作區]。

2. 建立新叢集。 選擇叢集名稱及接受其餘的預設設定。

   ![新的叢集設定](./media/service-endpoint-cosmosdb/create-cluster.png)

3. 建立叢集之後，瀏覽至叢集頁面，然後選取 [程式庫] 索引標籤。選取 [安裝新的] 並上傳 Spark 連接器 jar 檔案，以安裝程式庫。

    ![安裝 Spark 連接器程式庫](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    您可以在 [程式庫] 索引標籤確認程式庫已安裝。

    ![Databricks 叢集程式庫索引標籤](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>從 Databricks Notebook 查詢 Cosmos DB

1. 瀏覽至您的 Azure Databricks 工作區，並建立新的 Python Notebook。

    ![建立新的 Databricks Notebook](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. 執行下列 Python 程式碼以設定 Cosmos DB 連線組態。 據此變更 [端點]、[主要金鑰]、[資料庫] 及 [集合]。

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. 使用下列 Python 程式碼來載入資料並建立暫存檢視。

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. 使用下列神奇命令來執行可傳回資料的 SQL 陳述式。

    ```python
    %sql
    select * from storm
    ```

    您已將 VNet 插入的 Databricks 工作區成功連線到已啟用服務端點的 Cosmos DB 資源。 若要深入了解如何連線到 Cosmos DB，請參閱[適用於 Apache Spark 的 Azure Cosmos DB 連接器](https://github.com/Azure/azure-cosmosdb-spark)。

## <a name="clean-up-resources"></a>清除資源

若不再需要，可刪除資源群組、Azure Databricks 工作區和所有相關資源。 刪除作業可避免不必要的計費。 如果您計劃在未來使用 Azure Databricks 工作區，您可以停止叢集且稍後重新啟動。 如果您不再繼續使用此 Azure Databricks 工作區，請使用下列步驟，刪除本教學課程所建立的所有資源：

1. 從 Azure 入口網站的左側功能表，按一下 [資源群組]，然後按一下您所建立的資源群組名稱。

2. 在資源群組頁面上，選取 [刪除]，在文字方塊中輸入要刪除的資源名稱，然後再次選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將 Azure Databricks 工作區部署到虛擬網路，並使用 Cosmos DB Spark 連接器從 Databricks 查詢 Cosmos DB 資料。 若要深入了解如何在虛擬網路中使用 Azure Databricks，請繼續進行使用 SQL Server 搭配 Azure Databricks 的教學課程。

> [!div class="nextstepaction"]
> [教學課程：從 Azure Databricks Notebook 查詢虛擬網路中的 SQL Server Linux Docker 容器](vnet-injection-sql-server.md)
