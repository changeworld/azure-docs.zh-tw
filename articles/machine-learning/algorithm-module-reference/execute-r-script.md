---
title: 執行 R 腳本：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [執行 R 腳本] 模組來執行 R 程式碼。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: 3559ae5c246129aa369cb49e7749e499002f1dc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87048190"
---
# <a name="execute-r-script-module"></a>執行 R 腳本模組

本文說明如何使用 [執行 R 腳本] 模組，在您的 Azure Machine Learning designer （預覽）管線中執行 R 程式碼。

使用 R，您可以執行現有模組目前不支援的工作，例如： 
- 建立自訂資料轉換
- 使用您自己的計量來評估預測
- 使用未在設計工具中實作為獨立模組的演算法來建立模型

## <a name="r-version-support"></a>R 版本支援

Azure Machine Learning 設計工具會使用 R 的 CRAN （全方位 R 封存網路）散發。目前使用的版本是 CRAN 3.5.1。

## <a name="supported-r-packages"></a>受支援的 R 套件

R 環境已預先安裝了100以上的套件。 如需完整清單，請參閱[預先安裝的 R 套件](#preinstalled-r-packages)一節。

您也可以將下列程式碼新增至任何執行 R 腳本模組，以查看已安裝的封裝。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> 如果您的管線包含多個執行 R 腳本模組，而這些模組需要不在預先安裝清單中的封裝，請在每個模組中安裝套件。 

## <a name="installing-r-packages"></a>安裝 R 套件
若要安裝其他 R 套件，請使用 `install.packages()` 方法。 系統會針對每個執行 R 腳本模組安裝封裝。 它們不會在其他執行 R 腳本模組之間共用。

> [!NOTE]
> 當您安裝套件時，請指定 CRAN 存放庫，例如 `install.packages("zoo",repos = "http://cran.us.r-project.org")` 。

此範例說明如何安裝 Zoo：
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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
 > 安裝套件之前，請檢查它是否已存在，以避免重複安裝。 [重複安裝] 可能會導致 web 服務要求超時。     

## <a name="uploading-files"></a>上傳檔案
[執行 R 腳本] 模組支援使用 Azure Machine Learning R SDK 上傳檔案。

下列範例顯示如何在執行 R 腳本中上傳影像檔：
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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

管線執行完成之後，您可以在模組的右面板中預覽影像。

> [!div class="mx-imgBorder"]
> ![已上傳影像的預覽](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>如何設定執行 R 腳本

[執行 R 腳本] 模組包含可用來做為起點的範例程式碼。 若要設定執行 R 腳本模組，請提供一組要執行的輸入和程式碼。

![R 模組輸入的圖表](media/module/execute-r-script.png)

載入此模組時，會自動將儲存在設計工具中的資料集轉換成 R 資料框架。

1.  將 [**執行 R 腳本**] 模組新增至您的管線。  

1. 連接腳本所需的任何輸入。 輸入是選擇性的，而且可以包含資料和其他 R 程式碼。

    * **Dataset1**：以形式參考第一個輸入 `dataframe1` 。 輸入資料集必須格式化為 CSV、TSV 或 ARFF 檔案。 或者，您也可以連接 Azure Machine Learning 資料集。

    * **Dataset2**：以形式參考第二個輸入 `dataframe2` 。 此資料集也必須格式化為 CSV、TSV 或 ARFF 檔案，或做為 Azure Machine Learning 資料集。

    * **腳本**配套：第三個輸入會接受 .zip 檔案。 Zip 壓縮檔案可以包含多個檔案和多個檔案類型。

1. 在 [ **R 腳本**] 文字方塊中，輸入或貼上有效的 R 腳本。

    > [!NOTE]
    > 撰寫腳本時請小心。 請確定沒有任何語法錯誤，例如使用未宣告的變數或未匯入模組或函數。 請特別注意本文結尾的預先安裝的套件清單。 若要使用未列出的封裝，請將它們安裝在您的腳本中。 例如 `install.packages("zoo",repos = "http://cran.us.r-project.org")`。
    
    為了協助您開始使用，[ **R 腳本**] 文字方塊會預先填入範例程式碼，您可以編輯或取代它。
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    進入點函式必須具有輸入引數 `Param<dataframe1>` 和 `Param<dataframe2>` ，即使函式中未使用這些引數也一樣。

    > [!NOTE]
    > 傳遞至執行 R 腳本模組的資料會當做 `dataframe1` 和來參考 `dataframe2` ，這與 Azure Machine Learning 設計工具（設計工具參考為 `dataset1` 、 `dataset2` ）不同。 請確定您的腳本中正確地參考了輸入資料。  
 
    > [!NOTE]
    > 現有的 R 程式碼可能需要較小的變更，才能在設計工具管線中執行。 例如，您以 CSV 格式提供的輸入資料應該先明確轉換成資料集，您才能在程式碼中使用它。 在 R 語言中使用的資料和資料行類型，在設計工具中使用的資料和資料行類型方面也有不同的差異。

1.  針對 [**隨機種子**]，輸入要在 R 環境內用來做為隨機種子值的值。 此參數等同於呼叫 R 程式碼中的 `set.seed(value)`。  

1. 提交管線。  

## <a name="results"></a>結果

執行 R 腳本模組可以傳回多個輸出，但必須以 R 資料框架的形式提供。 資料框架會自動轉換成設計工具中的資料集，以與其他模組相容。

來自 R 的標準訊息和錯誤會傳回給模組的記錄檔。

如果您需要將結果列印在 R 腳本中，您可以在模組右側面板中的 [**輸出 + 記錄**] 索引標籤底下**70_driver_log** ，找到列印的結果。

## <a name="sample-scripts"></a>範例指令碼

有許多方法可以使用自訂 R 腳本來擴充您的管線。 本節提供一般工作的範例程式碼。


### <a name="add-an-r-script-as-an-input"></a>新增 R 腳本作為輸入

[執行 R 腳本] 模組支援任意的 R 腳本檔案做為輸入。 若要使用它們，您必須將它們上傳到您的工作區，做為 .zip 檔案的一部分。

1. 若要將包含 R 程式碼的 .zip 檔案上傳至您的工作區，請移至 [**資料集**資產] 頁面。 選取 **[** **建立資料集**]，然後選取 [**從本機**檔案] 和 [檔案資料集類型] 選項。  

1. 確認左側模組樹狀目錄中 [**資料集**] 類別底下的 [**我的資料集**] 清單中有可壓縮的檔案。

1.  將資料集連接至 [**腳本**組合] 輸入埠。

1. .Zip 檔案中的所有檔案都可在管線執行時間使用。 

    如果腳本組合檔案包含目錄結構，則會保留結構。 但是您必須更改程式碼，才能在路徑前面加上 **/Script**組合。

### <a name="process-data"></a>處理資料

下列範例示範如何調整和標準化輸入資料：

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>讀取 .zip 檔案作為輸入

這個範例示範如何使用 .zip 檔案中的資料集做為執行 R 腳本模組的輸入。

1. 建立 CSV 格式的資料檔案，並將它命名為**mydatafile.csv**。
1. 建立 .zip 檔，並將 CSV 檔案新增至封存。
1. 將壓縮檔案上傳至您的 Azure Machine Learning 工作區。 
1. 將產生的資料集連接到**執行 R 腳本**模組的**ScriptBundle**輸入。
1. 使用下列程式碼，從 zip 壓縮檔案讀取 CSV 資料。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>複寫資料列

這個範例示範如何在資料集內複寫正向記錄，以平衡範例：

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

您可以使用內部序列化機制，在 [執行 R 指令碼] 模組的執行個體之間傳遞 R 物件。 這個範例假設您想要在 `A` 兩個執行 r 腳本模組之間移動名為的 r 物件。

1. 將第一個**執行 R 腳本**模組新增至您的管線。 然後在 [ **R 腳本**] 文字方塊中輸入下列程式碼，以建立序列化物件， `A` 做為模組輸出資料表中的資料行：  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    因為序列化函式會以 R 格式輸出資料 `Raw` （設計工具不支援），所以明確轉換成整數類型是完成的。

1. 新增 [**執行 R 腳本**] 模組的第二個實例，並將它連接到上一個模組的輸出埠。

1. 在 [ **R 腳本**] 文字方塊中輸入下列程式碼，將物件 `A` 從輸入資料表中解壓縮。 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>預先安裝的 R 套件

下列預先安裝的 R 套件目前可供使用：

| 套件      | 版本    | 
|--------------|------------| 
| askpass      | 1.1        | 
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
| clipr        | 0.6.0      | 
| 叢集      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| datasets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| 泛型     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| graphics     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| 方格         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1：      | 
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
| lubridate    | avro-mapred-1.7.4-hadoop2.jar      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51。4   | 
| Matrix       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1。1 | 
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
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44 秒內-1。1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| tools        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | lsscsi-0.23-2.el6.x86 64.rpm-4     | 
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
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
