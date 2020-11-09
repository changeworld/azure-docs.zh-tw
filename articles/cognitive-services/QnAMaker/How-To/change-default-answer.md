---
title: 取得預設的解答-QnA Maker
description: 當沒有任何相符的問題時，就會傳回預設的答案。 您可能會想要變更標準預設答案的預設答案。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: ceff3127eba724ba9aa9bc8f9398d0f27ba687eb
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376601"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>變更 QnA Maker 資源的預設答案

若找不到答案，則應傳回知識庫的預設答案。 如果您使用的是用戶端應用程式（例如 [Azure Bot 服務](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna)），它可能也會有個別的預設答案，指出沒有符合分數閾值的答案。

## <a name="types-of-default-answer"></a>預設答案的類型

您的知識庫中有兩種類型的預設答案。 請務必瞭解每個從預測查詢傳回的方式和時機：

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (穩定版本) ](#tab/v1)

|預設答案的類型|解答的描述|
|--|--|
|未決定任何答案時的 KB 答案|`No good match found in KB.` -當 [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 找不到問題的任何相符答案時， `DefaultAnswer` 就會傳回 App service 的設定。 相同 QnA Maker 資源中的所有知識庫都會共用相同的預設答案文字。<br>您可以透過 App service 管理 Azure 入口網站中的設定，或使用 REST Api 來 [取得](https://docs.microsoft.com/rest/api/appservice/webapps/listapplicationsettings) 或 [更新](https://docs.microsoft.com/rest/api/appservice/webapps/updateapplicationsettings) 設定。|
|後續提示指令文字|在對話流程中使用後續的提示時，您可能不需要 QnA 組的答案，因為您想要讓使用者從後續的提示中選取。 在此情況下，您可以設定預設的回應文字來設定特定的文字，此文字會隨後續提示的每個預測一起傳回。 文字是要顯示為所選後續提示的解說文字。 這是預設答案文字的範例 `Please select from the following choices` 。 本檔的後續幾節將說明這項設定。 也可以設定為使用 REST API 之知識庫定義的 `defaultAnswerUsedForExtraction` 一部分[REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)。|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 受控 (預覽版本) ](#tab/v2)

|預設答案的類型|解答的描述|
|--|--|
|未決定任何答案時的 KB 答案|`No good match found in KB.` -當 [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) 找不到問題的任何相符答案時，就會顯示預設文字回應。 在 QnA Maker 受控 (預覽) 您可以在 **知識庫的設定** 中設定此文字。 <br><br> ![QnA Maker 受控 (預覽) 設定預設答案](../media/qnamaker-how-change-default-answer/qnamaker-v2-change-default-answer.png)|
|後續提示指令文字|在對話流程中使用後續的提示時，您可能不需要 QnA 組的答案，因為您想要讓使用者從後續的提示中選取。 在此情況下，您可以設定預設的回應文字來設定特定的文字，此文字會隨後續提示的每個預測一起傳回。 文字是要顯示為所選後續提示的解說文字。 這是預設答案文字的範例 `Please select from the following choices` 。 本檔的後續幾節將說明這項設定。 您也可以使用 REST API，將此設定為知識庫定義的一部分 `defaultAnswerUsedForExtraction` 。 [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)|

---

### <a name="client-application-integration"></a>用戶端應用程式整合

針對用戶端應用程式，例如具有 **Azure bot service** 的 bot，您可以選擇下列常見案例：

* 使用知識庫的設定
* 在用戶端應用程式中使用不同的文字，以區別何時會傳回答案，但不符合分數閾值。 這段文字可以是儲存在程式碼中的靜態文字，也可以儲存在用戶端應用程式的設定清單中。

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>當您建立知識庫時，請設定後續提示的預設答案

當您建立新的知識庫時，預設答案文字就是其中一項設定。 如果您選擇不在建立程式期間設定它，您可以稍後使用下列程式加以變更。

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>在 QnA Maker 入口網站中變更後續提示的預設答案

當 QnA Maker 服務未傳回任何答案時，就會傳回知識庫預設答案。

1. 登入 [QnA Maker 入口網站](https://www.qnamaker.ai/) ，並從清單中選取您的知識庫。
1. 從巡覽列選取 [ **設定** ]。
1. 變更 [ **管理知識庫** ] 區段中 **預設答案文字** 的值。

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="QnA Maker 入口網站 [設定] 頁面的螢幕擷取畫面，其中反白顯示預設答案文字方塊。":::

1. 選取 [ **儲存並定型** ] 以儲存變更。

## <a name="next-steps"></a>後續步驟

* [建立知識庫](../How-to/manage-knowledge-bases.md)