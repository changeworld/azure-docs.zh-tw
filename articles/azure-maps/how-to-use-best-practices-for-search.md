---
title: 使用 Azure 地圖服務搜尋服務有效率地搜尋 |Microsoft Azure 對應
description: 瞭解如何使用 Microsoft Azure 對應來套用搜尋服務的最佳做法。
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e29b3d70c576955637424208aeb0f980669b67bb
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899165"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure 地圖服務搜尋服務的最佳做法

Azure 地圖服務[搜尋服務](https://docs.microsoft.com/rest/api/maps/search)包含提供各種功能的 api。 例如，搜尋位址 API 可以尋找特定位置的相關點數（POI）或資料。 

本文說明當您從 Azure 地圖服務搜尋服務呼叫資料時，如何套用音效實務。 您將學習如何：

* 建立查詢以傳回相關的相符專案。
* 限制搜尋結果。
* 瞭解結果類型之間的差異。
* 讀取位址搜尋-回應結構。

## <a name="prerequisites"></a>必要條件

若要對 Azure 地圖服務服務 Api 進行呼叫，您需要 Azure 地圖服務帳戶和金鑰。 如需詳細資訊，請參閱[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)並[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 

如需 Azure 地圖服務中驗證的相關資訊，請參閱[Azure 地圖服務中的管理驗證](./how-to-manage-authentication.md)。

> [!TIP]
> 若要查詢搜尋服務，您可以使用[Postman 應用程式](https://www.getpostman.com/apps)來建立 REST 呼叫。 或者，您可以使用您偏好的任何 API 開發環境。

## <a name="best-practices-to-geocode-addresses"></a>地理編碼位址的最佳做法

當您使用 Azure 地圖服務搜尋服務來搜尋完整或部分位址時，API 會從您的搜尋查詢讀取關鍵字。 然後，它會傳回位址的經度和緯度座標。 此程式稱為*地理編碼*。 

在國家/地區地理編碼的能力取決於道路資料的可用性和地理編碼服務的精確度。 如需依國家或地區 Azure 地圖服務地理編碼功能的詳細資訊，請參閱[地理編碼涵蓋範圍](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)。

### <a name="limit-search-results"></a>限制搜尋結果

 Azure 地圖服務搜尋 API 可協助您適當地限制搜尋結果。 您可以限制結果，以便向您的使用者顯示相關資料。

> [!NOTE]
> 搜尋 Api 支援的參數比本文所討論的還要多。

#### <a name="geobiased-search-results"></a>Geobiased 搜尋結果

若要將結果 geobias 至使用者的相關區域，請一律盡可能新增最多位置詳細資料。 您可能想要藉由指定一些輸入類型來限制搜尋結果：

* 設定 `countrySet` 參數。 例如，您可以將它設定為 `US,FR`。 根據預設，API 會搜尋整個世界，因此它可能會傳回不必要的結果。 如果您的查詢沒有 `countrySet` 參數，則搜尋可能會傳回不正確的結果。 例如，名為*Bellevue*的城市搜尋會傳回美國和法國的結果，因為兩個國家/地區都包含一個名為*Bellevue*的城市。

* 您可以使用 `btmRight` 和 `topleft` 參數來設定周框方塊。 這些參數會將搜尋限制在地圖上的特定區域。

* 若要影響結果的相關性區域，請定義 `lat` 和 `lon` 座標參數。 使用 `radius` 參數來設定搜尋區域的半徑。


#### <a name="fuzzy-search-parameters"></a>模糊搜尋參數

當您不知道搜尋查詢的使用者輸入時，建議使用 Azure 地圖服務[搜尋模糊 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 此 API 會將 POI 搜尋和地理編碼結合成標準的*單行搜尋*： 

* 即使查詢參數不完全符合使用者想要的資訊，`minFuzzyLevel` 和 `maxFuzzyLevel` 參數也有助於傳回相關的相符專案。 若要將效能最大化並減少不尋常的結果，請將搜尋查詢設定為 `minFuzzyLevel=1` 和 `maxFuzzyLevel=2`的預設值。 

    例如，當 [`maxFuzzyLevel`] 參數設定為2時，搜尋詞彙*restrant*會與*餐廳*進行比對。 當您需要時，可以覆寫預設的模糊層級。 

* 請使用 `idxSet` 參數來排列一組確切結果類型的優先順序。 若要設定一組確切結果的優先順序，您可以提交以逗號分隔的索引清單。 在您的清單中，專案順序並不重要。 Azure 地圖服務支援下列索引：

    * `Addr` - **位址範圍**：從街道開頭和結尾處插補的位址點。 這些點會以位址範圍表示。
    * `Geo` - **地理**位置：土地的管理部門。 例如，地理位置可以是國家/地區、州或城市。
    * `PAD` - **點位址**：包含街道名稱和號碼的位址。 您可以在索引中找到點位址。 例如， *Soquel Dr 2501*。 點位址提供位址可用的最高精確度層級。  
    * `POI`**感興趣的  - 點**：對應上被視為值得注意的點，或是可能會有興趣的點。 [搜尋位址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不會傳回 poi。  
    * `Str` - **街道**：地圖上的街道。
    * `XStr` - **交叉街道或交集**：兩個街道相交的接合或位置。


#### <a name="usage-examples"></a>使用範例

* `idxSet=POI`-僅搜尋 Poi。 

* `idxSet=PAD,Addr`-僅搜尋位址。 `PAD` 指出點位址，`Addr` 表示位址範圍。

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Geography 實體類型的反向地理編碼和篩選

當您在[搜尋位址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)中執行反向地理編碼搜尋時，服務可以傳回系統管理區域的多邊形。 若要將搜尋範圍縮小至特定地理位置實體類型，請在您的要求中包含 `entityType` 參數。 

產生的回應會包含地理識別碼，以及符合的實體類型。 如果您提供一個以上的實體，則端點會傳回*可用的最小實體*。 您可以使用傳回的幾何識別碼，透過[搜尋多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)取得地理位置的幾何。

#### <a name="sample-request"></a>範例要求

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>回應

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>設定結果語言

使用 `language` 參數來設定所傳回搜尋結果的語言。 如果要求未設定語言，則根據預設，搜尋服務會在國家或地區中使用最常見的語言。 當指定的語言中沒有可用的資料時，會使用預設語言。 

如需詳細資訊，請參閱[Azure 地圖服務支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)。


### <a name="use-predictive-mode-automatic-suggestions"></a>使用預測模式（自動建議）

若要尋找部分查詢的更多相符專案，請將 `typeahead` 參數設定為 `true`。 此查詢會被視為部分輸入，而且搜尋會進入預測模式。 如果您未將 `typeahead` 參數設定為 `true`，則服務會假設所有相關資訊都已經傳入。

在下列範例查詢中，會查詢搜尋位址服務是否有*Microso*。 在這裡，`typeahead` 參數設定為 `true`。 回應會顯示搜尋服務將查詢轉譯為部分查詢。 回應包含自動建議查詢的結果。

#### <a name="sample-query"></a>範例查詢

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>回應

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>編碼 URI 以處理特殊字元 

若要尋找交叉街道位址，您必須編碼 URI 以處理位址中的特殊字元。 請考慮此位址範例：*第1個 & 聯集街道，西雅圖*。 在這裡，您會在傳送要求之前，先編碼連字號（`&`）。 

我們建議您在 URI 中編碼字元資料。 在 URI 中，您可以使用百分比符號（`%`）和兩個字元的十六進位值（對應至字元的 UTF-8 程式碼）來編碼所有字元。

#### <a name="usage-examples"></a>使用範例

從這個位址開始：

```
query=1st Avenue & E 111th St, New York
```

將位址編碼：

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

您可以使用下列方法。

JavaScript 或 TypeScript：
```Javascript
encodeURIComponent(query)
```

C#或 Visual Basic：
```csharp
Uri.EscapeDataString(query)
```

Java：
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python：
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++：
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP：
```PHP
urlencode(query)
```

加注
```Ruby
CGI::escape(query) 
```

Swift：
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

消失
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>POI 搜尋的最佳做法

在 POI 搜尋中，您可以依名稱要求 POI 結果。 例如，您可以依名稱搜尋商務。 

我們強烈建議您使用 `countrySet` 參數來指定應用程式需要涵蓋範圍的國家/地區。 預設行為是搜尋整個世界。 這種廣泛的搜尋可能會傳回不必要的結果，而且搜尋可能會花很長的時間。

### <a name="brand-search"></a>品牌搜尋

為了改善結果的相關性和回應中的資訊，POI 搜尋回應包含品牌資訊。 您可以使用此資訊進一步剖析回應。

在要求中，您可以提交以逗號分隔的品牌名稱清單。 使用清單，藉由設定 `brandSet` 參數，將結果限制為特定品牌。 在您的清單中，專案順序並不重要。 當您提供多個品牌清單時，傳回的結果必須至少屬於您的其中一個清單。

若要探索品牌搜尋，讓我們建立[POI 類別搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)要求。 在下列範例中，我們會尋找位於華盛頓州 Redmond 的 Microsoft 校園附近的天然氣站。 回應會顯示所傳回之每個 POI 的品牌資訊。


#### <a name="sample-query"></a>範例查詢

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>回應

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>機場搜尋

藉由使用搜尋 POI API，您可以使用其官方程式碼來尋找機場。 例如，您可以使用*海運*來尋找西雅圖 Tacoma 國際機場： 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>鄰近搜尋

若要取得有關特定位置的 POI 結果，您可以嘗試使用[鄰近的 API 搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)。 端點只會傳回 POI 結果。 它不會接受搜尋查詢參數。 

若要限制結果，我們建議您設定 radius。

## <a name="understanding-the-responses"></a>瞭解回應

讓我們在西雅圖尋找位址，方法是對 Azure 地圖服務搜尋服務提出位址搜尋要求。 在下列要求 URL 中，我們將 `countrySet` 參數設定為 `US`，以搜尋美國的位址。

### <a name="sample-query"></a>範例查詢

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>支援的結果類型

* **點位址**：指向具有街道名稱和數位之特定位址的地圖上的點。 [點位址] 提供位址最高的精確度等級。 

* **位址範圍**：從街道開頭和結尾處插補的位址點範圍。  

* **Geography**：地圖上的區域，代表土地的管理部門，例如國家/地區、州或城市。 

* **POI**：位於地圖上的點值得注意，而且可能會有興趣。

* **街道**：地圖上的街道。 位址會解析為包含位址之街道的緯度和經度座標。 可能不會處理房屋號碼。 

* **交叉街道**：交集。 交叉街道代表兩個街道相交的接合。

### <a name="response"></a>回應

讓我們看一下回應結構。 在接下來的回應中，結果物件的類型是不同的。 如果您仔細查看，您會看到三種類型的結果物件：

* 點位址
* Street
* 交叉街道

請注意，位址搜尋不會傳回 Poi。  

每個回應物件的 `Score` 參數會指出符合分數如何與相同回應中其他物件的分數產生關聯。 如需回應物件參數的詳細資訊，請參閱[取得搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)。

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>幾何

*Geometry*的回應類型可以包含在 `geometry` 和 `id`下，`dataSources` 物件中傳回的幾何識別碼。 例如，您可以使用「[搜尋多邊形」服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)，以 GeoJSON 格式來要求 geometry 資料。 藉由使用此格式，您可以取得一組實體的城市或機場外框。 接著，您可以使用此界限資料，在幾何內[設定地理柵欄](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或[搜尋 poi](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)。


[搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)Api 或[搜尋模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)api 的回應可以包含在 `geometry` 和 `id`下，`dataSources` 物件中傳回的幾何識別碼：


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>後續步驟

* 瞭解[如何建立 Azure 地圖服務搜尋服務要求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)。
* 探索 Azure 地圖服務[搜尋服務 API 檔](https://docs.microsoft.com/rest/api/maps/search)。 
