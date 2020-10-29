---
title: 教學課程：使用 Azure Notebooks (Python) 來聯結感應器資料與氣象預報資料 | Microsoft Azure 地圖服務
description: 本教學課程關於如何使用 Azure Notebooks (Python) 來聯結感應器資料與得自 Microsoft Azure 地圖服務氣象服務的氣象預報資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/29/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: f020f3d9e23b9f834fd203f6d030656581fb4416
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896594"
---
# <a name="tutorial-join-sensor-data-with-weather-forecast-data-by-using-azure-notebooks-python"></a>教學課程：使用 Azure Notebooks (Python) 來聯結感應器資料與氣象預報資料

風力發電可替代化石燃料來作為能源來源，以對抗氣候變遷。 由於風本身並不是固定的，因此，風力發電操作員必須建立機器學習 (ML) 模型來預測風力。 此預測是符合電力需求及確保電網穩定性所必需的。 在本教學課程中，我們將逐步解說如何將 Azure 地圖服務的氣象預報資料與天候數據的示範資料結合在一起。 藉由呼叫 Azure 地圖服務的氣象服務，即可要求氣象預報資料。

在本教學課程中，您將：

> [!div class="checklist"]
> * 使用雲端中 [Azure Notebooks](../notebooks/index.yml) 的資料檔案。
> * 從檔案載入示範資料。
> * 在 Python 中呼叫 Azure 地圖服務 REST API。
> * 在地圖上呈現位置資料。
> * 使用 Azure 地圖服務的[每日預報](/rest/api/maps/weather/getdailyforecastpreview)天氣資料來擴充示範資料。
> * 在圖表中繪製預報資料。


## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您需要先：

1. 依照[建立帳戶](quick-demo-map-app.md#create-an-azure-maps-account)中的指示，在 S0 定價層中建立 Azure 地圖服務帳戶訂用帳戶。
2. 依照[取得主要金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)中的指示，取得帳戶的主要訂用帳戶金鑰。


如需 Azure 地圖服務中驗證的詳細資訊，請參閱[管理 Azure 地圖服務中的驗證](./how-to-manage-authentication.md)。

若要熟悉 Azure Notebooks 以及了解如何開始使用，請遵循[建立 Azure Notebook](./tutorial-ev-routing.md#create-an-azure-notebooks-project) 中的指示。

> [!Note]
> 此專案的 Jupyter 筆記本檔案可從[氣象地圖 Jupyter 筆記本存放庫](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data)下載。

## <a name="load-the-required-modules-and-frameworks"></a>載入必要的模組和架構

若要載入所有必要的模組和架構，請執行下列指令碼：

```python
import pandas as pd
import datetime
from IPython.display import Image, display
!pip install aiohttp
import aiohttp
```

## <a name="import-weather-data"></a>匯入天氣資料

為了進行本教學課程，我們將利用安裝在四個不同風力發電機組上的感應器所傳來的天氣資料數據。 範例資料包含 30 天的天氣數據。 這些數據是從每個發電機組位置附近的天氣資料中心收集而來。 示範資料包含溫度、風速和風向的資料數據。 您可以從[這裡](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/tree/master/AzureMapsJupyterSamples/Tutorials/Analyze%20Weather%20Data/data)下載示範資料。 下列指令碼會將示範資料匯入到 Azure Notebook。

```python
df = pd.read_csv("./data/weather_dataset_demo.csv")
```

## <a name="request-daily-forecast-data"></a>要求每日預報資料

在我們的案例中，我們會要求每個感應器位置的每日預報。 下列指令碼會呼叫 Azure 地圖服務氣象服務的[每日預測 API](/rest/api/maps/weather/getdailyforecastpreview)。 此 API 會針對每個風力發電機，傳回目前日期後 15 天內的氣象預報。


```python
subscription_key = "Your Azure Maps key"

# Get a lists of unique station IDs and their coordinates 
station_ids = pd.unique(df[['StationID']].values.ravel())
coords = pd.unique(df[['latitude','longitude']].values.ravel())

years,months,days = [],[],[]
dates_check=set()
wind_speeds, wind_direction = [], []

# Call azure maps weather service to get daily forecast data for 15 days from current date
session = aiohttp.ClientSession()
j=-1
for i in range(0, len(coords), 2):
    wind_speeds.append([])
    wind_direction.append([])
    
    query = str(coords[i])+', '+str(coords[i+1])
    forecast_response = await(await session.get("https://atlas.microsoft.com/weather/forecast/daily/json?query={}&api-version=1.0&subscription-key={}&duration=15".format(query, subscription_key))).json()
    j+=1
    for day in range(len(forecast_response['forecasts'])):
            date = forecast_response['forecasts'][day]['date'][:10]
            wind_speeds[j].append(forecast_response['forecasts'][day]['day']['wind']['speed']['value'])
            wind_direction[j].append(forecast_response['forecasts'][day]['day']['windGust']['direction']['degrees'])
            
            if date not in dates_check:
                year,month,day= date.split('-')
                years.append(year)
                months.append(month)
                days.append(day)
                dates_check.add(date)
            
await session.close()
```

下列指令碼會藉由呼叫 Azure 地圖服務的[取得地圖影像服務](/rest/api/maps/render/getmapimage)，在地圖上呈現發電機組的所在位置。

```python
# Render the turbine locations on the map by calling the Azure Maps Get Map Image service
session = aiohttp.ClientSession()

pins="default|la-25+60|ls12|lc003C62|co9B2F15||'Location A'{} {}|'Location B'{} {}|'Location C'{} {}|'Location D'{} {}".format(coords[1],coords[0],coords[3],coords[2],coords[5],coords[4], coords[7],coords[6])

image_response = "https://atlas.microsoft.com/map/static/png?subscription-key={}&api-version=1.0&layer=basic&style=main&zoom=6&center={},{}&pins={}".format(subscription_key,coords[7],coords[6],pins)

static_map_response = await session.get(image_response)

poi_range_map = await static_map_response.content.read()

await session.close()

display(Image(poi_range_map))
```

![發電機組的所在位置](./media/weather-service-tutorial/location-map.png)


我們會根據工作站的識別碼，將示範資料分組成預測資料。 工作站識別碼代表天氣資料中心。 此分組動作會使用預測資料來擴充示範資料。

```python
# Group forecasted data for all locations
df = df.reset_index(drop=True)
forecast_data = pd.DataFrame(columns=['StationID','latitude','longitude','Year','Month','Day','DryBulbCelsius','WetBulbFarenheit','WetBulbCelsius','DewPointFarenheit','DewPointCelsius','RelativeHumidity','WindSpeed','WindDirection'])

for i in range(len(station_ids)):
    loc_forecast = pd.DataFrame({'StationID':station_ids[i], 'latitude':coords[0], 'longitude':coords[1], 'Year':years, 'Month':months, 'Day':days, 'WindSpeed':wind_speeds[i], 'WindDirection':wind_direction[i]})
    forecast_data = pd.concat([forecast_data,loc_forecast], axis=0, sort=False)
    
combined_weather_data = pd.concat([df,forecast_data])
grouped_weather_data = combined_weather_data.groupby(['StationID'])
```

下表顯示其中一個發電機組所在位置的歷史和預報合併資料。

```python
# Display data for first location
grouped_weather_data.get_group(station_ids[0]).reset_index()
```

<center>

![已分組的資料](./media/weather-service-tutorial/grouped-data.png)</center>

## <a name="plot-forecast-data"></a>繪製預報資料

我們會根據預測日期，將預測值繪製成圖。 此圖可讓我們在接下來的 15 天內查看風的速度和方向變更。

```python
# Plot wind speed
curr_date = datetime.datetime.now().date()
windsPlot_df = pd.DataFrame({ 'Location A': wind_speeds[0], 'Location B': wind_speeds[1], 'Location C': wind_speeds[2], 'Location D': wind_speeds[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind speed")
```

```python
#Plot wind direction 
windsPlot_df = pd.DataFrame({ 'Location A': wind_direction[0], 'Location B': wind_direction[1], 'Location C': wind_direction[2], 'Location D': wind_direction[3]}, index=pd.date_range(curr_date,periods=15))
windsPlot = windsPlot_df.plot.line()
windsPlot.set_xlabel("Date")
windsPlot.set_ylabel("Wind direction")
```

下圖將預測資料視覺化。 針對風速的變更，請參閱左側圖表。 針對風向的變更，請參閱右側圖表。 這些資料是資料索取當天後 15 天的預測。

<center>

![風速圖](./media/weather-service-tutorial/speed-date-plot.png) ![風向圖](./media/weather-service-tutorial/direction-date-plot.png)</center>

在本教學課程中，您已了解如何呼叫 Azure 地圖服務 REST API 來取得氣象預報資料。 您也了解如何將圖表上的資料視覺化。

若要深入了解如何在 Azure Notebooks 內呼叫 Azure 地圖服務 REST API，請參閱[使用 Azure Notebooks 的 EV 路由](./tutorial-ev-routing.md)。

若要探索此教學課程中所使用的 Azure 地圖服務 API，請參閱：

* [每日預報](/rest/api/maps/weather/getdailyforecastpreview)
* [轉譯 - 取得地圖影像](/rest/api/maps/render/getmapimage)

如需 Azure 地圖服務 REST API 的完整清單，請參閱 [Azure 地圖服務 REST API](./consumption-model.md) \(部分機器翻譯\)。

## <a name="next-steps"></a>後續步驟

若要深入了解 Azure Notebooks，請參閱

> [!div class="nextstepaction"]
> [Azure Notebooks](../notebooks/index.yml)