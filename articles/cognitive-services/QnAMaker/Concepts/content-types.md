---
title: 內容類型 - QnA 製造商
description: 內容類型包括許多標準結構化文檔，如 PDF、DOC 和 TXT。
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650192"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可以添加到知識庫的文檔的內容類型
內容類型包括許多標準結構化文檔，如 PDF、DOC 和 TXT。

## <a name="file-and-url-data-types"></a>檔和 URL 資料類型

下表摘要說明 QnA Maker 所支援的內容類型和檔案格式。

|來源類型|內容類型| 範例|
|--|--|--|
|URL|常見問題集<br> (一般，含有區段或含有主題首頁)<br>支援頁面 <br> (單頁操作說明文章、疑難排解文章等等。)|[一般的常見問題集](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[具有連結的常見問題集](https://www.microsoft.com/en-us/software-download/faq)、<br> [具有主題首頁的常見問題集](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支援文件](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|常見問題集、<br> 產品手冊、<br> 小手冊、<br> 文件、<br> 傳單原則、<br> 支援指南、<br> 結構化 QnA<br> 等|**無多轉**<br>[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [示例白皮書.pdf，](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)<br><br>**多轉**：<br>[表面專業版（多cx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[康托索優勢 （多 cx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[康托索福利 （pdf）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|*Excel|結構化 QnA 檔案<br> (包括 RTF、HTML 支援)|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|*TXT/TSV|結構化 QnA 檔案|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果需要對資料來源進行身份驗證，請考慮以下方法將該內容放入 QnA Maker：

* 手動下載檔案並導入 QnA 製造商
* 從經過身份驗證[的共用點位置](../how-to/add-sharepoint-datasources.md)添加檔

## <a name="url-content"></a>URL 內容

可通過 QnA Maker 中的**URL**導入兩種類型的文檔：

* 常見問題集 URL
* 支援 URL

每種類型都表示預期的格式。

## <a name="file-based-content"></a>基於檔的內容

您可以在[QnA Maker 門戶](https://www.qnamaker.ai)中從公共源或本地檔案系統將檔添加到知識庫。

## <a name="content-format-guidelines"></a>內容格式指南

詳細瞭解不同檔的[格式指南](../reference-document-format-guidelines.md)。

## <a name="next-steps"></a>後續步驟

瞭解哪些資訊存儲在[問答 （QnA） 集中](question-answer-set.md)。