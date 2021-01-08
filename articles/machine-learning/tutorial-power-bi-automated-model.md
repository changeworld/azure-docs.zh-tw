---
title: 教學課程：使用自動化 ML 建立預測模型 (第 1 部分，共 2 部分)
titleSuffix: Azure Machine Learning
description: 了解如何建置和部署自動化機器學習模型，讓您可以使用最佳模型來預測 Microsoft Power BI 中的結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: 6dc99d58f15653e9d3f991622de3bb3388690459
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814800"
---
# <a name="tutorial-power-bi-integration---create-the-predictive-model-by-using-automated-machine-learning-part-1-of-2"></a>教學課程：Power BI 整合 - 使用自動化機器學習建立預測模型 (第 1 部分，共 2 部分)

在本教學課程的第 1 部分中，您會定型和部署預測性機器學習模型。 您會在 Azure Machine Learning Studio 中使用自動化機器學習 (ML)。  在第 2 部分中，您將使用表現最佳的模型來預測 Microsoft Power BI 中的結果。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 計算叢集。
> * 建立資料集。
> * 建立自動化機器學習執行。
> * 將最佳模型部署至即時評分端點。


有三種方式可用來建立及部署您將在 Power BI 中使用的模型。  本文將說明「選項 C：在 Studio 中使用自動化機器學習定型和部署模型。」  此選項無需程式碼撰寫經驗， 可將資料準備和模型訓練完全自動化。 

但是，您可以改為使用其他選項：

* [選項 A：使用 Jupyter Notebooks 定型和部署模型](tutorial-power-bi-custom-model.md)。 此選項會使用 Azure Machine Learning Studio 中裝載的 Jupyter Notebooks，需要程式碼撰寫經驗。
* [選項 B：使用 Azure Machine Learning 設計工具定型和部署模型](tutorial-power-bi-designer-model.md)。 此選項使用拖放使用者介面，需要一些程式碼撰寫經驗。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有訂用帳戶，可以使用[免費試用](https://aka.ms/AMLFree)。 
- Azure Machine Learning 工作區。 如果沒有工作區，請參閱[建立和管理 Azure Machine Learning 工作區](./how-to-manage-workspace.md#create-a-workspace)。

## <a name="create-a-compute-cluster"></a>建立計算叢集

自動化機器學習會自動將許多不同的機器學習模型定型，以找出「最佳」演算法和參數。 Azure Machine Learning 會透過計算叢集平行處理模型訓練執行。

若要開始，請在 [Azure Machine Learning Studio](https://ml.azure.com) 的左側功能表中，選取 [計算]。 開啟 [計算叢集] 索引標籤。然後選取 [新增]：

:::image type="content" source="media/tutorial-power-bi/create-compute-cluster.png" alt-text="顯示如何建立計算叢集的螢幕擷取畫面。":::

在 **建立計算叢集** 頁面中：

1. 選取 VM 大小。 在本教學課程中，**Standard_D11_v2** 機器的大小已足夠。
1. 選取 [下一步]  。
1. 提供有效的機器名稱。
1. 將 **最小節點數目** 保持為 `0`。
1. 將 **節點數上限** 變更為 `4`。
1. 選取 [建立]。

叢集的狀態會變更為 **正在建立**。

>[!NOTE]
> 新叢集有 0 個節點，因此不會產生計算成本。 只有在執行自動化機器學習作業時，才會產生成本。 叢集會在 120 秒的閒置時間後自動調整並回復為 0。


## <a name="create-a-dataset"></a>建立資料集

在本教學課程中，您會使用[糖尿病資料集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。 此資料集可在 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)中取得。

若要建立資料集，請在左側功能表中，選取 [資料集]。 接著，選取 [建立資料集]。 您可以看到下列幾種選項：

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="螢幕擷取畫面，顯示如何建立新的資料集。":::

選取 [自開始資料集]。 然後在 **從開啟資料集建立資料集** 頁面中：

1. 使用搜尋列尋找「糖尿病」。
1. 選取 **範例:糖尿病**。
1. 選取 [下一步]  。
1. 將您的資料集命名為「糖尿病」。
1. 選取 [建立]。

若要瀏覽資料，請選取資料集，然後選取 [探索]：

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="螢幕擷取畫面，顯示如何探索資料集。":::

資料包含 10 個基準輸入變數 (例如年齡、性別、身體質量指數、平均血壓和六種血清測量值)。 同時也有一個名為 **Y** 的目標變數。此目標變數是在基準後一年糖尿病病情進展的量化量值。

## <a name="create-an-automated-machine-learning-run"></a>建立自動化機器學習執行

請在 [Azure Machine Learning Studio](https://ml.azure.com) 的左側功能表中，選取 [自動化 ML]。 接著選取 [新增自動化 ML 執行]：

:::image type="content" source="media/tutorial-power-bi/create-new-run.png" alt-text="螢幕擷取畫面，顯示如何建立新的自動化機器學習執行。":::

接下來，選取您稍早建立的 **糖尿病** 資料集， 然後，選取 [下一步]：

:::image type="content" source="media/tutorial-power-bi/select-dataset.png" alt-text="螢幕擷取畫面，顯示如何選取資料集。":::
 
在 **設定執行** 頁面上：

1. 在 **實驗名稱** 底下， 選取 [新建]。
1. 為實驗命名。
1. 在 **目標資料行** 欄位中，選取 [Y]。
1. 在 **選取計算叢集** 欄位中，選取您稍早建立的計算叢集。 

完成的表單看起來應該像這樣：

:::image type="content" source="media/tutorial-power-bi/configure-automated.png" alt-text="螢幕擷取畫面，顯示如何設定自動化機器學習。":::

最後，選取機器學習工作。 在此處，工作為 **迴歸**：

:::image type="content" source="media/tutorial-power-bi/configure-task.png" alt-text="螢幕擷取畫面，顯示如何設定工作。":::

選取 [完成]。

> [!IMPORTANT]
> 自動化機器學習大約需要 30 分鐘才能完成 100 個模型的訓練。

## <a name="deploy-the-best-model"></a>部署最佳模型

自動化機器學習執行完成後，您可以選取 **模型** 索引標籤，查看所有已嘗試的不同機器學習模型的清單。模型會依照效能排序；會先顯示最佳的執行模型。 選取最佳模型後，將會啟用 [部署] 按鈕：

:::image type="content" source="media/tutorial-power-bi/list-models.png" alt-text="螢幕擷取畫面，顯示模型清單。":::

選取 [部署] 以開啟 **部署模型** 視窗：

1. 將模型服務命名為 diabetes-model。
1. 選取 [Azure Container Service]。
1. 選取 [部署]。

您應該會看到一則訊息，指出已成功部署模型。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用機器學習定型和部署機器學習模型。 在下一個教學課程中，您將了解如何在 Power BI 中使用 (計分) 此模型。

> [!div class="nextstepaction"]
> [教學課程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
