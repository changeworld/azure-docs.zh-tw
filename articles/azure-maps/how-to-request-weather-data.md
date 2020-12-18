---
title: 使用 Azure 地圖服務天氣服務 (預覽) 要求即時和預測的氣象資料
description: '瞭解如何使用 Microsoft Azure Maps 天氣服務 (Preview，要求目前的 (目前) 和預測 (分鐘、每小時、每日) 天氣資料) '
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fe1b337fe3e1dcf499f9a7428f66543108d0c050
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680417"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services-preview"></a>使用 Azure 地圖服務天氣服務 (預覽) 要求即時和預測的氣象資料 

> [!IMPORTANT]
> Azure 地圖服務的氣象服務目前為公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure 地圖服務 [氣象服務](/rest/api/maps/weather) 是一組 RESTful 的 api，可讓開發人員將高度動態歷程記錄、即時和預測的氣象資料和視覺效果整合到其解決方案中。 在本文中，我們將示範如何要求即時和預測的氣象資料。

在本文中，您將瞭解如何：

* 使用「 [取得目前的條件」 API](/rest/api/maps/weather/getcurrentconditionspreview)，要求即時 (目前) 氣象資料。
* 使用「 [取得嚴重天氣警示 API](/rest/api/maps/weather/getsevereweatheralertspreview)」來要求嚴重的氣象警示。
* 使用 [取得每日預測 API](/rest/api/maps/weather/getdailyforecastpreview)來要求每日預測。
* 使用 [取得每小時的預測 API](/rest/api/maps/weather/gethourlyforecastpreview)來要求每小時預測。
* 使用「 [取得分鐘預測 API](/rest/api/maps/weather/getminuteforecastpreview)」來要求以分鐘為單位的預測。

這段影片提供對 Azure 地圖服務氣象服務進行 REST 呼叫的範例。

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。 如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

    >[!IMPORTANT]
    >[取得分鐘預測 API](/rest/api/maps/weather/getminuteforecastpreview)需要 S1 定價層金鑰。 所有其他 Api 都需要 S0 定價層金鑰。

本教學課程使用 [Postman](https://www.postman.com/) 應用程式，但您可以選擇不同的 API 開發環境。

## <a name="request-real-time-weather-data"></a>要求即時天氣資料

[取得目前的條件 API](/rest/api/maps/weather/getcurrentconditionspreview)會傳回特定座標位置的詳細天氣狀況，例如降雨、溫度和風。 此外，您可以抓取過去6或24小時內的特定位置觀察。 回應包括觀察日期和時間、天氣狀況的簡短描述、天氣圖標、降雨指標旗標和溫度等詳細資料。 也會傳回 RealFeel™溫度和 ultraviolet (UV) 索引。

在此範例中，您將使用「 [取得目前的條件」 API](/rest/api/maps/weather/getcurrentconditionspreview) ，在位於華盛頓州西雅圖的座標處取得目前天氣狀況。

1. 開啟 Postman 應用程式。 在 Postman 應用程式頂端處，選取 [新增]。 在 [新建] 視窗中，選取 [集合]。  為集合命名，然後選取 [建立] 按鈕。 本檔中的其餘範例將使用此集合。

2. 若要建立要求，請再次選取 [新增]。 在 [新建] 視窗中，選取 [要求]。 輸入要求的 [要求名稱]。 選取您在先前的步驟中建立的集合，然後選取 [儲存]。

3. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. 按一下藍色的 [ **傳送** ] 按鈕。 回應主體包含目前的氣象資訊。

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>要求嚴重的氣象警示

[Azure 地圖服務取得嚴重的氣象警示 API](/rest/api/maps/weather/getsevereweatheralertspreview) ，會傳回來自全球各地政府氣象機關和領導全球、區域氣象警示提供者所提供的重大氣象警示。 服務可以傳回如警示類型、類別、層級，以及有關所要求位置之作用中嚴重警示的詳細說明，例如颶風、thunderstorms、閃電、熱度波或樹系引發。 例如，物流經理可以將地圖上的嚴重天氣狀況，以及商務位置和規劃的路線視覺化，並進一步協調驅動程式和本機背景工作。

在此範例中，您將使用「 [取得嚴重天氣警示 API](/rest/api/maps/weather/getsevereweatheralertspreview) 」來取得位於 CHEYENNE，WY 中的座標目前天氣狀況。

>[!NOTE]
>此範例會在撰寫本文時，捕獲嚴重的氣象警示。 要求的地點可能不再有任何嚴重的氣象警示。 若要在執行此範例時取出實際的嚴重警示資料，您必須在不同的座標位置取得資料。

1. 開啟 Postman 應用程式，按一下 [ **新增**]，然後選取 [ **要求**]。 輸入要求的 [要求名稱]。 選取您在上一節中建立的集合，或建立一個新的集合，然後選取 [ **儲存**]。

2. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 按一下藍色的 [ **傳送** ] 按鈕。 如果沒有任何嚴重的氣象警示，回應主體會包含空的 `results[]` 陣列。 如果有嚴重的氣象警示，回應主體會包含類似下列 JSON 回應的內容：

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>要求每日氣象預報資料

[取得每日預測 API](/rest/api/maps/weather/getdailyforecastpreview)會傳回詳細的每日天氣預報，例如溫度和風。 要求可以指定要傳回的天數：1、5、10、15、25或45天的指定座標位置。 回應包括溫度、風、降雨、空氣品質和 UV 指數等詳細資料。  在此範例中，我們會透過設定來要求五天 `duration=5` 。

>[!IMPORTANT]
>在 S0 定價層中，您可以要求下1、5、10和15天的每日預測。 在 S1 定價層中，您也可以要求未來25天和45天的每日預測。

在此範例中，您將使用「 [取得每日預測 API](/rest/api/maps/weather/getdailyforecastpreview) 」來取得位於西雅圖華盛頓州之座標的五天氣象預測。

1. 開啟 Postman 應用程式，按一下 [ **新增**]，然後選取 [ **要求**]。 輸入要求的 [要求名稱]。 選取您在上一節中建立的集合，或建立一個新的集合，然後選取 [ **儲存**]。

2. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 按一下藍色的 [ **傳送** ] 按鈕。 回應主體包含五天的氣象預報資料。 為了簡潔起見，下列 JSON 回應顯示了第一天的預測。
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>要求每小時的氣象預測資料

「 [取得每小時的預測 API](/rest/api/maps/weather/gethourlyforecastpreview) 」會針對指定座標位置的下1、12、24 (1 天) 、72 (3 天) 、120 (5 天) 和240小時 (10 天) 傳回詳細氣象預測。 API 會傳回溫度、濕度、風、降雨和 UV 索引等詳細資料。

>[!IMPORTANT]
>在 S0 定價層中，您可以針對接下來的1、12、24小時 (1 天) 和72小時 (3 天) 要求每小時的預測。 在 S1 定價層中，您也可以針對接下來的 120 (5 天) 和240小時 (10 天) 要求每小時的預測。

在此範例中，您將使用「 [取得每小時的預測 API](/rest/api/maps/weather/gethourlyforecastpreview) 」，在位於華盛頓州西雅圖的座標上，取得接下來12小時的每小時氣象預報。

1. 開啟 Postman 應用程式，按一下 [ **新增**]，然後選取 [ **要求**]。 輸入要求的 [要求名稱]。 選取您在上一節中建立的集合，或建立一個新的集合，然後選取 [ **儲存**]。

2. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 按一下藍色的 [ **傳送** ] 按鈕。 回應主體包含接下來12小時的氣象預報資料。 為了簡潔起見，下列 JSON 回應顯示第一個小時的預測。

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>要求每分鐘的氣象預測資料

 [取得分鐘預測 API](/rest/api/maps/weather/getminuteforecastpreview)會針對下一個120分鐘的指定位置傳回每分鐘的預測。 使用者可以在1、5和15分鐘的間隔內要求氣象預測。 回應包括 (降雨類型的詳細資料，包括雨、雪，或) 、開始時間和降雨強度值的混合 (dBZ) 。

在此範例中，您將使用「 [取得分鐘預測」 API](/rest/api/maps/weather/getminuteforecastpreview) ，在位於華盛頓州西雅圖的座標處取得每分鐘的氣象預測。 天氣預測會在接下來的120分鐘內提供。 我們的查詢要求以15分鐘的間隔提供預測，但您可以將參數調整為1或5分鐘。

1. 開啟 Postman 應用程式，按一下 [ **新增**]，然後選取 [ **要求**]。 輸入要求的 [要求名稱]。 選取您在上一節中建立的集合，或建立一個新的集合，然後選取 [ **儲存**]。

2. 在 [建立器] 索引標籤中選取 [ **取得** HTTP 方法]，並輸入下列 URL。 對於此要求以及本文中提及的其他要求，請將 `{Azure-Maps-Primary-Subscription-key}` 取代為您的主要訂用帳戶金鑰。

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. 按一下藍色的 [ **傳送** ] 按鈕。 回應主體會在15分鐘的間隔內，包含下一個120分鐘的氣象預報資料。

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure 地圖服務天氣服務 (預覽) 概念](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure 地圖服務天氣服務 (預覽) REST API](/rest/api/maps/weather)