---
title: 多回合交談-QnA Maker
description: 使用提示和內容來管理 bot 的多重回合（稱為多重回合），從一個問題到另一個問題。 多回合功能是能夠進行反向對話，其中先前的問題內容會影響下一個問題和答案。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 26fc976983fc08857e7771d58f15d0abcd9a1d3c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353216"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>使用後續追蹤提示來建立多回合對話

使用後續追蹤提示和內容來管理 bot 的多重回合（稱為 _多重回合_），從一個問題到另一個問題。

若要查看多回合的運作方式，請參閱下列示範影片：

[![QnA Maker 中的多重回合交談](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>什麼是多重回合交談？

某些問題無法在單一回合中回答。 當您設計用戶端應用程式 (聊天機器人) 對話時，使用者可能會詢問需要篩選或調整的問題，以判斷正確的答案。 您可以藉由向使用者呈現 *後續的提示*，讓此流程得以完成。

當使用者詢問問題時，QnA Maker 會傳回答案 _和_ 任何後續提示。 這個回應可讓您將後續問題顯示為選擇。

> [!CAUTION]
> 多回合提示不會從常見問題檔中解壓縮。 如果您需要多回合解壓縮，請移除將 QnA 組指定為常見問題的問號。

## <a name="example-multi-turn-conversation-with-chat-bot"></a>使用聊天機器人的多重回合對話範例

使用多回合時，聊天機器人會管理與使用者的交談，以判斷最終答案，如下圖所示：

![包含提示的多重回合對話方塊，可引導使用者進行對話](../media/conversational-context/conversation-in-bot.png)

在上圖中，使用者已輸入 **我的帳戶** 來開始交談。 知識庫有三個連結的問題和答案配對。 若要精簡答案，使用者可以選取知識庫中的三個選項之一。  ( # 1) 的問題，有三個後續的提示，在聊天機器人中提供三個選項 ( # 2) 。

當使用者選取 ( # 3) 的選項時，就會顯示下一個精簡選項清單 ( # 4) 。 此順序會繼續 ( # 5) ，直到使用者判斷正確的最終答案 ( # 6) 為止。


### <a name="use-multi-turn-in-a-bot"></a>在 bot 中使用多重回合

發佈您的知識庫之後，您可以選取 [ **建立 Bot** ] 按鈕，將 QnA Maker bot 部署至 Azure bot service。 提示會出現在您為 bot 啟用的聊天用戶端中。

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>從檔結構建立多回合交談

當您建立知識庫時， **填入 KB** 區段會顯示 [ **啟用從 url、.pdf 或 .docx 檔案進行多回合解壓縮** ] 核取方塊。

![啟用多重回合解壓縮的核取方塊](../media/conversational-context/enable-multi-turn.png)

當您選取此選項時，QnA Maker 會解壓縮檔結構中出現的階層。 階層會轉換成後續的提示，而階層的根則會作為父 QnA。 在某些檔中，階層的根沒有可作為答案的內容，您可以提供「預設答案文字」作為替代答案文字，以將這類階層解壓縮。

您只能從 Url、PDF 檔案或 .DOCX 檔案推斷多回合結構。 如需結構的範例，請參閱 [Microsoft Surface 使用者手動 PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)檔案的影像。

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="螢幕擷取畫面顯示使用者手冊中的結構範例。" lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>建立您自己的多回合檔

如果您要建立多回合檔，請記住下列指導方針：

* 使用標題和子標題表示階層。 例如，您可以用 h1 來表示父 QnA 和 h2，以代表應視為提示的 QnA。 使用小標題大小來表示後續階層。 請勿使用樣式、色彩或其他某些機制來暗示檔中的結構，QnA Maker 將不會解壓縮多回合提示。

* 標題的第一個字元必須為大寫。

* 請勿以問號結尾的標題結尾 `?` 。

* 您可以使用 [範例檔](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) 作為範例，建立您自己的多回合檔。

### <a name="adding-files-to-a-multi-turn-kb"></a>將檔案新增至多回合 KB

當您新增階層式檔時，QnA Maker 會從結構判斷後續提示，以建立對話流程。

1. 在 QnA Maker 中，選取使用 [**啟用從 url、.pdf 或 .docx 檔案進行多回合解壓縮**] 建立的現有知識庫。 啟用。
1. 移至 [ **設定** ] 頁面，選取要新增的檔案或 URL。
1. **儲存並定型** 知識庫。

> [!Caution]
> 不支援使用匯出的 TSV 或 .XLS 多重回合知識庫檔案作為新的或空白知識庫的資料來源。 您必須從 QnA Maker 入口網站的 [**設定**] 頁面匯 **入** 該檔案類型，才能將匯出的多回合提示新增至知識庫。


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>使用 Create API 建立具有多回合提示的知識庫

您可以使用 [QnA Maker CREATE API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create)來建立具有多回合提示的知識案例。 提示會在 `context` 屬性的陣列中新增 `prompts` 。

## <a name="show-questions-and-answers-with-context"></a>以內容顯示問題和解答

將只顯示的問答組縮減為具有內容相關交談的配對。

選取 [ **視圖選項**]，然後選取 [ **顯示內容**]。 此清單會顯示包含後續提示的問答配對。

![依內容相關對話篩選問答配對](../media/conversational-context/filter-question-and-answers-by-context.png)

多回合內容會顯示在第一個資料行中。

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="螢幕擷取畫面顯示反白顯示的內容區段。" lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

在上圖中， **#1** 表示資料行中的粗體文字，這表示目前的問題。 父問題是資料列中的最上層專案。 下列任何問題都是連結的問答配對。 這些專案是可選取的，因此您可以立即移至其他內容專案。

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>將現有問答組新增為後續提示

原始問題（即 **我的帳戶**）有後續的提示，例如 **帳戶和登入**。

![「帳戶和登入」的答案和後續提示](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

將後續提示新增至目前未連結的現有問答組。 由於問題未連結到任何問答組，因此必須變更目前的視圖設定。

1. 若要將現有問答組連結至後續提示，請選取問答組的資料列。 若為介面手冊，請搜尋 **登出** 以縮減清單。
1. 在 **Signout** 的資料列中，選取 [ **回答** ] 資料行中的 [ **加入後續追蹤] 提示**。
1. 在 [ **追蹤提示** ] 快顯視窗的欄位中，輸入下列值：

    |欄位|值|
    |--|--|
    |顯示文字|輸入 **關閉裝置**。 這是要在後續提示中顯示的自訂文字。|
    |僅限內容| 選取此核取方塊。 只有當問題指定內容時，才會傳迴響應。|
    |回應的連結|輸入 **使用登入畫面** 來尋找現有的問答組。|


1.  傳回一個相符的。 選取此答案做為後續追蹤，然後選取 [ **儲存**]。

    ![[後續追蹤提示 (預覽) ] 頁面](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 新增後續提示之後，請選取頂端導覽中的 [ **儲存並定型** ]。

### <a name="edit-the-display-text"></a>編輯顯示文字

當您建立後續的提示，並將現有問答組輸入為 **要回答的連結** 時，您可以輸入新的 **顯示文字**。 此文字不會取代現有的問題，也不會加入新的替代問題。 它與這些值不同。

1. 若要編輯顯示文字，請在 **內容** 欄位中搜尋並選取問題。
1. 在該問題的資料列中，選取 [解答] 資料行中的後續提示。
1. 選取您要編輯的顯示文字，然後選取 [ **編輯**]。

    ![顯示文字的編輯命令](../media/conversational-context/edit-existing-display-text.png)

1. 在 **後續的提示** 快顯視窗中，變更現有的顯示文字。
1. 當您完成編輯顯示文字時，請選取 [ **儲存**]。
1. 在頂端導覽列中， **儲存並定型**。


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>在後續的提示字元中加入新的問與答配對

當您將新的問答組新增至知識庫時，每一組都應連結至現有的問題做為後續的提示。

1. 在 [知識庫] 工具列上，搜尋並選取帳戶的現有問答組 **並登入**。

1. 在這個問題的 [ **回答** ] 欄中，選取 [ **新增後續追蹤] 提示**。
1. 在 [ **後續提示 (預覽])** 中，輸入下列值以建立新的後續提示：

    |欄位|值|
    |--|--|
    |顯示文字|*建立 Windows 帳戶*。 要在後續提示中顯示的自訂文字。|
    |僅限內容|選取此核取方塊。 只有當問題指定了內容時，才會傳回此回應。|
    |回應的連結|輸入下列文字作為答案：<br>*使用新的或現有的電子郵件帳戶 [建立](https://account.microsoft.com/)Windows 帳戶*。<br>當您儲存和定型資料庫時，將會轉換此文字。 |
    |||

    ![建立新的提示問題和解答](../media/conversational-context/create-child-prompt-from-parent.png)


1. 選取 [ **建立新** 的]，然後選取 [ **儲存**]。

    此動作會建立新的問答組，並將選取的問題連結為後續的提示。 這兩個問題的 **內容** 資料行表示後續的提示關聯性。

1. 選取 [ **視圖選項**]，然後選取 [ [**顯示內容 (預覽])**](#show-questions-and-answers-with-context)。

    新的問題會顯示其連結的方式。

    ![建立新的後續操作提示](../media/conversational-context/new-qna-follow-up-prompt.png)

    父問題會將新問題顯示為其中一個選項。

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="螢幕擷取畫面顯示這兩個問題的內容資料行，指出後續的提示關聯性。" lightbox="../media/conversational-context/child-prompt-created.png":::

1. 新增後續的提示之後，請選取頂端導覽列中的 [ **儲存並定型** ]。

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>在測試後續提示時查看多重回合

當您在 [ **測試** ] 窗格中測試問題與後續的提示時，回應會包含後續的提示。

![回應包括後續提示](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>傳回初始回應和後續提示的 JSON 要求

使用空的 `context` 物件來要求使用者問題的答案，並包含後續提示。

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>傳回初始回應和後續提示的 JSON 回應

上一節要求回答和任何後續的提示，以進行 **帳戶和登入**。 回應會包含提示資訊，此資訊位於 *答案 [0]. 內容*，以及要顯示給使用者的文字。

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts`陣列提供屬性中的文字 `displayText` 和 `qnaId` 值。 您可以在對話流程中將這些答案顯示為下一個顯示的選項，然後將選取的結果傳送 `qnaId` 至下列要求中的 QnA Maker。

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>傳回非初始回應和後續提示的 JSON 要求

填滿 `context` 物件以包含先前的內容。

在下列 JSON 要求中，目前的問題是 *使用 Windows Hello 登入* ，而先前的問題是 *帳戶和登入*。

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>傳回非初始回應和後續提示的 JSON 回應

QnA Maker 的 _GenerateAnswer_ JSON 回應包含 `context` 物件中第一個專案的屬性中的後續提示 `answers` ：

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>使用 QnA Maker 識別碼查詢知識庫

如果您要使用多回合功能來建立自訂應用程式。 在初始問題的回應中，會傳回任何後續的提示和其相關聯的 `qnaId` 。 現在您已有識別碼，您可以在後續提示的要求本文中傳遞此識別碼。 如果要求本文包含 `qnaId` 、和內容物件 (其中包含先前的 QnA Maker 屬性) ，則 GenerateAnswer 會依識別碼傳回確切的問題，而不是使用排名演算法來尋找問題文字的答案。


## <a name="display-order-is-supported-in-the-update-api"></a>更新 API 中支援顯示順序

[更新 API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)可支援在 JSON 回應中傳回的[顯示文字和顯示順序](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)。

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>使用更新 API 新增或刪除多回合提示

您可以使用 [QnA Maker 更新 API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)來新增或刪除多回合提示。  提示會在 `context` 屬性的 `promptsToAdd` 陣列和陣列中新增 `promptsToDelete` 。

## <a name="export-knowledge-base-for-version-control"></a>匯出版本控制的知識庫

QnA Maker 藉由在匯出的檔案中包含多回合對話步驟來支援版本控制。

## <a name="next-steps"></a>後續步驟

深入瞭解此 [對話方塊範例](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) 中的內容交談，或深入瞭解 [多回合交談的概念 bot 設計](/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)。

> [!div class="nextstepaction"]
> [移轉知識庫](../Tutorials/migrate-knowledge-base.md)