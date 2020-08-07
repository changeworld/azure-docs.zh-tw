---
title: Synapse Studio 筆記本
description: 在本文中，您將瞭解如何建立及開發 Azure Synapse Studio (預覽) 筆記本，以進行資料準備和視覺化。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 6e0062450889a2bbbdfcd47137ffbe36b83cae57
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849093"
---
# <a name="create-develop-and-maintain-synapse-studio-preview-notebooks-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中建立、開發及維護 Synapse Studio (preview) 筆記本

Synapse Studio (preview) 筆記本是一個 web 介面，可讓您建立檔案，其中包含即時程式碼、視覺效果和敘述文字。 筆記本是驗證想法和使用快速實驗從您的資料取得見解的絕佳位置。 筆記本也廣泛用於資料準備、資料視覺效果、機器學習和其他巨量資料案例。

使用 Azure Synapse Studio 筆記本，您可以：

* 無須設定即可開始使用。
* 使用內建的企業安全性功能保護資料的安全。
* 分析包括原始格式 (CSV、txt、JSON 等)、經處理的檔案格式 (parquet、Delta Lake、ORC 等)，以及針對 Spark 和 SQL 的 SQL 表格式資料檔案等各種資料。
* 透過增強的撰寫功能和內建的資料視覺效果，提高生產力。

本文說明如何在 Azure Synapse Studio 中使用筆記本。

## <a name="create-a-notebook"></a>建立 Notebook

建立筆記本的方法有兩種。 您可以從**物件總管**建立新的筆記本，也可以將現有的筆記本匯入到 Azure Synapse 工作區。 Azure Synapse Studio 筆記本可以辨識標準 Jupyter Notebook IPYNB 檔案。

![synapse-create-import-notebook](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>開發筆記本

筆記本是由儲存格所組成，是可以獨立或以群組方式執行的個別程式碼區塊或文字。

### <a name="add-a-cell"></a>新增儲存格

有多種方式可將新的儲存格新增至您的筆記本。

1. 展開左上方的 [+ 儲存格] 按鈕，然後選取 [加入程式碼儲存格] 或 [加入文字儲存格]。

    ![add-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 將游標停留在兩個儲存格之間的空間，然後選取 [新增程式碼] 或 [新增文字]。

    ![add-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. 使用 [命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按下 [A]，將儲存格插入目前的儲存格上方。 按下 [B]，將儲存格插入目前的儲存格下方。

### <a name="set-a-primary-language"></a>設定主要語言

Azure Synapse Studio 筆記本支援四種 Apache Spark 語言：

* pySpark (Python)
* Spark (Scala)
* SparkSQL
* 適用於 Apache Spark 的 .NET (C#)

您可以從頂端命令列的下拉式清單中，為新加入的儲存格設定主要語言。

   ![default-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>使用多種語言

您可以在儲存格的開頭指定正確的語言 magic 命令，以便在同個筆記本中使用多種語言。 下表列出用來切換儲存格語言的 magic 命令。

|Magic 命令 |Language | 描述 |  
|---|------|-----|
|%%pyspark| Python | 針對 Spark 內容執行 **Python** 查詢。  |
|%%spark| Scala | 針對 Spark 內容執行 **Scala** 查詢。  |  
|%%sql| SparkSQL | 針對 Spark 內容執行 **SparkSQL** 查詢。  |
|%%csharp | 適用於 Spark C# 的 .NET | 針對 Spark 內容執行**適用於 Spark C# 的 .NET** 查詢。 |

下圖是如何使用 **%%pyspark** magic 命令，或在 **Spark(Scala)** 筆記本中使用 **%%sql** magic 命令的 SparkSQL 查詢，以便撰寫 PySpark 查詢的範例。 請注意，筆記本的主要語言會設定為 pySpark。

   ![synapse-spark-magics](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>使用暫存資料表跨語言參考資料

您無法直接在 Synapse Studio 筆記本中的不同語言之間參考資料或變數。 在 Spark 中，可以跨語言參考暫存資料表。 以下範例說明如何使用 Spark 暫存資料表做為因應措施，讀取 `PySpark` 和 `SparkSQL` 中的 `Scala` 資料框架。

1. 在儲存格 1 中，使用 Scala 從 SQL 集區連接器讀取資料框架，並建立暫存資料表。

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 在儲存格 2 中，使用 Spark SQL 查詢資料。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 在儲存格 3 中，使用 PySpark 中的資料。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>整合式開發環境 (IDE) 樣式的 IntelliSense

Azure Synapse Studio 與 Monaco 編輯器整合，可將整合式開發環境樣式的 IntelliSense 帶入儲存格編輯器。 語法醒目提示、錯誤標記和自動程式碼完成可協助您撰寫程式碼並更快速地找出問題。

IntelliSense 功能在不同語言的成熟度層級不同。 使用下表來檢視支援的功能。

|Languages| 語法醒目提示 | 語法錯誤標記  | 語法程式碼完成 | 變數程式碼完成| 系統函數程式碼完成| 使用者函式程式碼完成| 智慧縮排 | 程式碼摺疊功能|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|是|是|是|是|是|是|是|是|
|Spark (Scala)|是|是|是|是|-|-|-|是|
|SparkSQL|是|是|-|-|-|-|-|-|
|適用於 Spark (C#) 的 .NET|是|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>使用工具列按鈕格式化文字儲存格

您可以使用 [文字儲存格] 工具列中的 [格式] 按鈕來執行常見的 Markdown 動作。 其中包含粗體文字、斜體文字、插入程式碼片段、插入未排序的清單、插入已排序的清單，以及從 URL 插入影像。

  ![synapse-text-cell-toolbar](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>復原儲存格作業
按一下 [復原] 按鈕，或按下 **Ctrl + Z** 撤銷最近的儲存格作業。 現在您可以復原最近的 20 個歷史儲存格動作。 

   ![synapse-undo-cells](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>移動儲存格

選取省略符號 (...)，即可存取最右側的其他儲存格動作功能表。 然後選取 [向上移動儲存格] 或 [向下移動儲存格] 以移動目前的儲存格。 

您也可以使用 [命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按下 **Ctrl+Alt+↑** 上移目前的儲存格。 按下 **Ctrl+Alt+↓** 下移目前的儲存格。

   ![move-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>刪除儲存格

若要刪除儲存格，請選取省略符號 (...)，以存取最右側的其他儲存格動作功能表，然後選取 [刪除儲存格]。 

您也可以使用 [命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按下 **D,D** 以刪除目前的儲存格。
  
   ![delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>摺疊儲存格輸入
按一下目前儲存格底部的箭號按鈕予以摺疊。 若要將它展開，請在儲存格摺疊時按一下箭號按鈕。

   ![collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>摺疊儲存格輸出

按一下目前儲存格輸出左上方的 [摺疊輸出] 按鈕將其摺疊。 若要將它展開，請在儲存格輸出摺疊時，按一下 [顯示儲存格輸出]。

   ![collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>執行筆記本

您可以個別或一次全部在您的筆記本中執行程式碼儲存格。 每個儲存格的狀態和進度都會在筆記本中表示。

### <a name="run-a-cell"></a>執行儲存格

有數種方式可以在儲存格中執行程式碼。

1. 將游標停留在您想要執行的儲存格上，然後選取 [執行儲存格] 按鈕，或按下 **Ctrl+Enter**。

   ![run-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 若要存取最右側的其他儲存格動作功能表，請選取省略符號 ( **...** )。然後，選取 [執行儲存格]。

   ![run-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. 使用 [命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按下 **Shift+Enter** 以執行目前的儲存格，然後選取下方的儲存格。 按下 **Alt+Enter** 以執行目前的儲存格，並在下方插入新的儲存格。


### <a name="run-all-cells"></a>執行所有儲存格
按一下 [全部執行] 按鈕，依序執行目前筆記本中的所有儲存格。

   ![run-all-cells](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>執行上方或下方所有儲存格

若要存取最右側的其他儲存格動作功能表，請選取省略符號 ( **...** )。然後，選取 [執行上方的儲存格]，依序執行目前儲存格上方的所有儲存格。 選取 [執行下方的儲存格]，依序執行目前儲存格下方的所有儲存格。

   ![run-cells-above-or-below](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>取消所有正在執行的儲存格
按一下 [**全部取消**] 按鈕，取消正在等候佇列中的執行中儲存格或儲存格。 
   ![全部取消-資料格](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

### <a name="cell-status-indicator"></a>儲存格狀態指標

儲存格下方會逐步顯示儲存格執行狀態，以協助您檢視目前的進度。 儲存格執行完成之後，會顯示包含總持續時間和結束時間的執行摘要，並保留在該處供日後參考。

![cell-status](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 進度指示器

Azure Synapse Studio 筆記本純粹是以 Spark 為基礎。 程式碼儲存格會從遠端在 Spark 集區上執行。 系統隨即會提供具有即時進度列的 Spark 作業進度列指示器，以協助您瞭解作業執行狀態。
每個作業或階段的工作數可協助您識別 spark 作業的平行層級。 您也可以透過按一下作業 (或階段) 名稱的連結，深入瞭解特定作業的 Spark UI， (或階段) 。


![spark-progress-indicator](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 工作階段設定

您可以在 [設定工作階段] 中指定要提供給目前 Spark 工作階段的執行程式逾時時間、數目和大小。 重新啟動 Spark 工作階段，設定變更才會生效。 所有快取的筆記本變數都會清除。

[![會話管理](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-spark-session-management.png#lightbox)

Spark 會話的推薦現在已可在 spark 會話設定面板上取得。 您可以直接從 [會話設定] 面板中選取 spark 集區，並查看有多少節點正在使用，以及有多少剩餘的執行的執行。 這些資訊可協助您適當地設定會話大小，而不是來回修改。

![會話-建議](./media/apache-spark-development-using-notebooks/synapse-spark-session-recommender.png)


## <a name="bring-data-to-a-notebook"></a>將資料帶入筆記本

您可以從 Azure Blob 儲存體、Azure Data Lake Store Gen 2 和 SQL 集區載入資料，如下列程式碼範例所示。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>從 Azure Data Lake Store Gen2 讀取 CSV 做為 Spark 資料框架

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (blob_container_name, blob_account_name,  blob_relative_path)

spark.conf.set("fs.azure.account.auth.type.%s.dfs.core.windows.net" %account_name, "SharedKey")
spark.conf.set("fs.azure.account.key.%s.dfs.core.windows.net" %account_name ,"Your ADLSg2 Primary Key")

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>以 Spark 資料框架的形式從 Azure Blob 儲存體讀取 CSV

```python

from pyspark.sql import SparkSession
from pyspark.sql.types import *

blob_account_name = "Your blob account name"
blob_container_name = "Your blob container name"
blob_relative_path = "Your blob relative path"
blob_sas_token = "Your blob sas token"

wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)

```

### <a name="read-data-from-the-primary-storage-account"></a>讀取主要儲存體帳戶中的資料

您可以直接存取主要儲存體帳戶中的資料。 不需要提供祕密金鑰。 在 [資料總管] 中，以滑鼠右鍵按一下檔案，然後選取 [新筆記本]，以檢視已自動產生資料解壓縮的新筆記本。

![data-to-cell](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>在筆記本中將資料視覺化

### <a name="produce-rendered-table-view"></a>產生呈現的資料表視圖

表格式結果檢視會提供選項，以建立橫條圖、折線圖、圓形圖、散佈圖和區域圖。 您可以將資料視覺化，而不需要撰寫程式碼。 您可以在 [圖表選項] 中自訂圖表。 

預設會在轉譯的資料表檢視中顯示 **%%sql** magic 命令的輸出。 您可以 <code>display(df)</code> 在 Spark 資料框架或復原的分散式資料集上呼叫， (RDD) 函數來產生轉譯的資料表視圖。

   [![builtin-charts](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png#lightbox)

### <a name="visualize-built-in-charts-from-large-scale-dataset"></a>將大型資料集的內建圖表視覺化 

根據預設，函式 <code>display(df)</code> 只會接受資料的前1000個數據列來轉譯圖表。 檢查**所有結果的匯總**，**然後按一下 [** 套用] 按鈕，即可從整個資料集套用圖表產生。 當圖表設定變更時，將會觸發 spark 作業，需要一些時間來完成計算並轉譯圖表。 
    [![內建-圖表-匯總-全部](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-aggregation-all.png#lightbox)


### <a name="visualize-data-statistic-information"></a>視覺化資料統計資訊
您可以使用 <code>display(df, summary = true)</code> 來檢查給定 Spark 資料框架的統計資料摘要，其中包含每個資料行的資料行名稱、資料行類型、唯一值和遺漏值。 您也可以選取特定資料行，以查看其最小值、最大值、平均值和標準差。
    [內 ![ 建-圖表-摘要 ](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png)](./media/apache-spark-development-using-notebooks/synapse-builtin-charts-summary.png#lightbox)

### <a name="render-html-or-interactive-libraries"></a>呈現 HTML 或互動式程式庫

您可以使用 **displayHTML()** 來呈現 HTML 或互動式程式庫，例如 **bokeh**。

下圖是使用 **bokeh** 在地圖上繪製圖像的範例。

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

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

## <a name="save-notebooks"></a>儲存筆記本

您可以將單一筆記本或所有筆記本儲存在工作區中。

1. 若要儲存對單一筆記本所做的變更，請在筆記本命令列上選取 [發佈] 按鈕。

   ![publish-notebook](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 若要儲存工作區中的所有筆記本，請選取 [工作區] 命令列上的 [全部發佈] 按鈕。 

   ![publish-all](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

在筆記本屬性中，您可以設定是否要在儲存時包含儲存格輸出。

   ![notebook-properties](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic 命令
您可以在 Azure Synapse Studio 筆記本中使用您熟悉的 Jupyter magic 命令。 請檢查下列清單，以取得目前可用的 magic 命令。 請告訴我們您在 GitHub 上的使用案例，讓我們可以繼續建立更多的 magic 命令以符合您的需求。

可用的行 magic： [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)， [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)， [% time it](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

可用的儲存格 magic：[%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)、[%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)、[%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)、[%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)、[%%sql](#use-multiple-languages)、[%%pyspark](#use-multiple-languages)、[%%spark](#use-multiple-languages)、[%%csharp](#use-multiple-languages)


## <a name="orchestrate-notebook"></a>協調筆記本

### <a name="add-a-notebook-to-a-pipeline"></a>將筆記本新增至管線

按一下右上角的 [**新增至管線**] 按鈕，將筆記本新增至現有的管線或建立新的管線。

![新增至管線](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>指定參數資料格

若要參數化您的筆記本，請選取省略號 ( ... ) 以存取最右側的其他 [資料格動作] 功能表。 然後選取 [**切換參數資料格**]，將儲存格指定為 [參數] 資料格。

![切換參數](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

Azure Data Factory 會尋找 [參數] 資料格，並將此資料格視為在執行時傳入之參數的預設值。 執行引擎會在 [參數] 資料格下方加入具有輸入參數的新資料格，以便覆寫預設值。 若未指定任何參數資料格，插入的儲存格將會插入筆記本的頂端。

### <a name="assign-parameters-values-from-a-pipeline"></a>從管線指派參數值

建立具有參數的筆記本之後，您可以使用 Azure Synapse 筆記本活動從管線執行它。 將活動新增至管線畫布之後，您將能夠在 [**設定**] 索引標籤上設定 [**基底參數**] 區段底下的參數值。 

![指派-參數](./media/apache-spark-development-using-notebooks/assign-parameter.png)

指派參數值時，您可以使用[管線運算式語言](../../data-factory/control-flow-expression-language-functions.md)或[系統變數](../../data-factory/control-flow-system-variables.md)。



## <a name="shortcut-keys"></a>快速鍵

與 Jupyter 筆記本類似，Azure Synapse Studio 筆記本具有強制回應的使用者介面。 鍵盤會根據筆記本儲存格所在的模式來執行不同的動作。 Synapse Studio 筆記本針對指定的程式碼儲存格支援下列兩種模式：命令模式和編輯模式。

1. 沒有文字游標提示您輸入時，儲存格就會處於命令模式。 儲存格處於命令模式時，您可以將筆記本當做整體編輯，但無法輸入個別的儲存格。 按下 `ESC` 或使用滑鼠在儲存格的編輯器區域外按一下，進入命令模式。

   ![command-mode](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 編輯模式會以文字游標指示，提示您在編輯器區域中輸入。 當儲存格處於編輯模式時，您可以在資料格中輸入。 按下 `Enter` 或使用滑鼠按一下儲存格的編輯器區域，以進入編輯模式。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>命令模式下的快速鍵

使用下列按鍵快捷鍵，您可以更輕鬆地在 Azure Synapse 筆記本中瀏覽和執行程式碼。

| 動作 |Synapse Studio 筆記本捷徑  |
|--|--|
|執行目前的儲存格並在下方選取 | Shift+Enter |
|執行目前的儲存格並在下方插入 | Alt+Enter |
|選取上方儲存格| 上移 |
|選取下方儲存格| 向下 |
|在上方插入儲存格| A |
|在下方插入儲存格| B |
|展開上方選取的儲存格| Shift+Up |
|展開下方選取的儲存格| Shift+Down|
|向上移動儲存格| Ctrl+Alt+↑ |
|向下移動儲存格| Ctrl+Alt+↓ |
|刪除選取的儲存格| D,D |
|切換至編輯模式| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>編輯模式下的快速鍵

在編輯模式中，使用下列按鍵快捷鍵，您可以更輕鬆地在 Azure Synapse 筆記本中瀏覽和執行程式碼。

| 動作 |Synapse Studio 筆記本捷徑  |
|--|--|
|向上移動資料指標 | 上移 |
|向下移動資料指標|向下|
|復原|Ctrl + Z|
|取消復原|Ctrl + Y|
|註解/取消註解|Ctrl + /|
|刪除此前的文字|Ctrl + 退格鍵|
|刪除此後的文字|Ctrl + Delete|
|移至儲存格開端|Ctrl + Home|
|移至儲存格末端 |Ctrl + End|
|往左移一個單字|Ctrl + 向左鍵|
|往右移一個單字|Ctrl + 向右鍵|
|全選|Ctrl + A|
|縮排| Ctrl + ]|
|Dedent|Ctrl + [|
|切換到命令類型| Esc |

## <a name="next-steps"></a>後續步驟
- [查看 Synapse 範例筆記本](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [快速入門：使用 Web 工具在 Azure Synapse Analytics 中建立 Apache Spark 集區 (預覽)](../quickstart-apache-spark-notebook.md)
- [什麼是 Azure Synapse Analytics 中的 Apache Spark](apache-spark-overview.md)
- [使用適用於 Apache Spark 的 .NET 搭配 Azure Synapse Analytics](spark-dotnet.md)
- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
