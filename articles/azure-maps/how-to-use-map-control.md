---
title: 開始使用 Web 地圖控制項 |微軟 Azure 地圖
description: 瞭解如何使用 Microsoft Azure 地圖映射控制用戶端 JavaScript 庫，將地圖和嵌入 Azure 地圖功能呈現到 Web 或移動應用程式中。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 6becb504671c1fa380207fda9d7d553fca8ceddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335240"
---
# <a name="use-the-azure-maps-map-control"></a>使用 Azure 地圖映射控制項

地圖控制項用戶端 JavaScript 庫允許您在 Web 或移動應用程式中呈現地圖和嵌入 Azure 地圖功能。

## <a name="create-a-new-map-in-a-web-page"></a>在網頁中建立新的地圖

您可以使用地圖控制項用戶端 JavaScript 庫將地圖嵌入網頁中。

1. 建立新的 HTML 檔案。

2. 載入 Azure 地圖服務 Web SDK。 您可以選擇兩個選項之一;您可以選擇兩個選項之一。

    * 通過在 HTML 檔案`<head>`元素中添加對 JavaScript 和樣式表的引用，使用全域託管的 Azure 地圖 Web SDK 版本：

        ```HTML
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        ```

    * 使用[Azure 映射控制項](https://www.npmjs.com/package/azure-maps-control)NPM 包在本地載入 Azure 映射 Web SDK 原始程式碼，並將其與應用一起託管。 此套件也包含 TypeScript 定義。

        > **npm 安裝 azure-maps-control**

       然後將參考新增到 Azure 地圖服務樣式表，並將指令碼來源參考新增到檔案的 `<head>` 元素：

        ```HTML
        <link rel="stylesheet" href="node_modules/azure-maps-control/dist/atlas.min.css" type="text/css"> 
        <script src="node_modules/azure-maps-control/dist/atlas.min.js"></script>
        ```

    > [!Note]
    > 可以通過添加以下代碼將類型腳本定義導入到應用程式中：
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

5. 要初始化地圖控制項，請在 html 正文中定義一個新的腳本標記。 在創建`Map`類`id`的實例時`<div>`，將`HTMLElement`映射或 （`document.getElementById('myMap')`例如 ）， 作為第一個參數傳遞。 使用您自己的 Azure 地圖服務帳戶金鑰或 Azure Active Directory (AAD) 認證來使用[驗證選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions)驗證地圖。 

   如果需要創建帳戶或查找金鑰，請按照["創建帳戶"中的](quick-demo-map-app.md#create-an-account-with-azure-maps)說明[獲取主金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 

   [語言]**** 選項會指定要用於地圖標籤和控制項的語言。 有關受支援語言的詳細資訊，請參閱[支援的語言](supported-languages.md)。如果使用訂閱金鑰進行身份驗證，請使用以下操作：

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

   如果使用 Azure 活動目錄 （AAD） 進行身份驗證，請使用以下操作：

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

   此處列出了如何將 Azure 活動目錄 （AAD） 與 Azure 映射集成[的示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)清單。 
    
   有關詳細資訊，請參閱 Azure[映射](azure-maps-authentication.md)文檔和 Azure 映射[Azure AD 身份驗證示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)。

6. 除此之外，將下列中繼標籤元素新增到頁面的標頭可能會對您很有幫助：

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. 將其全部放在一起的 HTML 檔案應類似于以下代碼：

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

8. 在網頁瀏覽器中開啟此檔案，並檢視轉譯的地圖。 它應類似于下圖：

   ![顯示渲染結果的地圖圖像](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>將地圖當地語系化

Azure 地圖提供了兩種不同的方法來設置渲染地圖的語言和區域視圖。 第一個選項是將此資訊添加到全域`atlas`命名空間，這將導致應用中的所有地圖控制實例預設為這些設置。 下面將語言設置為法語（"fr-FR"），區域視圖為"自動"：

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

第二個選項是在載入地圖時將此資訊傳遞到地圖選項中，如下所示：

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
> 使用 Web SDK，可以在同一頁上載入具有不同語言和地區設定的多個映射實例。 此外，這些設置可以在地圖載入後使用`setStyle`地圖的功能進行更新。 

下面是 Azure 映射的示例，其語言設置為"fr-FR"，區域視圖設置為"自動"。

![以法語顯示標籤的地圖圖像](./media/how-to-use-map-control/websdk-localization.png)

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="azure-government-cloud-support"></a>Azure 政府雲支援

Azure 地圖 Web SDK 支援 Azure 政府雲。 用於訪問 Azure 地圖 Web SDK 的所有 JavaScript 和 CSS URL 保持不變。 連接到 Azure 地圖平臺的 Azure 政府雲版本需要執行以下任務。

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

了解如何建立地圖並與其互動：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

了解如何設定地圖的樣式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](choose-map-style.md)

要向地圖添加更多資料，請進行：

> [!div class="nextstepaction"]
> [建立地圖](map-create.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)

有關如何將 Azure 活動目錄 （AAD） 與 Azure 映射集成的示例清單，請參閱：

> [!div class="nextstepaction"]
> [Azure AD 身份驗證示例](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
