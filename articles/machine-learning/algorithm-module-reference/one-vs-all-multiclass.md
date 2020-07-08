---
title: 一對多多級
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用一對一多元模組，從二元分類模型的集團建立多元分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 29934758ab729e0fb888c10b7f834da3d0bf7fb0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79456075"
---
# <a name="one-vs-all-multiclass"></a>一對多多級

本文說明如何在 Azure Machine Learning 設計工具（預覽）中使用一對一的多元模組。 目標是使用*一對一*方法來建立可預測多個類別的分類模型。

當結果取決於連續或類別預測量變數時，此模組可用來建立能夠預測三個或更多可能結果的模型。 此方法也可讓您使用二元分類方法解決需要多個輸出級別的問題。

### <a name="more-about-one-versus-all-models"></a>深入瞭解一對一與所有模型

某些分類演算法允許依設計使用兩個以上的類別。 有些則會將可能的結果限制為兩個值的其中一個（二進位或兩個類別的模型）。 但是，即使是二元分類演算法，也可以透過各種不同的策略來調整多類別分類工作。 

此模組會執行一對一與全部的方法，其中會為每個輸出類別建立二進位模型。 模組會針對個別類別的每個二進位模型，評估其補數（模型中的所有其他類別），就好像是二元分類問題一樣。 然後，此模組會執行這些二元分類器，並選擇具有最高信賴分數的預測，藉此執行預測。  

基本上，此模組會建立個別模型的集團，然後合併結果，以建立可預測所有類別的單一模型。 任何二元分類器都可以當做一對一模型的基礎使用。  

例如，假設您設定了[兩個類別的支援向量機器](two-class-support-vector-machine.md)模型，並提供做為一對一多元模組的輸入。 此模組會為輸出類別的所有成員建立兩個類別的支援向量機器模型。 然後，它會套用一對一和 all 方法，以合併所有類別的結果。  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>如何設定一對多多元分類器  

此模組會建立二元分類模型的集團來分析多個類別。 若要使用此模組，您必須先設定和定型*二元分類*模型。 

您可以將二進位模型連接到一對一的多元模組。 接著，您可以使用[定型模型](train-model.md)與加上標籤的訓練資料集來定型模型的集團。

當您結合模型時，一個 vs-All 多元會建立多個二元分類模型、為每個類別優化演算法，然後合併模型。 雖然訓練資料集可能會有多個類別值，但模組仍會執行這些工作。

1. 在設計工具中，將 [一對一] 多元模組新增至您的管線。 您可以在 [**分類**] 分類中的 [ **Machine Learning-初始化**] 下找到此模組。

   一對一多元分類器沒有自己的可設定參數。 任何自訂都必須在提供做為輸入的二元分類模型中完成。

2. 將二元分類模型加入至管線，並設定該模型。 例如，您可能會使用[兩個類別的支援向量機器](two-class-support-vector-machine.md)或[兩個類別的推進式決策樹](two-class-boosted-decision-tree.md)。

3. 將[訓練模型](train-model.md)模組新增至您的管線。 連接未定型的分類器，這是一對一與所有多元的輸出。

4. 在 [[定型模型](train-model.md)] 的其他輸入上，連接具有多個類別值的加上標籤的訓練資料集。

5. 提交管線。

## <a name="results"></a>結果

定型完成之後，您可以使用模型來進行多元預測。

或者，您也可以將未定型的分類器傳遞至[交叉驗證模型](cross-validate-model.md)，以對已加上標籤的驗證資料集進行交叉驗證。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
