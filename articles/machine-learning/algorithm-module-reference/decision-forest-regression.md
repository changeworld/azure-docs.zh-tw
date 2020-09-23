---
title: 決策樹系回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的 [決策樹系回歸] 模組，根據決策樹的集團建立回歸模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 7f9677bfd793e7ff21ff2c6c7e6760b630dc074b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898544"
---
# <a name="decision-forest-regression-module"></a>決策樹系回歸模組

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組，根據決策樹的集團來建立回歸模型。

設定模型之後，您必須使用加上標籤的資料集和「 [定型模型](./train-model.md) 」模組來定型模型。 然後，定型的模型就可用來進行預測。 

## <a name="how-it-works"></a>運作方式

決策樹是非參數化模型，可對每個執行個體執行一系列簡單的測試，周遊整個二元樹狀資料結構，直到抵達葉節點 (決策) 為止。

決策樹有下列優點：

- 在定型和預測期間，執行運算和記憶體使用都很有效率。

- 可以代表非線性決策界限。

- 執行整合式特徵選取和分類，在出現雜訊特徵時也能靈活應變。

此迴歸模型由決策樹的集團所組成。 回歸決策樹系中的每個樹狀結構都會輸出高斯分佈作為預測。 系統會對樹狀結構的集團執行匯總，以找出最接近模型中所有樹狀結構之合併分佈的高斯散發。

如需有關此演算法及其實作為理論架構的詳細資訊，請參閱這篇文章：決策樹系 [：適用于分類、回歸、密度估計、各種方式學習和半監督式學習的統一架構](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>如何設定決策樹系回歸模型

1. 將 **決策樹系回歸** 模組新增至管線。 您可以在設計工具中的 [ **Machine Learning**、 **初始化模型**和 **回歸**] 下找到此模組。

2. 開啟模組屬性，然後在 [重新 **取樣] 方法**中，選擇用來建立個別樹狀結構的方法。  您可以選擇 [ **封袋]** ] **或 [** 複寫]。

    - **封袋]**：封袋] 也稱為 *啟動*程式匯總。 回歸決策樹系中的每個樹狀結構都會透過預測來輸出高斯分佈。 匯總是為了藉由結合個別樹狀結構所傳回的所有散發，來找出前兩分鐘符合高斯分佈組合時間的高斯。

         如需詳細資訊，請參閱 [啟動](https://wikipedia.org/wiki/Bootstrap_aggregating)程式匯總的維琪百科專案。

    - 複寫 **：在**複寫中，每個樹狀結構都會以完全相同的輸入資料進行定型。 判斷每個樹狀節點所使用的分割述詞會保持隨機，而樹狀結構將會不同。

         如需有關使用 [複寫 **] 選項定型程式的詳細**資訊，請參閱[電腦視覺和醫療影像分析的決策樹系。Criminisi 和 Shotton。Springer link 2013 ...](https://research.microsoft.com/projects/decisionforests/)

3. 藉由設定 [ **建立定型模式]** 選項，指定您要如何定型模型。

    - **單一參數**

      如果您知道要如何設定模型，您可以提供一組特定值做為引數。 您可能已經透過實驗知道這些值，或已依據指導收到這些值。

    - **參數範圍**：如果您不確定最佳參數，且想要執行參數清除，請選取此選項。 選取要逐一查看的值範圍，而 [ [微調模型超參數](tune-model-hyperparameters.md) ] 會逐一查看您提供的所有可能設定組合，以判斷產生最佳結果的超參數。 



4. 針對 [ **決策樹數目**]，指定要在集團中建立的決策樹總數目。 藉由建立多個決策樹，您或許能夠有較佳的涵蓋範圍，但是定型時間會拉長。

    > [!TIP]
    > 此值也會控制視覺化定型模型時所顯示的樹狀結構數目。 如果您想要查看或列印單一樹狀結構，您可以將此值設定為1。不過，這表示只會產生一個樹狀結構 (具有初始) 參數集的樹狀結構，而不會執行進一步的反覆運算。

5. 如需決策樹的 **最大深度**，請輸入一個數位，以限制任何決策樹的最大深度。 增加樹狀結構的深度可增加有效位數，但可能會有過度配適及定型時間增加的風險。

6. 針對 **每個節點的隨機分割數目**，請輸入在建立樹狀結構的每個節點時所要使用的分割數目。 *分割*表示樹 (節點) 中每個層級的功能會隨機分割。

7. 如需 **每個分葉節點的樣本數下限**，請指定在樹狀結構中建立任何終端節點 (分葉) 所需的最小案例數目。

     藉由增加此值，您會增加建立新規則的臨界值。 例如，若預設值是 1，即使單一案例可能會造成新規則的建立。 如果您將此值增加為 5，則定型資料必須至少包含五個符合相同條件的案例。


9. 定型模型：

    + 如果您將 [ **建立定型模式** ] 設定為 [ **單一參數**]，請連接已標記的資料集和「 [定型模型](train-model.md) 」模組。  
  
    + 如果您將 [ **建立定型模式** ] 設定為 [ **參數範圍**]，請連接已加上標籤的資料集，並使用 [微調模型超參數](tune-model-hyperparameters.md)來定型模型。  
  
    > [!NOTE]
    > 
    > 如果您將參數範圍傳遞給 [定型模型](train-model.md)，它只會使用單一參數清單中的預設值。  
    > 
    > 如果您將一組參數值傳遞至 [微調模型超參數](tune-model-hyperparameters.md) 模組，當它預期每個參數的設定範圍時，會忽略這些值，並使用學習模組的預設值。  
    > 
    > 如果您選取 [ **參數範圍** ] 選項，並輸入任何參數的單一值，則整個清除期間都會使用您所指定的單一值，即使其他參數會在某個範圍的值之間變更。

   

10. 提交管線。

### <a name="results"></a>結果

定型完成後：

+ 若要儲存定型模型的快照集，請選取定型模組，然後切換至右面板中的 [ **輸出** ] 索引標籤。 按一下圖示 **註冊模型**。  您可以在模組樹狀結構中，找到儲存的模型作為模組。 

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 