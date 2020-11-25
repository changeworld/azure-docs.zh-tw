---
title: 使用 Azure 地圖服務服務模組 |Microsoft Azure 對應
description: 瞭解 Azure 地圖服務服務模組。 瞭解如何載入和使用此協助程式程式庫，以存取 web 或 Node.js 應用程式中 Azure 地圖服務 REST 服務。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: devx-track-js
ms.openlocfilehash: 2e07b614e87ed5dad94cf9bc5994e78071187839
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008594"
---
# <a name="use-the-azure-maps-services-module"></a>使用 Azure 地圖服務服務模組

Azure 地圖服務 Web SDK 提供 *服務模組*。 此課程模組是協助程式程式庫，可讓您輕鬆地在 web 或 Node.js 應用程式中使用 JavaScript 或 TypeScript 來使用 Azure 地圖服務 REST 服務。

## <a name="use-the-services-module-in-a-webpage"></a>使用網頁中的服務模組

1. 建立新的 HTML 檔案。
1. 載入 Azure 地圖服務服務模組。 您可以透過下列兩種方式之一載入它：
    - 使用 Azure 地圖服務服務模組的全球託管 Azure 內容傳遞網路版本。 將腳本參考新增至檔案的 `<head>` 元素：

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - 或者，使用 [Azure 地圖服務-rest](https://www.npmjs.com/package/azure-maps-rest) npm 套件在本機載入 AZURE 地圖服務 Web SDK 原始程式碼的服務模組，然後將它裝載在您的應用程式中。 此套件也包含 TypeScript 定義。 使用此命令：
    
        > **npm install azure-maps-rest**
    
        然後，將腳本參考新增至檔案的 `<head>` 元素：

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. 建立驗證管線。 您必須先建立管線，才能初始化服務 URL 用戶端端點。 使用您自己的 Azure 地圖服務帳戶金鑰或 Azure Active Directory (Azure AD) 認證來驗證 Azure 地圖服務搜尋服務用戶端。 在此範例中，將會建立搜尋服務 URL 用戶端。 

    如果您使用訂用帳戶金鑰進行驗證：

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    如果您使用 Azure AD 進行驗證：

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
      try {
        console.log("Renewing token");
        var token = await getAadToken();
        tokenCredential.token = token;
        tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
      } catch (error) {
        console.log("Caught error when renewing token");
        clearTimeout(tokenRenewalTimer);
        throw error;
      }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
      // Use the signed-in auth context to get a token.
      return new Promise((resolve, reject) => {
        // The resource should always be https://atlas.microsoft.com/.
        const resource = "https://atlas.microsoft.com/";
        authContext.acquireToken(resource, (error, token) => {
          if (error) {
            reject(error);
          } else {
            resolve(token);
          }
        });
      })
    }

    function getExpiration(jwtToken) {
      // Decode the JSON Web Token (JWT) to get the expiration time stamp.
      const json = atob(jwtToken.split(".")[1]);
      const decode = JSON.parse(json);

      // Return the milliseconds remaining until the token must be renewed.
      // Reduce the time until renewal by 5 minutes to avoid using an expired token.
      // The exp property is the time stamp of the expiration, in seconds.
      const renewSkew = 300000;
      return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    如需詳細資訊，請參閱 [使用 Azure 地圖服務進行驗證](azure-maps-authentication.md)。

1. 下列程式碼會使用新建立的 Azure 地圖服務搜尋服務 URL 用戶端來地理編碼位址：「1個 Microsoft 的方式，Redmond，WA」。 程式碼會使用 `searchAddress` 函數，並將結果顯示為頁面主體中的資料表。

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa')
      .then(response => {
        var html = [];

        // Display the total results.
        html.push('Total results: ', response.summary.numResults, '<br/><br/>');

        // Create a table of the results.
        html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

        for(var i=0;i<response.results.length;i++){
          html.push('<tr><td>', (i+1), '.</td><td>', 
            response.results[i].address.freeformAddress, 
            '</td><td>', 
            response.results[i].position.lat,
            '</td><td>', 
            response.results[i].position.lon,
            '</td></tr>');
        }

        html.push('</table>');

        // Add the resulting HTML to the body of the page.
        document.body.innerHTML = html.join('');
    });
    ```

    以下是完整的程式碼範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用服務模組" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上<a href='https://codepen.io/azuremaps/pen/zbXGMR/'>使用 [服務] 模組</a>Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 來<a href='https://codepen.io'>CodePen</a>查看畫筆。
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure Government 雲端支援

Azure 地圖服務 Web SDK 支援 Azure Government 雲端。 用來存取 Azure 地圖服務 Web SDK 的所有 JavaScript 和 CSS Url 都會保持不變，不過，您必須完成下列工作，才能連接到 Azure Government 的 Azure 地圖服務平臺雲端版本。

使用互動式地圖控制項時，請在建立類別的實例之前，加入下列程式程式碼 `Map` 。 

```javascript
atlas.setDomain('atlas.azure.us');
```

驗證對應和服務時，請務必使用 Azure Government 雲端平臺的 Azure 地圖服務驗證詳細資料。

使用服務模組時，您必須在建立 API URL 端點的實例時設定服務的網域。 例如，下列程式碼會建立類別的實例 `SearchURL` ，並將網域指向 Azure Government 雲端。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

如果直接存取 Azure 地圖服務 REST 服務，請將 URL 網域變更為 `atlas.azure.us` 。 例如，如果使用搜尋 API 服務，請將 URL 網域從變更 `https://atlas.microsoft.com/search/` 為 `https://atlas.azure.us/search/` 。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [MapsURL](/javascript/api/azure-maps-rest/atlas.service.mapsurl)

> [!div class="nextstepaction"]
> [SearchURL](/javascript/api/azure-maps-rest/atlas.service.searchurl)

> [!div class="nextstepaction"]
> [RouteURL](/javascript/api/azure-maps-rest/atlas.service.routeurl)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential)

> [!div class="nextstepaction"]
> [TokenCredential](/javascript/api/azure-maps-rest/atlas.service.tokencredential)

如需使用服務模組的更多程式碼範例，請參閱下列文章：

> [!div class="nextstepaction"]
> [在地圖上顯示搜尋結果](./map-search-location.md)

> [!div class="nextstepaction"]
> [從座標取得資訊](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)