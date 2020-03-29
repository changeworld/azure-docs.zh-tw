---
title: 動態字典 - 翻譯工具文字 API
titleSuffix: Azure Cognitive Services
description: 本文介紹如何使用 Azure 認知服務轉換器文本 API 的動態字典功能。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: 944bca8644da6127e73af04eb75d01697cd34399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446719"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>如何使用動態詞典

如果已經知道想要套用至單字或片語的翻譯，則可以在要求內以標記來提供。 動態字典僅對複合名詞（如專有名稱和產品名稱）是安全的。

**語法：**

<mstrans：詞典翻譯_"短語翻譯">短語</mstrans：字典>

**需求：**

* `From`和`To`語言必須包括英語和其他受支援的語言。 
* 您必須在 API`From`轉換請求中包含參數，而不是使用自動檢測功能。 

**範例：en-de:**

源輸入：`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

目標輸出：`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

不論有無 HTML 模式，此功能都以相同的方式運作。

請謹慎使用該功能。 自訂翻譯的更好方法是使用自訂翻譯。 Custom Translator 會完全利用內容和統計機率。 如果您已有或能夠建立定型資料，以便在上下文中顯示您的單字或片語，則會得到更好的結果。 您可以在 中找到有關自訂轉換器的更多資訊。 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator)
