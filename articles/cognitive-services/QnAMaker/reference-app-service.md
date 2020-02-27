---
title: 服務設定-QnA Maker
description: 瞭解設定資源的方式和位置。
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651993"
---
# <a name="service-configuration"></a>服務設定

QnA Maker 使用數個 Azure 資源（服務），包括認知搜尋、App Service、App Service 方案和 Application Insights。

QnA Maker 所支援的這些設定的所有自訂，如下所示。

## <a name="app-service"></a>App Service 方案

QnA Maker 使用 App Service 來提供[GENERATEANSWER API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)所使用的查詢執行時間。


這些設定適用于 App Service 的 Azure 入口網站。 您可以依序選取 [**設定**] 和 [設定] 來取得**設定。**

您可以透過 [應用程式設定] 清單來設定個別設定，或選取 [ **Advanced edit**] 來修改數個設定。

|資源|設定|
|--|--|
|AzureSearchAdminKey|認知搜尋-用於 QnA 設定儲存體和 Ranker #1|
|AzureSearchName|認知搜尋-用於 QnA 設定儲存體和 Ranker #1|
|DefaultAnswer|找不到相符的答案文字|
|UserAppInsightsAppId|聊天記錄和遙測|
|UserAppInsightsKey|聊天記錄和遙測|
|UserAppInsightsName|聊天記錄和遙測|

瞭解[如何將您的認知搜尋服務變更](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)為您的服務。

當您完成變更之後，您必須從 Azure 入口網站的 [**總覽**] 頁面**重新開機**服務。

## <a name="qna-maker-service"></a>QnA Maker 服務

QnA Maker 服務會提供設定，讓下列使用者在單一 QnA Maker 服務及其所有知識庫上共同作業。

瞭解[如何將](./how-to/collaborate-knowledge-base.md)共同作業者新增至您的服務。

## <a name="application-insights"></a>Application Insights

Application Insights 沒有 QnA Maker 特有的設定。

## <a name="app-service-plan"></a>App Service 方案

App Service 計畫沒有 QnA Maker 特有的設定。

## <a name="next-steps"></a>後續步驟

深入瞭解您想要匯入知識庫之檔和 Url 的[格式](reference-document-format-guidelines.md)。