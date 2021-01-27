---
title: 句子配對與對齊 - 自訂翻譯工具
titleSuffix: Azure Cognitive Services
description: 在執行訓練時，出現在平行文件中的句子皆已配對或已對齊。 自訂翻譯工具會藉由讀取句子和此句子的翻譯，一次一個句子地學習翻譯。 接著將這兩個句子中的字組和片語彼此對齊。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 0c33d766bfd3dff47ddb151e8ce4ea7b25c37548
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897946"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>平行文件中的句子配對與對齊

上傳檔之後，平行檔中的句子會成對或對齊。 自訂翻譯工具會報告其可以在每個資料集中配對為「對齊句子」的數目。

## <a name="pairing-and-alignment-process"></a>配對和對齊程序

自訂翻譯工具會一次一個句子地學習句子翻譯。 它會讀取來源文字中的句子，然後從目標文字轉譯這個句子。 接著將這兩個句子中的字組和片語彼此對齊。 此程式可讓它在一個句子中建立單字和片語的對應，以轉譯句子中的對等單字和片語。 對齊功能會嘗試確保系統是在彼此的翻譯句子上進行訓練。

## <a name="pre-aligned-documents"></a>預先對齊的文件

如果您知道您有平行文件，您可能會提供預先對齊的文字檔案來覆寫句子對齊。 您可以將這兩份文件中的所有句子擷取到文字檔，並組織成每一行一個句子，然後以 `.align` 副檔名來上傳檔案。 `.align` 副檔名會告知自訂翻譯工具應該略過句子對齊。

為了獲得最佳結果，請嘗試確定您的檔案內容是每行一個句子。 一個句子中不能有換行字元，因為這會導致不好的對齊效果。

## <a name="suggested-minimum-number-of-sentences"></a>建議的最小句子數目

為了讓定型成功，下表顯示每個檔案類型所需的最小句子數目。這項限制是安全的網路，可確保您的並行句子包含足夠的獨特詞彙來成功定型轉譯模型。 一般的指導方針是讓人工翻譯品質的多網域平行處理句子產生更高品質的模型。

| 文件型別   | 建議的最小句子計數 | 最大句子計數 |
|------------|--------------------------------------------|--------------------------------|
| 訓練   | 10,000                                     | 沒有上限                 |
| 調整     | 500                                      | 2,500       |
| 測試    | 500                                      | 2,500  |
| Dictionary | 0                                          | 沒有上限                 |

> [!NOTE]
> - 如果未符合定型的10000最小句子計數，定型將不會啟動，而且將會失敗。 
> - 調整和測試是選擇性的。 如果您未提供這些值，系統會從定型中移除適當的百分比，以用於驗證和測試。 
> - 您可以只使用字典資料來定型模型。 請參閱 [什麼是字典](./what-is-dictionary.md)。

## <a name="next-steps"></a>後續步驟

- 了解如何在自訂翻譯工具中使用[字典](what-is-dictionary.md)。
