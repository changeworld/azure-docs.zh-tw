---
title: 使用設計師構建 ML 模型
titleSuffix: Azure Machine Learning
description: 瞭解構成 Azure 機器學習設計器的術語、概念和工作流。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 11/12/2019
ms.openlocfilehash: 78a6e7fa8d030185f537136a3a2124d8bc59d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037635"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>什麼是 Azure Machine Learning 設計工具 (預覽)？ 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Azure 機器學習設計器允許您在互動式畫布上直觀地連接[資料集](#datasets)和[模組](#module)，以創建機器學習模型。 要瞭解如何開始與設計師合作，請參閱[教程：與設計師一起預測汽車價格](tutorial-designer-automobile-price-train-score.md)

![Azure Machine Learning 設計工具範例](./media/concept-designer/designer-drag-and-drop.gif)

設計器使用 Azure 機器學習[工作區](concept-workspace.md)來組織共用資源，例如：

+ [管線](#pipeline)
+ [資料集](#datasets)
+ [計算資源](#compute)
+ [已註冊的模型](concept-azure-machine-learning-architecture.md#models)
+ [已發佈的管道](#publish)
+ [即時端點](#deploy)

## <a name="model-training-and-deployment"></a>模型培訓和部署

設計器為您提供了用於構建、測試和部署機器學習模型的視覺化畫布。 通過設計師，您可以：

+ 將[資料集](#datasets)和[模組](#module)拖放到畫布上。
+ 將模組連接在一起以創建[管道拔模](#pipeline-draft)。
+ 使用 Azure 機器學習工作區中的計算資源提交[管道運行](#pipeline-run)。
+ 將**訓練管道**轉換為**推理管道**。
+ [將管道發佈到](#publish)REST**管道終結點**，以提交具有不同參數和資料集的新管道運行。
    + 發佈**訓練管道**以重用單個管道以訓練多個模型，同時更改參數和資料集。
    + 發佈**批次處理推理管道**，使用以前訓練的模型對新資料進行預測。
+ [將](#deploy)**即時推理管道**部署到即時終結點，以便即時預測新資料。

![用於設計器中訓練、批次處理推理和即時推理的工作流圖](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>管線

[管道](concept-azure-machine-learning-architecture.md#ml-pipelines)由資料集和分析模組組成，這些模組連接在一起。 管道有許多用途：您可以製作一個訓練單個模型的管道，也可以製作一個訓練多個模型的管道。 您可以創建即時或批量進行預測的管道，也可以創建僅清理資料的管道。 管道允許您重用工作並組織專案。

### <a name="pipeline-draft"></a>管道草稿

在設計器中編輯管道時，進度將另存為**管道草稿**。 您可以通過添加或刪除模組、配置計算目標、創建參數等隨時編輯管道草稿。

有效的管道具有以下特徵：

* 資料集只能連接到模組。
* 模組只能連接到資料集或其他模組。
* 模組的所有輸入埠都必須與資料流程具有某種連接。
* 必須設置每個模組的所有必需參數。

準備好運行管道草稿時，將提交管道運行。

### <a name="pipeline-run"></a>管線執行

每次運行管道時，管道的配置及其結果都會在工作區中存儲，因為**管道正在運行**。 您可以返回運行的任何管道以檢查它以進行故障排除或審核。 **克隆**管道運行以創建新的管道草稿供編輯。

管道運行被分組到[實驗中](concept-azure-machine-learning-architecture.md#experiments)來組織執行歷程記錄。 您可以為每個管道回合設定實驗。 

## <a name="datasets"></a>資料集

機器學習資料集便於訪問和使用資料。 設計器中包含許多示例資料集，可供您試用。 您可以根據需要[註冊](how-to-create-register-datasets.md)更多資料集。

## <a name="module"></a>模組

模組是指您在資料上可執行的演算法。 設計人員具有許多模組，從資料入口功能到培訓、評分和驗證過程。

模組可能有一組參數可用來設定模組的內部演算法。 當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的 [屬性]  窗格中。 您可以在此窗格中修改參數來調整模型。 您可以為設計器中的單個模組設置計算資源。 

![模組屬性](./media/concept-designer/properties.png)

有關在機器學習演算法庫中導航的一些説明，請參閱[演算法&模組參考概述](algorithm-module-reference/module-reference.md)

## <a name="compute-resources"></a><a name="compute"></a>計算資源

使用工作區的計算資源運行管道，並將部署的模型託管為即時終結點或管道終結點（用於批次處理推理）。 支援的計算目標包括：

| 計算目標 | 訓練 | 部署 |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Kubernetes Service | | ✓ |

計算目標附加到 Azure[機器學習工作區](concept-workspace.md)。 在[Azure 機器學習工作室（經典）](https://ml.azure.com)中，在工作區中管理計算目標。

## <a name="deploy"></a>部署

要執行即時推斷，必須將管道部署為**即時終結點**。 即時終結點在外部應用程式和評分模型之間創建介面。 對即時終結點的調用將預測結果即時返回給應用程式。 要調用即時終結點，請傳遞部署終結點時創建的 API 金鑰。 端點基於 REST，這是 Web 程式設計專案的熱門體系結構選擇。

即時終結點必須部署到 Azure 庫伯奈斯服務群集。

要瞭解如何部署模型，請參閱[教程：使用設計器部署機器學習模型](tutorial-designer-automobile-price-deploy.md)。

## <a name="publish"></a>發佈

還可以將管道發佈到**管道終結點**。 與即時終結點類似，管道終結點允許您使用 REST 調用提交從外部應用程式運行的新管道。 但是，您不能使用管道終結點即時發送或接收資料。

已發佈的管道是靈活的，它們可用於訓練或重新訓練模型、[執行批量推斷](how-to-run-batch-predictions-designer.md)、處理新資料等等。 您可以將多個管道發佈到單個管道終結點，並指定要運行的管道版本。

已發佈的管道將運行在每個模組的管道草稿中定義的計算資源上。

設計器創建與 SDK 相同的[已發佈管道](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py)物件。


## <a name="moving-from-the-visual-interface-to-the-designer"></a>從可視介面移動到設計器

視覺化介面（預覽）已更新，現在是 Azure 機器學習設計器（預覽版）。 設計器已重新構建為使用基於管道的後端，該後端與 Azure 機器學習的其他功能完全集成。 

由於這些更新，視覺化介面的某些概念和術語已更改或重命名。 有關最重要的概念更改，請參閱下表。 

| 設計師的概念 | 以前在視覺化介面中 |
| ---- |:----:|
| 管道草稿 | 實驗 |
| 即時終結點 | Web 服務 |

### <a name="migrating-to-the-designer"></a>遷移到設計器

您可以將現有的可視介面實驗和 Web 服務轉換為設計器中的管道和即時終結點。 使用以下步驟遷移可視介面資源：

[!INCLUDE [migrate from the visual interface](../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>後續步驟

* 通過教程瞭解預測分析和機器學習的基礎知識[：與設計師一起預測汽車價格](tutorial-designer-automobile-price-train-score.md)
* 瞭解如何修改現有[設計器示例](samples-designer.md)以使其適應您的需要。

