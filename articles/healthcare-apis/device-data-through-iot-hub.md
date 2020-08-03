---
title: 教學課程：透過 Azure IoT 中樞接收裝置資料
description: 在本教學課程中，您將了解如何透過 IoT 連接器，啟用從 IoT 中樞到 Azure API for FHIR 的裝置資料路由。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 06/27/2020
ms.author: punagpal
ms.openlocfilehash: aeed0c90eeecd59e23a1d87a3ebc1e1a836a84ec
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87116847"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>教學課程：透過 Azure IoT 中樞接收裝置資料

IoT 連接器可讓您將來自醫學物聯網 (IoMT) 裝置的資料內嵌到 Azure API for FHIR 中。 [使用 Azure 入口網站部署 IoT 連接器 (預覽)](iot-fhir-portal-quickstart.md) 快速入門示範了 Azure IoT Central 所管理的裝置如何[將遙測傳送至 IoT 連接器](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot)的範例。 IoT 連接器也可以使用透過 Azure IoT 中樞佈建和管理的裝置。 本教學課程提供將裝置資料從 Azure IoT 中樞連線並路由至 IoT 連接器的程序。

## <a name="prerequisites"></a>必要條件

- 有效的 Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 至少具有一個 IoT 連接器的 Azure API for FHIR 資源 - [使用 Azure 入口網站部署 IoT 連接器 (預覽)](iot-fhir-portal-quickstart.md)
- 與實際或模擬裝置連線的 Azure IoT 中樞資源 - [使用 Azure 入口網站建立 IoT 中樞](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)

> [!TIP]
> 如果您使用 Azure IoT 中樞模擬裝置應用程式，則可以在不同的支援語言和系統上，自由挑選您選擇的應用程式。

## <a name="get-connection-string-for-iot-connector-preview"></a>取得 IoT 連接器的連接字串 (預覽)

Azure IoT 中樞需要連接字串，才能安全地與您的 IoT 連接器連線。 如[產生連接字串](iot-fhir-portal-quickstart.md#generate-a-connection-string)所述，為您的 IoT 連接器建立新的連接字串。 保留此連接字串以便在接下來的步驟中使用。

IoT 連接器會在幕後使用 Azure 事件中樞執行個體來接收裝置訊息。 上面建立的連接字串基本上是此基礎事件中樞的連接字串。

## <a name="connect-azure-iot-hub-with-the-iot-connector-preview"></a>使用 IoT 連接器連線 Azure IoT 中樞 (預覽)

Azure IoT 中樞支援稱為[訊息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)的功能，可提供將裝置資料傳送至各種 Azure 服務 (例如事件中樞、儲存體帳戶和服務匯流排) 的功能。 IoT 連接器會利用這項功能，將裝置資料從 Azure IoT 中樞連線並傳送到其事件中樞端點。

> [!NOTE] 
> 目前，您只能使用 PowerShell 或 CLI 命令[建立訊息路由](https://docs.microsoft.com/azure/iot-hub/tutorial-routing)，因為客戶的訂用帳戶上並未裝載 IoT 連接器的事件中樞，因此您無法透過 Azure 入口網站看到訊息路由。 不過，一旦使用 PowerShell 或 CLI 新增訊息路由物件，訊息路由就會顯示在 Azure 入口網站上，而且可以從該處進行管理。

只需兩步驟即可設定訊息路由。

### <a name="add-an-endpoint"></a>新增端點。
此步驟會定義 IoT 中樞會將資料路由至其中的端點。 請根據您的嘉好設定，使用 [AzIotHubRoutingEndpoint](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell 命令或 [az iot hub routing-endpoint create](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?#az-iot-hub-routing-endpoint-create) CLI 命令來建立此端點。

以下是要搭配命令以建立端點的參數清單：

|PowerShell 參數|CLI 參數|描述|
|---|---|---|
|resourceGroupName|resource-group|IoT 中樞資源的資源群組名稱。|
|名稱|hub-name|您 IoT 中樞資源的名稱。|
|EndpointName|endpoint-name|使用您想要指派給所建立之端點的名稱。|
|EndpointType|endpoint-type|IoT 中樞需要連線的端點類型。 針對 PowerShell 使用 "EventHub" 常值，而針對 CLI 則使用 "eventhub"。|
|EndpointResourceGroup|endpoint-resource-group|IoT 連接器的 Azure API for FHIR 資源的資源群組名稱。 您可以從 Azure API for FHIR 的 [概觀] 頁面取得此值。|
|EndpointSubscriptionId|endpoint-subscription-id|IoT 連接器 Azure API for FHIR 資源的訂用帳戶識別碼。 您可以從 Azure API for FHIR 的 [概觀] 頁面取得此值。|
|ConnectionString|connection-string|您 IoT 連接器的連接字串。 使用上一個步驟中取得的值。|

### <a name="add-a-message-route"></a>新增訊息路由
此步驟會使用上面建立的端點來定義訊息路由。 請根據您的喜好設定，使用 [Add-AzIotHubRoute](https://docs.microsoft.com/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell 命令或 [az iot hub route create](https://docs.microsoft.com/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI 命令來建立路由點。

以下是要搭配命令以建立端點的參數清單：

|PowerShell 參數|CLI 參數|描述|
|---|---|---|
|resourceGroupName|g|IoT 中樞資源的資源群組名稱。|
|名稱|hub-name|您 IoT 中樞資源的名稱。|
|EndpointName|endpoint-name|您先前建立的端點名稱。|
|RouteName|route-name|您想要指派給所建立之訊息路由的名稱。|
|來源|source-type|傳送至端點的資料類型。 針對 PowerShell 使用 "DeviceMessages" 常值，而針對 CLI 則使用 "devicemessages"。|

## <a name="send-device-message-to-iot-hub"></a>將裝置訊息傳送到 IoT 中樞

使用您的裝置 (實際或模擬)，將下面顯示的範例心率訊息傳送至 Azure IoT 中樞。 這則訊息會路由傳送至 IoT 連接器，其中訊息會轉換成 FHIR 的觀測資源，並儲存到 Azure API for FHIR 中。

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> 請務必傳送以 IoT 連接器設定，且符合[對應範本](iot-mapping-templates.md)的裝置訊息。

## <a name="view-device-data-in-azure-api-for-fhir"></a>在 Azure API for FHIR 中檢視裝置資料

您可以使用 Postman，在 Azure API for FHIR 上檢視 IoT 連接器所建立的 FHIR 觀察資源。 設定您的 [Postman 以存取 Azure API for FHIR](access-fhir-postman-tutorial.md)，並向 `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` 提出 `GET` 要求，以在上述範例訊息中，使用已提交的心率值來檢視觀察 FHIR 資源。

> [!TIP]
> 請確定您的使用者擁有適當的 Azure API for FHIR 資料平面存取權。 使用 [Azure 角色型存取控制](configure-azure-rbac.md)，指派必要的資料平面角色。


## <a name="next-steps"></a>後續步驟

在本快速入門手冊中，您會設定 Azure IoT 中樞以將裝置資料路由傳送至 IoT 連接器。 從下面的後續步驟中選取各項主題，以深入了解 IoT 連接器：

了解 IoT 連接器內不同的資料流程階段。

>[!div class="nextstepaction"]
>[IoT 連接器資料流程](iot-data-flow.md)

了解如何使用裝置和 FHIR 對應範本來設定 IoT 連接器。

>[!div class="nextstepaction"]
>[IoT 連接器對應範本](iot-mapping-templates.md)

FHIR 是 HL7 的註冊商標，必須搭配 HL7 權限方可使用。

