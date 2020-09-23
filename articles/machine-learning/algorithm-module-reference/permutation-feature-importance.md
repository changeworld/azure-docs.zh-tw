---
title: 排列功能重要性：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用排列功能重要性模組，以根據定型的模型和測試資料集來計算特徵變數的排列功能重要性分數。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: ecf739ea838ad3742612eab7a1b42ac8c8ac455a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905266"
---
# <a name="permutation-feature-importance"></a>排列功能重要性

本文描述如何在 Azure Machine Learning 表設計工具中使用排列功能重要性模組，以計算資料集的一組功能重要性分數。 您可以使用這些分數來協助您判斷要在模型中使用的最佳功能。

在此課程模組中，功能值會隨機隨機地隨機行出，一次一個資料行。 模型的效能會在之前和之後進行測量。 您可以選擇其中一個標準計量來測量效能。

模組傳回的分數代表定型模型在排列之後的效能 *變更* 。 重要功能通常會對跳過程式更敏感，因此會導致重要性較高的分數。 

本文概述排列功能、其理論基礎，以及其在機器學習服務中的應用程式： [排列功能重要性](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)。  

## <a name="how-to-use-permutation-feature-importance"></a>如何使用排列功能重要性

產生一組功能分數需要您已定型的模型，以及測試資料集。  

1.  將排列功能重要性模組新增至您的管線。 您可以在 [ **特徵選取** ] 分類中找到此模組。 

2.  將定型的模型連接至左方輸入。 模型必須是回歸模型或分類模型。  

3.  在右邊的輸入上，串連資料組。 最好是選擇與您用來定型模型的資料集不同的資料集。 此資料集會根據定型的模型來進行評分。 它也可用來在功能值變更之後評估模型。  

4.  針對 [ **隨機種子**]，輸入要做為隨機載入的種子值。 如果您指定 0 (預設) ，則會根據系統時鐘產生數位。

     種子值是選擇性的，但如果您想要在相同管線的執行之間重現性，則應該提供一個值。  

5.  針對 **測量效能的計量**，請選取當您在排列之後計算模型品質時要使用的單一度量。  

     Azure Machine Learning 設計工具支援下列計量，取決於您是否正在評估分類或回歸模型：  

    -   **分類**

        精確度、有效位數、召回率  

    -   **迴歸**

        精確度、召回率、平均絕對錯誤、根平均平方誤差、相對絕對錯誤、相對平方誤差、判斷係數  

     如需這些評估計量及其計算方式的詳細描述，請參閱 [評估模型](evaluate-model.md)。  

6.  提交管線。  

7.  此模組會輸出特徵資料行清單以及與它們相關聯的分數。 此清單會依分數的遞減順序排序。  


##  <a name="technical-notes"></a>技術說明

排列功能重要性的運作方式是隨機變更每個特徵資料行的值，一次一個資料行。 然後，它會評估模型。 

模組所提供的排名通常與您從以 [篩選器為基礎的特徵選取專案](filter-based-feature-selection.md)所取得的排名不同。 以篩選為基礎的特徵選取會在建立模型 *之前* 計算分數。 

差異的原因在於排列功能重要性不會測量功能與目標值之間的關聯。 相反地，它會從模型中捕捉每項功能對預測的影響程度。
  
## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
