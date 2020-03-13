---
title: Azure Machine Learning 中的資料
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 如何與您的資料互動，以及如何在您的機器學習實驗中使用它。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 12/09/2019
ms.openlocfilehash: a2af1e87ce7b17183ae09fb02b2652a04f585e84
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270260"
---
# <a name="data-access-in-azure-machine-learning"></a>Azure Machine Learning 中的資料存取

在本文中，您將瞭解適用于機器學習工作的 Azure Machine Learning 資料管理和整合解決方案。 本文假設您已建立[azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)和[azure 儲存體服務](https://docs.microsoft.com/azure/storage/common/storage-introduction)。

當您準備好要使用儲存體中的資料時，我們建議您

1. 建立 Azure Machine Learning 資料存放區。
2. 從該資料存放區，建立 Azure Machine Learning 資料集。 
3. 請在您的機器學習實驗中使用該資料集，方法是 
    1. 將它裝載到實驗的計算目標以進行模型定型

        **OR** 

    1. 直接在 Azure Machine Learning 解決方案（例如自動化機器學習（自動化 ML）實驗執行、機器學習管線和[Azure Machine Learning 設計](concept-designer.md)工具）中使用它。
4. 針對您的模型輸出資料集建立資料集監視器，以偵測資料漂移。 
5. 如果偵測到資料漂移，請更新您的輸入資料集，並據以重新定型您的模型。

下圖提供此建議資料存取工作流程的視覺化示範。

![資料-概念-圖表](./media/concept-data/data-concept-diagram.svg)

## <a name="access-data-in-storage"></a>存取儲存體中的資料

若要存取您儲存體帳戶中的資料，Azure Machine Learning 提供資料存放區和資料集。 資料存放區回答問題：如何安全地連接到我的 Azure 儲存體中的資料？ 資料存放區提供您儲存體服務的抽象層。 這有助於安全性和輕鬆存取您的儲存體，因為連接資訊會保留在資料存放區中，而不會在腳本中公開。 

資料集回答問題：如何取得我資料存放區中的特定資料檔案？ 資料集指的是您想要用於機器學習實驗的特定檔案或基礎儲存體中的檔案。 資料存放區和資料集一起提供安全、可擴充且可重現的資料傳遞工作流程，供您的機器學習工作使用。

### <a name="datastores"></a>資料存放區

Azure Machine Learning 資料存放區是 Azure 儲存體服務的儲存體抽象概念。 [註冊並建立](how-to-access-data.md)資料存放區，即可輕鬆地連接到您的 azure 儲存體帳戶，並存取基礎 azure 儲存體服務中的資料。

可註冊為數據存放區的支援 Azure 儲存體服務：
+ Azure Blob 容器
+ Azure 檔案共用
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ 適用於 PostgreSQL 的 Azure 資料庫
+ Databricks 檔案系統
+ 適用於 MySQL 的 Azure 資料庫

### <a name="datasets"></a>資料集

[建立 Azure Machine Learning 資料集](how-to-create-register-datasets.md)以與資料存放區中的資料互動，並將您的資料封裝到適用于機器學習工作的可取用物件。 將資料集註冊到您的工作區，以在不同的實驗間共用和重複使用，而不會產生資料的複雜性

您可以從本機檔案、公用 url、 [Azure 開放資料集](#open)或資料存放區中的特定檔案來建立資料集。 若要從記憶體中的 pandas 資料框架建立資料集，請將資料寫入本機檔案（例如 csv），然後從該檔案建立資料集。 資料集不是資料的複本，而是指向儲存體服務中資料的參考，因此不會產生額外的儲存體成本。 

下圖顯示如果您沒有 Azure 儲存體服務，您可以直接從本機檔案、公用 url 或 Azure 開放資料集建立資料集。 這麼做會將您的資料集連接到使用實驗的[Azure Machine Learning 工作區](concept-workspace.md)自動建立的預設資料存放區。

![資料-概念-圖表](./media/concept-data/dataset-workflow.svg)

您可以在下列檔中找到其他資料集功能：

+ [版本和追蹤](how-to-version-track-datasets.md)資料集歷程。
+ [監視您](how-to-monitor-datasets.md)的資料集，以協助進行資料漂移偵測。
+  如需這兩種資料集類型的檔，請參閱下列內容：
    + [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)藉由剖析提供的檔案或檔案清單，以表格格式表示資料。 這可讓您將資料具體化為 Pandas 或 Spark 資料框架，以便進一步操作和清理。 如需您可以從中建立 TabularDatasets 的完整檔案清單，請參閱[TabularDatasetFactory 類別](https://aka.ms/tabulardataset-api-reference)。

    + [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)會參考您資料存放區或公用 url 中的單一或多個檔案。 藉由這個方法，您可以將選擇的檔案下載或掛接至您的計算目標，做為 FileDataset 物件。

## <a name="work-with-your-data"></a>使用您的資料

有了資料集，您可以透過與 Azure Machine Learning 功能的緊密整合，完成數個機器學習工作。 

+ [訓練機器學習模型](how-to-train-with-datasets.md)。
+ 使用中的資料集 
     + [自動化 ML 實驗](how-to-use-automated-ml-for-ml-models.md)
     + [設計](tutorial-designer-automobile-price-train-score.md#import-data)工具 
+ 使用[機器學習管線](how-to-create-your-first-pipeline.md)中的批次推斷來存取資料集以進行評分。
+ 建立[資料標籤專案](#label)。
+ 設定[資料漂移](#drift)偵測的資料集監視。

<a name="open"></a>

## <a name="azure-open-datasets"></a>Azure 開放資料集

[Azure 開放資料集](how-to-create-register-datasets.md#create-datasets-with-azure-open-datasets)是策劃的公用資料集，您可以使用這些公用資料集，將案例專有的功能新增至機器學習解決方案，以獲得更準確的模型。 開放資料集位於雲端上的 Microsoft Azure，並已整合到 Azure Machine Learning。 您也可以透過 API 存取資料集，並在其他產品 (例如 Power BI 和 Azure Data Factory) 中使用它們。

Azure 開放資料集包含天氣、人口普查、假日、公共安全和位置的公用網域資料，可協助您訓練機器學習服務模型並擴充預測性解決方案。 您也可以在 Azure 開放資料集上共用公用資料集。

<a name="label"></a>

## <a name="data-labeling"></a>資料標籤

在機器學習專案中，該如何為大量資料加上標籤往往是令人頭痛的問題。 具有「電腦視覺」元件（例如影像分類或物件偵測）的人員通常需要數千個影像和對應的標籤。

Azure Machine Learning 可讓您在集中的位置建立、管理及監視標籤專案。 加上標籤專案有助於讓資料、標籤和小組成員協調一致，從而讓您更有效率地管理加上標籤工作。 目前支援的工作有影像分類 (多標籤或多類別)，以及使用週框方塊來進行的物體識別。

+ 建立[資料標籤專案](how-to-create-labeling-projects.md)，並輸出要在機器學習實驗中使用的資料集。

<a name="drift"></a>

## <a name="data-drift"></a>資料漂移

在機器學習環境中，資料漂移是模型輸入資料的變更，會導致模型效能降低。 這是一段時間的其中一個主要原因模型精確度下降，因此監視資料漂移有助於偵測模型效能問題。
請參閱[建立資料集監視](how-to-monitor-datasets.md)一文，以深入瞭解如何針對資料集內的新資料偵測和警示資料漂移。

## <a name="next-steps"></a>後續步驟 

+ 在 Azure Machine Learning studio 或使用 Python SDK 建立資料集，請[使用下列步驟。](how-to-create-register-datasets.md)
+ 透過我們的[範例筆記本](https://aka.ms/dataset-tutorial)試用資料集定型範例。
+ 如需資料漂移的範例，請參閱此[資料漂移教學](https://aka.ms/datadrift-notebook)課程。
