---
title: 使用 Azure 機器學習設計器重新訓練模型(預覽版)
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習設計器(預覽)中使用已發布的管道重新訓練模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: keli19
author: likebupt
ms.date: 04/06/2020
ms.openlocfilehash: 721e5414fc4753cd5d58a17fc7ed51ea99868778
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80810370"
---
# <a name="retrain-models-with-azure-machine-learning-designer-preview"></a>使用 Azure Machine Learning 設計工具 (預覽) 重新定型模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在本操作操作者文章中,您將瞭解如何使用 Azure 機器學習設計器重新訓練機器學習模型。 您將使用已發布的管道來自動執行工作流並設置參數,以根據新資料訓練模型。 

在本文中，您將學會如何：

> [!div class="checklist"]
> * 將機器學習模型定型。
> * 創建管道參數。
> * 發佈培訓管道。
> * 使用新參數重新訓練模型。

## <a name="prerequisites"></a>Prerequisites

* 具備 Enterprise SKU 的 Azure Machine Learning 工作區。
* 設計器可訪問的數據集。 這可以是下列項目之一：
   * Azure 機器學習註冊資料集
    
     **-或-**
   * 存儲在 Azure 機器學習數據存儲中的數據檔。
   
有關使用設計器存取資料的資訊,請參考[如何匯入設計器](how-to-designer-import-data.md)。

本文還假定您具備在設計器中構建管道的基本知識。 有關指導性介紹,請完成[教程](tutorial-designer-automobile-price-train-score.md)。 

### <a name="sample-pipeline"></a>範例管線

本文中使用的管道是[示例 3:收入預測](samples-designer.md#classification-samples)的更改版本。 管道使用[匯入資料](algorithm-module-reference/import-data.md)模組而不是示例數據集來演示如何使用您自己的數據訓練模型。

![螢幕截圖,顯示修改的範例導管,並帶有突顯匯入資料模組的框](./media/how-to-retrain-designer/modified-sample-pipeline.png)

## <a name="create-a-pipeline-parameter"></a>建立導管參數

創建管道參數以在運行時動態設置變數。 在此範例中,您將訓練數據路徑從固定值更改為參數,以便可以根據不同資料重新訓練模型。

1. 選擇 **「導入資料**」 模組。

    > [!NOTE]
    > 本示例使用導入數據模塊訪問已註冊數據存儲中的數據。 但是,如果使用替代數據訪問模式,可以執行類似的步驟。

1. 在畫布右側的模組詳細資訊窗格中,選擇數據源。

1. 輸入數據的路徑。 您還可以選擇 **「瀏覽路徑」** 來瀏覽檔案樹。 

1. 滑鼠懸停**路徑**欄位,然後選擇顯示的**路徑**欄位上方的橢圓。

    ![展示如何建立導管參數的螢幕擷取](media/how-to-retrain-designer/add-pipeline-parameter.png)

1. 選擇 **'新增到導管' 參數**。

1. 提供參數名稱和預設值。

   > [!NOTE]
   > 您可以通過選擇管道拔模標題旁邊的 **「設置**齒輪」圖示來檢查和編輯管道參數。 

1. 選取 [儲存]  。

1. 提交管道運行。

## <a name="find-a-trained-model"></a>查找經過培訓的模型

設計器將所有管道輸出(包括經過訓練的模型)保存到預設工作區存儲帳戶。 您還可以直接在設計器中存取經過訓練的模型:

1. 等待管道完成運行。
1. 選取 **訓練模型** 模組。
1. 在「模組詳細資訊」窗格中,在畫布右側選擇 **「輸出 + 日誌**」。。
1. 您可以在**其他輸出**中尋找模型以及執行紀錄。
1. 或者,選擇 **「查看輸出**」圖示。 在此處,您可以按照對話框中的說明直接導航到數據存儲。 

![顯示如何下載訓練的模型的螢幕擷取](./media/how-to-retrain-designer/trained-model-view-output.png)

## <a name="publish-a-training-pipeline"></a>發佈培訓管道

將管道發佈到管道終結點,以便將來輕鬆重用管道。 管道終結點將創建一個 REST 終結點來調用將來的管道。 在此範例中,管道終結點允許您重用管道以重新訓練不同數據的模型。

1. 選擇 **「在**設計器畫布上方發佈」。
1. 選擇或創建管道終結點。

   > [!NOTE]
   > 您可以將多個管道發佈到單個終結點。 指定的分態的每個導管都會獲得一個版本號,您可以在調用管道終結點時指定該版本號。

1. 選取 [發佈]  。

## <a name="retrain-your-model"></a>重新訓練模型

現在,您已經發佈了培訓管道,您可以使用它重新訓練模型的新數據。 可以從工作室工作區或以程式設計方式提交從管道終結點的運行。

### <a name="submit-runs-by-using-the-designer"></a>使用設計器提交執行

使用以下步驟提交從設計器執行的參數化管道終結點:

1. 轉到工作室工作區中的 **「終結點」** 頁面。
1. 選擇 **「管道終結點**」 選項卡。然後,選擇管道終結點。
1. 選擇 **「已發布管道」** 選項卡。然後,選擇要運行的管道版本。
1. 選取 [提交]  。
1. 在「設定」對話框中,可以指定運行的參數值。 在此示例中,更新數據路徑以使用非美國數據集訓練模型。

![展示如何設定在設計器中執行的參數化導管的螢幕擷取](./media/how-to-retrain-designer/published-pipeline-run.png)

### <a name="submit-runs-by-using-code"></a>使用代碼提交執行

您可以在概述面板中找到已發佈管道的 REST 終結點。 通過調用終結點,可以重新訓練已發佈的管道。

要進行 REST 呼叫,您需要一個 OAuth 2.0 承載式身份驗證標頭。 有關將身份驗證設定為工作區與參數化 REST 呼叫的資訊,請參考建[構 Azure 機器學習管線以進行批次處理評分](tutorial-pipeline-batch-scoring-classification.md#publish-and-run-from-a-rest-endpoint)。

## <a name="next-steps"></a>後續步驟

在本文中,您學習了如何使用設計器創建參數化訓練管道終結點。

有關如何部署模型進行預測的完整演練,請參閱[設計器教程](tutorial-designer-automobile-price-train-score.md)以訓練和部署回歸模型。
