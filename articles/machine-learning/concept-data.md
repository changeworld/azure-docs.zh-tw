---
title: 在雲端中保護資料存取
titleSuffix: Azure Machine Learning
description: 瞭解如何從 Azure Machine Learning 安全地連接到您的資料，以及如何使用資料集和資料存放區來進行 ML 工作。 資料存放區可以儲存 Azure Blob 的資料，Azure Data Lake Gen 1 & 2，SQL db，Databricks,.。。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/24/2020
ms.openlocfilehash: e41d00a31a0065e2923259cbb0bb36986cbb2ec8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995058"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Azure Machine Learning 中的安全資料存取

Azure Machine Learning 可讓您輕鬆地連接到雲端中的資料。  它提供基礎儲存體服務的抽象層，讓您可以安全地存取和使用您的資料，而不需要撰寫儲存類型特有的程式碼。 Azure Machine Learning 也提供下列資料功能：

*    資料歷程的版本控制和追蹤
*    資料標籤 
*    資料漂移監視
*    與 Pandas 和 Spark 資料框架的互通性

## <a name="data-workflow"></a>資料工作流程

當您準備好要使用雲端式儲存體解決方案中的資料時，我們建議您進行下列資料傳遞工作流程。 此工作流程假設您在 Azure 中的雲端式儲存體服務中有[azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)和資料。 

1. 建立[Azure Machine Learning 資料](#datastores)存放區，以將連線資訊儲存到您的 Azure 儲存體。

2. 從該資料存放區，建立[Azure Machine Learning 資料集](#datasets)以指向基礎儲存體中的特定檔案。 

3. 若要在您的機器學習實驗中使用該資料集，您可以
    1. 將它掛接到實驗的計算目標，以進行模型定型。

        **OR** 

    1. 直接在 Azure Machine Learning 解決方案（例如自動化機器學習（自動化 ML）實驗執行、機器學習管線或[Azure Machine Learning 設計](concept-designer.md)工具）中取用。

4. 針對您的模型輸出資料集建立[資料集監視器](#data-drift)，以偵測資料漂移。 

5. 如果偵測到資料漂移，請更新您的輸入資料集，並據以重新定型您的模型。

下圖提供此建議工作流程的視覺化示範。

![資料-概念-圖表](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>資料存放區

Azure Machine Learning 資料存放區會安全地將連線資訊保留在您的 Azure 儲存體中，因此您不需要在腳本中進行編碼。 [註冊並建立](how-to-access-data.md)資料存放區，即可輕鬆地連接到您的儲存體帳戶，並存取基礎 Azure 儲存體服務中的資料。 

Azure 中支援的雲端式儲存體服務，可註冊為數據存放區：

+ Azure Blob 容器
+ Azure 檔案共用
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ 適用於 PostgreSQL 的 Azure 資料庫
+ Databricks 檔案系統
+ 適用於 MySQL 的 Azure 資料庫

## <a name="datasets"></a>資料集

Azure Machine Learning 資料集是指向儲存體服務中資料的參考。 它們不是您的資料複本，因此不會產生額外的儲存成本。 若要與您在儲存體中的資料互動，請[建立資料集](how-to-create-register-datasets.md)，以將您的資料封裝到適用于機器學習工作的取用物件。 將資料集註冊到您的工作區，以在不同的實驗間共用和重複使用，而不會產生資料的複雜性

您可以透過資料存放區，從本機檔案、公用 url、 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)或 azure 儲存體服務建立資料集。 若要從記憶體中的 pandas 資料框架建立資料集，請將資料寫入本機檔案（例如 parquet），然後從該檔案建立資料集。  

我們支援2種類型的資料集： 
+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)會藉由剖析提供的檔案或檔案清單，以表格格式表示資料。 您可以將 TabularDataset 載入 Pandas 或 Spark 資料框架中，以進一步操作和清理。 如需您可以從中建立 TabularDatasets 之資料格式的完整清單，請參閱[TabularDatasetFactory 類別](https://aka.ms/tabulardataset-api-reference)。

+ [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)會參考您資料存放區或公用 url 中的單一或多個檔案。 您可以將 FileDatasets 所參考的檔案[下載或掛接](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets)至您的計算目標。

您可以在下列檔中找到其他資料集功能：

+ [版本和追蹤](how-to-version-track-datasets.md)資料集歷程。
+ [監視您](how-to-monitor-datasets.md)的資料集，以協助進行資料漂移偵測。    

## <a name="work-with-your-data"></a>使用您的資料

有了資料集，您可以透過與 Azure Machine Learning 功能的緊密整合，完成數個機器學習工作。 

+ 建立[資料標籤專案](#label)。
+ 訓練機器學習模型：
     + [自動化 ML 實驗](how-to-use-automated-ml-for-ml-models.md)
     + [設計](tutorial-designer-automobile-price-train-score.md#import-data)工具
     + [notebook](how-to-train-with-datasets.md)
     + [Azure Machine Learning 管線](how-to-create-your-first-pipeline.md)
+ 使用[機器學習管線](how-to-create-your-first-pipeline.md)中的[批次推斷](how-to-use-parallel-run-step.md)來存取資料集以進行評分。
+ 設定[資料漂移](#drift)偵測的資料集監視。

<a name="label"></a>

## <a name="data-labeling"></a>資料標籤

在機器學習專案中，該如何為大量資料加上標籤往往是令人頭痛的問題。 具有「電腦視覺」元件（例如影像分類或物件偵測）的人員通常需要數千個影像和對應的標籤。

Azure Machine Learning 可讓您在集中的位置建立、管理及監視標籤專案。 加上標籤專案有助於讓資料、標籤和小組成員協調一致，從而讓您更有效率地管理加上標籤工作。 目前支援的工作有影像分類 (多標籤或多類別)，以及使用週框方塊來進行的物體識別。

建立[資料標籤專案](how-to-create-labeling-projects.md)，並輸出要在機器學習實驗中使用的資料集。

<a name="drift"></a>

## <a name="data-drift"></a>資料漂移

在機器學習環境中，資料漂移是模型輸入資料的變更，會導致模型效能降低。 這是一段時間的其中一個主要原因模型精確度下降，因此監視資料漂移有助於偵測模型效能問題。

請參閱[建立資料集監視](how-to-monitor-datasets.md)一文，以深入瞭解如何針對資料集內的新資料偵測和警示資料漂移。

## <a name="next-steps"></a>後續步驟 

+ [使用下列步驟](how-to-create-register-datasets.md)，在 Azure Machine Learning Studio 或 Python SDK 中建立資料集。
+ 透過我們的[範例筆記本](https://aka.ms/dataset-tutorial)試用資料集定型範例。
+ 如需資料漂移的範例，請參閱此[資料漂移教學](https://aka.ms/datadrift-notebook)課程。
