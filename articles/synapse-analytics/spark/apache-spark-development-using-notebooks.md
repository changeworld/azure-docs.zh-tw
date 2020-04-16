---
title: 建立、開發和維護 Azure 同步工作室(預覽)筆記本
description: 在本文中,您將瞭解如何創建和開發 Azure Synapse Studio(預覽)筆記本,以進行數據準備和可視化。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430223"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>建立、開發和維護 Azure 同步工作室(預覽)筆記本

Azure Synapse Studio(預覽)筆記本是一個 Web 介面,用於建立包含即時代碼、可視化效果和敘述文本的檔。 筆記本是驗證想法和使用快速實驗從數據中獲取見解的好地方。 筆記本還廣泛應用於數據準備、數據可視化、機器學習和其他大數據方案。

使用 Azure 同步工作室筆記本,您可以:

* 開始零設置工作。
* 通過內置的企業安全功能確保數據安全。
* 分析不同原始格式(CSV、txt、JSON 等)、已處理的檔案格式(鑲木地板、三角洲湖、ORC 等)的數據,以及針對 Spark 和 SQL 的 SQL 表格數據檔。
* 通過增強的創作功能和內置數據可視化提高工作效率。

本文介紹如何在 Azure 同步工作室中使用筆記本。

## <a name="create-a-notebook"></a>建立 Notebook

創建筆記本有兩種方法。 您可以創建新筆記本或將現有筆記本從**物件資源管理員**導入 Azure 同步工作區。 Azure Synapse 工作室筆記本可以識別標準 Jupyter 筆記本 IPYNB 檔。

![突觸-創建-導入-筆記本](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>開發筆記本

筆記本由單元格組成,單元格是可獨立運行或作為組運行的代碼或文本的單個塊。

### <a name="add-a-cell"></a>新增儲存格

有多種方法可以向筆記本添加新單元格。

1. 展開左上角 **+ 單元格**按鈕,然後選擇 **「添加代碼單元格**」或 **「添加文字單元格**」。

    ![帶儲存格的儲存格](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 將滑鼠懸停在兩個單元格之間的空間上,然後選擇 **「添加代碼**」或 **「添加文字**」。

    ![空間間的附加儲存格](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. 在[指令模式使用捷徑](#shortcut-keys-under-command-mode)。 按**A**可在當前儲存格上方插入單元格。 按**B**可在當前單元格下方插入單元格。

### <a name="set-a-primary-language"></a>設定主要語言

Azure Synapse 工作室筆記本支援四種火花語言:

* 火花(蛇)
* 火花(斯卡拉)
* 火花SQL
* Spark.NET (C#)

可以從頂部命令列中的下拉清單中設置新添加的儲存格的主語言。

   ![預設突觸語言](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>使用多種語言

通過在單元格的開頭指定正確的語言魔術命令,可以在一個筆記本中使用多種語言。 下表列出了切換單元格語言的神奇命令。

|魔法命令 |Language | 描述 |  
|---|------|-----|
|%pyspark| Python | 針對 Spark 上下文執行**Python**查詢。  |
|%%火花| Scala | 針對Spark上下文執行**Scala**查詢。  |  
|%sql| SparkSQL | 針對Spark上下文執行**SparkSQL**查詢。  |
|%csharp | Spark.NET C# | 針對 Spark 上下文執行**Spark.NET C#** 查詢。 |

下圖是如何使用 **%%-pyspark**魔術命令編寫 PySpark 查詢的範例,或者使用**Spark(Scala)** 筆記本中 **%%sql**魔術命令的 SparkSQL 查詢。 請注意,筆記本的主要語言設置為 Scala。

   ![突觸-火花魔法](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>使用暫存表跨語言引用資料

您不能在 Synapse Studio 筆記本中直接跨不同語言引用數據或變數。 在 Spark 中,可以跨語言引用臨時表。 下面是如何在`Scala``PySpark`中 讀取數據幀`SparkSQL`和使用 Spark 臨時表作為解決方法的示例。

1. 在單元 1 中,使用 Scala 從 SQL 池連接器讀取數據幀並創建臨時表。

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 在單元格 2 中,使用 Spark SQL 查詢數據。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 在單元格 3 中,使用 PySpark 中的數據。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 風格的感知

Azure Synapse 工作室筆記本與摩納哥編輯器集成,將 IDE 風格的 IntelliSense 引入單元編輯器。 語法突出顯示、錯誤創建器和自動代碼完成可説明您更快地編寫代碼和識別問題。

IntelliSense 功能在不同的語言中處於不同的成熟度級別。 使用下表查看支援的內容。

|Languages| 語法高光 | 語法錯誤標記  | 語法代碼完成 | 可變程式碼完成| 系統功能代碼完成| 使用者功能代碼完成| 智慧縮排 | 代碼折疊|
|--|--|--|--|--|--|--|--|--|
|皮火花(Python)|是|是|是|是|是|是|是|是|
|火花(斯卡拉)|是|是|是|是|-|-|-|是|
|SparkSQL|是|是|-|-|-|-|-|-|
|Spark.NET (C#)|是|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>使用工具列按鈕設定文字儲存格的格式

您可以使用文字儲存格工具列中的格式按鈕執行常見的標記操作。 它包括粗體文本、斜體文本、插入代碼段、插入無序列表、插入有序清單和從 URL 插入圖像。

  ![突觸-文字-儲存格-工具列](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>復原儲存格操作
按一下**復原**按鈕或按**Ctrl_Z**復原最新的儲存格操作。 現在,您可以撤銷最多最新的 20 個歷史單元格操作。 

   ![突觸-撤銷細胞](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>移動儲存格

選擇橢圓 (...) 以造訪最右側的其他單元格操作功能表。 然後選擇**向上移動儲存格**或**向下移動單元格**以移動當前儲存格。 

您可以[使用捷徑](#shortcut-keys-under-command-mode)。 按**Ctrl_Alt+** 向上移動當前儲存格。 按**Ctrl_Alt+** 可向下移動當前儲存格。

   ![移動儲存格](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>刪除儲存格

要刪除單元格,請選擇橢圓 (...) 以造訪最右側的其他單元格操作功能表,然後選擇 **「刪除單元格**」。 

您可以[使用捷徑](#shortcut-keys-under-command-mode)。 按**D,D**以刪除當前儲存格。
  
   ![刪除儲存格](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>收起儲存格輸入
按下目前儲存格底部的箭頭按鈕將其摺疊。 要展開它,請在單元格摺疊時按一下箭頭按鈕。

   ![收起儲存單位輸入](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>折疊儲存格輸出

按一下目前單元輸出左上角的**折疊輸出**按鈕將其摺疊。 要展開它,請在單元格輸出摺疊時按一**下 「顯示單元格輸出**」。

   ![折疊單位輸出](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>執行筆記本

您可以單獨或全部執行筆記本中的代碼單元格。 筆記本中表示每個單元格的狀態和進度。

### <a name="run-a-cell"></a>執行儲存格

在單元格中運行代碼的方法有多種。

1. 將滑鼠移動在要執行的儲存格上,然後選擇 **「執行儲存格」** 按鈕或按**Ctrl_Enter**。

   ![執行單元-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 要訪問最右側的其他單元格操作功能表,請選擇橢圓 **(...)。** 然後,選擇 **「運行單元格**」。。

   ![執行單元-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. 在[指令模式使用捷徑](#shortcut-keys-under-command-mode)。 按**Shift_Enter**可運行當前儲存格並選擇下面的儲存格。 按**Alt_Enter**可運行當前單元格,並在下方插入新單元格。


### <a name="run-all-cells"></a>執行所有儲存格
按下「**全部執行」** 按鈕以按順序執行目前的筆記本中的所有單元格。

   ![執行全單元](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>執行上面或下方的所有儲存格

要訪問最右側的其他單元格操作功能表,請選擇橢圓 **(...)。** 然後,選擇**上面的"運行單元格**"以按順序運行當前上方的所有單元格。 選擇**下面的「執行儲存格**」以按順序執行目前下的所有單元格。

   ![執行儲存格 ─上面或以下](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>儲存格狀態指示器

儲存格下方將顯示分步儲存格執行狀態,以説明您查看其當前進度。 單元運行完成後,將顯示一個執行摘要,其中將顯示總持續時間和結束時間,並保留在那裡供將來參考。

![儲存格狀態](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>火花進度指示器

Azure Synapse 工作室筆記本純基於 Spark。 代碼單元在Spark池上遠端執行。 Spark 作業進度指示器提供即時進度列,似乎可説明您瞭解作業執行狀態。


![火花-進度指示器](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>火花工作階段設定

您可以指定超時持續時間、數量和執行器的大小,以便將其授予**配置作業**階段中的當前 Spark 工作階段。 重新啟動 Spark 會話使配置更改生效。 清除所有緩存的筆記本變數。

![會話-mgmt](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>將資料帶到筆記本上

您可以載入來自 Azure Blob 儲存、Azure 資料湖儲存第 2 代和 SQL 池的數據,如下代碼範例所示。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>從 Azure 資料儲存第 2 代將 CSV 為 Spark 資料幀讀取

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

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>從 Azure Blob 儲存中讀取 CSV 為 Spark 資料幀

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

### <a name="read-data-from-the-primary-storage-account"></a>從家儲存帳戶讀取資料

您可以直接存取主儲存帳戶中的數據。 不需要提供密鑰。 在資料資源管理員中,右鍵按一下檔案並選擇 **「新建筆記本**」以查看具有數據提取器自動生成的新筆記本。

![資料到儲存格](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>視覺化筆記型資料

### <a name="display"></a>顯示()

提供表格結果視圖的選項,用於創建條形圖、折線圖、餅圖、散點圖和面積圖。 無需編寫代碼即可可視化資料。 圖表可以在**圖表選項**中自定義。 

預設情況下 **,%%sql**魔術命令的輸出將顯示在呈現的表視圖中。 您可以在 Spark 資料幀或彈性分散式資料集 (RDD) 函數上調用**display(`<DataFrame name>` ** ) 以生成呈現的表視圖。

   ![內置圖表](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>顯示 HTML()

您可以使用**顯示 HTML()** 渲染 HTML 或互動式庫(如**散景**)。

下圖是使用**散景**在地圖上繪製字形的示例。

   ![散景範例](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

運行以下範例代碼以繪製上面的圖像。

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

您可以在工作區中保存單個筆記本或所有筆記本。

1. 要保存對單個筆記本所做的更改,請選擇筆記本命令列上的 **「發佈**」按鈕。

   ![發佈筆記本](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 要將工作區中的所有筆記本保存,請選擇工作區命令列上的「**發布所有**」按鈕。 

   ![發佈-全部](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

在筆記本屬性中,可以配置是否在保存時包括單元格輸出。

   ![筆記本屬性](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>魔法命令
您可以在 Azure Synapse 工作室筆記本中使用您熟悉的 Jupyter 魔術命令。 選中下面的清單,作為當前可用的魔術命令。 在 GitHub 上告訴我們您的用例,以便我們繼續構建更多神奇的命令,以滿足您的需求。

可用的行魔術: [%lsmagic,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic) [%時間](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%時間it](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

可用的細胞魔法: [%%時間](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%時間it,](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit) [%%捕獲](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%寫檔案](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql,](#use-multiple-languages) [%%pyspark,](#use-multiple-languages) [%火花](#use-multiple-languages), [%csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>快速鍵

與 Jupyter 筆記本類似,Azure Synapse 工作室筆記本具有模式用戶介面。 鍵盤會根據筆記本單元處於哪種模式執行不同操作。 Synapse Studio 筆記本支援給定代碼單元的以下兩種模式:命令模式和編輯模式。

1. 當沒有提示您鍵入的文本游標時,單元格處於命令模式。 當單元格處於命令模式時,可以編輯整個筆記本,但不能鍵入單個單元格。 通過按`ESC`或使用滑鼠按一下單元格的編輯器區域外部進入命令模式。

   ![命令模式](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 編輯模式由文字游標指示,提示您在編輯器區域鍵入。 當儲存格處於編輯模式時,無法鍵入單元格。 通過按下`Enter`或使用滑鼠按一下單下單元格的編輯器區域進入編輯模式。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>命令模式下的捷徑

使用以下擊鍵快捷方式,您可以更輕鬆地在 Azure Synapse 筆記本中導航和運行代碼。

| 動作 |突觸工作室筆記本快捷方式  |
|--|--|
|執行目前儲存格並選擇以下內容 | Shift+Enter |
|執行目前儲存格在下方插入 | Alt+Enter |
|在上面選擇儲存格| 上移 |
|選擇下面的儲存格| 向下 |
|在上面插入儲存格| A |
|在下面插入儲存格| B |
|將選取的儲存格到上面| Shift + 向上鍵 |
|在下面延伸選取格| Shift + 向下鍵|
|向上移動儲存格| Ctrl_Alt__ |
|向下移動儲存格| Ctrl_Alt_ |
|刪除選取的儲存格| D, D |
|切換到編輯模式| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>編輯模式下的捷徑

使用以下擊鍵快捷方式,在"編輯"模式下,您可以更輕鬆地在 Azure 同步筆記本中導航和運行代碼。

| 動作 |突觸工作室筆記本快捷方式  |
|--|--|
|向上移動游標 | 上移 |
|向下移游標|向下|
|復原|Ctrl + Z|
|取消復原|Ctrl + Y|
|註解/取消註解|Ctrl = /|
|刪除之前的單字|Ctrl = 後空間|
|刪除單字後|Ctrl = 移除|
|跳到儲存格啟動|Ctrl + Home|
|跳到儲存格端 |Ctrl + End|
|向左走一個字|Ctrl = 左側|
|向右走一個字|Ctrl = 右側|
|全選|Ctrl + A|
|Indent| Ctrl ||
|縮排|Ctrl ||
|切換到命令模式| Esc |

## <a name="next-steps"></a>後續步驟

- [阿帕奇火花文檔的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
