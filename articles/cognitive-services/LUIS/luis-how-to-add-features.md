---
title: 功能-LUIS
titleSuffix: Azure Cognitive Services
description: 使用 Language Understanding (LUIS) 來新增應用程式功能，可改善對類別和模式進行意圖和實體的偵測或預測
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 12445ec5b14f4c274e471bf1b061a3b221664d20
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592299"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>使用功能來提升單字清單的信號

您可以將功能新增到 LUIS 應用程式來改善它的準確性。 這些功能協助 LUIS 的方式是提供某些字組和片語屬於某個應用程式網域詞彙的提示。

請參閱[概念](luis-concept-feature.md)以瞭解使用功能的時機和原因。

## <a name="add-phrase-list-as-a-feature"></a>將片語清單新增為功能

1. 登入[LUIS 入口網站](https://www.luis.ai)，並選取您的**訂**用帳戶和**撰寫資源**，以查看指派給該撰寫資源的應用程式。
1. 在**我的應用程式**] 頁面上選取您的應用程式名稱，以開啟它。
1. 選取 [**組建**]，然後選取應用程式左側面板中的 [**功能**]。

1. 在 [**功能**] 頁面上，選取 [ **+ 建立**]。

1. 在 [**建立新的片語清單] 功能**對話方塊中，輸入名稱，例如 `Cities` 。 在 [**值**] 方塊中，輸入城市的範例，例如 `Seattle` 。 您可以一次輸入一個值，或輸入一組以逗號分隔的值，然後按 **Enter**。

    > [!div class="mx-imgBorder"]
    > ![新增功能（片語清單）城市的螢幕擷取畫面](./media/luis-add-features/add-phrase-list-cities.png)

    一旦您輸入足夠的 LUIS 值，就會出現建議。 您可以 **+ 新增所有**提議的值，或選取個別的詞彙。

1. 如果可以交換使用片語，請將**這些值保留為可互換**。

1. 片語清單可套用至具有**全域**設定的整個應用程式，或套用至特定模型（意圖或實體）。 如果您建立片語清單做為意圖或實體的_功能_，則不會針對全域設定切換。 在此情況下，切換的意義是此功能只會在該模型的本機上，因此不是應用程式的_全域_。

1. 選取 [完成]  。 新的功能會新增至**ML 功能**頁面。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 您可以從 [ **ML 功能**] 頁面上的內容相關工具列中刪除或停用片語清單。

## <a name="next-steps"></a>後續步驟

新增、編輯、刪除或停用功能之後，再次[訓練並測試應用程式](luis-interactive-test.md)，以查看效能是否改善。
