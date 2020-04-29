---
title: 設計知識庫-QnA Maker
description: QnA Maker 知識庫是由一組問答集（QnA）集以及與每個 QnA 配對相關聯的選用中繼資料所組成。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 1d24434d1343f4174cfbfeb3a30c36737b213168
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756795"
---
# <a name="question-and-answer-set-concepts"></a>問題與解答集概念

知識庫是由問題和答案（QnA）集所組成。  每個集合都有一個答案，而一組包含與該_答案_相關聯的所有資訊。 答案可能會與資料庫資料列或資料結構實例鬆散類似。

## <a name="question-and-answer-sets"></a>問答集

問題與答案（QnA）集中的**必要**設定包括：

* 使用者查詢的**問題**文字，用來 QnA Maker 的機器學習服務，以不同的用語和相同的答案，與使用者的問題文字配合
* **答案**-集合的答案是當使用者查詢符合相關聯的問題時，所傳回的回應。

每個集合都是以**識別碼**表示。

集合的**選擇性**設定包括：

* **問題的替代形式**-這有助於 QnA Maker 傳回更多片語問題的正確答案。
* **中繼資料**：中繼資料是與 QnA 組相關聯的標記，並以索引鍵/值組表示。 中繼資料標記用來篩選 QnA 配對，並限制執行查詢比對的集合。
* **多回合提示**，用來繼續多回合交談

![QnA Maker 知識庫](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="editorially-add-to-knowledge-base"></a>透過編輯來新增至知識庫

如果您沒有預先存在的內容來填入知識庫，您可以在 QnA Maker 入口網站中新增 QnA 組編輯方面。 在[這裡](../How-To/edit-knowledge-base.md)了解如何更新知識庫。

## <a name="editing-your-knowledge-base-locally"></a>在本機編輯您的知識庫

在建立知識庫之後，建議您在 [QnA Maker 入口網站](https://qnamaker.ai)中對知識庫文字進行編輯，而不要透過本機檔案匯出和重新匯入。 不過，有時您可能需要在本機編輯知識庫。

請從 [設定]**** 頁面匯出知識庫，然後使用 Microsoft Excel 來編輯知識庫。 如果您選擇使用另一個應用程式來編輯已匯出的檔案，應用程式可能會產生語法錯誤，因為它不是完全符合 TSV 標準。 Microsoft Excel 的 TSV 檔案通常不會造成任何格式設定錯誤。

完成編輯之後，請從 [設定]**** 檔案重新匯入 TSV 檔案。 這會以匯入的知識庫完全取代目前的知識庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [QnA Maker 中的知識庫生命週期](./development-lifecycle-knowledge-base.md)