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
ms.date: 11/19/2019
ms.openlocfilehash: 4991fe9fb06d0a49da3e0404f0362c85f15571af
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152290"
---
# <a name="execute-r-script"></a>執行 R 指令碼

本文說明如何使用 [**執行 r 腳本**] 模組，在您的 Azure Machine Learning designer （預覽）管線中執行 r 程式碼。

使用 R，您可以執行現有模組目前不支援的工作，例如： 
- 建立自訂資料轉換
- 使用您自己的計量來評估預測
- 使用未在設計工具中實作為獨立模組的演算法來建立模型

## <a name="r-version-support"></a>R 版本支援

Azure Machine Learning 設計工具會使用 R 的 CRAN （全方位 R 封存網路）散發。目前使用的版本是 CRAN 3.5.1。

## <a name="supported-r-packages"></a>受支援的 R 套件

R 環境已預先安裝了超過100個套件。 如需完整清單，請參閱[預先安裝的 R 套件](#pre-installed-r-packages)一節。

您也可以將下列程式碼新增至任何**執行 R 腳本**模組，並查看已安裝的封裝。

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>安裝 R 套件
若要安裝其他 R 套件，請使用 `install.packages()` 方法。 請務必指定 CRAN 存放庫。 封裝會針對每個**執行 r 腳本**模組安裝，而且不會在其他**執行 r 腳本**模組之間共用。

此範例說明如何安裝 Zoo：
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
  > 請先檢查套件是否已存在，再加以安裝，以避免重複安裝。 如同上述範例程式碼中的 `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")`。 重複安裝可能會導致 web 服務要求超時。     

## <a name="how-to-configure-execute-r-script"></a>如何設定執行 R 腳本

[**執行 R 腳本**] 模組包含可用來做為起點的範例程式碼。 若要設定**執行 R 腳本**模組，請提供一組要執行的輸入和程式碼。

![R 模組](media/module/execute-r-script.png)

載入此模組時，會自動將儲存在設計工具中的資料集轉換成 R 資料框架。

1.  將 [**執行 R 腳本**] 模組新增至您的管線。

  

1. 連接腳本所需的任何輸入。 輸入是選擇性的，而且可以包含資料和其他 R 程式碼。

    * **Dataset1**：以 `dataframe1`參考第一個輸入。 輸入資料集的格式必須為 CSV、TSV、ARFF，或者您可以連接 Azure Machine Learning 資料集。

    * **Dataset2**：參考第二個輸入做為 `dataframe2`。 此資料集也必須格式化為 CSV、TSV、ARFF 檔案，或做為 Azure Machine Learning 資料集。

    * **腳本**配套：第三個輸入接受 ZIP 檔案。 Zip 壓縮檔案可以包含多個檔案和多個檔案類型。

1. 在 [ **R 腳本**] 文字方塊中，輸入或貼上有效的 R 腳本。

    為了協助您開始使用，[ **R 腳本**] 文字方塊會預先填入範例程式碼，可供您編輯或取代。
    
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

 * 腳本必須包含名為 `azureml_main`的函式，這是此模組的進入點。

 * 進入點函數最多可包含兩個輸入引數： `Param<dataframe1>` 和 `Param<dataframe2>`
 
   > [!NOTE]
    > 傳遞至**執行 R 腳本**模組的資料會當做 `dataframe1` 和 `dataframe2`參考，這與 Azure Machine Learning 設計工具（設計工具參考為 `dataset1`，`dataset2`）不同。 請檢查並確定已在您的腳本中正確 referneced 輸入資料。  
 
    > [!NOTE]
    >  現有的 R 程式碼可能需要較小的變更，才能在設計工具管線中執行。 例如，您以 CSV 格式提供的輸入資料應該先明確轉換成資料集，您才能在程式碼中使用它。 在 R 語言中使用的資料和資料行類型，在設計工具中使用的資料和資料行類型方面也有不同的差異。

1.  **隨機種子**：輸入要在 R 環境內用來做為隨機種子值的值。 此參數等同於呼叫 R 程式碼中的 `set.seed(value)`。  

1. 執行管道。  

## <a name="results"></a>結果

**執行 R 腳本**模組可以傳回多個輸出，但必須以 R 資料框架的形式提供。 資料框架會自動轉換成設計工具中的資料集，以與其他模組相容。

來自 R 的標準訊息和錯誤會傳回給模組的記錄檔。

## <a name="sample-scripts"></a>範例指令碼

有許多方法可讓您使用自訂 R 腳本來擴充管線。  本節提供一般工作的範例程式碼。


### <a name="add-r-script-as-an-input"></a>將 R 腳本新增為輸入

[**執行 R 腳本**] 模組支援任意的 r 腳本檔案做為輸入。 若要這麼做，您必須將它們上傳到您的工作區，做為 ZIP 檔案的一部分。

1. 若要將包含 R 程式碼的 ZIP 檔案上傳至工作區，請依序按一下 [**新增**]、[**資料集**] 和 [**從本機**檔案] 和 [ **ZIP**檔案] 選項。  

1. 在 [**已儲存的資料集**] 清單中，確認已壓縮檔案可供使用。

1.  將資料集連接至 [**腳本**組合] 輸入埠。

1. ZIP 檔案中包含的所有檔案都可在管線執行時間使用。 

    如果腳本組合檔案包含目錄結構，則會保留結構。 不過，您必須修改程式碼，才能在路徑前面加上 **/Script**組合。

### <a name="process-data"></a>處理資料

下列範例示範如何調整和標準化輸入資料：

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

### <a name="read-a-zip-file-as-input"></a>讀取 ZIP 檔案做為輸入

這個範例示範如何使用 ZIP 檔案中的資料集做為**執行 R 腳本**模組的輸入。

1. 建立 CSV 格式的資料檔案，並將它命名為 "mydatafile"。
1. 建立 ZIP 檔，並將 CSV 檔案新增至封存。
1. 將壓縮檔案上傳至您的 Azure Machine Learning 工作區。 
1. 將產生的資料集連接到**執行 R 腳本**模組的**ScriptBundle**輸入。
1. 使用下列程式碼從 zip 壓縮檔案讀取 CSV 資料。

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

您可以使用內部序列化機制，在 [執行 R 指令碼] 模組的執行個體之間傳遞 R 物件。 這個範例假設您想要在兩個**執行 r 腳本**模組之間移動名為 `A` 的 R 物件。

1. 將第一個 [**執行 R 腳本**] 模組加入您的管線，然後在 [ **R 腳本**] 文字方塊中輸入下列程式碼，以建立序列化物件 `A` 做為模組輸出資料表中的資料行：  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    將明確轉換成整數類型是因為序列化函式會以 R `Raw` 格式輸出資料，但設計工具並不支援。

1. 新增 [**執行 R 腳本**] 模組的第二個實例，並將它連接到上一個模組的輸出埠。

1. 在 [ **R 腳本**] 文字方塊中輸入下列程式碼，以從輸入資料表中解壓縮物件 `A`。 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>預先安裝的 R 套件

可供使用的預先安裝 R 套件目前清單：

|              |            | 
|--------------|------------| 
| 套件      | 版本    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| 反向移植    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| 開機         | 1.3-22     | 
| 掃帚        | 0.5.2      | 
| callr        | 3.2.0      | 
| 插入號        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| 群集      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
| 編譯器     | 3.5.1      | 
| 起來       | 1.3.4      | 
| 彎曲         | 3.3        | 
| 資料。資料表   | 1.12.2     | 
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
| 外部的      | 0.8-71     | 
| fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| 泛型     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| 黏附         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| 圖形     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| 格線         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1：      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| Iterator    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1.23       | 
| 標籤     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | avro-mapred-1.7.4-hadoop2.jar      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| 大規模         | 7.3-51。4   | 
| 矩陣       | 1.2-17     | 
| 方法      | 3.5.1      | 
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
| 要件       | 1.4.1      | 
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
| 等      | 0.1.5      | 
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
| 縮放比例       | 1.0.0      | 
| 選取      | 0.4-1      | 
| 空間      | 7.3-11     | 
| 曲線      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| 至關重要     | 2.44 秒內-1。1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| Timedate 時間     | 3043.102   | 
| tinytex      | 0.13       | 
| 工具        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | lsscsi-0.23-2.el6.x86 64.rpm-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| 須值      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| 動物園          | 1.8-6      | 

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 
