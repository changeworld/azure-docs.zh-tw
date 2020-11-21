---
title: 人工智慧 (AI)
description: LUIS 使用人工智慧 (AI) 根據您定義的架構，為您的資料提供語言理解。
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021366"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Language Understanding (LUIS 中的人工智慧) 

LUIS 使用人工智慧 (AI) 根據您所定義的架構，為您的資料提供自然語言理解 (NLU) 。

## <a name="natural-language-processing-nlp"></a>自然語言處理 (NLP) 

天然 Language Understanding (NLU) 是自然語言處理 (NLP) 的特定子主題。

自然語言處理是一種更廣泛的概念，可處理任何形式的文字資料處理，包括如下的內容：

* 切分
* 語音 (pos) 標記的一部分
* 分割
* 形態分析
* 語義相似性
* 論述
* 翻譯

## <a name="natural-language-processing-in-luis"></a>LUIS 中的自然語言處理

您可以透過下列方式，在 LUIS 應用程式中使用自然語言處理：
* [自然語言理解](#natural-language-processing-nlp) (LUIS) 
* LUIS 中可設定的 NLP 層面：
    * [Token 化](luis-language-support.md#tokenization)
    * 透過字元符號、標點符號和 word forms [API 設定](luis-reference-application-settings.md)Morphology
* 由其他 [認知服務](../what-are-cognitive-services.md) 提供的查詢語句前置或後置處理，例如：
    * [翻譯](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>自然語言理解 (NLU)

NLU 是將語言語句 _轉換_ 成標記法的能力，讓您可以自然地瞭解您的使用者。 自然語言理解仍是極具挑戰性的問題，而且定義為 _AI_ 難題。

LUIS 的目的是要專注于意圖和解壓縮，這包括能夠識別：
* 使用者想要的內容
* 他們所說的內容。

LUIS 對更廣泛的 _NLP_ 層面（例如語義相似性）幾乎沒有任何瞭解，例如範例中沒有明確的識別。 例如，下列權杖 (字組) 有三個不同的專案，直到在提供的範例中使用類似的內容：

* buy
* 買
* 買

## <a name="next-steps"></a>下一步

* 瞭解 LUIS 應用程式的[開發生命週期](luis-concept-app-iteration.md)