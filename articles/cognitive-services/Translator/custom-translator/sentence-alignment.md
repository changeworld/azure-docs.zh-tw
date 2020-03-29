---
title: 句子配對與對齊 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 在執行訓練時，出現在平行文件中的句子皆已配對或已對齊。 自訂翻譯工具會藉由讀取句子和此句子的翻譯，一次一個句子地學習翻譯。 接著將這兩個句子中的字組和片語彼此對齊。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cf5b2b84142c9104ea5b3afa3ad179fd0ec07449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80370145"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>平行文件中的句子配對與對齊

在訓練時，出現在平行文件中的句子皆已配對或已對齊。 自訂翻譯工具會報告其可以在每個資料集中配對為「對齊句子」的數目。

## <a name="pairing-and-alignment-process"></a>配對和對齊程序

自訂翻譯工具會一次一個句子地學習句子翻譯。 它會從來源中讀取句子，然後從目標中讀取這個句子的翻譯。 接著將這兩個句子中的字組和片語彼此對齊。 此程序可讓自訂翻譯工具為一個句子中的字組和片語，以及此句子翻譯中的同等字組和片語建立對應。 對齊功能會嘗試確保系統是在彼此的翻譯句子上進行訓練。

## <a name="pre-aligned-documents"></a>預先對齊的文件

如果您知道您有平行文件，您可能會提供預先對齊的文字檔案來覆寫句子對齊。 您可以將這兩份文件中的所有句子擷取到文字檔，並組織成每一行一個句子，然後以 `.align` 副檔名來上傳檔案。 `.align` 副檔名會告知自訂翻譯工具應該略過句子對齊。

為了獲得最佳結果，請嘗試確定您的檔案內容是每行一個句子。一個句子中不能有換行字元，因為這會導致不好的對齊效果。

## <a name="suggested-minimum-number-of-sentences"></a>建議的最小句子數

要成功培訓，下表顯示了每種文件類型所需的最小句子數。此限制是一個安全網，以確保您的並行句子包含足夠的唯一詞彙，以成功訓練翻譯模型。 一般準則是有更多的域內並行句子，人類翻譯品質應產生更高品質的模型。

| 文件型別   | 建議的最小刑期數 | 最大句子計數 |
|------------|--------------------------------------------|--------------------------------|
| 訓練   | 10,000                                     | 沒有上限                 |
| 調整     | 500                                      | 2,500       |
| 測試    | 500                                      | 2,500  |
| 字典 | 0                                          | 沒有上限                 |

> [!NOTE]
> - 如果不符合培訓的最低刑期數，培訓將不會開始，並且將失敗。 
> - 調整和測試是可選的。 如果不提供它們，系統將從"培訓"中刪除適當的百分比，用於驗證和測試。 
> - 您可以只使用字典資料來定型模型。 請參閱[什麼是字典](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/what-is-dictionary)。

## <a name="next-steps"></a>後續步驟

- 了解如何在自訂翻譯工具中使用[字典](what-is-dictionary.md)。
