---
title: 搜尋 Bing 當地商家搜尋 API 的類別
titleSuffix: Azure Cognitive Services
description: 使用本文了解如何針對 Bing 當地商家搜尋 API 端點指定搜尋類別。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 41acc956d4eca2accc16c46cdc52336c3cd89065
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095419"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>搜尋 Bing 當地商家搜尋 API 的類別

> [!WARNING]
> Bing 搜尋 Api 會從認知服務移至 Bing 搜尋服務。 從 **2020 年10月 30** 日開始，任何新的 Bing 搜尋實例都必須依照 [此處](https://aka.ms/cogsvcs/bingmove)所述的程式進行布建。
> 接下來的三年或 Enterprise 合約結束之前，將支援使用認知服務布建的 Bing 搜尋 Api （以先發生者為准）。
> 如需遷移指示，請參閱 [Bing 搜尋服務](https://aka.ms/cogsvcs/bingmigration)。

Bing 當地商家搜尋 API 可讓您搜尋各種類別的當地商家實體，且優先考慮接近使用者的位置。 您可以在搜尋中包含這些搜尋以及 `localCircularView` 和 `localMapView` [參數](specify-geographic-search.md)。


## <a name="toplevel-categories"></a>最上層類別 

下列類型定義了主要的搜尋類別。  可以使用指派給 `localCategories` 參數的逗號分隔清單指定多個類別。  
- EatDrink 
- SeeDo 
- Shop 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parking 
- Hospitals 

## <a name="sub-categories"></a>子類別
子類別的傳遞方式與 `localCategories` 相同。 子類別會是更特定的類別。 它們是從屬的，如果您在相同的逗號分隔清單中指定類別 C 和該類別其中一個子類別 S，您將收到與單獨指定 C 相同的結果。

### <a name="eat-drink"></a>Eat Drink

> BreweriesAndBrewPubs、CocktailLounges、AfricanRestaurants、AmericanRestaurants、Bagels、BarbecueRestaurants、Taverns、SportsBars、橫條、BarsGrillsAndPubs、BuffetRestaurants |BelgianRestaurants、BritishRestaurants、CafeRestaurants、CaribbeanRestaurants、ChineseRestaurants、CoffeeAndTea、Delicatessens、DeliveryService、Diners、DiscountStores、甜甜圈、FastFood、FrenchRestaurants、FrozenYogurt、GermanRestaurants、超市、GreekRestaurants、Grocers、HawaiianRestaurants、HungarianRestaurants、IceCreamAndFrozenDesserts、IndianRestaurants、ItalianRestaurants、JapaneseRestaurants、動力、KoreanRestaurants、LiquorStores、MexicanRestaurants、MiddleEasternRestaurants、比薩、PolishRestaurants、PortugueseRestaurants、Pretzels、RussianAndUkrainianRestaurants、三明治、SeafoodRestaurants、SpanishRestaurants、SteakHouseRestaurants、SushiRestaurants、重點、ThaiRestaurants、TurkishRestaurants、VegetarianAndVeganRestaurants、VietnameseRestaurants、

### <a name="see-do"></a>See Do

> AmusementParks、景點、Carnivals、Casinos、LandmarksAndHistoricalSites、MiniatureGolfCourses、MovieTheaters、博物館、公園、SightseeingTours、TouristInformation、動物園

### <a name="shop"></a>Shop

> AntiqueStores, Bookstores, CDAndRecordStores, ChildrensClothingStores, CigarAndTobaccoShops, ComicBookStores, DepartmentStores, DiscountStores, FleaMarketsAndBazaars, FurnitureStores, HomeImprovementStores, JewelryAndWatchesStores, KitchenwareStores, LiquorStores, MallsAndShoppingCenters, MensClothingStores, MusicStores, OutletStores, PetShops, PetSupplyStores, SchoolAndOfficeSupplyStores, ShoeStores, SportingGoodsStores, ToyAndGameStores, VitaminAndSupplementStores, WomensClothingStores


## <a name="examples-of-local-categories-search"></a>當地類別搜尋的範例

下列範例根據 `localCategories` 參數取得結果：

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

下列查詢會將從 Bing 當地商家搜尋 API 傳回的 'hospital' 結果數目限制為前三個：

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

下列範例 JSON 回應包括大西雅圖地區的三家醫院：

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>下一步
- [地理搜尋界限](specify-geographic-search.md)
- [查詢和回應](local-search-query-response.md)
- [C# 快速入門](quickstarts/local-quickstart.md)
