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
ms.openlocfilehash: a1a09357c7f80d4af0198a33a2e0007782ef232f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905253"
---
# <a name="poisson-regression"></a>波氏迴歸

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組來建立管線中的波氏回歸模型。 波氏回歸是用於預測數值（通常是計數）。 因此，只有在您嘗試預測的值符合下列條件時，才應該使用此模組來建立回歸模型：

- 回應變數具有波氏 [分佈](https://en.wikipedia.org/wiki/Poisson_distribution)。  

- 計數不可為負數。 如果您嘗試使用具有負數標籤的模型，此方法會立即失敗。

- 波氏分佈是離散分佈;因此，將此方法與非整數搭配使用並沒有意義。

> [!TIP]
> 如果您的目標不是計數，則波氏迴歸可能不是適當的方法。 [在設計工具中嘗試其他回歸模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference#machine-learning-algorithms)。 

設定回歸方法之後，您必須使用包含您想要預測之值範例的資料集來定型模型。 然後，定型的模型就可用來進行預測。

## <a name="more-about-poisson-regression"></a>關於波氏回歸的詳細資訊

波氏迴歸是一種特殊的迴歸分析，通常用來模擬計數。 例如，波氏迴歸可用於以下案例：

- 模型化乘坐飛機相關的感冒次數

- 估計事件期間的緊急服務呼叫數目

- 投射升級後續的客戶查詢數目

- 建立列聯表

由於回應變數具有波氏分佈，因此，此模型會對資料及其機率分佈進行不同的假設，而不是使用最小平方回歸。 因此，您應該以不同于其他回歸模型的方式來解讀波氏模型。

## <a name="how-to-configure-poisson-regression"></a>如何設定波氏回歸

1. 在 [設計師] 中將 [波氏 **回歸** ] 模組新增至您的管線。 您可以在**回歸**分類的**Machine Learning 演算法**下找到此模組。

2. 加入包含正確類型之定型資料的資料集。 

    建議您先使用 [標準化資料](normalize-data.md) 來正規化輸入資料集，再使用它來定型回歸輸入變數。

3. 在 [波氏 **回歸** ] 模組的右窗格中，藉由設定 [ **建立定型模式]** 選項，指定您要如何定型模型。  
  
    - **單一參數**：如果您知道要如何設定模型，請提供一組特定值做為引數。
  
    - **參數範圍**：如果您不確定最佳參數，請使用 [微調模型超參數](tune-model-hyperparameters.md) 模組來執行參數清理。 講師會反復查看您指定的多個值，以找出最佳設定。
  
4. **優化容錯**：輸入在優化期間定義容錯間隔的值。 值愈小，配適就較慢，但愈精確。

5. **L1 正規化權數** 和 **l2 正規化權數**：輸入用於 L1 和 l2 正規化的值。 *正則化*會將限制加入至與模型各方面有關的演算法，這些方面與定型資料無關。 正則化通常用來避免過度配適。 

    - 如果目標是讓模型儘可能稀疏，則 L1 正則化很有用。

        L1 正則化的做法是將學習模組嘗試最小化的損失運算式減去權重向量的 L1 權數。 L1 範數是 L0 範數的良好近似值 (非零座標數)。

    - L2 正則化可防止加權向量中的任何單一座標量級成長太多。 如果目標是要擁有具備小的整體加權的模型，則 L2 正則化相當有用。

    在此模組中，您可以套用 L1 與 L2 正則化的組合。 藉由結合 L1 和 L2 正規化，您可以對參數值的大小造成負面影響。 學習模組會嘗試將懲罰降到最低，以換取最低損失。

    如需 L1 和 L2 正規化的詳細討論，請參閱 [l1 和 L2 正規化的 Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)。

6. **L BFGS 的記憶體大小**：指定要保留給模型調整和優化的記憶體數量。

     BFGS 是特定的優化方法，根據 Broyden – Fletcher – Goldfarb – Shanno (BFGS) 演算法。 方法會使用有限數量的記憶體 (L) 來計算下一個步驟方向。

     藉由變更此參數，您可以影響為下一個步驟計算所儲存的過去位置和漸層數目。

7. 將訓練資料集和未定型的模型連接到其中一個定型模組： 

    - 如果您將 [ **建立定型模式]** 設定為 [ **單一參數**]，請使用「 [定型模型](train-model.md) 」模組。

    - 如果您將 [ **建立定型模式]** 設定為 [ **參數範圍**]，請使用 [ [微調模型超參數](tune-model-hyperparameters.md) ] 模組。

    > [!WARNING]
    > 
    > - 如果您將參數範圍傳遞給 [定型模型](train-model.md)，它只會使用 [參數範圍] 清單中的第一個值。
    > 
    > - 如果您將一組參數值傳遞至 [微調模型超參數](tune-model-hyperparameters.md) 模組，當它預期每個參數的設定範圍時，會忽略這些值並使用學習模組的預設值。
    > 
    > - 如果您選取 [ **參數範圍** ] 選項，並輸入任何參數的單一值，則整個清除期間都會使用您所指定的單一值，即使其他參數會在某個範圍的值之間變更。

8.  提交管線。

## <a name="results"></a>結果

定型完成後：

+ 若要儲存定型模型的快照集，請選取定型模組，然後切換至右面板中的 [ **輸出 + 記錄** ] 索引標籤。 按一下圖示 **註冊資料集**。  您可以在模組樹狀結構中，找到儲存的模型作為模組。 

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
