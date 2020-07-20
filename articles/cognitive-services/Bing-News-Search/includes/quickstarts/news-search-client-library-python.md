---
title: Bing 新聞搜尋 Python 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "79503548"
---
使用本快速入門，透過適用於 Python 的 Bing 新聞搜尋用戶端程式庫開始搜尋新聞。 雖然 Bing 新聞搜尋具有與大部分程式設計語言相容的 REST API，但此用戶端程式庫可提供簡單的方法，將服務整合到您的應用程式。 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) 上找到。

## <a name="prerequisites"></a>Prerequisites

* [Python](https://www.python.org/) 2.x 或 3.x

建議使用[虛擬環境](https://docs.python.org/3/tutorial/venv.html)來開發 Python。 您可以使用 [venv 模組](https://pypi.python.org/pypi/virtualenv)來安裝和初始化虛擬環境。 您必須安裝適用於 Python 2.7 的 virtualenv。 您可以使用下列命令建立虛擬環境：

```console
python -m venv mytestenv
```

您可以使用下列命令安裝 Bing 新聞搜尋用戶端程式庫相依性：
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 Python 檔案，並匯入下列程式庫。 建立訂用帳戶金鑰變數和搜尋字詞。

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>初始化用戶端並傳送要求

1. 建立 `CognitiveServicesCredentials` 執行個體。
    
    ```python
    client = NewsSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

2. 將搜尋查詢傳送至 Bing 新聞搜尋 API，儲存回應。

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>剖析回應

如果找到任何搜尋結果，請列印第一個網頁結果：

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立單頁 Web 應用程式](../../tutorial-bing-news-search-single-page-app.md)
