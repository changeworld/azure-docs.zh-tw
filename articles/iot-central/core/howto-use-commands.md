---
title: 如何在 Azure IoT Central 解決方案中使用裝置命令
description: 如何使用 Azure IoT Central 解決方案中的裝置命令。 本教學課程說明如何以裝置開發人員身分，在用戶端應用程式中使用裝置命令來 Azure IoT Central 應用程式。
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 52872175eb799785674c331ad4d687ff8ef427a4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134274"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>如何在 Azure IoT Central 解決方案中使用命令

本操作指南說明如何以裝置開發人員身分，使用裝置範本中定義的命令。

操作員可以使用 IoT Central UI 在裝置上呼叫命令。 命令會控制裝置的行為。 例如，操作員可能會呼叫命令以重新開機裝置或收集診斷資料。

裝置可以︰

* 立即回應命令。
* 在收到命令時回應 IoT Central，之後當 *長時間* 執行的命令完成時，就會通知 IoT Central。

根據預設，命令會預期裝置必須連線，而且如果無法連線到裝置，則會失敗。 如果您在裝置範本 UI 中選取 [ **離線時佇列** ] 選項，則可以將命令排入佇列，直到裝置上線為止。 這些 *離線命令* 將在本文稍後的個別章節中說明。

## <a name="define-your-commands"></a>定義您的命令

標準命令會傳送至裝置，以指示裝置進行某些動作。 命令可包含具有其他資訊的參數。 例如，在裝置上開啟閥門的命令可能會有一個參數，以指定要開啟閥門的程度。 當裝置完成命令時，命令也可以接收傳回值。 例如，要求裝置執行某些診斷的命令可能會接收到診斷報告作為傳回值。

命令會定義為裝置範本的一部分。 下列螢幕擷取畫面顯示 **控溫器** 裝置範本中的 **Get Max-Min 報告** 命令定義。 這個命令同時有要求和回應參數： 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="顯示控溫器裝置範本中取得最大最小報告命令的螢幕擷取畫面":::

下表顯示命令功能的組態設定：

| 欄位             |描述|
|-------------------|-----------|
|顯示名稱       |儀表板和表單上使用的命令值。|
| 名稱            | 命令的名稱。 IoT Central 會從顯示名稱產生此欄位的值，但如有必要，您也可以選擇自己所要使用的值。 此欄位必須是英數位元。 裝置程式碼會使用此 **名稱** 值。|
| 功能類型 | 命令。|
| 離線時佇列 | 是否要讓此命令成為 *離線* 命令。 |
| 描述     | 命令功能的說明。|
| 註解     | 有關命令功能的任何註解。|
| 要求     | 裝置命令的承載。|
| 回應     | 裝置命令回應的承載。|

下列程式碼片段顯示裝置模型中命令的 JSON 標記法。 在此範例中，回應值是具有多個欄位的複雜 **物件** 類型：

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> 您可以從 [裝置範本] 頁面匯出裝置型號。

您可以使用下欄欄位，將此命令定義與 UI 的螢幕擷取畫面產生關聯：

* `@type` 若要指定功能的類型： `Command`
* `name` 作為命令值。

選擇性欄位（例如 [顯示名稱] 和 [描述]）可讓您將更多詳細資料新增至介面和功能。

## <a name="standard-commands"></a>標準命令

本節將說明裝置在收到命令時，如何傳送回應值。

下列程式碼片段顯示裝置如何可以立即回應命令以立即傳送成功的程式碼：

> [!NOTE]
> 為了簡單起見，本文使用 Node.js。 如需其他語言的範例，請參閱 [建立並將用戶端應用程式連線到您的 Azure IoT Central 應用程式](tutorial-connect-device.md) 教學課程。

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

呼叫以 `onDeviceMethod` 設定 `commandHandler` 方法。 此命令處理常式：

1. 檢查命令的名稱。
1. 針對 `getMaxMinReport` 命令，它會呼叫 `getMaxMinReportObject` 以取得要包含在傳回物件中的值。
1. 呼叫 `sendCommandResponse` 以將回應傳回 IoT Central。 此回應包含 `200` 回應碼以表示成功。

下列螢幕擷取畫面顯示如何在 IoT Central UI 中顯示成功的命令回應：

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="顯示如何查看標準命令的命令承載的螢幕擷取畫面":::

## <a name="long-running-commands"></a>長時間執行的命令

本節說明裝置如何延遲傳送命令所爭相的確認。

下列程式碼片段會顯示裝置如何執行長時間執行的命令：

> [!NOTE]
> 為了簡單起見，本文使用 Node.js。 如需其他語言的範例，請參閱 [建立並將用戶端應用程式連線到您的 Azure IoT Central 應用程式](tutorial-connect-device.md) 教學課程。

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

呼叫以 `onDeviceMethod` 設定 `commandHandler` 方法。 此命令處理常式：

1. 檢查命令的名稱。
1. 呼叫 `sendCommandResponse` 以將回應傳回 IoT Central。 此回應會包含 `202` 回應碼來指出暫止的結果。
1. 完成長時間執行的作業。
1. 使用與命令名稱相同的報告屬性，告訴 IoT Central 命令已完成。

下列螢幕擷取畫面顯示當命令回應收到202回應碼時，如何顯示在 IoT Central UI 中：

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="顯示從裝置立即回應的螢幕擷取畫面":::

下列螢幕擷取畫面顯示當 IoT Central UI 收到指出命令已完成的屬性更新時：

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="顯示長時間執行命令完成的螢幕擷取畫面":::

## <a name="offline-commands"></a>離線命令

本節說明裝置處理離線命令的方式。 如果裝置在線上，就可以在收到時立即處理離線命令。 如果裝置處於離線狀態，它會在下一次連線到 IoT Central 時處理離線命令。 裝置無法傳送回復值以回應離線命令。

> [!NOTE]
> 為了簡單起見，本文使用 Node.js。

下列螢幕擷取畫面顯示稱為 **GenerateDiagnostics** 的離線命令。 Request 參數是具有 datetime 屬性（稱為 **StartTime** ）的物件，以及名為 **Bank** 的整數列舉屬性：

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="顯示離線命令 UI 的螢幕擷取畫面":::

下列程式碼片段會顯示用戶端如何接聽離線命令並顯示訊息內容：

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

先前程式碼片段的輸出會顯示具有 **StartTime** 和 **Bank** 值的承載。 屬性清單會在 [ **方法名稱** ] 清單專案中包含命令名稱：

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何在 Azure IoT Central 應用程式中使用命令， [請參閱承載](concepts-telemetry-properties-commands.md) 以深入瞭解命令參數，並 [建立用戶端應用程式並將其連接至您的 Azure IoT Central 應用程式](tutorial-connect-device.md) ，以查看不同語言的完整程式碼範例。
