---
title: ML Studio (傳統版) 教學課程 2：定型信用風險模型 - Azure
description: 一個詳盡的教學課程，說明如何在 Azure Machine Learning (傳統) 中建立適用於信用風險評估的預測性分析解決方案。 本教學課程是三部分教學課程系列的第二部分。 其內容會說明如何訓練和評估模型。
keywords: 信用風險, 預測性分析解決方案, 風險評估
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: c2782d15d8be82d07f14f7ada5732b1dbef699c5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91337784"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>教學課程 2：訓練信用風險模型 - Azure Machine Learning Studio (傳統)

**適用於：** ![是](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (傳統版)![否](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)


在本教學課程中，您將進一步了解開發預測性分析解決方案的程序。 您將在 Machine Learning Studio (傳統) 中建立簡單的模型。  接著，您會將該模型部署為 Azure Machine Learning Web 服務。  這個已部署的模型可使用新資料進行預測。 本教學課程是**三部分教學課程系列的第二部分**。

假設您必須根據某個人在信用申請書上提供的資訊預測其信用風險。  

信用風險評估是一個複雜的問題，但本教學課程將稍微加以簡化。 您將使用它作為範例，以說明如何使用 Microsoft Azure Machine Learning Studio (傳統) 建立預測性分析解決方案。 針對此解決方案，您將使用 Azure Machine Learning Studio (傳統) 和 Machine Learning Web 服務。  

在此三部分的教學課程中，您將從可公開取得的信用風險資料開始著手。  接著，您將開發並訓練預測模型。  最後，您會將模型部署為 Web 服務。

在[本教學課程的第一部分](tutorial-part1-credit-risk.md)，您已建立 Machine Learning Studio (傳統) 工作區、上傳資料，以及建立實驗。

在教學課程的這個部分中，您將：
 
> [!div class="checklist"]
> * 訓練多個模型
> * 計分及評估模型


在[本教學課程的第三部分](tutorial-part3-credit-risk-deploy.md)，您會將模型部署為 Web 服務。

## <a name="prerequisites"></a>必要條件

完成[本教學課程的第一部分](tutorial-part1-credit-risk.md)。

## <a name="train-multiple-models"></a><a name="train"></a>訓練多個模型

使用 Azure Machine Learning Studio (傳統) 來建立機器學習服務模型的優點之一，是能夠在單一實驗中一次嘗試多個模型類型，並比較結果。 這類實驗可協助您針對問題找到最佳解決方案。

在我們於這個教學課程中開發的實驗內，您將會建立兩種不同的模型，然後比較其計分結果，以決定要用於最終實驗的演算法。  

有各種不同的模型可供您選擇。 若要查看可用的模型，請在模組選擇區展開 [機器學習] 節點，然後展開 [初始化模型]，再選擇其下方的節點。 基於本實驗的目的，您會選取[二元支援向量機器][two-class-support-vector-machine] (SVM) 和[二元促進式決策樹][two-class-boosted-decision-tree]模組。

> [!TIP]
> 如需取得說明以決定哪一種機器學習服務演算法最適合您要嘗試解決的特定問題，請參閱[如何選擇 Microsoft Azure Machine Learning Studio (傳統) 的演算法](../how-to-select-algorithms.md)。
> 
> 

您將在此實驗中新增[二元促進式決策樹][two-class-boosted-decision-tree]模組和[二元支援向量機器][two-class-support-vector-machine]模組。

### <a name="two-class-boosted-decision-tree"></a>二元促進式決策樹

首先，設定促進式決策樹模型。

1. 在模組選擇區中找到[二元促進式決策樹][two-class-boosted-decision-tree]模組，將其拖曳到畫布上。

1. 找到[訓練模型][train-model]模組，將它拖曳到畫布上，然後將[二元促進式決策樹][two-class-boosted-decision-tree]模組的輸出連線到[訓練模型][train-model]模組的左側輸入連接埠。
   
   [二元促進式決策樹][two-class-boosted-decision-tree]模組會將一般模組初始化，而[訓練模型][train-model]則會使用訓練資料來訓練模型。 

1. 將左側[執行 R 指令碼][execute-r-script]模組的左輸出連接到[訓練模型][train-model]模組的右側輸入連接埠 (您在本教學課程[使用了「分割資料」模組左側所傳來的資料](#train)來進行訓練)。
   
   > [!TIP]
   > 在這項實驗中，您不需要[執行 R 指令碼][execute-r-script]模組的兩個輸入和一個輸出，因此可以讓它們保持未連結。 
   > 
   > 

實驗的這部分目前看起來如下：  

![Training a model](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

現在您要告訴[訓練模型][train-model]模組，您要讓模型預測信用風險值。

1. 選取 [訓練模型][train-model] 模組。 按一下 [屬性] 窗格中的 [啟動資料行選取器]。

1. 在 [選取單一資料行] 對話方塊中，在 [可用的資料行] 下的 [搜尋] 欄位中輸入「信用風險」，然後選取下方的 [信用風險]，按一下向右箭號按鈕 ( **>** ) 將 [信用風險] 移至 [選取的資料行]。 

    ![為訓練模型模組選取信用風險資料行](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. 按一下 **[確定]** \(打勾記號)。

### <a name="two-class-support-vector-machine"></a>二元支援向量機器

接下來，您要設定 SVM 模型。  

首先，簡單說明一下 SVM。 強化的決策樹適合處理任何類型的特性。 不過，因為 SVM 模組會產生線性分類器，而它所產生的模型在所有數值特性都有相同的尺度時，將具有最佳檢定誤差。 為了將所有數值特徵轉換成相同的尺度，您要使用 Tanh 轉換 (搭配[標準化資料][normalize-data]模組)。 這會將我們的數字轉換到 [0,1] 範圍內。 SVM 模組會將字串特徵轉換為類別特性，再轉換為二進位 0/1 特徵，因此您無須手動轉換字串特徵。 此外，您不想要轉換 [信用風險] 資料行 (資料行 21) - 它是數值，但這是您訓練模型來預測的值，因此必須維持原狀。  

若要設定 SVM 模型，請執行下列動作：

1. 在模組選擇區中找到[二元支援向量機器][two-class-support-vector-machine]模組，將它推曳到畫布上。

1. 以滑鼠右鍵按一下[訓練模型][train-model]模組，選取 [複製]，然後以滑鼠右鍵按一下畫布並選取 [貼上]。 [訓練模型][train-model]模組複本的資料行選擇與原始模組相同。

1. 將[二元支援向量機器][two-class-support-vector-machine]模組的輸出連線到第二個[訓練模型][train-model]模組的左側輸入連接埠。

1. 找到[標準化資料][normalize-data]模組，將其拖曳到畫布上。

1. 將左側 [[執行 R 指令碼]][execute-r-script] 模組的左側輸出連接到此模組的輸出 (請注意，模組的輸出連接埠可能連接到多個其他模組)。

1. 將[標準化資料][normalize-data]模組的左側輸出連接埠連線到第二個[訓練模型][train-model]模組的右側輸入連接埠。

實驗的這部分目前看起來如下：  

![Training the second model](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

現在，設定[標準化資料][normalize-data]模組︰

1. 按一下以選取[標準化資料][normalize-data]模組。 在 [屬性] 窗格中，選取 [Tanh] 做為 [轉換方法] 參數。

1. 按一下 [啟動資料行選取器]，選取 [開始於] 的 [無資料行]，在第一個下拉式清單中選取 [包含]，在第二個下拉式清單中選取 [資料行類型]，然後在第三個下拉式清單中選取 [數值]。 這樣會指定轉換所有數值資料行 (且僅限數值)。

1. 按一下此資料列右側的加號 (+) - 這會建立一排下拉式清單。 在第一個下拉式清單中選取 [排除]，在第二個下拉式清單中選取 [資料行名稱]，然後在文字欄位中輸入「信用風險」。 這會指定應忽略 [信用風險] 資料行 (需要這麼做是因為此資料行為數值，如未排除，它會被轉換)。

1. 按一下 **[確定]** \(打勾記號)。  

    ![選取標準化資料模組的資料行](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


[標準化資料][normalize-data]模組現在已設定為在所有數值資料行上執行 Tanh 轉換 ([信用風險] 資料行除外)。  

## <a name="score-and-evaluate-the-models"></a>計分及評估模型

您將使用由[資料分割][split]模組所分開的測試資料，給我們訓練的模型評分。 然後，您就可以比較兩個模型的結果，了解何者產生的結果較佳。  

### <a name="add-the-score-model-modules"></a>新增評分模型模組

1. 找到[評分模型][score-model]模組並拖曳到畫布上。

1. 將已連線到[二元促進式決策樹][two-class-boosted-decision-tree]模組的[訓練模型][train-model]模組，連線到[評分模型][score-model]模組的左側輸入連接埠。

1. 將右側[執行 R 指令碼][execute-r-script]模組 (或測試資料) 連線到[評分模型][score-model]模組的右側輸入連接埠。

    ![已連接評分模型模組](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   [評分模型][score-model]模組可以立即從測試資料中採取信用資訊，並且將模型產生的預測情況與測試資料中的實際信用風險資料行進行比較。

1. 複製並貼上[評分模型][score-model]模組來建立第二個複本。

1. 將 SVM 模型的輸出 (亦即，已連接到 [[二元支援向量機器]][two-class-support-vector-machine] 的 [[訓練模型]][train-model] 模組的輸出連接埠)，連接到第二個 [[評分模型]][score-model] 模組的輸入連接埠。

1. 在 SVM 模型中，您必須像是轉換訓練資料一樣，對測試資料進行相同的轉換。 因此，請複製並貼上[標準化資料][normalize-data]模組來建立第二個複本，再將其連線到右邊[執行 R 指令碼][execute-r-script]模組。

1. 將第二個[標準化資料][normalize-data]模組的左邊輸出連線到第二個[評分模型][score-model]模組的右邊輸入連接埠。

    ![已連接兩個評分模型模組](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>新增評估模型模組

為了評估兩個評計分結果並加以比較，您要使用[評估模型][evaluate-model]模組。  

1. 找到[評估模型][evaluate-model]模組並拖曳到畫布上。

1. 將與促進式決策樹模型相關聯的[評分模型][score-model]模組的輸出連接埠，連線到[評估模型][evaluate-model]模組的左側輸入連接埠。

1. 將其他[評分模型][score-model]模組連線到右側輸入連接埠。  

    ![已連接評估模型模組](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>執行實驗並檢查結果

若要執行實驗，請按一下畫布下方的 [執行] 按鈕。 可能需要數分鐘的時間。 每個模組上的旋轉指示器表示正在執行，模組完成時會出現綠色打勾記號。 當所有模組都出現核取記號時，表示實驗執行完成。

實驗目前看起來如下：  

![Evaluating both models](./media/tutorial-part2-credit-risk-train/final-experiment.png)


若要檢查結果，按一下[評估模型][evaluate-model]模組的輸出連接埠，然後選取 [視覺化]。  

[評估模型][evaluate-model]模組會產生一對曲線和度量，讓您比較兩個評分模型的結果。 您可以將結果顯示成「受測者操作特徵 (ROC)」曲線、「正確性/召回」曲線或「升力」曲線。 其他顯示的資料還包括混淆矩陣、曲線下面積 (AUC) 的累計值，以及其他度量。 您可以將滑動軸左右移動來變更臨界值，觀察這樣如何影響度量組。  

在圖形的右邊，按一下 [已計分的資料集] 或 [要比較的已計分資料集]，以醒目提示相關聯的曲線，並在下方顯示相關聯的度量。 在曲線的圖例中，"Scored dataset" 對應至 [[評估模型]][evaluate-model] 模組的左側輸入埠 - 在我們的案例中，這是促進式決策樹模型。 「要比較的已計分資料集」會對應至右側輸入連接埠 (在本例中為 SVM 模型)。 按一下其中一個標籤時，該模型的曲線會反白顯示，並顯示如下圖所示的相對應度量。  

![ROC curves for models](./media/tutorial-part2-credit-risk-train/roc-curves.png)

您可以檢查這些值，以判斷哪個模型最可能提供您想要的結果。 您可以返回並變更不同模型中的參數值，以反覆執行實驗。 

解讀這些結果和微調模型效能的藝術與科學超出本教學課程的範圍。 如需更多說明，請參閱下列文章：
- [如何在 Azure Machine Learning Studio (傳統) 中評估模型效能](evaluate-model-performance.md)
- [選擇參數來最佳化 Azure Machine Learning Studio (傳統) 中的演算法](algorithm-parameters-optimize.md)
- [在 Azure Machine Learning Studio (傳統) 中解譯模型結果](interpret-model-results.md)

> [!TIP]
> 每次執行實驗，[執行歷程記錄] 中就會保留該筆逐一查看的記錄。 您可以檢視這些反覆運算，按一下畫布下方的 [檢視執行歷程記錄]  即可回到其中任何一個。 您也可以按一下 [屬性] 窗格中的 [先前執行]，回到您目前開啟的反覆運算之前的那一個反覆運算。
> 
> 您可以按一下畫布下方的 [另存新檔]  ，為實驗的任何反覆項目製作一個複本。 
> 使用實驗的 [摘要] 和 [描述] 屬性，以記錄在您實驗反覆運算中已嘗試的動作。
> 
> 如需詳細資訊，請參閱[在 Azure Machine Learning Studio (傳統) 中管理實驗反覆運算](manage-experiment-iterations.md)。  
> 
> 

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已完成下列步驟： 
 
> [!div class="checklist"]
> * 建立實驗
> * 訓練多個模型
> * 計分及評估模型

您現在可以開始針對此資料部署模型。

> [!div class="nextstepaction"]
> [教學課程 3 - 部署模型](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/