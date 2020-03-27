---
title: 快速入門：使用 Python 呼叫您的 Bing 自訂搜尋端點 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 您可以使用本快速入門，開始使用 Python 要求 Bing 自訂搜尋執行個體所產生的搜尋結果
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: a601b309d18e489f6b631cb26e5f3e13ef790b42
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "80238840"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>快速入門：使用 Python 呼叫您的 Bing 自訂搜尋端點

您可以使用本快速入門來開始向「Bing 自訂搜尋」執行個體要求搜尋結果。 雖然此應用程式是以 Python 撰寫的，但 Bing 自訂搜尋 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py) 上有此範例的原始程式碼。

## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)，以取得詳細資訊。
- [Python](https://www.python.org/) 2.x 或 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列匯入陳述式。 為您的訂用帳戶金鑰、「自訂設定識別碼」及搜尋字詞建立變數。 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>傳送及接收搜尋要求 

1. 將搜尋字詞附加至 `q=` 查詢參數，並將搜尋執行個體的「自訂設定識別碼」附加至 `customconfig=`，以建構要求 URL。 使用 `&` 字元來分隔參數。 您可以使用下方的全域端點，也可以使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. 將要求傳送至 Bing 自訂搜尋執行個體，並印出傳回的搜尋結果。  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](./tutorials/custom-search-web-page.md)
