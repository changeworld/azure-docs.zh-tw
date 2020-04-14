---
title: 多轉對話 ─ QnA 製造商
description: 使用提示和上下文管理自動程式從一個問題到另一個問題的多個轉彎(稱為多轉)。 多轉折是進行反覆對話的能力,其中前一個問題的上下文會影響下一個問題和答案。
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261460"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>使用後續追蹤提示來建立多回合對話

使用後續提示和上下文管理自動程式從一個問題到另一個問題的多個_轉彎(稱為多轉_)。

要查看多轉的工作原理,請查看以下演示視頻:

[![QnA 製造商中的多轉對話](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>什麼是多轉式對話?

有些問題不能一轉就回答。 在設計用戶端應用程式(聊天機器人)對話時,使用者可能會提出需要篩選或優化以確定正確答案的問題。 通過向使用者提供*後續提示*,可以通過這些問題進行此流。

當用戶提問時,QnA Maker 會返回答案_和_任何後續提示。 此回應允許您將後續問題作為選擇提出。

> [!CAUTION]
> 不會從常見問題解答文檔中提取多轉提示。 如果需要多轉提取,請刪除將 QnA 對指定為常見問題的問號。

## <a name="example-multi-turn-conversation-with-chat-bot"></a>使用聊天機器人進行多轉對話的範例

通過多轉,聊天機器人管理與用戶的對話以確定最終答案,如下圖所示:

![多轉對話框,其中包含引導使用者完成對話的提示](../media/conversational-context/conversation-in-bot.png)

在前面的映射中,用戶通過輸入**我的帳戶**啟動了對話。 知識庫有三個連結的問答對。 要優化答案,用戶選擇知識庫中的三個選項之一。 問題(#1)有三個後續提示,在聊天機器人中作為三個選項(#2)顯示。

當使用者選擇選項(#3)時,將顯示下一個優化選項清單(#4)。 此序列將繼續(#5),直到用戶確定正確的最終答案(#6)。


### <a name="use-multi-turn-in-a-bot"></a>在自動程式中使用多轉

發佈知識庫後,您可以選擇 **「創建自動程式」** 按鈕,將 QnA Maker 機器人部署到 Azure 機器人服務。 提示將顯示在您為機器人啟用的聊天客戶端中。

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>從文件結構建立多圈對話

創建知識庫時,「**填充知識庫」** 部分將顯示**從 URL、.pdf 或 .docx 檔啟用多轉提取**複選框。

![開啟多轉提取的選取](../media/conversational-context/enable-multi-turn.png)

選擇此選項時,QnA Maker 會提取文檔結構中存在的層次結構。 層次結構將轉換為後續提示,層次結構的根用作父 QnA。 在某些文件中,層次結構的根目錄沒有可以用作答案的內容,您可以提供"預設答案文本",作為替代答案文本來提取此類層次結構。

只能從 URL、PDF 檔或 DOCX 檔中推斷多轉結構。 有關結構的示例,請查看[Microsoft Surface 用戶手冊 PDF 檔](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)的圖像。

![!【使用者手冊中的結構範例](../媒體/對話上下文/匯入檔案-與對話結構.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>編譯您自己的多轉文件

如果要建立多轉文件,請記住以下準則:

* 使用標題和子標題表示層次結構。 例如,您可以 h1 表示父 QnA 和 h2 以表示應作為提示符的 QnA。 使用小標題大小表示後續層次結構。 不要使用樣式、顏色或其他機制來暗示文檔中的結構,QnA Maker 不會提取多轉提示。

* 標題的第一個字元必須大寫。

* 不要用問號結束標題。 `?`

* 可以使用[範例文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)創建自己的多轉文件。

### <a name="adding-files-to-a-multi-turn-kb"></a>新增檔案到多轉 KB

添加分層文件時,QnA Maker 會確定結構中的後續提示以創建對話流。

1. 在 QnA Maker 中,選擇使用**啟用 URL、.pdf 或 .docx 檔進行多轉提取**創建的現有知識庫。 啟用。
1. 跳到 **「設定」** 頁,選擇要新增的檔案或網址。
1. **保存並培訓**知識庫。

> [!Caution]
> 不支援支援將匯出的 TSV 或 XLS 多轉知識庫檔用作新知識庫的資料來源。 您需要從 QnA Maker 門戶的 **「設定」** 頁**匯入**該檔類型,以便將匯出的多轉提示添加到知識庫。


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>使用建立 API 建立具有多轉提示的知識庫

您可以使用[QnA 製造商創建 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)創建具有多轉提示的知識案例。 提示正在`context`屬性`prompts`的陣列中添加。

## <a name="show-questions-and-answers-with-context"></a>使用內容顯示問題和答案

將顯示的問答對減少到僅具有上下文對話的對。

選擇 **'查看'選項**,然後選擇 **"顯示上下文**" 該清單顯示包含後續提示的問答對。

![依上下文對話篩選問答對](../media/conversational-context/filter-question-and-answers-by-context.png)

多轉上下文顯示在第一列中。

![![上下文(預覽)"列](../媒體/對話上下文/表面-手動-pdf-後續提示.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

在前面的圖像中 **,#1**表示列中的粗體文本,表示當前問題。 父問題是行中的頂部項。 它下面的任何問題都是連結的問答對。 這些項目是可選的,因此您可以立即轉到其他上下文項。

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>將現有問答對新增為後續提示

原始問題"**我的帳戶**"具有後續提示,如 **「帳戶」和「登錄」。**

!["帳戶和登錄"答案和後續提示](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

向當前未連結的現有問答對添加後續提示。 由於問題未連結到任何問答對,因此需要更改當前檢視設置。

1. 要將現有問答對連結為後續提示,請選擇問答對的行。 對於 Surface 手冊,搜索 **「註銷」** 以減少清單。
1. 在"**應答**「列中」**註銷「** 行中,選擇 **」添加後續提示**」。。
1. 在 **「後續」提示**彈出視窗中的欄位中,輸入以下值:

    |欄位|值|
    |--|--|
    |顯示文字|輸入**關閉裝置**。 這是要在後續提示中顯示的自定義文本。|
    |只只文| 選取此核取方塊。 僅當問題指定上下文時,才會返回答案。|
    |連結回答|輸入**使用登錄螢幕**查找現有的問答對。|


1.  返回一個匹配項。 選擇此答案作為後續答案,然後選擇 **"保存**"。

    !["後續提示 (預覽)"頁面](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. 新增後續提示後,在頂部導航中選擇 **"儲存"並訓練**。

### <a name="edit-the-display-text"></a>編輯顯示文字

建立後續提示,並將現有問答對輸入為 **「答案連結**」時,可以輸入新的 **「顯示」 文字**。 此文本不會替換現有問題,也不會添加新的備用問題。 它與這些值是分開的。

1. 要編輯顯示文本,請在 **「上下文」** 欄位中搜尋並選擇問題。
1. 在該行中,在答案列中選擇後續提示。
1. 選擇要編輯的顯示文本,然後選擇 **「編輯**」。

    ![顯示文字的「編輯」指令](../media/conversational-context/edit-existing-display-text.png)

1. 在 **「後續」提示**彈出視窗中,更改現有顯示文本。
1. 編輯完顯示文字後,選擇「**儲存**」 。
1. 在頂部導覽列中,**儲存和訓練**。


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>新增新問答對作為後續提示

將新的問答對添加到知識庫時,每對應連結到現有問題作為後續提示。

1. 在知識庫工具列上,搜尋並選擇「帳戶」的現有問答對**並登入**。

1. 在「**回答**」列中,選擇 **「添加後續提示**」。。
1. 在**後續提示 (PREVIEW)** 下,透過輸入以下值建立新的後續提示:

    |欄位|值|
    |--|--|
    |顯示文字|*建立 Windows 帳號*。 要在後續提示中顯示的自訂文本。|
    |只只文|選取此核取方塊。 僅當問題指定上下文時,才會返回此答案。|
    |連結回答|輸入以下文字作為答案:<br>*[使用](https://account.microsoft.com/)新的或現有的電子郵件帳戶建立 Windows 帳號*。<br>保存並訓練資料庫時,將轉換此文本。 |
    |||

    ![建立新的提示問題和答案](../media/conversational-context/create-child-prompt-from-parent.png)


1. 選擇 **"創建新**",然後選擇 **"儲存**"。

    此操作將創建新的問答對,並將所選問題作為後續提示連結。 對於這兩個問題,"**上下文**「列指示後續提示關係。

1. 選擇 **「檢視」選項**,然後選擇[**「顯示上下文」(預覽)。**](#show-questions-and-answers-with-context)

    新的問題顯示了它是如何連結的。

    ![建立新的後續提示](../media/conversational-context/new-qna-follow-up-prompt.png)

    父問題顯示一個新問題作為其選擇之一。

    ![![對於這兩個問題,上下文列指示後續提示關係](../媒體/對話上下文/兒童提示創建.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. 添加後續提示后,在頂部導航欄中選擇 **"保存"和"訓練**"。

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>在後續提示測試期間檢視多轉

在 **「測試」** 窗格中使用後續提示測試問題時,回應包括後續提示。

![回應包括後續提示](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON 請求返回初始答案和後續提示

使用空`context`物件請求用戶問題的答案,並包含後續提示。

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>JSON 回應,用於返回初始答案和後續提示

上一節要求答覆和任何後續提示到**帳號和登入**。 回應包括位於*答案{0}上下文*的提示資訊,以及要向用戶顯示的文本。

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

陣列`prompts``displayText`提供 屬性和`qnaId`值中 的文字。 您可以將這些答案顯示為對話流中的下一個顯示選項,然後在以下請求中將所選`qnaId`選項發送回 QnA Maker。

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON 請求返回非初始答案和後續提示

填充`context`物件以包括以前的上下文。

在下面的 JSON 請求中,當前的問題是*使用 Windows Hello 登入*,而上一個問題是*帳戶和登入*。

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>JSON 回應,用於返回非初始答案和後續提示

QnA Maker _GenerateAnswer_ JSON 回應包括`context``answers`物件中第一項屬性中的後續提示:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>使用 QnA 製造商 ID 查詢知識庫

如果要使用多轉功能構建自定義應用程式。 在初始問題的回答中,將傳回任何後續提示及其`qnaId`關聯 。 現在您擁有了 ID,您可以在後續提示的請求正文中傳遞該 ID。 如果請求正文包含和`qnaId`上下文物件(包含以前的 QnA Maker 屬性),則「生成應答」將按 ID 返回確切的問題,而不是使用排名演演演算法通過問題文本查找答案。


## <a name="display-order-is-supported-in-the-update-api"></a>更新 API 中支援顯示順序

在 JSON 回應中返回的[顯示文本和顯示順序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)受[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)支援進行編輯。

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>使用更新 API 新增或移除多轉提示

您可以使用[QnA 製造商更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)添加或刪除多轉提示。  提示正在`context`屬性`promptsToAdd`的陣列`promptsToDelete`和陣列中添加。

## <a name="export-knowledge-base-for-version-control"></a>版本控制的匯出知識庫

QnA Maker 通過在匯出的檔中包括多轉對話步驟支援版本控制。

## <a name="next-steps"></a>後續步驟

從此[對話方塊範例](https://aka.ms/qnamakermultiturnsample)瞭解有關上下文對話的更多內容,或瞭解有關[多轉對話的概念機器人設計](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)詳細資訊。

> [!div class="nextstepaction"]
> [移轉知識庫](../Tutorials/migrate-knowledge-base.md)
