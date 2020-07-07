---
title: 開始使用 web 地圖控制項 |Microsoft Azure 對應
description: 瞭解如何使用 Microsoft Azure 地圖的地圖控制項用戶端 JavaScript 程式庫，將地圖和內嵌的 Azure 地圖服務功能轉譯成您的 web 或行動應用程式。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335240"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure 地圖服務的地圖控制項

地圖控制項用戶端 JavaScript 程式庫可讓您將地圖和內嵌 Azure 地圖服務功能轉譯到 web 或行動應用程式中。

## <a name="create-a-new-map-in-a-web-page"></a>在網頁中建立新的地圖

您可以使用地圖控制項的用戶端 JavaScript 程式庫，在網頁中內嵌對應。

1. 建立新的 HTML 檔案。

2. 載入 Azure 地圖服務 Web SDK。 您可以選擇兩個選項的其中一個：

    * 藉由在 HTML 檔案的元素中新增 JavaScript 和樣式表單的參考，使用 Azure 地圖服務 Web SDK 的全域裝載 CDN 版本 `<head>` ：

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * 使用[Azure 地圖服務控制](https://www.npmjs.com/package/azure-maps-control)NPM 套件在本機載入 AZURE 地圖服務 Web SDK 原始程式碼，並將其與您的應用程式搭配使用。 此套件也包含 TypeScript 定義。

        > **npm 安裝 azure-maps-control**

       然後將參考新增到 Azure 地圖服務樣式表，並將指令碼來源參考新增到檔案的 `<head>` 元素：

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > 您可以藉由新增下列程式碼，將 Typescript 定義匯入至您的應用程式：
    >
    > ```Javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. 若要轉譯地圖來使它填滿頁面的完整主體，請將下列 `<style>` 元素新增到 `<head>` 元素。

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. 在頁面的主體中，新增 `<div>` 元素，並為它提供 **myMap** 的 `id`。

   ```HTML
    <body>
        <div id="myMap"></div>
    </body>
   ```

5. 若要初始化地圖控制項，請在 html 主體中定義新的腳本標記。 `id` `<div>` 建立類別的實例時，傳入對應或的（例如 `HTMLElement` ， `document.getElementById('myMap')` ）作為第一個參數 `Map` 。 使用您自己的 Azure 地圖服務帳戶金鑰或 Azure Active Directory (AAD) 認證來使用[驗證選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)驗證地圖。 

   如果您需要建立帳戶或尋找您的金鑰，請依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)和[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的指示進行。 

   [語言]**** 選項會指定要用於地圖標籤和控制項的語言。 如需支援語言的詳細資訊，請參閱[支援的語言](supported-languages.md)。如果您使用訂用帳戶金鑰進行驗證，請使用下列各項：

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

   如果您使用 Azure Active Directory （AAD）進行驗證，請使用下列各項：

   ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: '<Your AAD Tenant Id>'
            }
        });
    </script>
   ```

   您可以在[這裡](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)找到示範如何整合 AZURE ACTIVE DIRECTORY （AAD）與 Azure 地圖服務的範例清單。 
    
   如需詳細資訊，請參閱[使用 Azure 地圖服務的驗證](azure-maps-authentication.md)檔和[Azure 地圖服務 Azure AD 驗證範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)。

6. 除此之外，將下列中繼標籤元素新增到頁面的標頭可能會對您很有幫助：

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. 將它全部放在您的 HTML 檔案中看起來應該像下面這段程式碼：

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. 在網頁瀏覽器中開啟此檔案，並檢視轉譯的地圖。 看起來應該如下圖所示：

   ![顯示呈現結果的地圖影像](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>將地圖當地語系化

Azure 地圖服務提供兩種不同的方式來設定轉譯地圖的語言和地區視圖。 第一個選項是將這項資訊新增至全域 `atlas` 命名空間，這會導致應用程式中的所有地圖控制項實例預設為這些設定。 以下會將語言設定為法文（"fr-fr"），並將地區視圖設為「自動」：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

第二個選項是在載入對應時，將此資訊傳入對應選項，如下所示：

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!Note]
> 使用 Web SDK，可以使用不同的語言和區域設定，在相同的頁面上載入多個對應實例。 此外，您可以在使用對應的函式載入對應之後，更新這些設定 `setStyle` 。 

以下是語言設為 "fr-fr" 且地區視圖設為 "Auto" 的 Azure 地圖服務範例。

![以法文顯示標籤的地圖影像](./media/how-to-use-map-control/websdk-localization.png)

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="azure-government-cloud-support"></a>Azure Government 雲端支援

Azure 地圖服務 Web SDK 支援 Azure Government 雲端。 所有用來存取 Azure 地圖服務 Web SDK 的 JavaScript 和 CSS Url 都會保持不變。 您必須完成下列工作，才能連接到 Azure 地圖服務平臺的 Azure Government cloud 版本。

使用互動式地圖控制項時，請在建立類別的實例之前，加入下列程式程式碼 `Map` 。 

```javascript
atlas.setDomain('atlas.azure.us');
```

驗證對應和服務時，請務必使用來自 Azure Government 雲端平臺的 Azure 地圖服務驗證詳細資料。

使用服務模組時，必須在建立 API URL 端點的實例時設定服務的網域。 例如，下列程式碼會建立類別的實例 `SearchURL` ，並將該網域指向 Azure Government 雲端。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

如果直接存取 Azure 地圖服務 REST 服務，請將 URL 網域變更為 `atlas.azure.us` 。 例如，如果使用搜尋 API 服務，請將 URL 網域從變更 `https://atlas.microsoft.com/search/` 為 `https://atlas.azure.us/search/` 。

## <a name="next-steps"></a>後續步驟

了解如何建立地圖並與其互動：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

了解如何設定地圖的樣式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](choose-map-style.md)

若要將更多資料新增至您的對應：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)

如需顯示如何整合 Azure Active Directory （AAD）與 Azure 地圖服務的範例清單，請參閱：

> [!div class="nextstepaction"]
> [Azure AD 驗證範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
