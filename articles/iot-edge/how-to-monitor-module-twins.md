---
title: 監視模組 twins-Azure IoT Edge
description: 如何解讀裝置 twins 和模組 twins 以判斷連線能力和健全狀況。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c24cef2cf9e4c54d16ebc75eb1a56273d8826355
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84221139"
---
# <a name="monitor-module-twins"></a>監視模組對應項

Azure IoT 中樞中的模組 twins，可讓您監視 IoT Edge 部署的連線能力與健康情況。 模組 twins 會在您的 IoT 中樞中儲存有用的資訊，以瞭解執行中模組的效能。 [IoT Edge 代理程式](iot-edge-runtime.md#iot-edge-agent)和[IoT Edge 中樞](iot-edge-runtime.md#iot-edge-hub)執行時間模組分別各自維護其模組 twins `$edgeAgent` 和 `$edgeHub` ：

* `$edgeAgent`包含有關 IoT Edge 代理程式和 IoT Edge 中樞執行時間模組，以及您的自訂模組的健全狀況和連線資料。 IoT Edge 代理程式負責部署模組、監視它們，以及向您的 Azure IoT 中樞報告線上狀態。
* `$edgeHub`包含在裝置上執行的 IoT Edge 中樞與 Azure IoT 中樞之間通訊的相關資料。 這包括處理來自下游裝置的傳入訊息。 IoT Edge 中樞負責處理 Azure IoT 中樞與 IoT Edge 裝置和模組之間的通訊。

在模組 twins 的 JSON 結構中，資料會組織成中繼資料、標記，以及所需和報告的屬性集。 您在檔案的 deployment.js中指定的所需屬性會複製到模組 twins。 IoT Edge 代理程式和 IoT Edge 中樞都會更新其模組的報告屬性。

同樣地，在 deployment.json 檔案中為自訂模組指定的所需屬性會複製到其模組對應項，但是您的解決方案會負責提供其報告的屬性值。

本文說明如何在 Visual Studio Code 中查看 Azure 入口網站、Azure CLI 和中的模組 twins。 如需監視裝置如何接收部署的相關資訊，請參閱[監視 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md)。 如需模組 twins 概念的總覽，請參閱[瞭解及使用 IoT 中樞中的模組 twins](../iot-hub/iot-hub-devguide-module-twins.md)。

> [!TIP]
> 如果 IoT Edge 裝置與 IoT 中樞中斷連線，則執行時間模組的報告屬性可能會過時。 您可以[偵測](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` 模組，以判斷連接是否已中斷。

## <a name="monitor-runtime-module-twins"></a>監視執行時間模組 twins

若要針對部署連線問題進行疑難排解，請參閱 IoT Edge 代理程式和 IoT Edge 中樞執行時間模組 twins，然後向下切入至其他模組。

### <a name="monitor-iot-edge-agent-module-twin"></a>監視 IoT Edge 代理程式模組對應項

下列 JSON 會 `$edgeAgent` 在 Visual Studio Code 中顯示模組對應項，其中包含折迭的大部分 JSON 區段。

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeAgent",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDUz",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 53,
  "properties": {
    "desired": { "···" },
    "reported": {
      "schemaVersion": "1.0",
      "version": { "···" },
      "lastDesiredStatus": { "···" },
      "runtime": { "···" },
      "systemModules": {
        "edgeAgent": { "···" },
        "edgeHub": { "···" }
      },
      "lastDesiredVersion": 5,
      "modules": {
        "SimulatedTemperatureSensor": { "···" }
      },
      "$metadata": { "···" },
      "$version": 48
    }
  }
}
```

從頂端開始，下列章節會說明 JSON：

* 中繼資料-包含連接資料。 有趣的是，IoT Edge 代理程式的線上狀態一律處於中斷連線的狀態： `"connectionState": "Disconnected"` 。 線上狀態與裝置到雲端（D2C）訊息相關，而且 IoT Edge 代理程式不會傳送 D2C 訊息的原因。
* Properties-包含和子區段 `desired` `reported` 。
* 屬性。所需的（顯示折迭）必須是檔案的 deployment.js中的運算子所設定的屬性值。
* Properties。報告-IoT Edge agent 回報的最新屬性值。

`properties.desired`和區段都 `properties.reported` 有類似的結構，並包含架構、版本和執行時間資訊的其他中繼資料。 也包含 `modules` 任何自訂模組的區段（例如 `SimulatedTemperatureSensor` ），以及 `systemModules` `$edgeAgent` 和執行時間模組的區段 `$edgeHub` 。

藉由比較報告屬性值與所需的值，您可以判斷不一致的情況，並識別可協助您針對問題進行疑難排解的中斷連線。 在進行這些比較時，請檢查 `$lastUpdated` `metadata` 您要調查的屬性一節中的報告值。

下列屬性對於檢查疑難排解很重要：

* **exitcode** -零以外的任何值表示模組因失敗而停止。 不過，如果模組故意設定為停止狀態，則會使用錯誤碼137或143。

* **lastStartTimeUtc** -顯示上次啟動容器的**日期時間**。 如果容器未啟動，此值為 0001-01-01T00：00：00Z。

* **lastExitTimeUtc** -顯示容器上次完成的**日期時間**。 如果容器正在執行且永遠不會停止，此值為 0001-01-01T00：00：00Z。

* **runtimeStatus** -可以是下列其中一個值：

    | 值 | 說明 |
    | --- | --- |
    | 未知 | 預設狀態，直到建立部署為止。 |
    | 輪詢 | 模組已排程為啟動，但目前不在執行中。 此值適用于正在重新開機狀態變更的模組。 當失敗的模組在短時間內等待重新開機時，模組將會處於輪詢狀態。 |
    | 執行中 | 表示模組目前正在執行。 |
    | isapi | 表示健康情況探查檢查失敗或已超時。 |
    | 已經 | 指出模組已成功結束（含零結束代碼）。 |
    | 失敗 | 指出模組已結束，並出現失敗結束代碼（非零）。 模組可以根據作用中的重新開機原則，從這個狀態轉換回輪詢。 此狀態可能表示模組發生無法復原的錯誤。 當 Microsoft Monitoring Agent （MMA）無法再 resuscitate 模組，而需要新的部署時，就會發生失敗。 |

如需詳細資訊，請參閱[EdgeAgent 報告屬性](module-edgeagent-edgehub.md#edgeagent-reported-properties)。

### <a name="monitor-iot-edge-hub-module-twin"></a>監視 IoT Edge 中樞模組對應項

下列 JSON 會 `$edgeHub` 在 Visual Studio Code 中顯示模組對應項，其中包含折迭的大部分 JSON 區段。

```json
{
  "deviceId": "Windows109",
  "moduleId": "$edgeHub",
  "etag": "AAAAAAAAAAU=",
  "deviceEtag": "NzgwNjA1MDU2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00Z",
  "connectionState": "Connected",
  "lastActivityTime": "0001-01-01T00:00:00Z",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 102,
    "properties": {
      "desired": { "···" },
      "reported": {
        "schemaVersion": "1.0",
        "version": { "···" },
      "lastDesiredVersion": 5,
      "lastDesiredStatus": { "···" },
      "clients": {
        "Windows109/SimulatedTemperatureSensor": {
          "status": "Disconnected",
          "lastConnectedTimeUtc": "2020-04-08T21:42:42.1743956Z",
          "lastDisconnectedTimeUtc": "2020-04-09T07:02:42.1398325Z"
        }
      },
      "$metadata": { "···" },
      "$version": 97
    }
  }
}

```

從頂端開始，下列章節會說明 JSON：

* 中繼資料-包含連接資料。

* Properties-包含和子區段 `desired` `reported` 。
* 屬性。所需的（顯示折迭）必須是檔案的 deployment.js中的運算子所設定的屬性值。
* 屬性。報告-IoT Edge 中樞回報的最新屬性值。

如果您遇到下游裝置的問題，檢查這項資料是很好的起點。

## <a name="monitor-custom-module-twins"></a>監視自訂模組 twins

您自訂模組連線的相關資訊會保留在 IoT Edge 代理程式模組對應項中。 您的自訂模組的模組對應項主要用於維護解決方案的資料。 您在 deployment.json 檔案中定義的所需屬性會反映在模組對應項中，而您的模組可以視需要更新報告的屬性值。

您可以使用慣用的程式設計語言搭配[Azure IoT 中樞裝置 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks) ，根據模組的應用程式代碼來更新模組對應項中報告的屬性值。 下列程式會使用 Azure SDK for .NET 來執行此動作，方法是使用[SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs)模組中的程式碼：

1. 使用[CreateFromEnvironmentAysnc](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)方法建立[ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient)的實例。

1. 使用[GetTwinAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync?view=azure-dotnet)方法取得模組對應項的屬性集合。

1. 使用[SetDesiredPropertyUpdateCallbackAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync?view=azure-dotnet)方法建立接聽程式（傳遞回呼）來攔截所需屬性的變更。

1. 在您的回呼方法中，使用[UpdateReportedPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient)方法來更新模組對應項中的報告屬性，並傳遞您要設定的屬性值[TwinCollection](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twincollection) 。

## <a name="access-the-module-twins"></a>存取模組 twins

您可以在 Azure IoT 中樞、Visual Studio Code 和 Azure CLI 中，參閱模組 twins 的 JSON。

### <a name="monitor-in-azure-iot-hub"></a>Azure IoT 中樞中的監視

若要查看模組對應項的 JSON：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 從左側窗格功能表中選取 [IoT Edge]。
1. 在 [ **IoT Edge 裝置**] 索引標籤上，選取具有您要監視之模組的裝置的**裝置識別碼**。
1. 從 [**模組**] 索引標籤中選取模組名稱，然後從上方功能表列中選取 [**模組識別**對應項]。

  ![選取要在 Azure 入口網站中查看的模組對應項](./media/how-to-monitor-module-twins/select-module-twin.png)

如果您看到「此模組不存在模組身分識別」訊息，此錯誤表示後端解決方案已無法再使用，因為原先已建立身分識別。

### <a name="monitor-module-twins-in-visual-studio-code"></a>監視 Visual Studio Code 中的模組 twins

若要檢查和編輯模組對應項：

1. 如果尚未安裝，請安裝 Visual Studio Code 的[Azure IoT Tools 延伸](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)模組。
1. 在**Explorer**中，展開 [ **Azure IoT 中樞**]，然後展開包含您要監視之模組的裝置。
1. 在模組上按一下滑鼠右鍵，然後選取 [**編輯模組**對應項]。 模組對應項的暫存檔案會下載至您的電腦，並顯示在 Visual Studio Code 中。

  ![取得要在 Visual Studio Code 中編輯的模組對應項](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

如果您進行變更，請在編輯器中選取程式碼上方的 [**更新模組**對應項]，以將變更儲存到您的 IoT 中樞。

  ![更新 Visual Studio Code 中的模組對應項](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>監視 Azure CLI 中的模組 twins

若要查看 IoT Edge 是否正在執行，請使用[az IoT hub invoke-module-方法](how-to-edgeagent-direct-method.md#ping)來偵測 IoT Edge 代理程式。

[Az iot hub module 對應項](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin)結構提供下列命令：

* **az iot hub 模組**對應項 show-顯示模組對應項定義。
* **az iot hub module-對應項更新**-更新模組對應項定義。
* **az iot hub module-** 對應項 replace-將模組對應項定義取代為目標 JSON。

## <a name="next-steps"></a>後續步驟

了解如何[使用內建的直接方法與 EdgeAgent 通訊](how-to-edgeagent-direct-method.md)。
