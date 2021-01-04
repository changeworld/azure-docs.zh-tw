---
title: 教學課程：使用自動化 ML 建立預測模型 (第 1 部分，共 2 部分)
titleSuffix: Azure Machine Learning
description: 了解如何建置和部署自動化 ML 模型，讓您可以使用最佳模型來預測 Microsoft Power BI 中的結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 897f493edf6ccdebb25c201e8e4f9babfb0754c5
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370646"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-using-automated-machine-learning-part-1-of-2"></a>教學課程：Power BI 整合 - 使用自動化機器學習建立預測模型 (第 1 部分，共 2 部分)

在本教學課程的第一個部分中，您會使用 Azure Machine Learning Studio 中自動化的機器學習來定型和部署預測性機器學習模型。  在第 2 部分中，您將使用表現最佳的模型來預測 Microsoft Power BI 中的結果。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 計算叢集
> * 建立資料集
> * 建立自動化 ML 執行
> * 將最佳模型部署至即時評分端點


有三種不同的方式可用來建立及部署您將在 Power BI 中使用的模型。  本文將說明選項 C：在 Studio 中使用自動化 ML 定型和部署模型。  此選項會提供無程式碼撰寫體驗，可將資料準備和模型訓練完全自動化。 

您也可以改為使用：

* [選項 A：使用 Notebooks 定型並部署模型](tutorial-power-bi-custom-model.md) - 程式碼優先撰寫體驗 (使用 Azure Machine Learning Studio 中裝載的 Jupyter Notebooks)。
* [選項 B：使用設計工具來訓練和部署模型](tutorial-power-bi-designer-model.md) - 使用設計工具的低程式碼撰寫體驗 (拖放使用者介面)。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 ([可使用免費試用版](https://aka.ms/AMLFree))。 
- Azure Machine Learning 工作區。 如果您還沒有工作區，請遵循[如何建立 Azure Machine Learning 工作區](./how-to-manage-workspace.md#create-a-workspace)。

## <a name="create-compute-cluster"></a>建立計算叢集

自動化 ML 會自動將許多不同的機器學習模型定型，以尋找「最佳」演算法和參數。 Azure Machine Learning 會透過計算叢集平行處理模型訓練執行。

在 [Azure Machine Learning Studio](https://ml.azure.com) 中，選取 [計算叢集] 索引標籤，再從左側功能表中選取 [計算]。選取 [新增]：

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="顯示如何建立計算叢集的螢幕擷取畫面":::

在 **建立計算叢集** 畫面中：

1. 選取 VM 大小 (本教學課程中可選取 `Standard_D11_v2` 機器)。
1. 選取 [**下一步**]
1. 提供有效的電腦名稱
1. 將 **最小節點數目** 保持為 0
1. 將 **節點數上限** 變更為 4
1. 選取 [建立] 

您可以看到叢集的狀態已變更為 **正在建立**。

>[!NOTE]
> 建立叢集時有 0 個節點，這表示不會產生計算成本。 當自動化 ML 作業執行時，才會產生成本。 叢集會在 120 秒的閒置時間後自動調整並回復為 0。


## <a name="create-dataset"></a>建立資料集

在本教學課程中，您會使用可在 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)中取得的[糖尿病資料集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。

若要建立資料集，請選取 [建立資料集]，然後在左側功能表中選取 [資料集] - 您會看到下列選項：

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="螢幕擷取畫面，顯示如何建立新的資料集":::

選取 [從開啟資料集]，然後在 **從 開啟資料集建立資料集** 畫面中：

1. 使用搜尋列搜尋「糖尿病」
1. 選取 **範例:糖尿病**
1. 選取 [**下一步**]
1. 提供資料集名稱 -「糖尿病」。
1. 選取 [建立] 

您可以選取 **探索** 再選取資料集 ，以瀏覽資料：

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="螢幕擷取畫面，顯示如何探索資料集":::

資料有 10 個基準輸入變數 (例如年齡、性別、身體質量指數、平均血壓和六個血清測量值)，以及一個名為 **Y** 的目標變數 (糖尿病在基準後一年的量化值)。

## <a name="create-automated-ml-run"></a>建立自動化 ML 執行

在 [Azure Machine Learning Studio](https://ml.azure.com) 中，選取 [新增自動化 ML 執行]，再從左側功能表中選取 [自動化 ML]：

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="螢幕擷取畫面，顯示如何建立新的自動化 ML 執行":::

接下來，選取您稍早建立的 **糖尿病** 資料集，然後選取 [下一步]：

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="螢幕擷取畫面，顯示如何選取資料集":::
 
在 **設定執行** 畫面中：

1. 在 **實驗名稱** 底下， 選取 [新建]
1. 提供實驗名稱
1. 在目標資料行欄位中，選取 [Y]
1. 在 **選取計算叢集** 欄位中，選取您稍早建立的計算叢集。 

完成的表單看起來應該像這樣：

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="螢幕擷取畫面，顯示如何設定自動化 ML":::

最後，您必須選取要執行的機器學習工作，也就是 **迴歸**：

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="螢幕擷取畫面，顯示如何設定工作":::

選取 [完成]。

> [!IMPORTANT]
> 自動化 ML 需要大約 30分鐘才能訓練完成 100 個不同的模型。

## <a name="deploy-the-best-model"></a>部署最佳模型

自動化 ML 執行完成後，您可以選取 [模型] 索引標籤，查看所有已嘗試的不同機器學習模型的清單。模型會依照效能順序排序 - 會先顯示最佳的執行模型。 選取最佳模型時，將會啟用 [部署] 按鈕：

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="螢幕擷取畫面，顯示模型清單":::

選取 [部署]，系統會顯示 **部署模型** 畫面：

1. 提供模型服務的名稱 - 使用 **diabetes-model**
1. 選取 [Azure Container Service]
1. 選取 [部署]

您應該會看到一則訊息，指出已成功部署模型。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用自動化 ML 來定型和部署機器學習模型。 在下一個教學課程中，您將了解如何從 Power BI 使用此模型 (為模型計分)。

> [!div class="nextstepaction"]
> [教學課程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
