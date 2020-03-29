---
title: 編輯知識庫 - QnA Maker
description: QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131644"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>在知識庫中編輯 QnA 集

QnA Maker 可讓您提供方便使用的編輯方式，藉以管理知識庫的內容。

QnA 集從資料來源（如檔或 URL）添加，或添加為編輯源。 編輯源指示 QnA 集已手動添加到 QnA 門戶中。 所有 QnA 集都可用於編輯。

## <a name="add-an-editorial-qna-set"></a>添加編輯 QnA 集
1. 登錄到[QnA 門戶](https://www.qnamaker.ai/)，然後選擇知識庫以將 QnA 集添加到 。
1. 在知識庫的**EDIT**頁上，選擇 **"添加 QnA 集**"以添加新的 QnA 集。

1. 在新的 QnA 設置行中，添加所需的**問答**欄位 **。** 其他欄位是可選的。 可以隨時更改所有欄位。

1. 可以選擇添加**備用短語**。 替代措辭是與原始問題有顯著差異但應提供相同答案的任何類型的問題。

    當您的知識庫發佈，並且您打開了主動學習時，QnA Maker 會收集備用短語選項，供您接受。 選擇這些選項是為了提高預測精度。

1. 可以選擇添加**中繼資料**。 要查看中繼資料，請在內容功能表中選擇 **"查看"選項**。 中繼資料提供用戶端應用程式（如聊天機器人）提供答案的篩選器。

1. 或者，添加**後續提示**。 後續提示為用戶端應用程式提供了其他對話路徑，以便向使用者演示。

1. 選擇 **"保存"並訓練**以查看預測，包括新的 QnA 集。

## <a name="edit-a-qna-set"></a>編輯 QnA 集

可以編輯任何 QnA 集中的任何欄位，而不考慮原始資料來源。 由於當前 **"視圖選項**"設置（位於上下文工具列中），某些欄位可能不可見。

## <a name="delete-a-qna-set"></a>刪除 QnA 集

若要刪除 QnA，請按一下 QnA 資料列最右側的 [刪除]**** 圖示。 這是永久性的作業。 該作業無法復原。 請考慮在刪除集之前從 **"發佈"** 頁匯出知識庫。

![刪除 QnA 集](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>查找 QnA 設置 ID

如果需要查找 QnA 集 ID，可以在以下兩個位置找到它：

* 將滑鼠懸停在您感興趣的 QnA 設置行上的刪除圖示上。 懸停文本包括 QnA 設置 ID。
* 匯出知識庫。 知識庫中的每個 QnA 集都包括 QnA 集 ID。

## <a name="add-alternate-questions"></a>新增替代問題

將備用問題添加到現有 QnA 集，以提高與使用者查詢匹配的可能性。

![新增替代問題](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>連結 QnA 集

連結 QnA 集提供[後續提示](multiturn-conversation.md)。 這是在知識庫級別管理的 QnA 集之間的邏輯連接。 您可以在 QnA Maker 門戶中編輯後續提示。

無法在答案的中繼資料中連結 QnA 集。

## <a name="add-metadata"></a>新增中繼資料

通過首先選擇 **"查看"選項**，然後選擇 **"顯示中繼資料**"來添加元資料集。 這將顯示中繼資料列。 接下來，選擇添加**+** 元資料集的符號。 此集由一個鍵和一個值組成。

## <a name="save-changes-to-the-qna-sets"></a>保存對 QnA 集的更改

定期選擇 **"保存"並在**進行編輯後進行訓練，以避免丟失更改。

![新增中繼資料](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [在知識庫上共同作業](./collaborate-knowledge-base.md)

* [管理 QnA 製造商使用的 Azure 資源](set-up-qnamaker-service-azure.md)