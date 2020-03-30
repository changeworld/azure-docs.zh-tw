---
title: Azure 機器學習中的資料
titleSuffix: Azure Machine Learning
description: 瞭解 Azure 機器學習如何安全地連接到資料，並將這些資料用於機器學習任務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/20/2020
ms.openlocfilehash: 982c9c9eadec4403c8116430e1e25092de99f1d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128482"
---
# <a name="data-access-in-azure-machine-learning"></a>Azure 機器學習中的資料訪問

Azure 機器學習使連接到雲中的資料變得容易。  它在基礎存儲服務上提供了一個抽象層，因此您可以安全地訪問和使用資料，而無需編寫特定于存儲類型的代碼。 Azure 機器學習還提供以下資料功能：

*    資料血統的個版本和跟蹤
*    資料標籤 
*    資料漂移監視
*    與熊貓和火花資料幀的互通性

## <a name="data-workflow"></a>資料工作流

當您準備好在基於雲的存儲解決方案中使用資料時，我們建議使用以下資料交付工作流。 此工作流假定 Azure 中的基於雲的存儲服務中具有[Azure 存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)和資料。 

1. 創建[Azure 機器學習資料存儲](#datastores)以存儲到 Azure 存儲的連接資訊。

2. 從該資料存儲中，創建[Azure 機器學習資料集](#datasets)以指向基礎存儲中的特定檔。 

3. 要在機器學習實驗中使用該資料集，可以
    1. 將其安裝到實驗的計算目標中，用於模型訓練。

        **或** 

    1. 直接在 Azure 機器學習解決方案中使用它，例如自動機器學習 （自動 ML） 實驗運行、機器學習管道或 Azure[機器學習設計器](concept-designer.md)。

4. 為模型輸出資料集創建[資料集監視器](#data-drift)，以檢測資料漂移。 

5. 如果檢測到資料漂移，請更新輸入資料集並相應地重新訓練模型。

下圖提供了此建議的工作流的視覺化演示。

![資料概念圖](./media/concept-data/data-concept-diagram.svg)

## <a name="datastores"></a>資料存儲

Azure 機器學習資料存儲安全地將連接資訊保留到 Azure 存儲，因此無需在腳本中對其進行編碼。 [註冊並創建資料存儲](how-to-access-data.md)，以便輕鬆連接到存儲帳戶，並訪問基礎 Azure 存儲服務中的資料。 

Azure 中支援的基於雲的存儲服務可以註冊為數據存儲：

+ Azure Blob 容器
+ Azure 檔案共用
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Azure SQL Database
+ 適用於 PostgreSQL 的 Azure 資料庫
+ Databricks 檔案系統
+ 適用於 MySQL 的 Azure 資料庫

## <a name="datasets"></a>資料集

Azure 機器學習資料集是指向存儲服務中資料的引用。 它們不是您的資料副本，因此不會產生額外的存儲成本。 要與存儲中的資料進行交互，[請創建資料集](how-to-create-register-datasets.md)，將資料打包到用於機器學習任務的可消耗物件中。 將資料集註冊到工作區，以便跨不同的實驗共用和重用資料集，而無需資料引入複雜性。

資料集可以通過資料存儲從本地檔、公共 URL、Azure[開放資料集](https://azure.microsoft.com/services/open-datasets/)或 Azure 存儲服務創建。 要從記憶體中的熊貓資料框創建資料集，請將資料寫入本地檔（如鑲木地板）並從該檔創建資料集。  

我們支援兩種類型的資料集： 
+ [表格資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)通過分析提供的檔或檔案清單來表示表格格式的資料。 您可以將表格資料集載入到熊貓或 Spark 資料幀中，以便進一步操作和清理。 有關可以從中創建表格資料集的完整資料格式清單，請參閱[表格資料集工廠類](https://aka.ms/tabulardataset-api-reference)。

+ [檔資料集](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py)參考資料存儲或公共 URL 中的單個或多個檔。 您可以將檔資料集引用[的檔下載或裝載](how-to-train-with-datasets.md#option-2--mount-files-to-a-remote-compute-target)到計算目標。

其他資料集功能可在以下文檔中找到：

+ [版本和跟蹤](how-to-version-track-datasets.md)資料集的血統。
+ [監視資料集](how-to-monitor-datasets.md)以説明進行資料漂移檢測。    

## <a name="work-with-your-data"></a>處理您的資料

借助資料集，您可以通過與 Azure 機器學習功能無縫集成來完成許多機器學習任務。 

+ 創建[資料標記專案](#label)。
+ 培訓機器學習模型：
     + [自動 ML 實驗](how-to-use-automated-ml-for-ml-models.md)
     + [設計師](tutorial-designer-automobile-price-train-score.md#import-data)
     + [筆記本](how-to-train-with-datasets.md)
     + [Azure 機器學習管道](how-to-create-your-first-pipeline.md)
+ 訪問資料集，用於在[機器學習管道](how-to-create-your-first-pipeline.md)中進行[批次處理推理](how-to-use-parallel-run-step.md)評分。
+ 設置資料集監視器以進行[資料漂移](#drift)檢測。

<a name="label"></a>

## <a name="data-labeling"></a>資料標籤

在機器學習專案中，該如何為大量資料加上標籤往往是令人頭痛的問題。 具有電腦視覺元件（如圖像分類或物件檢測）通常需要數千個圖像和相應的標籤。

Azure Machine Learning 可讓您在集中的位置建立、管理及監視標籤專案。 加上標籤專案有助於讓資料、標籤和小組成員協調一致，從而讓您更有效率地管理加上標籤工作。 目前支援的工作有影像分類 (多標籤或多類別)，以及使用週框方塊來進行的物體識別。

創建[資料標記專案](how-to-create-labeling-projects.md)，並輸出資料集以用於機器學習實驗。

<a name="drift"></a>

## <a name="data-drift"></a>資料漂移

在機器學習上下文中，資料漂移是模型輸入資料的變化，導致模型性能下降。 它是模型精度隨時間而降低的主要原因之一，因此監視資料漂移有助於檢測模型性能問題。

請參閱["創建資料集監視器](how-to-monitor-datasets.md)"一文，詳細瞭解如何檢測和警報資料在資料集中的新資料漂移。

## <a name="next-steps"></a>後續步驟 

+ [使用以下步驟](how-to-create-register-datasets.md)在 Azure 機器學習工作室或使用 Python SDK 創建資料集。
+ 使用示例[筆記本](https://aka.ms/dataset-tutorial)試用資料集訓練示例。
+ 有關資料漂移示例，請參閱此[資料漂移教程](https://aka.ms/datadrift-notebook)。
