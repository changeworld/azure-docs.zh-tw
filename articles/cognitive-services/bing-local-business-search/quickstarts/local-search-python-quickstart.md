---
title: 快速入門 - 傳送查詢至以 Python 撰寫的 API - Bing 當地企業搜尋
titleSuffix: Azure Cognitive Services
description: 使用本快速入門來開始使用以 Python 撰寫的 Bing 當地商家搜尋 API。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: tracking-python
ms.openlocfilehash: fcd3ab3cce74aa0ef021427904077c364de1f493
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84606270"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>快速入門：傳送查詢至以 Python 撰寫的 Bing 當地商家搜尋 API

使用此快速入門了解如何向 Bing 當地企業搜尋 API 傳送要求，該 API 是 Azure 認知服務。 雖然此簡單應用程式是以 Python 撰寫，但 API 是一種與任何程式語言相容的 RESTful Web 服務，可產生 HTTP 要求，並剖析 JSON。

此範例應用程式會從 API 取得搜尋查詢的當地回應資料。

## <a name="prerequisites"></a>Prerequisites

* [Python](https://www.python.org/) 2.x 或 3.x。
* 具有 Bing 搜尋 API 的[認知服務 API 帳戶](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)。 [免費試用版](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)即足以供本快速入門使用。 啟動免費試用時，請儲存提供的 API 金鑰。 如需詳細資訊，請參閱[認知服務定價 - Bing 搜尋 API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)。

## <a name="run-the-complete-application"></a>執行完整應用程式

下列範例會取得本地化的結果，其會在下列步驟中實作：
1. 宣告變數以依主機及路徑指定端點。
2. 指定查詢參數。 
3. 定義建立要求的搜尋函式，並新增 `Ocp-Apim-Subscription-Key` 標題。
4. 設定 `Ocp-Apim-Subscription-Key` 標頭。 
5. 進行連線，並傳送要求。
6. 列印 JSON 結果。

此示範的完整程式碼如下：

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>後續步驟
- [當地商家搜尋 Java 快速入門](local-search-java-quickstart.md)
- [當地企業搜尋 C# 快速入門](local-quickstart.md)
- [當地企業搜尋 Node.js 快速入門](local-search-node-quickstart.md)
