---
title: 匯出 & 刪除資料-LUIS
titleSuffix: Azure Cognitive Services
description: 您可以完全掌控其資料的觀看、匯出及刪除。 刪除客戶資料，以確保隱私權和合規性。
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/10/2020
ms.openlocfilehash: 0a2d0ce683261ca3460c7aeaa0d7a42152b81a1e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680159"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>在認知服務的 Language Understanding (LUIS) 中匯出和刪除客戶資料

刪除客戶資料，以確保隱私權和合規性。

## <a name="summary-of-customer-data-request-features"></a>客戶資料要求功能的摘要
Language Understanding Intelligent Service (LUIS) 可保存客戶內容以執行此服務，但 LUIS 使用者具有檢視、匯出和刪除其資料的完整控制權。 這可以透過 LUIS web [入口](luis-reference-regions.md) 網站或 [LUIS 撰寫 (（也稱為程式設計) api）](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)來完成。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

客戶的內容會加密並儲存在 Microsoft 區域 Azure 儲存體中，且包含：

- 註冊時收集的使用者帳戶內容
- 建立模型所需的定型資料
- [主動學習](luis-concept-review-endpoint-utterances.md)用來協助改善模型的已記錄使用者查詢
  - 使用者可藉由將 `&log=false`附加至要求以關閉查詢記錄，詳情請見[這裡](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>刪除客戶資料
LUIS 使用者有完整的控制權可刪除任何使用者內容，不論是透過 LUIS 入口網站或 LUIS 撰寫 (也稱為程式設計) Api。 下表顯示這兩者的輔助連結：

| | **使用者帳戶** | **應用程式** | **範例語句 (s)** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **入口網站** | [連結](luis-concept-data-storage.md#delete-an-account) | [連結](luis-how-to-start-new-app.md#delete-app) | [連結](luis-concept-data-storage.md#utterances-in-an-intent) | [主動式學習語句](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[已記錄語句](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>匯出客戶資料
LUIS 使用者具有完整控制權，可在入口網站上查看資料，但必須透過 LUIS 撰寫 (（也稱為程式設計) Api）來匯出。 下表顯示透過 LUIS 撰寫來協助資料匯出的連結 (也稱為程式設計) Api：

| | **使用者帳戶** | **應用程式** | **語句 (s)** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **API** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>主動式學習的位置

若要啟用 [主動式學習](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning)，在發佈的 LUIS 端點上收到的使用者記錄語句會儲存在下列 Azure 地理位置：

* [歐洲](#europe)
* [澳大利亞](#australia)
* [美國](#united-states)

除了使用中的學習資料之外 (詳細說明) ，LUIS 會遵循 [區域服務的資料儲存作法](https://azuredatacentermap.azurewebsites.net/)。

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>歐洲

歐洲撰寫 (也稱為程式設計 Api) 資源裝載于 Azure 的歐洲地區，並支援將端點部署至下列 Azure 地理位置：

* 歐洲
* 法國
* 英國

部署至這些 Azure 地理位置時，端點從您應用程式的終端使用者收到的語句將會儲存在 Azure 的歐洲地理位置，以供主動學習之用。

### <a name="australia"></a>澳洲

澳大利亞撰寫 (也稱為程式設計 Api) 資源裝載于 Azure 的澳大利亞地理位置，並支援將端點部署到下列 Azure 地理位置：

* 澳洲

部署至這些 Azure 地理位置時，端點從您應用程式的終端使用者收到的語句將會儲存在 Azure 的澳大利亞地理位置，以供主動學習之用。

### <a name="united-states"></a>美國

美國撰寫 (也稱為程式設計 Api) 資源裝載在 Azure 的美國地區，並支援將端點部署到下列 Azure 地理位置：

* 歐洲或澳大利亞 authoring 區域不支援的 Azure 地理位置

部署至這些 Azure 地理位置時，端點從您應用程式的終端使用者收到的語句將會儲存在 Azure 的美國地區，以供主動學習之用。 

## <a name="disable-active-learning"></a>停用主動式學習

若要停用主動式學習，請參閱 [停用主動式學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 若要管理儲存的語句，請參閱 [刪除語句](luis-how-to-review-endpoint-utterances.md#delete-utterance)。


## <a name="next-steps"></a>下一步

> [!div class="nextstepaction"]
> [LUIS 區域參考](./luis-reference-regions.md)
