---
title: 描述項 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用 Language Understanding (LUIS) 來新增應用程式功能，可改善對類別和模式進行意圖和實體的偵測或預測
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74123140"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>使用描述項提升單字清單的信號

您可以將功能新增到 LUIS 應用程式來改善它的準確性。 這些功能協助 LUIS 的方式是提供某些字組和片語屬於某個應用程式網域詞彙的提示。 

[描述項](luis-concept-feature.md)（片語清單）包括屬於同一類且必須類似處理的值（例如，城市或產品的名稱）的一組值（單詞或短語）。 LUIS 對於其中一個值的認識也會自動套用到其他值。 此清單與匹配單詞[的清單實體](reference-entity-list.md)（精確文本匹配）不同。

描述項將應用域的詞彙量添加到 LUIS 有關這些單詞的第二個信號。

查看[功能概念](luis-concept-feature.md)，以瞭解何時以及為什麼使用描述項。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>添加描述項

1. 通過按一下 **"我的應用"** 頁上的名稱打開應用，然後按一下 **"生成**"，然後按一下應用左側面板中的 **"描述項**"。 

1. 在**描述項**頁上，按一下 **"添加描述項**"。 
 
1. 在"**創建新片語清單描述項"** 對話方塊中，輸入描述項等`Cities`名稱。 在 **"值"** 框中，鍵入描述項的值，如`Seattle`。 您可以一次輸入一個值，或輸入一組以逗號分隔的值，然後按 **Enter**。

    > [!div class="mx-imgBorder"]
    > ![添加描述項 城市](./media/luis-add-features/add-phrase-list-cities.png)

    為 LUIS 輸入足夠的值後，將顯示建議。 您可以**添加所有**建議的值，或選擇單個術語。

1. 如果添加的描述項值是可互換使用的替代方法，**則保留這些值是可互換**的。

1. 選擇 **"完成**"。 新的描述項將添加到**描述項**頁。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 您可以從**描述項**頁面上的上下文工具列中刪除或停用描述項。

## <a name="next-steps"></a>後續步驟

添加、編輯、刪除或刪除描述項後，再次[訓練和測試應用](luis-interactive-test.md)以查看性能是否提高。
