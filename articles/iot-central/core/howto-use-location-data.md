---
title: 在 Azure IoT Central 解決方案中使用位置資料
description: 瞭解如何使用從連線至 IoT Central 應用程式的裝置所傳送的位置資料。 在地圖上繪製位置資料，或建立地理柵欄規則。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c909fd1438488e3625f3674dd26f959cf6fad79f
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127998"
---
# <a name="use-location-data-in-an-azure-iot-central-solution"></a>在 Azure IoT Central 解決方案中使用位置資料

本文說明如何在 IoT Central 應用程式中使用位置資料。 連線至 IoT Central 的裝置可以將位置資料傳送為遙測串流，或使用裝置屬性來報告位置資料。

解決方案產生器可以使用位置資料來：

* 將報告的位置繪製在地圖上。
* 繪製遙測位置記錄 om 的地圖。
* 建立地理柵欄規則，以在裝置進入或離開特定區域時通知操作員。

## <a name="add-location-capabilities-to-a-device-template"></a>將位置功能新增至裝置範本

下列螢幕擷取畫面顯示裝置範本，其中包含使用位置資料的裝置屬性和遙測類型範例。 定義會使用位置語義型別和 **地理****位置** 架構類型：

:::image type="content" source="media/howto-use-location-data/location-device-template.png" alt-text="顯示裝置範本中位置屬性定義的螢幕擷取畫面":::

如需參考， [數位 Twins 定義語言 (DTDL ](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) 這些功能的定義) 定義如下列程式碼片段所示：

```json
{
  "@type": [
    "Property",
    "Location"
  ],
  "displayName": {
    "en": "DeviceLocation"
  },
  "name": "DeviceLocation",
  "schema": "geopoint",
  "writable": false
},
{
  "@type": [
    "Telemetry",
    "Location"
  ],
  "displayName": {
    "en": "Tracking"
  },
  "name": "Tracking",
  "schema": "geopoint"
}
```

> [!NOTE]
> **Geopoint** 架構類型不是 [DTDL 規格](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)的一部分。 IoT Central 目前支援 **geopoint** 架構型別和 **位置** 語義型別，以提供回溯相容性。

## <a name="send-location-data-from-a-device"></a>從裝置傳送位置資料

當裝置針對上一節所示的 **DeviceLocation** 屬性傳送資料時，承載看起來會像下列 JSON 程式碼片段：

```json
{
  "DeviceLocation": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

當裝置針對上一節顯示的 **追蹤** 遙測資料傳送資料時，承載看起來會像下列 JSON 程式碼片段：

```json
{
  "Tracking": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

## <a name="display-device-location"></a>顯示裝置位置

您可以在 IoT Central 應用程式中的多個位置顯示位置資料。 例如，在與個別裝置或儀表板相關聯的視圖上。

當您建立裝置的視圖時，您可以選擇在地圖上繪製位置，或顯示個別的值：

:::image type="content" source="media/howto-use-location-data/location-views.png" alt-text="顯示位置資料範例視圖的螢幕擷取畫面":::

您可以將地圖底圖新增至儀表板，以繪製一或多個裝置的位置。 當您新增地圖底圖來顯示位置遙測時，您可以在一段時間內繪製位置。 下列螢幕擷取畫面顯示模擬裝置在過去30分鐘內回報的位置：

:::image type="content" source="media/howto-use-location-data/location-dashboard.png" alt-text="顯示範例儀表板的螢幕擷取畫面，其中包含位置資料":::

## <a name="create-a-geofencing-rule"></a>建立地理柵欄規則

您可以使用位置遙測來建立地理柵欄規則，以在裝置移入或移出矩形區域時產生警示。 下列螢幕擷取畫面顯示的規則會使用四個條件來定義使用緯度和經度值的矩形區域。 當裝置移至矩形區域時，此規則會產生電子郵件：

:::image type="content" source="media/howto-use-location-data/geofence-rule.png" alt-text="顯示地理柵欄規則定義的螢幕擷取畫面":::

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何在 Azure IoT Central 應用程式中使用屬性，請參閱：

* [內容](concepts-telemetry-properties-commands.md)
* [建立用戶端應用程式並將其連線到您的 Azure IoT Central 應用程式](tutorial-connect-device.md)