---
title: 選取資料行轉換：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 表設計工具中使用 [選取資料行轉換] 模組來執行 select 轉換。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f03840e55366d7f105ca4b57bd60061c82833e72
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420711"
---
# <a name="select-columns-transform"></a>選取資料行轉換

本文描述如何在 Azure Machine Learning 表設計工具中使用 [選取資料行轉換] 模組。 「選取資料行」轉換模組的目的是要確保下游機器學習作業中使用的是可預測且一致的資料行集。

此模組適用于需要特定資料行的評分等工作。 可用資料行中的變更可能會中斷管線或變更結果。

您可以使用 [選取資料行] 轉換來建立和儲存一組資料行。 然後，使用 [套用 [轉換](apply-transformation.md) ] 模組將這些選項套用至新的資料。

## <a name="how-to-use-select-columns-transform"></a>如何使用選取資料行轉換

此案例假設您想要使用特徵選取來產生一組將用來定型模型的動態資料行。 為確保評分程式的資料行選取專案相同，您可以使用 [選取資料行轉換] 模組來捕捉資料行選取專案，並將其套用至管線中的其他位置。

1. 在設計工具中，將輸入資料集加入至您的管線。

2. 加入以 [篩選器為基礎的特徵選取](filter-based-feature-selection.md)實例。

3. 連接模組並設定特徵選取模組，以自動在輸入資料集中尋找一些最佳功能。

4. 加入 [定型模型](train-model.md) 的實例，並使用以 [篩選器為基礎的特徵選取](filter-based-feature-selection.md) 輸出作為定型的輸入。

    > [!IMPORTANT]
    > 因為特徵重要性是根據資料行中的值，所以您事先無法事先知道哪些資料行可用於 [定型模型](train-model.md)的輸入。  
5. 附加「選取資料行」轉換模組的實例。 

    此步驟會產生一個資料行選取專案，做為可儲存或套用到其他資料集的轉換。 此步驟可確保儲存特徵選取中所識別的資料行，以供其他要重複使用的模組使用。

6. 新增 [計分模型](score-model.md) 模組。 

   *請勿連接輸入資料集。* 請改為加入「套用 [轉換](apply-transformation.md) 」模組，然後連接「特徵選取」轉換的輸出。

   管線結構應如下所示：

   > [!div class="mx-imgBorder"]
   > ![範例管線](media/module/filter-based-feature-selection-score.png)

   > [!IMPORTANT]
   > 您不應該預期會將以 [篩選器為基礎的特徵選取](filter-based-feature-selection.md) 套用至計分資料集，並取得相同的結果。 因為特徵選取是以值為基礎，所以它可能會選擇一組不同的資料行，這會導致計分作業失敗。
    
7. 提交管線。

儲存然後套用資料行選取專案的這個程式，可確保相同的資料結構描述可供定型和評分之用。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
