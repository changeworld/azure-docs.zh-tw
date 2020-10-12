---
title: 在影片索引子中自訂語言模型 - Azure
titleSuffix: Azure Media Services
description: 本文概要說明影片索引子中的語言模型是什麼，以及如何加以自訂。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 29490e08748a37f7eb93fbb8804f55f74c53df35
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87047133"
---
# <a name="customize-a-language-model-with-video-indexer"></a>使用影片索引子自訂語言模型

影片索引子透過與 Microsoft [自訂語音服務](https://azure.microsoft.com/services/cognitive-services/custom-speech-service/)的整合支援自動語音辨識。 您可以上傳適應文字 (也就是從您想要引擎適應其詞彙的網域中取得的文字) 自訂語言模型。 您定型模型時，會辨識調適文字中出現的新文字，並假設預設發音，而且語言模型將了解文字可能出現的新序列。 支援的自訂語言模型包括英文、西班牙文、法文、德文、義大利文、中文 (簡體)、日文、俄文、葡萄牙文、印度文和韓文。 

以「Kubernetes」(在 Azure Kubernetes 服務的內容中) 之類相當明確的字為例。 由於這對於影片索引子而言是新字，因此會將它辨識為「社群」。 您必須將模型定型，才能將它辨識為「Kubernetes」。 在其他情況下，這些文字確實存在，但是語言模型並未預期這些文字出現在特定內容中。 例如，「容器服務」不是非特製化語言模型視為特定字組的 2 字組序列。

您可以選擇上傳在文字檔內的清單中沒有內容的文字。 這會被視為部份調適。 或者，您也可以上傳內容有關文件或句子的文字檔，以提升適應效果。

您可以使用影片索引子 API 或網站建立和編輯自訂語言模型，如本主題的[後續步驟](#next-steps)小節中的主題所述。

## <a name="best-practices-for-custom-language-models"></a>自訂語言模型的最佳作法

影片索引子可根據字詞組合學習，因此達到最佳的學習效果：

* 盡可能提供可能說出的句子實例。
* 每行一句，不要超過一句。 否則系統會學習跨越句子的機率。
* 一句可以只有一個字，以便提升該字，不過系統學習完整句子的效果最好。
* 加入新的字詞或縮寫時，盡可能提供多個在完整的句子中使用的範例，以盡量提供給系統最多的內容。
* 嘗試加入多個適應選項，並查看運作的效果。
* 避免多次重複完全相同的句子。 它可能會造成相對於其餘輸入的偏差。
* 避免包含不常用的符號 (~、#、@、%、&)，否則會予以捨棄。 出現這些符號的句子也將予以捨棄。
* 避免太大量的輸入，例如數十萬個句子，因為這麼做會減弱提升的效果。

## <a name="next-steps"></a>後續步驟

[使用 API 自訂語言模型](customize-language-model-with-api.md)

[使用網站自訂語言模型](customize-language-model-with-website.md)
