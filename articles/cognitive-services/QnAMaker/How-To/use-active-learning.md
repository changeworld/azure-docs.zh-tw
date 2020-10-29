---
title: 使用主動式學習與知識庫-QnA Maker
description: 瞭解如何使用主動式學習來改善知識庫的品質。 在不移除或變更現有問題的情況下，進行審查、接受或拒絕、新增。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 8492e2722e456b689e23041726f6eaf94e284c3b
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "93028117"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>使用主動式學習來改善知識庫

[主動式學習](../Concepts/active-learning-suggestions.md) 可讓您藉由建議替代問題，來改善知識庫的品質。 系統會將使用者提交納入考慮，並在替代問題清單中顯示為建議。 您可以彈性地將這些建議新增為替代問題或予以拒絕。

您的知識庫不會自動變更。 為了讓變更生效，您必須接受建議。 這些建議會新增問題，但不會變更或移除現有的問題。


## <a name="upgrade-runtime-version-to-use-active-learning"></a>升級執行階段版本以使用主動式學習

執行階段 4.4.0 版和更新版本可支援主動式學習。 如果您的知識庫是在舊版中建立的，請[升級執行階段](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)以使用這項功能。

## <a name="turn-on-active-learning-for-alternate-questions"></a>開啟適用于替代問題的主動式學習

依預設會關閉主動式學習。 開啟它以查看建議的問題。 開啟主動式學習之後，您必須將用戶端應用程式的資訊傳送至 QnA Maker。 如需詳細資訊，請參閱 [使用 GenerateAnswer 的架構流程以及從 Bot 定型 api](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)。

1. 選取 [ **發行** ] 來發行知識庫。 主動式學習查詢只會從 GenerateAnswer API 預測端點收集。 QnA Maker 入口網站中 [測試] 窗格的查詢不會影響主動式學習。

1. 若要在 QnA Maker 入口網站中開啟主動式學習，請移至右上角，選取您的 **名稱** ，然後移至 [ [**服務設定**](https://www.qnamaker.ai/UserSettings)]。

    ![從 [服務設定] 頁面開啟主動式學習的建議問題替代方案。 在右上方的功能表中選取您的使用者名稱，然後選取 [服務設定]。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 尋找 QnA Maker 服務，然後切換 **主動式學習** 。

    > [!div class="mx-imgBorder"]
    > [![在 [服務設定] 頁面上，切換主動式學習功能。如果您無法切換功能，則可能需要升級您的服務。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上述映射上的確切版本只會顯示為範例。 您的版本可能會不同。

    啟用 **主動學習** 之後，知識庫會根據使用者提交的問題定期提供新問題的建議。 您可以再次切換設定以停用 **主動式學習** 。

## <a name="review-suggested-alternate-questions"></a>查看建議的替代問題

在每個知識庫的 [ **編輯** ] 頁面上 [查看替代建議的問題](improve-knowledge-base.md)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立知識庫](./manage-knowledge-bases.md)
