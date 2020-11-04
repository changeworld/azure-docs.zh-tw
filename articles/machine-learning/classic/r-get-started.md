---
title: 搭配使用 R 與 Machine Learning Studio (傳統) -Azure
description: 您可以使用此 R 程式設計教學課程，開始使用 Azure Machine Learning Studio (R 中的傳統) 來建立預測解決方案。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: b7c442aaf6484e8e47bd6d00c91023fba43af75d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325013"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>開始使用 R 中的 Azure Machine Learning Studio (傳統) 

**適用於：** ![是](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)![否 ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


<!-- Stephen F Elston, Ph.D. -->
在本教學課程中，您將瞭解如何使用 Azure Machine Learning Studio (傳統) 來建立、測試和執行 R 程式碼。 最後，您將會有一個完整的預測解決方案。

> [!div class="checklist"]
> * 建立用來清除和轉換資料的程式碼。
> * 分析資料集中數個變數之間的關聯性。
> * 建立牛奶生產的季節性時間序列預測模型。


Machine Learning Studio (傳統) 包含許多功能強大的機器學習和資料操作模組。 使用 R 程式設計語言，此組合可讓您使用 R 的彈性和深度分析，輕鬆地部署 Machine Learning Studio (傳統) 。

預測是廣泛採用且有用的分析方法。 常見的用法範圍是預測季節性專案的銷售量，以及決定最佳的清查層級來預測 macroeconomic 變數。 進行預測時通常是搭配時間序列模型。 時間序列資料係指其當中的值具有時間索引的資料。 時間索引可以是一般的，例如每個月或每分鐘。 時間索引也可以是不規則的。 時間序列模型會根據時間序列資料。 R 程式設計語言包含時間序列資料的彈性架構和廣泛分析。

## <a name="get-the-data"></a>取得資料

在本教學課程中，您將使用加州牛奶的生產和定價資料，其中包含數個牛奶產品生產環境的每月資訊，以及牛奶 fat 的價格，也就是基準測試商品。

本文中使用的資料以及 R 腳本，可以從 [MachineLearningSamples-筆記本/studio 範例](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)中下載。 檔案中的資料 `cadairydata.csv` 原先是從威斯康辛 [牛奶市場網站](https://dairymarkets.com)的大學提供的資訊合成。

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>在 Machine Learning Studio (傳統) 中與 R 語言互動

本節將逐步引導您瞭解在 Machine Learning Studio (傳統) 環境中與 R 程式設計語言互動的一些基本概念。 R 語言提供一個功能強大的工具，可在 Machine Learning Studio (傳統) 環境內建立自訂的分析和資料操作模組。

我們將使用 RStudio 來大規模開發、測試及調試 R 程式碼。 然後，此程式碼會剪下並貼到 [ [執行 R 腳本][execute-r-script] ] 模組中，準備好在 Machine Learning Studio (傳統) 中執行。

### <a name="the-execute-r-script-module"></a>執行 R 指令碼模組

在 Machine Learning Studio (傳統) 中，R 腳本會在 [ [執行 r 腳本][execute-r-script] ] 模組中執行。 Machine Learning Studio (傳統) 的 [ [執行 R 腳本][execute-r-script] ] 模組範例如下所示。

 ![顯示 R 程式設計語言的螢幕擷取畫面： Machine Learning Studio 中選取的 [執行 R 腳本] 模組 (傳統) 。](./media/r-quickstart/fig1.png)

上圖顯示 Machine Learning Studio (傳統) 環境中使用「 [執行 R 腳本][execute-r-script] 」模組的一些主要部分：

* 實驗中的模組會顯示在中間的窗格。
* 右窗格的上半部包含可供您用來查看和編輯 R 腳本的視窗。
* 右窗格的下半部會顯示 [執行 R 腳本][execute-r-script]的某些屬性。 您可以藉由選取此窗格的適當區域來查看錯誤和輸出記錄。

我們將在本文的其餘部分將更詳細地討論 [Execute R 腳本][execute-r-script] 。

當您使用複雜的 R 函式時，建議您在 RStudio 中進行編輯、測試和調試。 如同任何軟體發展，請以累加方式擴充您的程式碼，並在小型、簡單的測試案例中進行測試。 然後，將您的函式剪下並貼到[執行 R 指令碼][execute-r-script]模組的 [R 指令碼] 視窗中。 這種方法可讓您充分利用 (IDE) 的 RStudio 整合式開發環境，以及 Machine Learning Studio (傳統) 的強大功能。

#### <a name="execute-r-code"></a>執行 R 程式碼

當您選取 [執行 **] 按鈕執行** 實驗時，[ [執行 R 腳本][execute-r-script]] 模組中的任何 R 程式碼都會執行。 當執行完成時，[ [執行 R 腳本][execute-r-script] ] 圖示上會出現一個核取記號。

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Machine Learning Studio (傳統) 的防禦式 R 編碼

如果您要使用 Machine Learning Studio (傳統) 開發 R 程式碼（例如 web 服務），您應該明確地規劃程式碼將如何處理非預期的資料輸入和例外狀況。 為了更清楚起見，我們尚未在大部分顯示的程式碼範例中，包含太多檢查或例外狀況處理的方式。 當我們繼續進行時，我們會使用 R 的例外狀況處理功能來提供您幾個函式範例。

如果您需要更完整的 R 例外狀況處理方式，請閱讀本書籍的適用章節，Wickham 著 [進一步閱讀](#appendixb)。

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Machine Learning Studio (傳統) 中的 Debug 和 test R

在 RStudio 中大規模測試及偵測 R 程式碼。 也有一些情況下，您必須在 [執行 r 腳本][execute-r-script] 本身中追蹤 R 程式碼問題。 此外，在 Machine Learning Studio (傳統) 中檢查結果是很好的作法。

從 R 程式碼執行和 Machine Learning Studio (傳統) 平臺的輸出，主要是在 output. log 中找到。 某些額外的資訊位於錯誤 .log。

如果您在執行 R 程式碼時 Machine Learning Studio (傳統) 中發生錯誤，您的第一個動作應該是查看錯誤 .log。 此檔案可能包含可協助您了解並更正錯誤的實用錯誤訊息。 若要查看 [錯誤]，請在包含錯誤的 [ [執行 R 腳本][execute-r-script]] 的 [屬性] 窗格上，選取 [ **view error log** ]。

例如，我們在 [ [執行 r 腳本][execute-r-script] ] 模組中執行了具有未定義變數 y 的下列 R 程式碼。

```r
x <- 1.0
z <- x + y
```

這段程式碼無法執行，這會導致錯誤狀況。 選取 [屬性] 窗格上的 [ **View error log** ] 會產生下列顯示。

  ![顯示錯誤訊息快顯視窗的螢幕擷取畫面。](./media/r-quickstart/fig2.png)

看來我們必須查看 output.log 來找出 R 錯誤訊息。 選取 [ [執行 R 腳本][execute-r-script] ] 模組，然後在右邊的 [屬性] 窗格中選取 [ **View output** ] （記錄）專案。 新的瀏覽器視窗隨即開啟，並出現下列錯誤訊息。

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

此錯誤訊息沒有任何出人意料的內容，並且清楚地指出問題所在。

若要檢查 R 中任何物件的值，您可以將這些值列印至 output.log 檔案中。 檢查物件值的規則基本上與在互動式 R 工作階段中相同。 例如，如果您在行上輸入變數名稱，則會將物件的值列印到輸出 .log 檔。

#### <a name="packages-in-machine-learning-studio-classic"></a>Machine Learning Studio 中的套件 (傳統) 

Machine Learning Studio (傳統) 隨附350以上預先安裝的 R 語言套件。 您可以使用[執行 R 指令碼][execute-r-script]模組中的下列程式碼，來擷取預先安裝的封裝清單。

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

如果您目前對此程式碼的最後一行還不了解，請往下閱讀。 在本文的其餘部分，我們將廣泛討論如何在 Machine Learning Studio (傳統) 環境中使用 R。

### <a name="introduction-to-rstudio"></a>RStudio 簡介

RStudio 是適用于 R 的廣泛使用 IDE。我們將使用 RStudio 來編輯、測試及偵測本指南中使用的一些 R 程式碼。 測試並準備好 R 程式碼之後，您可以從 RStudio 編輯器剪下並貼到 Machine Learning Studio (傳統) [執行 R 腳本][execute-r-script] 模組。

如果您的桌上型電腦上未安裝 R 程式設計語言，請立即這麼做。 您可以在 [完整的 R 封存網路 (CRAN) ](https://www.r-project.org/)取得開放原始碼 r 語言的免費下載。 下載適用于 Windows、Mac OS 和 Linux/UNIX。 選擇附近的鏡像，並遵循下載指示。 此外，CRAN 還包含許多有用的分析和資料操作套件。

如果您不熟悉 RStudio，您應該下載並安裝桌上出版。 您可以在 [RStudio](http://www.rstudio.com/products/RStudio/)中找到適用于 Windows、Mac OS 和 LINUX/UNIX 的 RStudio 下載。 請依照提供的指示，在您的桌上型電腦上安裝 RStudio。

您可以 [使用 RSTUDIO IDE](https://support.rstudio.com/hc/sections/200107586-Using-RStudio)來取得 RStudio 的教學課程簡介。

如需有關使用 RStudio 的詳細資訊，請參閱 [RStudio 檔的指南](#appendixa)。

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>將資料輸入執行 R 指令碼模組及從此模組輸出

在本節中，我們將討論如何將資料傳入和傳出「 [執行 R 腳本][execute-r-script] 」模組。 我們也將探討如何處理讀入和傳出「 [執行 R 腳本][execute-r-script] 」模組的各種資料類型。

本節的完整程式碼位於 [MachineLearningSamples-筆記本/studio 範例](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)中。

### <a name="load-and-check-data"></a>載入和檢查資料

#### <a name="load-the-dataset"></a><a id="loading"></a>載入資料集

首先，我們會將 **csdairydata.csv** 檔案載入至 Machine Learning Studio (傳統) 。

1. 啟動您的 Machine Learning Studio (傳統) 環境。
1. 選取畫面左下角的 [ **+ 新增** ]，然後選取 [ **資料集** ]。
1. 選取 [ **從本機** 檔案]，然後選取 **[流覽]** 以選取檔案。
1. 請確定您選取的 **一般 csv 檔案標頭 ( .csv)** 做為資料集的類型。
1. 選取核取記號。
1. 上傳資料集之後，當您選取 [ **資料集** ] 索引標籤時，您應該會看到新的資料集。

#### <a name="create-an-experiment"></a>建立實驗

既然我們已在 Machine Learning Studio (傳統) 中有一些資料，我們需要建立實驗來進行分析。  

1. 選取畫面左下角的 [ **+ 新增** ]，然後選取 [ **實驗**  >  **空白實驗** ]。
1. 在頁面頂端選取和修改標題 **上所建立的實驗** ，以命名您的實驗。 例如，將它變更為「 **CA 牛奶分析** 」。
1. 在 [實驗] 頁面的左側，選取 [ **儲存的資料集**  >  **我的資料集** ]。 您應該會看到先前上傳的 **cadairydata.csv** 檔案。
1. 將 **csdairydata.csv 資料集** 拖曳到實驗上。
1. 在左窗格頂端的 [ **搜尋實驗專案** ] 方塊中，輸入 [ [執行 R 腳本][execute-r-script]]。 模組會出現在搜尋清單中。
1. 將 [ [執行 R 腳本][execute-r-script] ] 模組拖曳到您的託盤上。
1. 將 **csdairydata.csv 資料集** 的輸出連接到 [Execute R 腳本][execute-r-script]的最左邊輸入 ( **Dataset1** ) 。
1. 選取 [儲存]  。

此時，您的實驗應該會看起來像此範例。

![顯示 CA 牛奶分析實驗和資料集和執行 R 腳本模組的圖表。](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>檢查資料

讓我們來看看我們已載入實驗中的資料。 在實驗中，選取 **cadairydata.csv 資料集** 的輸出，然後選取 [ **視覺化** ]。 您應該會看到類似下面的摘要。

![顯示 cadairydata.csv 資料集摘要的螢幕擷取畫面。](./media/r-quickstart/fig4.png)

這個視圖會顯示許多有用的資訊。 我們可以看到資料集的前幾個資料列。 如果選取資料行，[ **統計資料]** 區段會顯示有關資料行的詳細資訊。 例如，[ **功能類型** ] 資料列會顯示 Machine Learning Studio (傳統) 指派給資料行的資料類型。 在開始進行任何嚴重的工作之前，請先檢查此視圖。

### <a name="first-r-script"></a>第一個 R 指令碼

讓我們建立一個簡單的第一個 R 腳本，以在 Machine Learning Studio (傳統) 中進行實驗。 我們已在 RStudio 中建立並測試下列腳本。

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

現在，我們需要將此腳本傳送給 Machine Learning Studio (傳統) 。 您可以剪下並貼上，但在此情況下，請透過 zip 檔案傳輸 R 腳本。

### <a name="data-input-to-the-execute-r-script-module"></a>執行 R 指令碼模組的資料輸入

讓我們看看[執行 R 指令碼][execute-r-script]模組的輸入。 在此範例中，我們會將加州牛奶資料讀入「 [執行 R 腳本][execute-r-script] 」模組。

[執行 R 指令碼][execute-r-script]模組有三個可能的輸入。 您可以根據您的應用程式，使用任何一個或所有輸入。 您也可以使用不接受任何輸入的 R 腳本。

讓我們從左到右看看這當中的每一個輸入。 您可以將游標放到輸入上方並閱讀工具提示，來查看每個輸入的名稱。

#### <a name="script-bundle"></a>腳本套件組合

腳本套件組合輸入可讓您將 zip 檔案的內容傳遞至「 [執行 R 腳本][execute-r-script] 」模組。 您可以使用下列其中一個命令將 Zip 檔案的內容讀入到 R 程式碼中。

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (傳統) 會將 zip 檔案中的檔案視為在 src/目錄中，因此您需要在檔案名前面加上此目錄名稱。 例如，如果 zip 檔案包含檔案， `yourfile.R` 而且 `yourData.rdata` 在 zip 檔案的根目錄中，則在 `src/yourfile.R` `src/yourData.rdata` 使用和時，您會將這些檔案定址 `source` `load` 。

我們已討論載入 [資料集的](#loading)載入資料集。 在您建立並測試上一節所示的 R 腳本之後，請執行下列步驟。

1. 將 R 指令碼儲存成 .R 檔案。 我們稱之為腳本檔 **simpleplot。R** 。 以下是檔案中的功能：

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. 建立 zip 檔案，並將您的腳本複製到這個 zip 檔案中。 在 Windows 中，您可以在檔案上按一下滑鼠右鍵，然後選取 [ **傳送到**  >  **壓縮的資料夾** ]。 此動作會建立新的 zip 檔案，其中包含 **simpleplot。R** 檔案。

1. 將您的檔案新增至 Machine Learning Studio (傳統) 中的 **資料集** ，並將類型指定為 **zip** 。 您現在應該會在您的資料集內看到的此 Zip 檔案。

1. 將 zip 檔案從 **資料集** 拖曳到 **ML Studio (傳統) 畫布** 上。

1. 將 [Zip 資料] 圖示的輸出連接到[執行 R 指令碼][execute-r-script]模組的 [指令碼組合] 輸入。

1. 在 [ `source()` [執行 R 腳本][execute-r-script] ] 模組的程式碼視窗中，輸入具有您 ZIP 檔案名的函式。 在此案例中，我們輸入了 `source("src/simpleplot.R")` 。

1. 選取 [儲存]  。

完成這些步驟之後， [執行 r 腳本][execute-r-script] 模組會在執行實驗時，執行 zip 檔案中的 r 腳本。 此時，您的實驗應該會看起來像此範例。

![此圖顯示使用壓縮 R 腳本的實驗。](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>資料集1

您可以使用 [資料集1] 輸入將矩形資料表傳遞給您的 R 程式碼。 在我們的簡單腳本中，此函式會 `maml.mapInputPort(1)` 從埠1讀取資料。 此資料會接著被指派給您程式碼中的資料框架變數名稱。 在我們的簡單腳本中，第一行程式碼會執行指派。

```r
cadairydata <- maml.mapInputPort(1)
```

選取 [執行] 按鈕來執行 **您的實驗** 。 當執行完成時，請選取 [ [執行 R 腳本][execute-r-script] ] 模組，然後選取 [屬性] 窗格上的 [ **查看輸出記錄** 檔]。 您的瀏覽器中應該會出現一個新頁面，當中顯示 output.log 檔案的內容。 當您向下滾動時，您應該會看到類似下列的輸出。

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

頁面越下層，資料行的詳細資訊就會類似下列輸出。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

這些結果大致上如預期，資料框架中有 228 個觀察值和 9 個資料行。 我們可以看到資料行名稱、R 資料類型，以及每個資料行的範例。

> [!NOTE]
> 從[執行 R 指令碼][execute-r-script]模組的 [R 裝置] 輸出可以便利地取得這個相同的列印輸出。 在下一節中，我們將討論「 [執行 R 腳本][execute-r-script] 」模組的輸出。  

#### <a name="dataset2"></a>資料集2

[資料集2] 輸入的行為與 [資料集1] 的行為相同。 您可以使用此輸入，將第二個矩形資料表傳遞至您的 R 程式碼。 含有引數 2 的函式 `maml.mapInputPort(2)`可用來傳遞此資料。  

### <a name="execute-r-script-outputs"></a>執行 R 指令碼輸出

#### <a name="output-a-dataframe"></a>輸出資料框架

您可以使用 `maml.mapOutputPort()` 函式，透過 [結果資料集1] 連接埠將 R 資料框架的內容輸出成矩形資料表。 在我們的簡單 R 腳本中，此動作會由下列程式列執行。

```r
maml.mapOutputPort('cadairydata')
```

執行實驗之後，請選取結果 Dataset1 輸出埠，然後選取 [ **視覺化** ]。 您應該會看到類似此範例的內容。

![顯示加州牛奶資料輸出視覺效果的螢幕擷取畫面。](./media/r-quickstart/fig7.png)

此輸出看起來與輸入相同，完全如我們的預期。

### <a name="r-device-output"></a>R 裝置輸出

[執行 R 指令碼][execute-r-script]模組的 [裝置] 輸出包含訊息和圖形輸出。 來自 R 的標準輸出和標準錯誤訊息都會傳送到 [R 裝置] 輸出連接埠。

若要查看 R 裝置輸出，請選取埠，然後選取 [ **視覺化** ]。 我們會在這裡看到來自 R 腳本的標準輸出和標準錯誤。

![顯示來自 R 裝置埠的標準輸出和標準錯誤的螢幕擷取畫面。](./media/r-quickstart/fig8.png)

向下滾動，我們會看到 R 腳本的圖形輸出。

![顯示來自 R 裝置埠之圖形輸出的螢幕擷取畫面。](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>資料篩選和轉換

在本節中，我們將在加州牛奶資料上執行一些基本的資料篩選和轉換作業。 在本節結尾，我們將會有適合用來建立分析模型的格式資料。

更具體來說，在本節中，我們將會執行數個常見的資料清除和轉換工作：類型轉換、資料框架篩選、加入新的計算資料行，以及值轉換。 此背景應該可以協助您處理在真實世界問題中遇到的許多變化。

您可以在 [MachineLearningSamples-筆記本/studio 範例](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)中取得本節的完整 R 程式碼。

### <a name="type-transformations"></a>類型轉換

既然我們可以將加州乳製品資料讀入到[執行 R 指令碼][execute-r-script]模組的 R 程式碼中，我們需要確保資料行中的資料具有預期的類型和格式。

R 是動態指定類型的語言，這表示會視需要強制將資料類型從一種類型轉換成另一種類型。 R 中的不可部分完成資料類型包括數值、邏輯和字元。 因素類型可用來簡潔地儲存分類資料。 如需資料類型的詳細資訊，請參閱 [進一步閱讀](#appendixb)的參考。

從外部來源將表格式資料讀入 R 時，最好先檢查資料行中的結果類型。 您可能會想要有一個字元類型的資料行，但在許多情況下，資料行將會顯示為因素，反之亦然。 在其他情況下，您認為應該是數值的資料行是以字元資料來表示，例如 "1.23"，而不是1.23 作為浮點數。

幸運的是，只要可以進行對應，就可以輕鬆地將一個類型轉換成另一個類型。 例如，您無法將 "內華達拉斯維加斯市" 轉換成數值，但您可以將它轉換成 (類別變數) 的因數。 另舉一個範例，您可以將數位1轉換成字元 "1" 或一個因素。

任何這些轉換的語法都很簡單： `as.datatype()`。 這些類型轉換函式包含下列函數：

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

查看我們在上一節中輸入之資料行的資料類型，所有資料行的類型都是數值。 例外狀況是標示為 "Month" 的資料行，這是字元類型。 讓我們將此型別轉換成一個因素，並測試結果。

我們已刪除建立散佈圖矩陣的行，並新增了可將月份資料行轉換成因數的線條。 在此實驗中，我們會將 R 程式碼剪下並貼到 [ [執行 R 腳本][execute-r-script] ] 模組的程式碼視窗中。 您也可以更新 zip 檔案，並將它上傳至 Machine Learning Studio (傳統) ，但此選項需要數個步驟。

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

讓我們執行這個程式碼並查看 R 指令碼的輸出記錄檔。 記錄檔中的相關資料如下所示。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

月份的型別現在應該會是有 **/14 層級的係數** 。 此類型是問題，因為一年只有12個月。 您也可以查看結果資料集埠的 **視覺化** 型別是否為 **類別** 。

問題在於 Month 資料行未有系統地編碼。 在某些情況下，一個月稱為四月，其他則縮寫為 Apr。我們可以藉由將字串修剪成三個字元來解決這個問題。 這一行程式碼現在看起來像下面的範例。

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

重新執行實驗，並查看輸出記錄檔。 預期的結果會顯示在這裡。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


我們的因素變數現在具有所需的 12 個層級。

### <a name="basic-dataframe-filtering"></a>基本資料框架篩選

R 資料框架支援強大的篩選功能。 藉由在資料列或資料行使用邏輯篩選，可將資料集再細分成子集。 在許多情況下，將會需要複雜的篩選條件。 如需篩選資料框架的詳盡範例，請參閱 [進一步閱讀](#appendixb)的參考。

我們應該對我們的資料集進行一項篩選。 如果您查看 cadariydata 資料框架中的資料行，就會看到兩個不必要的資料行。 第一個資料行包含不太實用的資料列編號。 第二個資料行 Year.Month 包含重複的資訊。 我們可以使用下列 R 程式碼輕鬆地排除這些資料行。

> [!NOTE]
> 從現在開始，我們將在 [ [執行 R 腳本][execute-r-script] ] 模組中顯示我們所新增的其他程式碼。 我們會在函式 *之前* 新增每一行 `str()` 。 我們會使用此函數來確認 Machine Learning Studio (傳統) 的結果。

我們會將下列程式程式碼新增至 [ [執行 r 腳本][execute-r-script] ] 模組中的 r 程式碼。

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

在您的實驗中執行此程式碼，並檢查輸出記錄檔的結果。 這些結果會顯示在這裡。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

我們現在會取得預期的結果。

### <a name="add-a-new-column"></a>加入新的資料行

若要建立時間序列模型，在時間序列開始時，有一個包含月份的資料行會很方便。 我們會建立新的資料行 Month. Count。

為了協助組織程式碼，我們將建立我們的第一個簡單函數 `num.month()` 。 接著，我們會套用此函數，在資料框架中建立新的資料行。 新的程式碼如下所示：

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

現在執行更新的實驗，並使用輸出記錄檔來查看結果。 這些結果會顯示在這裡。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


看來一切運作正常。 我們的資料框架中有了包含預期值的新資料行。

### <a name="value-transformations"></a>值轉換

在本節中，我們將對資料框架的某些資料行中的值執行一些簡單的轉換。 R 語言幾乎支援任何一種值轉換。 如需更多範例，請參閱 [進一步閱讀](#appendixb)的參考。

如果您查看資料框架摘要中的值，您應該會在這裡看到奇怪的內容。 加州生產的冰淇淋比牛奶多？ 當然沒有。 問題在於單位不同。 價格以美元為單位，而牛奶的單位為1000000美元，霜淇淋的單位為1000美國加侖，小屋乳酪則以1000美元的單位表示。 假設霜淇淋針對每加侖6.5 磅進行了權衡，我們可以輕鬆地將這些值轉換成相等單位的1000磅。

針對我們的預測模型，我們使用乘法模型來調整此資料的趨勢和季節性。 記錄轉換可讓我們使用線性模型，以簡化此程式。 我們可以在套用乘數的相同函式中套用對數轉換。

在下列程式碼中，我們會定義新的函式， `log.transform()` 並將它套用至包含數值的資料列。 R `Map()` 函式可用來將 `log.transform()` 函式套用至資料框架中的所選資料行。 函式 `Map()` 類似于 `apply()` ，但可允許函式有多個引數清單。 請注意，乘數清單會提供 `log.transform()` 函式的第二個引數。 `na.omit()`函數會用來做為清除的一部分，以確保在資料框架中沒有遺漏或未定義的值。

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

函數中有相當多的情況 `log.transform()` 。 此程式碼中大部分會檢查引數的潛在問題，或是處理仍可能在計算期間發生的例外狀況。 這段程式碼只有幾行程式碼會進行計算。

防禦程式設計的目標是防止單一函式失敗，而導致無法繼續處理。 長時間執行的分析突然失敗可能會讓使用者感到挫折。 為了避免這種情況，必須選擇預設的傳回值，以將損害限制在下游處理。 系統也會產生訊息來警示使用者發生錯誤。

如果您不是在 R 中用來防禦程式設計，則這段程式碼可能會很困難。 讓我們逐步解說主要步驟：

1. 定義包含四個訊息的向量。 這些訊息用來傳達一些可能在此程式碼發生的錯誤和例外狀況的相關資訊。
1. 我們會針對每個案例傳回 NA 值。 有許多其他可能會有較少副作用的可能性。 例如，我們可以傳回零或原始輸入向量的向量。
1. 對函式的引數執行檢查。 在每個案例中，如果偵測到錯誤，便會傳回預設值，並且由 `warning()` 函式產生訊息。 我們使用 `warning()` 的是，而不是 `stop()` 因為後者將終止執行，這就是我們想要避免的結果。 這段程式碼是以程式性的樣式撰寫的，因為在此情況下，功能性方法看似複雜且隱匿。
1. 記錄計算會包裝在中， `tryCatch()` 因此例外狀況不會造成突然中斷的處理。 如果沒有 `tryCatch()` ，R 函數引發的大部分錯誤都會產生停止信號，而這就是如此。

請在您的實驗中執行此 R 程式碼，然後看看 output.log 檔案中的列印輸出。 您現在會在記錄檔中看到四個數據行的轉換值，如下所示。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

我們會看到值已經轉換。 牛奶生產現在大幅超過所有其他的牛奶產品生產，請重新叫用我們現在查看記錄規模。

此時，我們的資料會清除，而且我們已準備好進行一些模型化。 如果您查看 [ [執行 R 腳本][execute-r-script] ] 模組的結果資料集輸出的視覺化摘要，您將會看到 [月份] 資料行是具有12個唯一值的類別，就像我們想要的一樣。

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>時間序列物件和相互關聯分析

在本節中，我們將探索一些基本的 R 時間序列物件，並分析部分變數之間的關聯性。 我們的目標是輸出資料框架，其中包含數個延遲的成對相互關聯資訊。

本節的完整 R 程式碼位於 [MachineLearningSamples-筆記本/studio 範例](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)中。

### <a name="time-series-objects-in-r"></a>R 中的時間序列物件

如已經提過的，時間序列是一系列依時間編制索引的資料值。 R 時間序列物件可用來建立和管理時間索引。 使用時間序列物件有數個優點。 時間序列物件可讓您不必理會管理封裝在物件中之時間序列索引值的許多細節。 此外，時間序列物件也可讓您使用多個時間序列方法來繪製、列印、模型化等等。

POSIXct 時間序列類別是常用且相對簡單的類別。 此時間序列類別是從 1970 年 1 月 1 日開始計量時間。 在此範例中，我們將使用 POSIXct 時間序列物件。 其他廣泛使用的 R 時間序列物件類別包括 zoo 和 xts (可延伸的時間序列) 。

### <a name="time-series-object-example"></a>時間序列物件範例

讓我們開始進行我們的範例。 將新的 [ [執行 R 腳本][execute-r-script] ] 模組拖曳到您的實驗中。 將現有[執行 R 指令碼][execute-r-script]模組的 [結果資料集 1] 輸出連接埠連接到新的[執行 R 指令碼][execute-r-script]模組的 [資料集 1] 輸入連接埠。

如同我們在第一個範例中所做的一樣，我們將逐步解說範例。 在某些時間點，我們只會顯示每個步驟的額外額外行 R 程式碼。

#### <a name="read-the-dataframe"></a>閱讀資料框架

第一步是先讀入一個資料框架，然後確定得到預期的結果。 下列程式碼應該能執行此作業。

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

現在，請執行實驗。 新的 [執行 R 腳本] 圖形的記錄看起來應該像此範例。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

此資料的類型和格式皆如預期。 現在 Month 資料行的類型是因素，並且具有預期的層級數目。

#### <a name="create-a-time-series-object"></a>建立時間序列物件

我們需要將時間序列物件新增到我們的資料框架中。 將目前的程式碼取代為下列程式碼，此程式碼會加入 POSIXct 類別的新資料行。

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

現在，請檢查記錄檔。 它應該看起來像此範例。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

我們可以從摘要看出，新資料行的類別事實上是 POSIXct。

### <a name="explore-and-transform-the-data"></a>探索和轉換資料

讓我們探索此資料集內的一些變數。 散佈圖矩陣是產生快速外觀的好方法。 我們會將先前 R 程式碼中的函式取代為 `str()` 下列程式程式碼。

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

執行這段程式碼，看看會發生什麼事。 在 R 裝置埠上產生的繪圖看起來應該像此範例。

![顯示所選變數散佈圖矩陣的螢幕擷取畫面。](./media/r-quickstart/fig17.png)

這些變數之間的關聯性中有一些看似奇怪的結構。 這可能是因為資料中的趨勢，以及我們尚未將變數標準化的事實。

### <a name="correlation-analysis"></a>相互關聯分析

若要執行相互關聯分析，我們需要對變數進行還原趨勢和標準化。 我們可以就使用既可將變數置中又可縮放變數的 R `scale()` 函式。 此函式可能也執行得更快。 但是，讓我們看看 R 中的防禦程式設計範例。

`ts.detrend()`下列函數會執行這兩項作業。 下列兩行程式碼會將資料去除趨勢，然後將值標準化。

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

函數中有相當多的情況 `ts.detrend()` 。 此程式碼中大部分會檢查引數的潛在問題，或是處理仍可能在計算期間發生的例外狀況。 此程式碼中實際上只有幾行在進行計算。

我們已在值轉換中討論過防禦程式設計的範例。 兩個計算區塊皆包裝在 `tryCatch()` 中。 針對某些錯誤，傳回原始輸入向量是合理的。 在其他情況下，我們會傳回零的向量。

請注意，用於去除趨勢的線性迴歸是時間序列迴歸。 預測工具變數是時間序列物件。

`ts.detrend()`定義之後，我們會將它套用至資料框架中感興趣的變數。 我們必須使用將建立的結果清單強制轉換 `lapply()` 成資料框架中的資料 `as.data.frame()` 。 因為的防禦性層面 `ts.detrend()` ，所以無法處理其中一個變數，也不會妨礙其他變數的正確處理。

最後一行程式碼會建立成對的散佈圖。 執行 R 程式碼之後，散佈圖的結果會顯示在這裡。

![顯示已取消趨勢和標準化時間序列之成對散佈圖的螢幕擷取畫面。](./media/r-quickstart/fig18.png)

您可以比較這些結果與上述範例所示的結果。 在已移除趨勢並將變數標準化的情況下，我們會看到這些變數之間的關係少了許多結構。

以 R ccf 物件方式計算相互關聯的程式碼如下所示。

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

執行此程式碼會產生此處所示的記錄檔。

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

每個延遲都有一個相互關聯值。 這些相互關聯值都沒有大到足以具有重要性。 我們可以推斷出我們可以獨立建立每個變數的模型。

### <a name="output-a-dataframe"></a>輸出資料框架
我們已將成對相互關聯計算為 R ccf 物件的清單。 這樣會有一點問題，因為 [結果資料集] 輸出連接埠確實需要資料框架。 此外，ccf 物件本身是清單，而我們只想要此清單的第一個元素中的值，也就是各種延隔的關聯性。

下列程式碼會從 ccf 物件 (本身是清單) 的清單中擷取延隔時間值。

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

第一行程式碼有點棘手，有些說明可能有助於您瞭解它。 從內部工作，我們有：

1. With 引數 **1** 的 **[[** 運算子] 會從 ccf 物件清單的第一個元素，選取延遲的關聯向量。
1. `do.call()` 函式會在 `lapply()` 所傳回之清單的項目上套用 `rbind()` 函式。
1. `data.frame()` 函式會強制將 `do.call()` 產生的結果轉換成資料框架。

請注意，資料列名稱會在資料框架的資料行中。 這樣做會在從 [Execute R 腳本][execute-r-script]輸出時保留資料列名稱。

當我們選取 [ **視覺化** ] 以查看結果資料集埠的輸出時，執行程式碼會產生如下所示的輸出。 資料列名稱如預期般在第一個資料行中。

![顯示相互關聯分析結果輸出的螢幕擷取畫面。](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>時間序列範例：季節性預測

我們的資料現在是適用于分析的表單，而且我們判斷變數之間沒有顯著的關聯性。 讓我們繼續來建立時間序列預測模型。 我們將使用此模型來預測適用于12個月2013的加州牛奶生產。

我們的預測模型將會有兩個元件，亦即趨勢元件和季節性元件。 這兩個元件的乘積即是完整預測。 這種模型稱為乘法模型。 替代模型是加法模型。 我們已將記錄轉換套用到感興趣的變數，使此分析方便追蹤。

本節的完整 R 程式碼位於 [MachineLearningSamples-筆記本/studio 範例](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)中。

### <a name="create-the-dataframe-for-analysis"></a>建立資料框架進行分析

首先，將新的 [ [執行 R 腳本][execute-r-script] ] 模組新增至您的實驗。 將現有[執行 R 指令碼][execute-r-script]模組的 [結果資料集] 輸出連接到新模組的 [資料集1] 輸入。 結果看起來應該像此範例。

![顯示已加入新的 [執行 R 腳本] 模組之實驗的圖表。](./media/r-quickstart/fig21.png)

與我們剛剛完成的相互關聯分析相同，我們需要新增一個含有 POSIXct 時間序列物件的資料行。 下列程式碼會加入資料行。

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

執行此程式碼，並查看記錄檔。 結果看起來應該像此範例。

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

有了這個結果，我們就可以開始進行分析。

### <a name="create-a-training-dataset"></a>建立訓練資料集

在資料框架的結構中，我們需要建立訓練資料集。 此資料將包含所有觀察值，但 2013 年最後一個的 12 除外，這是我們的測試資料集。 下列程式碼會將資料框架細分成子集，並繪製乳製品產量和價格變數的圖。 接著，我們會建立四個生產和價格變數的繪圖。 匿名函式可用來定義一些用於繪圖的引數，然後藉由 `Map()`逐一查看其他兩個引數的清單。 如果您想要讓 for 迴圈可在這裡正常運作，您就是正確的。 但是，由於 R 是一種功能性語言，因此我們正在探討功能性方法。

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

執行程式碼會從此處顯示的 R 裝置輸出產生一連串的時間序列繪圖。 時間軸是以日期為單位，這是時間序列繪圖方法的優點。

![加州牛奶產品和價格資料的第一次時間序列圖。](./media/r-quickstart/unnamed-chunk-161.png)

![加州牛奶產品和價格資料的第二個時間序列圖。](./media/r-quickstart/unnamed-chunk-162.png)

![加州牛奶產品和價格資料的第三個時間序列圖。](./media/r-quickstart/unnamed-chunk-163.png).

![加州牛奶生產和價格資料的第四個時間序列圖](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>趨勢模型

既然我們已建立時間序列物件並查看資料，讓我們開始為加州牛奶的生產資料建立趨勢模型。 我們可以使用時間序列回歸。 您可以從繪圖中清楚地找出需要更多斜率和攔截，以精確地在定型資料中建立觀察趨勢的模型。

由於資料的規模很小，我們會在 RStudio 中建立趨勢的模型，然後將產生的模型剪下並貼到 Machine Learning Studio (傳統) 中。 RStudio 針對這種互動式分析提供了互動式環境。

在第一次嘗試時，我們會嘗試以最多三個乘冪的多項式回歸。 過度調整這類模型有一個真正的風險。 最好避免高序位的詞彙。 此函 `I()` 式會抑制內容的解讀， (以) 的方式解讀內容，並可讓您在回歸方程式中撰寫常被解讀的函式。

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

此函數會產生下列輸出。

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 **_
## Time              1.63e-09   1.72e-10    9.47   <2e-16 _*_
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 _  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

從 P 值 (`Pr(>|t|)`) 在此輸出中，我們可以看到平方詞彙可能不重要。 我們將使用函式 `update()` ，藉由卸載平方詞彙來修改此模型。

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

此函數會產生下列輸出。

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 **_
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

此輸出看起來更好。 所有的項都變得有意義。 2e-16 值是預設值，不應該太嚴重。  

讓我們繪製顯示趨勢曲線的加州乳製品產量資料時間序列圖，來做為例行性測試。 我們已將下列程式碼新增至 Machine Learning Studio (傳統) [執行 R 腳本][execute-r-script] 模型 (未 RStudio) 建立模型並進行繪圖。 結果會顯示在下列範例中。

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![顯示趨勢模型的加州牛奶產量資料。](./media/r-quickstart/unnamed-chunk-18.png)

看起來趨勢模型與資料非常相符。 此外，似乎沒有過度調整的證據，例如模型曲線中的奇數擺動。

### <a name="seasonal-model"></a>季節性模型

有了趨勢模型之後，我們還需要繼續進行來納入季節性效果。 我們會使用一年中的月份作為線性模型中的虛擬變數，以捕獲每月的效果。 當您在模型中引入因素變數時，就不能計算攔截。 如果您沒有這麼做，公式會過度指定，且 R 會卸載其中一個所需的因素，但會保留攔截詞彙。

因為我們有令人滿意的趨勢模型，所以我們可以使用函式 `update()` 將新的詞彙新增至現有的模型。 更新公式中的-1 會卸除截距項。 目前先繼續在 RStudio 中進行：

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

此函數會產生下列輸出。

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 **_
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 _*_
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 _*_
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 _*_
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 _*_
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 _*_
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 _*_
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 _*_
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 _*_
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 _*_
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 _*_
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

我們會看到模型不再具有截距項，並且擁有 12 個重要的月份因素。 這正是我們想要看到的結果。

讓我們繪製另一張加州乳製品產量資料的時間序列圖，看看季節性模型運作得如何。 我們已在 Machine Learning Studio (傳統) [執行 R 腳本][execute-r-script] 中新增下列程式碼，以建立模型並進行繪圖。

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

在 Machine Learning Studio (傳統) 中執行此程式碼會產生如下所示的圖。

![模型包含季節性效果的加州牛奶產量。](./media/r-quickstart/unnamed-chunk-20.png)

此範例中顯示的資料符合此範例的建議。 趨勢和季節性效果 (每月變化) 看起來都相當合理。

接下來，對模型進行另一項檢查，讓我們看看殘差。 下列程式碼會從我們的兩個模型計算預測值、計算季節性模型的殘差，然後繪製這些殘差的圖以供訓練資料使用。

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

剩餘的圖如下所示。

![定型資料之季節性模型的殘差。](./media/r-quickstart/unnamed-chunk-21.png)

這些殘差看起來相當合理。 除了2008-2009 經濟衰退的效果之外，不會有任何特定的結構，因為我們的模型不會特別考慮。

此範例中顯示的圖適合用來偵測殘差中任何時間相依的模式。 計算和繪製所用殘差的明確方法，會將殘差時間放在繪圖上。 如果 `milk.lm$residuals` 繪圖已繪製，則繪圖不會以時間順序排列。

您也可以使用 `plot.lm()` 來產生一系列診斷圖：

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

此程式碼會產生一系列的診斷圖，如下列範例所示。

![季節性模型的第一個診斷圖。](./media/r-quickstart/unnamed-chunk-221.png)

![季節性模型的第二個診斷圖。](./media/r-quickstart/unnamed-chunk-222.png)

![季節性模型的第三個診斷圖。](./media/r-quickstart/unnamed-chunk-223.png)

![季節性模型的第四個診斷圖。](./media/r-quickstart/unnamed-chunk-224.png)

這些圖中有指出一些高影響力的點，但是不需要太過關注。 此外，我們可以從一般 Q Q 繪圖中看出殘差接近正常分佈，這是線性模型的重要假設。

### <a name="forecasting-and-model-evaluation"></a>預測和模型評估

另外還有一件事要完成我們的範例。 我們需要計算預測，並對照實際資料來測量誤差。 我們的預測將會針對 2013 年的 12 個月。 我們可以將這項預測的錯誤量值計算到不屬於訓練資料集的實際資料。 此外，我們可以將 18 年訓練資料的相關表現與 12 個月的測試資料做比較。

有一些衡量標準可用來衡量時間序列模型的表現。 在我們的案例中，我們將使用 mean 平方根 (RMS) 錯誤。 下列函式會計算兩個數列間的 RMS 誤差。

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

如同 `log.transform()` 我們在「值轉換」一節中討論的函式，此函式中有很多錯誤檢查和例外狀況修復程式碼。 所採用的原則相同。 工作會由包裝在 `tryCatch()`中的兩個地方完成。 首先，系統會 exponentiated 時間序列，因為我們使用的是這些值的記錄。 其次，則是會計算實際的 RMS 誤差。

提供可測量 RMS 錯誤的函式，讓我們建立和輸出包含 RMS 錯誤的資料框架。 我們將只包含趨勢模型的詞彙，以及包含季節性因數的完整模型。 下列程式碼會使用我們所建立的兩個線性模型來執行工作。

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

執行此程式碼會在結果資料集輸出埠上產生如下所示的輸出。

![顯示模型的 RMS 錯誤比較的螢幕擷取畫面。](./media/r-quickstart/fig26.png)

我們可以從這些結果看出，將季節性因素新增到模型中可大幅降低 RMS 誤差。 不出所料，訓練資料的 RMS 誤差比預測的 RMS 誤差小一些。

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>RStudio 檔指南

RStudio 已妥善記載。 以下是 RStudio 檔中重要章節的一些連結，可讓您開始使用。

* **建立專案** ：您可以使用 RStudio，將 R 程式碼組織和管理到專案中。 如需詳細資訊，請參閱 [使用專案](https://support.rstudio.com/hc/articles/200526207-Using-Projects)。 遵循這些指示，並為本文中的 R 程式碼範例建立專案。
* **編輯和執行 r 程式碼** ： RStudio 提供用於編輯和執行 r 程式碼的整合式環境。 如需詳細資訊，請參閱 [編輯和執行程式碼](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code)。
* **Debug** ： RStudio 包含強大的調試功能。 如需這些功能的詳細資訊，請參閱 [使用 RStudio 進行的調試](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio)程式。 如需中斷點疑難排解功能的相關資訊，請參閱 [中斷點疑難排解](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)。

## <a name="further-reading"></a><a id="appendixb"></a>進階閱讀

此 R 程式設計教學課程涵蓋了使用 R 語言搭配 Machine Learning Studio (傳統) 所需的基本概念。 如果您不熟悉 R，CRAN 上有兩個簡介：

* Emmanuel Paradis 的[R For 初學者](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf)是不錯的開端。
* [R 的簡介](https://cran.r-project.org/doc/manuals/R-intro.html) （依 W. N）。 Venables et。深入探討。

R 有許多可協助您開始使用的書籍：

* **R 程式設計的藝術： Norman Matloff 的統計軟體設計導覽** 是在 r 中進行程式設計的絕佳簡介。
* Paul Teetor 的 **R 操作手冊** 提供使用 r 的問題和解決方案方法。
* Robert Kabacoff 的 **R In Action** 是另一個有用的簡介書。 隨附的 [快速 R 網站](https://www.statmethods.net/) 是有用的資源。
* 多人 **Inferno 的 R** 是一項令人驚訝的幽默書籍，它會處理在 R 中進行程式設計時可能遇到的一些棘手且困難的主題。這本書可免費在 [R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/)取得。
* **Advanced r** By hadley 所 wickham 著可讓您深入瞭解 R 中的 advanced 主題。您可以從 [Advanced R](http://adv-r.had.co.nz/)免費取得這本書的線上版本。
* 使用 R by Paul Cowpertwait 和 Andrew Metcalfe 的 **簡介時間序列** ，提供使用 r 進行時間序列分析的簡介。 許多理論文本皆有提供 R 範例。

以下是一些絕佳的網際網路資源：

* [CRAN 工作視圖：時間序列分析](https://cran.r-project.org/web/views/TimeSeries.html) 具有 R 時間序列套件的目錄。 如需特定時間序列物件套件的詳細資訊，請參閱該封裝的檔。
* [R 簡介](https://www.datacamp.com/courses/introduction-to-r) 是 DataCamp 的免費互動式課程，可讓您在瀏覽器中使用影片課程和程式碼撰寫練習來輕鬆地教授 r。 最新 R 技巧和封裝均有互動式教學課程。
* [瞭解 R 程式設計，這是 DataMentor 的最終指南](https://www.datamentor.io/r-programming/) 。
* [R 編碼員](https://r-coder.com/) 有詳細的 r 教學課程，以及適用于初學者的免費 r 課程。
* Clarkson 大學的王黑色[R 教學](https://www.cyclismo.org/tutorial/R/)課程是快速教學課程。
* 用[來改善資料技能的熱門 r 語言資源](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html)會列出60以上的 r 資源。

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script