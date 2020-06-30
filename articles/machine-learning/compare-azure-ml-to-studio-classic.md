---
title: Azure Machine Learning 與Machine Learning Studio (傳統)
description: Azure Machine Learning 和 Machine Learning Studio (傳統) 之間有何差異？
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 03/25/2020
ms.openlocfilehash: 065fd166fb2ce82a3338322d55ef1ee5886ac56b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210366"
---
# <a name="azure-machine-learning-vs-machine-learning-studio-classic"></a>Azure Machine Learning 與 Machine Learning Studio (傳統)

在本文中，您將了解 Azure Machine Learning 和 Machine Learning Studio (傳統) 之間的差異。 

Azure Machine Learning 提供 Python 和 R SDK，**以及**「拖放」設計工具來建立和部署機器學習模型。 Studio (傳統) 只提供獨立的拖放體驗。

我們建議新使用者選擇 Azure Machine Learning，以取得最廣泛的尖端機器學習工具。

## <a name="quick-comparison"></a>快速比較

下表摘要說明 Azure Machine Learning 與 Studio (傳統) 之間的一些主要差異：

| | Machine Learning Studio (傳統) | Azure Machine Learning |
|---| --- | --- |
| 拖放介面 | 支援 | 支援 - [Azure Machine Learning 設計工具 (預覽)](concept-designer.md) <br/>(需要企業工作區) | 
| 實驗 | 可調整規模 (有 10 GB 的定型資料限制) | 使用計算目標進行調整 |
| 定型計算目標 | 專屬計算目標，僅限 CPU 支援 | 各種可自訂的[訓練計算目標](concept-compute-target.md#train)。 包括 GPU 和 CPU 支援 | 
| 部署計算目標 | 專屬 Web 服務格式 (不可自訂) | 各種可自訂的[部署計算目標](concept-compute-target.md#deploy)。 包括 GPU 和 CPU 支援 |
| ML 管線 | 不支援 | 建立彈性的模組化[管線](concept-ml-pipelines.md)來自動化工作流程 |
| MLOps | 基本模型管理和部署 | 實體版本控制 (模型、資料、工作流程)、工作流程自動化、與 CICD 工具整合，[以及其他更多功能](concept-model-management-and-deployment.md) |
| 模型格式 | 專屬格式，僅限 Studio (傳統) | 視訓練作業類型而定的多種支援格式 |
| 自動化模型定型和超參數調整 |  不支援 | [可在 SDK 和視覺化工作區中提供支援](concept-automated-ml.md) | 
| 資料漂移偵測 | 不支援 | [可在 SDK 和視覺化工作區中提供支援](how-to-monitor-datasets.md) |


## <a name="migrate-from-machine-learning-studio-classic"></a>從 Machine Learning Studio (傳統) 遷移

目前沒有任何方法可將 Studio (傳統) 資產遷移至 Azure Machine Learning 設計工具 (預覽)。 目前的 Studio (傳統) 使用者可以繼續使用其機器學習資產。 不過，我們鼓勵所有使用者考慮使用設計工具，其提供您熟悉的拖放功能和**加上**可擴縮性、版本控制和企業安全性的改良工作流程。

## <a name="get-started-with-azure-machine-learning"></a>開始使用 Azure Machine Learning

下列資源可協助您開始使用 Azure Machine Learning。 

- 請參閱 [Azure Machine Learning 概觀](overview-what-is-azure-ml.md)。

- [使用 Python SDK 建立您的第一個實驗](tutorial-1st-experiment-sdk-setup.md)。

- [建立您的第一個設計工具管線](tutorial-designer-automobile-price-train-score.md)來預測汽車價格。

![Azure Machine Learning 設計工具範例](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>後續步驟

除了設計工具中的拖放功能之外，Azure Machine Learning 還有其他可用的工具：  
  + [使用 Python 筆記本來定型和部署 ML 模型](tutorial-1st-experiment-sdk-setup.md)
  + [使用 R Markdown 來定型和部署 ML 模型](tutorial-1st-r-experiment.md) 
  + [使用自動化機器學習來定型和部署 ML 模型](tutorial-first-experiment-automated-ml.md)  
  + [使用機器學習 CLI 來定型和部署模型](tutorial-train-deploy-model-cli.md)

