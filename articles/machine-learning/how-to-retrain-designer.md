---
title: 使用 Azure Machine Learning 設計工具重新定型模型（預覽）
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計工具（預覽）中使用已發行的管線重新定型模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 77c1cc6ef5a7353d246958d1b047f054d5f49890
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837153"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>使用 Azure Machine Learning 設計工具 (預覽) 重新定型模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在此操作說明文章中，您將瞭解如何使用 Azure Machine Learning 設計工具來重新定型機器學習模型。 您將會使用已發佈的管線來自動化您的工作流程，並設定參數來對新資料定型您的模型。 

在本文中，您將學會如何：

> [!div class="checklist"]
> * 將機器學習模型定型。
> * 建立管線參數。
> * 發佈您的訓練管線。
> * 使用新的參數重新定型您的模型。

## <a name="prerequisites"></a>Prerequisites

* 具備 Enterprise SKU 的 Azure Machine Learning 工作區。
* 完成本 how-to 系列的第1部分：[在設計工具中轉換資料](how-to-designer-transform-data.md)。

本文也假設您已具備在設計工具中建立管線的基本知識。 如需引導式簡介，請完成[教學](tutorial-designer-automobile-price-train-score.md)課程。 

### <a name="sample-pipeline"></a>範例管線

本文中使用的管線是[範例3：收入預測](samples-designer.md#classification-samples)的變更版本。 管線會使用匯[入資料](algorithm-module-reference/import-data.md)模組，而不是範例資料集，來示範如何使用您自己的資料來定型模型。

![顯示已修改之範例管線的螢幕擷取畫面，其中含有反白顯示 [匯入資料] 模組的方塊](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>建立管線參數

建立管線參數，以在執行時間動態設定變數。 在此範例中，您會將定型資料路徑從固定值變更為參數，讓您可以在不同的資料上重新訓練模型。

1. 選取 [匯**入資料**] 模組。

    > [!NOTE]
    > 這個範例會使用匯入資料模組來存取已註冊資料存放區中的資料。 不過，如果您使用替代的資料存取模式，則可以遵循類似的步驟。

1. 在 [模組詳細資料] 窗格中，選取畫布右邊的資料來源。

1. 輸入您資料的路徑。 您也可以選取 **[流覽路徑**] 來流覽您的檔案樹狀目錄。 

1. 將 [**路徑**] 欄位放在上方，然後選取出現在 [**路徑**] 欄位上方的省略號。

    ![顯示如何建立管線參數的螢幕擷取畫面](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. 選取 [**新增至管線參數**]。

1. 提供參數名稱和預設值。

   > [!NOTE]
   > 您可以選取管線草稿標題旁邊的**設定**齒輪圖示，檢查和編輯管線參數。 

1. 選取 [儲存]  。

1. 提交管線執行。

## <a name="find-a-trained-model"></a>尋找定型的模型

設計工具會將所有管線輸出（包括已定型的模型）儲存至預設工作區儲存體帳戶。 您也可以直接在設計工具中存取已定型的模型：

1. 等候管線完成執行。
1. 選取 **訓練模型** 模組。
1. 在 [模組詳細資料] 窗格中，選取畫布右邊的 [**輸出 + 記錄**]。
1. 您可以在**其他輸出**中尋找您的模型，以及執行記錄。
1. 或者，選取 [**視圖輸出**] 圖示。 從這裡開始，您可以遵循對話方塊中的指示，直接流覽至您的資料存放區。 

![顯示如何下載已定型模型的螢幕擷取畫面](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>發行訓練管線

將管線發佈至管線端點，以便日後輕鬆地重複使用您的管線。 管線端點會建立 REST 端點，以在未來叫用管線。 在此範例中，您的管線端點可讓您重複使用管線，在不同的資料上重新定型模型。

1. 選取設計工具畫布上方的 [**發行**]。
1. 選取或建立管線端點。

   > [!NOTE]
   > 您可以將多個管線發佈至單一端點。 給定端點中的每個管線都會獲得版本號碼，您可以在呼叫管線端點時指定此編號。

1. 選取 [發佈]  。

## <a name="retrain-your-model"></a>重新定型您的模型

現在您已經有已發佈的訓練管線，可以用它來對新資料重新定型模型。 您可以從 studio 工作區或以程式設計方式，從管線端點提交執行。

### <a name="submit-runs-by-using-the-designer"></a>使用設計工具提交執行

使用下列步驟，從設計工具提交參數化管線端點執行：

1. 移至您 studio 工作區中的 [**端點**] 頁面。
1. 選取 [**管線端點**] 索引標籤。然後，選取您的管線端點。
1. 選取 [**已發佈的管線**] 索引標籤。然後，選取您要執行的管線版本。
1. 選取 [提交]  。
1. 在 [安裝程式] 對話方塊中，您可以指定執行的參數值。 在此範例中，請使用非 US 資料集來更新資料路徑，以將您的模型定型。

![顯示如何在設計工具中設定參數化管線執行的螢幕擷取畫面](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>使用程式碼提交執行

您可以在 [總覽] 面板中找到已發佈管線的 REST 端點。 藉由呼叫端點，您可以重新定型已發行的管線。

若要進行 REST 呼叫，您需要 OAuth 2.0 持有人類型驗證標頭。 如需設定工作區驗證及進行參數化 REST 呼叫的詳細資訊，請參閱[建立批次計分的 Azure Machine Learning 管線](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)。

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何使用設計工具建立參數化定型管線端點。

如需如何部署模型以進行預測的完整逐步解說，請參閱設計工具[教學](tutorial-designer-automobile-price-train-score.md)課程來定型和部署回歸模型。
