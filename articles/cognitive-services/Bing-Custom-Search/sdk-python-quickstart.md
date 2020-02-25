---
title: 快速入門：使用 Python SDK 呼叫您的 Bing 自訂搜尋端點
titleSuffix: Azure Cognitive Services
description: 您可以使用本快速入門，開始使用 Python SDK 要求 Bing 自訂搜尋執行個體所產生的搜尋結果。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 02/12/2020
ms.author: aahi
ms.openlocfilehash: 4f4b6bad38992a9e0146d6324bc3a3fc7632ded2
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201249"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-the-python-sdk"></a>快速入門：使用 Python SDK 呼叫您的 Bing 自訂搜尋端點 

您可以使用本快速入門，開始使用 Python SDK 要求 Bing 自訂搜尋執行個體所產生的搜尋結果。 雖然 Bing 自訂搜尋具有與大部分程式設計語言相容的 REST API，但 Bing 自訂搜尋 SDK 會提供簡單的方法，將服務整合到您的應用程式。 在 [Github](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py) 上可找到此範例的完整原始程式碼，以及其他錯誤處理和註釋。

## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](quick-start.md)，以取得詳細資訊。
- Python [2.x 或 3.x](https://www.python.org/) 
- [適用於 Python 的 Bing 自訂搜尋 SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

## <a name="install-the-python-sdk"></a>安裝 Python SDK

使用下列命令安裝 Bing 自訂搜尋服務 SDK。

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>建立新的應用程式

在您慣用的編輯器或 IDE 中建立新的 Python 檔案，並新增下列匯入項目。

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>建立搜尋用戶端並傳送要求

1. 建立訂用帳戶金鑰和端點的變數。

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. 使用 `CognitiveServicesCredentials` 物件與訂用帳戶金鑰建立 `CustomSearchClient` 的執行個體。 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. 透過 `client.custom_instance.search()` 傳送搜尋要求。 將搜尋字詞附加到 `query` 參數，並將 `custom_config` 設為您的自訂組態識別碼，以使用您的搜尋執行個體。 您可以按一下 [生產]  索引標籤，從 [Bing 自訂搜尋入口網站](https://www.customsearch.ai/)取得您的識別碼。

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>檢視搜尋結果

如果找到任何網頁搜尋結果，請取得第一個結果並列印其名稱、URL，以及找到的網頁總數。

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建置自訂搜尋 Web 應用程式](./tutorials/custom-search-web-page.md)
