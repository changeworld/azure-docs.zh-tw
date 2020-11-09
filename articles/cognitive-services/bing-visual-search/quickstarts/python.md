---
title: 快速入門：使用 REST API 和 Python 來取得影像見解 - Bing 圖像式搜尋
titleSuffix: Azure Cognitive Services
description: 了解如何使用 Bing 圖像式搜尋 API 和 Python 上傳影像，然後取得影像的相關見解。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-python
ms.openlocfilehash: 712f86eeaa49c1afe281ad5ede7a6cf2cc0ada4b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074975"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>快速入門：使用 Bing 圖像式搜尋 REST API 和 Python 取得影像見解

> [!WARNING]
> Bing 搜尋 API 將從認知服務移至 Bing 搜尋服務。 從 **2020 年 10 月 30 日** 開始，所有 Bing 搜尋的新執行個體都必須依照[這裡](https://aka.ms/cogsvcs/bingmove)所述的程序進行佈建。
> 使用認知服務佈建的 Bing 搜尋 API 將在未來三年受到支援，或支援到您的 Enterprise 合約結束為止 (視何者先發生)。
> 如需移轉指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

使用本快速入門，第一次呼叫 Bing 圖像式搜尋 API。 此 Python 應用程式會將影像上傳至 API，並顯示它傳回的資訊。 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

## <a name="prerequisites"></a>Prerequisites

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>初始化應用程式

1. 在您慣用的 IDE 或編輯器中建立新的 Python 專案，以及新增下列 `import` 陳述式：

    ```python
    import requests, json
    ```

2. 為您的訂用帳戶金鑰、端點，以及您上傳影像的路徑，建立變數。 對於 `BASE_URI` 值，您可以使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的[自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. 上傳本機影像時，表單資料必須包含 `Content-Disposition` 標頭。 將其 `name` 參數設為 "image"，以及將 `filename` 參數設為影像的檔案名稱。 表單的內容包含影像的二進位資料。 您可以上傳的影像大小上限為 1 MB。
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. 建立字典物件來保存您要求的標頭資訊。 將訂用帳戶金鑰繫結至字串 `Ocp-Apim-Subscription-Key`。

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. 建立包含您影像的另一個字典，這將會在您傳送要求時開啟並上傳。

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>剖析 JSON 回應

建立名為 `print_json()` 的方法來接受 API 回應，以及列印 JSON。

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>傳送要求

使用 `requests.post()` 將要求傳送至 Bing 圖像式搜尋 API。 包含您端點、標頭和檔案資訊的字串。 使用 `print_json()` 列印 `response.json()`。

```python
try:
    response = requests.post(BASE_URI, headers=HEADERS, files=file)
    response.raise_for_status()
    print_json(response.json())
    
except Exception as ex:
    raise ex
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [建立圖像式搜尋單頁 Web 應用程式](../tutorial-bing-visual-search-single-page-app.md)
