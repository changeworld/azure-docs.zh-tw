---
title: 使用 Azure 地圖服務搜尋服務有效率地搜尋 |Microsoft Azure 對應
description: 瞭解如何使用 Microsoft Azure 對應來套用搜尋服務的最佳作法
author: walsehgal
ms.author: v-musehg
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 68c7408f13027ded7beaabf46fb663217a90c52b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845763"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>使用 Azure 地圖服務搜尋服務的最佳做法

Azure 地圖服務[搜尋服務](https://docs.microsoft.com/rest/api/maps/search)包含具有各種功能的 api。 例如，位址搜尋 API 是用來搜尋特定位置的相關點（POI）或資料。 本文示範從 Azure 地圖服務搜尋服務呼叫資料時，要套用的音效實務。 您將了解如何：

* 建立查詢以傳回相關的相符專案
* 限制搜尋結果
* 瞭解各種結果類型之間的差異
* 讀取位址搜尋回應結構


## <a name="prerequisites"></a>必要條件

若要對地圖服務 Api 進行任何呼叫，您需要 Azure 地圖服務帳戶和金鑰。 如有需要，請依照[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)和[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的指示進行。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

> [!Tip]
> 若要查詢搜尋服務，您可以使用[Postman 應用程式](https://www.getpostman.com/apps)來建立 REST 呼叫，也可以使用您偏好的任何 API 開發環境。


## <a name="best-practices-for-geocoding-address-search"></a>地理編碼的最佳作法（網址搜尋）

當您使用 Azure 地圖服務搜尋服務搜尋完整或部分位址時，API 會從您的搜尋查詢讀取關鍵字，並傳回位址的經度和緯度座標。 此程式稱為地理編碼。 在國家/地區中地理編碼的能力仰賴道路資料涵蓋範圍，以及地理編碼服務的地理編碼精確度。

請參閱[地理編碼涵蓋範圍](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)，以深入瞭解每個國家/地區的 Azure 地圖服務地理編碼功能。

### <a name="limit-search-results"></a>限制搜尋結果

 Azure 地圖服務搜尋 API 可協助您適當地限制搜尋結果，讓您可以向使用者顯示相關資料。

   > [!Note]
   > 並非所有搜尋 Api 支援的參數如下所示

   **地理偏差搜尋結果**

   為了將您的結果地理偏差到使用者的相關區域，您應該一律新增最多可能的詳細位置輸入。 若要限制搜尋結果，請考慮新增下列輸入類型：

   1. 設定 `countrySet` 參數，例如 "US，FR"。 預設搜尋行為是搜尋整個世界，可能會傳回不必要的結果。 如果您的查詢確實具有 `countrySet` 參數，則搜尋可能會傳回不正確的結果。 例如，搜尋名為**Bellevue**的城市將會傳回美國和法國的結果，因為 broth 法國和美國的城市名為**Bellevue** 。

   2. 您可以使用 `btmRight` 和 `topleft` 參數來設定周框方塊，以將搜尋限制在地圖上的特定區域。

   3. 若要影響結果的相關性區域，您可以定義 `lat`和 `lon` 座標參數，並使用 `radius` 參數來設定搜尋區域的半徑。


   **模糊搜尋參數**
   
  當您不知道使用者輸入的搜尋查詢內容時，建議使用 Azure 地圖服務[模糊搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 。 此 API 會將相關的點（POI）搜尋和地理編碼結合成標準的*單行搜尋*。

   1. `minFuzzyLevel` 和 `maxFuzzyLevel` 有助於傳回相關的相符專案，即使查詢參數不符合所需的資訊也一樣。 為了取得效能並減少不尋常的結果，`minFuzzyLevel=1` 和 `maxFuzzyLevel=2` 的預設搜尋查詢。 如需搜尋詞彙 "restrant" 的範例，當 `maxFuzzyLevel` 設定為2時，它會與「餐廳」相符。 預設的模糊層級可以視需要覆寫。  

   2. 您也可以使用 `idxSet` 參數，設定要傳回的一組確切結果類型的優先順序。 基於此目的，您可以提交以逗號分隔的索引清單;專案順序並不重要。 支援下列索引：

       * `Addr` - **位址範圍**：某些街道的位址點，會從街道的開頭和結尾插入。 這些點會以位址範圍表示。
       * `Geo` - **地理**位置：地圖上的區域，代表土地的管理部門，也就是國家/地區、州/省。
       * `PAD` - **點位址**：位於地圖上的點，其中具有街道名稱和數位的特定位址可以在索引中找到，例如 Soquel Dr 2501。 這個 idxSet 值是位址可用的最高精確度層級。  
       * `POI`**感興趣的  - 點**：對應上值得注意的點，而且可能會很有趣。  [取得搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不會傳回 poi。  
       * `Str` - **街道**：地圖上的街道標記法。
       * `XStr` - **交叉街道/交集**：聯接的標記法;兩個街道相交的位置。


       **使用範例**：

       * idxSet = POI （僅限搜尋點） 

       * idxSet = PAD，Addr （僅搜尋位址，PAD = 點位址，位址 = 位址範圍）

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>反向地理編碼和 geography 實體類型篩選

使用[搜尋位址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)進行反向地理編碼搜尋時，服務可以傳回系統管理區域的多邊形。 藉由在要求中提供參數 `entityType`，您可以縮小指定 geography 實體類型的搜尋範圍。 產生的回應將包含地理識別碼，以及符合的實體類型。 如果您提供一個以上的實體，則端點會傳回**可用的最小實體**。 傳回的 Geometry ID 可以透過[取得多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)，用來取得該地理位置的幾何。

**範例要求：**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**回應：**

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

### <a name="search-results-language"></a>搜尋結果語言

`language` 參數可讓您選取 API 所傳回之結果的語言。 如果未在要求中設定語言，搜尋服務會自動預設為國家/地區中最常見的語言。 此外，當指定語言中的資料無法使用時，會使用預設語言。 請參閱[支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)，以取得依國家/地區 Azure 地圖服務服務支援的語言清單。


### <a name="predictive-mode-autosuggest"></a>預測模式（自動建議）

若要尋找部分查詢的更多相符專案，`typeahead` 參數應該設定為 ' true '。 查詢將會被視為部分輸入，而且搜尋會進入預測模式。 否則，服務會假設所有相關資訊都已經傳入。

在下面的範例查詢中，您可以看到搜尋位址服務已查詢 "Microso"，並將 `typeahead` 參數設為**true**。 如果您觀察到回應，您可以看到搜尋服務將查詢轉譯為部分查詢。 回應包含 autosuggested 查詢的結果。

**範例查詢︰**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**回應：**

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


### <a name="uri-encoding-to-handle-special-characters"></a>用來處理特殊字元的 URI 編碼 

若要尋找交叉街道位址，您必須編碼 URI 以處理位址中的特殊字元。 請考慮此位址範例：「第1號 & 等位，西雅圖」。 在傳送要求之前，必須先編碼特殊字元 ' & '。 我們建議您在 URI 中編碼字元資料，其中所有字元都會使用 '% ' 字元和對應到 UTF-8 字元的兩個字元十六進位值進行編碼。

**使用範例**：

取得搜尋位址：

```
query=1st Avenue & E 111th St, New York
```

 應編碼為：

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


以下是不同語言所使用的不同方法： 

JavaScript/TypeScript：
```Javascript
encodeURIComponent(query)
```

C#VB
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


## <a name="best-practices-for-poi-search"></a>POI 搜尋的最佳做法

感點數（POI）搜尋可讓您依名稱要求 POI 結果，例如依名稱搜尋業務。 我們強烈建議您使用 `countrySet` 參數來指定您的應用程式需要涵蓋範圍的國家/地區，因為預設行為是搜尋整個世界，可能會傳回不必要的結果，並（或）導致較長的搜尋時間。

### <a name="brand-search"></a>品牌搜尋

為了改善結果的相關性和回應中的資訊，「感興趣」（POI）搜尋回應包含可進一步用來剖析回應的品牌資訊。

您也可以在要求中提交品牌名稱的逗號分隔清單。 您可以使用清單，使用 `brandSet` 參數，將結果限制為特定品牌。 專案順序並不重要。 當提供多個品牌時，只會傳回其中一個提供的清單的結果（至少）。

讓我們對 Microsoft 校園附近的天然氣站（Redmond，WA）進行[POI 類別搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)要求。 如果您觀察到回應，您可以看到每個傳回之 POI 的品牌資訊。


**範例查詢︰**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**回應：**

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

POI 搜尋支援使用官方機場代碼來搜尋機場。 例如，**海運**（西雅圖 Tacoma 國際機場）。 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>鄰近搜尋

若只要取得特定位置的 POI 結果，附近的[搜尋 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)可能是正確的選擇。 此端點只會傳回 POI 的結果，並不會接受搜尋查詢參數。 若要限制結果，建議您設定 radius。

## <a name="understanding-the-responses"></a>瞭解回應

讓我們對 Azure 地圖服務[搜尋服務](https://docs.microsoft.com/rest/api/maps/search)提出位址搜尋要求，以取得位於西雅圖的位址。 如果您仔細看下面的 [要求 URL]，我們已將 [`countrySet`] 參數設定為 [**美國**]，以搜尋美國地區的位址。

**範例查詢︰**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

接下來讓我們看一下下面的回應結構。 回應中結果物件的結果類型不同。 如果您仔細觀察，可以看到我們有三種不同類型的結果物件，也就是「點位址」、「街道」和「交叉街道」。 請注意，[位址搜尋] 不會傳回 Poi。 每個回應物件的 `Score` 參數會指示相同回應中其他物件分數的相對符合分數。 若要深入瞭解回應物件參數，請參閱[取得搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)。

**支援的結果類型：**

* **點位址：** 在地圖上指向具有街道名稱和數位的特定位址。 位址可用的最高精確度層級。 

* **位址範圍：** 對於某些街道，會有從街道開頭和結尾處插補的位址點;這些點會以位址範圍表示。 

* **地理位置：** 地圖上的區域，代表土地的管理部門，也就是國家/地區、州/省。 

* **POI-（感重點）：** 地圖上值得注意的點，而且可能會很有趣。

* **街道：** 地圖上的街道標記法。 位址會解析為包含位址之街道的緯度/經度座標。 可能不會處理門牌號碼。 

* **交叉街道：** 交集. 聯接的標記法;兩個街道相交的位置。

**回應：**

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

當回應類型是**geometry**時，它可以包含在 "geometry" 和 "ID" 下的資料**源**物件中傳回的 geometry 識別碼。 例如，「[取得多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)」可讓您以 GeoJSON 格式要求 geometry 資料。 例如，一組實體的城市或機場外框。 您可以將此界限資料用於幾何內的[地理柵欄](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) 或[搜尋 poi](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)。


[搜尋位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)或[搜尋模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)API 回應可以包含在「geometry」和「識別碼」下的資料來源物件中傳回的**幾何識別碼**。


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
