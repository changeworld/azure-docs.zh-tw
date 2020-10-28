---
title: 使用 Microsoft Azure Maps 要求傳輸資料行動服務
description: 瞭解如何使用 Azure 地圖服務行動服務來要求公開傳輸資料，例如 metro 區域識別碼、運輸停止、路線和路線路線。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/22/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3f6f50d0ffeb48b5f359221992cc9a51d2ebb056
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895659"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>使用 Azure 地圖服務行動服務要求公開傳輸資料

本文說明如何使用 Azure 地圖服務 [行動服務](/rest/api/maps/mobility) 來要求公開傳輸資料。 傳輸資料包含傳輸停止、路線資訊和行進時間估計。

在本文中，您將瞭解如何：

* 使用[取得 Metro 區域 API](/rest/api/maps/mobility/getmetroareapreview)取得 METRO 區域識別碼
* 使用 [ [取得鄰近的傳輸](/rest/api/maps/mobility/getnearbytransitpreview) ] 服務，要求附近的傳輸停止。
* 查詢 [取得傳輸路由 API](/rest/api/maps/mobility/gettransitroutepreview) ，以使用公開傳輸來規劃路由。
* 使用「 [取得傳輸路線 API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)」來要求路線的傳輸路由幾何和詳細排程。

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

本教學課程使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

## <a name="get-a-metro-area-id"></a>取得 metro 區域識別碼

為了要求有關運輸機關的詳細資訊，以及特定的大都市區域支援的傳輸類型，您需要該 `metroId` 區域的。 「 [取得 Metro 區域 API](/rest/api/maps/mobility/getmetroareapreview) 」可讓您要求 Azure 地圖服務行動服務可用的 metro 區域。 回應包含詳細資料 `metroId` ，例如、 `metroName` 和 GeoJSON 格式的 metro 區域幾何標記法。

讓我們提出要求，以取得 Seattle-Tacoma metro 區域識別碼的 Metro 區域。 若要要求 metro 區域的識別碼，請完成下列步驟：

1. 開啟 Postman 應用程式，讓我們建立集合來儲存要求。 在 Postman 應用程式頂端處，選取 [新增]。 在 [新建] 視窗中，選取 [集合]。  為集合命名，然後選取 [建立] 按鈕。

2. 若要建立要求，請再次選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入要求的 [要求名稱]。 選取您在上一個步驟中建立的集合，做為儲存要求的位置。 然後選取 [儲存]。
  
    ![在 Postman 中建立要求](./media/how-to-request-transit-data/postman-new.png)

3. 在 [建立器] 索引標籤上選取 [ **取得** HTTP 方法]，並輸入下列 URL 以建立 GET 要求。 `{subscription-key}`將取代為您 Azure 地圖服務的主要金鑰。

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. 成功要求之後，您會收到下列回應：

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="request-nearby-transit-stops"></a>要求附近的傳輸停止

Azure 地圖服務 [取得鄰近傳輸](/rest/api/maps/mobility/getnearbytransitpreview) 服務可讓您搜尋傳輸物件。 API 會傳回傳輸物件詳細資料，例如公用傳輸停止，以及在指定位置周圍共用的自行車。 接下來，我們會向服務提出要求，以搜尋附近的公用運輸在指定位置周圍的300計量半徑內停止。

若要對「 [取得鄰近的傳輸](/rest/api/maps/mobility/getnearbytransitpreview)」提出要求，請依照下列步驟執行：

1. 在 [Postman] 中，按一下 [ **新增要求**  |  **get 要求** ]，並將其命名為 **附近的停止** 。

2. 在 [建立器] 索引標籤上，選取 [ **取得** HTTP 方法]，為您的 API 端點輸入下列要求 URL，然後按一下 [ **傳送** ]。

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 在成功的要求之後，回應結構看起來應該如下所示：

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    } 
    ```

如果您仔細觀察回應結構，就會看到它包含每個傳輸物件的參數。 每個傳輸物件都有參數 `id` ，例如，、、 `type` `stopName` 、 `mainTransitType` 、 `mainAgencyName` 和物件的位置（以座標表示）。

基於學習的目的，我們會 `id` 在下一節中使用匯流排停止作為來源的起點。  

## <a name="request-a-transit-route"></a>要求傳輸路由

Azure 地圖服務 [取得傳輸路由 API](/rest/api/maps/mobility/gettransitroutepreview) 允許進行行程規劃。 它會從來源傳回最可能的路由選項至目的地。 此服務提供不同類型的旅遊模式，包括行走、biking 和公開運輸。 接下來，我們會搜尋從最接近的匯流排停止至西雅圖的空間指標的路線。

### <a name="get-location-coordinates-for-destination"></a>取得目的地的位置座標

為了取得空間指標塔的位置座標，我們將使用 Azure 地圖服務 [模糊搜尋服務](/rest/api/maps/search/getsearchfuzzy)。

若要對「模糊搜尋」服務提出要求，請依照下列步驟執行：

1. 在 [Postman] 中，按一下 [ **新增要求**  |  **get 要求** ]，並將其命名為 **取得位置座標** 。

2. 在 [建立器] 索引標籤上，選取 [ **取得** HTTP 方法]，輸入下列要求 URL，然後按一下 [ **傳送** ]。

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. 如果您仔細查看回應，它會在空間指標搜尋的結果中包含多個位置。 每個結果都會包含 **位置的位置座標。** 在 `lat` `lon` 第一個結果的 **位置** 下複製和。

   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
                    "countrySubdivisionName": "Washington"
                },
                "position": {
                    "lat": 47.62039,
                    "lon": -122.34928
                },
                "viewport": {
                    "topLeftPoint": {
                        "lat": 47.62129,
                        "lon": -122.35061
                    },
                    "btmRightPoint": {
                        "lat": 47.61949,
                        "lon": -122.34795
                    }
                },
                "entryPoints": [
                    {
                        "type": "main",
                        "position": {
                            "lat": 47.61982,
                            "lon": -122.34886
                        }
                    }
                ]
            },
            ...,
            ...,
            ...
        ]
    }
    ```

### <a name="request-route"></a>要求路由

若要建立路由要求，請完成下列步驟：

1. 在 [Postman] 中，按一下 [ **新增要求**  |  **get 要求** ]，並將其命名為 **取得路由資訊** 。

2. 在 [建立器] 索引標籤上，選取 [ **取得** HTTP 方法]，為您的 API 端點輸入下列要求 URL，然後按一下 [ **傳送** ]。

    我們會藉由指定和參數來要求匯流排的公用傳輸路由 `modeType` `transitType` 。 要求 URL 包含在上一節中取出的位置。 針對 `originType` ，我們現在有一個 **stopId** 。 而且 `destionationType` ，我們有 **位置** 。

    查看您可以在要求中使用的 [URI 參數清單](/rest/api/maps/mobility/gettransitroutepreview#uri-parameters) ，以 [取得傳輸路由 API](/rest/api/maps/mobility/gettransitroutepreview)。
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 當要求成功時，回應結構看起來應該如下所示：

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. 如果您仔細觀察，回應中會有多個 **匯流排** 路由。 每個路線都有唯一的 **路線識別碼** 、描述路線中每個階段的摘要，以及 `itineraryFare` 提供各項和總價格的匯流排票證。 路由階段是兩個停止導航點之間路由的一部分。 接下來，我們將使用回應中的來要求最快路由的詳細資料 `itineraryId` 。

## <a name="request-fastest-route-itinerary"></a>要求最快的路線路線

Azure 地圖服務 [取得傳輸路線](/rest/api/maps/mobility/gettransititinerarypreview)服務可讓您使用「 [取得傳輸路由 API](/rest/api/maps/mobility/gettransitroutepreview) 」服務所傳回的路線路線 **識別碼** ，要求特定路由的資料。 若要提出要求，請完成下列步驟：

1. 在 Postman 中，按一下 [ **新增要求**  |  **get 要求** ]，並將其命名為 **取得傳輸資訊** 。

2. 在 [建立器] 索引標籤上，選取 [ **取得** HTTP 方法]。 為您的 API 端點輸入下列要求 URL，然後按一下 [ **傳送** ]。

    我們會將 `detailType` 參數設定為 **geometry** ，讓回應包含公用傳輸的停止訊息，以及路線的導覽與自行車腿的輪流導覽。

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. 當要求成功時，回應結構看起來應該如下所示。  如果您觀察到 JSON 回應，您會發現每個匯流排階段都包含一個 `legfare` 元素。 `legfare`元素包含個別購買之每個匯流排路由的成本（以分為計算）。 在回應結束時，您會看到一個元素， `itineraryFare` 其中包含整個路線的成本（以分為計算）。 在此範例中，每個都有四個匯流排路由 `$2.75` 。 但是，如果您針對整個路線購買單一票證，成本就是 `$5.50` 。

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>後續步驟

瞭解如何使用行動服務要求即時資料：

> [!div class="nextstepaction"]
> [如何要求即時資料](how-to-request-real-time-data.md)

探索 Azure 地圖服務行動服務 API 檔

> [!div class="nextstepaction"]
> [行動服務檔](/rest/api/maps/mobility)