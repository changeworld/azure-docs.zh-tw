---
title: 最佳做法 - QnA Maker
description: 使用下列最佳做法可改善您的知識庫，並為您的應用程式/聊天機器人使用者提供更好的結果。
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 9a6f7f7d6edc4544942476050a1ed3c2011af7fb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80053123"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>QnA Maker 知識庫的最佳做法

[知識庫開發生命週期](../Concepts/development-lifecycle-knowledge-base.md)可引導您了解如何從頭到尾管理知識庫。 使用這些最佳作法來改善您的知識庫，並為您的用戶端應用程式或聊天機器人的使用者提供更好的結果。

## <a name="extraction"></a>擷取

QnA Maker 服務會持續改進從內容中擷取 QnA 的演算法，並擴充支援的檔案和 HTML 格式清單。 請遵循[指導方針](../Concepts/content-types.md)，以根據您的文件類型擷取資料。

一般情況下，常見問題集頁面應獨立存在，而不會與其他資訊結合。 產品手冊應有清楚的標題，且最好有索引頁面。

### <a name="configuring-multi-turn"></a>設定多回合

啟用多回合解壓縮以[建立您的知識庫](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure)。 如果您的知識庫會執行或應該支援問題階層，則可以從檔解壓縮此階層，或在檔解壓縮之後建立。

## <a name="creating-good-questions-and-answers"></a>建立良好的問題與解答

### <a name="good-questions"></a>良好的問題

最好的問題非常簡單。 考慮每個問題的關鍵字或片語，然後建立針對該關鍵字或片語的簡單問題。

您可視需要新增多個替代問題，但要讓替代問題維持一樣的簡單性。 新增更多的單字或片語並非問題的主要目的，也無助於 QnA Maker 尋找答案。


### <a name="add-relevant-alternative-questions"></a>新增相關的替代問題

您的使用者可能會以交談風格的文字`How do I add a toner cartridge to my printer?`或關鍵字搜尋（例如） `toner cartridge`輸入問題。 知識庫應該有兩種問題樣式，才能正確地傳回最佳答案。 如果您不確定客戶輸入的關鍵字，請使用 Application Insights 資料來分析查詢。

### <a name="good-answers"></a>良好的解答

最佳的答案是簡單的答案，但不太簡單。 請勿使用像是`yes`和`no`的答案。 如果您的答案應連結至其他來源，或透過媒體和連結提供豐富的體驗，請使用[元資料標記](../how-to/edit-knowledge-base.md#add-metadata)來區別答案，然後以`strictFilters`屬性中的元資料標記[提交查詢](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)，以取得正確的答案版本。

|Answer|Follup 提示|
|--|--|
|使用鍵盤上的 [電源] 按鈕，關閉 Surface 膝上型電腦的電源。|* 按鍵-結合睡眠、關機和重新開機。<br>* 如何為 Surface 膝上型電腦進行硬開機<br>* 如何變更 Surface 膝上型電腦的 BIOS<br>* 睡眠、關機和重新開機之間的差異|
|客戶服務可透過電話、Skype 和文字訊息每天24小時取得。|* 銷售的連絡人資訊。<br> * Office 和商店地點和時數，供親自造訪。<br> * Surface 膝上型電腦的配件。|

## <a name="chit-chat"></a>閒聊
將閒聊新增至您的 Bot，輕而易舉地讓 Bot 變得更健談且吸引人。 您可以在建立知識庫時，輕鬆地從預先定義的特質加入閒聊的資料集，並隨時加以變更。 了解如何[將閒聊新增至您的 KB](../How-To/chit-chat-knowledge-base.md)。

閒聊-支援[多種語言](../how-to/chit-chat-knowledge-base.md#language-support)的交談。

### <a name="choosing-a-personality"></a>選擇特質
閒聊-針對數個預先定義的特質支援聊天：

|風格 |QnA Maker 資料集檔案 |
|---------|-----|
|Professional |[qna_chitchat_professional tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|易記 |[qna_chitchat_friendly tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|詼諧 |[qna_chitchat_witty tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|管也 |[qna_chitchat_caring tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|熱心 |[qna_chitchat_enthusiastic tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

回應的範圍從正式到非正式和不敬。 您應該選取最符合您希望 Bot 所用語調的特質。 您可以查看[資料集](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)，並選擇做為 bot 基底的一個，然後自訂回應。

### <a name="edit-bot-specific-questions"></a>編輯 Bot 特有問題
有一些屬於閒聊資料集的 Bot 特有問題，而且已填入泛型回答。 變更下列回答，充分反映您的 Bot 詳細資料。

建議讓下列閒聊 QnA 更加明確：

* 您是誰？
* 您可以做什麼？
* 你幾歲？
* 誰建立了你？
* 您好

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>使用元資料標記新增自訂閒聊-交談

如果您新增自己的閒聊交談 QnA 配對，請務必新增中繼資料，以便傳回這些答案。 中繼資料名稱/值組為`editorial:chitchat`。

## <a name="searching-for-answers"></a>搜尋解答

GenerateAnswer API 會使用這兩個問題和答案來搜尋使用者查詢的最佳解答。

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>僅在答案不相關時搜尋問題

[`RankerType=QuestionOnly`](#choosing-ranker-type)如果您不想要搜尋答案，請使用。

其中一個範例是，當知識庫是以完整形式做為解答的問題目錄時，即為縮寫。 答案的值將不會協助搜尋適當的答案。

## <a name="rankingscoring"></a>排名/評分
請務必充分利用 QnA Maker 所支援的排名功能。 這樣將可提高指定的使用者查詢獲得適當回應的可能性。

### <a name="choosing-a-threshold"></a>選擇閾值

用來作為臨界值的預設[信賴分數](confidence-score.md)為0，不過您可以根據自己的需求[變更 KB 的閾值](confidence-score.md#set-threshold)。 每個知識庫各有不同，因此您應該測試並選擇最適合您知識庫的閾值。

### <a name="choosing-ranker-type"></a>選擇 Ranker 類型
根據預設，QnA Maker 會搜尋問題和答案。 如果您只想要搜尋問題，若要產生解答，請`RankerType=QuestionOnly`在 GenerateAnswer 要求的 POST 主體中使用。

### <a name="add-alternate-questions"></a>新增替代問題
[替代問題](../How-To/edit-knowledge-base.md)可提高使用者查詢產生相符項目的可能性。 在同樣的問題可用多種方式提問時，替代問題就有其效用。 這可以包括文句結構和字組樣式的變更。

|原始查詢|替代查詢|變更|
|--|--|--|
|Is parking available?|Do you have car park?|文句結構|
 |Hi|Yo<br>Hey there!|字組樣式或俚語|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>使用中繼資料標記來篩選問題和答案

[中繼資料](../How-To/edit-knowledge-base.md)可讓用戶端應用程式知道它不會採取所有答案，而是根據元資料標記來縮小使用者查詢結果的範圍。 即使查詢相同，知識庫解答也可能根據中繼資料標記而不同。 例如，如果餐廳分店的地點不同 (亦即，中繼資料為「地點：西雅圖」** 和「地點：雷德蒙」** 的不同)，「停車場在哪裡」** 就可能有不同的回答。

### <a name="use-synonyms"></a>使用同義字
雖然英文語言有一些同義字的支援，但透過更改[API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace)使用不區分大小寫的文字變異，將同義字新增至採用不同形式的關鍵字。 同義字會加入至 QnA Maker 服務層級，並由服務中的所有知識庫共用。

|原始字組|同義字|
|--|--|
|buy|purchase<br>網路-銀行<br>net banking|

### <a name="use-distinct-words-to-differentiate-questions"></a>使用相異字詞區隔問題
QnA Maker 排名演算法會比對使用者查詢與知識庫中的所含問題，在每個問題分別處理不同的需求時，可獲得最佳效果。 若在不同問題間有相同的字組重複出現，就比較難以用這些字組為指定的使用者查詢選擇正確的解答。

例如，您對於下列問題可能會有兩個不同的 QnA：

|QnA|
|--|
|where is the parking *location*|
|其中是 ATM*位置*|

因為這兩個 QnA 是以非常類似的字組表達，此相似性可能會對以 *"where is the `<x>` location"* 這類方式表達的許多使用者查詢，造成非常類似的分數。 相反地，請儘量避免*像是「* 位置」*之類的查詢，藉*此清楚區分您的知識庫中可能有許多問題的文字。

## <a name="collaborate"></a>共同作業
QnA Maker 可讓使用者對知識庫進行[共同作業](../How-to/collaborate-knowledge-base.md)。 使用者需要有 Azure QnA Maker 資源群組的存取權，才能存取知識庫。 有些組織可能想要將知識庫的編輯和維護委外處理，但同時仍能夠保護其 Azure 資源的存取權。 若要完成此「編輯者-核准者」模型，可以在不同的訂用帳戶中設定兩個相同的 [QnA Maker 服務](../How-to/set-up-qnamaker-service-azure.md)，並選取其中一個用於編輯測試週期。 測試完成之後，就會使用[匯入-匯出](../Tutorials/migrate-knowledge-base.md)程序將知識庫內容轉移至核准者的 QnA Maker 服務，由他來執行最終的知識庫發佈和端點更新。



## <a name="active-learning"></a>主動學習

[主動式學習](../How-to/use-active-learning.md)具有各種品質和數量的使用者查詢時，其在建議替代問題方面的表現最好。 務必讓用戶端應用程式的使用者查詢參與主動式回饋迴圈，而不需要審查。 在 QnA Maker 入口網站中建議問題之後，您可以**[依建議進行篩選](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)**，然後複習並接受或拒絕這些建議。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [編輯知識庫](../How-to/edit-knowledge-base.md)
