---
title: 排列特徵重要性：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"排列要素重要性"模組，以計算具有訓練模型和測試資料集的要素變數的排列要素重要性分數。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456058"
---
# <a name="permutation-feature-importance"></a>排列功能重要性

本文介紹如何使用 Azure 機器學習設計器（預覽）中的"排列要素重要性"模組來計算資料集的一組要素重要性分數。 使用這些分數可説明您確定在模型中使用的最佳功能。

在此模組中，要素值隨機隨機排列，一次一列。 模型的性能在前後進行測量。 您可以選擇一個標準指標來衡量績效。

模組返回的分數表示經過訓練的模型在排列後性能*的變化*。 重要功能通常對洗牌過程更敏感，因此它們會導致更高的重要性分數。 

本文概述了排列功能及其理論基礎及其在機器學習中的應用：[排列特徵的重要性](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)。  

## <a name="how-to-use-permutation-feature-importance"></a>如何使用排列功能重要性

生成一組要素分數需要具有已訓練的模型以及測試資料集。  

1.  將"排列功能重要性"模組添加到管道中。 您可以在 **"功能選擇**"類別中找到此模組。 

2.  將訓練的模型連接到左側輸入。 模型必須是回歸模型或分類模型。  

3.  在正確的輸入上，串連資料組。 最好選擇一個不同于用於訓練模型的資料集。 此資料集用於根據訓練的模型進行評分。 它還用於在要素值更改後評估模型。  

4.  對於**隨機種子**，輸入一個值，用作隨機化的種子。 如果指定 0（預設值），則根據系統時鐘生成數位。

     種子值是可選的，但如果希望在同一管道的運行中重現，則應提供值。  

5.  對於**測量性能的指標**，選擇在計算排列後模型品質時要使用的單個指標。  

     Azure 機器學習設計器支援以下指標，具體取決於是評估分類模型還是回歸模型：  

    -   **分類**

        精度、精度、召回  

    -   **回歸**

        精度、召回、平均絕對誤差、根均值平方誤差、相對絕對誤差、相對平方誤差、確定係數  

     有關這些評估指標及其計算方式的更詳細說明，請參閱[評估模型](evaluate-model.md)。  

6.  提交管道。  

7.  模組輸出要素列及其關聯的分數的清單。 清單按分數的降冪排列。  


##  <a name="technical-notes"></a>技術說明

排列要素重要性的工作原理是隨機更改每個要素列的值，一次一列一列。 然後，它評估模型。 

模組提供的排名通常不同于您從[基於篩選器的功能選擇](filter-based-feature-selection.md)中獲得的排名。 基於篩選器的特徵選取在創建模型*之前*計算分數。 

區別的原因是，排列要素重要性並不測量要素和目標值之間的關聯。 相反，它捕獲每個要素對模型預測的影響。
  
## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
