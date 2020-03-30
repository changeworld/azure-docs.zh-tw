---
title: 使用 Azure 地圖搜索服務搜索位置 |微軟 Azure 地圖
description: 在本文中，您將學習如何使用 Microsoft Azure 地圖搜索服務搜索位置進行地理編碼和反向地理編碼。
author: philmea
ms.author: philmea
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: cf0e5267885df1ace51271c53bb2d68ee5002f00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335438"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>使用 Azure 地圖搜索服務搜索位置

Azure 地圖[搜索服務](https://docs.microsoft.com/rest/api/maps/search)是一組 RESTful API，旨在説明開發人員搜索位址、地點、按名稱或類別分類的業務清單以及其他地理資訊。 除了支援傳統的地理編碼外，服務還可以根據緯度和經度反轉地理編碼位址和交叉街道。 搜索返回的緯度和經度值可用作其他 Azure 地圖服務（如[路由](https://docs.microsoft.com/rest/api/maps/route)和[天氣](https://docs.microsoft.com/rest/api/maps/weather)服務）中的參數。

在本文中，您將學習如何：

* 使用[搜索位址 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)請求位址（地理編碼位址位置）的緯度和經度座標
* 使用[模糊搜索 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)搜索位址或興趣點 （POI）
* 搜索位址以及屬性和座標
* 進行[反向位址搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)，將座標位置轉換為街道位址
* 使用[搜索位址反向跨街 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet)搜索十字路口

## <a name="prerequisites"></a>Prerequisites

要完成本文中的步驟，需要首先創建 Azure 地圖帳戶並獲取映射帳戶訂閱金鑰。 按照[創建帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)中的說明創建 Azure 地圖帳戶訂閱，然後按照[獲取主鍵](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的步驟獲取帳戶的主金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

本文使用 [Postman 應用程式](https://www.getpostman.com/apps)來建置 REST 呼叫。 您可以使用您偏好的任何 API 開發環境。

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>請求位址的緯度和經度（地理編碼）

在此示例中，我們使用 Azure 地圖[獲取搜索位址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)將街道位址轉換為緯度和經度座標。 您可以將完整或部分街道位址傳遞給 API，並接收包含詳細位址屬性（如街道、郵遞區號和國家/地區）以及緯度和經度位置值的回應。

如果您有一組要地理編碼的位址，則可以使用["後搜索位址批次處理 API"](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch)在單個 API 呼叫中發送一批查詢。

1. 在郵遞員中，按一下 **"新請求** | **GET 請求**"並命名為 **"位址搜索**"。

2. 在 [建立器] 索引標籤上，選取 [取得]**** HTTP 方法、輸入您 API 端點的要求 URL，然後選取授權通訊協定 (如果有的話)。

![地址搜尋](./media/how-to-search-for-address/address_search_url.png)

| 參數 | 建議的值 |
|---------------|------------------------------------------------| 
| HTTP method | GET |
| 要求 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| 授權 | 無授權 |

3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數： 

![地址搜尋](./media/how-to-search-for-address/address_search_params.png) 

| Key | 值 | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| subscription-key | \<您的 Azure 地圖服務金鑰\> | 
| 查詢 | 400 Broad St, Seattle, WA 98109 | 

4. 按一下 [傳送]****，然後檢視回應本文。 

在此情況下，您指定了完整的地址查詢，並在回應主體中接收單一結果。 

5. 在 Params 中，將查詢字串編輯為下列值： 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****： 

| Key | 值 | 
|-----|------------| 
| typeahead | true | 

**typeahead** 旗標會指示地址搜尋 API 將查詢視為部分輸入，並傳回預測值的陣列。

## <a name="using-fuzzy-search-api"></a>使用模糊搜索 API

當您不知道搜索查詢的使用者輸入是什麼時，建議使用 Azure 映射[模糊搜索 API。](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API 將興趣點 （POI） 搜索和地理編碼合併為規範的"單行搜索"。 例如，該 API 可以處理任何地址或 POI 權杖之組合的輸入。 API 也可與內容相關的位置進行加權 (lat./lon. pair)，完全受到座標和半徑所限制，或可以更廣泛地執行，不受任何地理偏差錨點影響。

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

    URL 路徑中的 **Json** 屬性會判斷回應格式。 本文使用 json，便於使用和可讀性。 您可以在[地圖服務功能性 API 參考](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)的＜取得搜尋模糊＞**** 定義中找到可用的回應格式。

3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數：

    ![模糊搜尋](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Key | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<您的 Azure 地圖服務金鑰\> |
    | 查詢 | 披薩 |

4. 按一下 [傳送]****，然後檢視回應本文。

    "比薩餅"的不明確查詢字串返回"比薩餅"和"餐廳"類別中的 10[個興趣點結果](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse)（POI）。 每個結果返回位置的街道位址、緯度和經度值、視圖埠和進入點。
  
    此查詢中的結果會有所不同，未繫結至任何特定的參考位置。 您可以使用**國家/地區設置**參數僅指定應用程式需要覆蓋的國家/地區。 預設行為是搜索整個世界，可能會返回不必要的結果。

5. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | Key | 值 |
    |------------------|-------------------------|
    | countrySet | US |
  
    結果現在會依國家/地區程式碼繫結，此查詢會傳回美國境內的披薩餐廳。
  
    若要提供某個位置的結果，您可以查詢景點，並使用針對模糊搜尋服務之呼叫中所傳回的緯度和經度值。 在此情況下，您使用搜尋服務來傳回「西雅圖太空針塔」的位置，並使用 lat. / lon. 值來導向搜尋。
  
6. 在 Params 中，輸入下列金鑰 / 值組，並按一下 [傳送]****：

    ![模糊搜尋](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | Key | 值 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>使用反向地址搜尋來搜尋街道地址

Azure 地圖[獲取搜索位址反向 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)有助於將座標（例如：37.786505，-122.3862）轉換為人類可以理解的街道位址。 通常，在跟蹤應用程式時，您需要從設備或資產接收 GPS 源，並想知道座標所在的位址。
如果您有一組座標位置來反轉地理編碼，則可以使用["後搜索位址反向批次處理 API"](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch)在單個 API 呼叫中發送一批查詢。


1. 在郵遞員中，按一下 **"新請求** | **GET 請求**"並命名為 **"反向位址搜索**"。

2. 在 [建立器] 索引標籤上，選取 [取得]**** HTTP 方法，然後輸入您 API 端點的要求 URL。
  
    ![反向地址搜尋 URL](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 參數 | 建議的值 |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | 授權 | 無授權 |
  
3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數：
  
    ![反向位址搜尋參數](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | Key | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<您的 Azure 地圖服務金鑰\> |
    | 查詢 | 47.591180,-122.332700 |
  
4. 按一下 [傳送]****，然後檢視回應本文。

    回應會包含關於薩菲柯球場的重要地址資訊。
  
5. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | Key | 值 |
    |-----|------------|
    | number | true |

    如果與請求一起發送[數位](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查詢參數，回應可能包括街道的一側（左側或右側）以及該號碼的偏移位置。
  
6. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | Key | 值 |
    |-----|------------|
    | returnSpeedLimit | true |
  
    設置[ReturnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查詢參數時，回應將返回發佈的速度限制。

7. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | Key | 值 |
    |-----|------------|
    | returnRoadUse | true |

    當設定 [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 查詢參數時，回應會傳回街道層級的反向地理代碼道路使用陣列。

8. 將下列機碼值組加入 **Params** 區段並按一下 [傳送]****：

    | Key | 值 |
    |-----|------------|
    | roadUse | true |

    您可以使用[道路使用](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)查詢參數將反向地理編碼查詢限制為特定類型的道路。
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>使用反向位址跨街搜索搜索交叉街道

1. 在郵遞員中，按一下 **"新請求** | **GET 請求**"並命名為 **"反向位址跨街搜索**"。

2. 在 [建立器] 索引標籤上，選取 [取得]**** HTTP 方法，然後輸入您 API 端點的要求 URL。
  
    ![反向地址交叉街道搜尋](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 參數 | 建議的值 |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | 要求 URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | 授權 | 無授權 |
  
3. 按一下 [Params]****，然後輸入下列金鑰 / 值組來作為要求 URL 中的查詢或路徑參數：
  
    | Key | 值 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | \<您的 Azure 地圖服務金鑰\> |
    | 查詢 | 47.591180,-122.332700 |
  
4. 按一下 [傳送]****，然後檢視回應本文。

## <a name="next-steps"></a>後續步驟

- 流覽[Azure 地圖搜索服務 REST API 文檔](https://docs.microsoft.com/rest/api/maps/search)。
- 瞭解[Azure 地圖搜索服務最佳實踐](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)以及如何優化查詢。
