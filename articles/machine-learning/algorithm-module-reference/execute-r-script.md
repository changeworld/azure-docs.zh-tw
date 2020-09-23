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
ms.date: 07/27/2020
ms.openlocfilehash: d5ef8d6a9b0c0039b500ce9d0238609e8a8edc93
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908004"
---
# <a name="execute-r-script-module"></a>執行 R 腳本模組

本文說明如何使用 [執行 R 腳本] 模組，在您的 Azure Machine Learning 設計工具管線中執行 R 程式碼。

使用 R 時，您可以執行現有模組目前不支援的工作，例如： 
- 建立自訂資料轉換
- 使用您自己的度量來評估預測
- 使用未在設計工具中實作為獨立模組的演算法來建立模型

## <a name="r-version-support"></a>R 版本支援

Azure Machine Learning 設計工具會使用 CRAN (全方位 R 封存網路) 的 R 散發套件。目前使用的版本是 CRAN 3.5.1。

## <a name="supported-r-packages"></a>受支援的 R 套件

R 環境已預先安裝超過100個套件。 如需完整清單，請參閱 [預先安裝的 R 套件](#preinstalled-r-packages)一節。

您也可以將下列程式碼新增至任何「執行 R 腳本」模組，以查看已安裝的封裝。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> 如果您的管線包含多個執行 R 腳本模組，而這些模組需要的套件不在預先安裝的清單中，請在每個模組中安裝套件。 

## <a name="installing-r-packages"></a>安裝 R 套件
若要安裝其他 R 套件，請使用 `install.packages()` 方法。 系統會為每個「執行 R 腳本」模組安裝套件。 它們不會在其他執行 R 腳本模組之間共用。

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
 > 安裝封裝之前，請先檢查是否已存在，如此您就不會重複執行安裝。 重複安裝可能會導致 web 服務要求超時。     

## <a name="uploading-files"></a>上傳檔案
「執行 R 腳本」模組支援使用 Azure Machine Learning R SDK 來上傳檔案。

下列範例示範如何在執行 R 腳本中上傳影像檔案：
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

管線執行完成之後，您可以在模組的右面板中預覽映射。

> [!div class="mx-imgBorder"]
> ![已上傳影像的預覽](media/module/upload-image-in-r-script.png)

## <a name="access-to-registered-dataset"></a>存取已註冊的資料集

您可以參考下列範例程式碼，以存取您工作區中 [已註冊的資料集](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets#access-datasets-in-your-script) ：

```R
        azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>如何設定執行 R 腳本

[執行 R 腳本] 模組包含可作為起點的範例程式碼。 若要設定 [執行 R 腳本] 模組，請提供一組要執行的輸入和程式碼。

![R 模組輸入的圖表](media/module/execute-r-script.png)

使用此模組載入時，儲存在設計工具中的資料集會自動轉換成 R 資料框架。

1.  將 [ **執行 R 腳本** ] 模組新增至您的管線。  

1. 連接腳本所需的任何輸入。 輸入是選擇性的，而且可以包含資料和其他 R 程式碼。

    * **Dataset1**：將第一個輸入參考為 `dataframe1` 。 輸入資料集必須格式化為 CSV、TSV 或 ARFF 檔案。 或者，您也可以連接 Azure Machine Learning 資料集。

    * **Dataset2**：將第二個輸入參考為 `dataframe2` 。 此資料集也必須格式化為 CSV、TSV 或 ARFF 檔案，或做為 Azure Machine Learning 資料集。

    * **腳本**組合：第三個輸入接受 .zip 檔。 壓縮檔案可包含多個檔案和多個檔案類型。

1. 在 [ **R 腳本** ] 文字方塊中，輸入或貼上有效的 R 腳本。

    > [!NOTE]
    > 撰寫腳本時請務必小心。 請確定沒有語法錯誤，例如使用未宣告的變數或未匯入模組或函數。 請特別注意本文結尾的預先安裝套件清單。 若要使用未列出的封裝，請在您的腳本中安裝它們。 例如 `install.packages("zoo",repos = "http://cran.us.r-project.org")`。
    
    為協助您開始使用，您可以編輯或取代 [ **R 腳本** ] 文字方塊中的範例程式碼。
    
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

    輸入點函式必須具有輸入引數，而且在函式 `Param<dataframe1>` `Param<dataframe2>` 中未使用這些引數時也必須有這些引數。

    > [!NOTE]
    > 傳遞至「執行 R 腳本」模組的資料會參考為 `dataframe1` 和 `dataframe2` ，這與設計工具參考 (的 Azure Machine Learning 設計工具不同， `dataset1` `dataset2`) 。 請確定您的腳本中正確參考了輸入資料。  
 
    > [!NOTE]
    > 現有的 R 程式碼可能需要在設計工具管線中執行較少的變更。 例如，您以 CSV 格式提供的輸入資料應該先明確轉換成資料集，您才能在程式碼中使用它。 在 R 語言中使用的資料和資料行類型，在設計工具中使用的資料和資料行類型的某些方式也不同。

    如果您的腳本大於16KB，請使用 **腳本** 套件組合埠來避免像 *命令列一樣的錯誤超過16597個字元的限制*。 
    
    將腳本和其他自訂資源組合成 zip 檔案，並將 zip 檔案以檔案 **資料集** 的形式上傳至 studio。 然後，您可以從 [設計師撰寫] 頁面的左模組窗格中的 [ *我的資料集* ] 清單，拖曳資料集模組。 將資料集模組連接至 [**執行 R 腳本**] 模組的**腳本**組合埠。
    
    以下是在腳本套件組合中使用腳本的範例程式碼：

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  針對 [ **隨機種子**]，輸入要在 R 環境內用來作為隨機種子值的值。 此參數等同於呼叫 R 程式碼中的 `set.seed(value)`。  

1. 提交管線。  

## <a name="results"></a>結果

執行 R 腳本模組可以傳回多個輸出，但必須提供這些輸出作為 R 資料框架。 資料框架會自動轉換為設計工具中的資料集，以與其他模組相容。

標準訊息和 R 的錯誤會傳回至模組的記錄檔。

如果您需要在 R 腳本中列印結果，可以在模組右面板的 [**輸出 + 記錄**] 索引標籤下，找到**70_driver_log**中的列印結果。

## <a name="sample-scripts"></a>範例指令碼

有許多方式可以使用自訂 R 腳本擴充您的管線。 本節提供一般工作的範例程式碼。


### <a name="add-an-r-script-as-an-input"></a>新增 R 腳本做為輸入

「執行 R 腳本」模組支援任意 R 腳本檔案作為輸入。 若要使用它們，您必須將它們上傳至您的工作區，作為 .zip 檔案的一部分。

1. 若要將包含 R 程式碼的 .zip 檔案上傳至您的工作區，請移至 [ **資料集** 資產] 頁面。 選取 [**建立資料集**]，然後**選取 [** **從本機**檔案] 和 [檔案資料集類型] 選項。  

1. 在左側模組樹狀結構的 [**資料集**] 類別下，確認 [**我的資料集**] 清單中是否有 zip 壓縮檔案。

1.  將資料集連線至 **腳本** 套件組合輸入埠。

1. 在管線執行時間，可以使用 .zip 檔案中的所有檔案。 

    如果腳本套件組合檔案包含目錄結構，則會保留結構。 但是您必須變更程式碼，以將 **/Script** 組合附加至路徑。

### <a name="process-data"></a>處理資料

下列範例顯示如何調整和標準化輸入資料：

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

這個範例示範如何使用 .zip 檔案中的資料集做為「執行 R 腳本」模組的輸入。

1. 建立 CSV 格式的資料檔案，並將它命名為 **mydatafile.csv**。
1. 建立 .zip 檔案，並將 CSV 檔案新增至封存。
1. 將 zip 壓縮檔案上傳至您的 Azure Machine Learning 工作區。 
1. 將產生的資料集連接至 [**執行 R 腳本**] 模組的**ScriptBundle**輸入。
1. 使用下列程式碼從 zip 壓縮檔案讀取 CSV 資料。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>複製資料列

此範例示範如何在資料集中複寫正面記錄以平衡範例：

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

您可以使用內部序列化機制，在 [執行 R 指令碼] 模組的執行個體之間傳遞 R 物件。 此範例假設您想要在 `A` 兩個「執行 r 腳本」模組之間移動名為的 R 物件。

1. 將第一個 [ **執行 R 腳本** ] 模組新增至您的管線。 然後，在 [ **R 腳本** ] 文字方塊中輸入下列程式碼，以建立序列化物件， `A` 做為模組輸出資料表中的資料行：  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    因為序列化函數會以 R 格式輸出資料 `Raw` （設計工具不支援），所以會明確轉換成整數類型。

1. 加入 [ **執行 R 腳本** ] 模組的第二個實例，並將它連接到上一個模組的輸出埠。

1. 在 [ **R 腳本** ] 文字方塊中輸入下列程式碼，以 `A` 從輸入資料表中將物件解壓縮。 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>預先安裝的 R 套件

目前提供下列預先安裝的 R 套件：

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
| 司 司長           | 1.3.1      | 
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
| lubridate    | "      | 
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
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
