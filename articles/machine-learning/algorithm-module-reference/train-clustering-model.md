---
title: 定型群集模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「定型群集模型」模組來定型群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 0dfde6fef7cc91edf2101095018bc031d392c4ed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898177"
---
# <a name="train-clustering-model"></a>定型叢集模型

本文描述 Azure Machine Learning 設計工具中的模組。

使用此模組來定型群集模型。

此模組會採用您已經使用 [K 表示](k-means-clustering.md) 叢集模組設定的未定型群集模型，並使用加上標籤或未標記的資料集來訓練模型。 此模組會建立可用於預測的定型模型，以及定型資料中每個案例的一組叢集指派。

> [!NOTE]
> 您無法使用「 [定型模型](train-model.md) 」模組來定型群集模型，這是用來定型機器學習模型的一般模組。 這是因為 [定型模型](train-model.md) 只適用于受監督的學習演算法。 K-意指和其他群集演算法允許非監督式學習，這表示演算法可以從未標記的資料中學習。  
  
## <a name="how-to-use-train-clustering-model"></a>如何使用定型群集模型  

1.  在設計工具中，將 [ **定型群集模型** ] 模組新增至您的管線。 您可以在 [**定型**] 類別目錄中的 [ **Machine Learning 模組**] 下找到此模組。  
  
2. 新增 [K 意指](k-means-clustering.md) 叢集模組，或另一個建立相容群集模型的自訂模組，並設定群集模型的參數。  
    
3.  將訓練資料集附加至 **定型群集模型**的右手邊輸入。
  
5.  在 [資料 **行集**] 中，從資料集選取要在建立群集中使用的資料行。 請務必選取提供良好功能的資料行：例如，避免使用識別碼或其他具有唯一值的資料行，或具有所有相同值的資料行。

    如果有可用的標籤，您可以使用它做為功能，或將其保留。  
  
6. 如果您想要將定型資料與新的叢集標籤一起輸出，請選取 [ **只檢查附加] 或 [取消**核取結果] 選項。

    如果您取消選取此選項，則只會輸出叢集指派。 

7. 提交管線，或按一下 [ **定型群集模型** ] 模組，然後選取 [ **執行選取**]。  
  
### <a name="results"></a>結果

定型完成後：

+ 若要儲存定型模型的快照集，請選取 [**定型模型**] 模組右面板中的 [**輸出**] 索引標籤。 選取 [ **註冊資料集** ] 圖示，將模型儲存為可重複使用的模組。

+ 若要從模型產生分數，請使用 [將資料指派給群集](assign-data-to-clusters.md)。

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 