---
title: 語言支援 - 文字分析 API
titleSuffix: Azure Cognitive Services
description: 文字分析 API 支援的自然語言清單。 本文說明每項作業支援的語言：情感分析、關鍵片語擷取、語言偵測，以及實體辨識。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221199"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>文字分析 API 支援的語言和區域

本文說明每項作業支援的語言：情感分析、關鍵字組提取、語言偵測和命名實體辨識。

## <a name="language-detection"></a>語言偵測

文字分析 API 可以偵測各種不同的語言、變體、方言，以及某些地區/文化語言。  語言偵測會傳回語言的「指令碼」。 例如，針對片語 "I have a dog"，它會傳回 `en` 而不是 `en-US`。 唯一的特殊案例是中文，若它可根據提供的文字決定指令碼，語言偵測功能會傳回 `zh_CHS` 或 `zh_CHT`。 若無法識別中文文件的特定指令碼，則只會傳回 `zh`。

我們未發佈這項功能確切的語言清單，但它可以偵測到多種不同的語言、變體、方言，以及某些區域性/文化語言。 

如果您有以較不常用的語言表示的內容，您可以嘗試使用「語言偵測」，看它是否會傳回代碼。 對於無法偵測到的語言，會產生 `unknown` 回應。

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>情感分析、關鍵片語擷取和命名實體辨識

情感分析、關鍵片語擷取和實體辨識的支援語言清單更具選擇性，因為分析器會進一步調整以配合其他語言的語言規則。 在命名實體辨識 v2 中，對一組完整[實體類型](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)的支援目前僅限於下列語言： 
* 英文
* 中文-簡體
* 法文
* 德文
* 西班牙文

只有 `Person`、`Location` 和 `Organization` 命名的實體會針對其他語言傳回。

## <a name="language-list-and-status"></a>語言清單和狀態

語言支援一開始推出時處於預覽階段，之後會進入正式運作 (GA) 狀態，彼此且與整體文字分析服務互不影響。 即使文字分析 API 轉換成正式運作，也可以保留預覽階段中的語言。

> [!NOTE]
> 如需命名實體辨識（NER） v3 公開預覽的詳細語言支援，請參閱[命名實體類型](named-entity-types.md)。

| Language              | 語言代碼 | 情感 | 主要片語 | 具名實體辨識 | 實體連結 |       注意        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| 阿拉伯文                |     `ar`      |           |             |           ✔ \*           |                |                    |
| 捷克文                 |     `cs`      |           |             |           ✔ \*           |                |                    |
| 中文-簡體    |   `zh-hans`   |  ✔ \*\*   |             |            ✔             |                | 也接受 `zh`                   |
| 中文-繁體   |   `zh-hant`   |  ✔ \*\*   |             |                          |                |                    |
| 丹麥文                |     `da`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 荷蘭文                 |     `nl`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 英文               |     `en`      |   ✔ \**   |      ✔      |          ✔ \*\*          |     ✔ \**      |                    |
| 芬蘭文               |     `fi`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 法文                |     `fr`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| 德文                |     `de`      |   ✔ \**   |      ✔      |            ✔             |                |                    |
| 希臘文                 |     `el`      |   ✔ \*    |             |                          |                |                    |
| 匈牙利文             |     `hu`      |           |             |           ✔ \*           |                |                    |
| 義大利文               |     `it`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 日文              |     `ja`      |   ✔ \**   |      ✔      |           ✔ \*           |                |                    |
| 韓文                |     `ko`      |   ✔ \*\*  |      ✔      |           ✔ \*           |                |                    |
| 挪威文 (巴克摩)   |     `no`      |   ✔ \*    |      ✔      |           ✔ \*           |                | 也接受 `nb`                   |
| 波蘭文                |     `pl`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 葡萄牙文 (葡萄牙) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔ \*           |                | 也接受 `pt` |
| 葡萄牙文 (巴西)   |    `pt-BR`    |           |      ✔      |           ✔ \*           |                |                    |
| 俄文               |     `ru`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 西班牙文               |     `es`      |   ✔\**    |      ✔      |           ✔ \*           |     ✔ \**      |                    |
| 瑞典文               |     `sv`      |   ✔ \*    |      ✔      |           ✔ \*           |                |                    |
| 土耳其文               |     `tr`      |   ✔ \*    |             |           ✔ \*           |                |                    |

\* 語言支援現供預覽

\** 也適用于[情感分析 v3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features)和/或[命名實體辨識 v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)公開預覽。

## <a name="see-also"></a>另請參閱

[認知服務文件頁面](https://docs.microsoft.com/azure/cognitive-services/)   
[認知服務產品頁面](https://azure.microsoft.com/services/cognitive-services/)
