---
title: 視覺效果
description: 使用 Azure Synapse 筆記本來將您的資料視覺化
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 73b18d15ad054f1c485d6f61cdefe54993148bc4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450535"
---
# <a name="visualize-data"></a>顯現資料
Azure Synapse 是一種整合式分析服務，可在資料倉儲和大型資料分析系統之間加快見解的時間。 資料視覺效果是一個重要元件，可深入瞭解您的資料。 它可協助您更輕鬆地讓人們瞭解大型資料和小型資料。 它也可讓您更輕鬆地偵測資料群組中的模式、趨勢和極端值。 

使用 Azure Synapse Analytics 中的 Apache Spark 時，有各種內建選項可協助您將資料視覺化，包括 Synapse 筆記本圖表選項、存取常用的開放原始碼程式庫，以及與 Synapse SQL 和 Power BI 的整合。

## <a name="notebook-chart-options"></a>筆記本圖表選項
使用 Azure Synapse 筆記本時，您可以使用圖表選項，將表格式結果檢視轉換成自訂圖表。 在這裡，您可以將資料視覺化，而不需要撰寫任何程式碼。 

### <a name="displaydf-function"></a>顯示 (df) 函數
```display```函數可讓您將 SQL 查詢和 Apache Spark 資料框架和 rdd 轉換成豐富的資料視覺效果。```display```函數可用於在 PySpark、Scala、JAVA 和 .net 中建立的資料框架或 rdd。

若要存取圖表選項：
1. ```%%sql```魔術命令的輸出預設會出現在轉譯的表格視圖中。 您也可以 ```display(df)``` 在 Spark 資料框架或復原的分散式資料集上呼叫 (RDD) 函數來產生轉譯的資料表視圖。 
   
2. 一旦有了轉譯的表格視圖，請切換至圖表視圖。
   ![內建圖表](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. 您現在可以藉由指定下列值來自訂視覺效果：
   | 組態 | 描述 |
   |--|--| 
   | 圖表類型 | ```display```函數支援各式各樣的圖表類型，包括橫條圖、散佈圖、折線圖和其他 |
   | 答案 | 指定 X 軸的值範圍|
   | 值 | 指定 y 軸值的值範圍 |
   | 序列群組 | 用來判斷匯總的群組 | 
   | 彙總 | 匯總視覺效果中資料的方法| 
   
   
    > [!NOTE]
    > 根據預設，函式 ```display(df)``` 只會採用前1000個數據列的資料來呈現圖表。 檢查 **所有結果的匯總** ，然後按一下 [套用 **] 按鈕，** 就會從整個資料集套用圖表產生。 當圖表設定變更時，將會觸發 Spark 作業。 請注意，完成計算並轉譯圖表可能需要幾分鐘的時間。
   
4. 完成之後，您就可以查看最終的視覺效果並與其互動！

### <a name="displaydf-statistic-details"></a>顯示 (df) 統計資料詳細資料
您可以使用 <code>display(df, summary = true)</code> 來檢查指定 Apache Spark 資料框架的統計資料摘要，其中包含資料行名稱、資料行類型、唯一值，以及每個資料行的遺漏值。 您也可以選取特定資料行，以查看其最小值、最大值、mean 值和標準差。
    ![內建圖表-摘要](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>displayHTML (df) 選項
Azure Synapse Analytics 筆記本使用函數支援 HTML 圖形 ```displayHTML``` 。

下圖是使用 [D3.js](https://d3js.org/)建立視覺效果的範例。

   ![d3-js-範例](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

執行下列程式碼以建立上面的視覺效果。

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>熱門程式庫
在資料視覺效果中，Python 提供多個以許多不同功能封裝的圖形程式庫。 依預設，Azure Synapse Analytics 中的每個 Apache Spark 集區都包含一組策劃和熱門的開放原始碼程式庫。 您也可以使用 Azure Synapse Analytics 程式庫管理功能，新增或管理 & 版本的額外程式庫。 

### <a name="bokeh"></a>Bokeh
您可以使用來呈現 HTML 或互動式程式庫（例如 **bokeh**） ```displayHTML(df)``` 。 

下圖是使用 **bokeh** 在地圖上繪製圖像的範例。

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

執行下列範例程式碼以繪製上述影像。

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```

### <a name="matplotlib"></a>Matplotlib
您可以使用每個程式庫的內建轉譯功能，轉譯標準繪圖程式庫（例如 Matplotlib）。

下圖是使用 **Matplotlib** 建立橫條圖的範例。
   ![折線圖範例。](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

執行下列範例程式碼以繪製上述影像。

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```

### <a name="additional-libraries"></a>其他程式庫 
除了這些程式庫之外，Azure Synapse Analytics 執行時間也包含下列一組通常用於資料視覺效果的程式庫：
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 

如需可用程式庫和版本的最新資訊，您可以流覽 Azure Synapse Analytics 執行時間 [檔](./spark/../apache-spark-version-support.md) 。

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>使用 Apache Spark & SQL 隨選 Power BI 連接到
Azure Synapse Analytics 與 Power BI 緊密整合，可讓資料工程師建立分析解決方案。

Azure Synapse Analytics 可讓不同的工作區計算引擎共用其 Spark 集區與無伺服器 SQL 集區之間的資料庫和資料表。 使用 [共用元資料模型](https://docs.microsoft.com/azure/synapse-analytics/metadata/overview)，您可以使用 SQL 隨選查詢 Apache Spark 資料表。 完成之後，您可以將 SQL 隨選端點連線到 Power BI，以便輕鬆地查詢同步的 Spark 資料表。


## <a name="next-steps"></a>後續步驟
- 如需有關如何設定 Spark SQL DW 連接器的詳細資訊： [SYNAPSE SQL Connector](./spark/../synapse-spark-sql-pool-import-export.md)
- 查看預設程式庫： [Azure Synapse Analytics 運行](../spark/apache-spark-version-support.md)時間