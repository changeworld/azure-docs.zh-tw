---
title: 快速入門：使用 Python 呼叫您的 Bing 自訂搜尋端點 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 您可以使用本快速入門，開始使用 Python 要求 Bing 自訂搜尋執行個體所產生的搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: b214eaebf10473985ef5458590d962d37797c4e4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080986"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>快速入門：使用 Python 呼叫您的 Bing 自訂搜尋端點

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照[這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

使用本快速入門了解如何要求 Bing 自訂搜尋執行個體所產生的搜尋結果。 雖然此應用程式是以 Python 撰寫的，但 Bing 自訂搜尋 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py) 上有此範例的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 如需詳細資訊，請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)。
- [Python](https://www.python.org/) 2.x 或 3.x。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

- 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入陳述式。 為您的訂用帳戶金鑰、自訂設定識別碼和搜尋字詞建立變數。

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>傳送及接收搜尋要求 

1. 將搜尋字詞附加至 `q=` 查詢參數，並將搜尋執行個體的自訂設定識別碼附加至 `customconfig=` 參數，以建構要求 URL。 請以 `&` 符號分隔參數。 您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源顯示的[自訂子網域](../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. 將要求傳送至 Bing 自訂搜尋執行個體，並列印傳回的搜尋結果。  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](./tutorials/custom-search-web-page.md)
