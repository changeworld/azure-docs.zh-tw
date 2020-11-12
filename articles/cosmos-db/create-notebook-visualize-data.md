---
title: 教學課程：在 Azure Cosmos DB 中建立筆記本來分析資料並將其視覺化
description: 教學課程：了解如何使用內建 Jupyter 筆記本，將資料匯入 Azure Cosmos DB、分析資料，並將輸出視覺化。
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: e16a738264e64e37cfa42722832dac7e34fee899
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339490"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>教學課程：在 Azure Cosmos DB 中建立筆記本來分析資料並將其視覺化
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文說明如何使用內建 Jupyter 筆記本，將範例零售資料匯入 Azure Cosmos DB。 您將了解如何使用 SQL 和 Azure Cosmos DB magic 命令來執行查詢、分析資料，以及將結果視覺化。

## <a name="prerequisites"></a>必要條件

* [在建立 Azure Cosmos 帳戶時啟用筆記本支援](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>建立資源並匯入資料
 
在這一節中，您將建立 Azure Cosmos 資料庫、容器，並將零售資料匯入容器。

1. 瀏覽至 Azure Cosmos 帳戶並開啟 [資料總管]。

1. 移至 [筆記本] 索引標籤，選取 [我的筆記本] 旁邊的 `…`，然後建立 [新筆記本]。 選取 [Python 3]作為預設核心。

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="建立新的筆記本":::

1. 建立新筆記本之後，您可以將它重新命名為 **VisualizeRetailData.ipynb** 之類。

1. 接下來，您將建立名為 "RetailDemo" 的資料庫，以及名為 "WebsiteData" 的容器來存放零售資料。 您可以使用/CartID 作為分割區索引鍵。 將下列程式碼複製並貼到您筆記本中的新儲存格，並加以執行：

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   若要執行儲存格，請選取 `Shift + Enter` 或選取儲存格，然後選擇資料總管瀏覽列上的 [執行作用儲存格] 選項。

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="執行作用儲存格":::

   系統會在您目前的 Azure Cosmos 帳戶中建立資料庫和容器。 此容器會以每秒 400 RU的速率佈建。 建立資料庫和容器之後，您將會看到下列輸出。 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   您也可以重新整理 [資料] 索引標籤，並查看新建立的資源：

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="重新整理 [資料] 索引標籤以查看新容器":::

1. 接下來，您會將範例零售資料匯入 Azure Cosmos 容器中。 以下是來自零售資料的項目格式：

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   針對教學課程目的，零售資料範例會存放在 Azure Blob 儲存體中。 您可以將下列程式碼貼入新儲存格中，進而將該範例匯入 Azure Cosmos 容器。 您可藉由執行查詢來選取項目數，以確認資料已成功匯入。

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   當您執行前一個查詢時，它會傳回下列輸出：

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>將您的資料放入資料框架中

在執行查詢來分析資料之前，您可以將容器中的資料讀取到 [Pandas 資料框架](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html)進行分析。 使用下列 sql magic 命令，將資料讀取至資料框架：

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

若要深入了解，請參閱 [Azure Cosmos DB 中的內建筆記本命令和功能](use-python-notebook-features-and-commands.md)一文。 您將會執行查詢 - `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`。 結果將會儲存到名為 df_cosmos 的 Pandas 資料框架中。 在新的儲存格中貼上下列命令並加以執行：

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

在新的筆記本儲存格中，執行下列程式碼以讀取輸出中的前 10 個項目：

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="執行查詢以取得前 10 個項目":::

## <a name="run-queries-and-analyze-your-data"></a>執行查詢及分析您的資料

在這一節中，您將對所擷取的資料執行一些查詢。

* **查詢 1：** 在資料框架上執行依群組查詢，以取得每個國家/區域的總銷售收益總和，並顯示結果中的 5 個項目。 在新的筆記本儲存格中，執行下列程式碼：

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="總銷售收益輸出":::

* **查詢 2：** 若要取得前五個購買項目的清單，請開啟新的筆記本儲存格，然後執行下列程式碼：

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="前五個購買項目":::

## <a name="visualize-your-data"></a>將您的資料視覺化  

1. 既然我們已有 Azure Cosmos 容器中的收益資料，您可以使用您所選的視覺效果程式庫將您的資料視覺化。 在本教學課程中，我們將使用 Bokeh 程式庫。 開啟新的筆記本儲存格，然後執行下列程式碼以安裝 Bokeh 程式庫。 滿足所有需求之後，就會安裝此程式庫。

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. 接下來，準備將資料繪製在地圖上。 聯結 Azure Cosmos DB 中的資料與位於 Azure Blob 儲存體中的國家/區域資訊，並將結果轉換為 GeoJSON 格式。 將下列程式碼複製到新的筆記本儲存格並加以執行。

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. 在新的筆記本儲存格中執行下列程式碼，以在世界地圖上將不同國家/區域的銷售收益視覺化：

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   輸出會以不同的色彩顯示世界地圖。 較深到較淺的色彩代表具有最高收益到最低收益的國家/區域。

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="國家/區域收益地圖視覺效果":::

1. 讓我們看看另一個資料視覺效果案例。 WebsiteData 容器記錄了已檢視項目、放入其購物車，以及購買項目的使用者。 讓我們繪製所購買項目的轉換率。 在新儲存格中執行下列程式碼，將每個項目的轉換率視覺化：

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="將購買轉換率視覺化":::

## <a name="next-steps"></a>後續步驟

* 若要深入了解 Python 筆記本命令，請參閱[如何使用 Azure Cosmos DB 中的內建筆記本命令和功能](use-python-notebook-features-and-commands.md)一文。
