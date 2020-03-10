---
title: Bing 自訂搜尋 Python 用戶端程式庫快速入門
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78253086"
---
開始使用適用於 Python 的 Bing 自訂搜尋用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。 Bing 自訂搜尋 API 可讓您針對感興趣的主題，建立量身訂做且無廣告的搜尋經驗。在 [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py)上可以找到此範例的原始程式碼。

使用適用於 Python 的 Bing 自訂搜尋用戶端程式庫：
* 從您的 Bing 自訂搜尋執行個體，在 Web 上尋找搜尋結果。

[參考文件](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch) | [套件 (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) | [範例](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Prerequisites

- 「Bing 自訂搜尋」執行個體。 請參閱[快速入門：建立您的第一個 Bing 自訂搜尋執行個體](../../quick-start.md)，以取得詳細資訊。
- Python [2.x 或 3.x](https://www.python.org/) 
- [適用於 Python 的 Bing 自訂搜尋 SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>安裝 Python 用戶端程式庫

使用下列命令安裝 Bing 自訂搜尋用戶端程式庫。

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
> [建置自訂搜尋 Web 應用程式](../../tutorials/custom-search-web-page.md)
