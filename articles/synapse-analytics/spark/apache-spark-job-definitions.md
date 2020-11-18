---
title: 教學課程：在 Synapse Studio 中建立 Apache Spark 作業定義
description: 教學課程 - 使用 Azure Synapse Analytics 來建立 Spark 作業定義，並將其提交至適用於 Azure Synapse Analytics 的 Apache Spark 集區。
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: b8c7792a09dd86e7d4ac043c572f69fc47ee6e63
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307184"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>教學課程：在 Synapse Studio 中建立 Apache Spark 作業定義

本教學課程示範如何使用 Azure Synapse Studio 來建立 Apache Spark 作業定義，然後將其提交至無伺服器 Apache Spark 集區。

本教學課程涵蓋下列工作：
> [!div class="checklist"]
>
> - 建立適用於 PySpark (Python) 的 Apache Spark 作業定義
> - 建立適用於 Spark (Scala) 的 Apache Spark 作業定義
> - 建立適用於 .NET Spark (C#/F#) 的 Apache Spark 作業定義
> - 以批次作業的形式提交 Apache Spark 作業定義
> - 將 Apache Spark 作業定義新增至管線

## <a name="prerequisites"></a>必要條件

開始本教學課程之前，請確定您符合下列需求：

* Azure Synapse Analytics 工作區。 如需指示，請參閱[建立 Azure Synapse Analytics 工作區](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)。
* 無伺服器 Apache Spark 集區。
* ADLS Gen2 儲存體帳戶。 您必須是所要使用的 ADLS Gen2 檔案系統的 **儲存體 Blob 資料擁有者**。 如果您還不是，則必須手動新增權限。
* 如果您不想要使用工作區預設儲存體，請在 Synapse Studio 中連結必要的 ADLS Gen2 儲存體帳戶。 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>建立適用於 PySpark (Python) 的 Apache Spark 作業定義

在本節中，您會建立適用於 PySpark (Python) 的 Apache Spark 作業定義。

1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。

2. 您可以移至 [用來建立 Apache Spark 作業定義的範例檔案](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python)，下載 **python.zip 的範例檔案**，然後將壓縮套件解壓縮，擷取 **wordcount.py** 和 **shakespeare.txt** 檔案。 

     ![範例檔案](./media/apache-spark-job-definitions/sample-files.png)

3. 選取 [資料] -> [已連結] -> [Azure Data Lake Storage Gen2]，然後將 **wordcount.py** 和 **shakespeare.txt** 上傳至您的 ADLS Gen2 檔案系統。 

     ![上傳 Python 檔案](./media/apache-spark-job-definitions/upload-python-file.png)

4. 選取 [開發] 中樞，再選取 [+] 圖示，然後選取 [Spark 作業定義] 以建立新的 Spark 作業定義。 

     ![建立 Python 的新定義](./media/apache-spark-job-definitions/create-new-definition.png)

5. 從 Apache Spark 作業定義主視窗的 [語言] 下拉式清單中，選取 [PySpark (Python)]。

     ![選取 Python](./media/apache-spark-job-definitions/select-python.png)

6. 填入 Apache Spark 作業定義的資訊。 

     |  屬性   | 描述   |  
     | ----- | ----- |  
     |作業定義名稱| 輸入 Spark 作業定義的名稱。 此名稱可隨時更新，直到作業定義發佈出去為止。 <br> 範例： `job definition sample`|
     |主要定義檔| 用於作業的主要檔案。 從您的儲存體選取 PY 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 <br> 範例： `abfss://…/path/to/wordcount.py`|
     |命令列引數| 作業的選擇性引數。 <br> 範例： `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意：範例作業定義的兩個引數是以空格分隔。*|
     |參考檔案| 主要定義檔中用來作為參考的其他檔案。 您可以選取 [上傳檔案]  ，以將檔案上傳至儲存體帳戶。 |
     |Spark 集區| 作業將會提交至選取的 Apache Spark 集區。|
     |Spark 版本| Apache Spark 集區正在執行的 Apache Spark 版本。|
     |執行程式| 要在指定 Apache Spark 集區中提供給作業使用的執行程式數目。|
     |執行程式大小| 在指定 Apache Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|  
     |驅動程式大小| 在指定 Apache Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

     ![設定適用於 Python 的 Spark 作業定義值](./media/apache-spark-job-definitions/create-py-definition.png)

7. 選取 [發佈] 以儲存 Apache Spark 作業定義。

     ![發佈 py 定義](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>建立適用於 Apache Spark (Scala) 的 Apache Spark 作業定義

在本節中，您會建立適用於 Apache Spark (Scala) 的 Apache Spark 作業定義。

 1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 您可以移至 [用來建立 Apache Spark 作業定義的範例檔案](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala)，下載 **scala.zip 的範例檔案**，然後將壓縮套件解壓縮，擷取 **wordcount.jar** 和 **shakespeare.txt** 檔案。 
 
     ![範例檔案 scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. 選取 [資料] -> [已連結] -> [Azure Data Lake Storage Gen2]，然後將 **wordcount.jar** 和 **shakespeare.txt** 上傳至您的 ADLS Gen2 檔案系統。
 
     ![準備 Scala 結構](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. 選取 [開發] 中樞，再選取 [+] 圖示，然後選取 [Spark 作業定義] 以建立新的 Spark 作業定義。 (範例影像與 **針對 PySpark 建立 Apache Spark 作業定義 (Python)** 步驟 4 的影像相同。)

 5. 從 Apache Spark 作業定義主視窗的 [語言] 下拉式清單中，選取 [Spark (Scala)]。

     ![選取 scala](./media/apache-spark-job-definitions/select-scala.png)

 6. 填入 Apache Spark 作業定義的資訊。 您可以複製範例資訊。

     |  屬性   | 描述   |  
     | ----- | ----- |  
     |作業定義名稱| 輸入 Spark 作業定義的名稱。 此名稱可隨時更新，直到作業定義發佈出去為止。 <br> 範例： `scala`|
     |主要定義檔| 用於作業的主要檔案。 從您的儲存體選取 JAR 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 <br> 範例： `abfss://…/path/to/wordcount.jar`|
     |Main class name (主要類別名稱)| 主要定義檔中的完整識別碼或主要類別。 <br> 範例： `WordCount`|
     |命令列引數| 作業的選擇性引數。 <br> 範例： `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意：範例作業定義的兩個引數是以空格分隔。* |
     |參考檔案| 主要定義檔中用來作為參考的其他檔案。 您可以選取 [上傳檔案]  ，以將檔案上傳至儲存體帳戶。|
     |Spark 集區| 作業將會提交至選取的 Apache Spark 集區。|
     |Spark 版本| Apache Spark 集區正在執行的 Apache Spark 版本。|
     |執行程式| 要在指定 Apache Spark 集區中提供給作業使用的執行程式數目。|  
     |執行程式大小| 在指定 Apache Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|
     |驅動程式大小| 在指定 Apache Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

     ![設定適用於 Scala 的 Spark 作業定義值](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. 選取 [發佈] 以儲存 Apache Spark 作業定義。

      ![發佈 Scala 定義](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>建立適用於 .NET Spark (C#/F#) 的 Apache Spark 作業定義

在本節中，您會建立適用於 .NET Spark (C#/F#) 的 Apache Spark 作業定義。
 1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 您可以移至 [用來建立 Apache Spark 作業定義的範例檔案](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET)，下載 **dotnet.zip 的範例檔案**，然後將壓縮套件解壓縮，擷取 **wordcount.zip** 和 **shakespeare.txt** 檔案。 

     ![範例 dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. 選取 [資料] -> [已連結] -> [Azure Data Lake Storage Gen2]，然後將 **wordcount.zip** 和 **shakespeare.txt** 上傳至您的 ADLS Gen2 檔案系統。
 
     ![準備 Dotnet 結構](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. 選取 [開發] 中樞，再選取 [+] 圖示，然後選取 [Spark 作業定義] 以建立新的 Spark 作業定義。 (範例影像與 **針對 PySpark 建立 Apache Spark 作業定義 (Python)** 步驟 4 的影像相同。)

 5. 從 Apache Spark 作業定義主視窗的 [語言] 下拉式清單中，選取 [.NET Spark (C#/F#)]。

     ![選取 dotnet](./media/apache-spark-job-definitions/select-dotnet.png)

 6. 填入 Apache Spark 作業定義的資訊。 您可以複製範例資訊。
    
     |  屬性   | 描述   |  
     | ----- | ----- |  
     |作業定義名稱| 輸入 Spark 作業定義的名稱。 此名稱可隨時更新，直到作業定義發佈出去為止。 <br> 範例： `dotnet`|
     |主要定義檔| 用於作業的主要檔案。 從儲存體中選取 .NET for Apache Spark 應用程式 (亦即，主要的可執行檔、包含使用者定義函式的 DLL，以及其他必要檔案) 所在的 ZIP 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 <br> 範例： `abfss://…/path/to/wordcount.zip`|
     |主要可執行檔| 主要定義 ZIP 檔案中的主要可執行檔。 <br> 範例： `WordCount`|
     |命令列引數| 作業的選擇性引數。 <br> 範例： `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *注意：範例作業定義的兩個引數是以空格分隔。* |
     |參考檔案| 背景工作節點為了執行主要定義 ZIP 檔案中未包含的 .NET for Apache Spark 應用程式所需的其他檔案 (亦即，相依的 jar、其他的使用者定義函式 DLL，以及其他設定檔)。 您可以選取 [上傳檔案]  ，以將檔案上傳至儲存體帳戶。|
     |Spark 集區| 作業將會提交至選取的 Apache Spark 集區。|
     |Spark 版本| Apache Spark 集區正在執行的 Apache Spark 版本。|
     |執行程式| 要在指定 Apache Spark 集區中提供給作業使用的執行程式數目。|  
     |執行程式大小| 在指定 Apache Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|
     |驅動程式大小| 在指定 Apache Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

     ![設定適用於 Dotnet 的 Spark 作業定義值](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. 選取 [發佈] 以儲存 Apache Spark 作業定義。

      ![發佈 Dotnet 定義](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>以批次作業的形式提交 Apache Spark 作業定義

在建立 Apache Spark 作業定義後，您可以將其提交至 Apache Spark 集區。 請確定您是所要使用的 ADLS Gen2 檔案系統的 **儲存體 Blob 資料擁有者**。 如果您還不是，則必須手動新增權限。

### <a name="scenario-1-submit-apache-spark-job-definition"></a>案例 1：提交 Apache Spark 作業定義
 1. 選取 Apache Spark 作業定義以開啟其視窗。

      ![開啟要提交的 Spark 作業定義 ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. 選取 [提交] 圖示將您的專案提交至選取的 Apache Spark 集區。 您可以選取 [Spark 監視 URL] 索引標籤，以查看 Apache Spark 應用程式的 LogQuery。

    ![選取 [提交] 按鈕以提交 Spark 作業定義](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![[提交 Spark] 對話方塊](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>案例 2：檢視 Apache Spark 作業的執行進度

 1. 選取 [監視器]，然後選取 [Apache Spark 應用程式] 選項。 您可以找到已提交的 Apache Spark 應用程式。

     ![檢視 Spark 應用程式](./media/apache-spark-job-definitions/view-spark-application.png)

 2. 然後，選取 Apache Spark 應用程式，[SparkJobDefinition] 作業視窗隨即顯示。 您可以從這裡檢視作業的執行進度。
     
     ![檢視 Spark 應用程式 LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>案例 3：檢查輸出檔案

 1. 選取 [資料] -> [已連結] -> [Azure Data Lake Storage Gen2] (hozhaobdbj)，開啟先前建立的 [結果] 資料夾，您可以移至 [結果] 資料夾，並檢查是否已產生輸出。

     ![檢視輸出檔案](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>將 Apache Spark 作業定義新增至管線

在本節中，您會將 Apache Spark 作業定義新增至管線。

 1. 開啟現有的 Apache Spark 作業定義。

 2. 選取 Apache Spark 作業定義右上方的圖示，選擇 [現有的管線] 或 [新增管線]。 如需詳細資訊，您可以參考 [管線] 頁面。

     ![新增至 pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![新增至 pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>後續步驟

接下來，您可以使用 Azure Synapse Studio 來建立 Power BI 資料集和管理 Power BI 資料。 繼續參閱[將 Power BI 工作區連結至 Synapse 工作區](../quickstart-power-bi.md)一文，以深入了解。 

