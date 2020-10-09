---
title: 動態字典-翻譯工具
titleSuffix: Azure Cognitive Services
description: 本文說明如何使用 Azure 認知服務翻譯工具的動態字典功能。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: de45867e717f001ab54e16c4b21f04494affd326
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "83996976"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>如何使用動態字典

如果已經知道想要套用至單字或片語的翻譯，則可以在要求內以標記來提供。 動態字典只適用于複合名詞，例如適當的名稱和產品名稱。

**語法：**

<s： dictionary 轉譯 = "翻譯片語" >片語</mstrans： dictionary>

**需求：**

* `From`和 `To` 語言必須包含英文和其他支援的語言。 
* 您必須 `From` 在 API 轉譯要求中包含參數，而不是使用自動偵測功能。 

**範例：en-de:**

來源輸入： `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

目標輸出： `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

不論有無 HTML 模式，此功能都以相同的方式運作。

請謹慎使用此功能。 自訂翻譯的較佳方式是使用自訂翻譯。 Custom Translator 會完全利用內容和統計機率。 如果您已有或能夠建立定型資料，以便在上下文中顯示您的單字或片語，則會得到更好的結果。 您可以在中找到自訂翻譯的詳細資訊 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) 。