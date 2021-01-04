---
title: 教學課程：拖放以建立預測模型 (第 1 部分，共 2 部分)
titleSuffix: Azure Machine Learning
description: 了解如何使用設計工具建置和部署機器學習預測模型，以使用該模型來預測 Microsoft Power BI 的結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: samkemp
author: samuel100
ms.reviewer: sdgilley
ms.date: 12/11/2020
ms.openlocfilehash: f0e1ffe60069a2379f8eddab1aae74db2b4eac50
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/13/2020
ms.locfileid: "97370596"
---
# <a name="tutorial--power-bi-integration---drag-and-drop-to-create-the-predictive-model-part-1-of-2"></a>教學課程：Power BI 整合 - 拖放以建立預測模型 (第 1 部分，共 2 部分)

在本教學課程的第一個部分中，您會使用無需太多程式碼的拖放使用者介面 Azure Machine Learning 設計工具，定型和部署預測性機器學習模型。 在第 2 部分中，您將使用模型來預測 Microsoft Power BI 中的結果。

在本教學課程中，您：

> [!div class="checklist"]
> * 建立 Azure Machine Learning 計算執行個體
> * 建立 Azure Machine Learning 推斷叢集
> * 建立資料集
> * 定型迴歸模型
> * 將模型部署到即時評分端點


有三種不同的方式可用來建立及部署您將在 Power BI 中使用的模型。  本文將說明選項 B：使用設計工具訓練和部署模型。  此選項說明使用設計工具的低程式碼撰寫體驗 (拖放使用者介面)。  

您也可以改為使用：

* [選項 A：使用 Notebooks 定型並部署模型](tutorial-power-bi-custom-model.md) - 程式碼優先撰寫體驗 (使用 Azure Machine Learning Studio 中裝載的 Jupyter Notebooks)。
* [選項 C：使用自動化 ML 訓練和部署模型](tutorial-power-bi-automated-model.md) - 無程式碼撰寫體驗，可將資料準備和模型訓練完全自動化。

## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 ([可使用免費試用版](https://aka.ms/AMLFree))。 
- Azure Machine Learning 工作區。 如果您還沒有工作區，請遵循[如何建立 Azure Machine Learning 工作區](./how-to-manage-workspace.md#create-a-workspace)。
- 機器學習工作流程的簡介知識。


## <a name="create-compute-for-training-and-scoring"></a>建立用於訓練和評分的計算

在本節中，您會建立「計算執行個體」，用於訓練機器學習模型。 此外，您也會建立一個「推斷叢集」，用來裝載已部署的模型以進行即時評分。

登入 [Azure Machine Learning Studio](https://ml.azure.com) ，然後從左側功能表中選取 [新增]，接著選取 [計算] ：

:::image type="content" source="media/tutorial-power-bi/create-new-compute.png" alt-text="顯示如何建立計算執行個體的螢幕擷取畫面":::

在產生的 **建立計算執行個體** 畫面上，選取 [下一個]，接著選取 VM 大小 (在本教學課程中選取 `Standard_D11_v2`)。 在設定頁面中，為您的計算執行個體提供有效的名稱，然後選取 [建立]。 

>[!TIP]
> 計算執行個體也可以用來建立和執行筆記本。

您現在可以看到計算執行個體 **狀態** 為 **正在建立** - 佈建機器需要約 4 分鐘的時間。 在等候時，請在計算頁面上選取 [新增]，接著選取 [推斷叢集] 索引標籤：

:::image type="content" source="media/tutorial-power-bi/create-cluster.png" alt-text="螢幕擷取畫面，顯示如何建立推斷叢集":::

在產生的 **建立推斷叢集** 頁面中，選取後面接著 VM 大小的區域 (在本教學課程中，選取 `Standard_D11_v2`)，然後選取 [下一個]。 在 **組態設定** 頁面上：

1. 提供有效的電腦名稱
1. 選取 [Dev-test] 作為叢集用途 (建立單一節點來裝載已部署的模型)
1. 選取 [建立] 

您現在可以看到推斷叢集的 **狀態** 為 **正在建立** - 部署單一節點叢集需要約 4 分鐘的時間。

## <a name="create-a-dataset"></a>建立資料集

在本教學課程中，您會使用可在 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/)中取得的[糖尿病資料集](https://www4.stat.ncsu.edu/~boos/var.select/diabetes.html)。

若要建立資料集，請在左側功能表中選取 [資料集]，然後選取 [建立資料集] - 您會看到下列選項：

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

## <a name="create-a-machine-learning-model-using-designer"></a>使用設計工具建立機器學習模型

建立計算和資料集後，您可以繼續使用設計工具來建立機器學習模型。 在 Azure Machine Learning Studio 中，選取 [新增管線]，再選取 [設計工具]：

:::image type="content" source="media/tutorial-power-bi/create-designer.png" alt-text="螢幕擷取畫面，顯示如何建立新的管線":::

您會看到空白的「畫布」，也可以在其中看到 **設定功能表**：

:::image type="content" source="media/tutorial-power-bi/select-compute.png" alt-text="螢幕擷取畫面，顯示如何選取計算目標":::

在 **設定** 功能表上，選取 [計算目標]，然後選取您稍早建立的計算執行個體，接著選取 [儲存]。 將 **草稿名稱** 重新命名為更容易記住名稱 (例如螢幕擷取畫面) 並輸入描述。

接下來，在列出的資產中展開 **資料集** 並尋找 **糖尿病** 資料集 - 將此模組拖放到畫布上：

:::image type="content" source="media/tutorial-power-bi/drag-component.png" alt-text="螢幕擷取畫面，顯示如何將元件拖曳至其上":::

接下來，將下列元件拖放到畫布上：

1. 線性迴歸 (位於 **Machine Learning 演算法**)
1. 定型模型 (位於 **模型訓練**)

您的畫布看起來應該像這樣 (請注意，元件的頂端和底部有一個小圓圈，稱為端口 - 會以紅色反白顯示)：

:::image type="content" source="media/tutorial-power-bi/connections.png" alt-text="螢幕擷取畫面，顯示未連接的元件":::
 
接下來，您需要將這些元件「串連」在一起。 選取位於 **糖尿病** 資料集底部的端口，並拖曳至 **定型模型** 元件頂端的右側端口。 選取位於 **線性迴歸** 元件底部的端口，並拖曳至 **定型模型** 端口頂端的左側端口。

選擇資料集中作為要預測的標籤 (目標) 變數使用的資料行。 選取 [編輯資料行]，接著選取 [定型模型] 元件。 從對話方塊中 - 在下拉式清單中選取 **Y**，接著選取 [輸入資料行名稱]：

:::image type="content" source="media/tutorial-power-bi/label-columns.png" alt-text="螢幕擷取畫面，顯示選取標籤資料行":::

選取 [儲存]。 您的機器學習「工作流程」看起來應該如下所示：

:::image type="content" source="media/tutorial-power-bi/connected-diagram.png" alt-text="螢幕擷取畫面，顯示如何連接元件":::

選取 [提交]，然後選取 [實驗] 下的 [新建]。 提供實驗的名稱，然後選取 [提交]。

>[!NOTE]
> 在第一次執行時，您的實驗應該需要大約 5 分鐘才能完成。 後續執行會較快速 - 因為設計工具已將執行的元件快取以減少延遲。

實驗完成時，您會看到：

:::image type="content" source="media/tutorial-power-bi/completed-run.png" alt-text="顯示已完成執行的螢幕擷取畫面":::

您可以選取 [輸出 + 記錄] 再選取 [定型模型]，以檢查實驗的記錄。

## <a name="deploy-the-model"></a>部署模型

若要部署模型，請選取 [即時推斷管線]，再選取 [建立推斷管線] (位於畫布頂端)：

:::image type="content" source="media/tutorial-power-bi/pipeline.png" alt-text="螢幕擷取畫面，顯示即時推斷管線":::
 
管線會壓縮為執行模型評分所需的元件。 對資料計分時，您不會知道目標變數值，因此我們可以從資料集移除 **Y**。 若要移除，請將 **選取資料集中的資料行** 元件新增至畫布。 串連元件，讓糖尿病資料整合為輸入，並將結果輸出到 **計分模型** 元件中：

:::image type="content" source="media/tutorial-power-bi/remove-column.png" alt-text="顯示移除資料行的螢幕擷取畫面":::

選取 [編輯欄位]，再選取畫布上的 [選取資料集中的資料行] 元件。 在選取資料行對話方塊中，選取 [依名稱]，然後確定 **除了** 目標以外已選取所有輸入變數：

:::image type="content" source="media/tutorial-power-bi/removal-settings.png" alt-text="顯示移除資料行設定的螢幕擷取畫面":::

選取 [儲存]。 最後，選取 [評分模型] 元件，並確定未勾選 [將分數資料行新增至輸出] 核取方塊 (只會傳回預測，而不會傳回輸入「和」預測，以減少延遲)：

:::image type="content" source="media/tutorial-power-bi/score-settings.png" alt-text="顯示評分模型元件設定的螢幕擷取畫面":::

在畫布頂端，選取 [提交]。

成功執行推斷管線時，您可以接著將模型部署至推斷叢集。 選取 [部署]，這會顯示 **設定即時端點** 對話方塊。 選取 [部署新的即時端點]、將端點命名為 **my-diabetes-model**、選取稍早建立的推斷，再選取 [部署]：

:::image type="content" source="media/tutorial-power-bi/endpoint-settings.png" alt-text="顯示即時端點設定的螢幕擷取畫面":::
## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何定型和部署設計工具模型。 在下一部分中，您將了解如何從 Power BI 使用此模型 (為模型計分)。

> [!div class="nextstepaction"]
> [教學課程：在 Power BI 中使用模型](/power-bi/connect-data/service-aml-integrate?context=azure/machine-learning/context/ml-context)
