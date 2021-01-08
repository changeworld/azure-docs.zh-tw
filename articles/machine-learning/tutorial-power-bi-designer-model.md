---
title: 教學課程：拖放以建立預測模型 (第 1 部分，共 2 部分)
titleSuffix: Azure Machine Learning
description: 了解如何使用設計工具來建置和部署機器學習預測模型。 稍後您可以使用此模型來預測 Microsoft Power BI 中的結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.custom: designer
ms.openlocfilehash: 995979c7fe100637aa8e241489805fb09d6723f7
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814783"
---
# <a name="tutorial-power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>教學課程：Power BI 整合 - 拖放以建立預測模型 (第 1 部分，共 2 部分)

在本教學課程的第 1 部分中，您會使用 Azure Machine Learning 設計工具來定型和部署預測性機器學習模型。 設計工具是一個無需程式碼的拖放使用者介面。 在第 2 部分中，您將使用模型來預測 Microsoft Power BI 中的結果。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 計算執行個體。
> * 建立 Azure Machine Learning 推斷叢集。
> * 建立資料集。
> * 定型迴歸模型。
> * 將模型部署到即時評分端點。


有三種方式可用來建立及部署您將在 Power BI 中使用的模型。  本文將說明「選項 B：使用設計工具來定型和部署模型。」  此選項會使用設計工具介面，無需程式碼撰寫經驗。  

但是，您可以改為使用其他選項：

* [選項 A：使用 Jupyter Notebooks 定型和部署模型](tutorial-power-bi-custom-model.md)。 此選項會使用 Azure Machine Learning Studio 中裝載的 Jupyter Notebooks，需要程式碼撰寫經驗。
* [選項 C：使用自動化機器學習定型和部署模型](tutorial-power-bi-automated-model.md)。 此選項無需程式碼撰寫經驗，可將資料準備和模型訓練完全自動化。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。 如果您沒有訂用帳戶，可以使用[免費試用](https://aka.ms/AMLFree)。 
- Azure Machine Learning 工作區。 如果沒有工作區，請參閱[建立和管理 Azure Machine Learning 工作區](./how-to-manage-workspace.md#create-a-workspace)。
- 機器學習工作流程的簡介知識。


## <a name="create-compute-to-train-and-score"></a>建立計算來定型和評分

在本節中，您會建立「計算執行個體」。 計算執行個體用來定型機器學習模型。 您也會建立一個「推斷叢集」，以裝載已部署的模型以進行即時評分。

登入 [Azure Machine Learning Studio](https://ml.azure.com)。 在左側功能表中，選取 [計算]，再選取 [新增]：

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="顯示如何建立計算執行個體的螢幕擷取畫面。":::

在 [建立計算執行個體] 頁面上，選取 VM 大小。 在本教學課程中，請選取 **Standard_D11_v2** VM。 然後選取 [下一步]。 

在 **設定** 頁面上，為您的計算執行個體命名。 然後選取 [建立]。 

>[!TIP]
> 您也可以使用計算執行個體來建立和執行筆記本。

您的計算執行個體 **狀態** 現在為 **正在建立**。 電腦需要約 4 分鐘來佈建。 

當您等候時，請在 **計算** 頁面上選取 [推斷叢集] 索引標籤。然後選取 [新增]：

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="螢幕擷取畫面，顯示如何建立推斷叢集。":::

在 **建立推斷叢集** 頁面上，選取區域和 VM 大小。 在本教學課程中，請選取 **Standard_D11_v2** VM。 然後選取 [下一步]。 

在 **組態設定** 頁面上：

1. 提供有效的計算名稱。
1. 選取 [Dev-test] 作為叢集用途。 此選項會建立單一節點來裝載已部署的模型。
1. 選取 [建立]。

您的推斷叢集 **狀態** 現在為 **正在建立**。 您的單一節點叢集需要約 4 分鐘來部署。

## <a name="create-a-dataset"></a>建立資料集

在本教學課程中，您會使用[糖尿病資料集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。 此資料集可在 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)中取得。

若要建立資料集，請在左側功能表中，選取 [資料集]。 接著，選取 [建立資料集]。 您可以看到下列幾種選項：

:::image type="content" source="media/tutorial-power-bi/create-dataset.png" alt-text="螢幕擷取畫面，顯示如何建立新的資料集。":::

選取 [自開始資料集]。 在 **從開啟資料集建立資料集** 頁面中：

1. 使用搜尋列尋找「糖尿病」。
1. 選取 **範例:糖尿病**。
1. 選取 [下一步]  。
1. 將您的資料集命名為「糖尿病」。
1. 選取 [建立]。

若要瀏覽資料，請選取資料集，然後選取 [探索]：

:::image type="content" source="media/tutorial-power-bi/explore-dataset.png" alt-text="螢幕擷取畫面，顯示如何探索資料集。":::

資料包含 10 個基準輸入變數 (例如年齡、性別、身體質量指數、平均血壓和六種血清測量值)。 同時也有一個名為 **Y** 的目標變數。此目標變數是在基準後一年糖尿病病情進展的量化量值。

## <a name="create-a-machine-learning-model-by-using-the-designer"></a>使用設計工具建立機器學習模型

建立計算和資料集之後，您可以使用設計工具來建立機器學習模型。 在 Azure Machine Learning Studio 中，選取 [設計工具]，再選取 [新增管線]：

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="螢幕擷取畫面，顯示如何建立新的管線。":::

您會看到空白「畫布」和 **設定** 功能表：

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="螢幕擷取畫面，顯示如何選取計算目標。":::

在 **設定** 功能表上，選擇 [選取計算目標]。 選取您稍早建立的計算執行個體，然後選取 [儲存]。 將 **草稿名稱** 變更為較容易記住的名稱，例如 diabetes-model。 最後，輸入描述。

在資產清單中，展開 **資料集** 並尋找 **糖尿病** 資料集。 將此元件拖曳至畫布上：

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="螢幕擷取畫面，顯示如何將元件拖曳至畫布上。":::

接下來，將下列元件拖曳到畫布上：

1. **線性迴歸** (位於 **Machine Learning 演算法**)
1. **定型模型** (位於 **模型訓練**)

注意畫布上元件頂端和底部的圓形。 這些圓形是連接埠。

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="螢幕擷取畫面，顯示未連接元件的連接埠。":::
 
現在將元件「連結」起來。 選取位於 **糖尿病** 資料集底部的連接埠， 並拖曳至 **訓練模型** 右上方的連接埠元件。 選取 **線性迴歸** 元件底部的連接埠。 並拖曳至 **定型模型** 左上方的連接埠元件。

選擇作為要預測的標籤 (目標) 變數使用的資料集資料行。 選取 [定型模型]，接著選取 [編輯資料行]。 

在對話方塊中，選取 [輸入資料行名稱] > **Y**：

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="螢幕擷取畫面，顯示如何選取標籤資料行。":::

選取 [儲存]。 您的機器學習「工作流程」看起來應該如下所示：

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="螢幕擷取畫面，顯示連線的元件。":::

選取 [提交]  。 在 **實驗** 底下， 選取 [新建]。 將實驗命名為，然後選取 [提交]。

>[!NOTE]
> 第一次執行實驗應該需要大約 5 分鐘。 後續執行會較快速，因為設計工具已將執行所需元件快取以減少延遲。

實驗完成時，您會看到此檢視：

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="顯示已完成執行的螢幕擷取畫面。":::

若要檢查實驗記錄，請選取 [定型模型]，然後選取 [輸出 + 記錄]。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，請在畫布頂端選取 [建立推斷管線] > [即時推斷管線]：

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="螢幕擷取畫面，顯示選取即時推斷管線的位置。":::
 
管線會壓縮為執行模型評分所需的元件。 對資料評分時，您不會知道目標變數的值。 因此您可以從資料集移除 **Y**。 

若要移除 **Y**，請將 **選取資料集中的資料行** 元件新增至畫布。 連接元件，讓糖尿病資料集整合為輸入。 結果會輸出到 **計分模型** 元件中：

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="顯示如何移除資料行的螢幕擷取畫面。":::

在畫布上選取 [選取資料集中的資料行] 元件，再選取的 [編輯資料行]。 

在 **選取資料行** 對話方塊中，選擇 [依名稱]。 然後，確定已選取所有輸入變數，但「未」 選取目標：

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="顯示如何移除資料行設定的螢幕擷取畫面。":::

選取 [儲存]。 

最後，選取 [計分模型] 元件，並確定已清除 [將分數資料行附加至輸出] 核取方塊。 為了減少延遲，系統會在沒有輸入的情況下傳回預測。

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="顯示計分模型元件設定的螢幕擷取畫面。":::

在畫布頂端，選取 [提交]。

成功執行推斷管線時，您可以接著將模型部署至推斷叢集。 選取 [部署]。 

在 **設定即時端點** 對話方塊中選取 [部署新的即時端點]。 將端點命名為 my-diabetes-model。 選取您稍早建立的推斷，然後選取 [部署]：

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="顯示即時端點設定的螢幕擷取畫面。":::
## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何定型和部署設計工具模型。 在下一部分中，您將了解如何在 Power BI 使用此模型 (為模型計分)。

> [!div class="nextstepaction"]
> [教學課程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
