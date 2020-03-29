---
title: 功能 - LUIS
titleSuffix: Azure Cognitive Services
description: 將特性新增至語言模型，以針對如何辨識您想要標示或分類的輸入，提供相關提示。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: b151043babd6c67a17f704bb671a415fcc3ba7b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221511"
---
# <a name="machine-learned-features"></a>機器學習功能 

在機器學習中，_功能_是系統觀察&學習的資料的特徵或屬性。 在語言理解 （LUIS） 中，一項功能描述並解釋了您的意圖和實體的重要意義。

在[預覽 LUIS 門戶](https://preview.luis.ai)中，要素是_描述項，_ 因為它們用於_描述_意圖或實體。  

## <a name="features-_descriptors_-in-language-understanding"></a>語言理解中的功能 （_描述項_）

功能（也稱為描述項）描述説明語言理解識別示例陳述的線索。 功能包括： 

* 片語清單作為意圖或實體的功能
* 實體作為意圖或實體的特徵

要素應被視為模型分解架構的必要部分。 

## <a name="what-is-a-phrase-list"></a>什麼是片語清單

片語清單是單詞、短語、數位或其他字元的清單，可説明確定您嘗試識別的概念。 該清單不區分大小寫。 

## <a name="when-to-use-a-phrase-list"></a>何時使用片語清單

使用片語清單，LUIS 會考慮上下文並進行一般性化，以標識與文本匹配（但不是確切文本匹配）的專案。 如果您需要 LUIS 應用能夠概括和標識新專案，請使用片語清單。 

當希望能夠識別新實例（如應識別新連絡人名稱的會議計畫程式）或應識別新產品的清單應用時，請從機器學習實體開始。 然後創建一個片語清單，説明 LUIS 查找具有類似含義的單詞。 此片語清單通過為這些單詞的值添加其他重要性來指導 LUIS 識別示例。 

片語清單就像是網域專屬詞彙，有助於增強意圖和實體的理解品質。 

## <a name="considerations-when-using-a-phrase-list"></a>使用片語清單時的注意事項

預設情況下，片語清單將應用於應用中的所有模型。 這將適用于可以跨所有意圖和實體的片語清單。 對於可分解性，應僅將片語清單應用於與它相關的模型。 

如果創建片語清單（預設情況下創建全域），則稍後將其作為描述項（功能）應用於特定模型，則會將其從其他模型中刪除。 此刪除為應用於的模型的片語清單添加了相關性，有助於提高它在模型中提供的準確性。 

標誌`enabledForAllModels`在 API 中控制此模型作用域。 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>如何使用片語清單

當您的意圖或實體具有重要的單詞或短語時，[創建片語清單](luis-how-to-add-features.md)，例如：

* 業界術語
* 俚語
* 縮寫
* 公司特定語言
* 來自另一種語言但通常在您的應用程式中使用的語言
* 範例表達中的關鍵字或片語

**不要**添加所有可能的單詞或短語。 相反，一次添加幾個單詞或短語，然後重新訓練和發佈。 隨著清單隨時間的增長，您可能會發現某些術語具有許多表單（同義字）。 將這些清單分解為另一個清單。 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>何時將實體用作特徵 

可以在意圖或實體級別添加實體作為要素。 

### <a name="entity-as-a-feature-to-an-intent"></a>實體作為意圖的要素

當檢測到該實體對意圖很重要時，將實體作為描述項（功能）添加到意圖中。

例如，如果意圖是預訂航班，而實體是機票資訊（如座位數、始發地和目的地），則查找機票資訊實體應為預訂航班意向的預測增加權重。 

### <a name="entity-as-a-feature-to-another-entity"></a>實體作為另一個實體的要素

當該實體 （A） 的檢測對於預測實體 （B） 非常重要時，應將實體 （A） 作為特徵添加到另一個實體 （B）。

例如，如果檢測到街道位址實體 （A），則查找街道位址 （A） 會增加送貨位址實體 （B） 的預測權重。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

請參閱[新增功能](luis-how-to-add-features.md)，深入了解如何將功能新增至 LUIS 應用程式。