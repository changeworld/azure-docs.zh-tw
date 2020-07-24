---
title: 波氏回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用波氏回歸模組來建立波氏回歸模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 6682d9426ed3fe011fe5c493ec34fcdf0a2b35b7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096167"
---
# <a name="poisson-regression"></a>波氏迴歸

本文說明 Azure Machine Learning 設計工具 (預覽) 中的模組。

使用此模組在管線中建立波氏回歸模型。 波氏回歸是用於預測數值，通常是計數。 因此，只有當您嘗試預測的值符合下列條件時，您才應該使用此模組來建立回歸模型：

- 回應變數具有波氏[分佈](https://en.wikipedia.org/wiki/Poisson_distribution)。  

- 計數不可為負數。 如果您嘗試使用具有負數標籤的模型，此方法會立即失敗。

- 波氏分佈是離散散發;因此，使用此方法時，不會有任何意義。

> [!TIP]
> 如果您的目標不是計數，則波氏迴歸可能不是適當的方法。 [在設計工具中嘗試其他回歸模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference#machine-learning-algorithms)。 

設定回歸方法之後，您必須使用包含您想要預測之值範例的資料集來定型模型。 然後，定型的模型就可用來進行預測。

## <a name="more-about-poisson-regression"></a>關於波氏回歸的詳細資訊

波氏迴歸是一種特殊的迴歸分析，通常用來模擬計數。 例如，波氏迴歸可用於以下案例：

- 模型化乘坐飛機相關的感冒次數

- 估計事件期間的緊急服務呼叫數目

- 投射升級之後的客戶查詢數目

- 建立列聯表

因為回應變數具有波氏分佈，所以模型會對資料及其機率分佈做出不同的假設，而不是最小平方回歸。 因此，應該以不同于其他回歸模型的方式來解讀波氏模型。

## <a name="how-to-configure-poisson-regression"></a>如何設定波氏回歸

1. 將 [波氏**回歸**] 模組加入至您在設計工具（預覽）中的管線。 您可以在 [**回歸**] 分類的 [ **Machine Learning 演算法**] 底下找到此模組。

2. 加入包含正確類型之定型資料的資料集。 

    我們建議您先使用[標準化資料](normalize-data.md)來將輸入資料集正規化，再使用它來定型回歸輸入變數。

3. 在 [波氏**回歸**] 模組的右窗格中，藉由設定 [建立定型者**模式]** 選項，指定您要如何訓練模型。  
  
    - **單一參數**：如果您知道要如何設定模型，請提供一組特定值做為引數。
  
    - **參數範圍**：如果您不確定最佳參數，請使用[微調模型超參數](tune-model-hyperparameters.md)模組來進行參數清理。 訓練員會逐一查看您指定的多個值，以找出最佳設定。
  
4. **優化容錯**：輸入在優化期間定義容錯間隔的值。 值愈小，配適就較慢，但愈精確。

5. **L1 正規化權數**和**l2 正規化權數**：要用於 L1 和 l2 正規化的輸入值。 *正則化*會將限制加入至與模型各方面有關的演算法，這些方面與定型資料無關。 正則化通常用來避免過度配適。 

    - 如果目標是讓模型儘可能稀疏，則 L1 正則化很有用。

        L1 正則化的做法是將學習模組嘗試最小化的損失運算式減去權重向量的 L1 權數。 L1 範數是 L0 範數的良好近似值 (非零座標數)。

    - L2 正則化可防止加權向量中的任何單一座標量級成長太多。 如果目標是要擁有具備小的整體加權的模型，則 L2 正則化相當有用。

    在此模組中，您可以套用 L1 與 L2 正則化的組合。 藉由結合 L1 和 L2 正規化，您可以對參數值的大小施加負面影響。 學習模組會嘗試將懲罰降到最低，以換取最低損失。

    如需 L1 和 L2 正規化的絕佳討論，請參閱[l1 和 L2 正規化 for Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)。

6. **BFGS 的記憶體大小**：指定要保留給模型調整和優化的記憶體數量。

     L BFGS 是根據 Broyden – Fletcher – Goldfarb – Shanno （BFGS）演算法優化的特定方法。 方法會使用有限的記憶體數量（L）來計算下一個步驟方向。

     藉由變更這個參數，您可以影響儲存來計算下一個步驟的過去位置和漸層數目。

7. 將訓練資料集和未定型的模型連接到其中一個定型模組： 

    - 如果您將 [**建立訓練員模式]** 設定為 [**單一參數**]，請使用 [[定型模型](train-model.md)] 模組。

    - 如果您將 [**建立訓練員模式]** 設定為 [**參數範圍**]，請使用 [[微調模型超參數](tune-model-hyperparameters.md)] 模組。

    > [!WARNING]
    > 
    > - 如果您將參數範圍傳遞至[定型模型](train-model.md)，它只會使用 [參數範圍] 清單中的第一個值。
    > 
    > - 如果您將一組參數值傳遞至[微調模型超參數](tune-model-hyperparameters.md)模組，當它預期每個參數的設定範圍時，就會忽略這些值，並使用學習模組的預設值。
    > 
    > - 如果您選取 [**參數範圍**] 選項，並輸入任何參數的單一值，則會在整個清除中使用您指定的單一值，即使其他參數在某個範圍的值之間變更也是如此。

8.  提交管線。

## <a name="results"></a>結果

定型完成後：

+ 若要儲存定型模型的快照集，請選取定型模組，然後切換至右面板中的 [**輸出 + 記錄**] 索引標籤。 按一下 [**註冊資料集**] 圖示。  您可以在模組樹狀結構中，找到已儲存的模型作為模組。 

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
