---
title: 教學課程：如何使用 Bing 圖像式搜尋 API 上傳影像
titleSuffix: Azure Cognitive Services
description: 了解如何將影像上傳至 Bing、取得深入解析並顯示回應。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2020
ms.author: scottwhi
ms.custom: devx-track-javascript
ms.openlocfilehash: c2e0e907734f30010690817ddb7bb15527c93bbf
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87404008"
---
# <a name="tutorial-upload-images-to-the-bing-visual-search-api"></a>教學課程：將影像上傳至 Bing 圖像式搜尋 API

Bing 圖像式搜尋 API 可讓您在 Web 上搜尋類似您所上傳影像的影像。 您可以使用此教學課程來建立 Web 應用程式，以將影像傳送至該 API，並在網頁內顯示它所傳回的見解。 請注意，此應用程式不會遵守使用此 API 的所有 [Bing 使用和顯示需求](../bing-web-search/use-display-requirements.md)。

您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchUploadImage.html) 上找到此範例的完整原始程式碼，其中含有其他錯誤處理和註釋。

本教學課程應用程式說明如何：

> [!div class="checklist"]
> * 將影像上傳至 Bing 圖像式搜尋 API
> * 在 Web 應用程式中顯示影像搜尋結果
> * 探索該 API 所提供的不同見解

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-structure-the-webpage"></a>建立和建構網頁

建立 HTML 網頁，以將影像傳送至 Bing 圖像式搜尋 API、取得見解並加以顯示。 在您慣用的編輯器或 IDE 中，建立名為 "uploaddemo.html" 的檔案。 將下列基本 HTML 結構新增至檔案：

```html
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <title>Visual Search Upload Demo</title>
    </head>

    <body>
    </body>
</html>
```

將頁面分成要求區段 (其中使用者會提供要求所需的所有資訊) 和回應區段 (其中會顯示見解)。 將下列 `<div>` 標記新增至 `<body>`。 `<hr>` 標記會以視覺化方式區分要求區段與回應區段：

```html
<div id="requestSection"></div>
<hr />
<div id="responseSection"></div>
```

將 `<script>` 標記新增至 `<head>` 標記，以包含應用程式的 JavaScript：

```html
<script>
<\script>
```

## <a name="get-the-upload-file"></a>取得上傳檔案

為讓使用者選取要上傳的影像，應用程式會使用 `<input>` 標記搭配設為 `file` 的類型屬性。 UI 需要將其清除，應用程式才可使用 Bing 取得搜尋結果。

將下列 `<div>` 新增至 `requestSection` `<div>`。 檔案輸入可接受任何影像類型 (例如 .jpg、.gif、.png) 的單一檔案。 `onchange` 事件可指定使用者選取檔案時所呼叫的處理常式。

`<output>` 標記用來顯示所選影像的縮圖：

```html
<div>
    <p>Select image to get insights from Bing:
        <input type="file" accept="image/*" id="uploadImage" name="files[]" size=40 onchange="handleFileSelect('uploadImage')" />
    </p>

    <output id="thumbnail"></output>
</div>
```

## <a name="create-a-file-handler"></a>建立檔案控制代碼

建立可讀入所要上傳影像的處理常式函式。 逐一查看 `FileList` 物件中的檔案時，此處理常式應確認選取的檔案為影像檔，且其大小為 1 MB 或更小。 如果影像較大，您必須先將它縮小，再上傳。 最後，處理常式會顯示影像的縮圖：

```javascript
function handleFileSelect(selector) {

    var files = document.getElementById(selector).files; // A FileList object

    for (var i = 0, f; f = files[i]; i++) {

        // Ensure the file is an image file.
        if (!f.type.match('image.*')) {
            alert("Selected file must be an image file.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        // Image must be <= 1 MB and should be about 1500px.
        if (f.size > 1000000) {
            alert("Image must be less than 1 MB.");
            document.getElementById("uploadImage").value = null;
            continue;
        }

        var reader = new FileReader();

        // Capture the file information.
        reader.onload = (function(theFile) {
            return function(e) {
                var fileOutput = document.getElementById('thumbnail');

                if (fileOutput.childElementCount > 0) {
                    fileOutput.removeChild(fileOutput.lastChild);  // Remove the current pic, if it exists
                }

                // Render thumbnail.
                var span = document.createElement('span');
                span.innerHTML = ['<img class="thumb" src="', e.target.result,
                                    '" title="', escape(theFile.name), '"/>'].join('');
                fileOutput.insertBefore(span, null);
            };
        })(f);

        // Read in the image file as a data URL.
        reader.readAsDataURL(f);
    }
}
```

## <a name="add-and-store-a-subscription-key"></a>新增和儲存訂用帳戶金鑰

應用程式需要訂用帳戶金鑰，才能對 Bing 圖像式搜尋 API 進行呼叫。 在本教學課程中，您會在 UI 中提供該金鑰。 將下列 `<input>` 標記 (其類型屬性設為 text) 新增至 `<body>` (位於檔案的 `<output>` 標記正下方)：

```html
    <div>
        <p>Subscription key: 
            <input type="text" id="key" name="subscription" size=40 maxlength="32" />
        </p>
    </div>
```

如果您有影像與訂用帳戶金鑰，就可以呼叫 Bing 圖像式搜尋來取得有關影像的見解。 在本教學課程中，此呼叫會使用預設的市場 (`en-us`) 和安全搜尋值 (`moderate`)。

此應用程式有一個選項可變更這些值。 在訂用帳戶金鑰 `<div>` 下方新增下列 `<div>`。 應用程式會使用 `<select>` 標記，提供市場和安全搜尋值的下拉式清單。 這兩份清單都會顯示預設值。

```html
<div>
    <p><a href="#" onclick="expandCollapse(options.id)">Query options</a></p>

    <div id="options" style="display:none">
        <p style="margin-left: 20px">Market: 
            <select id="mkt">
                <option value="es-AR">Argentina (Spanish)</option>
                <option value="en-AU">Australia (English)</option>
                <option value="de-AT">Austria (German)</option>
                <option value="nl-BE">Belgium (Dutch)</option>
                <option value="fr-BE">Belgium (French)</option>
                <option value="pt-BR">Brazil (Portuguese)</option>
                <option value="en-CA">Canada (English)</option>
                <option value="fr-CA">Canada (French)</option>
                <option value="es-CL">Chile (Spanish)</option>
                <option value="da-DK">Denmark (Danish)</option>
                <option value="fi-FI">Finland (Finnish)</option>
                <option value="fr-FR">France (French)</option>
                <option value="de-DE">Germany (German)</option>
                <option value="zh-HK">Hong Kong SAR(Traditional Chinese)</option>
                <option value="en-IN">India (English)</option>
                <option value="en-ID">Indonesia (English)</option>
                <option value="it-IT">Italy (Italian)</option>
                <option value="ja-JP">Japan (Japanese)</option>
                <option value="ko-KR">Korea (Korean)</option>
                <option value="en-MY">Malaysia (English)</option>
                <option value="es-MX">Mexico (Spanish)</option>
                <option value="nl-NL">Netherlands (Dutch)</option>
                <option value="en-NZ">New Zealand (English)</option>
                <option value="no-NO">Norway (Norwegian)</option>
                <option value="zh-CN">People's Republic of China (Chinese)</option>
                <option value="pl-PL">Poland (Polish)</option>
                <option value="pt-PT">Portugal (Portuguese)</option>
                <option value="en-PH">Philippines (English)</option>
                <option value="ru-RU">Russia (Russian)</option>
                <option value="ar-SA">Saudi Arabia (Arabic)</option>
                <option value="en-ZA">South Africa (English)</option>
                <option value="es-ES">Spain (Spanish)</option>
                <option value="sv-SE">Sweden (Swedish)</option>
                <option value="fr-CH">Switzerland (French)</option>
                <option value="de-CH">Switzerland (German)</option>
                <option value="zh-TW">Taiwan (Traditional Chinese)</option>
                <option value="tr-TR">Turkey (Turkish)</option>
                <option value="en-GB">United Kingdom (English)</option>
                <option value="en-US" selected>United States (English)</option>
                <option value="es-US">United States (Spanish)</option>
            </select>
        </p>
        <p style="margin-left: 20px">Safe search: 
            <select id="safesearch">
                <option value="moderate" selected>Moderate</option>
                <option value="strict">Strict</option>
                <option value="off">off</option>
            </select>
        </p>
    </div>
</div>
```

## <a name="add-search-options-to-the-webpage"></a>將搜尋選項新增至網頁

應用程式會在可摺疊的 `<div>` 中，隱藏受 [查詢選項] 連結控制的清單。 當您按一下 [查詢選項] 連結時，`<div>` 會展開，因此您可以查看並修改查詢選項。 如果您再按一下 [查詢選項] 連結，`<div>` 便會摺疊並隱藏起來。 下列程式碼片段顯示 [查詢選項] 連結的 `onclick` 處理常式。 此處理常式會控制要展開還是摺疊 `<div>`。 將此處理常式新增至 `<script>` 區段。 此處理常式會由示範中的所有可摺疊 `<div>` 區段使用。

```javascript
// Contains the toggle state of divs.
var divToggleMap = {};  // divToggleMap['foo'] = 0;  // 1 = show, 0 = hide


// Toggles between showing and hiding the specified div.
function expandCollapse(divToToggle) {
    var div = document.getElementById(divToToggle);

    if (divToggleMap[divToToggle] == 1) {   // if div is expanded
        div.style.display = "none";
        divToggleMap[divToToggle] = 0;
    }
    else {                                  // if div is collapsed
        div.style.display = "inline-block";
        divToggleMap[divToToggle] = 1;
    }
}
```

## <a name="call-the-onclick-handler"></a>呼叫 `onclick` 處理常式

在本文中的選項 `<div>` 下方，新增下列 `"Get insights"` 按鈕。 此按鈕可讓您起始呼叫。 按一下按鈕時，游標會變成旋轉等待的游標，且系統會呼叫 `onclick` 處理常式。

```html
<p><input type="button" id="query" value="Get insights" onclick="document.body.style.cursor='wait'; handleQuery()" /></p>
```

將按鈕的 `onclick` 處理常式 (`handleQuery()`) 新增至 `<script>` 標記。

## <a name="handle-the-query"></a>處理查詢

`handleQuery()` 處理常式會確定訂用帳戶金鑰存在且長度為 32 個字元，而且已選取影像。 它也會清除前一個查詢的任何見解。 之後會呼叫 `sendRequest()` 函式來進行呼叫。

```javascript
function handleQuery() {
    var subscriptionKey = document.getElementById('key').value;

    // Make sure user provided a subscription key and image.
    // For this demo, the user provides the key but typically you'd
    // get it from secured storage.
    if (subscriptionKey.length !== 32) {
        alert("Subscription key length is not valid. Enter a valid key.");
        document.getElementById('key').focus();
        return;
    }

    var imagePath = document.getElementById('uploadImage').value;

    if (imagePath.length === 0)
    {
        alert("Please select an image to upload.");
        document.getElementById('uploadImage').focus();
        return;
    }

    var responseDiv = document.getElementById('responseSection');

    // Clear out the response from the last query.
    while (responseDiv.childElementCount > 0) {
        responseDiv.removeChild(responseDiv.lastChild);
    }

    // Send the request to Bing to get insights about the image.
    var f = document.getElementById('uploadImage').files[0];
    sendRequest(f, subscriptionKey);
}
```

## <a name="send-the-search-request"></a>傳送搜尋要求

`sendRequest()` 函式會設定端點 URL 的格式、將 `Ocp-Apim-Subscription-Key` 標頭設定為訂用帳戶金鑰、附加要上傳之影像的二進位檔、指定回應處理常式，以及進行呼叫：

```javascript
function sendRequest(file, key) {
    var market = document.getElementById('mkt').value;
    var safeSearch = document.getElementById('safesearch').value;
    var baseUri = `https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=${market}&safesearch=${safeSearch}`;

    var form = new FormData();
    form.append("image", file);

    var request = new XMLHttpRequest();

    request.open("POST", baseUri);
    request.setRequestHeader('Ocp-Apim-Subscription-Key', key);
    request.addEventListener('load', handleResponse);
    request.send(form);
}
```

## <a name="get-and-handle-the-api-response"></a>取得並處理 API 回應

`handleResponse()` 函式會處理來自 Bing 圖像式搜尋呼叫的回應。 如果呼叫成功，它會將 JSON 回應剖析成個別的標籤，其中包含見解。 接下來，它會將搜尋結果新增至頁面。 然後，應用程式會針對每個標記建立可摺疊的 `<div>`，以管理顯示的資料量。 將處理常式新增至 `<script>` 區段。

```javascript
function handleResponse() {
    if(this.status !== 200){
        alert("Error calling Bing Visual Search. See console log for details.");
        console.log(this.responseText);
        return;
    }

    var tags = parseResponse(JSON.parse(this.responseText));
    var h4 = document.createElement('h4');
    h4.textContent = 'Bing internet search results';
    document.getElementById('responseSection').appendChild(h4);
    buildTagSections(tags);

    document.body.style.cursor = 'default'; // reset the wait cursor set by query insights button
}
```

### <a name="parse-the-response"></a>剖析回應

`parseResponse` 函式會逐一查看 `json.tags`，以將 JSON 回應轉換成字典物件。

```javascript
function parseResponse(json) {
    var dict = {};

    for (var i =0; i < json.tags.length; i++) {
        var tag = json.tags[i];

        if (tag.displayName === '') {
            dict['Default'] = JSON.stringify(tag);
        }
        else {
            dict[tag.displayName] = JSON.stringify(tag);
        }
    }

    return(dict);
}
```

### <a name="build-a-tag-section"></a>建置標記區段

`buildTagSections()` 函式會逐一查看剖析的 JSON 標記，並呼叫 `buildDiv()` 函式，以便針對每個標記建置 `<div>`。 每個標記都會顯示為連結。 按一下連結時，標記會展開，顯示與該標記相關聯的見解。 再次按一下該連結會導致區段收合。

```javascript
function buildTagSections(tags) {
    for (var tag in tags) {
        if (tags.hasOwnProperty(tag)) {
            var tagSection = buildDiv(tags, tag);
            document.getElementById('responseSection').appendChild(tagSection);
        }
    }  
}

function buildDiv(tags, tag) {
    var tagSection = document.createElement('div');
    tagSection.setAttribute('class', 'subSection');

    var link = document.createElement('a');
    link.setAttribute('href', '#');
    link.setAttribute('style', 'float: left;')
    link.text = tag;
    tagSection.appendChild(link);

    var contentDiv = document.createElement('div');
    contentDiv.setAttribute('id', tag);
    contentDiv.setAttribute('style', 'clear: left;')
    contentDiv.setAttribute('class', 'section');
    tagSection.appendChild(contentDiv);

    link.setAttribute('onclick', `expandCollapse("${tag}")`);
    divToggleMap[tag] = 0;  // 1 = show, 0 = hide

    addDivContent(contentDiv, tag, tags[tag]);

    return tagSection;
}
```

## <a name="display-the-search-results-in-the-webpage"></a>在網頁中顯示搜尋結果

`buildDiv()` 函式會呼叫 `addDivContent` 函式，以建置每個標記之可摺疊 `<div>` 的內容。

標籤的內容包括標籤回應中的 JSON。 一開始，只會顯示 JSON 的前 100 個字元，但是您可以按一下 JSON 字串以顯示所有的 JSON。 如果您再按一下該字串，JSON 字串便會摺疊回 100 個字元。

接著，加入在標籤中找到的動作類型。 針對每個動作類型，呼叫適當的函式以新增其見解：

```javascript
function addDivContent(div, tag, json) {

    // Adds the first 100 characters of the json that contains
    // the tag's data. The user can click the text to show the
    // full json. They can click it again to collapse the json.
    var para = document.createElement('p');
    para.textContent = String(json).substr(0, 100) + '...';
    para.setAttribute('title', 'click to expand');
    para.setAttribute('style', 'cursor: pointer;')
    para.setAttribute('data-json', json);
    para.addEventListener('click', function(e) {
        var json = e.target.getAttribute('data-json');

        if (e.target.textContent.length <= 103) {  // 100 + '...'
            e.target.textContent = json;
            para.setAttribute('title', 'click to collapse');
        }
        else {
            para.textContent = String(json).substr(0, 100) + '...';
            para.setAttribute('title', 'click to expand');
        }
    });
    div.appendChild(para); 

    var parsedJson = JSON.parse(json);

    // Loop through all the actions in the tag and display them.
    for (var j = 0; j < parsedJson.actions.length; j++) {
        var action = parsedJson.actions[j];

        var subSectionDiv = document.createElement('div');
        subSectionDiv.setAttribute('class', 'subSection');
        div.appendChild(subSectionDiv);

        var h4 = document.createElement('h4');
        h4.innerHTML = action.actionType;
        subSectionDiv.appendChild(h4);

        if (action.actionType === 'ImageResults') {
            addImageWithWebSearchUrl(subSectionDiv, parsedJson.image, action);
        }
        else if (action.actionType === 'DocumentLevelSuggestions') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'RelatedSearches') {
            addRelatedSearches(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'PagesIncluding') {
            addPagesIncluding(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'VisualSearch') {
            addRelatedImages(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'Recipes') {
            addRecipes(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'ShoppingSources') {
            addShopping(subSectionDiv, action.data.offers);
        }
        else if (action.actionType === 'ProductVisualSearch') {
            addProducts(subSectionDiv, action.data.value);
        }
        else if (action.actionType === 'TextResults') {
            addTextResult(subSectionDiv, action);
        }
        else if (action.actionType === 'Entity') {
            addEntity(subSectionDiv, action);
        }
    }
}
```

## <a name="display-insights-for-different-actions"></a>顯示不同動作的見解

下列函式可顯示不同動作的見解。 這些函式會提供可點選的影像或可點選的連結，帶您前往內含更多影像相關資訊的網頁。 此頁面裝載於 Bing.com 或影像的原始網站。 並非所有見解資料都會顯示在此應用程式中。 若要查看所有可用於見解的欄位，請參閱[影像 - 圖像式搜尋](https://aka.ms/bingvisualsearchreferencedoc)參考。

> [!NOTE]
> 您必須在頁面中顯示的見解資訊有最小數量。 如需詳細資訊，請參閱 [Bing 搜尋 API 的使用和顯示需求](../bing-web-search/use-display-requirements.md)。

### <a name="relatedimages-insights"></a>RelatedImages 見解

`addRelatedImages()` 函式會逐一查看 `RelatedImages` 動作清單，並將 `<img>` 標記附加至各自的外部 `<div>`，以針對每個裝載相關影像的網站建立標題：

```javascript
    function addRelatedImages(div, images) {
        var length = (images.length > 10) ? 10 : images.length;

        // Set the title to the website that hosts the image. The title displays
        // when the user hovers over the image.

        // Make the image clickable. If the user clicks the image, they're taken
        // to the image in Bing.com.

        for (var j = 0; j < length; j++) {
            var img = document.createElement('img');
            img.setAttribute('src', images[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('title', images[j].hostPageDisplayUrl);
            img.setAttribute('data-webSearchUrl', images[j].webSearchUrl)

            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })

            div.appendChild(img);
        }
    }
```

### <a name="pagesincluding-insights"></a>PagesIncluding 見解

`addPagesIncluding()` 函式會逐一查看 `PagesIncluding` 動作清單，並將 `<img>` 標記附加至各自的外部 `<div>`，以針對每個裝載已上傳影像的網站建立連結：

```javascript

    // Display links to the first 5 webpages that include the image.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addPagesIncluding(div, pages) {
        var length = (pages.length > 5) ? 5 : pages.length;

        for (var j = 0; j < length; j++) {
            var page = document.createElement('a');
            page.text = pages[j].name;
            page.setAttribute('href', pages[j].hostPageUrl);
            page.setAttribute('style', 'margin: 20px 20px 0 0');
            page.setAttribute('target', '_blank')
            div.appendChild(page);

            div.appendChild(document.createElement('br'));
        }
    }
```

### <a name="relatedsearches-insights"></a>RelatedSearches 見解

`addRelatedSearches()` 函式會逐一查看 `RelatedSearches` 動作清單，並將 `<img>` 標記附加至各自的外部 `<div>`，以針對裝載影像的網站建立連結：

```javascript

    // Display the first 10 related searches. Include a link with the image
    // that when clicked, takes the user to Bing.com and displays the 
    // related search results.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRelatedSearches(div, relatedSearches) {
        var length = (relatedSearches.length > 10) ? 10 : relatedSearches.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', relatedSearches[j].thumbnail.url + '&w=120&h=120');
            img.setAttribute('style', 'margin: 20px 20px 0 0;');
            childDiv.appendChild(img);

            var relatedSearch = document.createElement('a');
            relatedSearch.text = relatedSearches[j].displayText;
            relatedSearch.setAttribute('href', relatedSearches[j].webSearchUrl);
            relatedSearch.setAttribute('target', '_blank');
            childDiv.appendChild(relatedSearch);

        }
    }
```

### <a name="recipes-insights"></a>Recipes 見解

`addRecipes()` 函式會逐一查看 `Recipes` 動作清單，並將 `<img>` 標記附加至各自的外部 `<div>`，以針對每個傳回的食譜建立連結：

```javascript
    // Display links to the first 10 recipes. Include the recipe's rating,
    // if available.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addRecipes(div, recipes) {
        var length = (recipes.length > 10) ? 10 : recipes.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var recipe = document.createElement('a');
            recipe.text = recipes[j].name;
            recipe.setAttribute('href', recipes[j].url);
            recipe.setAttribute('style', 'margin: 20px 20px 0 0');
            recipe.setAttribute('target', '_blank')
            para.appendChild(recipe);

            if (recipes[j].hasOwnProperty('aggregateRating')) {
                var span = document.createElement('span');
                span.textContent = 'rating: ' + recipes[j].aggregateRating.text;
                para.appendChild(span);
            }

            div.appendChild(para);
        }
    }
```

### <a name="shopping-insights"></a>Shopping 見解

`addShopping()` 函式會逐一查看 `RelatedImages` 動作清單，並將 `<img>` 標記附加至各自的外部 `<div>`，以針對任何傳回的購物結果建立連結：

```javascript
    // Display links for the first 10 shopping offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addShopping(div, offers) {
        var length = (offers.length > 10) ? 10 : offers.length;

        for (var j = 0; j < length; j++) {
            var para = document.createElement('p');

            var offer = document.createElement('a');
            offer.text = offers[j].name;
            offer.setAttribute('href', offers[j].url);
            offer.setAttribute('style', 'margin: 20px 20px 0 0');
            offer.setAttribute('target', '_blank')
            para.appendChild(offer);

            var span = document.createElement('span');
            span.textContent = 'by ' + offers[j].seller.name + ' | ' + offers[j].price + ' ' + offers[j].priceCurrency;
            para.appendChild(span);

            div.appendChild(para);
        }
    }
```

### <a name="products-insights"></a>Products 見解

`addProducts()` 函式會逐一查看 `Products` 動作清單，並將 `<img>` 標記附加至各自的外部 `<div>`，以針對任何傳回的產品結果建立連結：

```javascript

    // Display the first 10 related products. Display a clickable image of the
    // product that takes the user to Bing.com search results for the product.
    // If there are any offers associated with the product, provide links to the offers.
    // TODO: Add 'more' link in case the user wants to see all of them.
    function addProducts(div, products) {
        var length = (products.length > 10) ? 10 : products.length;

        for (var j = 0; j < length; j++) {
            var childDiv = document.createElement('div');
            childDiv.setAttribute('class', 'stackLink');
            div.appendChild(childDiv);

            var img = document.createElement('img');
            img.setAttribute('src', products[j].thumbnailUrl + '&w=120&h=120');
            img.setAttribute('title', products[j].name);
            img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
            img.setAttribute('data-webSearchUrl', products[j].webSearchUrl)
            img.addEventListener('click', function(e) {
                var url = e.target.getAttribute('data-webSearchUrl');
                window.open(url, 'foo');
            })
            childDiv.appendChild(img);

            if (products[j].insightsMetadata.hasOwnProperty('aggregateOffer')) {
                if (products[j].insightsMetadata.aggregateOffer.offerCount > 0) {
                    var offers = products[j].insightsMetadata.aggregateOffer.offers;

                    // Show all the offers. Not all markets provide links to offers.
                    for (var i = 0; i < offers.length; i++) {  
                        var para = document.createElement('p');

                        var offer = document.createElement('a');
                        offer.text = offers[i].name;
                        offer.setAttribute('href', offers[i].url);
                        offer.setAttribute('style', 'margin: 20px 20px 0 0');
                        offer.setAttribute('target', '_blank')
                        para.appendChild(offer);

                        var span = document.createElement('span');
                        span.textContent = 'by ' + offers[i].seller.name + ' | ' + offers[i].price + ' ' + offers[i].priceCurrency;
                        para.appendChild(span);

                        childDiv.appendChild(para);
                    }
                }
                else {  // Otherwise, just show the lowest price that Bing found.
                    var offer = products[j].insightsMetadata.aggregateOffer;

                    var para = document.createElement('p');
                    para.textContent = `${offer.name} | ${offer.lowPrice} ${offer.priceCurrency}`; 

                    childDiv.appendChild(para);
                }
            }
        }
    }
```

### <a name="textresult-insights"></a>TextResult 見解

`addTextResult()` 函式會顯示影像中任何可辨識的文字：

```javascript

    function addTextResult(div, action) {
        var text = document.createElement('p');
        text.textContent = action.displayName;
        div.appendChild(text);
    }
```

`addEntity()` 函式會顯示可將使用者帶往 Bing.com 的連結，以取得影像中實體類型的相關詳細資訊 (如果偵測到任何實體的話)：

```javascript
    // If the image is of a person, the tag might include an entity
    // action type. Display a link that takes the user to Bing.com
    // where they can get details about the entity.
    function addEntity(div, action) {
        var entity = document.createElement('a');
        entity.text = action.displayName;
        entity.setAttribute('href', action.webSearchUrl);
        entity.setAttribute('style', 'margin: 20px 20px 0 0');
        entity.setAttribute('target', '_blank');
        div.appendChild(entity);
    }
```

`addImageWithWebSearchUrl()` 函式會顯示 `<div>` 可點選的影像，以將使用者帶往 Bing.com 上的搜尋結果：

```javascript
    function addImageWithWebSearchUrl(div, image, action) {
        var img = document.createElement('img');
        img.setAttribute('src', image.thumbnailUrl + '&w=120&h=120');
        img.setAttribute('style', 'margin: 20px 20px 0 0; cursor: pointer;');
        img.setAttribute('data-webSearchUrl', action.webSearchUrl);
        img.addEventListener('click', function(e) {
            var url = e.target.getAttribute('data-webSearchUrl');
            window.open(url, 'foo');
        })
        div.appendChild(img);
    }

```

## <a name="add-a-css-style"></a>新增 CSS 樣式

將下列 `<style>` 區段新增至 `<head>` 標記，以組織網頁的版面配置：

```html
        <style>

            .thumb {
                height: 75px;
                border: 1px solid #000;
            }

            .stackLink {
                width:180px;
                min-height:210px;
                display:inline-block;
            }
            .stackLink a {
                float:left;
                clear:left;
            }

            .section {
                float:left;
                display:none;
            }

            .subSection {
                clear:left;
                float:left;
            }

        </style>
```

## <a name="next-steps"></a>後續步驟

>[!div class="nextstepaction"]
> [教學課程：使用 ImageInsightsToken 尋找先前搜尋中的類似影像](./tutorial-visual-search-insights-token.md)
