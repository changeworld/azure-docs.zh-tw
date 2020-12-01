---
title: 服務設定-QnA Maker
description: 瞭解設定資源的方式和位置。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: e7dfc6db50432b19d515671fabe26edf9cfb45ef
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345663"
---
# <a name="service-configuration"></a>服務設定

每個版本的 QnA Maker 都會使用一組不同的 Azure 資源 (服務) 。 本文說明這些服務所支援的自訂。 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

QnA Maker 使用 App Service 來提供 [GENERATEANSWER API](/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer)所使用的查詢執行時間。

這些設定適用于 App Service 的 Azure 入口網站。 您可以依序選取 [**設定**] 和 [設定] 來使用這些 **設定。**

您可以透過 [應用程式設定] 清單設定個別設定，或選取 [ **Advanced edit （編輯**）] 來修改數個設定。

|資源|設定|
|--|--|
|AzureSearchAdminKey|認知搜尋-用於 QnA 配對儲存體和 Ranker #1|
|AzureSearchName|認知搜尋-用於 QnA 配對儲存體和 Ranker #1|
|DefaultAnswer|找不到相符的答案文字|
|UserAppInsightsAppId|聊天記錄和遙測|
|UserAppInsightsKey|聊天記錄和遙測|
|UserAppInsightsName|聊天記錄和遙測|

完成變更之後，您必須從 Azure 入口網站的 [**總覽**] 頁面 **重新開機** 服務。

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

App Service 自訂不適用於 QnA Maker 受控 (預覽) 。

---

## <a name="qna-maker-service"></a>QnA Maker 服務

QnA Maker 服務會提供下列使用者的設定，以在單一 QnA Maker 服務及其所有知識庫上共同作業。

瞭解 [如何將](./reference-role-based-access-control.md) 共同作業者新增至您的服務。

## <a name="change-azure-cognitive-search"></a>變更 Azure 認知搜尋

瞭解如何變更連結至 QnA Maker 服務 [的認知搜尋服務](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 。

## <a name="change-default-answer"></a>變更預設答案

瞭解 [如何變更預設答案的文字](How-To/change-default-answer.md)。 

## <a name="telemetry"></a>遙測

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 正式發行 (穩定版本)](#tab/v1)

Application Insights 用於監視 QnA Maker GA 的遙測。 QnA Maker 沒有特定的設定設定。

# <a name="qna-maker-managed-preview-release"></a>[受控 QnA Maker (預覽版本)](#tab/v2)

瞭解 [如何將遙測新增至您的 QnA Maker 受控 (預覽版) 服務](How-To/get-analytics-knowledge-base.md)。 

---

## <a name="app-service-plan"></a>App Service 方案

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (穩定版本) ](#tab/v1)

App Service 方案沒有適用于 QnA Maker 的任何設定設定。

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker 受控 (預覽版本) ](#tab/v2)

App Service 方案不適用於 QnA Maker 受控 (preview) 。

---

## <a name="next-steps"></a>後續步驟

深入瞭解您想要匯入知識庫的檔和 Url [格式](reference-document-format-guidelines.md) 。