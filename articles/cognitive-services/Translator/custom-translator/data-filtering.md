---
title: 資料篩選 - 自訂翻譯
titleSuffix: Azure Cognitive Services
description: 當您提交要用於訓練自訂系統的文件時，文件會經歷一系列的處理和篩選步驟，準備進行訓練。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595897"
---
# <a name="data-filtering"></a>資料篩選

當您提交要用於訓練自訂系統的文件時，文件會經歷一系列的處理和篩選步驟，準備進行訓練。 這些步驟如下詳述。 篩選的知識可協助您了解「自訂翻譯」中顯示的句子計數，以及您可自行採取哪些步驟來準備文件，以便使用「自訂翻譯」進行訓練。

## <a name="sentence-alignment"></a>句子對齊
如果文件不是 XLIFF、TMX 或 ALIGN 格式，則「自訂翻譯」會逐句對齊來源與目標文件的句子。 翻譯工具不會執行文件對齊，它會依照您的文件命名方式來尋找其他語言的相符文件。 在文件中，「自訂翻譯」會嘗試在其他語言中尋找對應的句子。 它會使用文件標記 (如內嵌 HTML 標記) 來協助對齊。  

如果源端文檔中的句子數與目標端文檔中的句子數之間存在較大差異，則文檔可能最初沒有平行，或者由於其他原因無法對齊。 您需要再次查看每一端上句子差異很大 (> 10%) 的文件配對，以確保它們確實平行。 如果句子計數疑似不同，則「自訂翻譯」會在文件旁邊顯示警告。  


## <a name="deduplication"></a>重複資料刪除
「自訂翻譯」會從訓練資料中移除出現於測試和調整文件的句子。移除作業會以動態方式發生在訓練回合中，而不會發生在資料處理步驟中。 「自訂翻譯」會在移除之前，在專案概觀中向您報告句子計數。  

## <a name="length-filter"></a>長度篩選
* 移除任一邊只有一個字組的句子。
* 移除任一邊有超過 100 個字組的句子。中文、日文、韓文均豁免。
* 移除具有少於 3 個字元的句子。 中文、日文、韓文均豁免。
* 移除具有超過 2000 個中文、日文、韓文字元的句子。
* 移除具有少於 1% 字母字元的句子。
* 移除包含超過 50 個字組的字典項目。

## <a name="white-space"></a>空白字元
* 以單一空格字元，取代任何空白字元序列，包括索引標籤和 CR/LF 序列。
* 移除句子中的前置或尾端空格。

## <a name="sentence-end-punctuation"></a>句子結尾標點符號
以單一執行個體取代多個句子結尾標點符號字元。  

## <a name="japanese-character-normalization"></a>日文字元正規化
將全寬字母和數位轉換為半寬度字元。

## <a name="unescaped-xml-tags"></a>未逸出的 XML 標記
篩選作業會將未逸出的標記轉換成逸出的標記：
* `&lt;` 變成 `&amp;lt;`
* `&gt;` 變成 `&amp;gt;`
* `&amp;` 變成 `&amp;amp;`

## <a name="invalid-characters"></a>無效字元
「自訂翻譯」會移除包含 Unicode 字元 U+FFFD 的句子。 字元 U+FFFD 表示編碼轉換失敗。

## <a name="next-steps"></a>後續步驟

- 在「自訂翻譯」中[將模型定型](how-to-train-model.md)。
