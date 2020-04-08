---
title: 服務設定 - QnA 製造商
description: 瞭解如何配置資源以及在哪裡配置資源。
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 3be32d1778604121c2acac88415cbfbc4bdbca3d
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804255"
---
# <a name="service-configuration"></a>服務設定

QnA Maker 使用多個 Azure 資源(服務),包括認知搜索、應用服務、應用服務計劃和應用程式見解。

下面列出了 QnA Maker 支援的這些設置的所有自定義項。

## <a name="app-service"></a>App Service 方案

QnA Maker 使用應用服務提供[生成應答 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)使用的查詢運行時。


這些設置在 Azure 門戶中可用於應用服務。 這些設定可通過選擇 **「設定」 (** 然後 **「設定」 來**提供。

您可以通過"應用程式設置"清單設置單個設置,也可以通過選擇 **「高級編輯**」來修改多個設置。

|資源|設定|
|--|--|
|Azure 搜尋管理金鑰|認知搜尋 - 用於 QnA 儲存和 Ranker #1|
|Azure 搜尋名稱|認知搜尋 - 用於 QnA 儲存和 Ranker #1|
|預設答案|找不到符合項目時的應答文字|
|使用者AppInsightsAppId|聊天日誌和遙測|
|使用者套用解鍵|聊天日誌和遙測|
|使用者套用解名稱|聊天日誌和遙測|

[瞭解如何將更改認知搜索服務](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource)添加到服務中。

完成更改後,需要從 Azure 門戶的 **「概述」** 頁**重新啟動**服務。

## <a name="qna-maker-service"></a>QnA Maker 服務

QnA Maker 服務為以下使用者提供配置,以便協作處理單個 QnA Maker 服務及其所有知識庫。

[瞭解如何提供服務的協作者](./how-to/collaborate-knowledge-base.md)。

## <a name="application-insights"></a>Application Insights

應用程式見解沒有特定於 QnA 製造商的配置設置。

## <a name="app-service-plan"></a>App Service 方案

應用服務計劃沒有特定於 QnA 製造商的配置設置。

## <a name="next-steps"></a>後續步驟

詳細瞭解要匯入知識庫的文件與網址的[格式](reference-document-format-guidelines.md)。