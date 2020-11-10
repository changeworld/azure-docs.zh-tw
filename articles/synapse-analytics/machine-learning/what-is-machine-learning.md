---
title: Azure Synapse Analytics 中的機器學習
description: Azure Synapse Analytics 中的機器學習功能概觀。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 382ba871f95b3b36c3f819de8d582ba2c5dc358a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316078"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics-workspaces-preview"></a>Azure Synapse Analytics 中的機器學習功能 (工作區預覽)

[!INCLUDE [preview](../includes/note-preview.md)]

Azure Synapse Analytics 提供各種機器學習功能。 本文概述如何在 Azure Synapse 的內容中套用機器學習。

此概觀涵蓋從資料科學程序觀點，探討 Synapse 中與機器學習服務相關的不同功能。

您可能已熟悉一般資料科學程序的外觀。 這是一個大部分機器學習服務專案都遵循的知名程序。

大致來說，此程序包含下列步驟：
* (了解商務)
* 資料取得與認知
* 模型化
* 模型部署和評分

本文從資料科學程式觀點，說明不同分析引擎中的 Azure Synapse 機器學習功能。 針對資料科學程序中的每個步驟，會摘要說明可協助的 Azure Synapse 功能。

## <a name="azure-synapse-machine-learning-capabilities"></a>Azure Synapse 機器學習服務功能

### <a name="data-acquisition-and-understanding"></a>資料取得與認知

大部分的機器學習專案都牽涉到妥善建立的步驟，而其中一個步驟是存取和了解資料。

#### <a name="data-source-and-pipelines"></a>資料來源和管線

由於 [Azure Data Factory](/azure/data-factory/introduction) (這是 Azure Synapse 的原生整合部分)，有一組強大的工具可供資料內嵌和資料協調流程管線使用。 這可讓您輕鬆地建置資料管線以存取資料，並將資料轉換為可用於機器學習的格式。 [深入了解 Synapse 中的資料管線](/azure/data-factory/concepts-pipelines-activities?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。 

#### <a name="data-preparation-and-explorationvisualization"></a>資料準備和探索/視覺效果

機器學習程序的重要部分是透過探索和視覺效果來了解資料。

根據儲存資料的位置而定，Synapse 會提供一組不同的工具來探索並準備用於分析和機器學習。 開始使用資料探索最快速的方式之一，就是使用 Apache Spark 或直接透過 Data Lake 中的資料使用 Synapse SQL 無伺服器集區。

* [適用於 Azure Synapse 的 Apache Spark](../spark/apache-spark-overview.md) 提供了大規模轉換、準備及探索資料的功能。 這些 Spark 集區提供 PySpark/Python、Scala 和 .NET 等工具，以進行大規模的資料處理。 使用強大的視覺效果程式庫，可以增強資料探索體驗，以協助您更深入了解資料。 [深入了解如何使用 Spark 在 Synapse 中瀏覽及視覺化資料](../get-started-analyze-spark.md)。

* [Synapse SQL 無伺服器集區](../sql/on-demand-workspace-overview.md)提供一種方法，可透過 Data Lake 直接使用 TSQL 來探索資料。 Synapse SQL 無伺服器集區也會在 Synapse Studio 中提供一些內建的視覺效果。 [深入了解如何使用 Synapse SQL 無伺服器集區來瀏覽資料](../get-started-analyze-sql-on-demand.md)。

### <a name="modeling"></a>模型化

在 Azure Synapse 中，您可以使用 PySpark/Python、Scala 或 .NET 之類的工具，在 Apache Spark 集區上執行訓練機器學習模型。

#### <a name="train-models-on-spark-pools-with-mllib"></a>使用 MLlib 將 Spark 集區上的模型定型

您可以使用各種演算法和程式庫的協助將機器學習模型定型。 [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) 提供可調整的機器學習演算法，協助解決最傳統的機器學習問題。 [本教學課程](../spark/apache-spark-machine-learning-mllib-notebook.md)涵蓋如何使用 Synapse 中的 MLlib 來定型模型。

除了 MLlib 之外，也可以使用 [Scikit Learn](https://scikit-learn.org/stable/) 等熱門程式庫來開發模型。 如需如何在 Synapse Spark 集區上安裝程式庫的詳細資訊，請參閱[在 Azure Synapse Analytics 中管理 Apache Spark 的程式庫](../spark/apache-spark-azure-portal-add-libraries.md)。

#### <a name="train-models-with-azure-machine-learning-automl"></a>使用 Azure Machine Learning AutoML 將模型定型

另一種不需要事先熟悉機器學習即可將機器學習模型定型的方法，是使用 AutoML。 [AutoML](/azure/machine-learning/concept-automated-ml) 是一項功能，可自動定型一組機器學習模型，並可讓使用者根據特定計量來選取最佳模型。 由於與 Azure Synapse Notebooks 的 Azure Machine Learning 緊密整合，使用者可以利用傳遞 Azure Active Directory 驗證，輕鬆地在 Synapse 中運用 AutoML。  這表示您只需要指向您的 Azure Machine Learning 工作區，而不需要輸入任何認證。 以下是 [AutoML 教學課程](../spark/apache-spark-azure-machine-learning-tutorial.md)，會說明如何使用 Synapse Spark 集區上的 Azure Machine Learning AutoML 來定型模型。

### <a name="model-deployment-and-scoring"></a>模型部署和評分

已在 Azure Synapse 或外部 Azure Synapse 中定型的模型，可以輕鬆地用於批次評分。 目前在 Synapse 中，有兩種方式可執行批次評分。

* 您可以使用 Synapse SQL 集區中的 [TSQL PREDICT 函式](../sql-data-warehouse/sql-data-warehouse-predict.md)，在您的資料所在的位置執行預測。 這個功能強大且可擴充的函式可讓您擴充資料，而不需要將任何資料從資料倉儲中移出。 [Synapse Studio 中引進了新的引導式機器學習模型體驗](https://aka.ms/synapse-ml-ui)，您可以在其中從 Synapse SQL 集區中的 Azure Machine Learning 模型登錄部署 ONNX 模型，以使用 PREDICT 進行批次評分。

* Azure Synapse 中批次評分機器學習模型的另一個選項是利用 Azure Synapse 的 Apache Spark 集區。 視用來定型模型的程式庫而定，您可以使用程式碼體驗來執行批次評分。

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure Synapse Analytics](../get-started.md)
* [建立工作區](../get-started-create-workspace.md)
* [快速入門：在 Synapse 中建立新的 Azure Machine Learning 連結服務](quickstart-integrate-azure-machine-learning.md)
* [教學課程：機器學習模型評分精靈 - 專用 SQL 集區](tutorial-sql-pool-model-scoring-wizard.md)
