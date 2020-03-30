---
title: 使用 Azure 地圖服務模組 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Azure 地圖服務模組使用 Microsoft Azure 地圖 REST 服務。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: e985fdda4638529e8ade2c700456d595ff355e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988731"
---
# <a name="use-the-azure-maps-services-module"></a>使用 Azure 地圖服務模組

Azure 地圖 Web SDK 提供*服務模組*。 此模組是一個説明庫，通過使用 JavaScript 或 TypeScript，可以輕鬆地在 Web 或 Node.js 應用程式中使用 Azure 映射 REST 服務。

## <a name="use-the-services-module-in-a-webpage"></a>在網頁中使用服務模組

1. 建立新的 HTML 檔案。
1. 載入 Azure 映射服務模組。 您可以通過兩種方式之一載入它：
    - 使用 Azure 地圖服務模組的全域託管 Azure 內容傳遞網路版本。 添加對檔元素的`<head>`腳本引用：

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
        ```

    - 或者，使用[Azure 地圖-rest](https://www.npmjs.com/package/azure-maps-rest) npm 包在本地載入 Azure 地圖 Web SDK 原始程式碼的服務模組，然後將其與應用託管。 此套件也包含 TypeScript 定義。 使用此命令：
    
        > **npm install azure-maps-rest**
    
        然後，添加對檔元素的`<head>`腳本引用：

         ```html
        <script src="node_modules/azure-maps-rest/dist/atlas-service.min.js"></script>
         ```

1. 創建身份驗證管道。 必須先創建管道，然後才能初始化服務 URL 用戶端終結點。 使用自己的 Azure 地圖帳戶金鑰或 Azure 活動目錄 （Azure AD） 憑據對 Azure 地圖搜索服務用戶端進行身份驗證。 在此示例中，將創建搜索服務 URL 用戶端。 

    如果使用訂閱金鑰進行身份驗證：

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

    如果使用 Azure AD 進行身份驗證：

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

    有關詳細資訊，請參閱使用[Azure 映射進行身份驗證](azure-maps-authentication.md)。

1. 以下代碼使用新創建的 Azure 地圖搜索服務 URL 用戶端對位址進行地理編碼："1 微軟方式，雷德蒙德，華盛頓州"。 代碼使用 函數`searchAddress`並將結果顯示為頁面正文中的表。

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

    下面是完整的正在運行的代碼示例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="使用服務模組" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （）<a href='https://codepen.io/azuremaps/pen/zbXGMR/'>使用服務模組</a>的筆。
</iframe>

<br/>

## <a name="azure-government-cloud-support"></a>Azure 政府雲支援

Azure 地圖 Web SDK 支援 Azure 政府雲。 用於訪問 Azure 地圖 Web SDK 的所有 JavaScript 和 CSS URL 保持不變，但需要執行以下任務才能連接到 Azure 地圖平臺的 Azure 政府雲版本。

使用互動式地圖控制項時，在創建`Map`類的實例之前添加以下程式碼。 

```javascript
atlas.setDomain('atlas.azure.us');
```

在驗證地圖和服務時，請確保使用 Azure 政府雲平臺中的 Azure 映射身份驗證詳細資訊。

使用服務模組時，需要在創建 API URL 終結點的實例時設置服務的域。 例如，以下代碼創建類的`SearchURL`實例，並將域指向 Azure 政府雲。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

如果直接存取 Azure 映射 REST 服務，則將`atlas.azure.us`URL 域更改為 。 例如，如果使用搜索 API 服務，則將 URL 域`https://atlas.microsoft.com/search/`從`https://atlas.azure.us/search/`更改為 。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [搜索URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [路由URL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [訂閱金鑰委付](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [權杖憑據](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

有關使用服務模組的更多代碼示例，請參閱以下文章：

> [!div class="nextstepaction"]
> [在地圖上顯示搜尋結果](./map-search-location.md)

> [!div class="nextstepaction"]
> [從座標取得資訊](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [顯示從甲地到乙地的指示](./map-route.md)
