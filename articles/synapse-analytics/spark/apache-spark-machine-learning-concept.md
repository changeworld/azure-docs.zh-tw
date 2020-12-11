---
title: 使用 Apache Spark Machine Learning
description: 本文提供可透過 Azure Synapse Analytics 上的 Apache Spark 取得的機器學習和資料科學功能的概念性總覽。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: ca55186a53b228aa97cc82d33a09aa3ffe455eee
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97092001"
---
# <a name="machine-learning-with-apache-spark"></a>使用 Apache Spark 的機器學習

Azure Synapse Analytics 中的 Apache Spark 能以大量資料進行機器學習，讓您能夠從大量的結構化、非結構化和快速移動的資料中取得寶貴的見解。 

本節包含機器學習工作流程的總覽和教學課程，包括探索資料分析、特徵設計、模型定型、模型評分和部署。  

## <a name="synapse-runtime"></a>Synapse 執行時間 
Synapse 執行時間是最適合用於資料科學和機器學習的策劃環境。 Synapse 執行時間預設會提供一系列受歡迎的開放原始碼程式庫和 Azure Machine Learning SDK 中的組建。 Synapse 執行時間也包含許多外部程式庫，包括 PyTorch、Scikit-learn-學習、XGBoost 等等。

若要深入瞭解可用的程式庫和相關版本，請查看已發佈的 [Azure Synapse Analytics 運行](../spark/apache-spark-version-support.md)時間。

## <a name="exploratory-data-analysis"></a>探索式資料分析
使用 Azure Synapse Analytics 中的 Apache Spark 時，有各種內建選項可協助您將資料視覺化，包括 Synapse 筆記本圖表選項、存取常用的開放原始碼程式庫（例如以 seaborn 和 Matplotlib），以及與 Synapse SQL 和 Power BI 的整合。

查看有關如何 [使用 Azure Synapse 筆記本將資料視覺化](../spark/apache-spark-data-visualization.md)的文章，以深入瞭解資料視覺效果和資料分析選項。

## <a name="feature-engineering"></a>特徵設計
根據預設，Synapse 執行時間包含一組通常用於特徵設計的程式庫。 針對大型資料集，您可以使用 Spark SQL、MLlib 和 Koalas 來進行特徵工程。 針對較小的資料集，Numpy、Pandas 和 Scikit-learn 等協力廠商程式庫也會提供適用于這些案例的實用方法。

## <a name="train-models"></a>將模型定型
在 Azure Synapse Analytics 中使用 Azure Spark 定型機器學習模型時，有數個選項： Apache Spark MLlib、Azure Machine Learning，以及各種其他開放原始碼程式庫。 

查看有關如何 [在 Azure Synapse Analytics 中定型模型](../spark/apache-spark-machine-learning-training.md)的文章，以深入瞭解機器學習功能。

### <a name="sparkml-and-mllib"></a>SparkML 與 MLlib
Spark 的記憶體內分散式計算功能，使其成為機器學習和圖表計算中所使用反覆演算法的絕佳選擇。 ```spark.ml``` 提供一組統一的高階 Api，可協助使用者建立及調整機器學習管線。若要深入瞭解 ```spark.ml``` ，您可以造訪 [Apache Spark ML 程式設計指南](https://spark.apache.org/docs/1.2.2/ml-guide.html)。

### <a name="azure-machine-learning-automated-ml"></a>Azure Machine Learning 自動化 ML
[Azure Machine Learning 自動化 ML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) (自動化機器學習) 有助於將開發機器學習模型的程式自動化。 其可讓資料科學家、分析師和開發人員以高擴充性、效率和生產力來建置 ML 模型，同時維持模型品質。 執行 Azure Machine Learning 自動化 ML SDK 的元件會直接內建到 Synapse 執行時間。

### <a name="open-source-libraries"></a>開放原始碼程式庫
Azure Synapse Analytics 中的每個 Apache Spark 集區都隨附一組預先載入和熱門的機器學習程式庫。  有些相關的機器學習程式庫預設包含：

- [Scikit-learn-學習](https://scikit-learn.org/stable/index.html) 是傳統 ML 演算法最受歡迎的單一節點機器學習程式庫之一。 Scikit-learn-瞭解支援大部分的受監督和非監督式學習演算法，也可用於資料採礦和資料分析。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) 是一種熱門的機器學習程式庫，其中包含用於定型決策樹和隨機樹系的優化演算法。 
  
- [PyTorch](https://pytorch.org/)  & [Tensorflow](https://www.tensorflow.org/)是功能強大的 Python 深度學習程式庫。 在 Azure Synapse Analytics 的 Apache Spark 集區中，您可以使用這些程式庫，將集區的執行程式數目設定為零，以建立單一機器模型。 雖然 Apache Spark 在此設定下無法運作，但這是建立單一機器模型的簡單且符合成本效益的方式。

## <a name="track-model-development"></a>追蹤模型開發
[MLFlow](https://www.mlflow.org/) 是一個開放原始碼程式庫，可用於管理機器學習實驗的生命週期。 MLFlow 追蹤是 MLflow 的元件，可記錄及追蹤您的定型回合計量和模型成品。 若要深入瞭解如何透過 Azure Synapse Analytics 和 Azure Machine Learning 來使用 MLFlow 追蹤，請造訪本教學課程 [以瞭解如何使用 MLFlow](https://docs.microsoft.com/azure/machine-learning/how-to-use-mlflow)。

## <a name="model-scoring"></a>模型計分
模型計分（或推斷）是用來進行預測的階段。 若要使用 SparkML 或 MLLib 來評分模型，您可以利用原生 Spark 方法直接在 Spark 資料框架上執行推斷。 針對其他開放原始碼程式庫和模型類型，您也可以建立 Spark UDF，在大型資料集上相應放大推斷。 針對較小的資料集，您也可以使用程式庫所提供的原生模型推斷方法。

## <a name="register-and-serve-models"></a>註冊和服務模型
註冊模型可讓您在工作區中儲存、版本和追蹤模型的相關中繼資料。 完成模型的定型之後，您可以向 [Azure Machine Learning 模型](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#register-package-and-deploy-models-from-anywhere)登錄註冊模型。 註冊之後，ONNX 模型也可以用來充實儲存在專用 SQL 集區中 [的資料](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) 。

## <a name="next-steps"></a>後續步驟
若要開始使用 Azure Synapse Analytics 中的機器學習，請務必查看下列教學課程：
- [使用 Azure Synapse 筆記本分析資料](../spark/apache-spark-data-visualization-tutorial.md)

- [使用自動化 ML 訓練機器學習模型](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [使用 Apache Spark MLlib 訓練機器學習模型](../spark/apache-spark-machine-learning-mllib-notebook.md)
  