---
title: 執行 R 腳本：模組引用
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用執行 R 腳本模組來運行 R 代碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: f038293b48956ac89314e426df3f5dc491954df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064211"
---
# <a name="execute-r-script"></a>執行 R 指令碼

本文介紹如何使用執行 R**腳本**模組在 Azure 機器學習設計器（預覽）管道中運行 R 代碼。

使用 R，您可以執行現有模組當前不支援的任務，例如： 
- 建立自訂資料轉換
- 使用您自己的指標評估預測
- 使用設計器中未作為獨立模組實現的演算法構建模型

## <a name="r-version-support"></a>R 版本支援

Azure 機器學習設計器使用 R 的 CRAN（綜合 R 存檔網路）分發。當前使用的版本為 CRAN 3.5.1。

## <a name="supported-r-packages"></a>受支援的 R 套件

R 環境預裝了 100 多個套裝軟體。 有關完整清單，請參閱[預先安裝 R 包](#pre-installed-r-packages)部分。

您還可以將以下代碼添加到任何**執行 R 腳本**模組，並查看已安裝的套裝軟體。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>安裝 R 套件
要安裝其他 R 包，`install.packages()`請使用 方法。 請務必指定 CRAN 存儲庫。 每個執行 R**腳本**模組都安裝了包，並且不會在其他**執行 R 腳本**模組之間共用。

此示例演示如何安裝 Zoo：
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
  > 請在安裝之前檢查包是否已存在，以避免重複安裝。 如`  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")`上例代碼所示。 重複安裝可能會導致 Web 服務請求超時。     

## <a name="upload-files"></a>上傳檔案
**執行 R 腳本**支援使用 Azure 機器學習 R SDK 上載檔。

下面的示例演示如何在**執行 R 腳本**中上載影像檔：
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

管道運行完成後，您可以在模組的右側面板中預覽圖像

> [!div class="mx-imgBorder"]
> ![上傳圖像](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>如何配置執行 R 腳本

**執行 R 腳本**模組包含示例代碼，您可以將這些代碼用作起點。 要配置**執行 R 腳本**模組，請提供一組要執行的輸入和代碼。

![R 模組](media/module/execute-r-script.png)

使用此模組載入時，存儲在設計器中的資料集將自動轉換為 R 資料幀。

1.  將**執行 R 腳本**模組添加到管道中。

  

1. 連接腳本所需的任何輸入。 輸入是可選的，可以包括資料和額外的 R 代碼。

    * **資料集1**：將第一個`dataframe1`輸入引用為 。 輸入資料集必須格式化為 CSV、TSV、ARFF，或者可以連接 Azure 機器學習資料集。

    * **資料集2**： 將第二`dataframe2`個輸入引用為 。 此資料集還必須格式化為 CSV、TSV、ARFF 檔或 Azure 機器學習資料集。

    * **腳本捆綁**包 ：第三個輸入接受 ZIP 檔。 壓縮的檔可以包含多個檔和多個檔案類型。

1. 在**R 腳本**文字方塊中，鍵入或粘貼有效的 R 腳本。

    為了説明您入門 **，R Script**文字方塊預填充了示例代碼，您可以對其進行編輯或替換。
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # The entry point function can contain up to two input arguments:
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a zip file is connected to the third input port, it is
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * 腳本必須包含名為 的`azureml_main`函數，該函數是此模組的進入點。

 * 進入點函數最多可以包含兩個輸入參數： `Param<dataframe1>``Param<dataframe2>`
 
   > [!NOTE]
    > 傳遞給執行 R**腳本**模組的資料被引用為`dataframe1`和`dataframe2`，這與 Azure 機器學習設計器（設計器引用為`dataset1` `dataset2`） 不同。 請檢查以確保輸入資料在腳本中正確引用。  
 
    > [!NOTE]
    >  現有 R 代碼可能需要少量更改才能在設計器管道中運行。 例如，在代碼中使用之前，應顯式將以 CSV 格式提供的輸入資料轉換為資料集。 R 語言中使用的資料和列類型在某些方面也不同于設計器中使用的資料和列類型。

1.  **隨機種子**：鍵入要在 R 環境中用作隨機種子值的值。 此參數等同於呼叫 R 程式碼中的 `set.seed(value)`。  

1. 提交管道。  

## <a name="results"></a>結果

**執行 R 腳本**模組可以返回多個輸出，但它們必須作為 R 資料幀提供。 資料幀會自動轉換為設計器中的資料集，以便與其他模組相容。

來自 R 的標準消息和錯誤將返回到模組的日誌。

如果需要在 R 腳本中列印結果，可以在模組右側面板的 **"輸出_logs"** 選項卡下的 **"70_driver_log**中找到列印的結果。

## <a name="sample-scripts"></a>範例指令碼

可以使用自訂 R 腳本擴展管道的方法有很多種。  本節提供常見任務的示例代碼。


### <a name="add-r-script-as-an-input"></a>將 R 腳本添加為輸入

**執行 R 腳本**模組支援任意 R 指令檔作為輸入。 為此，它們必須作為 ZIP 檔的一部分上載到工作區。

1. 要將包含 R 代碼的 ZIP 檔上載到工作區，請轉到**資料集**資產頁，按一下"**創建資料集**"，然後選擇 **"從本地檔和****檔**資料集類型"選項。  

1. 驗證壓縮檔在左側模組樹中的 **"資料集**"類別下的"**我的資料集"** 清單中是否可用。

1.  將資料集連接到**腳本捆綁輸入**埠。

1. ZIP 檔中包含的所有檔在管道運行時都可用。 

    如果腳本捆綁檔包含目錄結構，則保留該結構。 但是，您必須更改代碼，以將目錄 **./腳本捆綁包**預置到路徑。

### <a name="process-data"></a>處理資料

以下示例演示如何縮放和正常化輸入資料：

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>將 ZIP 檔讀為輸入

此示例演示如何在 ZIP 檔中使用資料集作為**執行 R 腳本**模組的輸入。

1. 以 CSV 格式創建資料檔案，並將其命名為"mydatafile.csv"。
1. 創建 ZIP 檔並將 CSV 檔添加到存檔中。
1. 將壓縮檔上載到 Azure 機器學習工作區。 
1. 將生成的資料集連接到**執行 R 腳本**模組的**腳本包**輸入。
1. 使用以下代碼從壓縮檔中讀取 CSV 資料。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>複製行

此示例演示如何複製資料集中的正記錄以平衡示例：

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>在執行 R 腳本模組之間傳遞 R 物件

您可以使用內部序列化機制，在 [執行 R 指令碼]**** 模組的執行個體之間傳遞 R 物件。 此示例假定要在兩個執行 R 腳本模組`A`之間移動命名的**R**物件。

1. 將第一個**執行 R 腳本**模組添加到管道中，並在 R **Script**文字方塊中鍵入以下代碼，以在`A`模組的輸出資料表中創建序列化物件作為列：  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    顯式轉換為整數類型是因為序列化函數輸出 R`Raw`格式的資料，而設計器不支援 R 格式的資料。

1. 添加**執行 R 腳本**模組的第二個實例，並將其連接到前一個模組的輸出埠。

1. 在**R Script**文字方塊中鍵入以下代碼，以便`A`從輸入資料表中提取物件。 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>預先安裝的 R 包

可使用的預先安裝 R 包的當前清單：

|              |            | 
|--------------|------------| 
| Package      | 版本    | 
| 問票      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| Class - 類別        | 7.3-15     | 
| cli          | 1.1.0      | 
| 剪貼機        | 0.6.0      | 
| 叢集      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| 編譯器     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| 資料集     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| 評估     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| 司 司長           | 1.3.1      | 
| gdata        | 2.18.0     | 
| 泛型     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| 圖形     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| 方格 (grid)         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| 矩陣       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| 空間      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| 小特克斯      | 0.13       | 
| 工具        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| 動物園          | 1.8-6      | 

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
