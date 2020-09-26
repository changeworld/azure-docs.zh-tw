---
title: 如何使用 Azure IoT Central 解決方案中的屬性
description: 如何在 Azure IoT Central 解決方案中使用唯讀和可寫入屬性
author: v-krghan
ms.author: v-krghan
ms.date: 08/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: fa9b07d80c34ec26ca920fe147ada8f8ef7f2fd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342566"
---
# <a name="how-to-use-properties-in-an-azure-iot-central-solution"></a>如何使用 Azure IoT Central 解決方案中的屬性

本文說明如何在 Azure IoT Central 應用程式中使用裝置範本中定義的裝置屬性。

屬性代表時間點值。 例如，裝置可以使用屬性來報告其嘗試到達的目標溫度。 屬性也可讓您同步處理您的裝置與 IoT Central 應用程式之間的狀態。  您可以從 IoT Central 設定可寫入屬性。

您也可以在 IoT Central 應用程式中定義雲端屬性。 雲端屬性值絕對不會與裝置交換，而且不在本文的討論範圍內。

## <a name="define-your-properties"></a>定義您的屬性

屬性是代表裝置狀態的資料欄位。 使用屬性來代表裝置的持久狀態，例如裝置的關閉狀態。 屬性也可以代表基本裝置屬性，例如裝置的軟體版本。 您可將屬性宣告為唯讀或可寫入。

下列螢幕擷取畫面顯示 Azure IoT Central 應用程式中的屬性定義

![定義屬性](./media/howto-use-properties/property-definition.png)

下表顯示屬性功能的組態設定：

| 欄位           | 說明                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 顯示名稱    | 儀表板和表單上所用屬性值的顯示名稱。                                                                                                                                                              |
| 名稱            | 屬性的名稱。 IoT Central 會從顯示名稱產生此欄位的值，但如有必要，您也可以選擇自己所要使用的值。 此欄位必須是英數位元。                                                 |
| 功能類型 | 屬性。                                                                                                                                                                                                                          |
| 語意類型   | 屬性的語意類型，例如溫度、狀態或事件。 所選擇的語意類型會決定下列哪些欄位可供使用。                                                                       |
| 結構描述          | 屬性的資料類型，例如雙精度浮點數、字串或向量。 可用的選項取決於語意類型。 結構描述不適用於事件和狀態這兩種語意類型。                                               |
| 可寫入       | 如果屬性無法寫入，裝置可以向 IoT Central 報告屬性值。 如果屬性可寫入，則裝置可以向 IoT Central 報告屬性值，IoT Central 也可以向裝置傳送屬性更新。 |
| 嚴重性        | 僅適用於事件語意類型。 嚴重性為 [錯誤]****、[資訊]**** 或 [警告]****。                                                                                                                         |
| 狀態值    | 僅適用於狀態語意類型。 會定義可能的狀態值，每個狀態值都會有顯示名稱、名稱、列舉類型和值。                                                                                   |
| 單位            | 屬性值的單位，例如 **>mph**、 **%** 或** &deg; C**。                                                                                                                                                              |
| 顯示單位    | 要在儀表板和表單上使用的顯示單位。                                                                                                                                                                                    |
| 註解         | 屬性功能的任何相關註解。                                                                                                                                                                                        |
| 說明     | 屬性功能的說明。                                                                                                                                                                                          |

屬性也可以在裝置範本的介面中定義，如下所示：

``` json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
 "@type": "Property",
 "displayName": "Date ",
 "description": "The date on which the device is currently operating",
 "name": "date",
 "writable": true,
 "schema": "date"
},
{ 
 "@type": "Property",
 "displayName": "Location",
 "description": "The current location of the device",
 "name": "location",
 "writable": true,
 "schema": "geopoint"
},
{
 "@type": "Property",
 "displayName": "Vector Level",
 "description": "The Vector level of the device",
 "name": "vector",
 "writable": true,
 "schema": "vector"
}
```

此範例顯示五個屬性，這些屬性可以與 UI 中的屬性定義相關，如下所示：

* `@type` 若要指定功能的類型： `Property`
* `name` 做為屬性值。
* `schema` 指定屬性的資料類型。 這個值可以是基本類型，例如 double、integer、boolean 或 string。 也支援複雜物件類型、陣列和對應。
* `writable` 依預設，屬性為唯讀。 您可以使用此欄位將屬性標示為可寫入

選擇性欄位（例如 [顯示名稱] 和 [描述]）可讓您將更多詳細資料新增至介面和功能。

當您建立屬性時，您可以指定複雜的 **架構** 類型，例如 Object、Enum 等等。

![新增功能](./media/howto-use-properties/property.png)

當您選取複雜的架構（例如 **物件**）時，也需要定義物件。

![定義物件](./media/howto-use-properties/object.png)

下列程式碼顯示物件屬性類型的定義。 此物件具有兩個類型為 string 和 integer 的欄位：

``` json
{
  "@id": "<element id>",
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@id": "<element id>",
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "@id": "<element id>",
        "@type": "SchemaField",
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>執行唯讀屬性

依預設，屬性為唯讀。 唯讀屬性表示裝置會向您的 IoT Central 應用程式報告屬性值更新。 您的 IoT Central 應用程式無法設定唯讀屬性的值。

IoT Central 使用裝置對應項來同步處理裝置與 IoT Central 應用程式之間的屬性值。 裝置屬性值會使用裝置對應項報告屬性。 如需詳細資訊，請參閱 [裝置 twins](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)

下列來自裝置功能模型的程式碼片段會顯示唯讀屬性類型的定義：

``` json
{
  "@type": "Property",
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

裝置會將唯讀屬性傳送至 IoT Central。 屬性會以 JSON 承載的形式傳送。如需詳細資訊 [，請參閱](./concepts-telemetry-properties-commands.md)承載。

您可以使用 Azure IoT 裝置 SDK，將屬性更新傳送至您的 IoT Central 應用程式。

您可以使用下列函數將裝置對應項屬性傳送至 Azure IoT Central 應用程式：

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

本文使用 Node.js 為簡單起見，如需有關裝置應用程式範例的完整資訊，請參閱 [建立並將用戶端應用程式連接到您的 Azure IoT Central 應用程式 ( # A1) ](tutorial-connect-device-nodejs.md) 以及 [建立用戶端應用程式，並將其連接至 Azure IoT Central 應用程式 (Python) ](tutorial-connect-device-python.md) 教學課程。

下列 Azure IoT Central 應用程式中的視圖會顯示內容，您可以看到 [view] 會自動讓裝置模型屬性成為 _唯讀裝置屬性_。

![唯讀屬性的視圖](./media/howto-use-properties/read-only.png)

## <a name="implement-writable-properties"></a>執行可寫入屬性

可寫入的屬性是由表單上 IoT Central 應用程式中的運算子所設定。 IoT Central 將屬性傳送至裝置。 IoT Central 預期會有來自裝置的確認。 

下列來自裝置功能模型的程式碼片段會顯示可寫入屬性類型的定義：

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

裝置用戶端應該傳送看起來像下列範例的 JSON 承載，作為裝置對應項中的報告屬性：

``` json
{ "Brightness Level": 2 }
```

若要定義及處理您的裝置所回應的可寫入屬性，您可以使用下列程式碼。

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

回應訊息應包含 `ac` 和 `av` 欄位。 `ad` 是選擇性欄位。 如需範例，請參閱下列程式碼片段。

* `ac` 是使用下表中的值的數值欄位：

* `av` 是傳送給裝置的版本號碼。

* `ad` 這是選項字串描述。

| 值 | 標籤 | 描述 |
| ----- | ----- | ----------- |
| `'ac': 200` | Completed | 屬性變更作業已順利完成。 |
| `'ac': 202`  或 `'ac': 201` | Pending | 屬性變更作業暫止或進行中 |
| `'ac': 4xx` | 錯誤 | 要求的屬性變更無效或發生錯誤 |
| `'ac': 5xx` | 錯誤 | 裝置處理要求的變更時，發生未預期的錯誤。 |


如需詳細資訊，請參閱 [裝置 twins](https://docs.microsoft.com/azure/iot-hub/tutorial-device-twins)。

當操作員在 IoT Central 應用程式中設定可寫入屬性時，應用程式會使用裝置對應項所需的屬性，將值傳送至裝置。 裝置接著會使用裝置對應項報告屬性來回應。 當 IoT Central 收到回報的屬性值時，它會以 [已 **接受**] 的狀態更新屬性視圖。

下列視圖顯示可寫入的屬性。 當您輸入值並 **儲存**時，初始狀態為 [ **擱置**中]，當裝置接受變更時，狀態就會變更為 [已 **接受**]。

![暫止狀態](./media/howto-use-properties/status-pending.png)

![接受的屬性](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>後續步驟

既然您已瞭解如何在 Azure IoT Central 應用程式中使用屬性，您就[可以看到承載，並](concepts-telemetry-properties-commands.md)[建立用戶端應用程式，並將其連接至 Azure IoT Central 應用程式 ( # A0) ](tutorial-connect-device-nodejs.md)。
