---
title: 什麼是 ML Studio (傳統)
titleSuffix: Azure
description: Azure Machine Learning Studio (傳統) 是一種拖放工具，用來從已就緒可供使用的演算法與模組程式庫中快速建置模型。
services: machine-learning
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 10/17/2019
ms.openlocfilehash: f3be29dc222e0e9b98ed51d8f921fb9926b018ca
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314060"
---
# <a name="what-is-machine-learning-studio-classic"></a>什麼是 Machine Learning Studio (傳統)？

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Microsoft Azure Machine Learning Studio (傳統) 是一種共同作業的拖放工具，讓您可用來依據資料建置、測試及部署預測性分析解決方案。 Azure Machine Learning Studio (傳統) 會以 Web 服務方式發佈模型，讓自訂應用程式或 BI 工具 (例如 Excel) 都能夠很容易地使用。

Machine Learning Studio (傳統) 讓資料科學、預測性分析、雲端資源和您的資料能夠齊聚一堂。

## <a name="the-machine-learning-studio-classic--interactive-workspace"></a>Machine Learning Studio (傳統) 互動式工作區
若要開發預測分析模型，您通常會使用一或多個來源的資料，透過各種資料操作和統計函數來轉換和分析該資料，然後產生一組結果。 開發此類模型是一種反覆式過程。 隨著您修改各種函數及其參數，結果會不斷收斂，直到您對已訓練的有效模型感到滿意為止。

Azure Machine Learning Studio (傳統) 提供互動式的視覺化工作區，讓您輕鬆建置、測試和反覆運算預測分析模型。 您可以將「資料集」和分析「模組」拖放到互動式畫布，將它們連接在一起以構成「實驗」，然後在 Machine Learning Studio (傳統) 中執行。 若要反覆調整模型設計，請編輯實驗，儲存複本 (若需要)，然後重新提交。 當您準備好時，可以將您的「訓練實驗」轉換成「預測實驗」，然後發佈為「Web 服務」，讓其他人可以存取您的模型。

不需要設計程式，就能以視覺化方式連接資料集和模組來建構預測性分析模型。

![Azure Machine Learning Studio (傳統) 圖表：建立實驗、讀取許多來源的資料、寫入評分的資料及寫入模型。](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

<a name="compare"></a>
## <a name="how-does-machine-learning-studio-classic-differ-from-azure-machine-learning"></a>Machine Learning Studio (傳統) 與 Azure Machine Learning 有何不同？

[Azure Machine Learning](../overview-what-is-azure-ml.md) 同時提供 SDK **與** Azure Machine Learning 設計工具，可快速準備資料、定型及部署機器學習模型。 此設計工具會對 Studio (傳統) 提供類似的拖放體驗。 不過，不同於 Studio (傳統) 專屬的計算平台，此設計工具會使用您自己的計算資源，並與 Azure Machine Learning 完全整合。

以下將進行快速比較：

|| Machine Learning Studio (傳統) | Azure Machine Learning |
|---| --- | --- |
| 拖放介面 | 是 | 是 - [Azure Machine Learning 設計工具](../concept-designer.md) |
| 實驗 | 可調整規模 (有 10 GB 的定型資料限制) | 使用計算目標進行調整 |
| 適用於拖放介面的模組 | 多種 | 初始的熱門[模組](../algorithm-module-reference/module-reference.md)集合|
|定型計算目標| 專屬計算目標，僅限 CPU 支援| 支援 Azure Machine Learning 計算 (GPU 或 CPU) 與 Notebook VM。<br/>([SDK 中支援其他計算](../concept-compute-target.md#train))|
|推斷計算目標| 專屬 Web 服務格式 (不可自訂) |  Azure Kubernetes Service 和 AML Compute <br/>([SDK 中支援其他計算](../how-to-deploy-and-where.md)) |
| ML 管線 | 不支援 | 支援[管線](../concept-ml-pipelines.md) |
| MLOps | 基本模型管理和部署 | 可設定的部署：模型和管線版本設定與追蹤 |
| 模型格式 | 專屬格式，僅限 Studio (傳統) | 取決於定型作業類型的標準格式 |
|自動化模型定型和超參數調整 | 否 | 設計工具中尚未提供 <br/> ([SDK 和工作區登陸頁面中提供支援](../concept-automated-ml.md)) | 

利用[教學課程：使用設計工具預測汽車價格](../tutorial-designer-automobile-price-train-score.md)來試用設計工具

> [!NOTE]
> 在 Studio (傳統) 中建立的模型無法透過 Azure Machine Learning 進行部署或管理。 不過，可以透過 Azure Machine Learning 工作區來管理在設計工具中建立和部署的模型。

## <a name="download-the-machine-learning-studio-classic-overview-diagram"></a>下載 Machine Learning Studio (傳統) 概觀圖表
下載 **Microsoft Azure Machine Learning Studio (傳統) 功能概觀**圖表，並取得 Machine Learning Studio (傳統) 功能的高階檢視。 若要就近保留它，您可以將圖表列印於 Tabloid 大小 (11 x 17 英吋) 的紙張上。

**在這裡下載圖表：[Microsoft Azure Machine Learning Studio (傳統) 功能概觀](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Microsoft Azure Machine Learning Studio (傳統) 功能概觀](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Studio (傳統) 實驗的元件
實驗由資料集組成，資料集提供資料給分析模組，將模組連接起就能建構預測分析模型。 明確地說，有效的實驗有三個特性：

* 實驗至少有一個資料集和一個模組
* 資料集只能連接到模組
* 模組可以連接到資料集或其他模組
* 模組的所有輸入埠必須有資料流程的某些連線
* 必須設定每個模組的所有必要參數

您可以從頭建立實驗，或者使用現有的範例實驗做為範本。 如需詳細資訊，請參閱[複製範例實驗來建立新的機器學習服務實驗](sample-experiments.md)。

如需建立實驗的範例，請參閱[在 Azure Machine Learning Studio (傳統) 中建立簡易實驗](create-experiment.md)。

如需建立預測性分析解決方案的更完整逐步解說，請參閱[使用 Azure Machine Learning Studio (傳統) 開發預測性解決方案](tutorial-part1-credit-risk.md)。

### <a name="datasets"></a>資料集
資料集是指已上傳至 Machine Learning Studio (傳統)，因而可在模型化過程中使用的資料。 Machine Learning Studio (傳統) 隨附許多範例資料集供您實驗，您可以在需要時上傳更多資料集。 以下是隨附資料集的一些例子：

* **各種汽車的 MPG 資料** - 汽車的每加侖英里 (MPG) 值，以汽缸數、馬力等來識別。
* **乳癌資料** - 乳癌診斷資料。
* **森林火災資料** - 葡萄牙西北部的森林火災範圍

當您建置實驗時，可以從畫布左邊的資料集清單選擇。

如需 Machine Learning Studio (傳統) 隨附的範例資料集清單，請參閱[在 Azure Machine Learning Studio (傳統) 中使用範例資料集](use-sample-datasets.md)。

### <a name="modules"></a>模組
模組是指您在資料上可執行的演算法。 Azure Machine Learning Studio (傳統) 有許多模組，從資料輸入函數到訓練、評分和驗證程序都有。 以下是隨附模組的一些例子：

* [轉換成 ARFF][convert-to-arff] - 將 .NET 序列化資料集轉換為屬性關聯性檔案格式 (ARFF)。
* [計算基本統計資料][elementary-statistics] - 計算基本統計資料，例如平均值、標準差等。
* [線性迴歸][linear-regression] - 建立線上梯度下降線性迴歸模型。
* [評分模型][score-model] - 給訓練的分類或迴歸模型評分。

當您建置實驗時，可以從畫布左邊的可用模組清單中進行選擇。

模組可能有一組參數可用來設定模組的內部演算法。 當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的 [屬性]  窗格中。 您可以在此窗格中修改參數來調整模型。

如需瀏覽大型可用機器學習演算法程式庫的一些說明，請參閱[如何選擇適用於 Microsoft Azure Machine Learning Studio (傳統) 的演算法](algorithm-choice.md)。

## <a name="deploying-a-predictive-analytics-web-service"></a>部署預測性分析 Web 服務
當您的預測性分析模型已就緒時，即可從 Machine Learning Studio (傳統) 將它部署為 Web 服務。 如需此程序的詳細資訊，請參閱[部署 Azure Machine Learning Web 服務](deploy-a-machine-learning-web-service.md)。

## <a name="next-steps"></a>後續步驟
您可以使用[逐步快速入門](create-experiment.md)和[以範例建置](sample-experiments.md)，來了解預測性分析和機器學習的基本概念。

<!-- Module References -->
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
