---
title: 教學課程：使用設計工具預測汽車價格
titleSuffix: Azure Machine Learning
description: 了解如何使用拖放介面進行機器學習模型的定型、評分和部署。 本教學課程是使用線性迴歸預測汽車價格相關系列的第一部分 (共兩個部分)。
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 639a61cddde27b0d989e5a3dd4c599c353182a73
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720150"
---
# <a name="tutorial-predict-automobile-price-with-the-designer-preview"></a>教學課程：使用設計工具預測汽車價格 (預覽)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

在這個分成兩部分的教學課程中，您將了解如何使用 Azure Machine Learning 設計工具來開發及部署預測性分析解決方案，以預測任何汽車的價格。

在教學課程的第一部分中，您將了解如何：

> [!div class="checklist"]
> * 建立新管線。
> * 匯入資料。
> * 準備資料。
> * 將機器學習模型定型。
> * 評估機器學習模型。

在教學課程的[第二部分](tutorial-designer-automobile-price-deploy.md)中，您會將模型部署為即時推斷端點，以根據您傳入的技術規格預測任何汽車的價格。 

> [!NOTE]
>本教學課程完成後，將可作為範例管線。
>
>若要尋找此範例，請移至工作區中的設計工具。 在 [新增管線]  區段中，選取 **[範例 1 - 迴歸：汽車價格預測 (基本)]** 。

## <a name="create-a-new-pipeline"></a>建立新管線

Azure Machine Learning 管線會將多個機器學習和資料處理步驟組織成單一資源。 管線可讓您在不同的專案和使用者間組織、管理和重複使用複雜的機器學習工作流程。

若要建立 Azure Machine Learning 管線，您必須要有 Azure Machine Learning 工作區。 在本節中，您將了解如何建立這些資源。

### <a name="create-a-new-workspace"></a>建立新的工作區

如果您有企業版 Azure Machine Learning 工作區，請[跳至下一節](#create-the-pipeline)。

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal-enterprise.md)]

### <a name="create-the-pipeline"></a>建立管線

1. 登入 [ml.azure.com](https://ml.azure.com)，並選取您要使用的工作區。

1. 選取 [設計工具]  。

    ![視覺效果工作區的螢幕擷取畫面，其中顯示如何存取設計工具](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. 選取 [易於使用的預建模組]  。

1. 在畫布頂端選取預設管線名稱 **Pipeline-Created-on**。 請將其重新命名為*汽車價格預測*。 此名稱不必是唯一的。

## <a name="import-data"></a>匯入資料

設計工具中包含數個範例資料集，可供您在實驗時使用。 在本教學課程中，使用**汽車價格資料 (未經處理)** 。 

1. 管線畫布左側是資料集和模組的選擇區。 選取 [資料集]  ，然後選取 [範例]  區段以檢視可用的範例資料集。

1. 選取資料集**汽車價格資料 (原始)** ，並將其拖曳到畫布上。

   ![將資料拖曳到畫布](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>將資料視覺化

您可以將資料視覺化，以了解您將使用的資料集。

1. 選取 [汽車價格資料 (原始)]  模組。

1. 在畫布右側的 [屬性] 窗格中，選取 [輸出]  。

1. 選取圖形圖示以將資料視覺化。

    ![將資料視覺化](./media/tutorial-designer-automobile-price-train-score/visualize-data.png)

1. 選取資料視窗中的不同資料行，以檢視各個資料行的相關資訊。

    每個資料列分別代表一款汽車，而與每款汽車相關聯的變數會顯示為資料行。 此資料集中有 205 個資料列和 26 個資料行。

## <a name="prepare-data"></a>準備資料

資料集在分析之前通常需進行一些前置處理。 您在檢查資料集時，可能會發現有某些遺漏值。 必須清除這些遺漏的值，才能讓模型正確地分析資料。

### <a name="remove-a-column"></a>移除資料行

當您定型模型時，您必須對遺漏的資料採取某些動作。 在此資料集中，**自負虧損**資料行遺漏了許多值，因此您會將該資料行完全排除於模型外。

1. 在選擇區頂端的搜尋方塊中輸入**選取**，以尋找**選取資料集中的資料行**模組。

1. 將**選取資料集中的資料行**模組拖曳到畫布上。 將模組放在資料集模組下。

1. 將**汽車價格資料 (未經處理)** 資料集連線至**選取資料集中的資料行**模組。 從資料集的輸出連接埠 (即畫布上位於資料集底部的小圓圈) 拖曳到**選取資料集中的資料行**的輸入連接埠 (即模組頂端的小圓圈)。

    > [!TIP]
    > 將某個模組的輸出連接埠連線至另一個模組的輸入連接埠時，您會透過管線建立資料流程。
    >

    ![連線模組](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. 選取**選取資料集中的資料行**模組。

1. 在畫布右側的 [屬性] 窗格中，選取 [所有資料行]  。

1. 選取 **+** 以新增規則。

1. 從下拉式功能表中，選取 [排除]  和 [資料行名稱]  。
    
1. 在文字方塊中輸入*自負虧損*。

1. 在右下方選取 [儲存]  按鈕，以關閉資料行選取器。

    ![排除資料行](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. 選取**選取資料集中的資料行**模組。 

1. 在 [屬性] 窗格中選取 [註解]  文字方塊，然後輸入*排除自負虧損*。

    圖形上會出現註解，以協助您組織管線。

### <a name="clean-missing-data"></a>清除遺漏的資料

移除**自負虧損**資料行之後，您的資料集仍有遺漏值。 您可以使用**清除遺漏的資料**模組來移除其餘遺漏的資料。

> [!TIP]
> 在使用設計工具中大部分的模組時，都必須從輸入資料中清除遺漏值。

1. 在搜尋方塊中輸入**清除**，以尋找**清除遺漏的資料**模組。

1. 將**清除遺漏的資料**模組拖曳至管線畫布上。 將其連線至**選取資料集中的資料行**模組。 

1. 在 [屬性] 窗格中，選取 [清除模式]  下方的 [移除整個資料列]  。

1. 在 [屬性] 窗格的 [註解]  方塊中，輸入「移除遺漏的值資料列」  。 

    您的管線此時應會顯示如下：
    
    ![Select-column](./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png)

## <a name="train-a-machine-learning-model"></a>訓練機器學習模型

現在您已備妥用來處理資料的模組，接下來即可設定定型模組。

因為要預測價格，也就是一個數字，因此您將使用迴歸演算法。 在此範例中，您將使用線性迴歸模型。

### <a name="split-the-data"></a>分割資料

分割資料是機器學習服務中常見的工作。 您會將資料分割成兩個不同的資料集。 一個資料集會定型模型，另一個則會測試模型的執行效果。

1. 在搜尋方塊中輸入**分割資料**，以尋找**分割資料**模組。 將**清除遺漏的資料**模組左側的連接埠連線至**分割資料**模組。

    > [!IMPORTANT]
    > 請確實將**清除遺漏的資料**的左側輸出連接埠連線至**分割資料**。 左側連接埠包含已清除的資料。 右側連接埠包含已捨棄的資料。

1. 選取**分割資料**模組。

1. 在 [屬性] 窗格中，將**第一個輸出資料集中的資料列比例**設為 0.7。

    此選項會分割 70% 的資料來定型模型，而 30% 供測試之用。 70% 的資料集將透過左側輸出連接埠來存取。 其餘資料可透過右側輸出連接埠取得。

1. 在 [屬性] 的 [註解]  方塊中，輸入「將資料集分割為訓練集 (0.7) 和測試集 (0.3)」  。

### <a name="train-the-model"></a>將模型定型

藉由提供一個包含價格的資料集，將模型定型。 此演算法會建立一個模型，用以說明定型資料所呈現的特性與價格之間的關聯性。

1. 若要選取學習演算法，請清除您的模組選擇區搜尋方塊。

1. 展開**機器學習演算法**。
    
    此選項會顯示數個可用來初始化學習演算法的模組類別。

1. 選取 [迴歸]   > [線性迴歸]  ，然後將其拖曳到管線畫布上。

1. 找出**訓練模型**模組，並將其拖曳到管線畫布。 

1. 將 [線性迴歸]  模組的輸出連接到 [訓練模型]  模組的左側輸入。

1. 將**分割資料**模組的訓練資料輸出 (左側連接埠) 連接到**訓練模型**模組的右側輸入。
    
    > [!IMPORTANT]
    > 請確實將**分割資料**的左側輸出連接埠連線至**定型模型**。 左側連接埠包含定型集。 右側連接埠包含測試集。

    ![此螢幕擷取畫面顯示「訓練模型」模組的正確組態。 「線性迴歸」模組連線至「訓練模型」模組的左側連接埠，「分割資料」模組連線至「訓練模型」的右側連接埠](./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png)

1. 選取 **訓練模型** 模組。

1. 在 [屬性] 窗格中，選取 [編輯資料行]  選取器。

1. 在 [標籤資料行]  對話方塊中，展開下拉式功能表，然後選取 [資料行名稱]  。 

1. 在文字方塊中輸入*價格*，以指定您的模型要預測的值。

    您的管線應會顯示如下：

    ![此螢幕擷取畫面顯示管線在新增「訓練模型」模組之後的正確組態。](./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png)

## <a name="score-a-machine-learning-model"></a>為機器學習模型評分

使用 70% 的資料來定型模型後，您即可將該模型用來為其他 30% 的資料評分，以了解模型的運作是否理想。

1. 在搜尋方塊中輸入*評分模型*以尋找**評分模型**模組。 將此模組拖曳到管線畫布上。 

1. 將**訓練模型**模組的輸出連線至**評分模型**的左側輸入連接埠。 將**分割資料**模組的測試資料輸出 (右側連接埠) 連線至**評分模型**的右側輸入連接埠。

## <a name="evaluate-a-machine-learning-model"></a>評估機器學習模型

使用**評估模型**模組，評估您的模型在測試資料集下的評分。

1. 在搜尋方塊中輸入*評估*，以尋找**評估模型**模組。 將此模組拖曳到管線畫布上。 

1. 將**評分模型**模組的輸出連線至**評估模型**的左側輸入。 

    最終的管線應會顯示如下：

    ![此螢幕擷取畫面顯示管線的正確組態。](./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png)

## <a name="run-the-pipeline"></a>執行管道

[!INCLUDE [aml-ui-create-training-compute](../../includes/aml-ui-create-training-compute.md)]

### <a name="view-scored-labels"></a>檢視評分標籤

執行完成後，您可以檢視管線執行的結果。 首先，請查看迴歸模型產生的預測。

1. 選取 [評分模型]  模組以檢視其輸入。

1. 在 [屬性] 窗格中選取 [輸出]  > 圖形圖示 ![視覺化圖示](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png)，以檢視結果。

    您可以在這裡看到測試資料中的預測價格和實際價格。

    ![此螢幕擷取畫面將「評分標籤」資料行醒目提示的輸出視覺效果](./media/tutorial-designer-automobile-price-train-score/score-result.png)

### <a name="evaluate-models"></a>評估模型

使用**評估模型**，查看定型模型對測試資料集的執行效果。

1. 選取 [評估模型]  模組以檢視其輸入。

1. 在 [屬性] 窗格中選取 [輸出]  > 圖形圖示 ![視覺化圖示](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png)，以檢視結果。

您的模型會顯示下列統計資料：

* **平均絕對誤差 (MAE)** ：絕對誤差的平均值。 誤差是指預測值與實際值之間的差異。
* **均方根誤差 (RMSE)** ：對測試資料集所做之預測的平方誤差的評分根平均值。
* **相對絕對誤差**：相對於實際值與所有實際值之平均值之間的絕對差異的絕對誤差平均值。
* **相對平方誤差**：相對於實際值與所有實際值之平均值之間的平方差異的平方誤差平均值。
* **決定係數**：也稱為 R 平方值，這是一個統計計量，可指出模型對於資料的適用程度。

針對每個誤差統計資料，越小越好。 值越小，表示預測越接近實際值。 就決定係數而言，其值愈接近一 (1.0)，預測就愈精準。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程的第一部分中，您已完成下列工作：

* 建立管線
* 準備資料
* 將模型定型
* 對模型進行評分和評估

在第二部分中，您將了解如何將模型部署為即時端點。

> [!div class="nextstepaction"]
> [繼續部署模型](tutorial-designer-automobile-price-deploy.md)
