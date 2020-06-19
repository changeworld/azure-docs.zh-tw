---
title: Azure 地圖服務搜尋服務的最佳做法 | Microsoft Azure 地圖服務
description: 了解如何在使用 Microsoft Azure 地圖服務的搜尋服務時套用最佳做法。
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ea44355795f0685f42de1306e979707f34d8f142
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83742760"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure 地圖服務搜尋服務的最佳做法

Azure 地圖服務的[搜尋服務](https://docs.microsoft.com/rest/api/maps/search) \(部分機器翻譯\) 包含可提供各種功能的 API。 例如，搜尋地址 API 可以在特定位置周圍尋找興趣點 (POI) 或資料。 

此文章說明當您從 Azure 地圖服務的搜尋服務呼叫資料時如何套用完善的做法。 您將學習如何：

* 建置查詢以傳回相關的相符項目。
* 限制搜尋結果。
* 了解結果類型之間的差異。
* 讀取地址搜尋-回應結構。

## <a name="prerequisites"></a>Prerequisites

若要對 Azure 地圖服務 API 進行呼叫，您需要 Azure 地圖服務帳戶和金鑰。 如需詳細資訊，請參閱[建立帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)和[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 

如需 Azure 地圖服務中驗證的相關資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

> [!TIP]
> 若要查詢搜尋服務，您可以使用 [Postman 應用程式](https://www.getpostman.com/apps) \(英文\) 來建置 REST 呼叫。 或者，您可以使用任何慣用的 API 開發環境。

## <a name="best-practices-to-geocode-addresses"></a>對地址進行地理編碼的最佳做法

當您使用 Azure 地圖服務的搜尋服務來搜尋完整或部分地址時，API 會從您的搜尋查詢中讀取關鍵字。 然後，傳回該地址的經度和緯度座標。 此程序稱為「地理編碼」。 

在國家/地區中進行地理編碼的能力取決於道路資料的可用性，以及地理編碼服務的精確度。 如需 Azure 地圖服務依國家或地區提供之地理編碼功能的詳細資訊，請參閱[地理編碼涵蓋範圍](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) \(部分機器翻譯\)。

### <a name="limit-search-results"></a>限制搜尋結果

 Azure 地圖服務搜尋 API 可協助您適當地限制搜尋結果。 您可以限制結果，以便向使用者顯示相關資料。

> [!NOTE]
> 搜尋 API 支援的參數比此文章所討論的還要多。

#### <a name="geobiased-search-results"></a>地理偏好的搜尋結果

若要為使用者顯示對相關區域的地理偏好結果，請一律盡可能地新增最多位置詳細資料。 您可以透過指定一些輸入類型來限制搜尋結果：

* 設定 `countrySet` 參數。 例如，您可以將其設定為 `US,FR`。 根據預設，此 API 會搜尋全世界，因此可能會傳回不必要的結果。 如果您的查詢沒有 `countrySet` 參數，則搜尋可能會傳回不正確的結果。 例如，搜尋名為 *Bellevue* 的城市會傳回來自美國和法國的結果，因為這兩個國家/地區都包含名為 *Bellevue* 的城市。

* 您可以使用 `btmRight` 和 `topleft` 參數來設定界線框。 這些參數會將搜尋限制為地圖上的特定區域。

* 若要影響結果的相關區域，請定義 `lat` 和 `lon` 座標參數。 使用 `radius` 參數來設定搜尋區域的半徑。


#### <a name="fuzzy-search-parameters"></a>模糊搜尋參數

當您不知道使用者對搜尋查詢的輸入時，建議您使用 Azure 地圖服務的[搜尋模糊 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) \(部分機器翻譯\)。 此 API 會將 POI 搜尋和地理編碼合併為標準的「單行搜尋」： 

* 即使查詢參數未完全符合使用者想要的資訊，`minFuzzyLevel` 和 `maxFuzzyLevel` 參數也有助於傳回相關的相符項目。 若要將效能最大化並減少不尋常的結果，請將搜尋查詢設定為 `minFuzzyLevel=1` 和 `maxFuzzyLevel=2` 的預設值。 

    例如，當 `maxFuzzyLevel` 參數設定為 2 時，搜尋字詞 *restrant* 就會與 *restaurant* 相符。 您可以視需要覆寫預設的模糊等級。 

* 使用 `idxSet` 參數來排列一組確切結果類型的優先順序。 若要設定一組確切結果的優先順序，您可以提交以逗號分隔的索引清單。 在您的清單中，項目順序並不重要。 Azure 地圖服務支援下列索引：

* `Addr` - **地址範圍**：從街道起點和終點插入的地址點。 這些點會以地址範圍來表示。
* `Geo` - **地理位置**：土地的行政區。 例如，地理位置可以是國家/地區、州或城市。
* `PAD` - **點地址**：包含街道名稱和號碼的地址。 您可以在索引中找到點地址。 *Soquel Dr 2501* 即為一例。 點地址會提供適用於地址的最高等級精確度。  
* `POI` - **興趣點**：地圖上被視為值得注意的點，或者可能很有趣的點。 [搜尋地址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) \(部分機器翻譯\) 不會傳回 POI。  
* `Str` - **街道**：地圖上的街道。
* `XStr` - **交叉路口或十字路口**：兩個街道相交的接合處或地點。


#### <a name="usage-examples"></a>使用範例

* `idxSet=POI`：僅搜尋 POI。 

* `idxSet=PAD,Addr`：僅搜尋地址。 `PAD` 會標明點地址，而 `Addr` 會標明地址範圍。

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>針對地理實體類型進行反向地理編碼和篩選

當您在[反向搜尋地址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) \(部分機器翻譯\) 中進行反向地理編碼搜尋時，此服務可以傳回適用於行政區的多邊形。 若要將搜尋範圍縮小至特定地理實體類型，請在您的要求中包含 `entityType` 參數。 

產生的回應會包含地理識別碼及相符的實體類型。 如果您提供一個以上的實體，則端點會傳回「可用的最小實體」。 您可以使用傳回的幾何識別碼，透過[搜尋多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) \(部分機器翻譯\) 來取得地理位置的幾何。

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

使用 `language` 參數來設定所傳回搜尋結果的語言。 如果要求未設定語言，則根據預設，搜尋服務會使用在該國家或地區中最常見的語言。 當指定的語言中沒有可用資料時，即會使用預設語言。 

如需詳細資訊，請參閱 [Azure 地圖服務支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages) \(部分機器翻譯\)。


### <a name="use-predictive-mode-automatic-suggestions"></a>使用預測模式 (自動建議)

若要尋找更多符合部分查詢的項目，可將 `typeahead` 參數設定為 `true`。 此查詢會解譯為部分輸入，而搜尋會進入預測模式。 如果您未將 `typeahead` 參數設定為 `true`，則服務會假設所有相關資訊都已傳入。

在下列範例查詢中，會向搜尋地址服務查詢 *Microso*。 在這裡，`typeahead` 參數會設定為 `true`。 回應顯示搜尋服務已將查詢解譯為部分查詢。 回應會包含自動建議查詢的結果。

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


### <a name="encode-a-uri-to-handle-special-characters"></a>對 URI 進行編碼以處理特殊字元 

若要尋找十字路口的地址，您必須對 URI 進行編碼以處理地址中的特殊字元。 請考量此地址範例：*1st Avenue & Union Street, Seattle*。 在這裡，您會在傳送要求之前，先對表示 "and" 的符號字元 (`&`) 進行編碼。 

建議您對 URI 中的字元資料進行編碼。 在 URI 中，您可以使用百分比符號 (`%`) 和兩個字元的十六進位值 (對應至字元的 UTF-8 碼)，來對所有字元進行編碼。

#### <a name="usage-examples"></a>使用範例

從此地址開始：

```
query=1st Avenue & E 111th St, New York
```

對地址進行編碼：

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

您可以使用下列方法。

JavaScript 或 TypeScript：
```Javascript
encodeURIComponent(query)
```

C# 或 Visual Basic：
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

Ruby：
```Ruby
CGI::escape(query) 
```

Swift：
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go：
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>適用於 POI 搜尋的最佳做法

在 POI 搜尋中，您可以依名稱要求 POI 結果。 例如，您可以依名稱搜尋某家公司。 

強烈建議您使用 `countrySet` 參數，來指定應用程式需要涵蓋範圍的國家/地區。 預設行為是搜尋全世界。 這個廣泛的搜尋可能會傳回不必要的結果，而且搜尋可能會花很長的時間。

### <a name="brand-search"></a>品牌搜尋

為了改善結果的相關性和回應中的資訊，POI 搜尋回應會包含品牌資訊。 您可以使用此資訊，進一步剖析回應。

在要求中，您可以提交以逗號分隔的品牌名稱清單。 使用此清單，藉由設定 `brandSet` 參數來將結果限制為特定品牌。 在您的清單中，項目順序並不重要。 當您提供多個品牌清單時，傳回的結果必須至少屬於您清單的其中一個。

為了探索品牌搜尋，讓我們提出 [POI 類別搜尋](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) \(部分機器翻譯\) 要求。 在下列範例中，我們會尋找位於華盛頓州雷德蒙德市之 Microsoft 園區附近的加油站。 回應會針對每個傳回的 POI 顯示品牌資訊。

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

透過使用搜尋 POI API，您可以使用其官方代碼來尋找機場。 例如，您可以使用 *SEA* 來尋找西雅圖-塔科馬國際機場： 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>鄰近搜尋

若要擷取特定位置周圍的 POI 結果，您可以嘗試使用[搜尋鄰近地點 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) \(部分機器翻譯\)。 該端點只會傳回 POI 結果。 其不會接受搜尋查詢參數。 

若要限制結果，我們建議您設定半徑。

## <a name="understanding-the-responses"></a>了解回應

讓我們藉由對 Azure 地圖服務的搜尋服務提出地址搜尋要求，來尋找西雅圖的地址。 在下列要求 URL 中，我們將 `countrySet` 參數設定為 `US`，以搜尋美國的地址。

### <a name="sample-query"></a>範例查詢

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>支援的結果類型

* **點地址**：地圖上的點，其具有含街道名稱和號碼的特定地址。 點地址會提供地址的最高等級精確度。 

* **地址範圍**：從街道起點和終點插入的地址點範圍。  

* **Geography**：地圖上代表土地行政區的區域，例如國家/地區、州或城市。 

* **POI**：地圖上值得注意的點，或者可能很有趣的點。

* **街道**：地圖上的街道。 地址會解析為包含地址之街道的緯度與經度座標。 可能不會處理門牌號碼。 

* **交叉路口**：交集。 交叉路口代表兩個街道相交的接合處。

### <a name="response"></a>回應

讓我們看一下回應結構。 在接下來的回應中，結果物件的類型不同。 如果您仔細查看，就會看到三種類型的結果物件：

* 點地址
* Street
* 交叉路口

請注意，地址搜尋不會傳回 POI。  

每個回應物件的 `Score` 參數都指出，相符的分數如何與相同回應中其他物件的分數產生關聯。 如需回應物件參數的詳細資訊，請參閱[取得搜尋地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) \(部分機器翻譯\)。

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

「幾何」回應類型可以包含在 `geometry` 和 `id` 底下的 `dataSources` 物件中傳回的幾何識別碼。 例如，您可以使用[搜尋多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) \(部分機器翻譯\)，來要求 GeoJSON 格式的幾何資料。 藉由使用此格式，您可以針對一組實體取得城市或機場輪廓。 接著，可以使用此界線資料來[設定地理柵欄](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或[搜尋幾何內的 POI](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) \(部分機器翻譯\)。


對於[搜尋地址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) \(部分機器翻譯\) API 或[搜尋模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) \(部分機器翻譯\) API 的回應，可以包含在 `geometry` 和 `id` 底下的 `dataSources` 物件中傳回的幾何識別碼：


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>後續步驟

若要深入了解，請參閱：

> [!div class="nextstepaction"]
> [如何建置 Azure 地圖服務搜尋服務要求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [搜尋服務 API 文件](https://docs.microsoft.com/rest/api/maps/search)