---
title: 使用 Apache Spark 將機器學習模型定型
description: 使用 Azure Synapse Analytics 中的 Apache Spark 來定型機器學習模型
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116801"
---
# <a name="train-machine-learning-models"></a>訓練機器學習模型
Azure Synapse Analytics 中的 Apache Spark 能以大量資料進行機器學習，讓您能夠從大量的結構化、非結構化和快速移動的資料中取得寶貴的見解。 在 Azure Synapse Analytics 中使用 Azure Spark 定型機器學習模型時，有數個選項： Apache Spark MLlib、Azure Machine Learning，以及各種其他開放原始碼程式庫。 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML 和 MLlib
Azure Synapse Analytics 中的 Apache Spark 是 Microsoft 在雲端中的其中一種 Apache Spark 實作。 它提供統一、開放原始碼的平行資料處理架構，可支援記憶體內部處理，以大幅提升大型資料分析。 Spark 處理引擎是專為速度、易用性及精密分析打造的產品。 Spark 的記憶體內分散式計算功能，使其成為機器學習和圖表計算中所使用反覆演算法的絕佳選擇。 

有兩個可將演算法模型化功能導入此分散式環境的可調整機器學習服務程式庫：MLlib 與 SparkML。 MLlib 包含建置在 RDD 上的原始 API。 SparkML 是較新的套件，可提供建置在 DataFrames 上的較高階 API 來建構 ML 管線。 SparkML 尚未支援 MLlib 的所有功能，但會將 MLlib 取代為 Spark 的標準機器學習程式庫。

> [!NOTE]
> 
> 您可以遵循本 [教學](../spark/apache-spark-azure-machine-learning-tutorial.md)課程，深入瞭解如何建立 SparkML 模型。

## <a name="popular-libraries"></a>熱門程式庫
Azure Synapse Analytics 中的每個 Apache Spark 集區都隨附一組預先載入和熱門的機器學習程式庫。 這些程式庫提供可重複使用的程式碼，您可能會想要將它包含在程式或專案中。 有些相關的機器學習程式庫預設包含：
- [Scikit-learn-學習](https://scikit-learn.org/stable/index.html) 是傳統 ML 演算法最受歡迎的單一節點機器學習程式庫之一。 Scikit-learn-瞭解支援大部分的受監督和非監督式學習演算法，也可用於資料採礦和資料分析。
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) 是一種熱門的機器學習程式庫，其中包含用於定型決策樹和隨機樹系的優化演算法。 
  
- [PyTorch](https://pytorch.org/)  & [Tensorflow](https://www.tensorflow.org/)是功能強大的 Python 深度學習程式庫。 在 Azure Synapse Analytics 的 Apache Spark 集區中，您可以使用這些程式庫，將集區的執行程式數目設定為零，以建立單一機器模型。 雖然 Apache Spark 在此設定下無法運作，但這是建立單一機器模型的簡單且符合成本效益的方式。

您可以藉由查看已發佈的 [Azure Synapse Analytics 運行](../spark/apache-spark-version-support.md)時間，深入瞭解可用的程式庫和相關版本。

## <a name="mmlspark"></a>MMLSpark
適用於 Apache Spark 的 Microsoft Machine Learning 程式庫為 [MMLSpark](https://github.com/Azure/mmlspark)。 此程式庫的設計目的是要讓資料科學家在 Spark 上更具生產力、提高實驗率，以及在大型資料集上運用最先進的機器學習技術，包括深度學習。 

建立可調整的 ML 模型時 (例如，編製索引字串)，MMLSpark 會在 SparkML 的低階 API 上提供一個層級，將資料強制轉型成機器學習服務演算法預期的版面配置，並組合功能向量。 MMLSpark 程式庫簡化了這些流程，以及在 PySpark 中建立模型的常見工作。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning 中的自動化 ML 
Azure Machine Learning 是一個雲端式環境，可讓您定型、部署、自動化、管理和追蹤機器學習模型。 Azure Machine Learning 中的自動化 ML 接受定型資料和設定設定，並自動逐一查看不同功能正規化/標準化方法、模型和超參數設定的組合，以達到最佳模型。 

在 Azure Synapse Analytics 中使用自動化 ML 時，您可以利用不同服務之間的深層整合，以簡化驗證 & 模型定型。 

> [!NOTE]
> 
> 您可以遵循本 [教學](./spark/../apache-spark-azure-machine-learning-tutorial.md)課程，深入瞭解如何建立 AZURE MACHINE LEARNING 自動化 ML 實驗。

## <a name="azure-cognitive-services"></a>Azure 認知服務
[Azure 認知服務](../../cognitive-services/what-are-cognitive-services.md) 提供機器學習服務功能，可解決一般問題，例如分析情緒情感的文字，或分析影像以辨識物件或臉部。 您不需要具備機器學習或資料科學的專門知識，即可使用這些服務。 認知服務會在機器學習解決方案中提供部分或所有元件：資料、演算法和定型的模型。 這些服務的目的是要要求您資料的一般知識，而不需要使用機器學習或資料科學的經驗。 您可以在 Azure Synapse Analytics 內自動利用這些預先定型的認知服務。

## <a name="next-steps"></a>後續步驟
本文概述在 Azure Synapse Analytics 的 Apache Spark 集區中訓練機器學習模型的各種選項。 您可以遵循下列教學課程來深入瞭解模型定型：

- 使用 Azure Machine Learning 和 Azure Synapse Analytics 執行自動化 ML 實驗：[自動化 Ml 教學](../spark/apache-spark-azure-machine-learning-tutorial.md)課程 
- 執行 SparkML 實驗： [Apache SparkML 教學](../spark/apache-spark-machine-learning-mllib-notebook.md)課程
- 查看預設程式庫： [Azure Synapse Analytics 運行](../spark/apache-spark-version-support.md)時間