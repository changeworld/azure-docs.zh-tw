---
title: Two-Class 羅吉斯回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 Two-Class 羅吉斯回歸模組來建立羅吉斯回歸模型，此模型可用來預測兩個 (，以及僅兩個) 結果。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 525293c7ae3d0af49f6deaa0ce9d3cb037d9ba38
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907699"
---
# <a name="two-class-logistic-regression-module"></a>Two-Class 羅吉斯回歸模組

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組來建立羅吉斯回歸模型，以用來預測兩個 (以及只有兩個) 的結果。 

羅吉斯回歸是知名的統計技術，可用來建立許多種類問題的模型。 此演算法是一種受 *監督的學習* 方法; 因此，您必須提供已經包含結果的資料集來定型模型。  

### <a name="about-logistic-regression"></a>關於羅吉斯回歸  

羅吉斯回歸是在統計資料中已知的方法，可用來預測結果的機率，並且特別受限於分類工作。 此演算法會將資料放在羅吉斯函式中，以預測事件發生的機率。
  
在本課程模組中，分類演算法已針對二分或二進位變數進行優化。 如果您需要分類多個結果，請使用 [多元羅吉斯回歸](./multiclass-logistic-regression.md) 模組。

##  <a name="how-to-configure"></a>如何設定  

若要定型此模型，您必須提供包含標籤或類別資料行的資料集。 因為此模組適用于兩個類別的問題，所以標籤或類別資料行必須正好包含兩個值。 

例如，標籤資料行可能是 [投票]，其可能的值為 "Yes" 或 "No"。 或者，它可能是 [信用風險]，其可能的值為 [高] 或 [低]。 
  
1.  將 **兩個類別的羅吉斯回歸** 模組新增至您的管線。  
  
2.  藉由設定 [ **建立定型模式]** 選項，指定您要如何定型模型。  
  
    -   **單一參數**：如果您知道要如何設定模型，您可以提供一組特定值做為引數。  

    -   **參數範圍**：如果您不確定最佳參數，您可以使用 [ [微調模型超參數](tune-model-hyperparameters.md) ] 模組來尋找最佳參數。 您可以提供某些範圍的值，而講師會反復查看多個設定組合，以判斷產生最佳結果的值組合。
  
3.  針對 **優化容錯**，指定優化模型時要使用的臨界值。 如果反復專案之間的改進低於指定的臨界值，則演算法會被視為在解決方案上具有交集，且定型會停止。  
  
4.  若為 **l1 正規化權數** 和 **l2 正規化權數**，請輸入要用於正規化參數 L1 和 l2 的值。 非零值建議用於兩者。  
     *正規化* 是一種方法，可透過具有極端係數值的 penalizing 模型來防止過度學習。 正規化的運作方式是將與係數值相關聯的損失新增至假設的錯誤。 因此，具有極端係數值的精確模型會懲罰更多，但具有更保守值的較不精確模型會懲罰較少。  
  
     L1 與 L2 regularization 有不同的效果，並使用。  
  
    -   L1 可以套用到疏鬆的模型，使用高維度資料時，這是很有用。  
  
    -   相較之下，L2 regularization 是不是疏鬆的資料。  
  
     此演算法支援 L1 和 L2 正規化值的線性組合：亦即，如果 <code>x = L1</code> 和 <code>y = L2</code> ，則會 <code>ax + by = c</code> 定義正規化詞彙的線性範圍。  
  
    > [!NOTE]
    >  想要深入瞭解 L1 和 L2 正規化嗎？ 下列文章提供 L1 和 L2 正規化的不同之處，以及它們如何影響模型調整，以及適用于羅吉斯回歸和類神經網路模型的程式碼範例：  [L1 和 L2 正規化的 Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > 已為羅吉斯回歸模型設計了 L1 和 L2 詞彙的不同線性組合：例如， [彈性的 net 正規化](https://wikipedia.org/wiki/Elastic_net_regularization)。 建議您參考這些組合，以定義在模型中有效的線性組合。
      
5.  針對 **BFGS 的記憶體大小**，指定用於 *l BFGS* 優化的記憶體數量。  
  
     L BFGS 代表「有限的記憶體 Broyden-Fletcher-Goldfarb-Shanno」。 這是常用於參數估計的最佳化演算法。 此參數指出要儲存的通過位置和梯度數目，以計算下一個階梯。  
  
     此最佳化參數限制用來計算下一個步驟和方向的記憶體數量。 當您指定較少的記憶體時，訓練是更快，但較不精確。  
  
6.  針對 [ **亂數字種子**]，輸入整數值。 如果您想要讓結果在多個相同管線的執行之間重現，則定義種子值是很重要的。  
  
  
8. 將加上標籤的資料集新增至管線，並將模型定型：

    + 如果您將 [ **建立定型模式** ] 設定為 [ **單一參數**]，請連接已標記的資料集和「 [定型模型](train-model.md) 」模組。  
  
    + 如果您將 [ **建立定型模式** ] 設定為 [ **參數範圍**]，請連接已加上標籤的資料集，並使用 [微調模型超參數](tune-model-hyperparameters.md)來定型模型。  
  
    > [!NOTE]
    > 
    > 如果您將參數範圍傳遞給 [定型模型](train-model.md)，它只會使用單一參數清單中的預設值。  
    > 
    > 如果您將一組參數值傳遞至 [微調模型超參數](tune-model-hyperparameters.md) 模組，當它預期每個參數的設定範圍時，會忽略這些值，並使用學習模組的預設值。  
    > 
    > 如果您選取 [ **參數範圍** ] 選項，並輸入任何參數的單一值，則整個清除期間都會使用您所指定的單一值，即使其他參數會在某個範圍的值之間變更。  
  
9. 提交管線。  
  
## <a name="results"></a>結果

定型完成後：
 
  
+ 若要對新資料進行預測，請使用定型的模型和新的資料做為 [計分模型](./score-model.md) 模組的輸入。 


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 