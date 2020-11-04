---
title: 什麼是 Azure Machine Learning Studio？
description: Azure Machine Learning Studio 是 Azure Machine Learning 工作區的入口網站。 Studio 結合無程式碼和程式碼優先體驗建立了包容性資料科學平台。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: peterclu
ms.author: peterlu
ms.date: 08/24/2020
ms.openlocfilehash: 345c925d3b1350dfc2ea69d65d8d14688c6930b9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310162"
---
# <a name="what-is-azure-machine-learning-studio"></a>什麼是 Azure Machine Learning Studio？

在本文中，您將了解 [Azure Machine Learning](overview-what-is-azure-ml.md)中適用於資料科學家開發人員的 Azure Machine Learning Studio (Web 入口網站)。 Studio 在包容性資料科學平台中結合無程式碼和程式碼優先體驗。

在本文中，您將了解：
>[!div class="checklist"]
> - 如何在 Studio 中[撰寫機器學習專案](#author-machine-learning-projects)。
> - 如何[管理 Studio 中的資產和資源](#manage-assets-and-resources)。
> - [Azure Machine Learning Studio 與 ML Studio (傳統版)](#ml-studio-classic-vs-azure-machine-learning-studio) 之間的差異。


## <a name="author-machine-learning-projects"></a>撰寫機器學習專案

視專案類型和使用者體驗等級而定，Studio 可提供多種撰寫體驗。

+ **筆記本**

  在直接整合到 Studio 中的受控 [Jupyter Notebook 伺服器](how-to-run-jupyter-notebooks.md)中，撰寫並執行您自己的程式碼。 

+ **Azure Machine Learning 設計工具**

  使用設計工具來定型和部署機器學習模型，而不需撰寫任何程式碼。 拖放資料集和模組以建立 ML 管線。 試用[設計工具教學課程](tutorial-designer-automobile-price-train-score.md)。

    ![Azure Machine Learning 設計工具範例](media/concept-designer/designer-drag-and-drop.gif)

+ **自動化機器學習 UI**

  了解如何使用易於使用的介面建立[自動化 ML 實驗](tutorial-first-experiment-automated-ml.md)。 

  [![Azure Machine Learning Studio 導覽窗格](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)](./media/overview-what-is-azure-ml/azure-machine-learning-automated-ml-ui.jpg)

+ **資料標記**

    使用 [Azure Machine Learning 資料標記](how-to-create-labeling-projects.md)來有效率地協調資料標記專案。

## <a name="manage-assets-and-resources"></a>管理資產和資源

直接在您的瀏覽器中管理您的機器學習資產。 在 SDK 與 Studio 之間的相同工作區中共用資產，以提供連續而不中斷的體驗。 使用 Studio 管理下列項目：

- 模型
- 資料集
- 資料存放區
- 計算資源
- Notebooks
- 實驗
- 執行記錄檔
- Pipelines 
- 管線端點

即使您是經驗豐富的開發人員，Studio 也可以簡化您管理工作區資源的方式。

## <a name="ml-studio-classic-vs-azure-machine-learning-studio"></a>ML Studio (傳統版) 與 Azure Machine Learning Studio

**ML Studio (傳統版)** 發行於 2015 年，是我們的第一個拖放機器學習產生器。 此獨立服務只提供視覺體驗。 Studio (傳統版) 不會與 Azure Machine Learning 交互操作。

**Azure Machine Learning** 是獨立且現代化的服務，可提供完整的資料科學平台。 其支援程式碼優先和低程式碼體驗。

**Azure Machine Learning Studio** 是 Azure Machine Learning 中的 Web 入口網站，包含用於專案撰寫和資產管理的低程式碼和無程式碼選項。 

我們建議新的使用者選擇 **Azure Machine Learning** ，而不是 ML Studio (傳統版)，以取得最新的資料科學工具。

### <a name="feature-comparison"></a>功能比較

下表摘要說明 ML Studio (傳統) 與 Azure Machine Learning 之間的一些主要差異。

| 功能 | ML Studio (傳統) | Azure Machine Learning |
|---| --- | --- |
| 拖放介面 | 傳統體驗 | 更新體驗 - [Azure Machine Learning 設計工具](concept-designer.md)| 
| 程式碼 SDK | 不支援 | 與 [Azure Machine Learning Python](/python/api/overview/azure/ml/) 和 [R](tutorial-1st-r-experiment.md) SDK 完全整合 |
| 實驗 | 可調整規模 (有 10 GB 的定型資料限制) | 使用計算目標進行調整 |
| 定型計算目標 | 專屬計算目標，僅限 CPU 支援 | 各種可自訂的[訓練計算目標](concept-compute-target.md#train)。 包括 GPU 和 CPU 支援 | 
| 部署計算目標 | 專屬 Web 服務格式 (不可自訂) | 各種可自訂的[部署計算目標](concept-compute-target.md#deploy)。 包括 GPU 和 CPU 支援 |
| ML 管線 | 不支援 | 建立彈性的模組化[管線](concept-ml-pipelines.md)來自動化工作流程 |
| MLOps | 基本模型管理和部署；僅限 CPU 部署 | 實體版本控制 (模型、資料、工作流程)、工作流程自動化、與 CICD 工具整合、CPU 與 GPU 部署，[以及其他更多功能](concept-model-management-and-deployment.md) |
| 模型格式 | 專屬格式，僅限 Studio (傳統) | 視訓練作業類型而定的多種支援格式 |
| 自動化模型定型和超參數調整 |  不支援 | [支援](concept-automated-ml.md)。 程式碼優先和無程式碼選項。 | 
| 資料漂移偵測 | 不支援 | [支援](how-to-monitor-datasets.md) |
| 資料標記專案 | 不支援 | [支援](how-to-create-labeling-projects.md) |


## <a name="next-steps"></a>後續步驟

請造訪 [Studio](https://ml.azure.com)，或使用下列教學課程來探索不同的撰寫選項：  

- + [在您自己的開發環境中著手進行](tutorial-1st-experiment-sdk-setup-local.md)
  + [在計算執行個體上使用 Jupyter Notebook 來定型並部署模型](tutorial-1st-experiment-sdk-setup.md)
  + [使用自動化機器學習來定型和部署模型](tutorial-first-experiment-automated-ml.md)  
  + [使用設計工具來定型和部署模型](tutorial-designer-automobile-price-train-score.md)