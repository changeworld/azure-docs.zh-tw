---
title: 如何測試知識庫 - QnA Maker
description: QnA Maker 知識庫測試，對於用以改善傳回回應正確度的互動流程而言，是相當重要的部分。 您可以透過增強的交談介面測試知識庫，也能以之進行編輯。
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927267"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>在 QnA Maker 中測試您的知識庫

QnA Maker 知識庫測試，對於用以改善傳回回應正確度的互動流程而言，是相當重要的部分。 您可以透過增強的交談介面測試知識庫，也能以之進行編輯。

## <a name="interactively-test-in-qna-maker-portal"></a>在 QnA Maker 入口網站中以互動方式測試

1. 選取 [我的知識庫] 頁面上的資料庫名稱來存取知識庫。
1. 若要存取測試滑出面板，請選取應用程式上方面板中的 [測試]。
1. 在文字方塊中輸入查詢，並選取 [輸入]。
1. 會將知識庫中最相符的答案做為回應傳回。

### <a name="clear-test-panel"></a>清除測試面板

若要從測試主控台清除所有輸入的測試查詢及其結果，請選取測試面板左上角的 [重新開始]。

### <a name="close-test-panel"></a>關閉測試面板

若要關閉測試面板，請再次選取 [測試] 按鈕。 測試面板開啟時，您無法編輯知識庫內容。

### <a name="inspect-score"></a>檢查分數

您可在 [檢查] 面板中檢查測試結果的詳細資料。

1.  測試滑出面板開啟時，對於該回應的詳細資料選取 [檢查]。

    ![檢查回應](../media/qnamaker-how-to-test-kb/inspect.png)

2.  [檢查] 面板隨即出現。 該面板包含評分最高的意圖，以及任何已識別的實體。 該面板會顯示所選語句的結果。

### <a name="correct-the-top-scoring-answer"></a>修正評分最高的答案

如果評分最高的答案不正確，請從清單中選取正確的答案，並選取 [Save and train]\(儲存並訓練\)。

![修正評分最高的答案](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>新增替代問題

您可以將問題的替代格式加入到指定的答案。 在文字方塊中鍵入替代答案，並按一下 Enter 新增這些替代答案。 選取 [Save and train]\(儲存並訓練\) 儲存更新。

![新增替代問題](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>加入新答案

如果任何符合的現有答案不正確，或答案不存在於知識庫 (在資料庫中找不到任何合適的相符項目)，您可以加入新的答案。

在 [解答] 清單的底部，使用文字方塊來輸入新的答案，然後按 enter 加以新增。

選取 [Save and train]\(儲存並訓練\) 保留此答案。 新的問答組現在已加入至您的知識庫。

> [!NOTE]
> 只有在您按下 [Save and train]\(儲存並訓練\) 按鈕時，才會儲存對於知識庫進行的所有編輯。

### <a name="test-the-published-knowledge-base"></a>測試已發佈的知識庫

您可以在 [測試] 窗格中測試已發佈的知識庫版本。 發佈知識庫之後，請選取 [**已發佈的知識庫**] 方塊，然後傳送查詢以從已發佈的 kb 取得結果。

![針對已發佈的 KB 進行測試](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>使用工具進行批次測試

當您想要執行下列動作時，請使用批次測試控管：

* 判斷一組問題的最高答案和分數
* 驗證問題集合的預期解答

如需逐步指示，請參閱批次測試[教學](../Quickstarts/batch-testing.md)課程。

批次測試是以批次測試控管提供。 此工具可做為下載的[壓縮可執行檔](https://aka.ms/qnamakerbatchtestingtool)，或作為[ C#原始碼](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting)。

[此工具的參考檔](../reference-tsv-format-batch-testing.md)包括：

* 工具的命令列範例
* TSV 輸入和 outfile 檔案的格式

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [發佈知識庫](./publish-knowledge-base.md)
