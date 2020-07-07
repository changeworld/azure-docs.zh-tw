---
title: 使用 Azure 地圖服務搜尋服務來搜尋位置 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 [地理編碼] 和 [反向地理編碼] 的 [Microsoft Azure 對應] 搜尋服務來搜尋位置。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335438"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>使用 Azure 地圖服務搜尋服務搜尋位置

Azure 地圖服務[搜尋服務](https://docs.microsoft.com/rest/api/maps/search)是一組 RESTful api，旨在協助開發人員搜尋位址、位置、依名稱或類別的商務清單，以及其他地理資訊。 除了支援傳統地理編碼，服務也可以根據緯度和經度來反轉地理編碼位址和交叉街道。 搜尋所傳回的緯度和經度值可用來做為其他 Azure 地圖服務服務（例如[路線](https://docs.microsoft.com/rest/api/maps/route)和[氣象](https://docs.microsoft.com/rest/api/maps/weather)服務）中的參數。

在本文中，您將瞭解如何：

* 使用[搜尋位址 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)來要求位址（地理編碼位址位置）的緯度和經度座標
* 使用[模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)搜尋位址或感對的點（POI）
* 搜尋位址以及屬性和座標
* 進行[反向位址搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，將座標位置轉譯為街道位址
* 使用[搜尋位址反向交叉街道 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)搜尋交叉街道

## <a name="prerequisites"></a>必要條件

若要完成本文中的步驟，您必須先建立 Azure 地圖服務帳戶，並取得地圖服務帳戶金鑰。 依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的指示來建立 Azure 地圖服務帳戶訂用帳戶，並遵循[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟來取得您帳戶的主要金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>要求位址的緯度和經度（地理編碼）

在此範例中，我們會使用 Azure 地圖服務[取得搜尋位址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) ，將街道位址轉換成緯度和經度座標。 您可以將完整或部分街道位址傳遞給 API，並接收包含詳細位址屬性的回應，例如街道、郵遞區號和國家/地區，以及緯度和經度中的位置值。

如果您有一組要地理編碼的位址，您可以在單一 API 呼叫中使用[Post 搜尋位址 BATCH API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch)來傳送查詢批次。

1. 在 Postman 中，按一下 [**新增要求**] [  |  **取得要求**]，並將其命名為「**位址搜尋**

2. 在 [建立器] 索引標籤上，選取 [取得]**** HTTP 方法、輸入您 API 端點的要求 URL，然後選取授權通訊協定 (如果有的話)。

![地址搜尋](./media/how-to-search-for-address/address_search_url.png)

| 參數 | 建議的值 |
|---------------|------------------------------------------------| 
| HTTP method | GET |
| 要求 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| 授權 | 無授權 |

3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數： 

![地址搜尋](./media/how-to-search-for-address/address_search_params.png) 

| 機碼 | 值 | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<your Azure Maps key\> | 
| 查詢 | 400 Broad St, Seattle, WA 98109 | 

4. 按一下 [傳送]****，然後檢視回應本文。 

在此情況下，您指定了完整的地址查詢，並在回應主體中接收單一結果。 

5. 在 Params 中，將查詢字串編輯為下列值： 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****： 

| 機碼 | 值 | 
|-----|------------| 
| typeahead | true | 

**typeahead** 旗標會指示地址搜尋 API 將查詢視為部分輸入，並傳回預測值的陣列。

## <a name="using-fuzzy-search-api"></a>使用模糊搜尋 API

當您不知道使用者輸入的搜尋查詢內容時，建議使用 Azure 地圖服務[模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 此 API 會將相關的點（POI）搜尋和地理編碼結合成標準的「單行搜尋」。 例如，該 API 可以處理任何地址或 POI 權杖之組合的輸入。 API 也可與內容相關的位置進行加權 (lat./lon. pair)，完全受到座標和半徑所限制，或可以更廣泛地執行，不受任何地理偏差錨點影響。

大部分的搜尋查詢預設為 `maxFuzzyLevel=1` 以取得效能並減少不尋常的結果。 可以視需要依據要求覆寫此預設值，方法是傳入查詢參數 `maxFuzzyLevel=2` 或 `3`。

### <a name="search-for-an-address-using-fuzzy-search"></a>使用模糊搜尋來搜尋地址

1. 開啟 Postman 應用程式，按一下 [新增] | [新建]，然後選取 [取得要求]****。 輸入**模糊搜尋**的要求名稱，選取集合或資料夾來儲存它，然後按一下 [儲存]****。

2. 在 [建立器] 索引標籤上，選取 [取得]**** HTTP 方法，然後輸入您 API 端點的要求 URL。

    ![模糊搜尋](./media/how-to-search-for-address/fuzzy_search_url.png)

    | 參數 | 建議的值 |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | 授權 | 無授權 |

    URL 路徑中的 **Json** 屬性會判斷回應格式。 本文使用 json 來方便使用和可讀性。 您可以在[地圖服務功能性 API 參考](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)的＜取得搜尋模糊＞**** 定義中找到可用的回應格式。

3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數：

    ![模糊搜尋](./media/how-to-search-for-address/fuzzy_search_params.png)

    | 機碼 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | 查詢 | 披薩 |

4. 按一下 [傳送]****，然後檢視回應本文。

    「比薩餅」的不明確查詢字串會在「比薩餅」和「餐廳」類別中傳回10個[感興趣的結果](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse)（POI）。 每個結果都會傳回位置的街道位址、緯度和經度值、視圖埠和進入點。
  
    此查詢中的結果會有所不同，未繫結至任何特定的參考位置。 您可以使用**countrySet**參數來指定您的應用程式需要涵蓋範圍的國家/地區。 預設行為是搜尋整個世界，可能會傳回不必要的結果。

5. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | 機碼 | 值 |
    |------------------|-------------------------|
    | countrySet | 美國 |
  
    結果現在會依國家/地區程式碼繫結，此查詢會傳回美國境內的披薩餐廳。
  
    若要提供某個位置的結果，您可以查詢景點，並使用針對模糊搜尋服務之呼叫中所傳回的緯度和經度值。 在此情況下，您使用搜尋服務來傳回「西雅圖太空針塔」的位置，並使用 lat. / lon. 值來導向搜尋。
  
6. 在 Params 中，輸入下列金鑰 / 值組，並按一下 [傳送]****：

    ![模糊搜尋](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | 機碼 | 值 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>使用反向地址搜尋來搜尋街道地址

Azure 地圖服務[取得搜尋位址反向 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)可協助將座標（範例：37.786505、-122.3862）轉譯為人類易懂的街道位址。 最常見的情況是追蹤應用程式，您會從裝置或資產接收 GPS 摘要，並想要知道座標所在的位址。
如果您有一組要反向地理編碼的座標位置，您可以在單一 API 呼叫中使用[Post 搜尋位址反向批次 API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch)來傳送查詢批次。


1. 在 Postman 中，按一下 [**新增要求**] [  |  **取得要求**]，並將它命名為**反向位址搜尋**。

2. 在 [建立器] 索引標籤上，選取 [取得]**** HTTP 方法，然後輸入您 API 端點的要求 URL。
  
    ![反向地址搜尋 URL](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 參數 | 建議的值 |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | 授權 | 無授權 |
  
3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數：
  
    ![反向位址搜尋參數](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | 機碼 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | 查詢 | 47.591180,-122.332700 |
  
4. 按一下 [傳送]****，然後檢視回應本文。

    回應會包含關於薩菲柯球場的重要地址資訊。
  
5. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | 機碼 | 值 |
    |-----|------------|
    | number | true |

    如果[數位](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查詢參數與要求一起傳送，回應可能會包含街道的側邊（左或右），以及該數位的位移位置。
  
6. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | 機碼 | 值 |
    |-----|------------|
    | returnSpeedLimit | true |
  
    設定[returnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查詢參數時，回應會傳回已張貼的速度限制。

7. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | 機碼 | 值 |
    |-----|------------|
    | returnRoadUse | true |

    當設定 [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 查詢參數時，回應會傳回街道層級的反向地理代碼道路使用陣列。

8. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | 機碼 | 值 |
    |-----|------------|
    | roadUse | true |

    您可以使用[roadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查詢參數，將反向地理編碼查詢限制為特定類型的道路。
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>使用反向位址交叉街道搜尋搜尋交叉街道

1. 在 Postman 中，按一下 [**新增要求**] [  |  **取得要求**]，並將它命名為**反向位址交叉街道搜尋**。

2. 在 [建立器] 索引標籤上，選取 [取得]**** HTTP 方法，然後輸入您 API 端點的要求 URL。
  
    ![反向地址交叉街道搜尋](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 參數 | 建議的值 |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | 授權 | 無授權 |
  
3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數：
  
    | 機碼 | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<your Azure Maps key\> |
    | 查詢 | 47.591180,-122.332700 |
  
4. 按一下 [傳送]****，然後檢視回應本文。

## <a name="next-steps"></a>後續步驟

- 流覽[Azure 地圖服務搜尋服務 REST API 檔](https://docs.microsoft.com/rest/api/maps/search)。
- 瞭解[Azure 地圖服務搜尋服務最佳做法](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)，以及如何優化您的查詢。
