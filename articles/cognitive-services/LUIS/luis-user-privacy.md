---
title: 匯出 & 刪除資料-LUIS
titleSuffix: Azure Cognitive Services
description: 您可以完全掌控其資料的查看、匯出和刪除。 刪除客戶資料以確保隱私權和合規性。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "78273353"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>在認知服務的 Language Understanding (LUIS) 中匯出和刪除客戶資料

刪除客戶資料以確保隱私權和合規性。

## <a name="summary-of-customer-data-request-features"></a>客戶資料要求功能的摘要
Language Understanding Intelligent Service (LUIS) 可保存客戶內容以執行此服務，但 LUIS 使用者具有檢視、匯出和刪除其資料的完整控制權。 這可以透過 LUIS web[入口](luis-reference-regions.md)網站或[LUIS Authoring （也稱為程式設計） api](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)來完成。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

客戶的內容會加密並儲存在 Microsoft 區域 Azure 儲存體中，且包含：

- 註冊時收集的使用者帳戶內容
- 建立模型所需的定型資料
- [主動式學習](luis-concept-review-endpoint-utterances.md)用來協助改善模型的已記錄使用者查詢
  - 使用者可藉由將 `&log=false`附加至要求以關閉查詢記錄，詳情請見[這裡](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>刪除客戶資料
LUIS 使用者可以透過 LUIS web 入口網站或 LUIS Authoring （也稱為程式設計） Api，完全控制刪除任何使用者內容。 下表顯示這兩者的輔助連結：

| | **使用者帳戶** | **應用程式** | **範例語句** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **入口網站** | [連結](luis-concept-data-storage.md#delete-an-account) | [連結](luis-how-to-start-new-app.md#delete-app) | [連結](luis-concept-data-storage.md#utterances-in-an-intent) | [主動式學習語句](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[記錄的語句](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>匯出客戶資料
LUIS 使用者有完整的控制權可在入口網站上查看資料，但必須透過 LUIS 撰寫（也稱為程式設計） Api 匯出。 下表顯示透過 LUIS Authoring （也稱為程式設計） Api 來協助資料匯出的連結：

| | **使用者帳戶** | **應用程式** | **語句（秒）** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>主動式學習的位置

若要啟用[主動式學習](luis-how-to-review-endpoint-utterances.md#enable-active-learning)，已在發佈的 LUIS 端點接收的使用者記錄語句會儲存在下列 Azure 地理位置中：

* [歐洲](#europe)
* [澳大利亞](#australia)
* [美國](#united-states)

除了使用中的學習資料（如下所述）之外，LUIS 會遵循[區域服務的資料儲存做法](https://azuredatacentermap.azurewebsites.net/)。

### <a name="europe"></a>歐洲

[Eu.luis.ai](https://eu.luis.ai)入口網站和歐洲製作（也稱為程式設計 api）會裝載于 Azure 的歐洲地理位置。 Eu.luis.ai 入口網站和歐洲製作（也稱為程式設計 Api）支援將端點部署到下列 Azure 地理位置：

* 歐洲
* 法國
* United Kingdom

部署到這些 Azure 地理位置時，來自應用程式使用者的端點所收到的語句將會儲存在 Azure 的歐洲地區，以供主動式學習之用。 您可以停用主動式學習，請參閱[停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理儲存的語句，請參閱[Delete 語句](luis-how-to-review-endpoint-utterances.md#delete-utterance)。

### <a name="australia"></a>澳大利亞

[Au.luis.ai](https://au.luis.ai)入口網站和澳大利亞撰寫（也稱為程式設計 api）會裝載于 Azure 的澳大利亞地理位置。 Au.luis.ai portal 和澳大利亞 Authoring （也稱為程式設計 Api）支援將端點部署到下列 Azure 地理位置：

* 澳大利亞

部署到這些 Azure 地理位置時，來自應用程式使用者的端點所收到的語句將會儲存在 Azure 的澳大利亞地區，以供主動式學習之用。 您可以停用主動式學習，請參閱[停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理儲存的語句，請參閱[Delete 語句](luis-how-to-review-endpoint-utterances.md#delete-utterance)。

### <a name="united-states"></a>美國

[Luis.ai](https://www.luis.ai)入口網站和美國撰寫（也稱為程式設計 api）會裝載于 Azure 的美國地理位置。 Luis.ai 入口網站和美國撰寫（也稱為程式設計 Api）支援將端點部署到下列 Azure 地理位置：

* 歐洲或澳大利亞撰寫區域不支援的 Azure 地理位置

部署到這些 Azure 地理位置時，來自應用程式使用者的端點所收到的語句將會儲存在 Azure 的美國地區，以供主動式學習之用。 您可以停用主動式學習，請參閱[停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理儲存的語句，請參閱[Delete 語句](luis-how-to-review-endpoint-utterances.md#delete-utterance)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [LUIS 區域參考](./luis-reference-regions.md)
