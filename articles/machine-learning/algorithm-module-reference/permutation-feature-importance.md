---
title: 排列功能重要性：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的排列功能重要性模組來計算特徵變數的排列特徵重要性分數，並指定定型模型和測試資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79456058"
---
# <a name="permutation-feature-importance"></a>排列功能重要性

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的排列功能重要性模組，來計算資料集的一組功能重要性分數。 您可以使用這些分數來協助您判斷要在模型中使用的最佳功能。

在此課程模組中，功能值會隨機隨機播放，一次一個資料行。 模型的效能是在之前和之後測量。 您可以選擇其中一個標準計量來測量效能。

模組傳回的分數代表定型模型在排列後的效能*變更*。 重要功能通常對隨機程式較為敏感，因此會產生較高的重要性分數。 

本文概述排列功能、理論基礎，以及其在機器學習中的應用程式：[排列功能重要性](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)。  

## <a name="how-to-use-permutation-feature-importance"></a>如何使用排列功能重要性

若要產生一組功能分數，您必須擁有已定型的模型，以及測試資料集。  

1.  將排列功能重要性模組新增至您的管線。 您可以在 [**特徵選取**] 類別目錄中找到此模組。 

2.  將定型的模型連接到左側輸入。 模型必須是回歸模型或分類模型。  

3.  在右側輸入中，串連資料組。 最好是選擇與您用來定型模型的資料集不同的一個。 此資料集用於根據定型模型進行評分。 它也可用來在功能值變更之後評估模型。  

4.  針對 [**隨機種子**]，輸入要用來做為隨機設定種子的值。 如果您指定0（預設值），則會根據系統時鐘產生一個數位。

     種子值是選擇性的，但如果您想要跨相同管線的執行重現性，則應提供值。  

5.  如需**測量效能的度量**，請選取在排列後計算模型品質時要使用的單一計量。  

     Azure Machine Learning 設計工具支援下列計量，這取決於您是否要評估分類或回歸模型：  

    -   **分類**

        精確度、精確度、召回率  

    -   **迴歸**

        精確度、召回率、平均絕對錯誤、根平均平方誤差、相對絕對錯誤、相對平方誤差、判斷係數  

     如需這些評估計量和其計算方式的詳細描述，請參閱[評估模型](evaluate-model.md)。  

6.  提交管線。  

7.  此模組會輸出特徵資料行的清單，以及與它們相關聯的分數。 清單會以分數的遞減順序排序。  


##  <a name="technical-notes"></a>技術說明

排列功能重要性的運作方式是隨機變更每個特徵資料行的值，一次一個資料行。 然後，它會評估模型。 

模組提供的排名通常與您從 [以[篩選器為基礎的特徵選取](filter-based-feature-selection.md)] 所取得的排名不同。 以篩選為基礎的特徵選取會在建立模型*之前*計算分數。 

差異的原因在於排列功能重要性並不會測量功能與目標值之間的關聯。 相反地，它會從模型中的預測中，捕捉每項功能的影響程度。
  
## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
