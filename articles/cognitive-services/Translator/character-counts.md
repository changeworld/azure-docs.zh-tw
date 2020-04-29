---
title: 字元計數 - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 本文說明 Azure 認知服務翻譯工具文字 API 如何計算字元，讓您可以瞭解它內嵌內容的方式。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cb70b8624ac0d909511032622948f14f8764b153
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "73888150"
---
# <a name="how-the-translator-text-api-counts-characters"></a>翻譯工具文字 API 如何計算字元

翻譯工具文字 API 會將輸入文字的每個 Unicode 字碼指標計算為一個字元。 語言文字的每一個翻譯都會計算為不同的翻譯，即使您是在單一 API 呼叫中發出轉譯為多種語言的要求亦然。 回應的長度無關緊要。

會計算的項目包括：

* 要求主體中傳遞至翻譯工具文字 API 的文字
   * 使用翻譯、音譯和字典查閱方法時，會計算 `Text`
   * 使用字典範例方法時，會計算 `Text` 和 `Translation`
* 所有標記：要求主體的文字欄位內的 HTML、XML 標記等。 用來建置要求的 JSON 表示法 (例如 "Text:") 則不會計入。
* 個別字母
* 標點符號
* 空格、定位字元、標記和任何種類的空白字元
* 以 Unicode 定義的每個字碼指標
* 重複的翻譯，即使您先前已翻譯過相同文字亦然

對於以中文和日文漢字等表意文字為基礎的指令碼，翻譯工具文字 API 仍會計算 Unicode 字碼指標的數目，每一個表意文字計為一個字元。 例外狀況： Unicode 代理的計數為兩個字元。

要求、字組、位元組或句子的數目與字元計數無關。

對 Detect 和 BreakSentence 方法的呼叫不會計入字元使用量中。 不過，我們預期在這些對 Detect 和 BreakSentence 方法的呼叫，與其他被計入使用量之函式的使用之間，會有合理的比例。 如果您發出的 Detect 或 BreakSentence 呼叫數目超過其他計數方法的數目 100 倍，則 Microsoft 會保留限制您使用 Detect 和 BreakSentence 方法的權利。


如需有關字元計數的詳細資訊，請參閱 [Microsoft Translator 常見問題集](https://www.microsoft.com/en-us/translator/faq.aspx) \(英文\)。
