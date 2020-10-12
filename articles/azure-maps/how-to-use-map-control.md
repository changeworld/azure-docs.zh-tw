---
title: 開始使用 Microsoft Azure Maps web 地圖控制項
description: 瞭解如何使用 Azure 地圖服務中的地圖控制項用戶端 JavaScript 程式庫，將地圖新增至 web 和行動應用程式。 瞭解如何當地語系化地圖。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 19db4fb0d75369a0f272eef1180e86f47d45d284
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335325"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure 地圖服務的地圖控制項

地圖控制項用戶端 JavaScript 程式庫可讓您在 web 或行動應用程式中呈現地圖和內嵌 Azure 地圖服務功能。

## <a name="prerequisites"></a>必要條件

若要在網頁中使用地圖控制項，您必須具備下列其中一個必要條件：

* [製作 Azure 地圖服務的帳戶](quick-demo-map-app.md#create-an-azure-maps-account) ，並 [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。

* 使用 [驗證選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)取得 AZURE ACTIVE DIRECTORY (AAD) 認證。

## <a name="create-a-new-map-in-a-web-page"></a>在網頁中建立新的地圖

您可以使用地圖控制項用戶端 JavaScript 程式庫，在網頁中內嵌地圖。

1. 建立新的 HTML 檔案。

2. 載入 Azure 地圖服務 Web SDK。 您有兩個選項可選擇：

    * 藉由在 HTML 檔案的元素中加入 JavaScript 和樣式表單的參考，以使用 Azure 地圖服務 Web SDK 的全域裝載 CDN 版本 `<head>` ：

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * 使用 [Azure 地圖服務控制](https://www.npmjs.com/package/azure-maps-control) NPM 套件在本機載入 AZURE 地圖服務 Web SDK 原始程式碼，並將其裝載在您的應用程式中。 此套件也包含 TypeScript 定義。

      > **npm 安裝 azure-maps-control**

    然後，將 Azure 地圖服務樣式表單的參考新增至檔案的 `<head>` 元素：

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > 您可以藉由新增下列程式碼，將 Typescript 定義匯入至應用程式：
    >
    > ```javascript
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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. 現在，我們將初始化地圖控制項。 若要驗證此控制項，您必須擁有 Azure 地圖服務的訂用帳戶金鑰，或透過 [驗證選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)使用 AZURE ACTIVE DIRECTORY (AAD) 認證。

    如果您要使用訂用帳戶金鑰進行驗證，請複製下列腳本專案並貼到專案中 `<head>` ，並貼到第一個專案的下方 `<script>` 。 `<Your Azure Maps Key>`將取代為您的 Azure 地圖服務主要訂用帳戶金鑰。

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            }
        });
    </script>
    ```

    如果您使用 Azure Active Directory (AAD) 進行驗證，請複製下列腳本專案，並將其貼到專案中 `<head>` ，並貼在第一個元素的下方 `<script>` 。

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
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
            }
        });
    </script>
   ```

    如需有關使用 Azure 地圖服務進行驗證的詳細資訊，請參閱 Azure 地圖服務檔的 [驗證](azure-maps-authentication.md) 。 此外，您可以在 [這裡](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)找到範例清單，其中示範如何整合 AZURE ACTIVE DIRECTORY (AAD) 與 Azure 地圖服務。

    >[!TIP]
    >在此範例中，我們已傳入 `id` 地圖的 `<div>` 。 另一種方法是傳遞做 `HTMLElement` `document.getElementById('myMap')` 為第一個參數的物件。

6. （選擇性）您可能會發現，將下列元素新增至頁面的專案會很有説明 `meta` `head` ：

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. 總而言之，您的 HTML 檔案看起來應該像下面這類標記：

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


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

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
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. 在網頁瀏覽器中開啟此檔案，並檢視轉譯的地圖。 它看起來應該如下圖所示：

   ![顯示呈現結果的地圖影像](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>將地圖當地語系化

Azure 地圖服務提供兩種不同的方式來設定轉譯地圖的語言和區域查看。 第一個選項是將這項資訊新增至全域 `atlas` 命名空間，這會導致應用程式中的所有地圖控制項實例預設為這些設定。 以下將語言設為法文 ( "fr-fr" ) ，並將區域視圖設定為 "Auto"：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

第二個選項是在載入地圖時，將此資訊傳遞至對應選項，如下所示：

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

> [!NOTE]
> 您可以在相同頁面上，使用不同的語言和區域設定載入多個地圖實例。 此外，您可以在地圖使用地圖的函式載入之後，更新這些設定 `setStyle` 。

以下範例說明將語言設定為 "fr-fr" 的 Azure 地圖服務，並將區域視圖設定為 "Auto"。

![顯示法文標籤的地圖影像](./media/how-to-use-map-control/websdk-localization.png)

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="azure-government-cloud-support"></a>Azure Government 雲端支援

Azure 地圖服務 Web SDK 支援 Azure Government 雲端。 用來存取 Azure 地圖服務 Web SDK 的所有 JavaScript 和 CSS Url 都保持不變。 您必須完成下列工作，才能連接到 Azure Government cloud 版本的 Azure 地圖服務平臺。

使用互動式地圖控制項時，請在建立類別的實例之前，加入下列程式程式碼 `Map` 。

```javascript
atlas.setDomain('atlas.azure.us');
```

驗證對應和服務時，請務必使用 Azure Government 雲端平臺 Azure 地圖服務的驗證詳細資料。

使用服務模組時，您必須在建立 API URL 端點的實例時設定服務的網域。 例如，下列程式碼會建立類別的實例 `SearchURL` ，並將網域指向 Azure Government 雲端。

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

如果直接存取 Azure 地圖服務 REST 服務，請將 URL 網域變更為 `atlas.azure.us` 。 例如，如果使用搜尋 API 服務，請將 URL 網域從變更 `https://atlas.microsoft.com/search/` 為 `https://atlas.azure.us/search/` 。

## <a name="javascript-frameworks"></a>JavaScript 架構

如果使用 JavaScript 架構進行開發，下列其中一個開放原始碼專案可能會很有用：

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps) - Azure 地圖服務的 Angular 10 包裝函式。
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components) - Azure 地圖服務 Blazor 元件。
- [Azure 地圖服務 React 元件](https://github.com/WiredSolutions/react-azure-maps) - Azure 地圖服務控制項的反應包裝函式。
- [Vue Azure 地圖服務](https://github.com/rickyruiz/vue-azure-maps) - Vue 應用程式的 Azure 地圖服務元件。

## <a name="next-steps"></a>後續步驟

了解如何建立地圖並與其互動：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

了解如何設定地圖的樣式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](choose-map-style.md)

若要將更多資料新增至對應：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)

如需顯示如何整合 Azure Active Directory (AAD) 與 Azure 地圖服務的範例清單，請參閱：

> [!div class="nextstepaction"]
> [Azure AD 驗證範例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
