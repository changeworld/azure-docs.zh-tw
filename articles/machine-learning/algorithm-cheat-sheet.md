---
title: Machine Learning 演算法功能提要-設計工具
titleSuffix: Azure Machine Learning
description: 可列印的 Machine Learning 演算法功能提要可協助您在 Azure Machine Learning 設計師中為預測模型選擇正確的演算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: b824604ce9d0171b5612ab559eace4b35fd01eb8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82890967"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Azure Machine Learning 設計工具的 Machine Learning 演算法功能提要

**Azure Machine Learning 演算法**功能提要可協助您從預測性分析模型的設計工具中選擇正確的演算法。

Azure Machine Learning 具有來自***分類***、***推薦系統*** ***、叢集***、***異常偵測***、***回歸***和***文字分析***系列的大型演算法程式庫。 每項的設計均是用來處理不同類型的機器學習服務問題。

如需其他指引，請參閱[如何選取演算法](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>下載： Machine Learning 演算法功能提要

**在此下載小祕技：[機器學習服務演算法小祕技 (11x17 英吋)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Machine Learning 演算法功能提要：瞭解如何選擇 Machine Learning 演算法。](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

下載並列印 Tabloid 大小的「機器學習服務演算法小祕技」，以隨時瀏覽並獲得選擇演算法的協助。

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>如何使用 Machine Learning 演算法功能提要

這份演算法小祕技中提供的建議是近似經驗法則。 您可以屈從有些建議，也可以公然違反有些建議。 這份功能重點是用來建議起點。 別擔心您的資料上會出現數種演算法間勢均力敵的競爭。 您只需要瞭解每個演算法的原則，以及產生資料的系統。

每個機器學習服務演算法都有自己的風格或歸納偏差。 針對特定問題，可能會有數種演算法適合，而其中一個演算法可能比其他演算法更適合。 但不一定能夠事先知道哪一種最適合。 在這類情況下，小祕技中會一起列出數個演算法。 適當策略就是嘗試一個演算法，而如果結果還不滿意，就嘗試其他演算法。 

若要深入瞭解 Azure Machine Learning 設計工具中的演算法，請移至[演算法和模組參考](algorithm-module-reference/module-reference.md)。

## <a name="kinds-of-machine-learning"></a>機器學習服務的種類

機器學習有三個主要類別：*經過指導的學習*、*未經指導的學習*和*增強式學習*。

### <a name="supervised-learning"></a>監督式學習

在經過指導的學習中，每個資料點都會加上標籤或與感興趣的類別或值產生關聯。 分類標籤的範例就是將影像指派為「貓」或「狗」。 值標籤的範例則是與中古汽車相關聯的銷售價格。 監督式學習的目標是研究許多像是這些已加上標籤的範例，然後能夠做出有關未來資料點的預測。 例如，識別包含正確動物的新相片，或者針對其他中古汽車指派精確的銷售價格。 這是常見且實用的機器學習服務類型。

### <a name="unsupervised-learning"></a>不受監督學習

在未監督的學習中，資料點沒有與其相關聯的標籤。 然而，未經指導的學習演算法的目標在於以某種方式組織資料或描述其結構。 不受監督學習會將資料分組到叢集，例如，做為平均值，或尋找不同的方式來查看複雜資料，使其更簡單。

### <a name="reinforcement-learning"></a>增強式學習

在增強式學習中，演算法需要選擇一個動作來回應每個資料點。 這是在機器人領域中的常見方法，其中在某個時間點的感應器讀數集就是一個資料點，而演算法必須選擇機器人的下一個動作。 它也很適合用於物聯網 (Internet of Things) 應用程式。 此學習演算法也會在短時間內收到獎勵訊號，指出決策的好壞程度。 根據此信號，演算法會修改其策略，以達到最高的報酬。 

## <a name="next-steps"></a>後續步驟

* 請參閱其他關於[如何選取演算法](how-to-select-algorithms.md)的指引

* [瞭解 Azure Machine Learning 和 Azure 入口網站中的 studio](overview-what-is-azure-ml.md)。

* [教學課程：在 Azure Machine Learning 設計工具中建立預測模型](tutorial-designer-automobile-price-train-score.md)。

* [瞭解深度學習與機器學習](concept-deep-learning-vs-machine-learning.md)服務。
