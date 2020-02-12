---
title: 定型群集模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用訓練叢集模型模組來定型群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 7053fab28b4a231c92f31e344cf09ffef3a6b146
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152103"
---
# <a name="train-clustering-model"></a>定型群集模型

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來定型群集模型。

此模組會採用您已經使用[K 意指](k-means-clustering.md)叢集模組設定的未定型叢集模型，並使用加上標籤或未標記的資料集來訓練模型。 此模組會建立可用於預測的定型模型，以及定型資料中每個案例的一組叢集指派。

> [!NOTE]
> 叢集模型無法使用[訓練模型](train-model.md)模組來定型，這是用來定型機器學習模型的一般模組。 這是因為[定型模型](train-model.md)僅適用于受監督的學習演算法。 K-意指和其他群集演算法允許不受監督學習，這表示演算法可以從未標記的資料中學習。  
  
## <a name="how-to-use-train-clustering-model"></a>如何使用定型群集模型  

1.  在設計工具中，將**訓練叢集模型**模組新增至您的管線。 您可以在 [**定型**] 分類的 [ **Machine Learning 模組**] 底下找到模組。  
  
2. 新增[K 表示](k-means-clustering.md)叢集模組，或另一個建立相容叢集模型的自訂模組，並設定群集模型的參數。  
    
3.  將訓練資料集附加至**定型群集模型**的右側輸入。
  
5.  在 [資料**行集**] 中，選取要在建立叢集中使用之資料集的資料行。 請務必選取可提供良好功能的資料行：例如，避免使用具有唯一值的識別碼或其他資料行，或具有所有相同值的資料行。

    如果有可用的標籤，您可以使用它做為功能，或將其保留。  
  
6. 如果您想要將定型資料與新的叢集標籤一起輸出，請選取 [**僅針對結果檢查附加] 或 [取消**核取] 選項。

    如果您取消選取此選項，則只會輸出叢集指派。 

7. 執行管線，或按一下 [**定型群集模型**] 模組，然後選取 [**執行選取**]。  
  
### <a name="results"></a>結果

訓練完成之後：

+ 若要儲存定型模型的快照集，請選取 [**訓練模型**] 模組右面板中的 [**輸出**] 索引標籤。 選取 [**註冊資料集**] 圖示，將模型儲存為可重複使用的模組。

+ 若要從模型產生分數，請使用 [[將資料指派給群集](assign-data-to-clusters.md)]。

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 