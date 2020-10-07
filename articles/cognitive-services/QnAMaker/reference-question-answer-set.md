---
title: 設計知識庫-QnA Maker
description: QnA Maker 的知識庫是由一組問答 (QnA) 配對以及與每個 QnA 配對相關聯的選擇性中繼資料所組成。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/01/2020
ms.openlocfilehash: 363af72df89be9104e4d946bf8da0d44908382d4
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776216"
---
# <a name="question-and-answer-pair-concepts"></a>問題和答案配對概念

知識庫是由問題和答案 (QnA) 配對所組成。  每一組都有一個答案，而一組包含與該 _答案_相關聯的所有資訊。 答案可能會與資料庫資料列或資料結構實例鬆散類似。

## <a name="question-and-answer-pairs"></a>問與答組

問答 (QnA) 組的 **必要** 設定如下：

* 使用者查詢的 **問題** 文字，用來 QnA Maker 的機器學習服務，用來配合使用者問題的文字與不同的用語，但答案相同
* **答案**-配對的答案是使用者查詢與相關問題相符時所傳回的回應。

每個配對都會以 **識別碼**表示。

配對的 **選擇性** 設定包括：

* **問題的替代形式** -這有助於 QnA Maker 傳回更多不同問題的正確答案片語
* **中繼資料**：中繼資料是與 QnA 配對相關聯的標籤，並以索引鍵/值組表示。 中繼資料標記用來篩選 QnA 配對，並限制執行查詢比對的集合。
* **多回合提示**，用來繼續進行多重回合對話

![QnA Maker 知識庫](media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>透過編輯來新增至知識庫

如果您沒有預先存在的內容來填入知識庫，您可以在 QnA Maker 入口網站中新增 [QnA 組編輯方面]。 在[這裡](How-To/edit-knowledge-base.md)了解如何更新知識庫。

## <a name="editing-your-knowledge-base-locally"></a>在本機編輯您的知識庫

在建立知識庫之後，建議您在 [QnA Maker 入口網站](https://qnamaker.ai)中對知識庫文字進行編輯，而不要透過本機檔案匯出和重新匯入。 不過，有時您可能需要在本機編輯知識庫。

請從 [設定]**** 頁面匯出知識庫，然後使用 Microsoft Excel 來編輯知識庫。 如果您選擇使用其他應用程式來編輯匯出的檔案，則應用程式可能會產生語法錯誤，因為它不是完全符合規範。 Microsoft Excel 的 TSV 檔案通常不會造成任何格式設定錯誤。

完成編輯之後，請從 [設定]**** 檔案重新匯入 TSV 檔案。 這會以匯入的知識庫完全取代目前的知識庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker 中的知識庫生命週期](Concepts/development-lifecycle-knowledge-base.md)
