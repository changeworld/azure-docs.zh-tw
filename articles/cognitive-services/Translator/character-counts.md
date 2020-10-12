---
title: 字元計數-翻譯工具
titleSuffix: Azure Cognitive Services
description: 本文說明 Azure 認知服務 Translator 如何計算字元，讓您可以瞭解它內嵌內容的方式。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7fad90cf47591b5ffab4232c2be3180138738f01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83992823"
---
# <a name="how-the-translator-counts-characters"></a>Translator 如何計算字元

翻譯工具會將輸入文字的每個 unicode 程式碼點計算成一個字元。 語言文字的每一個翻譯都會計算為不同的翻譯，即使您是在單一 API 呼叫中發出轉譯為多種語言的要求亦然。 回應的長度無關緊要。

會計算的項目包括：

* 在要求本文中傳遞給翻譯工具的文字
   * 使用翻譯、音譯和字典查閱方法時，會計算 `Text`
   * 使用字典範例方法時，會計算 `Text` 和 `Translation`
* 所有標記： HTML、XML 標記等，在要求主體的文字欄位內。 用來建置要求的 JSON 表示法 (例如 "Text:") 則不會計入。
* 個別字母
* 標點符號
* 空格、定位字元、標記和任何種類的空白字元
* 以 Unicode 定義的每個字碼指標
* 重複的翻譯，即使您先前已翻譯過相同文字亦然

針對以表意文字（例如中文和日文漢字）為基礎的腳本，Translator 服務仍會計算 Unicode 程式碼點的數目，每個 ideogram 一個字元。 例外狀況： Unicode 代理的計數為兩個字元。

要求、字組、位元組或句子的數目與字元計數無關。

對 Detect 和 BreakSentence 方法的呼叫不會計入字元使用量中。 不過，我們預期在這些對 Detect 和 BreakSentence 方法的呼叫，與其他被計入使用量之函式的使用之間，會有合理的比例。 如果您發出的 Detect 或 BreakSentence 呼叫數目超過其他計數方法的數目 100 倍，則 Microsoft 會保留限制您使用 Detect 和 BreakSentence 方法的權利。

有關字元計數的詳細資訊位於 [TRANSLATOR 常見問題](https://www.microsoft.com/en-us/translator/faq.aspx)中。
