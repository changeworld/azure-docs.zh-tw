---
title: 使用 PySpark 和 Scala 在 HDInsight Spark 上執行分析 - Team Data Science Process
description: 團隊資料科學流程的示例，這些流程在 Azure HDInsight Spark 上使用 PySpark 和 Scala。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864140"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>在 Azure 上使用 PySpark 和 Scala 的 HDInsight Spark 資料科學逐步解說

這些逐步解說會在 Azure Spark 叢集上使用 PySpark 和 Scala 來執行預測性分析。 其遵循 Team Data Science Process 中所述的步驟。 如需 Team Data Science Process 的概觀，請參閱 [Data Science Process](overview.md)。 如需 Spark on HDInsight 的概觀，請參閱 [Spark on HDInsight 簡介](../../hdinsight/spark/apache-spark-overview.md)。

執行團隊資料科學流程的其他資料科學演練按他們使用**的平臺**分組。 如需這些範例的列述，請參閱[執行 Team Data Science Process 的逐步解說](walkthroughs.md)。

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>在 Azure Spark 上使用 PySpark 來預測計程車小費

使用紐約的計程車資料[，Azure HDInsight 演練上的"使用 Spark"](spark-overview.md)可預測是否支付了小費以及預期金額的範圍。 本示例使用團隊資料科學流程，使用[Azure HDInsight Spark 群集](https://azure.microsoft.com/services/hdinsight/)來存儲、流覽和功能工程師資料，這些資料來自公開可用的紐約市計程車旅行和票價資料集。 此概述主題使用 HDInsight Spark 群集和 Jupyter PySpark 筆記本。 這些 Notebook 會示範如何瀏覽資料、建立和取用模型。 進階的資料探索和模型化 Notebook 顯示如何包括交叉驗證、超參數清除和模型評估。

### <a name="data-exploration-and-modeling-with-spark"></a>使用 Spark 進行資料探索和模型化 
遵循[使用 Spark MLlib 工具組來建立資料的二進位分類和迴歸模型](spark-data-exploration-modeling.md)主題的內容，來探索資料集，以及建立、評分、評估 Machine Learning 模型。

### <a name="model-consumption"></a>模型耗用量
若要瞭解如何評分本主題中所建立的分類和迴歸模型，請參閱[評分及評估 Spark 建置機器學習服務模型](spark-model-consumption.md)。

### <a name="cross-validation-and-hyperparameter-sweeping"></a>交叉驗證和超參數掃掠
有關如何使用交叉驗證和超參數掃描對模型進行訓練[的高級資料探索和建模](spark-advanced-data-exploration-modeling.md)。


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>在 Azure Spark 上使用 Scala 來預測計程車小費

在 Azure 演練[中使用 Spark](scala-walkthrough.md)會預測是否支付了小費以及預期支付的金額範圍。 它會說明如何使用 Scala 搭配 Spark 機器學習程式庫 (MLlib) 和 Azure HDInsight Spark 叢集上的 SparkML 套件，處理受監督的機器學習工作。 它引導您完成構成[資料科學過程](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)的任務：資料引入和探索、視覺化、功能工程、建模和模型消耗。 建立的模型包括羅吉斯和線性迴歸、隨機樹系和漸層停駐推進式決策樹。


## <a name="next-steps"></a>後續步驟

有關團隊資料科學流程的概述，請參閱[團隊資料科學流程概述](overview.md)。

有關團隊資料科學流程生命週期的討論，請參閱[團隊資料科學流程生命週期](lifecycle.md)。 此生命週期概述了專案在執行時通常遵循的步驟（從開始到結束）。 

