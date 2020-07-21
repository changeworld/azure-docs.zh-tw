---
title: 內容類型-QnA Maker
description: 內容類型包含許多標準結構化檔，例如 PDF、DOC 和 TXT。
services: cognitive-services
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 70640246b15e7f68704657116724284498f62684
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524449"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可新增至知識庫的檔內容類型
內容類型包含許多標準結構化檔，例如 PDF、DOC 和 TXT。

## <a name="file-and-url-data-types"></a>檔案和 URL 資料類型

下表摘要說明 QnA Maker 所支援的內容類型和檔案格式。

|來源類型|內容類型| 範例|
|--|--|--|
|URL|常見問題集<br> (一般，含有區段或含有主題首頁)<br>支援頁面 <br> (單頁操作說明文章、疑難排解文章等等。)|[一般的常見問題集](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[具有連結的常見問題集](https://www.microsoft.com/en-us/software-download/faq)、<br> [具有主題首頁的常見問題集](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支援文件](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|常見問題集、<br> 產品手冊、<br> 小手冊、<br> 文件、<br> 傳單原則、<br> 支援指南、<br> 結構化 QnA<br> 等等。|**無多回合**<br>[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [範例白色 paper.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)，<br><br>**多回合**：<br>[Surface Pro （.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 權益（.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 權益（pdf）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|結構化 QnA 檔案<br> (包括 RTF、HTML 支援)|**無多回合**：<br>[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)<br><br>**多回合**：<br>[結構化的簡單 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx)<br>[Surface 膝上型電腦 FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-Surface-Pro.xlsx)|
|* TXT/TSV|結構化 QnA 檔案|[Sample chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果您需要驗證資料來源，請考慮下列方法，將該內容匯入 QnA Maker：

* 手動下載檔案並匯入 QnA Maker
* 從已驗證的[Sharepoint 位置](../how-to/add-sharepoint-datasources.md)新增檔案

## <a name="url-content"></a>URL 內容

您可以透過 QnA Maker 中的**URL**匯入兩種檔案類型：

* 常見問題集 URL
* 支援 URL

每種類型都表示預期的格式。

## <a name="file-based-content"></a>以檔案為基礎的內容

您可以在[QnA Maker 入口網站](https://www.qnamaker.ai)中，從公用來源或本機檔案系統將檔案新增至知識庫。

## <a name="content-format-guidelines"></a>內容格式指導方針

深入瞭解不同檔案的[格式方針](../reference-document-format-guidelines.md)。

## <a name="next-steps"></a>後續步驟

瞭解哪些資訊是以「[問答」（QnA）配對](question-answer-set.md)來儲存。