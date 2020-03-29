---
title: 改善知識庫 - QnA Maker
description: 通過積極學習提高知識庫的品質。 審核、接受或拒絕，添加而不刪除或更改現有問題。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071231"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>使用主動式學習來改善知識庫

[主動學習](../Concepts/active-learning-suggestions.md)通過提出其他問題來提高知識庫的品質。 使用者提交被考慮在內，並作為建議顯示在備用問題清單中。 您可以靈活地將這些建議添加為備選問題或拒絕它們。

您的知識庫不會自動變更。 為了使任何更改生效，您必須接受這些建議。 這些建議會新增問題，但不會變更或移除現有的問題。


## <a name="upgrade-runtime-version-to-use-active-learning"></a>升級執行階段版本以使用活動學習

執行階段 4.4.0 版和更新版本可支援主動式學習。 如果您的知識庫是在舊版中建立的，請[升級執行階段](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以使用這項功能。

## <a name="turn-on-active-learning-for-alternate-questions"></a>打開活動學習以備候補問題

依預設會關閉主動式學習。 開啟它以查看建議的問題。 打開活動學習後，您需要將資訊從用戶端應用發送到 QnA Maker。 有關詳細資訊，請參閱[使用生成應答的體系結構流，並從自動程式訓練 API。](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)

1. 選擇 **"發佈**"以發佈知識庫。 僅從生成應答 API 預測終結點收集活動學習查詢。 對 QnA Maker 門戶中的"測試"窗格的查詢不會影響活動學習。

1. 要在 QnA Maker 門戶中打開活動學習，請轉到右上角，選擇 **"您的姓名**"，轉到[**"服務設置**](https://www.qnamaker.ai/UserSettings)"。

    ![從"服務設置"頁打開活動學習建議的問題備選方案。 在右上角的功能表中選擇您的使用者名，然後選擇"服務設置"。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 尋找 QnA Maker 服務，然後切換**主動式學習**。

    > [!div class="mx-imgBorder"]
    > [![在"服務設置"頁上，在"活動學習"功能上切換。如果無法切換該功能，則可能需要升級服務。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上圖上的確切版本僅顯示為示例。 您的版本可能不同。

    啟用 **"主動學習"** 後，知識庫會根據使用者提交的問題定期提出新問題。 您可以再次切換設定以停用**主動式學習**。

## <a name="review-suggested-alternate-questions"></a>查看建議的備用問題

查看每個知識庫的 **"編輯"** 頁上[的備用建議問題](improve-knowledge-base.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立知識庫](./manage-knowledge-bases.md)