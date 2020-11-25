---
title: 使用功能改善 LUIS 字組清單
titleSuffix: Azure Cognitive Services
description: 使用 Language Understanding (LUIS) 來新增應用程式功能，可改善對類別和模式進行意圖和實體的偵測或預測
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 33435d2367b92f312d41f0e32fdbb3a93d59a9fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999921"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>使用功能來提升字組清單的信號

您可以將功能新增到 LUIS 應用程式來改善它的準確性。 這些功能協助 LUIS 的方式是提供某些字組和片語屬於某個應用程式網域詞彙的提示。

請參閱瞭解使用功能的時機和原因的 [概念](luis-concept-feature.md) 。

## <a name="add-phrase-list-as-a-feature"></a>將片語清單新增為功能

1. 登入 [LUIS 入口網站](https://www.luis.ai)，然後選取您的 [訂用帳戶] 和 [撰寫資源]，以查看指派給該撰寫資源的應用程式。
1. 在 **我的應用程式** ] 頁面上選取應用程式的名稱，以開啟您的應用程式。
1. 選取 [ **組建**]，然後選取應用程式左側面板中的 [ **功能** ]。

1. 在 [ **功能** ] 頁面上，選取 [ **+ 建立**]。

1. 在 [ **建立新的片語清單功能** ] 對話方塊中，輸入名稱，例如 `Cities` 。 在 [ **值** ] 方塊中，輸入城市的範例，例如 `Seattle` 。 您可以一次輸入一個值，或輸入一組以逗號分隔的值，然後按 **Enter**。

    > [!div class="mx-imgBorder"]
    > ![新增功能 (片語清單) 城市的螢幕擷取畫面](./media/luis-add-features/add-phrase-list-cities.png)

    一旦您輸入足夠的 LUIS 值，就會出現建議。 您可以 **+ 新增所有** 建議的值，或選取個別的詞彙。

1. 如果片語可以交替使用，請將 **這些值** 保持可互換的狀態。

1. 片語清單可以套用至具有 **全域** 設定的整個應用程式，或套用至特定模型 (意圖或實體) 。 如果您建立片語清單，做為意圖或實體的 _功能_ ，則不會針對 global 設定切換開關。 在此情況下，切換的意義是此功能只適用于該模型，因此不是應用程式的 _全域_ 功能。

1. 選取 [完成]。 新的功能會新增至 **ML 功能** 頁面。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> 您可以從 [ **ML 功能** ] 頁面上的內容工具列中刪除或停用片語清單。

## <a name="global-phrase-list-applies-to-entire-app"></a>全域片語清單適用于整個應用程式

片語清單應套用至其所要協助的意圖或實體，但有時可能會將片語清單套用至整個應用程式做為 **全域** 功能。

在 [ML 功能] 頁面上，選取 [片語] 清單，然後在頂端的內容工具列中選取 [ **設為全域** ]。

## <a name="model-as-a-feature"></a>模型即功能

實體可以是 [意圖或實體的功能](luis-concept-feature.md)。

若要將實體新增為意圖的功能，請從 [意圖] 頁面選取意圖，然後選取內容工具列上方的 [ **+ 新增功能** ]。 此清單會包含所有可套用為特徵的片語清單和實體。

若要將實體新增為另一個實體的功能，您可以使用 [實體](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) 選擇區在意圖詳細資料頁面上新增此功能，也可以在 [實體詳細資料] 頁面上 [加入](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) 此功能。

## <a name="next-steps"></a>後續步驟

新增、編輯、刪除或停用功能之後，請再次 [定型並測試應用程式](luis-interactive-test.md) ，以查看效能是否改善。
