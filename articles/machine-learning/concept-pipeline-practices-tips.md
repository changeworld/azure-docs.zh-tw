---
title: 反覆運算和進化機器學習管線
titleSuffix: Azure Machine Learning
description: 快速開發的模式、做法和秘訣
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 05/01/2020
ms.openlocfilehash: 2ea353469ed111eebb591aa6ba86c652683cc2f0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858188"
---
# <a name="iterating-and-evolving-machine-learning-pipelines"></a>反覆運算和進化機器學習管線

Azure Machine Learning 管線提供有效率的方式來模組化您的程式碼、重複使用結果，並將您的計算資源優化。 以下是使用管線的一些實用秘訣和做法。

## <a name="how-do-you-get-started-with-pipelines"></a>如何開始使用管線？

如果您是管線的新手，有數個選項可供您開始使用：

* 如果您瞭解如何閱讀並重新建立結構化程式，請參閱[使用 AZURE MACHINE LEARNING SDK 建立和執行機器學習管線](how-to-create-your-first-pipeline.md)一文是不錯的選擇 
* 如果您喜歡在 Jupyter 筆記本中以互動方式學習，在 Azure Machine Learning 管線中開發的管線[：消費者入門](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-getting-started.ipynb)筆記本可能適合您
* 如果您偏好程式碼優先的情況，複製[Azure Machine Learning 管線示範](https://github.com/microsoft/aml-pipelines-demo)存放庫會提供良好的起點

## <a name="how-do-you-modularize-pipeline-code"></a>如何模組化管線程式碼？ 

模組和`ModuleStep`類別讓您有絕佳的機會模組化您的 ML 程式碼。 不過，必須記住，在管線步驟之間移動遠比函式呼叫更昂貴。 您必須詢問的問題不太多「這些函數和資料在概念上與另一節中的不同？」 但是「我要把這些函數和資料分開發展嗎？」 或「這種計算成本高昂，我可以重複使用它的輸出嗎？」 如需詳細資訊，請參閱 thisn'tebook[如何建立模組、ModuleVersion，並在具有 ModuleStep 的管線中使用它們](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-how-to-use-modulestep.ipynb)。

如先前所述，將資料準備從定型中分離，通常是一個這樣的機會。 有時候資料準備工作既複雜又耗時，因此您可能會將程式分成個別的管線步驟。 其他機會包括訓練後測試和分析。 

## <a name="how-do-you-speed-pipeline-iteration"></a>如何加速管線反復專案？ 

快速反復執行管線的常見技巧包括： 

- 複製管線（製作複本）並快速重新執行管線
- 讓計算實例保持執行狀態，以避免啟動時間
- 設定資料和步驟以允許重複使用，可讓管線略過重新計算不變數據的情況

當您想要快速反復執行時，您可以複製管線、建立管線，然後重新執行管線。 另一個有用的技巧是，如果您保持計算暖，就不會產生加速新計算的成本。 如果您設定了允許重複使用執行結果的步驟，則重複執行會盡可能重複使用結果（當步驟中沒有任何變更時）。

## <a name="how-do-you-collaborate-using-ml-pipelines"></a>如何使用 ML 管線共同作業？ 

個別管線是自然線條，用於分割作業。 有多個開發人員或甚至多個小組都可以處理不同的步驟，只要在步驟之間流動的資料和引數經過同意即可。 

在開發期間，您可以從`PipelineRun`工作`StepRun`區中取出並執行結果、使用這些物件來下載最終和中繼輸出，並針對您自己的模組化工作使用這些構件。

## <a name="use-pipelines-to-test-techniques-in-isolation"></a>使用管線以隔離方式測試技術

真實世界的 ML 解決方案通常會牽涉到每個步驟的相當大自訂。 原始資料通常需要以某種方式進行準備：篩選、轉換和增強。 定型程式可能會有數個可能的架構，而對於深度學習，圖層大小和啟用功能可能會有許多變化。 即使使用一致的架構，超參數搜尋也可能產生顯著的 wins。

除了[AutoML](concept-automated-ml.md)和[自動化超參數搜尋](how-to-tune-hyperparameters.md)之類的工具，管線也可能是 A/B 測試解決方案的重要工具。 如果您有數個管線步驟的變體，則可以輕鬆地產生不同的回合，以嘗試其變化： 

```python
data_preparation_variants = [data1, data2, data3]
data_augmentation_variants = [aug1, aug2]
architecture_variants = [train1, train2, train3]

# Cartesian product
all_variants = np.array(np.meshgrid(data_preparation_variants,data_augmentation_variants,architecture_variants)).T.reshape(-1,3)

pipelines = [Pipeline(workspace=ws, steps=variant.tolist(), description=str(variant)) for variant in all_variants]

```

