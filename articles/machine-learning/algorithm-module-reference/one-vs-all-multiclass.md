---
title: 一對多多級
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的「一對多」多元分類模組，從二元分類模型的集團建立多元分類模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: dfe01e16b55325db03e4150a33ae5c4aa5822ae2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898509"
---
# <a name="one-vs-all-multiclass"></a>一對多多級

本文說明如何在 Azure Machine Learning 設計工具中使用「一對多」多元分類模組。 目標是要使用 *一對多* 方法來建立可以預測多個類別的分類模型。

當結果取決於連續或類別預測量變數時，此模組可用來建立能夠預測三個或更多可能結果的模型。 此方法也可讓您使用二元分類方法解決需要多個輸出級別的問題。

### <a name="more-about-one-versus-all-models"></a>深入瞭解一對一模型

某些分類演算法允許依設計使用兩個以上的類別。 其他的結果會將可能的結果限制為兩個值的其中一個 (二進位或雙類別的模型) 。 但即使是二元分類演算法也可以透過各種策略來調整多類別分類工作。 

這個模組會執行一對多方法，在此方法中，會為多個輸出類別建立二進位模型。 模組會針對個別類別，根據其補數來評估每個二進位模型， (模型中的所有其他類別) ，就像是二元分類問題一樣。 然後，模組會執行這些二元分類器並選擇具有最高信賴分數的預測，以執行預測。  

基本上，此模組會建立個別模型的集團，然後合併結果，以建立可預測所有類別的單一模型。 任何二元分類器都可用來做為一對一模型的基礎。  

例如，假設您設定 [兩個類別的支援向量機器](two-class-support-vector-machine.md) 模型，並將它提供給「一對多」多元分類模組的輸入。 模組會針對輸出類別的所有成員建立兩個類別的支援向量機器模型。 然後，它會套用一對多方法來合併所有類別的結果。  

## <a name="how-to-configure-the-one-vs-all-multiclass-classifier"></a>如何設定「一對多」多元分類分類器  

此課程模組會建立二元分類模型的集團來分析多個類別。 若要使用此模組，您必須先設定和定型 *二元分類* 模型。 

您可以將二進位模型連接到「一對多」多元分類模組。 然後，您可以使用 [定型模型](train-model.md) 搭配加上標籤的訓練資料集來定型模型的集團。

當您結合模型時，「一對多」多元分類會建立多個二元分類模型、將每個類別的演算法優化，然後合併模型。 雖然訓練資料集可能會有多個類別值，模組仍會執行這些工作。

1. 在設計工具中，將「一對多」多元分類模組新增至您的管線。 您可以在 [**分類**] 類別中的 [ **Machine Learning-初始化**] 下找到此模組。

   「一對多」多元分類分類器沒有自己的可設定參數。 任何自訂都必須在提供做為輸入的二元分類模型中完成。

2. 將二元分類模型加入至管線，並設定該模型。 例如，您可能會使用 [二級支援向量機器](two-class-support-vector-machine.md) 或 [雙類別促進式決策樹](two-class-boosted-decision-tree.md)。

3. 將「 [定型模型](train-model.md) 」模組新增至您的管線。 連接「一對多」多元分類之輸出的未定型分類器。

4. 在 [ [定型模型](train-model.md)] 的其他輸入上，連接具有多個類別值的加上標籤訓練資料集。

5. 提交管線。

## <a name="results"></a>結果

定型完成之後，您可以使用模型來進行多元預測。

或者，您可以將未定型的分類器傳遞至 [交叉驗證模型](cross-validate-model.md) ，以根據加上標籤的驗證資料集進行交叉驗證。


## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
