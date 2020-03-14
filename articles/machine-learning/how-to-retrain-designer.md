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
ms.date: 02/24/2020
ms.openlocfilehash: 264b169eefde18880f50feae2554aa3ca7037b1f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368157"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>使用 Azure Machine Learning 設計工具重新定型模型（預覽）
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在此操作說明文章中，您將瞭解如何使用 Azure Machine Learning 設計工具來重新定型機器學習模型。 瞭解如何使用已發佈的管線將機器學習工作流程自動化，以進行重新定型。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 將機器學習模型定型。
> * 建立管線參數。
> * 發佈您的訓練管線。
> * 重新定型您的模型。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://aka.ms/AMLFree)。
* 具有企業 SKU 的 Azure Machine Learning 工作區。

本文假設您已具備在設計工具中建立管線的基本知識。 如需設計工具的導引簡介，請完成[教學課程](tutorial-designer-automobile-price-train-score.md)。 

### <a name="sample-pipeline"></a>範例管線

本文中使用的管線是在[範例3：收入預測](how-to-designer-sample-classification-predict-income.md)中找到的變更版本。 它會使用匯[入資料](algorithm-module-reference/import-data.md)模組，而不是範例資料集，來示範如何使用您自己的資料來定型模型。

![顯示已修改之範例管線的螢幕擷取畫面，其中含有反白顯示 [匯入資料] 模組的方塊](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="train-a-machine-learning-model"></a>訓練機器學習模型

若要重新定型模型，您需要初始模型。 在本節中，您將瞭解如何使用設計工具來定型模型，並存取儲存的模型。

1. 選取 [匯**入資料**] 模組。
1. 在 [屬性] 窗格上，指定資料來源。

   ![顯示匯入資料模組範例設定的螢幕擷取畫面](./media/how-to-retrain-designer/import-data-settings.png)

   在此範例中，資料會儲存在[Azure](how-to-access-data.md)資料存放區中。 如果您還沒有資料存放區，您可以選取 [**新增資料**存放區] 來立即建立一個。

1. 指定資料的路徑。 您也可以選取 **[流覽路徑**] 以流覽至您的資料存放區。 
1. 選取畫布頂端的 [**執行**]。
    
   > [!NOTE]
   > 如果您已經為此管線草稿設定預設計算，管線就會自動執行。 否則，您可以遵循 [設定] 窗格上的提示來立即設定一個。

### <a name="find-your-trained-model"></a>尋找您的定型模型

設計工具會將所有管線輸出（包括定型的模型）儲存到預設儲存體帳戶。 您也可以直接在設計工具中存取已定型的模型：

1. 等候管線完成執行。
1. 選取 **訓練模型** 模組。
1. 在 [設定] 窗格中，選取 [**輸出 + 記錄**]。
1. 選取 [**視圖輸出**] 圖示，然後依照快顯視窗中的指示，尋找定型的模型。

![顯示如何下載已定型模型的螢幕擷取畫面](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="create-a-pipeline-parameter"></a>建立管線參數

新增管線參數，以在執行時間動態設定變數。 針對此管線，加入定型資料路徑的參數，以便您可以在新的資料集上重新定型您的模型。

1. 選取 [匯**入資料**] 模組。
1. 在 [設定] 窗格中，選取 [**路徑**] 欄位上方的省略號。
1. 選取 [**新增至管線參數**]。
1. 提供參數名稱和預設值。

   > [!NOTE]
   > 您可以選取管線草稿標題旁邊的**設定**齒輪圖示，檢查和編輯管線參數。 

![顯示如何建立管線參數的螢幕擷取畫面](media/how-to-retrain-designer/add-pipeline-parameter.png)

## <a name="publish-a-training-pipeline"></a>發行訓練管線

當您發佈管線時，它會建立管線端點。 管線端點可讓您重複使用和管理管線，以進行可重複性和自動化。 在此範例中，您已設定要重新定型的管線。

1. 選取設計工具畫布上方的 [**發行**]。
1. 選取或建立管線端點。

   > [!NOTE]
   > 您可以將多個管線發佈至單一端點。 端點中的每個管線都會獲得一個版本號碼，您可以在呼叫管線端點時指定此編號。

1. 選取 [發佈]。

## <a name="retrain-your-model"></a>重新定型您的模型

現在您已有已發佈的訓練管線，可以使用新的資料來重新定型您的模型。 您可以從 Azure 入口網站的管線端點提交執行，或以程式設計方式提交。

### <a name="submit-runs-by-using-the-designer"></a>使用設計工具提交執行

使用下列步驟，從設計工具提交管線端點執行：

1. 移至 [**端點**] 頁面。
1. 選取 [**管線端點**] 索引標籤。
1. 選取您的管線端點。
1. 選取 [**已發佈的管線**] 索引標籤。
1. 選取您要執行的管線。
1. 選取 [提交]。
1. 在 [安裝程式] 對話方塊中，您可以指定輸入資料路徑值的新值。 這個值會指向您的新資料集。

![顯示如何在設計工具中設定參數化管線執行的螢幕擷取畫面](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>使用程式碼提交執行

您可以在 [總覽] 面板中找到已發佈管線的 REST 端點。 藉由呼叫端點，您可以重新定型已發行的管線。

若要進行 REST 呼叫，您需要 OAuth 2.0 持有人類型驗證標頭。 如需設定工作區驗證及進行參數化 REST 呼叫的詳細資訊，請參閱[建立批次計分的 Azure Machine Learning 管線](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)。

## <a name="next-steps"></a>後續步驟

遵循[設計工具教學](tutorial-designer-automobile-price-train-score.md)課程來定型和部署回歸模型。
