---
title: 要求限制 - Translator Text API
titleSuffix: Azure Cognitive Services
description: 本文列出 Translator Text API 的要求限制。 費用是根據字元計數來產生，而不是根據受限於每個要求 5,000 個字元的要求頻率。 字元限制是以訂用帳戶為基礎，而 F0 受限於每小時 2 百萬個字元。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: swmachan
ms.openlocfilehash: 8d26efec2783d6f121c319e46b1b505b6e1b1e09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498926"
---
# <a name="request-limits-for-translator-text"></a>Translator Text 的要求限制

本文提供 Translator Text API 的節流限制。 服務包括翻譯、直譯、句子長度偵測、語言偵測和替代翻譯。

## <a name="character-and-array-limits-per-request"></a>每個請求的字元和陣列限制

每個翻譯請求僅限於 5，000 個字元，跨您要翻譯的所有目的語言。 例如，發送 1，500 個字元的翻譯請求以轉換為 3 種不同的語言會導致請求大小為 1，500x3 = 4，500 個字元，從而滿足請求限制。 系統會依照每個字元收費，而不是依照要求數目收費。 建議發送較短的請求。

下表列出了翻譯文本 API 的每個操作的陣列元素和字元限制。

| 作業 | 陣列元素的最大大小 |   陣列元素的最大數量 |  最大請求大小（字元） |
|:----|:----|:----|:----|
| Translate | 5,000 | 100   | 5,000 |
| Transliterate | 5,000 | 10    | 5,000 |
| Detect | 10,000 | 100 |   50,000 |
| BreakSentence | 10,000    | 100 | 50,000 |
| 字典查閱| 100 |  10  | 1,000 |
| 字典範例 | 100 文本和 100 翻譯 （共 200）| 10|   2,000 |

## <a name="character-limits-per-hour"></a>每小時的字元限制

每小時的字元限制是以 Translator Text 訂用帳戶層為基礎。 

每小時配額應在整個小時內均勻使用。 例如，在 F0 層限制時每小時 200 萬個字元，字元的消耗速度不應超過每分鐘滑動視窗大約 33，300 個字元（200 萬個字元除以 60 分鐘）。

如果達到或超過這些限制，或在短時間內發送了過大的配額部分，您可能會收到超出配額的回應。 併發請求沒有限制。

| 層 | 字元限制 |
|------|-----------------|
| F0 | 每小時 2 百萬個字元 |
| S1 | 每小時 4 千萬個字元 |
| S2 / C2 | 每小時 4 千萬個字元 |
| S3 / C3 | 每小時 1 億 2000 萬個字元 |
| S4 / C4 | 每小時 2 億個字元 |

[多服務訂閱](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)的限制與 S1 層相同。

這些限制僅限於微軟的標準翻譯模型。 使用自訂翻譯器的自訂翻譯模型限制為每秒 1，800 個字元。

## <a name="latency"></a>Latency

使用標準模型時，翻譯文本 API 的最大延遲為 15 秒，使用自訂模型時的最大延遲為 120 秒。 通常 *，100 個字元內文本的*回應將在 150 毫秒到 300 毫秒內返回。 自訂轉換器模型在持續請求速率上具有類似的延遲特徵，並且請求速率間歇性時可能具有更高的延遲。 回應時間將因請求和語言對的大小而異。 如果您在該時間範圍內沒有收到翻譯或[錯誤回應](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)，請檢查您的代碼、網路連接和重試。 

## <a name="sentence-length-limits"></a>句子長度限制

使用 [BreakSentence](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-break-sentence) 函式時，句子長度受限於 275 個字元。 下列語言有一些例外狀況：

| 語言 | 程式碼 | 字元限制 |
|----------|------|-----------------|
| 中文 | zh | 132 |
| 德文 | de | 290 |
| 義大利文 | it | 280 |
| 日文 | ja | 150 |
| 葡萄牙文 | pt | 290 |
| 西班牙文 | es | 280 |
| 義大利文 | it | 280 |
| 泰文 | th | 258 |

> [!NOTE]
> 這項限制不適用於翻譯。

## <a name="next-steps"></a>後續步驟

* [定價](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [區域可用性](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator Text API 參考](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
