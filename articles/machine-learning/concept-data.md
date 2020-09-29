---
title: 保護雲端中的資料存取
titleSuffix: Azure Machine Learning
description: 瞭解如何安全地從 Azure Machine Learning 連接到您的資料，以及如何使用資料集和資料存放區進行 ML 工作。 資料存放區可以儲存 Azure Blob 的資料，Azure Data Lake Gen 1 & 2、SQL db 和 Azure Databricks。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python
ms.openlocfilehash: a36c7076de0c4db64b67f4eba38de4daf4213bca
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446696"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Azure Machine Learning 中的安全資料存取

Azure Machine Learning 可讓您輕鬆地連接到雲端中的資料。  它提供基礎儲存體服務的抽象層，讓您可以安全地存取及使用資料，而不需要撰寫您的儲存體類型專屬的程式碼。 Azure Machine Learning 也提供下列資料功能：

*    與 Pandas 和 Spark 資料框架的互通性
*    資料歷程的版本控制和追蹤
*    資料標記 
*    資料漂移監視
    
## <a name="data-workflow"></a>資料工作流程

當您準備好在雲端式儲存體解決方案中使用資料時，我們建議使用下列資料傳遞工作流程。 此工作流程假設您在 Azure 中的雲端式儲存體服務中有 [azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) 和資料。 

1. 建立 [Azure Machine Learning 資料](#datastores) 存放區，以將連接資訊儲存到您的 Azure 儲存體。

2. 從該資料存放區，建立 [Azure Machine Learning 資料集](#datasets) ，以指向基礎儲存體中 () 的特定檔案。 

3. 若要在您的機器學習實驗中使用該資料集，您可以
    1. 將其掛接至實驗的計算目標，以進行模型定型。

        **OR** 

    1. 直接在 Azure Machine Learning 的解決方案中使用，例如自動化機器學習 (自動化 ML) 實驗回合、機器學習管線或 [Azure Machine Learning 設計](concept-designer.md)工具。

4. 為您的模型輸出資料集建立資料 [集監視器](#data-drift) ，以偵測資料漂移。 

5. 如果偵測到資料漂移，請更新您的輸入資料集，並據以重新定型您的模型。

下圖提供此建議工作流程的視覺化示範。

![圖表顯示 Azure 儲存體服務，該服務會流入資料存放區，以流入資料集。 此資料集會流入模型定型，而這些定型會流入資料的漂移，進而流動回資料集。](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>資料存放區

Azure Machine Learning 資料存放區會安全地將連線資訊保留在您的 Azure 儲存體中，因此您不需要在腳本中撰寫程式碼。 [註冊並建立資料](how-to-access-data.md) 存放區，以輕鬆地連接到您的儲存體帳戶，並存取基礎 Azure 儲存體服務中的資料。 

Azure 中支援的雲端式儲存體服務可註冊為數據存放區：

+ Azure Blob 容器
+ Azure 檔案共用
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ 適用於 PostgreSQL 的 Azure 資料庫
+ Databricks 檔案系統
+ 適用於 MySQL 的 Azure 資料庫

## <a name="datasets"></a>資料集

Azure Machine Learning 資料集不是您的資料複本。 藉由建立資料集，您可以在其儲存體服務中建立資料的參考，以及其中繼資料的複本。 

因為資料集會進行延遲的評估，且資料會保留在現有的位置，所以您

* 不會產生額外的儲存體成本。
* 不會有意外變更原始資料來源的風險。
* 改善 ML 工作流程的效能速度。

若要與儲存體中的資料互動，請 [建立資料集](how-to-create-register-datasets.md) ，以將資料封裝到機器學習工作的取用物件中。 將資料集註冊到您的工作區，以在不同的實驗之間共用和重複使用，而不需要資料內嵌複雜性。

您可以透過資料存放區，從本機檔案、公用 url、 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)或 Azure 儲存體服務建立資料集。 

有2種類型的資料集： 

+ [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py&preserve-view=true)會參考資料存放區或公用 url 中的單一或多個檔案。 如果您的資料已清理，而且準備好用於定型實驗，您可以將 FileDatasets 參考的檔案 [下載或掛接](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) 到您的計算目標。

+ [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true)藉由剖析提供的檔案或檔案清單，以表格格式代表資料。 您可以將 TabularDataset 載入 pandas 或 Spark 資料框架，以進一步操作和清理。 如需您可以從中建立 TabularDatasets 的完整資料格式清單，請參閱 [TabularDatasetFactory 類別](https://aka.ms/tabulardataset-api-reference)。

您可以在下列檔中找到其他資料集功能：

+ [版本和追蹤](how-to-version-track-datasets.md) 資料集歷程。
+ [監視您](how-to-monitor-datasets.md) 的資料集，以協助進行資料漂移偵測。    

## <a name="work-with-your-data"></a>使用您的資料

使用資料集，您可以透過與 Azure Machine Learning 功能的緊密整合，完成許多機器學習工作。 

+ 建立 [資料標記專案](#label)。
+ 訓練機器學習模型：
     + [自動化 ML 實驗](how-to-use-automated-ml-for-ml-models.md)
     + [設計](tutorial-designer-automobile-price-train-score.md#import-data)工具
     + [筆記本](how-to-train-with-datasets.md)
     + [Azure Machine Learning 管線](how-to-create-your-first-pipeline.md)
+ 存取資料集以在[機器學習管線](how-to-create-your-first-pipeline.md)中使用[批次推斷](how-to-use-parallel-run-step.md)進行評分。
+ 設定 [資料漂移](#drift) 偵測的資料集監視。

<a name="label"></a>

## <a name="data-labeling"></a>資料標記

在機器學習專案中，該如何為大量資料加上標籤往往是令人頭痛的問題。 具有電腦視覺元件（例如影像分類或物件偵測）的人，通常需要數千個影像和對應的標籤。

Azure Machine Learning 可讓您在集中的位置建立、管理及監視標籤專案。 加上標籤專案有助於讓資料、標籤和小組成員協調一致，從而讓您更有效率地管理加上標籤工作。 目前支援的工作有影像分類 (多標籤或多類別)，以及使用週框方塊來進行的物體識別。

建立 [資料標記專案](how-to-create-labeling-projects.md)，並輸出要用於機器學習實驗中的資料集。

<a name="drift"></a>

## <a name="data-drift"></a>資料漂移

在機器學習的內容中，資料漂移是模型輸入資料中導致模型效能降低的變更。 這是一段時間內的其中一個主要原因：模型精確度會降低，因此監視資料漂移有助於偵測模型效能問題。

請參閱 [建立資料集監視器](how-to-monitor-datasets.md) 的文章，以深入瞭解如何針對資料集內的新資料偵測和警示資料漂移。

## <a name="next-steps"></a>後續步驟 

+ [使用這些步驟](how-to-create-register-datasets.md)，在 Azure Machine Learning Studio 或 Python SDK 中建立資料集。
+ 使用我們的 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)試用資料集定型範例。
