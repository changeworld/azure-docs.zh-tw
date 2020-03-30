---
title: Azure 地圖搜索服務的最佳做法 |微軟 Azure 地圖
description: 瞭解如何在使用 Microsoft Azure 地圖中的搜索服務時應用最佳做法。
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335313"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure 地圖搜索服務的最佳做法

Azure 地圖[搜索服務](https://docs.microsoft.com/rest/api/maps/search)包括提供各種功能的 API。 例如，搜索位址 API 可以查找特定位置周圍的感興趣點 （POI） 或資料。 

本文介紹如何在從 Azure 地圖搜索服務調用資料時應用聲音實踐。 您將學習如何：

* 生成查詢以返回相關匹配項。
* 限制搜尋結果。
* 瞭解結果類型之間的差異。
* 讀取位址搜索-回應結構。

## <a name="prerequisites"></a>Prerequisites

要調用 Azure 地圖服務 API，需要 Azure 地圖帳戶和金鑰。 有關詳細資訊，請參閱[創建帳戶](quick-demo-map-app.md#create-an-account-with-azure-maps)並[獲取主金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)。 

有關 Azure 映射中的身份驗證的資訊，請參閱[在 Azure 映射中管理身份驗證](./how-to-manage-authentication.md)。

> [!TIP]
> 要查詢搜索服務，可以使用[Postman 應用](https://www.getpostman.com/apps)生成 REST 調用。 或者，您可以使用任何您喜歡的 API 開發環境。

## <a name="best-practices-to-geocode-addresses"></a>地理編碼位址的最佳做法

使用 Azure 地圖搜索服務搜索完整或部分位址時，API 將從搜索查詢中讀取關鍵字。 然後返回位址的經度和緯度座標。 此過程稱為*地理編碼*。 

國家/地區地理編碼的能力取決於道路資料的可用性和地理編碼服務的準確性。 有關按國家或地區對 Azure 地圖進行地理編碼功能的詳細資訊，請參閱[地理編碼覆蓋範圍](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)。

### <a name="limit-search-results"></a>限制搜尋結果

 Azure 地圖搜索 API 可以説明您適當地限制搜尋結果。 限制結果，以便向使用者顯示相關資料。

> [!NOTE]
> 搜索 API 支援更多的參數，而不僅僅是本文討論的參數。

#### <a name="geobiased-search-results"></a>地緣搜尋結果

要將地理偏置結果添加到使用者的相關區域，請始終添加盡可能多的位置詳細資訊。 您可能希望通過指定某些輸入類型來限制搜尋結果：

* 設置`countrySet`參數。 例如，您可以將其設置為`US,FR`。。 預設情況下，API 會搜索整個世界，以便返回不必要的結果。 如果查詢沒有`countrySet`參數，則搜索可能會返回不准確的結果。 例如，搜索名為*貝爾維尤*的城市返回來自美國和法國的結果，因為這兩個國家都包含一個名為*貝爾維尤*的城市。

* 可以使用 和`btmRight``topleft`參數設置邊界框。 這些參數將搜索限制為地圖上的特定區域。

* 要影響與結果相關的區域，請定義`lat`和`lon`座標參數。 使用`radius`參數設置搜索區域的半徑。


#### <a name="fuzzy-search-parameters"></a>模糊搜索參數

當不知道搜索查詢的使用者輸入時，我們建議您使用 Azure 地圖[搜索模糊 API。](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API 將 POI 搜索和地理編碼合併為規範*的單行搜索*： 

* `minFuzzyLevel`和`maxFuzzyLevel`參數説明返回相關匹配項，即使查詢參數與使用者需要的資訊不完全符合也是如此。 為了最大限度地提高性能和減少異常結果，請將搜索查詢設置為 和`minFuzzyLevel=1``maxFuzzyLevel=2`的預設值。 

    例如，當`maxFuzzyLevel`參數設置為 2 時，搜索術語*restrant*與*餐廳*匹配。 您可以在需要時覆蓋預設的模糊級別。 

* 使用`idxSet`參數確定結果類型的確切集的優先順序。 要確定一組確切結果的優先順序，可以提交逗號分隔的索引清單。 在清單中，物料訂單並不重要。 Azure 映射支援以下索引：

* `Addr` - **位址範圍**：從街道的開頭和結尾插值的位址點。 這些點表示為位址範圍。
* `Geo` - **地域**：土地的行政區劃。 例如，地理位置可以是國家/地區、州或城市。
* `PAD` - **點位址**：包含街道名稱和編號的位址。 點位址可以在索引中找到。 索*克爾博士2501就是一*個例子。 點位址為位址提供最高級別的精度。  
* `POI` - **興趣點**：地圖上被認為值得注意或可能有趣的點。 [搜索位址 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)不會返回 POIs。  
* `Str` - **街道**：地圖上的街道。
* `XStr` - **交叉街道或交叉點**：兩條街道相交的交匯點或地點。


#### <a name="usage-examples"></a>使用範例

* `idxSet=POI`- 僅搜索 POIs。 

* `idxSet=PAD,Addr`- 僅搜索位址。 `PAD`指示點位址，並`Addr`指示位址範圍。

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>地理實體類型的反向地理編碼和篩選器

在[搜索位址反向 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)中執行反向地理編碼搜索時，服務可以返回管理區域的多邊形。要將搜尋範圍縮小到特定的地理實體類型，請在`entityType`請求中包括參數。 

生成的回應包含地理 ID 和匹配的實體類型。 如果提供了多個實體，則終結點將返回*可用的最小實體*。 您可以使用返回的幾何 ID 通過[搜索多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)獲取地理的幾何體。

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

### <a name="set-the-results-language"></a>設置結果語言

使用`language`參數設置返回的搜尋結果的語言。 如果請求未設置該語言，則預設情況下，搜索服務使用國家或地區中最常見的語言。 當指定語言沒有可用的資料時，將使用預設語言。 

有關詳細資訊，請參閱[Azure 映射支援的語言](https://docs.microsoft.com/azure/azure-maps/supported-languages)。


### <a name="use-predictive-mode-automatic-suggestions"></a>使用預測模式（自動建議）

要查找部分查詢的更多匹配項，將`typeahead`參數設置為`true`。 此查詢被解釋為部分輸入，並且搜索進入預測模式。 如果未將`typeahead`參數設置為`true`，則服務假定已傳入所有相關資訊。

在以下依例查詢中，搜索位址服務將查詢*Microso*。 此處，參數`typeahead`設置為`true`。 回應顯示搜索服務將查詢解釋為部分查詢。 回應包含自動建議的查詢的結果。

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

要查找跨街位址，必須對 URI 進行編碼以處理位址中的特殊字元。 以這個位址為例：*西雅圖聯合街第一大道&聯合街*。 在這裡，在發送請求之前對安培`&`字元 （ ） 進行編碼。 

我們建議您在 URI 中對字元資料進行編碼。 在 URI 中，使用與字元的 UTF-8 代碼對應的百分比符號 （`%`） 和兩個字元十六進位值對所有字元進行編碼。

#### <a name="usage-examples"></a>使用範例

從此位址開始：

```
query=1st Avenue & E 111th St, New York
```

對位址進行編碼：

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

可以使用以下方法。

JavaScript 或類型腳本：
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

紅寶石：
```Ruby
CGI::escape(query) 
```

Swift：
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

去：
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>POI 搜索的最佳實踐

在 POI 搜索中，您可以按名稱請求 POI 結果。 例如，您可以按名稱搜索企業。 

我們強烈建議您使用該`countrySet`參數指定應用程式需要覆蓋的國家/地區。 預設行為是搜索整個世界。 此廣泛搜索可能會返回不必要的結果，並且搜索可能需要很長時間。

### <a name="brand-search"></a>品牌搜索

為了提高結果和回應中資訊的相關性，POI 搜索回應包括品牌資訊。 您可以使用此資訊進一步分析回應。

在請求中，您可以提交逗號分隔的品牌名稱清單。 使用清單通過設置`brandSet`參數將結果限制為特定品牌。 在清單中，物料訂單並不重要。 當您提供多個品牌清單時，返回的結果必須至少屬於您的一個清單。

為了探索品牌搜索，讓我們提出[一個POI類別搜索](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory)請求。 在下面的示例中，我們在華盛頓雷德蒙德的 Microsoft 園區附近尋找加油站。 回應顯示返回的每個 POI 的品牌資訊。

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


### <a name="airport-search"></a>機場搜索

通過使用搜索 POI API，您可以使用其官方代碼查找機場。 例如，您可以使用*SEA*查找西雅圖-塔科馬國際機場： 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>鄰近搜尋

若要在特定位置周圍檢索 POI 結果，可以嘗試使用["搜索附近"API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)。 終結點僅返回 POI 結果。 它不採用搜索查詢參數。 

要限制結果，我們建議您設置半徑。

## <a name="understanding-the-responses"></a>瞭解回應

讓我們通過向 Azure 地圖搜索服務發出位址搜索請求來查找西雅圖的位址。 在以下請求 URL 中，我們將`countrySet`參數`US`設置為搜索美國的位址。

### <a name="sample-query"></a>範例查詢

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>支援的結果類型

* **點位址**：地圖上具有街道名稱和編號的特定位址的點。 點位址為位址提供最高級別的精度。 

* **位址範圍**：從街道的開頭和結尾插值的位址點的範圍。  

* **地理位置**：地圖上表示土地行政區劃的區域，例如國家、州或城市。 

* **POI**：地圖上值得注意的點，可能很有趣。

* **街道**：地圖上的街道。 位址解析為包含位址的街道的緯度和經度座標。 房屋號碼可能無法處理。 

* **十字街**：交叉路口。 交叉街道表示兩條街道相交的交匯點。

### <a name="response"></a>回應

讓我們來看看回應結構。 在隨後的回應中，結果物件的類型不同。 如果仔細查看，您將看到三種類型的結果物件：

* 點位址
* Street
* 十字街

請注意，位址搜索不會返回 POIs。  

每個`Score`回應物件的參數指示匹配分數如何與同一回應中其他物件的分數相關。 有關回應物件參數的詳細資訊，請參閱[獲取搜索位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)。

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

*幾何體的*回應類型可以包括在 和`dataSources``geometry``id`下的物件中返回的幾何 ID。 例如，可以使用[搜索多邊形服務](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)以 GeoJSON 格式請求幾何資料。 通過使用此格式，您可以獲取一組實體的城市或機場大綱。 然後，可以使用此邊界資料在幾何體內[設置地理圍欄](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence)或[搜索 POIs。](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)


[搜索位址](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)API 或[搜索模糊](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)API 的回應可以包括在 和 下`dataSources``geometry`的物件中返回的幾何`id`ID 和 ：


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>後續步驟

要瞭解更多資訊，請參閱：

> [!div class="nextstepaction"]
> [如何生成 Azure 地圖搜索服務請求](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [搜索服務 API 文檔](https://docs.microsoft.com/rest/api/maps/search)