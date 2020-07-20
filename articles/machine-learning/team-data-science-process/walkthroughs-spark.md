---
title: 使用 PySpark 和 Scala 在 HDInsight Spark 上執行分析 - Team Data Science Process
description: Team 資料科學程式的範例，逐步解說如何在 Azure HDInsight Spark 上使用 PySpark 和 Scala。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75864140"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>在 Azure 上使用 PySpark 和 Scala 的 HDInsight Spark 資料科學逐步解說

這些逐步解說會在 Azure Spark 叢集上使用 PySpark 和 Scala 來執行預測性分析。 其遵循 Team Data Science Process 中所述的步驟。 如需 Team Data Science Process 的概觀，請參閱 [Data Science Process](overview.md)。 如需 Spark on HDInsight 的概觀，請參閱 [Spark on HDInsight 簡介](../../hdinsight/spark/apache-spark-overview.md)。

執行小組資料科學程式的其他資料科學逐步解說會依其使用的**平臺**分組。 如需這些範例的列述，請參閱[執行 Team Data Science Process 的逐步解說](walkthroughs.md)。

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>在 Azure Spark 上使用 PySpark 來預測計程車小費

使用紐約計程車資料，在[Azure HDInsight 上使用 Spark](spark-overview.md)逐步解說會預測秘訣是否已付費，以及預期的數量範圍。 這個範例會在使用[Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/)叢集的案例中使用 Team 資料科學程式，以便從公開可用的 NYC 計程車行程和費用資料集儲存、探索和特徵工程設計資料。 本總覽主題使用 HDInsight Spark 叢集和 Jupyter PySpark 筆記本。 這些 Notebook 會示範如何瀏覽資料、建立和取用模型。 進階的資料探索和模型化 Notebook 顯示如何包括交叉驗證、超參數清除和模型評估。

### <a name="data-exploration-and-modeling-with-spark"></a>使用 Spark 進行資料探索和模型化 
遵循[使用 Spark MLlib 工具組來建立資料的二進位分類和迴歸模型](spark-data-exploration-modeling.md)主題的內容，來探索資料集，以及建立、評分、評估 Machine Learning 模型。

### <a name="model-consumption"></a>模型耗用量
若要瞭解如何評分本主題中所建立的分類和迴歸模型，請參閱[評分及評估 Spark 建置機器學習服務模型](spark-model-consumption.md)。

### <a name="cross-validation-and-hyperparameter-sweeping"></a>交叉驗證和超參數掃掠
如需如何使用交叉驗證和超參數清除來定型模型的方式，請參閱[使用 Spark 的先進資料探索和](spark-advanced-data-exploration-modeling.md)模型化。


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>在 Azure Spark 上使用 Scala 來預測計程車小費

在[Azure 上使用 Scala 與 Spark](scala-walkthrough.md)逐步解說會預測秘訣是否已付費，以及預期要支付的金額範圍。 它會說明如何使用 Scala 搭配 Spark 機器學習程式庫 (MLlib) 和 Azure HDInsight Spark 叢集上的 SparkML 套件，處理受監督的機器學習工作。 它會逐步引導您進行構成[資料科學](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)程式的工作：資料內嵌和探索、視覺化、特徵設計、模型化和模型耗用量。 建立的模型包括羅吉斯和線性迴歸、隨機樹系和漸層停駐推進式決策樹。


## <a name="next-steps"></a>後續步驟

如需 Team 資料科學程式的總覽，請參閱[Team 資料科學流程總覽](overview.md)。

如需小組資料科學程式生命週期的討論，請參閱[Team 資料科學程式生命週期](lifecycle.md)。 此生命週期概述從開始到完成的步驟，專案通常會在執行時遵循。 

