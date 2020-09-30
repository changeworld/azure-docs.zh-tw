---
title: " 建置單頁 Web 應用程式 - Bing 圖像式搜尋"
titleSuffix: Azure Cognitive Services
description: 了解如何將 Bing 圖像式搜尋 API 整合至單頁 Web 應用程式。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 28f7d279d1328ff750bf7095f32fbf3ff65e591b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324517"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>教學課程：建立圖像式搜尋單頁 Web 應用程式

Bing 圖像式搜尋 API 會傳回影像的見解。 您可以上傳影像，也可以提供影像的 URL。 見解是視覺上類似的影像、購物來源、包含影像的網頁等。 Bing 圖像式搜尋 API 所傳回的見解，類似於 Bing.com/images 上顯示的內容。

本教學課程說明如何擴充 Bing 影像搜尋 API 的單頁 Web 應用程式。 若要檢視該教學課程或取得此處所用的原始程式碼，請參閱[教學課程：建立 Bing 影像搜尋 API 的單頁應用程式](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md)。

此應用程式的完整原始程式碼 (在經過擴充而使用 Bing 圖像式搜尋 API 之後) 可於 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html) 取得。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>呼叫 Bing 圖像式搜尋 API 並處理回應

編輯 Bing 影像搜尋教學課程，並將下列程式碼新增至 `<script>` 元素的結尾處 (並在結尾 `</script>` 標籤之前)。 下列程式碼會處理來自 API 的圖像式搜尋回應、逐一查看結果，並加以顯示：

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

下列程式碼會使用事件接聽程式來呼叫 `handleVisualSearchResponse()`，以將搜尋要求傳送至 API：

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>擷取深入解析權杖

將下列程式碼新增至 `searchItemsRenderer` 物件中。 程式碼會新增經點按時會呼叫 `bingVisualSearch` 函式的**尋找類似項目**連結。 此函式會接收 `imageInsightsToken` 以作為引數。

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>顯示類似的影像

在第 601 行將新增下列 HTML 程式碼。 此標記程式碼會新增用來顯示 Bing 圖像式搜尋 API 呼叫結果的元素：

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

當所有新的 JavaScript 程式碼與 HTML 元素皆設置妥當後，就會顯示搜尋結果以及**尋找類似項目**連結。 按一下此連結，可在 [類似]  區段中填入與您選擇的影像類似的影像。 您可能必須展開 [類似]  區段以顯示影像。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用適用於 C# 的 Bing 圖像式搜尋 SDK 裁剪影像](tutorial-visual-search-crop-area-results.md)
