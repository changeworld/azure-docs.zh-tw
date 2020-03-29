---
title: 匯出&刪除資料 - LUIS
titleSuffix: Azure Cognitive Services
description: 您可以完全控制查看、匯出和刪除其資料。 刪除客戶資料以確保隱私和合規性。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273353"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>在認知服務的 Language Understanding (LUIS) 中匯出和刪除客戶資料

刪除客戶資料以確保隱私和合規性。

## <a name="summary-of-customer-data-request-features"></a>客戶資料要求功能的摘要
Language Understanding Intelligent Service (LUIS) 可保存客戶內容以執行此服務，但 LUIS 使用者具有檢視、匯出和刪除其資料的完整控制權。 這可以通過 LUIS Web[門戶](luis-reference-regions.md)或[LUIS 創作（也稱為程式設計）API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)來完成。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

客戶的內容會加密並儲存在 Microsoft 區域 Azure 儲存體中，且包含：

- 註冊時收集的使用者帳戶內容
- 構建模型所需的培訓資料
- [活動學習](luis-concept-review-endpoint-utterances.md)用於説明改進模型的已記錄使用者查詢
  - 使用者可藉由將 `&log=false`附加至要求以關閉查詢記錄，詳情請見[這裡](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>刪除客戶資料
LUIS 使用者可以完全控制通過 LUIS Web 門戶或 LUIS 創作（也稱為程式設計）API 刪除任何使用者內容。 下表顯示這兩者的輔助連結：

| | **使用者帳戶** | **應用程式** | **示例** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **入口網站** | [連結](luis-concept-data-storage.md#delete-an-account) | [連結](luis-how-to-start-new-app.md#delete-app) | [連結](luis-concept-data-storage.md#utterances-in-an-intent) | [主動學習話語](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[已記錄的已陳述](luis-concept-data-storage.md#disable-logging-utterances) |
| **Api** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>匯出客戶資料
LUIS 使用者具有在門戶上查看資料的完全控制，但必須通過 LUIS 創作（也稱為程式設計）API 匯出資料。 下表顯示了通過 LUIS 創作（也稱為程式設計）API協助資料匯出的連結：

| | **使用者帳戶** | **應用程式** | **Utterance(s)** | **使用者查詢** |
| --- | --- | --- | --- | --- |
| **Api** | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [連結](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>主動學習的位置

為了啟用[主動學習](luis-how-to-review-endpoint-utterances.md#enable-active-learning)，在已發佈的 LUIS 終結點接收的使用者記錄的話語存儲在以下 Azure 地理位置中：

* [歐洲](#europe)
* [澳大利亞](#australia)
* [美國](#united-states)

除了活動學習資料（詳見下文），LUIS 遵循[區域服務的資料存儲實踐](https://azuredatacentermap.azurewebsites.net/)。

### <a name="europe"></a>歐洲

[eu.luis.ai](https://eu.luis.ai)門戶和歐洲創作（也稱為程式設計 API）託管在 Azure 的歐洲地理中。 eu.luis.ai門戶和歐洲創作（也稱為程式設計 API）支援將終結點部署到以下 Azure 地理位置：

* 歐洲
* 法國
* United Kingdom

部署到這些 Azure 地理位置時，從應用最終使用者處接收的終結點接收的話語將存儲在 Azure 的歐洲地理中以進行主動學習。 您可以禁用活動學習，請參閱[禁用活動學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 要管理存儲的話語，請參閱[刪除話語](luis-how-to-review-endpoint-utterances.md#delete-utterance)。

### <a name="australia"></a>澳大利亞

[au.luis.ai](https://au.luis.ai)門戶和澳大利亞創作（也稱為程式設計 API）託管在 Azure 的澳大利亞地理中。 au.luis.ai門戶和澳大利亞創作（也稱為程式設計 API）支援將終結點部署到以下 Azure 地理位置：

* 澳大利亞

部署到這些 Azure 地理位置時，從應用最終使用者處接收的終結點接收的話語將存儲在 Azure 的澳大利亞地理中以進行主動學習。 您可以禁用活動學習，請參閱[禁用活動學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 要管理存儲的話語，請參閱[刪除話語](luis-how-to-review-endpoint-utterances.md#delete-utterance)。

### <a name="united-states"></a>美國

[luis.ai](https://www.luis.ai)門戶和美國創作（也稱為程式設計 API）託管在 Azure 的美國地理位置中。 luis.ai門戶和美國創作（也稱為程式設計 API）支援將終結點部署到以下 Azure 地理位置：

* 歐洲或澳大利亞創作區域不支援的 Azure 地理位置

部署到這些 Azure 地理位置時，從應用最終使用者處接收的終結點接收的話語將存儲在 Azure 的美國地理位置中，以便進行主動學習。 您可以禁用活動學習，請參閱[禁用活動學習](luis-how-to-review-endpoint-utterances.md#disable-active-learning)。 要管理存儲的話語，請參閱[刪除話語](luis-how-to-review-endpoint-utterances.md#delete-utterance)。


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [LUIS 區域參考](./luis-reference-regions.md)
