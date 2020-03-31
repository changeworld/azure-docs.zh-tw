---
title: 選擇列轉換：模組引用
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"選擇列變換"模組來創建與給定資料集中相同的列子集的轉換。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455905"
---
# <a name="select-columns-transform"></a>選取資料行轉換

本文介紹如何在 Azure 機器學習設計器（預覽）中使用"選擇列轉換"模組。 "選擇列轉換"模組的目的是確保下游機器學習操作中使用一組可預測的、一致的列。

此模組對於需要特定列的任務（如評分）非常有用。 可用列中的更改可能會破壞管道或更改結果。

您可以使用"選擇列變換"創建和保存一組列。 然後，使用[應用轉換](apply-transformation.md)模組將這些選擇應用於新資料。

## <a name="how-to-use-select-columns-transform"></a>如何使用選擇列變換

此方案假定您希望使用要素選擇生成一組動態列，用於訓練模型。 為確保評分過程的列選擇相同，請使用"選擇列變換"模組捕獲列選擇並將其應用於管道中的其他位置。

1. 在設計器中向管道添加輸入資料集。

2. 添加[基於篩選器的功能選擇](filter-based-feature-selection.md)實例。

3. 連接模組並配置要素選擇模組，以自動查找輸入資料集中的多個最佳功能。

4. 添加[訓練模型](train-model.md)的實例，並使用[基於篩選器的特徵選取](filter-based-feature-selection.md)輸出作為培訓的輸入。

    > [!IMPORTANT]
    > 由於要素重要性基於列中的值，因此無法提前知道哪些列可用於輸入[訓練模型](train-model.md)。  
5. 附加"選擇列變換"模組的實例。 

    此步驟將生成列選擇作為可保存或應用於其他資料集的轉換。 此步驟可確保保存在要素選擇中標識的列以供其他模組重用。

6. 添加[分數模型](score-model.md)模組。 

   *不要連接輸入資料集。* 而是添加[應用轉換](apply-transformation.md)模組，並連接要素選擇轉換的輸出。

   > [!IMPORTANT]
   > 您不能指望將[基於篩選器的功能選擇](filter-based-feature-selection.md)應用於評分資料集並獲取相同的結果。 由於要素選擇基於值，因此可能會選擇一組不同的列，這將導致評分操作失敗。
7. 提交管道。

保存然後應用列選擇的過程可確保相同的資料架構可用於培訓和評分。


## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
