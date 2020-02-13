---
title: Azure Machine Learning 與Machine Learning Studio (傳統)
description: Azure Machine Learning 與 Machine Learning Studio (傳統) 有何不同
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/10/2020
ms.locfileid: "76311456"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Azure Machine Learning 與 Machine Learning Studio (傳統) 有何不同

此文章會將 Azure Machine Learning 的特性、功能及介面與 Machine Learning Studio (傳統) 進行比較。 

## <a name="about-machine-learning-studio-classic"></a>關於 Machine Learning Studio (傳統)
[Machine Learning Studio (傳統)](studio/what-is-ml-studio.md) 是可進行共同作業的拖放式視覺化工作區，您可於該處建置、測試及部署機器學習解決方案，而無須撰寫任何程式碼。 它會使用預先建置且預先設定的機器學習演算法和資料處理模組，以及專屬計算平台。

## <a name="about-azure-machine-learning"></a>關於 Azure Machine Learning

同時，[Azure Machine Learning](overview-what-is-azure-ml.md) 會提供稱為設計工具 (預覽) 的 Web 介面**與**數個 SDK 和 CLI，可快速準備資料、定型及部署機器學習模型。 利用 Azure Machine Learning，您就能進行規模調整、取得多個架構支援、進階的 ML 功能 (例如自動化機器學習) 及管線支援。

Azure Machine Learning 設計工具會對 Studio (傳統) 提供類似的拖放體驗。 不過，不同於 Studio (傳統) 專屬的計算平台，此設計工具會使用您自己的計算資源、可調整規模，並與 Azure Machine Learning 完全整合。  

> [!TIP]
> 我們鼓勵目前正在使用或評估 Machine Learning Studio (傳統) 的客戶嘗試使用 [Azure Machine Learning 設計工具](https://docs.microsoft.com/azure/machine-learning/concept-designer) \(部分機器翻譯\) (預覽)，這會提供拖放 ML 模組__加上__延展性、版本控制及企業安全性。

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>比較：Azure Machine Learning 與Machine Learning Studio (傳統)

以下是快速的比較。

||  Azure Machine Learning 設計工具|Studio (傳統) |
|---| --- | --- |
||設計工具目前處於預覽階段，Azure Machine Learning 則為 GA|正式上市 (GA) | 
|拖放介面| 是 | 是|
|實驗| 使用計算目標進行調整|調整 (有 10 GB 的訓練資料限制) | 
|介面適用的模組| [許多熱門模組](algorithm-module-reference/module-reference.md) | 多種 |
|定型計算目標| AML 計算 (GPU/CPU)|專屬計算目標，僅限 CPU|
|推斷計算目標| 適用於即時推斷的 Azure Kubernetes Service <br/>適用於批次推斷的 AML Compute|專屬 Web 服務格式 (不可自訂) | 
|ML 管線| 管線撰寫 <br/> 已發佈的管線 <br/> 管線端點 <br/> [深入了解 ML 管線](concept-ml-pipelines.md)|不支援 | 
|ML Ops| 可設定的部署、模型和管線版本控制|基本模型管理和部署 | 
|模型| 標準格式 (可能會因為定型作業不同而有所差異)|專屬且非可攜式格式。| 
|自動化模型定型|設計工具中尚未提供，但能透過介面和 SDK 取得。| 否 | 

## <a name="get-started-with-azure-machine-learning"></a>開始使用 Azure Machine Learning

下列資源可協助您開始使用 Azure Machine Learning

- 請參閱 [Azure Machine Learning 概觀](tutorial-first-experiment-automated-ml.md)。 

- [建立您的第一個設計工具管線](tutorial-designer-automobile-price-train-score.md)來預測汽車價格。

![Azure Machine Learning 設計工具範例](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>後續步驟

除了設計工具中的拖放功能之外，Azure Machine Learning 還有其他可用的工具：  
  + [使用 Python 筆記本來定型和部署 ML 模型](tutorial-1st-experiment-sdk-setup.md)
  + [使用 R Markdown 來定型和部署 ML 模型](tutorial-1st-r-experiment.md) 
  + [使用自動化機器學習來定型和部署 ML 模型](tutorial-designer-automobile-price-train-score.md) 
  + [使用機器學習 CLI 來定型和部署模型](tutorial-train-deploy-model-cli.md)

