---
title: 服務設定-QnA Maker
description: 瞭解設定資源的方式和位置。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 1d54b912d2177a3ccd0cf34d57fc0358af653199
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776709"
---
# <a name="service-configuration"></a>服務設定

QnA Maker 使用數個 Azure 資源 (服務) 包括認知搜尋、App Service、App Service 方案和 Application Insights。

以下列出 QnA Maker 支援的這些設定的所有自訂。

## <a name="app-service"></a>App Service

QnA Maker 使用 App Service 來提供 [GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)所使用的查詢執行時間。


這些設定適用于 App Service 的 Azure 入口網站。 您可以依序選取 [**設定**] 和 [設定] 來使用這些**設定。**

您可以透過 [應用程式設定] 清單設定個別設定，或選取 [ **Advanced edit （編輯**）] 來修改數個設定。

|資源|設定|
|--|--|
|AzureSearchAdminKey|認知搜尋-用於 QnA 配對儲存體和 Ranker #1|
|AzureSearchName|認知搜尋-用於 QnA 配對儲存體和 Ranker #1|
|DefaultAnswer|找不到相符的答案文字|
|UserAppInsightsAppId|聊天記錄和遙測|
|UserAppInsightsKey|聊天記錄和遙測|
|UserAppInsightsName|聊天記錄和遙測|

瞭解 [如何將您的認知搜尋服務變更](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) 為您的服務。

完成變更之後，您必須從 Azure 入口網站的 [**總覽**] 頁面**重新開機**服務。

## <a name="qna-maker-service"></a>QnA Maker 服務

QnA Maker 服務會提供下列使用者的設定，以在單一 QnA Maker 服務及其所有知識庫上共同作業。

瞭解 [如何將](./how-to/collaborate-knowledge-base.md) 共同作業者新增至您的服務。

## <a name="application-insights"></a>Application Insights

Application Insights 沒有 QnA Maker 專用的設定設定。

## <a name="app-service-plan"></a>App Service 方案

App Service 方案沒有適用于 QnA Maker 的任何設定設定。

## <a name="next-steps"></a>後續步驟

深入瞭解您想要匯入知識庫的檔和 Url [格式](reference-document-format-guidelines.md) 。