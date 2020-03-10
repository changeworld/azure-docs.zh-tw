---
title: 功能-LUIS
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393900"
---
# <a name="machine-learned-features"></a>機器學習的功能 

在機器學習中，_功能_是您的系統觀察 & 學習之資料的特性或屬性（attribute）。 在 Language Understanding （LUIS）中，功能會描述並說明您的意圖和實體有何意義。

在[預覽 LUIS 入口網站](https://preview.luis.ai)中，功能是描述項 _，因為它們是用_來_說明_意圖或實體。  

## <a name="features-_descriptors_-in-language-understanding"></a>Language Understanding 中的功能（_描述_項）

「功能」（也稱為「描述項」）描述可協助 Language Understanding 識別語句範例的線索。 功能包括： 

* 片語清單做為意圖或實體的功能
* 實體做為意圖或實體的特徵

您應該將功能視為架構的必要部分，以進行模型分解。 

## <a name="what-is-a-phrase-list"></a>什麼是片語清單

片語清單是單字、片語、數位或其他字元的清單，可協助您識別您嘗試識別的概念。 此清單不區分大小寫。 

## <a name="when-to-use-a-phrase-list"></a>使用片語清單的時機

在片語清單中，LUIS 會考慮 coNtext 和一般化來識別類似的專案，但不是完全相符的文字。 如果您需要 LUIS 應用程式能夠將新專案一般化並加以識別，請使用片語清單。 

當您想要能夠辨識新的實例時（例如應該辨識新連絡人名稱的會議排程器），或應辨識新產品的清查應用程式，請從機器學習的實體開始。 然後建立片語清單，協助 LUIS 尋找具有類似意義的單字。 此片語清單會將額外的重要性新增至這些單字的值，以引導 LUIS 辨識範例。 

片語清單就像是網域專屬詞彙，有助於增強意圖和實體的理解品質。 

## <a name="considerations-when-using-a-phrase-list"></a>使用片語清單時的考慮

根據預設，片語清單會套用至應用程式中的所有模型。 這適用于可跨所有意圖和實體的片語清單。 針對 decomposability，您應該只將片語清單套用到其相關的模型。 

如果您建立片語清單（預設為全域建立），然後將它當做描述項（功能）套用至特定模型，則會從其他模型中移除它。 這項移除會為其所套用的模型新增與片語清單的相關性，以協助改善模型中所提供的精確度。 

`enabledForAllModels` 旗標會控制此模型在 API 中的範圍。 

<a name="how-to-use-phrase-lists"></a>

### <a name="how-to-use-a-phrase-list"></a>如何使用片語清單

當您的意圖或實體具有很重要的單字或片語時，請[建立片語清單](luis-how-to-add-features.md)，例如：

* 業界術語
* 俚語
* 縮寫
* 公司特定語言
* 來自另一種語言但通常在您的應用程式中使用的語言
* 範例表達中的關鍵字或片語

請勿**新增每**個可能的單字或片語。 相反地，一次新增幾個單字或片語，然後重新定型和發佈。 當清單隨著時間成長時，您可能會發現某些詞彙有許多形式（同義字）。 將這些分解成另一個清單。 

<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="when-to-use-an-entity-as-a-feature"></a>使用實體做為功能的時機 

實體可以做為意圖或實體層級的功能加入。 

### <a name="entity-as-a-feature-to-an-intent"></a>實體做為意圖的功能

當實體的偵測對意圖而言很重要時，請將實體做為描述項（功能）新增至意圖。

例如，如果意圖是用來預約航班，而實體是票證資訊（例如基座數目、原點和目的地），則尋找票證資訊實體應該將權數新增至書籍航班意圖的預測。 

### <a name="entity-as-a-feature-to-another-entity"></a>實體做為另一個實體的功能

當實體（A）的偵測對實體（B）的預測很重要時，應該將實體（A）當做功能加入另一個實體（B）。

例如，如果偵測到街道位址實體（A），則尋找街道位址（A）會為出貨位址實體（B）的預測增加權數。 

## <a name="best-practices"></a>最佳作法
了解[最佳做法](luis-concept-best-practices.md)。

## <a name="next-steps"></a>後續步驟

請參閱[新增功能](luis-how-to-add-features.md)，深入了解如何將功能新增至 LUIS 應用程式。