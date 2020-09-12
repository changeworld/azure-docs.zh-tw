---
title: 轉換資料
description: 使用 Hadoop、Machine Learning 或 Azure Data Lake Analytics，在 Azure Data Factory 中轉換資料或處理資料。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 19b4ee621a3918a49b524c5e4515bddd5962e0f4
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89442780"
---
# <a name="transform-data-in-azure-data-factory"></a>Azure Data Factory 中的資料轉換

> [!div class="op_single_selector"]
> * [對應資料流程](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [Mapreduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight 串流](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Machine Learning](transform-data-using-machine-learning.md) 
> * [預存程序](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks 筆記本](transform-data-databricks-notebook.md)
> * [Databricks Jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET 自訂](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>概觀
本文說明 Azure Data Factory 中的資料轉換活動，您可以使用這些活動，將原始資料轉換和處理為大規模的預測和深入解析。 轉換活動會在計算環境中執行，例如 Azure Databricks 或 Azure HDInsight。 它會提供每個轉換活動的詳細資訊文章連結。

Data Factory 支援下列可個別或與其他活動鏈結而加入至 [管線](concepts-pipelines-activities.md) 的資料轉換活動。

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>使用資料流程在 Azure Data Factory 中原生轉換

### <a name="mapping-data-flows"></a>對應資料流程

對應資料流程是以視覺化方式設計 Azure Data Factory 中的資料轉換。 資料流程可讓資料工程師開發圖形化資料轉換邏輯，而不需要撰寫程式碼。 產生的資料流程會執行為使用相應放大 Spark 叢集 Azure Data Factory 管線內的活動。 您可以透過現有的 Data Factory 排程、控制、流程和監視功能來實際運作資料流程活動。 如需詳細資訊，請參閱 [對應資料流程](concepts-data-flow-overview.md)。

### <a name="wrangling-data-flows"></a>整頓資料流程

Azure Data Factory 中的整頓資料流程可讓您反復進行雲端規模的無程式碼資料準備。 整頓資料流程與 [Power Query Online](https://docs.microsoft.com/power-query/) 整合，讓 Power Query M 函式可供透過 spark 執行以雲端規模整頓的資料。 如需詳細資訊，請參閱 [整頓資料流程](wrangling-data-flow-overview.md)。

## <a name="external-transformations"></a>外部轉換

您也可以選擇手動撰寫程式碼，並自行管理外部計算環境。

### <a name="hdinsight-hive-activity"></a>HDInsight Hive 活動
Data Factory 管線中的 HDInsight Hive 活動會在您自己或隨選的 Windows/Linux 架構 HDInsight 叢集上執行 Hive 查詢。 如需此活動的詳細資料，請參閱 [Hive 活動](transform-data-using-hadoop-hive.md)一文。 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig 活動
Data Factory 管線中的 HDInsight Pig 活動會在您自己或隨選的 Windows/Linux 架構 HDInsight 叢集上執行 Pig 查詢。 如需此活動的詳細資訊，請參閱 [Pig 活動](transform-data-using-hadoop-pig.md)文章。 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce 活動
Data Factory 管線中的 HDInsight MapReduce 活動會在您自己或隨選的 Windows/Linux 架構 HDInsight 叢集上執行 MapReduce 程式。 如需此活動的詳細資料，請參閱 [MapReduce 活動](transform-data-using-hadoop-map-reduce.md)一文。

### <a name="hdinsight-streaming-activity"></a>HDInsight 串流活動
Data Factory 管線中的 HDInsight 串流活動會在您自己或隨選的 Windows/Linux 架構 HDInsight 叢集上執行 Hadoop 串流程式。 如需此活動的詳細資訊，請參閱 [HDInsight 串流活動](transform-data-using-hadoop-streaming.md) 。

### <a name="hdinsight-spark-activity"></a>HdInsight Spark 活動
Data Factory 管線中的 HDInsight Spark 活動會在您自己的 HDInsight 叢集上執行 Spark 程式。 如需詳細資訊，請參閱[從 Azure Data Factory 叫用 Spark 程式](transform-data-using-spark.md)。 

### <a name="machine-learning-activities"></a>Machine Learning 活動
Azure Data Factory 可讓您輕鬆地建立管線，使用已發佈的 Azure Machine Learning Web 服務進行預測性分析。 在 Azure Data Factory 管線中使用[批次執行活動](transform-data-using-machine-learning.md)，您可以叫用 Machine Learning Web 服務來對批次中的資料進行預測。

經過一段時間，必須使用新的輸入資料集重新訓練 Machine Learning 評分實驗中的預測模型。 完成重新訓練之後，您想要使用已重新訓練的 Machine Learning 模型來更新評分 Web 服務。 您可以使用[更新資源活動](update-machine-learning-models.md)，以新訓練的模型來更新 Web 服務。  

如需這些機器學習活動的詳細資料，請參閱 [使用 Machine Learning 活動](transform-data-using-machine-learning.md) 。 

### <a name="stored-procedure-activity"></a>預存程序活動
您可以在 Data Factory 管線中使用 SQL Server 預存程式活動，以叫用下列其中一個資料存放區中的預存程式： Azure SQL Database、Azure Synapse Analytics (先前的 SQL 資料倉儲) ，SQL Server 您的企業或 Azure VM 中的資料庫。 如需詳細資料，請參閱[預存程序活動](transform-data-using-stored-procedure.md)一文。  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL 活動
Data Lake Analytics U-SQL 活動會在 Azure Data Lake Analytics 叢集上執行 U-SQL 指令碼。 如需詳細資料，請參閱 [Data Analytics U-SQL 活動](transform-data-using-data-lake-analytics.md)一文。 

### <a name="databricks-notebook-activity"></a>Databricks Notebook 活動

Data Factory 管線中的 Azure Databricks 筆記本活動會在 Azure Databricks 工作區中執行 Databricks 筆記本。 Azure Databricks 是用於執行 Apache Spark 的受控平台。 請參閱[執行 Databricks Notebook 來轉換資料](transform-data-databricks-notebook.md)。

### <a name="databricks-jar-activity"></a>Databricks Jar 活動

Data Factory 管線中的 Azure Databricks Jar 活動會在 Azure Databricks 叢集中執行 Spark Jar 檔案。 Azure Databricks 是用於執行 Apache Spark 的受控平台。 請參閱[執行 Databricks Notebook 來轉換資料](transform-data-databricks-jar.md)。

### <a name="databricks-python-activity"></a>Databricks Python 活動

Data Factory 管線中的 Azure Databricks Python 活動會在 Azure Databricks 叢集中執行 Python 檔案。 Azure Databricks 是用於執行 Apache Spark 的受控平台。 請參閱[在 Azure Databricks 中執行 Python 活動來轉換資料](transform-data-databricks-python.md)。

### <a name="custom-activity"></a>自訂活動
如果您需要以 Data Factory 不支援的方法轉換資料，可以利用自己的資料處理邏輯建立自訂活動，然後在管線中使用活動。 您可以將自訂 .NET 活動設定為使用 Azure Batch 服務或 Azure HDInsight 叢集來執行。 如需詳細資訊請參閱 [使用自訂活動](transform-data-using-dotnet-custom-activity.md) 。 

您可以建立自訂活動，以便在已安裝 R 的 HDInsight 叢集上執行 R 指令碼。 請參閱 [使用 Azure Data Factory 執行 R 指令碼](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)。 

### <a name="compute-environments"></a>計算環境
您需要為計算環境建立連結服務，然後在定義轉換活動時使用該連結服務。 Data Factory 支援兩種類型的資計算環境。 

- **隨選**：在此情況下，運算環境完全由 Data Factory 管理。 Data Factory 服務會在工作提交前自動建立運算環境以處理資料，而在工作完成時予以移除。 您可以針對工作執行、叢集管理及啟動載入動作，設定和控制隨選計算環境的細微設定。 
- **攜帶您自己的裝置**：在此情況下，您可以註冊自己的運算環境 (例如 HDInsight 叢集)，做為 Data Factory 中的連結服務。 運算環境由您自行管理，而 Data Factory 會使用它來執行活動。 

如需了解 Data Factory 所支援的計算服務，請參閱 [計算連結服務](compute-linked-services.md) 一文。 

## <a name="next-steps"></a>接下來的步驟
如需使用轉換活動的範例，請參閱下列教學課程：[教學課程：使用 Spark 轉換資料](tutorial-transform-data-spark-powershell.md)
