---
title: 評估及調和機器學習模型中的公平性
titleSuffix: Azure Machine Learning
description: 了解機器學習模型中的公平性，以及 Fairlearn Python 套件如何協助您建置更公平的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/02/2020
ms.openlocfilehash: c21ec0329a7b5716a00262b7422296df3afe208b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596357"
---
# <a name="fairness-in-machine-learning-models"></a>機器學習模型中的公平性

了解機器學習服務中的公平性，以及 Fairlearn 開放原始碼 Python 套件如何協助您建置更公平的模型。

## <a name="what-is-fairness-in-machine-learning-systems"></a>機器學習系統中的公平性是什麼？

人工智慧和機器學習系統可能會顯示不公平的行為。 定義不公平行為的方式之一，是藉由其對人們的損害或影響來定義。 AI 系統可能產生許多類型的損害。 AI 造成的損害有兩種常見的類型：

- 配置的損害：AI 系統會擴充或保留機會、資源或資訊。 相關範例包括雇用、入學申請和貸款；在其模型中，從特定族群中挑選適當候選人的能力，可能會優於對其他族群的挑選。

- 服務品質的損害：AI 系統對於某個族群的運作效能不如其他族群。 例如，語音辨識系統對於女性的運作效能可能會不如男性。

若要減少 AI 系統中不公平的行為，您必須評估並緩解這些損害。

>[!NOTE]
> 公平性是一個社會技術課題。 公平性的許多層面 (例如司法和正當程序)，都是無法從量化的公平性計量反映的。 此外，許多量化的公平性計量無法同時令人滿意。 其目標是要讓人們能評估不同的緩解策略，然後就其所處情況進行適當的取捨。

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Fairlearn 的公平性評量和緩解

Fairlearn 是一個開放原始碼 Python 套件，可讓機器學習系統開發人員評估其系統的公平性，並緩解觀察到的公平性問題。

Fairlearn 有兩個元件：

- 評量儀表板：一項 Jupyter 筆記本小工具，可評估模型對於不同群體的預期影響。 此外也可讓您使用公平性和效能計量來比較多個模型。
- 緩解演算法：一組演算法，可緩解二元分類和迴歸中的不公平性。

在這些元件的搭配運作下，資料科學家和企業領導者將可掌控公平性與效能之間的任何取捨，並選取最符合其需求的緩解策略。

## <a name="fairness-assessment"></a>公平性評量

在 Fairlearn 中，公平性的概念來自於名為**群體公平性**的方法，這會提出下列問題：哪些個人群體有遭受損害的風險？

相關群體 (也稱為子總體) 會透過**敏感性特徵**或敏感性屬性來定義。 敏感性特徵會傳至 Fairlearn 估算器，作為名為 `sensitive_features` 的向量或矩陣。 顧名思義，系統設計工具在評估群體公平性時應對這些特徵特別敏感。 有一點要注意的是，這些特徵是否包含因個人識別資訊而產生的隱私權含意。 但「敏感性」一詞並不表示這些特徵不應用來進行預測。

在評量階段，公平性會透過差異計量進行量化。 **差異計量**能夠以比率或差異的形式，評估和比較模型在不同群體間的行為。 Fairlearn 支援兩種類別的差異計量：


- 模型效能的差異：這些計量集會計算選定的效能計量在不同子群體間的差異值。 部分範例包括：

  - 精確率的差異
  - 錯誤率的差異
  - 精確度的差異
  - 回收的差異
  - MAE 的差異
  - 其他多項差異

- 選取率的差異：此計量包含不同子群體間的選取率差異。 舉例來說，貸款核准率即為此類差異。 選取率是指每個類別中歸類為 1 的資料點所佔的比例 (在二元分類中)，或預測值的分佈 (在迴歸中)。

## <a name="unfairness-mitigation"></a>緩解不公平性

### <a name="parity-constraints"></a>同位檢查條件約束

Fairlearn 包含多種不同的不公平性緩解演算法。 這些演算法支援一組對預測工具行為的條件約束，稱為**同位檢查條件約束**或準則。 採用同位檢查條件約束時，預測工具行為的某些層面在敏感性特徵所定義的群體 (例如，不同種族) 之間必須是可比較的。 Fairlearn 的緩解演算法會使用這類同位檢查條件約束來緩解觀察到的公平性問題。

Fairlearn 支援下列類型的同位檢查條件約束：

|同位檢查條件約束  | 目的  |機器學習工作  |
|---------|---------|---------|
|人口統計同位檢查     |  減輕配置損害 | 二元分類、迴歸 |
|補償機率  | 診斷配置和服務品質損害 | 二元分類        |
|限定群體損失     |  減輕服務品質損害 | 迴歸 |

### <a name="mitigation-algorithms"></a>緩解演算法

Fairlearn 提供後處理和縮減不公平性緩解演算法：

- 縮減：這些演算法採用標準黑箱 ML 估算器 (例如 LightGBM 模型)，並使用一系列的重新加權訓練資料集來產生一組重新定型的模型。 例如，可對特定性別的申請者調升或調降權重以重新定型模型，並減少不同性別群體間的差異。 然後，使用者可以選擇一個在精確度 (或其他效能計量) 與差異之間提供最佳取捨的模型，而這通常需以商務規則和成本計算為基礎。  
- 後處理：這些演算法會採用現有的分類器和敏感性特徵作為輸入。 接著將會衍生分類器預測的轉換，以強制執行指定的公平性條件約束。 閾值最佳化最大的優點在於其簡易性和彈性，因為不需要重新定型模型。 

| 演算法 | 描述 | 機器學習工作 | 敏感性特徵 | 支援的同位檢查條件約束 | 演算法類型 |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | 公平分類的黑箱方法，如[公平分類的縮減方法](https://arxiv.org/abs/1803.02453)所說明 | 二元分類 | 類別 | [人口統計同位檢查](#parity-constraints)、[補償機率](#parity-constraints) | 縮減 |
| `GridSearch` | [公平分類的縮減方法](https://arxiv.org/abs/1803.02453)中說明的黑箱方法| 二元分類 | Binary | [人口統計同位檢查](#parity-constraints)、[補償機率](#parity-constraints) | 縮減 |
| `GridSearch` | 一種黑箱方法，會使用限定群體損失的演算法，實作公平迴歸的網格搜尋變異，相關說明請見[公平迴歸：量化定義和縮減型演算法](https://arxiv.org/abs/1905.12843) | 迴歸 | Binary | [限定群體損失](#parity-constraints) | 縮減 |
| `ThresholdOptimizer` | 根據[監督式學習中的機會均等](https://arxiv.org/abs/1610.02413)文件進行的後處理演算法。 這項技術會以現有的分類器和敏感性特徵作為輸入，並衍生分類器預測的單調轉換，以強制執行指定的同位檢查條件約束。 | 二元分類 | 類別 | [人口統計同位檢查](#parity-constraints)、[補償機率](#parity-constraints) | 後處理 |

## <a name="next-steps"></a>後續步驟

- 若要了解如何使用不同的元件，請參閱 [Fairlearn GitHub 存放庫](https://github.com/fairlearn/fairlearn/)和[範例筆記本](https://github.com/fairlearn/fairlearn/tree/master/notebooks)。
- 了解如何使用[差異隱私權和 WhisperNoise 套件](concept-differential-privacy.md)保有資料隱私權。