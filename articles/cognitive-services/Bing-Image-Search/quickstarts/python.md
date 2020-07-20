---
title: 快速入門：使用 Bing 影像搜尋 REST API 和 Python 來搜尋影像
titleSuffix: Azure Cognitive Services
description: 使用此快速入門以運用 Python 來傳送影像搜尋要求給「Bing 影像搜尋 REST API」，並接收 JSON 回應。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, tracking-python
ms.openlocfilehash: f818030f5fa7c562b4041543cb702d9673648dee
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85603327"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>快速入門：使用 Bing 影像搜尋 REST API 和 Python 來搜尋影像

使用本快速入門，了解如何將搜尋要求傳送至 Bing 影像搜尋 API。 這個 Python 應用程式會將搜尋查詢傳送至 API，並顯示結果中第一個影像的 URL。 雖然此應用程式是以 Python 撰寫的，但 API 是一種與大多數程式設計語言都相容的 RESTful Web 服務。

若要在 [MyBinder](https://mybinder.org) 上以 Jupyter Notebook 執行此範例，請選取 [啟動文件夾] 徽章：

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


此範例的原始程式碼可從 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) 取得，其中含有其他錯誤處理和註釋。


## <a name="prerequisites"></a>必要條件

* [Python 2.x 或 3.x](https://www.python.org/)
* [Python Imaging Library (PIL)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>建立應用程式並將其初始化

1. 在您最愛的 IDE 或編輯器中建立新的 Python 檔案，並匯入下列模組。 建立訂用帳戶金鑰、搜尋端點和搜尋字詞的變數。 針對 [，您可使用下列程式碼中的全域端點，或使用 Azure 入口網站中針對您的資源所顯示的`search_url`自訂子網域](../../../cognitive-services/cognitive-services-custom-subdomains.md)端點。

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. 藉由建立字典並將金鑰新增為值，將訂用帳戶金鑰新增至 `Ocp-Apim-Subscription-Key` 標頭。 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>建立及傳送搜尋要求

1. 建立搜尋要求參數的字典。 將您的搜尋字詞新增至 `q` 參數。 將 `license` 參數設定為 `public`，以搜尋公用網域中的影像。 將 `imageType` 設定為 `photo`，只搜尋相片。

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. 使用 `requests` 程式庫呼叫 Bing 影像搜尋 API。 將您的標頭和參數新增至要求，並傳回 JSON 物件形式的回應。 從回應的 `thumbnailUrl` 欄位取得數個縮圖影像的 URL。

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>檢視回應

1. 使用 matplotlib 程式庫，建立包含四個資料行和四個資料列的新圖表。 

2. 逐一查看該圖表的資料列和資料行，並使用 PIL 程式庫的 `Image.open()` 方法將影像縮圖新增至每個空間。 

3. 使用 `plt.show()` 繪製圖表並顯示影像。

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>範例 JSON 回應

來自「Bing 影像搜尋 API」的回應會以 JSON 形式傳回。 本範例回應已截斷而只顯示單一結果。

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Bing 影像搜尋單頁應用程式教學課程](../tutorial-bing-image-search-single-page-app.md) (英文)

* [什麼是 Bing 影像搜尋 API？](../overview.md)  
* [Bing 搜尋 API 的定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Azure 認知服務文件](https://docs.microsoft.com/azure/cognitive-services)
* [Bing 影像搜尋 API 參考](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
