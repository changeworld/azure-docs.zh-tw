---
title: 快速入門：使用 Python 執行搜尋 - Bing Web 搜尋 API
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Python 來傳送要求給「Bing Web 搜尋 REST API」，並接收 JSON 回應
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c1af1142faca76cc58b6b3ca9a7106bc0433ea18
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976361"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>快速入門：使用 Python 來呼叫 Bing Web 搜尋 API  

使用本快速入門進行對 Bing Web 搜尋 API 第一次的呼叫，並接收 JSON 回應。 這個 Python 應用程式會將搜尋要求傳送給 API，並顯示回應。 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

此範例在 [MyBinder](https://mybinder.org) (英文) 上以 Jupyter Notebook 形式執行。 選取啟動文件夾徽章：

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Prerequisites

* [Python 2.x 或 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>定義變數

將 `subscription_key` 值換成您的 Azure 帳戶中有效的訂用帳戶金鑰。

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

宣告 Bing Web 搜尋 API 端點。 您可以使用下方的全域端點，也可以使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

請自行取代 `search_term` 的值來自訂搜尋查詢。

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>發出要求

下列區塊會使用 `requests` 程式庫呼叫 Bing Web 搜尋 API，並以 JSON 物件的形式傳回結果。 API 金鑰會透過 `headers` 字典傳入，而搜尋字詞和查詢參數則會透過 `params` 字典傳入。 如需選項和參數的完整清單，請參閱 [Bing Web 搜尋 API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) (英文) 文件。

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>格式化並顯示回應

`search_results` 物件包含搜尋結果和中繼資料，例如相關的查詢和網頁。 此程式碼會使用 `IPython.display` 程式庫來格式化回應，並顯示在瀏覽器中。

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>GitHub 上的範例程式碼

如果您想要在本機執行此程式碼，完整的[範例在 GitHub 上](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py) (英文)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing Web 搜尋單頁應用程式教學課程](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
