---
title: 監視模組 twins-Azure IoT Edge
description: 如何解讀裝置 twins 和模組 twins，以判斷連線能力和健全狀況。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/29/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1bf2e3f07d9e5576f62ef9badd9c8a46ac92fad0
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450161"
---
# <a name="monitor-module-twins"></a>監視模組對應項

Azure IoT 中樞中的模組 twins，可監視 IoT Edge 部署的連線能力和健康情況。 模組 twins 會將有用的資訊儲存在 IoT 中樞，以瞭解您的執行中模組的效能。 [IoT Edge 代理程式](iot-edge-runtime.md#iot-edge-agent)和[IoT Edge 中樞](iot-edge-runtime.md#iot-edge-hub)執行時間模組分別會維護其模組 twins `$edgeAgent` 和 `$edgeHub` ：

* `$edgeAgent` 包含 IoT Edge 代理程式和 IoT Edge 中樞執行時間模組和您的自訂模組的健全狀況和連線資料。 IoT Edge 代理程式負責部署模組、監視它們，以及向您的 Azure IoT 中樞報告連接狀態。
* `$edgeHub` 包含在裝置和 Azure IoT 中樞上執行 IoT Edge 中樞之間通訊的相關資料。 這包括處理來自下游裝置的傳入訊息。 IoT Edge 中樞負責處理 Azure IoT 中樞和 IoT Edge 裝置與模組之間的通訊。

資料會組織成中繼資料、標記，以及模組 twins 的 JSON 結構中所需和報告的屬性集。 您在 deployment.js檔案中指定的所需屬性會複製到模組 twins。 IoT Edge 代理程式和 IoT Edge 中樞都會更新其模組的回報屬性。

同樣地，在檔案 deployment.js中為您自訂模組指定的所需屬性會複製到其模組對應項，但您的方案會負責提供其報告屬性值。

本文說明如何在 Visual Studio Code 中檢查 Azure 入口網站、Azure CLI 和中的模組 twins。 如需監視裝置如何接收部署的相關資訊，請參閱 [監視 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md)。 如需模組 twins 概念的總覽，請參閱 [瞭解及使用 IoT 中樞的模組 twins](../iot-hub/iot-hub-devguide-module-twins.md)。

> [!TIP]
> 如果 IoT Edge 裝置中斷與其 IoT 中樞的連線，則執行時間模組的回報屬性可能會過時。 您可以 [偵測](how-to-edgeagent-direct-method.md#ping) `$edgeAgent` 模組，以判斷連接是否遺失。

## <a name="monitor-runtime-module-twins"></a>監視執行時間模組 twins

若要針對部署連線問題進行疑難排解，請參閱 IoT Edge 代理程式和 IoT Edge 中樞執行時間模組 twins，然後向下切入到其他模組。

### <a name="monitor-iot-edge-agent-module-twin"></a>監視 IoT Edge 代理程式模組對應項

下列 JSON 會顯示 Visual Studio Code 中已折迭 `$edgeAgent` 大部分 JSON 區段的模組對應項。

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

您可以在下列各節中描述 JSON，從頂端開始：

* 中繼資料-包含連接資料。 有趣的是，IoT Edge 代理程式的連接狀態一律處於中斷線上狀態： `"connectionState": "Disconnected"` 。 線上狀態與裝置到雲端相關的原因 (D2C) 訊息，而 IoT Edge 代理程式不會傳送 D2C 訊息。
* 屬性-包含和子區段 `desired` `reported` 。
* 屬性。所需的 (所顯示的已折迭) 預期 deployment.json 檔案中的運算子所設定的屬性值。
* 屬性。回報-IoT Edge 代理程式回報最新的屬性值。

`properties.desired`和區段都 `properties.reported` 有類似的結構，並且包含架構、版本和執行時間資訊的額外中繼資料。 此外，也包含 `modules` 任何自訂模組的區段 (例如 `SimulatedTemperatureSensor`) ，以及 `systemModules` `$edgeAgent` 和執行時間模組的區段 `$edgeHub` 。

藉由比較報告的屬性值與所需的值，您可以判斷不一致的情況，並找出可協助您疑難排解問題的中斷連線。 進行這些比較時，請檢查 `$lastUpdated` 區段中報告的值， `metadata` 以取得您要調查的屬性。

以下是檢查疑難排解的重要屬性：

* **exitcode** -零以外的任何值表示模組因為失敗而停止。 但是，如果模組刻意設定為 [已停止] 狀態，則會使用錯誤碼137或143。

* **lastStartTimeUtc** -顯示上次啟動容器的 **日期時間** 。 如果容器未啟動，則此值為 0001-01-01T00：00：00Z。

* **lastExitTimeUtc** -顯示容器上次完成的 **日期時間** 。 如果容器正在執行，且永遠不會停止，則此值為 0001-01-01T00：00：00Z。

* **runtimeStatus** -可以是下列其中一個值：

    | 值 | 描述 |
    | --- | --- |
    | 未知 | 在建立部署之前的預設狀態。 |
    | 退避 | 模組已排程啟動，但目前不在執行中。 此值適用于正在重新開機狀態變更的模組。 當失敗的模組在冷卻期間等候重新開機時，模組會處於輪詢狀態。 |
    | 執行中 | 指出模組目前正在執行。 |
    | 不良 | 表示健康情況探查檢查失敗或已超時。 |
    | 停止 | 指出模組已成功結束 (並) 零結束代碼。 |
    | 失敗 | 指出模組結束，並顯示失敗的結束代碼 (非零的) 。 模組可以根據作用中的重新開機原則，從這個狀態轉換回輪詢。 此狀態可能表示模組發生無法復原的錯誤。 當 Microsoft Monitoring Agent (MMA) 無法再 resuscitate 模組（需要新的部署）時，就會發生失敗。 |

如需詳細資料，請參閱 [EdgeAgent 回報的屬性](module-edgeagent-edgehub.md#edgeagent-reported-properties) 。

### <a name="monitor-iot-edge-hub-module-twin"></a>監視 IoT Edge 中樞模組對應項

下列 JSON 會顯示 Visual Studio Code 中已折迭 `$edgeHub` 大部分 JSON 區段的模組對應項。

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

您可以在下列各節中描述 JSON，從頂端開始：

* 中繼資料-包含連接資料。

* 屬性-包含和子區段 `desired` `reported` 。
* 屬性。所需的 (所顯示的已折迭) 預期 deployment.json 檔案中的運算子所設定的屬性值。
* 屬性。回報-IoT Edge 中樞回報最新的屬性值。

如果您的下游裝置遇到問題，檢查這項資料是不錯的開端。

## <a name="monitor-custom-module-twins"></a>監視自訂模組 twins

您自訂模組連接的相關資訊會保留在 IoT Edge agent 模組對應項中。 您自訂模組的模組對應項主要用於維護解決方案的資料。 您在 deployment.js檔案中定義的所需屬性會反映在模組對應項中，而您的模組可以視需要更新報告的屬性值。

您可以使用慣用的程式設計語言搭配 [Azure IoT 中樞裝置 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks) ，根據模組的應用程式程式碼，更新模組對應項中報告的屬性值。 下列程式使用 Azure SDK for .NET 來執行這項作業，方法是使用 [SimulatedTemperatureSensor](https://github.com/Azure/iotedge/blob/dd5be125df165783e4e1800f393be18e6a8275a3/edge-modules/SimulatedTemperatureSensor/src/Program.cs) 模組中的程式碼：

1. 使用[CreateFromEnvironmentAysnc](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.createfromenvironmentasync)方法建立[ModuleClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient)的實例。

1. 使用 [GetTwinAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.gettwinasync?view=azure-dotnet) 方法取得模組對應項的屬性集合。

1. 使用 [SetDesiredPropertyUpdateCallbackAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.setdesiredpropertyupdatecallbackasync?view=azure-dotnet) 方法，建立接聽程式 (傳遞回呼) 來攔截所需屬性的變更。

1. 在您的回呼方法中，使用 [UpdateReportedPropertiesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient) 方法來更新模組對應項中報告的屬性，並傳遞您要設定的屬性值 [TwinCollection](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.shared.twincollection) 。

## <a name="access-the-module-twins"></a>存取模組 twins

您可以在 Azure IoT 中樞、Visual Studio Code 和 Azure CLI 中檢查模組 twins 的 JSON。

### <a name="monitor-in-azure-iot-hub"></a>在 Azure IoT 中樞中監視

若要查看模組對應項的 JSON：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後瀏覽至 IoT 中樞。
1. 從左側窗格功能表中選取 [IoT Edge]。
1. 在 [ **IoT Edge 裝置** ] 索引標籤上，選取您要監視之模組所在裝置的 **裝置識別碼** 。
1. 從 [ **模組** ] 索引標籤中選取模組名稱，然後從上方功能表列選取 [ **模組身分識別** 對應項]。

  ![選取要在 Azure 入口網站中查看的模組對應項](./media/how-to-monitor-module-twins/select-module-twin.png)

如果您看到「此模組不存在模組身分識別」訊息，此錯誤表示後端解決方案已無法再供最初建立身分識別使用。

### <a name="monitor-module-twins-in-visual-studio-code"></a>Visual Studio Code 中的監視模組 twins

若要檢查和編輯模組對應項：

1. 如果尚未安裝，請安裝適用于 Visual Studio Code 的 [Azure IoT Tools 延伸](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 模組。
1. 在 [ **Explorer**] 中，展開 **Azure IoT 中樞**，然後使用您要監視的模組來展開裝置。
1. 以滑鼠右鍵按一下模組，然後選取 [ **編輯模組**對應項]。 模組對應項的暫存檔案會下載到您的電腦，並顯示在 Visual Studio Code 中。

  ![取得要在 Visual Studio Code 中編輯的模組對應項](./media/how-to-monitor-module-twins/edit-module-twin-vscode.png)

如果您進行變更，請選取編輯器中程式碼上方的 [ **更新模組** 對應項]，以將變更儲存至您的 IoT 中樞。

  ![更新 Visual Studio Code 中的模組對應項](./media/how-to-monitor-module-twins/update-module-twin-vscode.png)

### <a name="monitor-module-twins-in-azure-cli"></a>Azure CLI 中的監視模組 twins

若要查看 IoT Edge 是否正在執行，請使用 [az IoT hub invoke 模組方法](how-to-edgeagent-direct-method.md#ping) 來偵測 IoT Edge 代理程式。

[Az iot 中樞模組對應項](/cli/azure/ext/azure-cli-iot-ext/iot/hub/module-twin)結構提供下列命令：

* **az iot hub 模組-** 對應項顯示-顯示模組對應項定義。
* **az iot hub 模組-** 對應項更新-更新模組對應項定義。
* **az iot hub 模組-** 對應項取代-將模組對應項定義取代為目標 JSON。

## <a name="next-steps"></a>後續步驟

了解如何[使用內建的直接方法與 EdgeAgent 通訊](how-to-edgeagent-direct-method.md)。
