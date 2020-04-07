---
title: 編輯知識庫 - QnA Maker
description: QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756733"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>編輯 QnA 對

QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。

QnA 對從資料來源(如檔案或 URL)添加,或添加為編輯源。 編輯源指示 QnA 對已手動添加到 QnA 門戶中。 所有 QnA 對都可用於編輯。

## <a name="add-an-editorial-qna-pair"></a>新增編輯 QnA 對
1. 登入[QnA 門戶](https://www.qnamaker.ai/),然後選擇知識庫以將 QnA 新增到 。
1. 在知識庫的**EDIT**頁上,選擇 **「添加 QnA 對**」以添加新的 QnA 對。

1. 在新的 QnA 對行中,新增所需的**問答**欄位 **。** 其他欄位是可選的。 可以隨時更改所有欄位。

1. 可以選擇新增**備用短語**。 替代措辭是與原始問題有顯著差異但應提供相同答案的任何類型的問題。

    當您的知識庫發佈,並且您打開了[主動學習](use-active-learning.md)時,QnA Maker 會收集備用短語選項,供您接受。 選擇這些選項是為了提高預測精度。

1. 可以選擇新增**中繼資料**。 要檢視中繼資料,請在上下文選單中選擇 **「檢視」選項**。 元數據提供客戶端應用程式(如聊天機器人)提供答案的篩選器。

1. 或者,新增**後續提示**。 後續提示為用戶端應用程式提供了其他對話路徑,以便向使用者演示。

1. 選擇 **「保存」並訓練**以查看預測,包括新的 QnA 對。

## <a name="edit-a-qna-pair"></a>編輯 QnA 對

可以編輯任何 QnA 對中的任何欄位,而不考慮原始數據來源。 由於當前 **「檢視選項**」設置(位於上下文工具列中),某些欄位可能不可見。

## <a name="delete-a-qna-pair"></a>刪除 QnA 組

若要刪除 QnA，請按一下 QnA 資料列最右側的 [刪除]**** 圖示。 這是永久性的作業。 該作業無法復原。 請考慮在刪除集之前從 **「發布」** 頁匯出知識庫。

![刪除 QnA 組](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>尋找 QnA 對 ID

如果需要尋找 QnA 對 ID,可以在以下兩個位置找到它:

* 將滑鼠懸停在您感興趣的 QnA 對行上的刪除圖示上。 懸停文字包括 QnA 對 ID。
* 匯出知識庫。 知識庫中的每個 QnA 對都包括 QnA 對 ID。

## <a name="add-alternate-questions"></a>新增替代問題

將替代問題新增至現有 QnA 組來改善與使用者查詢相符的可能性。

![新增替代問題](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>連結 QnA 對

連結QnA對提供[後續提示](multiturn-conversation.md)。 這是在知識庫級別管理的 QnA 對之間的邏輯連接。 您可以在 QnA Maker 入口中編輯後續提示。

無法在答案的元數據中連結 QnA 對。

## <a name="add-metadata"></a>新增中繼資料

以首先選擇 **「查看」選項**,然後選擇 **「顯示元資料**」來新增元資料集。 這將顯示元數據列。 接下來,選擇添加**+** 元數據集的符號。 此集由一個鍵和一個值組成。

## <a name="save-changes-to-the-qna-pairs"></a>儲存對 QnA 儲存變更

定期選擇 **「保存」並在**進行編輯後進行訓練,以避免丟失更改。

![新增中繼資料](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在知識庫上共同作業](./collaborate-knowledge-base.md)

* [管理 QnA 製造商使用的 Azure 資源](set-up-qnamaker-service-azure.md)