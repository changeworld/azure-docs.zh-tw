---
title: 教學課程：使用設計工具部署 ML 模型
titleSuffix: Azure Machine Learning
description: 在 Azure Machine Learning 設計工具中建置預測性分析解決方案。 使用拖放模組進行機器學習模型的訓練、評分和部署。
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: 6bba5ad17cbb6f1ed72d06b37c6d6af9ebd26495
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246463"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>教學課程：透過設計工具部署機器學習模型


您可以部署在[教學課程第一部分](tutorial-designer-automobile-price-train-score.md)中開發的預測模型，讓其他人有機會加以使用。 在第一部分中，您已將模型定型。 現在，是時候根據使用者輸入來產生新預測了。 在教學課程的這個部分中，您會：

> [!div class="checklist"]
> * 建立即時推斷管線。
> * 建立推斷叢集。
> * 部署即時端點。
> * 測試即時端點。

## <a name="prerequisites"></a>必要條件

完成[教學課程的第一個部分](tutorial-designer-automobile-price-train-score.md)，了解如何在設計工具中定型和評分機器學習模型。

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>建立即時推斷管線

若要部署您的管線，您必須先將訓練管線轉換成即時推斷管線。 此程序會移除訓練模組，並新增 Web 服務輸入和輸出以處理要求。

### <a name="create-a-real-time-inference-pipeline"></a>建立即時推斷管線

1. 在管線畫布上方，選取 [建立推斷管線] > [即時推斷管線]。

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="顯示要在哪裡尋找建立管線按鈕的螢幕擷取畫面":::

    您的管線此時應會顯示如下： 

   ![此螢幕擷取畫面顯示管線在做好部署準備後的預期組態](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    當您選取 [建立推斷管線] 時，會發生若干情況：
    
    * 定型的模型會儲存為模組選擇區中的 **資料集** 模組。 您可以在 [我的資料集] 下方找到該項目。
    * 訓練模組 (例如 **訓練模型** 和 **分割資料**) 會被移除。
    * 儲存的定型模型會加回管線中。
    * 會新增 **Web 服務輸入** 和 **Web 服務輸出** 模組。 這些模組會顯示使用者資料將在何處進入管線，以及資料會在何處傳回。

    > [!NOTE]
    > 根據預設，**Web 服務輸入** 會預期與用來建立預測管線的訓練資料相同的資料結構描述。 在此情況下，價格會包含在結構描述中。 不過，在預測期間並不會以價格作為因素。
    >

1. 選取 [提交]，並使用您在第一部分中使用的相同計算目標和實驗。

    如果這是第一次執行，您的管線可能需要 20 分鐘的時間才能完成執行。 預設計算設定的最小節點大小為 0，這表示設計工具必須在閒置之後配置資源。 重複的管線執行花費較少的時間，因為已經配置計算資源。 此外，設計工具會針對每個模組使用快取的結果，進一步提升效率。

1. 選取 [部署]。

## <a name="create-an-inferencing-cluster"></a>建立推斷叢集

在出現的對話方塊中，您可以從任何現有的 Azure Kubernetes Service (AKS) 叢集進行選取，以將模型部署至其中。 如果您沒有 AKS 叢集，請使用下列步驟建立一個。

1. 在出現的對話方塊中選取 [計算]，以移至 [計算] 頁面。

1. 在導覽功能區中，選取 [推斷叢集] > [+ 新增]。

    ![顯示如何移至 [新增推斷叢集] 窗格的螢幕擷取畫面](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. 在推斷叢集窗格中，設定新的 Kubernetes 服務。

1. 輸入 aks-compute 作為 **計算名稱**。
    
1. 選取附近的適用區域作為 **區域**。

1. 選取 [建立]。

    > [!NOTE]
    > 建立新的 AKS 服務需要約 15 分鐘。 您可以在 [推斷叢集] 頁面上查看佈建狀態。
    >

## <a name="deploy-the-real-time-endpoint"></a>部署即時端點

在您的 AKS 服務完成佈建後，請回到即時推斷管線以完成部署。

1. 選取畫布上方的 [部署]。

1. 選取 [部署新的即時端點]。 

1. 選取您建立的 AKS 叢集。

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="顯示如何設定新即時端點的螢幕擷取畫面":::

    您也可以變更即時端點的 **Advanced** 設定。
    
    |Advanced 設定|說明|
    |---|---|
    |啟用 Application Insights 診斷和資料收集| 是否要啟用 Azure 應用程式 Ingishts，以便從已部署的端點收集資料。 </br> 預設值： false |
    |評分超時| 對 web 服務評分呼叫強制執行的超時（以毫秒為單位）。</br>預設值：60000|
    |自動調整已啟用|   是否為 Web 服務啟用自動調整。</br>預設值： true|
    |最小複本| 自動調整此 Web 服務時，要使用的容器數目下限。</br>預設值：1|
    |最大複本| 自動調整此 Web 服務時，要使用的容器數目上限。</br> 預設值：10|
    |目標使用率|自動調整器應嘗試為此 Web 服務維持的目標使用率 (以百分比為單位，最高 100)。</br> 預設值：70|
    |重新整理期間|自動調整程式嘗試調整此 web 服務的 (頻率，以秒為單位) 。</br> 預設值：1|
    |CPU 保留容量|要為此 Web 服務配置的 CPU 核心數目。</br> 預設值：0。1|
    |記憶體保留容量|要為此 Web 服務配置的記憶體 (GB) 數量。</br> 預設值：0。5|
        

1. 選取 [部署]。 

    完成部署之後，畫布上方會出現成功通知。 這可能需要幾分鐘的時間。

> [!TIP]
> 如果您在 [即時端點設定] 方塊中選取 [**計算類型** 的 **azure 容器實例**]，您也可以將 **AZURE 容器實例** 部署 (ACI) 。
> Azure 容器實例用於測試或開發。 針對需要少於 48 GB RAM 的低規模 CPU 工作負載，請使用 ACI。

## <a name="view-the-real-time-endpoint"></a>檢視即時端點

部署完成後，您即可移至 [端點] 頁面，以檢視您的即時端點。

1. 在 [端點] 頁面上，選取您部署的端點。

1. 在 [詳細資料] 索引標籤中，您可以查看更多資訊，例如 REST URI、狀態和標記。

1. 在 [取用] 索引標籤中，您可找到安全性金鑰及設定驗證方法。

1. 在 [部署記錄] 索引標籤中，您可以找到即時端點的詳細部署記錄。 

如需取用 Web 服務的詳細資訊，請參閱[取用部署為 Web 服務的模型](how-to-consume-web-service.md)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解在如何設計工具中建立、部署和使用機器學習模型的關鍵步驟。 若要深入了解您可以如何使用設計工具，請參閱下列連結：

+ [設計工具範例](samples-designer.md)：了解如何使用設計工具來解決其他類型的問題。
+ [在 Azure 虛擬網路中使用 Azure Machine Learning 工作室](how-to-enable-studio-virtual-network.md)。