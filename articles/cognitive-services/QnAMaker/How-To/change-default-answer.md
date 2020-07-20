---
title: 取得預設解答 QnA Maker
description: 當不符合問題時，就會傳回預設答案。 您可能想要變更標準預設答案的預設答案。
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979960"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>變更 QnA Maker 資源的預設答案

當找不到答案時，應該會傳回知識庫的預設答案。 如果您使用的是[Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)之類的用戶端應用程式，它可能也會有個別的預設答案，指出沒有符合分數臨界值的答案。

## <a name="set-default-answer-when-you-create-knowledge-base"></a>設定建立知識庫時的預設答案

當您建立新的知識庫時，預設的回應文字是其中一個設定。 如果您選擇不在建立程式期間進行設定，您可以在稍後使用下列程式進行變更。

## <a name="change-default-answer-in-qna-maker-portal"></a>變更 QnA Maker 入口網站中的預設答案

當 QnA Maker 服務未傳回任何答案時，會傳回知識庫的預設答案。

1. 登入[QnA Maker 入口網站](https://www.qnamaker.ai/)，並從清單中選取您的知識庫。
1. 從巡覽列選取 [**設定**]。
1. 變更 [**管理知識庫**] 區段中 [**預設回應文字**] 的值。

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="QnA Maker 入口網站 [設定] 頁面的螢幕擷取畫面，其中已反白顯示預設答案文字方塊。":::

1. 選取 [**儲存並定型**] 以儲存變更。

## <a name="next-steps"></a>後續步驟

* [建立知識庫](../How-to/manage-knowledge-bases.md)