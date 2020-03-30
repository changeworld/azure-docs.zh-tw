---
title: 決策林回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用決策林回歸模組創建基於決策樹組合的回歸模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456534"
---
# <a name="decision-forest-regression-module"></a>決策林回歸模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以基於決策樹組創建回歸模型。

配置模型後，必須使用標記的資料集和[訓練模型](./train-model.md)模組訓練模型。 然後，定型的模型就可用來進行預測。 

## <a name="how-it-works"></a>運作方式

決策樹是非參數化模型，可對每個執行個體執行一系列簡單的測試，周遊整個二元樹狀資料結構，直到抵達葉節點 (決策) 為止。

決策樹有下列優點：

- 在定型和預測期間，執行運算和記憶體使用都很有效率。

- 可以代表非線性決策界限。

- 執行整合式特徵選取和分類，在出現雜訊特徵時也能靈活應變。

此迴歸模型由決策樹的集團所組成。 回歸決策林中的每個樹都輸出高斯分佈作為預測。 在樹群上執行聚合，以查找最接近模型中所有樹的組合分佈的高斯分佈。

有關此演算法的理論框架及其實現的詳細資訊，請參閱本文：[決策林：分類、回歸、密度估計、多管理系統學習和半監督學習的統一框架](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>如何配置決策林回歸模型

1. 將**決策林回歸**模組添加到管道中。 您可以在**機器學習**、**初始化模型**和**回歸**下的設計器中找到模組。

2. 打開模組屬性，對於**重新採樣方法**，請選擇用於創建單個樹的方法。  您可以從**裝袋**或**複製**中進行選擇。

    - **裝袋**： 裝袋也稱為*引導*聚合。 回歸決策林中的每個樹都通過預測輸出高斯分佈。 聚合是找到一個高斯，其前兩個時刻匹配高斯分佈的混合物的時刻，通過組合單個樹返回的所有分佈。

         有關詳細資訊，請參閱[Bootstrap 聚合](https://wikipedia.org/wiki/Bootstrap_aggregating)的維琪百科條目。

    - **複製**：在複製中，每個樹都接受完全相同的輸入資料的培訓。 為每個樹節點使用拆分謂詞的確定保持隨機狀態，並且樹將多種多樣。

         有關使用**複製**選項的培訓過程的詳細資訊，請參閱[電腦視覺和醫療圖像分析的決策林。克裡米尼西和J.肖特頓斯普林格2013.](https://research.microsoft.com/projects/decisionforests/).

3. 通過設置 **"創建培訓師模式"** 選項，指定如何訓練模型。

    - **單一參數**

      如果您知道要如何設定模型，您可以提供一組特定值做為引數。 您可能已經透過實驗知道這些值，或已依據指導收到這些值。

    - **參數範圍**：如果不確定最佳參數，並且想要運行參數掃描，請選擇此選項。 選擇要反覆運算的值範圍，[調諧模型 Hyper 參數](tune-model-hyperparameters.md)反覆運算您提供的所有可能組合設置，以確定生成最佳結果的超參數。 



4. 對於**決策樹數**，指示要在集合中創建的決策樹的總數。 藉由建立多個決策樹，您或許能夠有較佳的涵蓋範圍，但是定型時間會拉長。

    > [!TIP]
    > 此值還控制視覺化訓練的模型時顯示的樹數。 如果要查看或列印單個樹，可以將該值設置為 1;否則，可以將該值設置為 1。但是，這意味著將只生成一個樹（具有初始參數集的樹），並且不會執行進一步的反覆運算。

5. 對於**決策樹的最大深度**，鍵入一個數位以限制任何決策樹的最大深度。 增加樹狀結構的深度可增加有效位數，但可能會有過度配適及定型時間增加的風險。

6. 對於**每個節點的隨機拆分數**，鍵入生成樹的每個節點時要使用的拆分數。 *拆分*表示樹（節點）的每個級別中的要素被隨機分割。

7. 對於**每個葉節點的最小樣本數**，指示在樹中創建任何終端節點（葉）所需的最小案例數。

     藉由增加此值，您會增加建立新規則的臨界值。 例如，若預設值是 1，即使單一案例可能會造成新規則的建立。 如果您將此值增加為 5，則定型資料必須至少包含五個符合相同條件的案例。


9. 連接標記的資料集，選擇包含不超過兩個結果的單個標籤列，然後連接到["訓練模型](./train-model.md)"。

    - 如果將 **"創建訓練器模式"** 選項設置為 **"單一參數**"，請使用[訓練模型](./train-model.md)模組訓練模型。

   

10. 提交管道。

### <a name="results"></a>結果

培訓完成後：

+ 要保存已定型模型的快照，請選擇訓練模組，然後切換到右側面板中的 **"輸出"** 選項卡。 按一下圖示**註冊模型**。  您可以在模組樹中找到已保存的模型作為模組。 

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 