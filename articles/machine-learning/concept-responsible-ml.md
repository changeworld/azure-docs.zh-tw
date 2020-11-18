---
title: '什麼是負責機器學習 (預覽版) '
titleSuffix: Azure Machine Learning
description: 瞭解什麼是負責的機器學習，以及如何搭配 Azure Machine Learning 使用它來瞭解模型、保護資料及控制模型生命週期。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: ff85ed669fb7c3ac43e322eb747c537a30801920
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701427"
---
# <a name="what-is-responsible-machine-learning-preview"></a>什麼是負責機器學習服務？ (預覽)

在本文中，您將瞭解什麼是負責的機器學習服務 (ML) 是什麼，以及您可以如何運用 Azure Machine Learning 來實務。

## <a name="responsible-machine-learning-principles"></a>負責任的機器學習原則

在整個開發和使用 AI 系統的同時，信任是最重要的核心。 包括對平台、流程和模型的信任。 在 Microsoft，負責的機器學習包含下列值和準則：

- 了解機器學習模型
  - 解讀和說明模型行為
  - 評估和緩和模型的不公正之處
- 保護人員和其資料
  - 防止具有差異隱私權的資料曝光
  - 使用同態加密來處理加密的資料
- 控制端對端機器學習流程
  - 使用資料表記錄機器學習生命週期

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="負責任 ML 要素-可解譯性、差異隱私權、同態加密、audit 軌跡-Azure Machine Learning":::

當人工智慧和自動系統更加廣泛地整合到社群中的各個細微末節之處時，主動設法預測並減輕這些技術帶來的非預期結果非常重要。

## <a name="interpret-and-explain-model-behavior"></a>解讀和說明模型行為

很難解釋或不透明的系統可能會造成問題，因為這會讓專案關係人（例如系統開發人員、監管者、使用者和業務決策者）難以瞭解系統做出特定決策的原因。 相較於其他 AI 系統，有些 AI 系統較易於說明，有時也會在具有較高準確度的系統與更易於說明的系統之間進行取捨。

若要建置可解譯的 AI 系統，請使用 [InterpretML](https://github.com/interpretml/interpret)，這是由 Microsoft 所建置的開放原始碼套件。 [InterpretML 可以在 Azure Machine Learning 中使用](how-to-machine-learning-interpretability.md)，用來[解讀和說明您的機器學習模型](how-to-machine-learning-interpretability-aml.md)，包括[自動化機器學習模型](how-to-machine-learning-interpretability-automl.md)。

## <a name="mitigate-fairness-in-machine-learning-models"></a>減輕機器學習模型的公平

隨著社會中的日常決策變得越來越仰賴 AI 系統，如何為每個人提供公平的成果就變得十分重要。

AI 系統中的不公平性可能會導致下列非預期的結果：

- 來自個人的非法謀利商機、資源或資訊。
- 強化偏差和刻板印象。

無法透過計量擷取或表示許多公平層面。 有一些工具和實務可以改善 AI 系統設計和開發的公平性。

評量和緩和措施是在 AI 系統中減少不公平性的兩個重要步驟。 建議您使用 [FairLearn](https://github.com/fairlearn/fairlearn)，這是一種開放原始碼套件，可評估和緩和 AI 系統的潛在不公平性。 若要深入了解公平和 FairLearn 套件，請參閱 [ML 文章中的公平性](./concept-fairness-ml.md)。

## <a name="prevent-data-exposure-with-differential-privacy"></a>防止具有差異隱私權的資料曝光

當資料用於分析時，請務必在使用資料時保持隱私和機密。 差異隱私權是一組系統和實務，可協助保護個人資料的安全和隱私。

在傳統的案例中，未經處理資料會儲存在檔案和資料庫中。 當使用者分析資料時，通常會使用未經處理資料。 如此可能會因為侵害個人的隱私權而產生問題。 差異隱私權會嘗試處理此問題，方法是在資料中新增「雜訊」或隨機性，讓使用者無法識別任何個別的資料點。

實作差異隱私系統十分困難。 [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) 是開放原始碼專案，其中包含用來建立全域差異隱私系統的不同元件。 若要深入了解差異隱私權和 WhiteNoise 專案，請參閱 [使用差異隱私權和 WhiteNoise 來保留資料隱私權](./concept-differential-privacy.md)一文。

> [!NOTE]
> 請注意，我們會重新命名此工具組，並將在未來幾周推出新的名稱。 

## <a name="work-on-encrypted-data-with-homomorphic-encryption"></a>使用同態加密處理加密的資料

在傳統的雲端儲存體和計算解決方案中，雲端需要對客戶資料進行未加密的存取，以進行計算。 此存取會將資料公開給雲端操作員。 資料隱私權依賴雲端所執行並受客戶信任的存取控制原則。

Homomorphic 加密可讓您在加密資料上進行計算，而不需要存取祕密 (解密) 金鑰。 計算的結果會進行加密，且只能藉由秘密金鑰的擁有者顯示。 使用同態加密時，雲端操作員永遠不會對其儲存和計算的資料進行未加密的存取。 計算是直接在加密資料上執行。 資料隱私權依賴最先進的密碼編譯，而資料擁有者會控制所有資訊的發行。 如需 Microsoft 同態加密的詳細資訊，請參閱 [Microsoft Research](https://www.microsoft.com/research/project/homomorphic-encryption/)。

若要開始在 Azure Machine Learning 中使用同態加密，請使用適用于[MICROSOFT 密封](https://github.com/microsoft/SEAL)的[加密推斷](https://pypi.org/project/encrypted-inference/)Python 系結。 Microsoft 密封是開放原始碼同態加密程式庫，可讓您在加密的整數或實數上執行新增和乘法運算。 若要深入瞭解 Microsoft 密封，請參閱 [Azure 架構中心](/azure/architecture/solution-ideas/articles/homomorphic-encryption-seal) 或 [microsoft Research 專案頁面](https://www.microsoft.com/research/project/microsoft-seal/)。

請參閱下列範例，以瞭解 [如何在 Azure Machine Learning 中部署加密的推斷 web 服務](how-to-homomorphic-encryption-seal.md)。

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>使用資料表記錄機器學習生命週期

在機器學習流程中記錄正確的資訊，是在每個階段做出可靠決策的關鍵。 資料表是一種方法，可記錄機器學習生命週期中使用和建立的機器學習資產。

模型通常會視為「不透明的方塊」，且通常不會有很少的相關資訊。 由於機器學習系統變得越來越普遍，並廣泛地用於決策，因此使用資料表可進一步開發更多可靠的機器學習系統。

建議您記錄為一些模型資訊，以作為資料表的一部分內容：

- 預定用途
- 模型架構
- 訓練已使用的資料
- 評估已使用的資料
- 訓練模型效能計量
- 公平性資訊。

請參閱下列範例，以了解如何使用 Azure Machine Learning SDK 來實作[模型資料表](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)。

## <a name="additional-resources"></a>其他資源

- 如需詳細資訊，請參閱 [負責任的創新工具](/azure/architecture/guide/responsible-innovation/) 組，以瞭解最佳作法。
- 深入了解機器學習系統文件的 [有關 ML](https://www.partnershiponai.org/about-ml/) 指導方針集 。