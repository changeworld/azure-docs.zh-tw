---
title: 描述符 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用 Language Understanding (LUIS) 來新增應用程式功能，可改善對類別和模式進行意圖和實體的偵測或預測
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631454"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>使用描述符提升單字清單的訊號

您可以將功能新增到 LUIS 應用程式來改善它的準確性。 這些功能協助 LUIS 的方式是提供某些字組和片語屬於某個應用程式網域詞彙的提示。

[描述符](luis-concept-feature.md)(短語清單)包括屬於同一類且必須類似處理的值(例如,城市或產品的名稱)的一組值(單詞或短語)。 LUIS 對於其中一個值的認識也會自動套用到其他值。 此清單與匹配單詞[的清單實體](reference-entity-list.md)(精確文字匹配)不同。

描述符將應用域的詞彙量添加到 LUIS 有關這些單詞的第二個信號。

檢視[功能概念](luis-concept-feature.md),以瞭解何時以及為什麼使用描述符。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>新增描述子

1. 通過按下 **「我的應用」** 頁上的名稱打開應用,然後單擊 **「生成**」,然後按一下應用左側面板中的 **「描述符**」。

1. 描述**符**頁上,按下 **「添加描述符**」。

1. 在「**創建新短語清單描述符」** 對話框中,輸入描述符`Cities`等 名稱。 在 **「值」** 框中, 鍵入描述符的`Seattle`值,如 。 您可以一次輸入一個值，或輸入一組以逗號分隔的值，然後按 **Enter**。

    > [!div class="mx-imgBorder"]
    > ![新增描述子 城市](./media/luis-add-features/add-phrase-list-cities.png)

    為 LUIS 輸入足夠的值後,將顯示建議。 您可以**新增所有**建議的值,或選擇單個術語。

1. 如果添加的描述符值是可互換使用的替代方法,**則保留這些值是可互換**的。

1. 短語清單可以應用於具有**全域**設置的整個應用,也可以應用於特定模型(意圖或實體)。 如果創建短語清單,作為意圖或實體_的描述符_,則切換設置為非全域。 在這種情況下,切換的含義是描述符只是該模型的一個特徵,因此 _,它不是應用程式的全域_。

1. 選取 [完成]  。 新的描述符將添加到**描述符**頁。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 您可以從**描述符**頁面上的上下文工具列中刪除或停用描述符。

## <a name="next-steps"></a>後續步驟

添加、編輯、刪除或刪除描述符后,再次[訓練和測試應用](luis-interactive-test.md)以查看性能是否提高。
