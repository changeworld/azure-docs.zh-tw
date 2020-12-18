---
title: 顯示 Android 地圖上的交通資料 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Android SDK 來顯示地圖上的流量資料。
author: rbrundritt
ms.author: richbrun
ms.date: 12/04/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 113f39ac2976b870c9e07851cdd0919e2578940f
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680459"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>在地圖上顯示流量資料 (Android SDK) 

Flow 資料和事件資料是可在地圖上顯示的兩種類型的流量資料。 本指南說明如何顯示這兩種類型的流量資料。 事件資料包含資料點和以行為基礎的資料，例如結構、路標和事故。 流程資料會顯示有關道路流量的計量。

## <a name="prerequisites"></a>必要條件

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。 本文中的程式碼區塊可以插入 maps `onReady` 事件處理常式中。

## <a name="show-traffic-on-the-map"></a>在地圖上顯示路況

Azure 地圖服務可用的路況資料類型有兩種：

- 事件資料 - 包含以點和線為基礎的資料，用於施工、道路封閉和事故等事項。
- 車流資料 - 提供有關道路路況車流的計量。 車流資料通常用來為道路上色。 這些色彩是以有多少路況正在減慢車流為基礎 (相對於速度限制或另一個計量)。 有四個值可傳遞至地圖的流量 `flow` 選項。

    |流程值 | 描述|
    | :-- | :-- |
    | TrafficFlow。無 | 不顯示地圖上的交通資料 |
    | TrafficFlow。相對 | 顯示相對於道路的自由流動速度的流量資料 |
    | TrafficFlow.RELATIVE_DELAY | 顯示比平均預期延遲更慢的區域 |
    | TrafficFlow，絕對 | 顯示所有車輛在道路上的絕對速度 |

下列程式碼顯示如何在地圖上顯示路況資料。

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

下列螢幕擷取畫面顯示上述程式碼轉譯地圖上的即時交通資訊。

![顯示即時流量資訊的地圖](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>取得流量事件詳細資料

流量事件的詳細資料可在用來在地圖上顯示事件的功能屬性中取得。 使用 Azure 地圖服務流量事件向量磚服務，將流量事件新增至地圖。 這些向量圖格中的資料格式（如 [這裡所述](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)）。 下列程式碼會將 click 事件新增至地圖，並抓取已按下的「流量事件」功能，並顯示快顯訊息與部分詳細資料。

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

下列螢幕擷取畫面顯示上述程式碼轉譯地圖上的即時交通資訊，以及顯示事件詳細資料的快顯訊息。

![顯示即時交通資訊的地圖，以及顯示事件詳細資料的快顯訊息](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>後續步驟

若要瞭解如何將更多資料新增至您的地圖，請參閱下列指南：

> [!div class="nextstepaction"]
> [新增圖格圖層](how-to-add-tile-layer-android-map.md)
