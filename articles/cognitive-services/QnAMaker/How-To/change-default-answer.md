---
title: 取得預設解答 QnA Maker
description: 當不符合問題時，就會傳回預設答案。 您可能想要變更標準預設答案的預設答案。
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: d37e63d84be58e6ccd2f1e23a1344961d39ffa01
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87054173"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>變更 QnA Maker 資源的預設答案

當找不到答案時，應該會傳回知識庫的預設答案。 如果您使用的是[Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot)之類的用戶端應用程式，它可能也會有個別的預設答案，指出沒有符合分數臨界值的答案。

## <a name="types-of-default-answer"></a>預設答案的類型

您的知識庫中有兩種類型的預設答案。 請務必瞭解每個從預測查詢傳回的方式和時機：


|問題類型|解答的描述|
|--|--|
|未決定答案時的 KB 答案|`No good match found in KB.`-當[GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)找不到符合問題的答案時， `DefaultAnswer` 就會傳回 App service 的設定。 相同 QnA Maker 資源中的所有知識庫會共用相同的預設回應文字。<br>您可以透過 App service 或使用 REST Api 來管理 Azure 入口網站中的設定，以[取得](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings)或[更新](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings)設定。|
|後續提示指示文字|在對話流程中使用後續的提示時，您可能不需要 QnA 配對中的答案，因為您想要讓使用者從後續的提示中選取。 在此情況下，藉由設定預設的回應文字來設定特定文字，每次進行後續的預測時，都會傳回提示。 此文字的目的是要將解說文字顯示為後續追蹤提示的選取專案。 此預設答案文字的範例為 `Please select from the following choices` 。 這項設定會在本檔的後續幾節中說明。 也可以使用 REST API，設定為知識庫定義的 `defaultAnswerUsedForExtraction` 一部分[REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。|

### <a name="client-application-integration"></a>用戶端應用程式整合

針對用戶端應用程式（例如具有**Azure bot 服務**的 bot），您可以從下列常見案例中進行選擇：

* 使用知識庫的設定
* 在用戶端應用程式中使用不同的文字來區別何時會傳回答案，但不符合分數臨界值。 此文字可以是儲存在程式碼中的靜態文字，或可以儲存在用戶端應用程式的 [設定] 清單中。

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>當您建立知識庫時，設定後續追蹤提示的預設答案

當您建立新的知識庫時，預設的回應文字是其中一個設定。 如果您選擇不在建立程式期間進行設定，您可以在稍後使用下列程式進行變更。

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>在 QnA Maker 入口網站中變更後續追蹤提示的預設答案

當 QnA Maker 服務未傳回任何答案時，會傳回知識庫的預設答案。

1. 登入[QnA Maker 入口網站](https://www.qnamaker.ai/)，並從清單中選取您的知識庫。
1. 從巡覽列選取 [**設定**]。
1. 變更 [**管理知識庫**] 區段中 [**預設回應文字**] 的值。

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="QnA Maker 入口網站 [設定] 頁面的螢幕擷取畫面，其中已反白顯示預設答案文字方塊。":::

1. 選取 [**儲存並定型**] 以儲存變更。

## <a name="next-steps"></a>後續步驟

* [建立知識庫](../How-to/manage-knowledge-bases.md)
