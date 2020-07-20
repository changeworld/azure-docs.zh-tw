---
title: 教學課程：在 Synapse Studio 中建立 Apache Spark 作業定義
description: 教學課程 - 使用 Azure Synapse Analytics 來建立 Spark 作業定義，並將其提交至適用於 Azure Synapse Analytics 的 Apache Spark 集區。
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.subservice: ''
ms.date: 04/15/2020
ms.openlocfilehash: 3311a9a92cc5e63a6fa20e4dd0d2af00fdacc95c
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194479"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>教學課程：在 Synapse Studio 中建立 Apache Spark 作業定義

本教學課程示範如何使用 Azure Synapse Studio 來建立 Apache Spark 作業定義，然後將其提交至 Apache Spark 集區。

本教學課程涵蓋下列工作：

* 建立適用於 PySpark (Python) 的 Apache Spark 作業定義
* 建立適用於 Spark (Scala) 的 Apache Spark 作業定義
* 建立適用於 .NET Spark (C#) 的 Apache Spark 作業定義
* 以批次作業的形式提交 Apache Spark 作業定義
* 將 Apache Spark 作業定義新增至管線

## <a name="prerequisites"></a>必要條件

開始本教學課程之前，請確定您符合下列需求：

* Azure Synapse Analytics 工作區。 如需指示，請參閱[建立 Azure Synapse Analytics 工作區](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace)。
* Apache Spark 集區
* ADLS Gen2 儲存體帳戶。 您必須是所要使用的 ADLS Gen2 檔案系統的儲存體 Blob 資料擁有者。 如果您還不是，則必須手動新增權限。

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>建立適用於 PySpark (Python) 的 Apache Spark 作業定義

在本節中，您會建立適用於 PySpark (Python) 的 Apache Spark 作業定義。

1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。

2. 您可以移至[用來建立 Apache Spark 作業定義的範例檔案](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python)，以下載 **wordcount.jar** 和 **shakespear.txt**。 然後，將這些檔案上傳至 Azure 儲存體：按一下 [資料]、選取 [儲存體帳戶]，然後將相關檔案上傳到您的 ADLS Gen2 檔案系統。 如果您的檔案已在 Azure 儲存體中，請略過此步驟。 

     ![上傳 Python 檔案](./media/apache-spark-job-definitions/upload-python-file.png)

3. 按一下 [開發] 中樞，從左窗格中選取 [Spark 作業定義]，按一下[Spark 作業定義] 旁的 [...] 動作節點，然後在內容功能表中選取 [新增 Spark 作業定義]。

     ![建立 Python 的新定義](./media/apache-spark-job-definitions/create-new-definition.png)

4. 從 Apache Spark 作業定義主視窗的 [語言] 下拉式清單中，選取 [PySpark (Python)]。

5. 填入 Apache Spark 作業定義的資訊。 您可以複製範例資訊。

     |  屬性   | 描述   |  
     | ----- | ----- |  
     |作業定義名稱| 輸入 Spark 作業定義的名稱。 此名稱可隨時更新，直到作業定義發佈出去為止。 範例： `job definition sample`|
     |主要定義檔| 用於作業的主要檔案。 從您的儲存體選取 PY 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 範例： `abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/fileexists.py`|
     |命令列引數| 作業的選擇性引數。 範例： `shakespeare.txt`|
     |參考檔案| 主要定義檔中用來作為參考的其他檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 範例： `abfss://azureportaldeploy@storageaccountname.dfs.core.windows.net/synapse/workspaces/workspace name/batchjobs/python/shakespeare.txt`|
     |Spark 集區| 作業將會提交至選取的 Apache Spark 集區。|
     |Spark 版本| Apache Spark 集區正在執行的 Apache Spark 版本。|
     |執行程式| 要在指定 Apache Spark 集區中提供給作業使用的執行程式數目。|
     |執行程式大小| 在指定 Apache Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|  
     |驅動程式大小| 在指定 Apache Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

     ![設定適用於 Python 的 Spark 作業定義值](./media/apache-spark-job-definitions/create-py-definition.png)

6. 選取 [發佈] 以儲存 Apache Spark 作業定義。

     ![發佈 py 定義](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>建立適用於 Apache Spark (Scala) 的 Apache Spark 作業定義

在本節中，您會建立適用於 Apache Spark (Scala) 的 Apache Spark 作業定義。

 1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 您可以移至[用來建立 Apache Spark 作業定義的範例檔案](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala)，以下載 **wordcount.jar** 和 **shakespear.txt**。 然後，將這些檔案上傳至 Azure 儲存體：按一下 [資料]、選取 [儲存體帳戶]，然後將相關檔案上傳到您的 ADLS Gen2 檔案系統。 如果您的檔案已在 Azure 儲存體中，請略過此步驟。 
 
     ![準備 Scala 結構](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. 按一下 [開發] 中樞，從左窗格中選取 [Spark 作業定義]，按一下[Spark 作業定義] 旁的 [...] 動作節點，然後在內容功能表中選取 [新增 Spark 作業定義]。
     ![建立 Scala 的新定義](./media/apache-spark-job-definitions/create-new-definition.png)

 4. 從 Apache Spark 作業定義主視窗的 [語言] 下拉式清單中，選取 [Spark (Scala)]。

 5. 填入 Apache Spark 作業定義的資訊。 您可以複製範例資訊。

     |  屬性   | 描述   |  
     | ----- | ----- |  
     |作業定義名稱| 輸入 Spark 作業定義的名稱。 此名稱可隨時更新，直到作業定義發佈出去為止。 範例： `job definition sample`|
     |主要定義檔| 用於作業的主要檔案。 從您的儲存體選取 JAR 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 範例： `abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/wordcount.jar`|
     |Main class name (主要類別名稱)| 主要定義檔中的完整識別碼或主要類別。 範例： `WordCount`|
     |命令列引數| 作業的選擇性引數。 範例： `abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/scala/wordcount/result`|
     |參考檔案| 主要定義檔中用來作為參考的其他檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。|
     |Spark 集區| 作業將會提交至選取的 Apache Spark 集區。|
     |Spark 版本| Apache Spark 集區正在執行的 Apache Spark 版本。|
     |執行程式| 要在指定 Apache Spark 集區中提供給作業使用的執行程式數目。|  
     |執行程式大小| 在指定 Apache Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|
     |驅動程式大小| 在指定 Apache Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

     ![設定適用於 Scala 的 Spark 作業定義值](./media/apache-spark-job-definitions/create-scala-definition.png)

 6. 選取 [發佈] 以儲存 Apache Spark 作業定義。

     ![發佈 Scala 定義](./media/apache-spark-job-definitions/publish-scala-definition.png)


## <a name="create-an-apache-spark-job-definition-for-net-sparkc"></a>建立適用於 .NET Spark (C#) 的 Apache Spark 作業定義

在本節中，您會建立適用於 .NET Spark (C#) 的 Apache Spark 作業定義。
 1. 開啟 [Azure Synapse Studio](https://web.azuresynapse.net/)。

 2. 您可以移至[用來建立 Apache Spark 作業定義的範例檔案](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET)，以下載 **wordcount.zip** 和 **shakespear.txt**。 然後，將這些檔案上傳至 Azure 儲存體：按一下 [資料]、選取 [儲存體帳戶]，然後將相關檔案上傳到您的 ADLS Gen2 檔案系統。 如果您的檔案已在 Azure 儲存體中，請略過此步驟。 

     ![準備 Dotnet 結構](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 3. 按一下 [開發] 中樞，從左窗格中選取 [Spark 作業定義]，按一下[Spark 作業定義] 旁的 [...] 動作節點，然後在內容功能表中選取 [新增 Spark 作業定義]。

     ![建立 Dotnet 的新定義](./media/apache-spark-job-definitions/create-new-definition.png)

 4. 從 Apache Spark 作業定義主視窗的 [語言] 下拉式清單中，選取 [.NET Spark (C#/F#)]。

 5. 填入 Apache Spark 作業定義的資訊。 您可以複製範例資訊。
     |  屬性   | 描述   |  
     | ----- | ----- |  
     |作業定義名稱| 輸入 Spark 作業定義的名稱。 此名稱可隨時更新，直到作業定義發佈出去為止。 範例： `job definition sample`|
     |主要定義檔| 用於作業的主要檔案。 從儲存體中選取 .NET for Apache Spark 應用程式 (亦即，主要的可執行檔、包含使用者定義函式的 DLL，以及其他必要檔案) 所在的 ZIP 檔案。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。 範例： `abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/wordcount.zip`|
     |主要可執行檔| 主要定義 ZIP 檔案中的主要可執行檔。 範例： `WordCount`|
     |命令列引數| 作業的選擇性引數。 範例： `abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/shakespeare.txt abfss://sparkjob@storageaccountname.dfs.core.windows.net/dotnet/wordcount/result`|
     |參考檔案| 背景工作節點為了執行主要定義 ZIP 檔案中未包含的 .NET for Apache Spark 應用程式所需的其他檔案 (亦即，相依的 jar、其他的使用者定義函式 DLL，以及其他設定檔)。 您可以選取 [上傳檔案]，以將檔案上傳至儲存體帳戶。|
     |Spark 集區| 作業將會提交至選取的 Apache Spark 集區。|
     |Spark 版本| Apache Spark 集區正在執行的 Apache Spark 版本。|
     |執行程式| 要在指定 Apache Spark 集區中提供給作業使用的執行程式數目。|  
     |執行程式大小| 在指定 Apache Spark 集區中提供給作業使用的執行程式所能使用的核心和記憶體數目。|
     |驅動程式大小| 在指定 Apache Spark 集區中提供給作業使用的驅動程式所能使用的核心和記憶體數目。|

     ![設定適用於 Dotnet 的 Spark 作業定義值](./media/apache-spark-job-definitions/create-net-definition.png)

 6. 選取 [發佈] 以儲存 Apache Spark 作業定義。

      ![發佈 Dotnet 定義](./media/apache-spark-job-definitions/publish-net-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>以批次作業的形式提交 Apache Spark 作業定義

在建立 Apache Spark 作業定義後，您可以將其提交至 Apache Spark 集區。 請確定您是所要使用的 ADLS Gen2 檔案系統的儲存體 Blob 資料擁有者。 如果您還不是，則必須手動新增權限。

### <a name="scenario-1-submit-apache-spark-job-definition"></a>案例 1：提交 Apache Spark 作業定義
 1. 按一下 Apache Spark 作業定義以開啟其視窗。

      ![開啟要提交的 Spark 作業定義 ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. 按一下 [提交] 圖示將您的專案提交至選取的 Apache Spark 集區。 您可以按一下 [Spark 監視 URL] 索引標籤來查看 Apache Spark 應用程式的 LogQuery。

    ![按一下 [提交] 按鈕以提交 Spark 作業定義](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![[提交 Spark] 對話方塊](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>案例 2：檢視 Apache Spark 作業的執行進度

 1. 按一下 [監視器]，然後選取 [Spark 應用程式] 選項。 您可以找到已提交的 Apache Spark 應用程式。

     ![檢視 Spark 應用程式](./media/apache-spark-job-definitions/view-spark-application.png)

 2. 然後，按一下 Apache Spark 應用程式，隨即會顯示 [LogQuery] 視窗。 您可以從 [LogQuery] 檢視作業的執行進度。
     
     ![檢視 Spark 應用程式 LogQuery](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>案例 3：檢查輸出檔案

 1. 按一下 [資料]，然後選取 [儲存體帳戶]。 成功執行之後，便可以移至 ADLS Gen2 儲存體，然後檢查是否已產生輸出。

     ![檢視輸出檔案](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>將 Apache Spark 作業定義新增至管線

在本節中，您會將 Apache Spark 作業定義新增至管線。

 1. 開啟現有的 Apache Spark 作業定義。

 2. 按一下 Apache Spark 作業定義右上方的圖示，選擇 [現有的管線] 或 [新增管線]。 如需詳細資訊，您可以參考 [管線] 頁面。

     ![新增至管線](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![新增至管線](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>後續步驟

本教學課程示範如何使用 Azure Synapse Studio 來建立 Apache Spark 作業定義，然後將其提交至 Apache Spark 集區。 接下來，您可以使用 Azure Synapse Studio 來建立 Power BI 資料集和管理 Power BI 資料。

