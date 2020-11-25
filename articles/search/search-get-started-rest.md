---
title: 快速入門：使用 REST API 建立搜尋索引
titleSuffix: Azure Cognitive Search
description: 在本 REST API 快速入門中，了解如何使用 Postman 或 Visual Studio Code 來呼叫 Azure 認知搜尋 REST API。
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711301"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>快速入門：使用 REST API 建立 Azure 認知搜尋索引

本文說明如何使用 [Azure 認知搜尋 REST API](/rest/api/searchservice)，以互動方式編寫 REST API 要求，以及用來傳送和接收要求的 API 用戶端。 透過 API 用戶端與這些指示，您可以在撰寫任何程式碼前，先傳送要求並檢視回應。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>清除資源

使用您自己的訂用帳戶時，在專案結束後確認您是否還需要您建立的資源，是很好的做法。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

如果您使用免費服務，請記住您會有三個索引、索引子和資料來源的限制。 您可以在入口網站中刪除個別項目，以避免超出限制。 

## <a name="next-steps"></a>後續步驟

現在您已了解如何執行核心工作，接下來可以繼續進行其他的 REST API 呼叫以執行更進階的功能，例如索引子或[設定擴充管線](cognitive-search-tutorial-blob.md) (將內容轉換新增至索引)。 對於您的下一個步驟，建議您參閱下列連結：

> [!div class="nextstepaction"]
> [教學課程：使用 REST 和 AI 從 Azure Blob 產生可搜尋的內容](cognitive-search-tutorial-blob.md)