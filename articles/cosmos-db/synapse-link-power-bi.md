---
title: Power BI 和無伺服器 SQL 集區，以使用 Synapse 連結來分析 Azure Cosmos DB 資料
description: 瞭解如何建立 Synapse 的 SQL 無伺服器資料庫，並透過 Synapse 連結進行 Azure Cosmos DB、查詢 Azure Cosmos DB 容器，然後使用這些視圖的 Power BI 建立模型。
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/22/2020
ms.author: acomet
ms.openlocfilehash: 38077dca1b8a27098e8db17354b82340a651b880
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305164"
---
# <a name="use-power-bi-and-serverless-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link-preview"></a>使用 Power BI 和無伺服器 SQL 集區，透過 Synapse 連結 (預覽來分析 Azure Cosmos DB 資料) 

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)][!INCLUDE[appliesto-mongodb-apis](includes/appliesto-mongodb-api.md)]

在本文中，您將瞭解如何建立無伺服器 SQL 集區資料庫，並透過 Synapse 連結來進行 Azure Cosmos DB 的流覽。 您將會查詢 Azure Cosmos DB 的容器，然後建立模型，以 Power BI 這些視圖，以反映該查詢。

在此案例中，您將會在合作夥伴零售商店中使用有關 Surface product sales 的虛擬資料。 您將根據大型家庭的鄰近程度，以及對特定周廣告的影響，分析每個商店的收入。 在本文中，您會建立兩個名為 **RetailSales** 和 **StoreDemographics** 的視圖，以及兩者之間的查詢。 您可以從這個 [GitHub](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) 存放庫取得範例產品資料。

## <a name="prerequisites"></a>必要條件

開始之前，請務必先建立下列資源：

* [建立種類為 SQL (core) 或 MongoDB 的 Azure Cosmos DB 帳戶。](create-cosmosdb-resources-portal.md)

* 為您的[Azure Cosmos 帳戶](configure-synapse-link.md#enable-synapse-link)啟用 Azure Synapse 連結

* 在 Azure Cosmos 帳戶中建立資料庫，並在兩個已[啟用分析存放區](configure-synapse-link.md#create-analytical-ttl)的容器中建立資料庫。

* 將產品資料載入至 Azure Cosmos 容器，如此 [批次資料](https://github.com/Azure-Samples/Synapse/blob/master/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) 內嵌筆記本中所述。

* 建立名為 **SynapseLinkBI**[的 Synapse 工作區](../synapse-analytics/quickstart-create-workspace.md)。

* 將[Azure Cosmos 資料庫連線至 Synapse 工作區](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)。

## <a name="create-a-database-and-views"></a>建立資料庫和 views

在 [Synapse] 工作區中，移至 [ **開發** ] 索引標籤，選取 **+** 圖示，然後選取 [ **SQL 腳本** ]。

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="將 SQL 腳本新增至 Synapse Analytics 工作區":::

每個工作區都有無伺服器 SQL 端點。 建立 SQL 腳本之後，請從頂端的工具列連接至 **內建** 。

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="啟用 SQL 腳本以使用工作區中的無伺服器 SQL 端點":::

建立名為 **RetailCosmosDB** 的新資料庫，並在已啟用 Synapse 連結的容器上建立 SQL view。 下列命令顯示如何建立資料庫：

```sql
-- Create database
Create database RetailCosmosDB
```

接下來，在啟用 Azure Cosmos 容器的不同 Synapse Link 之間建立多個視圖。 視圖可讓您使用 T-sql 來聯結和查詢位於不同容器 Azure Cosmos DB 資料。  建立視圖時，請務必選取 **RetailCosmosDB** 資料庫。

下列腳本示範如何在每個容器上建立 views。 為了簡單起見，讓我們使用 Synapse SQL 無伺服器啟用 Synapse 連結的容器的 [自動架構推斷](analytical-store-introduction.md#analytical-schema) 功能：


### <a name="retailsales-view"></a>RetailSales view：

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

請務必在先前的 SQL 腳本中插入您的 Azure Cosmos DB 區域和主要金鑰。 區功能變數名稱稱中的所有字元都應為小寫，但不含空格。 與命令的其他參數不同的是 `OPENROWSET` ，您應該指定容器名稱參數，而不需要用引號括住。

### <a name="storedemographics-view"></a>StoreDemographics view：

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

現在，選取 [ **執行** ] 命令以執行 SQL 腳本。

## <a name="query-the-views"></a>查詢檢視

現在已建立兩個視圖，讓我們定義要聯結這兩個視圖的查詢，如下所示：

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

選取 [ **執行** ]，以提供下表作為結果：

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="聯結 StoreDemographics 和 RetailSales 視圖之後的查詢結果":::

## <a name="model-views-over-containers-with-power-bi"></a>使用 Power BI 的容器模型視圖

接著，開啟 Power BI desktop，然後使用下列步驟連接到無伺服器 SQL 端點：

1. 開啟 Power BI Desktop 應用程式。 選取 [ **取得資料** ]，然後選取 [ **其他** ]。

1. 從連線選項清單中選擇 **Azure Synapse Analytics (SQL DW)** 。

1. 輸入資料庫所在 SQL 端點的名稱。 在 `SynapseLinkBI-ondemand.sql.azuresynapse.net` [ **伺服器** ] 欄位內輸入。 在此範例中，  **SynapseLinkBI** 是工作區的名稱。 如果您的工作區指定了不同的名稱，請取代此名稱。 選取 [ **直接查詢** 資料連線模式]，然後選取 **[確定]** 。

1. 選取慣用的驗證方法，例如 Azure AD。

1. 選取 **RetailCosmosDB** 資料庫和 **RetailSales** ， **StoreDemographics** views。

1. 選取 [ **載入** ]，將兩個觀點載入至 [直接查詢] 模式。

1. 選取 [ **模型** ] 可透過 **storeId** 資料行建立兩個視圖之間的關聯性。

1. 將 [ **StoreId** ] 資料行從 [ **RetailSales** ] 視圖拖曳至 [ **StoreDemographics** 視圖] 中的 [ **StoreId** ] 資料行。

1. 選取 [多對一] ( *： 1) 關聯性，因為 **RetailSales** view 中有多個資料列具有相同的商店識別碼。 **StoreDemographics** 只有一個商店識別碼資料列 (它是維度資料表) 。

現在流覽至 [ **報表** ] 視窗並建立報表，以根據分散的收益和 LargeHH 指數標記法，將家庭大小的相對重要性與每位商店的平均收益進行比較：

1. 選取 [ **散佈圖** ]。

1. 將 **LargeHH** 從 **StoreDemographics** 視圖拖放至 X 軸。

1. 將 **收益** 從 **RetailSales** View 拖放到 Y 軸。 選取 [ **平均** ]，以取得每個商店和每週的每一產品平均銷售額。

1. 從 **RetailSales** view 將 [ **productCode** ] 拖放到 [圖例] 中，以選取特定的產品線。
選擇這些選項之後，您應該會看到類似下列螢幕擷取畫面的圖表：

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="將家庭大小的相對重要性與每個商店的平均收益進行比較的報表":::

## <a name="next-steps"></a>後續步驟

[使用 T-sql 利用 Azure Synapse Link 查詢 Azure Cosmos DB 資料](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

使用無伺服器 SQL 集區來 [分析 Azure 開放資料集，並在 Azure Synapse Studio 中將結果視覺化](../synapse-analytics/sql/tutorial-data-analyst.md)
