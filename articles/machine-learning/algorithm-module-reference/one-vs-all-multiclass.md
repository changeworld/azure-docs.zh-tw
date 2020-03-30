---
title: 一對多多級
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用"一個與所有"類別模組，從一組二進位分類模型創建多類分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456075"
---
# <a name="one-vs-all-multiclass"></a>一對多多級

本文介紹如何在 Azure 機器學習設計器（預覽）中使用"一個與所有多類"模組。 目標是通過使用*一對一*的方法創建一個可以預測多個類的分類模型。

當結果取決於連續或類別預測量變數時，此模組可用來建立能夠預測三個或更多可能結果的模型。 此方法也可讓您使用二元分類方法解決需要多個輸出級別的問題。

### <a name="more-about-one-versus-all-models"></a>更多關於一個模型與所有模型

某些分類演算法允許設計使用兩個以上類。 其他將可能的結果限制為兩個值之一（二進位或雙類模型）。 但是，即使是二進位分類演算法也可以通過各種策略適用于多類分類任務。 

此模組實現一對一的方法，在該方法中為每個多個輸出類創建二進位模型。 該模組根據每個類的補充（模型中的所有其他類）評估每個二進位模型，就好像它是二進位分類問題一樣。 然後，模組通過運行這些二進位分類器並選擇具有最高置信度的預測來執行預測。  

從本質上講，模組創建單個模型的組，然後合併結果，以創建預測所有類的單個模型。 任何二進位分類器都可以用作一個對一模型的基礎。  

例如，假設您配置了[雙類支援向量機](two-class-support-vector-machine.md)模型，並將其作為對"一個-所有多類"模組的輸入提供。 該模組將為輸出類的所有成員創建兩類支援向量機模型。 然後，它將應用一對一的方法來組合所有類的結果。  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>如何配置單數與全類分類器  

此模組創建一組二進位分類模型來分析多個類。 要使用此模組，您需要首先配置和訓練*二進位分類*模型。 

將二進位模型連接到一個 vs-All 類別模組。 然後，通過使用帶有標記[訓練資料集的"訓練模型"](train-model.md)來訓練模型的組。

合併模型時，One-vs-All Multi 類創建多個二進位分類模型，優化每個類的演算法，然後合併模型。 即使訓練資料集可能具有多個類值，模組也會執行這些任務。

1. 在設計器中將"一個與所有多類別模組"添加到管道中。 您可以在 **"機器學習 - 初始化**"下找到此模組。"**分類"** 類別中。

   單對全類分類器本身沒有可配置的參數。 任何自訂都必須在作為輸入提供的二進位分類模型中完成。

2. 向管道添加二進位分類模型，並配置該模型。 例如，您可以使用[雙類支援向量機](two-class-support-vector-machine.md)或[雙類提升決策樹](two-class-boosted-decision-tree.md)。

3. 將[列車模型](train-model.md)模組添加到管道中。 連接未訓練的分類器，該分類器是"一個與所有多類"的輸出。

4. 在[訓練模型](train-model.md)的其他輸入上，連接具有多個類值的標記訓練資料集。

5. 提交管道。

## <a name="results"></a>結果

培訓完成後，您可以使用模型進行多類預測。

或者，您可以將未經訓練的分類器傳遞給[交叉驗證模型](cross-validate-model.md)，以便針對標記的驗證資料集進行交叉驗證。


## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
