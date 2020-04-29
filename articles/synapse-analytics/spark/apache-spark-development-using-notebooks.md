---
title: 建立、開發及維護 Azure Synapse Studio （預覽）筆記本
description: 在本文中，您將瞭解如何建立及開發 Azure Synapse Studio （預覽）筆記本，以進行資料準備和視覺化。
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: ruxu
ms.reviewer: ''
ms.openlocfilehash: 506339cefa90fb17bedfc946f70cb4d7d8047cf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430223"
---
# <a name="create-develop-and-maintain-azure-synapse-studio-preview-notebooks"></a>建立、開發及維護 Azure Synapse Studio （預覽）筆記本

Azure Synapse Studio （預覽）筆記本是一個 web 介面，可讓您建立檔案，其中包含即時程式碼、視覺效果和敘述文字。 筆記本是驗證想法和使用快速實驗從您的資料取得見解的絕佳位置。 筆記本也廣泛用於資料準備、資料視覺效果、機器學習和其他海量資料案例。

使用 Azure Synapse Studio 筆記本，您可以：

* 開始使用零設定。
* 使用內建的企業安全性功能保護資料的安全。
* 跨原始格式（CSV、txt、JSON 等）分析資料、處理過的檔案格式（parquet、Delta Lake、ORC 等），以及針對 Spark 和 SQL 的 SQL 表格式資料檔案。
* 透過增強的撰寫功能和內建的資料視覺效果，提高生產力。

本文說明如何在 Azure Synapse Studio 中使用筆記本。

## <a name="create-a-notebook"></a>建立 Notebook

有兩種方式可以建立筆記本。 您可以從**物件總管**建立新的筆記本，或將現有的筆記本匯入到 Azure Synapse 工作區。 Azure Synapse Studio 筆記本可以辨識標準 Jupyter Notebook IPYNB 檔案。

![synapse-建立-匯入-筆記本](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook.png)

## <a name="develop-notebooks"></a>開發筆記本

筆記本是由儲存格所組成，它們是個別的程式碼區塊，或是可獨立執行或群組的文字。

### <a name="add-a-cell"></a>加入儲存格

有多種方式可將新的資料格新增至您的筆記本。

1. 展開左上方的 [**儲存格**] 按鈕，然後選取 [**加入程式碼資料格**] 或 [**加入文字資料格**]。

    ![[加入儲存格]-[儲存格] 按鈕](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. 將滑鼠停留在兩個數據格之間的空間，然後選取 [**加入程式碼**] 或 [**加入文字**

    ![新增-儲存格-空格之間](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. 使用[命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按**下以將資料**格插入目前的儲存格上方。 按**B** ，將資料格插入目前的儲存格下方。

### <a name="set-a-primary-language"></a>設定主要語言

Azure Synapse Studio 筆記本支援四種 spark 語言：

* pyspark （python）
* spark （Scala）
* sparkSQL
* Spark.NET （c #）

您可以從頂端命令列的下拉式清單中，為新加入的資料格設定主要語言。

   ![預設-synapse-language](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>使用多種語言

您可以在資料格的開頭指定正確的語言魔術命令，以在一個筆記本中使用多種語言。 下表列出用來切換資料格語言的神奇命令。

|魔術命令 |Language | 描述 |  
|---|------|-----|
|%% pyspark| Python | 針對 Spark 內容執行**Python**查詢。  |
|%% spark| Scala | 針對 Spark 內容執行**Scala**查詢。  |  
|%% sql| SparkSQL | 針對 Spark 內容執行**SparkSQL**查詢。  |
|%% csharp | Spark.NET C# | 針對 Spark 內容執行**Spark.NET c #** 查詢。 |

下圖是如何使用 **%% PySpark**魔術命令，或在**Spark （Scala）** 筆記本中具有 **%% sql**神奇命令的 SparkSQL 查詢，來撰寫 PySpark 查詢的範例。 請注意，筆記本的主要語言會設定為 Scala。

   ![synapse-spark-magic](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>使用臨時表跨語言參考資料

您無法直接在 Synapse Studio 筆記本中的不同語言之間參考資料或變數。 在 Spark 中，可以跨語言參考臨時表。 以下範例說明如何在中`Scala` `PySpark`讀取資料框架，並`SparkSQL`使用 Spark 臨時表做為因應措施。

1. 在儲存格1中，使用 Scala 從 SQL 集區連接器讀取資料框架，並建立臨時表。

   ```scala
   %%scala
   val scalaDataFrame = spark.read.option("format", "DW connector predefined type")
   scalaDataFrame.registerTempTable( "mydataframetable" )
   ```

2. 在儲存格2中，使用 Spark SQL 來查詢資料。
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. 在儲存格3中，使用 PySpark 中的資料。

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE 樣式的 IntelliSense

Azure Synapse Studio 筆記本與摩納哥編輯器整合，可將 IDE 樣式的 IntelliSense 帶入資料格編輯器。 語法醒目提示、錯誤製作程式和自動程式碼完成可協助您撰寫程式碼並更快速地識別問題。

IntelliSense 功能在不同語言的成熟度層級不同。 使用下表來查看支援的功能。

|Languages| 語法醒目提示 | 語法錯誤標記  | 語法程式碼完成 | 變數程式碼完成| 系統函數程式碼完成| 使用者函式程式碼完成| 智慧縮排 | 程式碼折迭|
|--|--|--|--|--|--|--|--|--|
|PySpark （Python）|是|是|是|是|是|是|是|是|
|Spark （Scala）|是|是|是|是|-|-|-|是|
|SparkSQL|是|是|-|-|-|-|-|-|
|Spark.NET （c #）|是|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>使用工具列按鈕格式化文字資料格

您可以使用 [文字資料格] 工具列中的 [格式] 按鈕來執行常見的 markdown 動作。 其中包含粗體文字、italicizing 文字、插入程式碼片段、插入未排序的清單、插入已排序的清單，以及從 URL 插入影像。

  ![synapse-文字-資料格-工具列](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

### <a name="undo-cell-operations"></a>復原資料格作業
按一下 [**復原**] 按鈕，或按**Ctrl + Z**撤銷最近的資料格作業。 現在您可以復原到最新的20個歷史資料格動作。 

   ![synapse-復原-資料格](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)

### <a name="move-a-cell"></a>移動資料格

選取省略號（...），即可存取最右側的其他 [資料格動作] 功能表。 然後選取 [**將資料格上移**] 或 [**下移資料格**]，以移動目前的儲存格。 

您也可以使用[命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按**Ctrl + Alt + ↑**以向上移動目前的儲存格。 按**Ctrl + Alt + ↓** ，將目前的資料格向下移動。

   ![移動-a-cell](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

### <a name="delete-a-cell"></a>刪除儲存格

若要刪除資料格，請選取省略號（...），以存取最右側的其他 [資料格動作] 功能表，然後選取 [**刪除儲存格**]。 

您也可以使用[命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按**d，d**可刪除目前的儲存格。
  
   ![刪除-a-cell](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

### <a name="collapse-a-cell-input"></a>折迭資料格輸入
按一下目前資料格底部的箭號按鈕以折迭它。 若要將它展開，請在資料格折迭時按一下箭號按鈕。

   ![折迭-資料格-輸入](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

### <a name="collapse-a-cell-output"></a>折迭資料格輸出

按一下目前資料格輸出左上方的 [折迭**輸出**] 按鈕，以折迭它。 若要展開它，請按一下 [在資料格輸出折迭時**顯示資料格輸出**]。

   ![折迭-資料格-輸出](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

## <a name="run-notebooks"></a>執行筆記本

您可以個別或一次在您的筆記本中執行程式碼資料格。 每個資料格的狀態和進度都會以筆記本表示。

### <a name="run-a-cell"></a>執行資料格

有數種方式可以在儲存格中執行程式碼。

1. 將滑鼠停留在您想要執行的資料格上，然後選取 [**執行儲存格**] 按鈕或按**Ctrl + Enter**。

   ![執行-cell-1](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)


2. 若要存取最右側的其他 [資料格動作] 功能表，請選取省略號（**...**）。然後，選取 [**執行儲存格**]。

   ![執行-cell-2](./media/apache-spark-development-using-notebooks/synapse-run-cell-2.png)
   
3. 使用[命令模式下的快速鍵](#shortcut-keys-under-command-mode)。 按**Shift + enter**以執行目前的儲存格，然後選取下方的資料格。 按**Alt + Enter**以執行目前的儲存格，並在下方插入新的資料格。


### <a name="run-all-cells"></a>執行所有資料格
按一下 [**全部執行**] 按鈕，依序執行目前筆記本中的所有儲存格。

   ![全部執行-資料格](./media/apache-spark-development-using-notebooks/synapse-run-all.png)

### <a name="run-all-cells-above-or-below"></a>執行上方或下方的所有資料格

若要存取最右側的其他 [資料格動作] 功能表，請選取省略號（**...**）。然後，選取 [**執行上方的儲存格**]，依序執行目前的所有資料格。 選取**下方**的 [執行資料格]，依序執行目前的所有資料格。

   ![執行單元-上方-或以下](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cell-status-indicator"></a>資料格狀態指標

資料格下方會顯示逐步驟的資料格執行狀態，以協助您查看其目前的進度。 儲存格執行完成之後，會顯示包含總持續時間和結束時間的執行摘要，並保留在該處供日後參考。

![資料格-狀態](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark 進度列指示器

Azure Synapse Studio 筆記本純粹是以 Spark 為基礎。 程式碼資料格會從遠端在 Spark 集區上執行。 隨即會提供即時進度列的 Spark 作業進度列指示器，以協助您瞭解作業執行狀態。


![spark-進度-指標](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark 會話設定

您可以指定要提供給 [**設定會話**] 中目前 Spark 會話的超時時間、數目和執行的大小。 重新開機 Spark 會話，設定變更才會生效。 所有快取的筆記本變數都會清除。

![會話-管理](./media/apache-spark-development-using-notebooks/synapse-spark-session-mgmt.png)


## <a name="bring-data-to-a-notebook"></a>將資料帶入筆記本

您可以從 Azure Blob 儲存體、Azure Data Lake Store Gen 2 和 SQL 集區載入資料，如下列程式碼範例所示。

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>從 Azure Data Lake Store Gen2 讀取 CSV 作為 Spark 資料框架

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

### <a name="read-data-from-the-primary-storage-account"></a>從主要儲存體帳戶讀取資料

您可以直接存取主要儲存體帳戶中的資料。 不需要提供秘密金鑰。 在資料總管中，以滑鼠右鍵按一下檔案，然後選取 [**新增筆記本**]，以查看已自動產生資料解壓縮的新筆記本。

![資料到儲存格](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="visualize-data-in-a-notebook"></a>將筆記本中的資料視覺化

### <a name="display"></a>Display （）

表格式結果檢視會提供選項，以建立橫條圖、折線圖、圓形圖、散佈圖和區域圖。 您可以將資料視覺化，而不需要撰寫程式碼。 您可以在**圖表選項**中自訂圖表。 

根據預設， **%% sql**魔術命令的輸出會出現在轉譯的資料表視圖中。 您可以呼叫 Spark 資料框架或復原分散式資料集（RDD）函數上的**display （`<DataFrame name>`）** ，以產生轉譯的資料表視圖。

   ![內建-圖表](./media/apache-spark-development-using-notebooks/synapse-builtin-charts.png)

### <a name="displayhtml"></a>DisplayHTML()

您可以使用**displayHTML （）** 來呈現 HTML 或互動式程式庫，例如**bokeh**。

下圖是使用**bokeh**透過地圖繪製圖像的範例。

   ![bokeh-範例](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png)
   

執行下列範例程式碼，以繪製上述影像。

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

1. 若要儲存對單一筆記本所做的變更，請選取 [筆記本] 命令列上的 [**發佈**] 按鈕。

   ![發佈-筆記本](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. 若要儲存工作區中的所有筆記本，請選取工作區命令列上的 [**全部發佈**] 按鈕。 

   ![發行-全部](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

在 [筆記本] 屬性中，您可以設定是否要在儲存時包含資料格輸出。

   ![筆記本-屬性](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>魔術命令
您可以在 Azure Synapse Studio 筆記本中使用您熟悉的 Jupyter 魔術命令。 請檢查下列清單，以取得目前可用的魔術命令。 請告訴我們您在 GitHub 上的使用案例，讓我們可以繼續建立更多的神奇命令以符合您的需求。

可用的行 magic： [% lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic)， [% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)， [% timeit.exe](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

可用的資料格 magic： [%% time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time)， [%% timeit.exe](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)， [%% capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture)， [%% writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile)， [%% sql](#use-multiple-languages)， [%% pyspark](#use-multiple-languages)， [%% spark](#use-multiple-languages)， [%% csharp](#use-multiple-languages)

## <a name="shortcut-keys"></a>快速鍵

類似于 Jupyter 筆記本，Azure Synapse Studio 筆記本具有強制回應的使用者介面。 鍵盤會根據筆記本儲存格所在的模式來執行不同的動作。 Synapse Studio 筆記本針對指定的程式碼資料格支援下列兩種模式：命令模式和編輯模式。

1. 當沒有文字游標提示您輸入時，儲存格就會處於命令模式。 當儲存格處於命令模式時，您可以將筆記本當做整體編輯，但不能輸入個別的資料格。 按下`ESC`或使用滑鼠在儲存格的編輯器區域外按一下，進入命令模式。

   ![命令模式](./media/apache-spark-development-using-notebooks/synapse-command-mode2.png)

2. 編輯模式會以文字游標指示，提示您在編輯器區域中輸入。 當儲存格處於編輯模式時，您不能在資料格中輸入。 按下`Enter`或使用滑鼠按一下儲存格的編輯器區域，進入編輯模式。
   
   ![edit-mode](./media/apache-spark-development-using-notebooks/synapse-edit-mode2.png)

### <a name="shortcut-keys-under-command-mode"></a>命令模式下的快速鍵

使用下列按鍵快捷方式，您可以更輕鬆地在 Azure Synapse 筆記本中流覽和執行程式碼。

| 動作 |Synapse Studio 筆記本快捷方式  |
|--|--|
|執行目前的儲存格，然後選取下方 | Shift+Enter |
|執行目前的儲存格，並在下方插入 | Alt+Enter |
|選取上方的儲存格| 上移 |
|選取下方的資料格| 向下 |
|插入上方的儲存格| A |
|在下方插入資料格| B |
|擴充上方選取的儲存格| Shift + 向上鍵 |
|擴充下方選取的資料格| Shift + 向下鍵|
|向上移動儲存格| Ctrl + Alt + ↑ |
|將資料格向下移動| Ctrl + Alt + ↓ |
|刪除選取的資料格| D、D |
|切換至編輯模式| Enter |

### <a name="shortcut-keys-under-edit-mode"></a>編輯模式下的快速鍵

使用下列按鍵快捷方式，可讓您在編輯模式時，更輕鬆地在 Azure Synapse 筆記本中流覽和執行程式碼。

| 動作 |Synapse Studio 筆記本快捷方式  |
|--|--|
|將游標向上移動 | 上移 |
|向下移動游標|向下|
|復原|Ctrl + Z|
|取消復原|Ctrl + Y|
|註解/取消註解|Ctrl +/|
|刪除前的文字|Ctrl + 倒退鍵|
|刪除單字之後|Ctrl + Delete|
|移至儲存格開始|Ctrl + Home|
|移至資料格結尾 |Ctrl + End|
|向左移一個字|Ctrl + Left|
|右移一個單字|Ctrl + Right|
|全選|Ctrl + A|
|Indent| Ctrl +]|
|Dedent|Ctrl + [|
|切換至命令模式| Esc |

## <a name="next-steps"></a>後續步驟

- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
