---
title: 在 QnA Maker 知識庫中新增閒聊
titleSuffix: Azure Cognitive Services
description: 當您建立 KB 時，將個人閒聊新增至您的 Bot，讓它變得更健談且吸引人。 QnA Maker 可讓您輕鬆地將預先填入的一組最常見閒聊，新增至您的 KB。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 3d5c47d3e3e9485c3a6de8f93782c654fb34a516
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330633"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>在知識庫中新增閒聊

將閒聊新增至您的 Bot，讓它變得更健談且吸引人。 QnA Maker 中的閒聊功能可讓您輕鬆地將預先填入的一組最常見閒聊，新增至您的資料庫 (KB)。 這可以是您 Bot 的特質起始點，並將節省您從頭撰寫它們的時間和成本。

這個資料集有大約100個閒聊的案例，像是專業、易記和機智等多個角色的語音。 選擇最類似您 Bot 聲音的角色。 假設有使用者查詢，QnA Maker 會嘗試將它與最接近的已知閒聊 QnA 比對。

以下是不同特質的一些範例。 您可以看到所有的個人化 [資料集](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets) ，以及特質的詳細資料。

針對的使用者查詢 `When is your birthday?` ，每個特性都有一個具樣式的回應：

<!-- added quotes so acrolinx doesn't score these sentences -->
|特質|範例|
|--|--|
|Professional|年齡不是真的適用于我。|
|易記|我真的沒有年齡。|
|機智|我的年齡沒有。|
|關心|我沒有年齡。|
|熱情|我是 bot，所以沒有年齡。|
||


## <a name="language-support"></a>語言支援

閒聊-支援下列語言的聊天資料集：

|Language|
|--|
|中文|
|英文|
|法文|
|德國|
|義大利文|
|日文|
|韓文|
|葡萄牙文|
|西班牙文|


## <a name="add-chit-chat-during-kb-creation"></a>在知識庫建立期間新增閒聊
在知識庫建立期間，新增來源 URL 和檔案之後，有可供新增閒聊的選項。 選擇您想要作為聊天基底的特質。 如果您不想新增閒聊，或如果您的資料來源中已經有閒聊支援，請選擇 [無]****。

## <a name="add-chit-chat-to-an-existing-kb"></a>在現有知識庫中新增閒聊
選取您的知識庫，並瀏覽至 [設定]**** 頁面。 有一個連結可連到適當 **.tsv** 格式的所有閒聊資料集。 下載您想要的特質，然後將它上傳為檔案來源。 當您下載並上傳檔案時，務必不要編輯格式或中繼資料。

![在現有知識庫中新增閒聊](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>編輯您的閒聊問題和回答
當您編輯知識庫時，您會看到閒聊的新來源，其是以您選取的特質為基礎。 您現在可以新增變更的問題或編輯回應，就像處理任何其他來源一樣。

![編輯閒聊 QnA](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

若要查看中繼資料，請選取工具列中的 [ **視圖選項** ]，然後選取 [ **顯示中繼資料**]。

## <a name="add-additional-chit-chat-questions-and-answers"></a>新增其他閒聊問題和回答
您可以新增不在預先定義的資料集中的新閒聊聊天 QnA 配對。 確保您不會複製閒聊集已經涵蓋的 QnA 配對。 當您新增任何新的閒聊 QnA 時，它會新增到您的**編輯**來源。 為確保 ranker 瞭解這是閒聊的聊天，請新增中繼資料索引鍵/值組 "聊天"，如下圖所示：

:::image type="content" source="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png" alt-text="新增閒聊 QnA" lightbox="../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png":::

## <a name="delete-chit-chat-from-an-existing-kb"></a>從現有知識庫中刪除閒聊
選取您的知識庫，並瀏覽至 [設定]**** 頁面。 您的特定閒聊來源會以檔案列示，內含選取的特質名稱。 您可以將此當作原始程式檔刪除。

![從知識庫中刪除閒聊](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [匯入知識庫](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>另請參閱

[QnA Maker 概觀](../Overview/overview.md)
