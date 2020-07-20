---
title: 人工智慧
description: LUIS 使用人工智慧，根據您所定義的架構，為您的資料提供語言理解。
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: 4f145585e097a3cf6a2338dbab879f6fce07f71d
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85802647"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Language Understanding 中的人工智慧（LUIS）

LUIS 使用人工智慧，根據您所定義的架構，為您的資料提供自然語言理解（NLU）。

## <a name="natural-language-processing"></a>自然語言處理

自然 Language Understanding （NLU）是自然語言處理（NLP）的特定子主題。

自然語言處理是一種更廣泛的概念，可處理文字資料的任何形式，這包括：

* Token 化
* 語音（pos）標記的一部分
* 分割
* 形態分析
* 語義相似
* Discourse
* 翻譯

## <a name="natural-language-processing-in-luis"></a>LUIS 中的自然語言處理

您的 LUIS 應用程式可透過下列方式使用自然語言處理：
* [自然語言理解](#natural-language-understanding)（LUIS）
* LUIS 中可設定的 NLP 層面：
    * [Token 化](luis-language-support.md#tokenization)
    * 透過變音符號、標點符號和 word forms [API 設定](luis-reference-application-settings.md)來 Morphology
* 其他[認知服務](../Welcome.md)所提供之查詢語句的前置或後置處理，例如：
    * [翻譯](../translator/translator-info-overview.md)

## <a name="natural-language-understanding"></a>自然語言理解

NLU 是將語言語句_轉換_成標記法的能力，可讓您以自然的方式瞭解使用者。 自然語言理解仍然是一項極具挑戰性的問題，並已定義為_AI-困難_的問題。

LUIS 的目的是要專注于意圖和解壓縮，這包括能夠識別：
* 使用者想要的內容
* 討論的內容。

LUIS 對於較廣泛的_NLP_層面（例如語義相似）沒有任何瞭解，在範例中沒有明確的識別。 例如，下列標記（單字）是三個不同的專案，除非它們用於範例所提供的類似內容中：

* buy
* 促成
* 台

## <a name="next-steps"></a>後續步驟

* 瞭解 LUIS 應用程式的[開發生命週期](luis-concept-app-iteration.md)