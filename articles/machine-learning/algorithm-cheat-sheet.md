---
title: 機器學習演算法備忘表
titleSuffix: Azure Machine Learning
description: 可列印的機器學習演算法備忘表可説明您在 Azure 機器學習設計器中選擇適合預測模型的演算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 03/05/2020
ms.openlocfilehash: 85fbb1c1d26f71903adab2eb96b0c1dd3bf74c33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328605"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Azure 機器學習設計器的機器學習演算法備忘表

**Azure Machine Learning 演算法小祕技**可協助您針對預測性分析模型選擇正確的演算法。

Azure 機器學習具有來自***分類***、***推薦系統***、***聚類***、***異常檢測***、***回歸***和***文本分析***系列的大型演算法庫。 每項的設計均是用來處理不同類型的機器學習服務問題。

有關其他指南，請參閱[如何選擇演算法](how-to-select-algorithms.md)

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>下載：機器學習演算法備忘表

**在此下載小祕技：[機器學習服務演算法小祕技 (11x17 英吋)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![機器學習演算法備忘表：瞭解如何選擇機器學習演算法。](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.svg)

下載並列印 Tabloid 大小的「機器學習服務演算法小祕技」，以隨時瀏覽並獲得選擇演算法的協助。

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>如何使用機器學習演算法備忘表

這份演算法小祕技中提供的建議是近似經驗法則。 您可以屈從有些建議，也可以公然違反有些建議。 此備忘單旨在建議一個起點。 別擔心您的資料上會出現數種演算法間勢均力敵的競爭。 理解每種演算法和生成資料的系統的原則是無可替代的。

每個機器學習服務演算法都有自己的風格或歸納偏差。 對於特定問題，幾種演算法可能比較合適，並且一種演算法可能比其他演算法更合適。 但不一定能夠事先知道哪一種最適合。 在這類情況下，小祕技中會一起列出數個演算法。 適當策略就是嘗試一個演算法，而如果結果還不滿意，就嘗試其他演算法。 

要瞭解有關 Azure 機器學習中的演算法的更多資訊，請訪問[演算法和模組引用](algorithm-module-reference/module-reference.md)。

## <a name="kinds-of-machine-learning"></a>機器學習種類

機器學習有三個主要類別：*經過指導的學習*、*未經指導的學習*和*增強式學習*。

### <a name="supervised-learning"></a>監督學習

在經過指導的學習中，每個資料點都會加上標籤或與感興趣的類別或值產生關聯。 分類標籤的範例就是將影像指派為「貓」或「狗」。 值標籤的範例則是與中古汽車相關聯的銷售價格。 監督式學習的目標是研究許多像是這些已加上標籤的範例，然後能夠做出有關未來資料點的預測。 例如，識別包含正確動物的新相片，或者針對其他中古汽車指派精確的銷售價格。 這是常見且實用的機器學習服務類型。

### <a name="unsupervised-learning"></a>無監督學習

在未監督的學習中，資料點沒有與其相關聯的標籤。 然而，未經指導的學習演算法的目標在於以某種方式組織資料或描述其結構。 無監督學習將資料分組到群集中，就像 K-means 一樣，或者找到查看複雜資料的不同方法，使其看起來更簡單。

### <a name="reinforcement-learning"></a>增強式學習

在增強式學習中，演算法需要選擇一個動作來回應每個資料點。 這是在機器人領域中的常見方法，其中在某個時間點的感應器讀數集就是一個資料點，而演算法必須選擇機器人的下一個動作。 它也很適合用於物聯網 (Internet of Things) 應用程式。 此學習演算法也會在短時間內收到獎勵訊號，指出決策的好壞程度。 基於此信號，演算法修改其策略以實現最高獎勵。 

## <a name="next-steps"></a>後續步驟

* 請參閱[有關如何選擇演算法](how-to-select-algorithms.md)的其他指南

* [在 Azure 機器學習和 Azure 門戶中瞭解工作室](overview-what-is-azure-ml.md)。

* [教程：在 Azure 機器學習設計器中構建預測模型](tutorial-designer-automobile-price-train-score.md)。

* [瞭解深度學習與機器學習](concept-deep-learning-vs-machine-learning.md)。
