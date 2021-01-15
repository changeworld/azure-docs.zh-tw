---
title: '降低機器學習模型中的公平 (預覽) '
titleSuffix: Azure Machine Learning
description: 了解機器學習模型中的公平性，以及 Fairlearn Python 套件如何協助您建置更公平的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 08/05/2020
ms.custom: responsible-ml
ms.openlocfilehash: 29e29e73aa1094a2941592655af78a24d95c53b5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223086"
---
# <a name="mitigate-fairness-in-machine-learning-models-preview"></a>降低機器學習模型中的公平 (預覽) 

瞭解機器學習服務的公平，以及 [Fairlearn](https://fairlearn.github.io/) 開放原始碼 Python 套件如何協助您減輕機器學習模型中的公平問題。 如果您不想要瞭解公平問題，以及在建立機器學習模型時評估公平，您可能會建立產生不公平結果的模型。

下列 Fairlearn 開放原始碼套件的 [使用者指南](https://fairlearn.github.io/master/user_guide/index.html) 摘要說明如何使用它來評定您所建立之 AI 系統的公平。  Fairlearn 開放原始碼套件也可提供選項，以協助降低或減少您觀察到的任何公平問題。  請參閱操作 [說明](how-to-machine-learning-fairness-aml.md) 和 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) ，以在 Azure Machine Learning 定型期間，啟用 AI 系統的公平評量。


## <a name="what-is-fairness-in-machine-learning-models"></a>機器學習模型中的公平為何？

>[!NOTE]
> 公平性是一個社會技術課題。 公平性的許多層面 (例如司法和正當程序)，都是無法從量化的公平性計量反映的。 此外，許多量化的公平性計量無法同時令人滿意。 Fairlearn 開放原始碼套件的目標是讓人類能夠評估不同的影響和緩和策略。 最後，使用者必須建立人工智慧和機器學習模型，以進行適用于其案例的取捨。

人工智慧和機器學習系統可能會顯示不公平的行為。 定義不公平行為的方式之一，是藉由其對人們的損害或影響來定義。 AI 系統可能產生許多類型的損害。 若要深入瞭解，請參閱 [Kate Crawford 的 NeurIPS 2017 專題演講](https://www.youtube.com/watch?v=fMym_BKWQzk) 。

AI 造成的損害有兩種常見的類型：

- 配置的傷害： AI 系統可延伸或 withholds 特定群組的商機、資源或資訊。 相關範例包括雇用、入學申請和貸款；在其模型中，從特定族群中挑選適當候選人的能力，可能會優於對其他族群的挑選。

- 服務品質的損害：AI 系統對於某個族群的運作效能不如其他族群。 例如，語音辨識系統對於女性的運作效能可能會不如男性。

若要減少 AI 系統中不公平的行為，您必須評估並緩解這些損害。

## <a name="fairness-assessment-and-mitigation-with-fairlearn"></a>Fairlearn 的公平性評量和緩解

Fairlearn 是一個開放原始碼 Python 套件，可讓機器學習系統開發人員評估其系統的公平性，並緩解觀察到的公平性問題。

Fairlearn 開放原始碼套件有兩個元件：

- 評量儀表板：一項 Jupyter 筆記本小工具，可評估模型對於不同群體的預期影響。 此外也可讓您使用公平性和效能計量來比較多個模型。
- 緩解演算法：一組演算法，可緩解二元分類和迴歸中的不公平性。

在這些元件的搭配運作下，資料科學家和企業領導者將可掌控公平性與效能之間的任何取捨，並選取最符合其需求的緩解策略。

## <a name="assess-fairness-in-machine-learning-models"></a>在機器學習模型中評估公平

在 Fairlearn 的開放原始碼封裝中，公平的概念是一種稱為「 **群組公平**」的方法，它會詢問：哪些個體群組有可能發生損害的風險？ 相關群體 (也稱為子總體) 會透過 **敏感性特徵** 或敏感性屬性來定義。 敏感性功能會傳遞至 Fairlearn 開放原始碼封裝中的估算器，作為向量或稱為的矩陣  `sensitive_features` 。 顧名思義，系統設計工具在評估群體公平性時應對這些特徵特別敏感。 

有一點要注意的是，這些功能是否包含私用資料所造成的隱私權含意。 但「敏感性」一詞並不表示這些特徵不應用來進行預測。

>[!NOTE]
> 公平評估並非純粹的技術練習。  Fairlearn 開放原始碼套件可協助您評估模型的公平，但不會為您執行評量。  Fairlearn 開放原始碼封裝有助於找出量化計量來評估公平，但開發人員也必須執行定性分析，以評估其專屬模型的公平。  上面所述的敏感性功能是這類定性分析的範例。     

在評量階段，公平性會透過差異計量進行量化。 **差異計量** 能夠以比率或差異的形式，評估和比較模型在不同群體間的行為。 Fairlearn 開放原始碼套件支援兩種差異計量類別：


- 模型效能的差異：這些計量集會計算選定的效能計量在不同子群體間的差異值。 部分範例包括：

  - 精確率的差異
  - 錯誤率的差異
  - 精確度的差異
  - 回收的差異
  - MAE 的差異
  - 其他多項差異

- 選取率的差異：此計量包含不同子群體間的選取率差異。 舉例來說，貸款核准率即為此類差異。 選取率是指每個類別中歸類為 1 的資料點所佔的比例 (在二元分類中)，或預測值的分佈 (在迴歸中)。

## <a name="mitigate-unfairness-in-machine-learning-models"></a>減緩機器學習模型中的不公平性

### <a name="parity-constraints"></a>同位檢查條件約束

Fairlearn 開放原始碼套件包含各種不公平性緩和演算法。 這些演算法支援一組對預測工具行為的條件約束，稱為 **同位檢查條件約束** 或準則。 採用同位檢查條件約束時，預測工具行為的某些層面在敏感性特徵所定義的群體 (例如，不同種族) 之間必須是可比較的。 Fairlearn 開放原始碼套件中的緩和演算法會使用這類同位條件約束，來減輕觀察到的公平問題。

>[!NOTE]
> 在模型中緩和不公平性意味著減少不公平性，但這項技術緩和措施無法完全消除這個不公平性。  Fairlearn 開放原始碼套件中的不公平性防護演算法可提供建議的風險降低策略，以協助減少機器學習模型中的不公平性，但它們不是完全免除不公平性的解決方案。  對於每個特定開發人員的機器學習模型，可能會有其他的同位條件約束或準則。 使用 Azure Machine Learning 的開發人員必須自行決定，因為緩和措施可充分消除其預定使用和部署機器學習模型的不公平性。  

Fairlearn 開放原始碼封裝支援下列類型的同位條件約束： 

|同位檢查條件約束  | 目的  |機器學習工作  |
|---------|---------|---------|
|人口統計同位檢查     |  減輕配置損害 | 二元分類、迴歸 |
|補償機率  | 診斷配置和服務品質損害 | 二元分類        |
|相等的商機 | 診斷配置和服務品質損害 | 二元分類        |
|限定群體損失     |  減輕服務品質損害 | 迴歸 |



### <a name="mitigation-algorithms"></a>緩解演算法

Fairlearn 開放原始碼套件提供了可逐步處理和縮減的不公平性緩和演算法：

- 減少：這些演算法採用標準的黑色機器學習估算器 (例如 LightGBM 模型) ，並使用一系列的重新加權訓練資料集來產生一組重新定型模型。 例如，可對特定性別的申請者調升或調降權重以重新定型模型，並減少不同性別群體間的差異。 然後，使用者可以選擇一個在精確度 (或其他效能計量) 與差異之間提供最佳取捨的模型，而這通常需以商務規則和成本計算為基礎。  
- 後處理：這些演算法會採用現有的分類器和敏感性特徵作為輸入。 接著將會衍生分類器預測的轉換，以強制執行指定的公平性條件約束。 閾值最佳化最大的優點在於其簡易性和彈性，因為不需要重新定型模型。 

| 演算法 | 描述 | 機器學習工作 | 敏感性特徵 | 支援的同位檢查條件約束 | 演算法類型 |
| --- | --- | --- | --- | --- | --- |
| `ExponentiatedGradient` | 公平分類的黑箱方法，如[公平分類的縮減方法](https://arxiv.org/abs/1803.02453)所說明 | 二元分類 | 類別 | [人口統計同位檢查](#parity-constraints)、[補償機率](#parity-constraints) | 縮減 |
| `GridSearch` | [公平分類的縮減方法](https://arxiv.org/abs/1803.02453)中說明的黑箱方法| 二元分類 | Binary | [人口統計同位檢查](#parity-constraints)、[補償機率](#parity-constraints) | 縮減 |
| `GridSearch` | 一種黑箱方法，會使用限定群體損失的演算法，實作公平迴歸的網格搜尋變異，相關說明請見[公平迴歸：量化定義和縮減型演算法](https://arxiv.org/abs/1905.12843) | 迴歸 | Binary | [限定群體損失](#parity-constraints) | 縮減 |
| `ThresholdOptimizer` | 根據[監督式學習中的機會均等](https://arxiv.org/abs/1610.02413)文件進行的後處理演算法。 這項技術會以現有的分類器和敏感性特徵作為輸入，並衍生分類器預測的單調轉換，以強制執行指定的同位檢查條件約束。 | 二元分類 | 類別 | [人口統計同位檢查](#parity-constraints)、[補償機率](#parity-constraints) | 後處理 |

## <a name="next-steps"></a>後續步驟

- 查看 Fairlearn 的 [GitHub](https://github.com/fairlearn/fairlearn/)、 [使用者指南](https://fairlearn.github.io/master/user_guide/index.html)、 [範例](https://fairlearn.github.io/master/auto_examples/)和 [範例筆記本](https://github.com/fairlearn/fairlearn/tree/master/notebooks)，以瞭解如何使用不同的元件。
- 瞭解 [如何](how-to-machine-learning-fairness-aml.md) 在 Azure Machine Learning 中啟用機器學習模型的公平評量。
- 請參閱 Azure Machine Learning 中其他公平評估案例的 [範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/contrib/fairness) 。 
