---
title: 使用 Azure Machine Learning 設計工具執行批次預測 (預覽)
titleSuffix: Azure Machine Learning
description: 了解如何使用設計工具來定型模型和設定批次預測管線。 將管線部署為可從任何 HTTP 程式庫觸發的參數化 Web 服務。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: peterlu
author: peterclu
ms.date: 02/24/2020
ms.custom: Ignite2019
ms.openlocfilehash: 01d69bffcf2c17abceba8ba2e0893360bead8b12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477216"
---
# <a name="run-batch-predictions-using-azure-machine-learning-designer-preview"></a>使用 Azure Machine Learning 設計工具執行批次預測 (預覽)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本文中，您將瞭解如何使用設計工具來建立批次預測管線。 批次預測可讓您使用可從任何 HTTP 程式庫觸發的 web 服務，依需求持續計分大型資料集。

在此操作說明中，您將學習如何執行下列工作：

> [!div class="checklist"]
> * 建立和發佈批次推斷管線
> * 使用管線端點
> * 管理端點版本

若要瞭解如何使用 SDK 設定批次評分服務，請參閱隨附的操作[說明](how-to-run-batch-predictions.md)。

## <a name="prerequisites"></a>先決條件

本操作說明假設您已經有訓練管線。 如需設計工具的引導式簡介，請完成設計工具教學課程的[第一個部分](tutorial-designer-automobile-price-train-score.md)。 

## <a name="create-a-batch-inference-pipeline"></a>建立批次推斷管線

您的定型管線必須至少執行一次，才能建立推斷管線。

1. 移至工作區中的 [**設計**工具] 索引標籤。

1. 選取訓練管線，以訓練您要用來進行預測的模型。

1. **提交**管線。

    ![提交管線](./media/how-to-run-batch-predictions-designer/run-training-pipeline.png)

現在已執行定型管線，您可以建立批次推斷管線。

1. 在 [**提交**] 旁，選取新的下拉式清單 [**建立推斷管線**]。

1. 選取 [**批次推斷管線**]。

    ![建立批次推斷管線](./media/how-to-run-batch-predictions-designer/create-batch-inference.png)
    
結果將是預設批次推斷管線。 

### <a name="add-a-pipeline-parameter"></a>新增管線參數

若要建立新資料的預測，您可以手動連接此管線草稿視圖中的不同資料集，或為您的資料集建立參數。 參數可讓您在執行時間變更批次推斷進程的行為。

在本節中，您會建立資料集參數，以指定要進行預測的不同資料集。

1. 選取 [資料集] 模組。

1. 窗格會出現在畫布的右邊。 在窗格底部，選取 [**設定為管線參數**]。
   
    輸入參數的名稱，或接受預設值。

## <a name="publish-your-batch-inferencing-pipeline"></a>發佈您的 batch 推斷管線

現在您已經準備好部署推斷管線。 這會部署管線，並使其可供其他人使用。

1. 選取 [發佈]**** 按鈕。

1. 在出現的對話方塊中，展開 [ **PipelineEndpoint**] 的下拉式，然後選取 [**新增 PipelineEndpoint**]。

1. 提供端點名稱和選擇性描述。

    在靠近對話方塊底部的地方，您可以看到您使用定型期間所使用之資料集識別碼的預設值所設定的參數。

1. 選取 [發佈]  。

![發佈管線](./media/how-to-run-batch-predictions-designer/publish-inference-pipeline.png)


## <a name="consume-an-endpoint"></a>使用端點

現在，您有一個已發行的管線，其中包含資料集參數。 管線會使用定型管線中建立的定型模型，將您提供的資料集評分為參數。

### <a name="submit-a-pipeline-run"></a>提交管線執行 

在本節中，您將設定手動管線執行並改變管線參數，以對新資料進行評分。 

1. 部署完成之後，請移至 [**端點**] 區段。

1. 選取 [**管線端點**]。

1. 選取您建立的端點名稱。

![端點連結](./media/how-to-run-batch-predictions-designer/manage-endpoints.png)

1. 選取 [**已發佈的管線**]。

    此畫面會顯示在此端點下發布的所有已發佈管線。

1. 選取您發佈的管線。

    [管線詳細資料] 頁面會顯示管線的詳細執行歷程記錄和連接字串資訊。 
    
1. 選取 [**提交**] 以建立管線的手動執行。

    ![管線詳細資料](./media/how-to-run-batch-predictions-designer/submit-manual-run.png)
    
1. 將參數變更為使用不同的資料集。
    
1. 選取 [**提交**] 以執行管線。

### <a name="use-the-rest-endpoint"></a>使用 REST 端點

您可以在 [**端點**] 區段中找到如何使用管線端點和已發佈管線的相關資訊。

您可以在 [執行總覽] 面板中找到管線端點的 REST 端點。 藉由呼叫端點，您會使用其預設的已發佈管線。

您也可以在 [**已發佈的管線**] 頁面中使用已發佈的管線。 選取已發佈的管線並尋找其 REST 端點。 

![Rest 端點詳細資料](./media/how-to-run-batch-predictions-designer/rest-endpoint-details.png)

若要進行 REST 呼叫，您將需要 OAuth 2.0 持有人類型驗證標頭。 若要進一步了解如何設定對工作區進行驗證以及如何進行參數化 REST 呼叫，請參閱下列[教學課程章節](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)。

## <a name="versioning-endpoints"></a>版本設定端點

設計工具會將版本指派給您發行至端點的每個後續管線。 您可以在 REST 呼叫中指定要當做參數執行的管線版本。 如果您未指定版本號碼，則設計工具會使用預設管線。

當您發佈管線時，您可以選擇讓它成為該端點的新預設管線。

![設定預設管線](./media/how-to-run-batch-predictions-designer/set-default-pipeline.png)

您也可以在端點的 [**已發佈的管線**] 索引標籤中設定新的預設管線。

![設定預設管線](./media/how-to-run-batch-predictions-designer/set-new-default-pipeline.png)

## <a name="next-steps"></a>後續步驟

依照設計工具[教學課程](tutorial-designer-automobile-price-train-score.md)的指示來定型和部署迴歸模型。
''