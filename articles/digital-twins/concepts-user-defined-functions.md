---
title: 資料處理和使用者定義函數-Azure 數位 Twins |Microsoft Docs
description: Azure Digital Twins 的資料處理、比對器和使用者定義函式的概觀。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 75ed2029582438ede43687addfd54c0a187e0120
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78382289"
---
# <a name="data-processing-and-user-defined-functions"></a>資料處理與各項使用者定義功能

Azure Digital Twins 提供進階計算功能。 開發人員可定義自訂函式並對傳入的遙測訊息加以執行，以將事件傳送至預先定義的端點。

## <a name="data-processing-flow"></a>資料處理流程

裝置將遙測資料傳送至 Azure Digital Twins 後，開發人員可依四階段處理資料：*驗證*、*比對*、*計算*和*分派*。

[![Azure 數位 Twins 資料處理流程](media/concepts/digital-twins-data-processing-flow.png)](media/concepts/digital-twins-data-processing-flow.png#lightbox)

1. 驗證階段會將傳入的遙測訊息轉換為一般認知的[資料轉送物件](https://docs.microsoft.com/aspnet/web-api/overview/data/using-web-api-with-entity-framework/part-5)格式。 此階段也會執行裝置和感應器驗證。
1. 比對階段會尋找適當的使用者定義函式並加以執行。 預先定義的比對器會根據傳入的遙測訊息中包含的裝置、感應器和空間資訊，來尋找使用者定義函式。
1. 計算階段會執行在上一個階段中比對出來的使用者定義函式。 這些函式可讀取和更新空間圖形節點上的計算值，並可發出自訂通知。
1. 分派階段會將計算階段所產生的任何自訂通知路由到圖形中定義的端點。

## <a name="data-processing-objects"></a>資料處理物件

Azure Digital Twins 中的資料處理由三個物件的定義所組成：*比對器*、*使用者定義函式*和*角色指派*。

[![Azure 數位 Twins 資料處理物件](media/concepts/digital-twins-user-defined-functions.png)](media/concepts/digital-twins-user-defined-functions.png#lightbox)

### <a name="matchers"></a>比對器

比對器會定義一組條件，據以評估要根據傳入的感應器遙測資料執行的動作。 用來決定相符項目的條件可能包含來自於感應器、感應器的父裝置和感應器父空間的屬性。 這些條件會表示為與 [JSON 路徑](https://jsonpath.com/)間的比較，如下列範例所述：

- datatype **Temperature** 的所有感應器是由逸出字串值 `\"Temperature\"` 表示
- 在其連接埠中具有 `01`
- 所屬裝置具有設為逸出字串值 **的擴充屬性索引鍵**Manufacturer`\"Contoso\"`
- 它們屬於逸出字串 `\"Venue\"` 所指定之類型的空格
- 這是父**SpaceId**的子系 `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "id": "23535afafd-f39b-46c0-9b0c-0dd3892a1c30",
  "name": "My custom matcher",
  "spaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value Contoso and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "conditions": [
    {
      "id": "43898sg43-e15a-4e9c-abb8-2gw464364",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "id": "wt3th44-e15a-35sg-seg3-235wf3ga463",
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "id": "735hs33-e15a-37jj-23532-db901d550af5",
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"Contoso\"",
      "comparison": "Equals"
    },
    {
      "id": "222325-e15a-49fg-5744-463643644",
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - JSON 路徑會區分大小寫。
> - JSON 承載與下列項目所傳回的承載相同：
>   - 感應器的 `/sensors/{id}?includes=properties,types`。
>   - 感應器父裝置的 `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes`。
>   - 感應器父空間的 `/spaces/{id}?includes=properties,types,location,timezone`。
> - 比較不會區分大小寫。

### <a name="user-defined-functions"></a>使用者自訂函數

使用者定義函式是可在隔離之 Azure Digital Twins 環境內執行的自訂函式。 使用者定義函式可存取所接收到的原始感應器遙測訊息。 使用者定義函式也可存取空間圖形和發送器服務。 在圖形內註冊使用者定義函式之後，就必須建立比對器 (詳細說明請見[上方](#matchers))，以指定何時要執行 UDF。 例如，當 Azure Digital Twins 從指定的感應器接收新的遙測資料時，相符的使用者定義函式可計算過去數個感應器讀數的移動平均值。

可使用 JavaScript 寫入使用者定義函式。 協助程式方法可在使用者定義的執行環境中用來與圖形互動。 開發人員可對感應器遙測訊息執行自訂程式碼片段。 範例包括：

- 設定直接在圖形中讀取感應器物件的感應器。
- 根據圖形中的空間內不同的感應器讀數來執行動作。
- 建立傳入的感應器讀數符合特定條件時所發出的通知。
- 在傳送通知之前將圖形中繼資料附加至感應器讀數。

如需詳細資訊，請參閱[如何使用使用者定義的函數](./how-to-user-defined-functions.md)。

#### <a name="examples"></a>範例

[Digital Twins C# 範例的 GitHub 存放庫](https://github.com/Azure-Samples/digital-twins-samples-csharp/)包含一些使用者定義函式範例：
- [此函式](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js)會尋找二氧化碳、動作和溫度值，來判斷是否有房間的這些值是在範圍內。 [Digital Twins 教學課程](tutorial-facilities-udf.md)會更詳細地探討此函式。 
- [此函式](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/multiplemotionsensors.js)會尋找多個動作感應器中的資料，如果沒有任何感應器偵測到任何動作，則判斷該空間可供使用。 您可以透過進行檔案註解區段中所述的變更，輕鬆地取代[快速入門](quickstart-view-occupancy-dotnet.md)或[教學課程](tutorial-facilities-setup.md)中使用的使用者定義函式。 

### <a name="role-assignment"></a>角色指派

使用者定義函式的動作會受限於 Azure Digital Twins 的[角色型存取控制](./security-role-based-access-control.md)，以保護服務內的資料。 角色指派定義哪些使用者定義函式具有與空間圖及其實體互動的適當權限。 例如，使用者定義函式可能具有在給定空間下*建立*、*讀取*、*更新*或*刪除*圖形資料的能力與權限。 當使用者定義函式要求資料的圖形或嘗試執行動作時，系統會檢查使用者定義函式的存取層級。 如需詳細資訊，請參閱[角色型存取控制](./security-create-manage-role-assignments.md)。

比對器有可能觸發沒有任何角色指派的使用者定義函式。 在此情況下，使用者定義函式無法讀取圖形中的任何資料。

## <a name="next-steps"></a>後續步驟

- 若要深入了解如何將事件和遙測訊息路由至 Azure 服務，請參閱[路由事件和訊息](./concepts-events-routing.md)。

- 若要深入了解如何建立比對器、使用者定義函式和角色指派，請參閱[使用者定義函式的使用指南](./how-to-user-defined-functions.md)。

- 檢閱[使用者定義函式用戶端程式庫參考文件](./reference-user-defined-functions-client-library.md)。
