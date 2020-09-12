---
title: 針對 Azure IoT Central 的裝置連線進行疑難排解 |Microsoft Docs
description: 針對您在 IoT Central 中看不到裝置資料的原因進行疑難排解
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.openlocfilehash: 2bf48b6808fccb1f4344e66a2b8f1fc2d4c52ef6
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322444"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>疑難排解您的裝置資料未顯示在 Azure IoT Central 中的原因

本檔可協助裝置開發人員找出裝置傳送到 IoT Central 的資料可能不會顯示在應用程式中的原因。

有兩個主要區域要調查：

- 裝置連線能力問題
  - 驗證問題，例如裝置有不正確認證
  - 網路連線問題
  - 未核准或封鎖裝置
- 裝置承載圖形問題

這份疑難排解指南著重于裝置連線問題和裝置承載圖形問題。

## <a name="device-connectivity-issues"></a>裝置連線能力問題

本節可協助您判斷資料是否達到 IoT Central。

如果您尚未這麼做，請安裝 `az cli` 工具和擴充功能 `azure-iot` 。

若要瞭解如何安裝 `az cli` ，請參閱 [安裝 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

若[install](https://docs.microsoft.com/cli/azure/azure-cli-reference-for-IoT?view=azure-cli-latest#extension-reference-installation)要安裝 `azure-iot` 擴充功能，請執行下列命令：

```cmd/bash
az extension add --name azure-iot
```

> [!NOTE]
> 當 `uamqp` 您第一次執行延伸模組命令時，系統可能會提示您安裝程式庫。

當您已安裝擴充功能時 `azure-iot` ，請啟動您的裝置，以查看正在傳送的訊息是否正在進行 IoT Central。

使用下列命令來登入您的 IoT Central 應用程式所在的訂用帳戶：

```cmd/bash
az login
az set account --subscription <your-subscription-id>
```

若要監視裝置所傳送的遙測，請使用下列命令：

```cmd/bash
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

如果裝置已成功連線到 IoT Central，您會看到類似下列的輸出：

```cmd/bash
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

若要監視您的裝置與 IoT Central 交換的屬性更新，請使用下列預覽命令：

```cmd/bash
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

如果裝置成功傳送屬性更新，您會看到類似下列的輸出：

```cmd/bash
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

如果您看到資料出現在終端機中，則資料會變成您 IoT Central 應用程式的最遠。

如果您在幾分鐘之後看不到任何資料，請嘗試按 `Enter` `return` 鍵盤上的或鍵，以防輸出停滯。

如果您仍然看不到任何資料出現在終端機上，可能是您的裝置有網路連線問題，或未正確地將資料傳送給 IoT Central。

### <a name="check-the-provisioning-status-of-your-device"></a>檢查裝置的布建狀態

如果您的資料未顯示在監視器上，請執行下列命令來檢查裝置的布建狀態：

```cmd/bash
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

下列輸出顯示封鎖連接的裝置範例：

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| 裝置布建狀態 | 描述 | 可能的緩和措施 |
| - | - | - |
| 已佈建 | 沒有立即可辨識的問題。 | N/A |
| 已登錄 | 裝置尚未連線到 IoT Central。 | 檢查您的裝置記錄是否有連線問題。 |
| 封鎖 | 裝置遭到封鎖而無法連接到 IoT Central。 | 裝置遭到封鎖而無法連接到 IoT Central 應用程式。 請在 IoT Central 中解除封鎖裝置，然後重試。 若要深入瞭解，請參閱 [封鎖裝置](concepts-get-connected.md#device-status-values)。 |
| 未經 批准 | 裝置未通過核准。 | 裝置未獲核准，無法連接至 IoT Central 應用程式。 請在 IoT Central 中核准裝置，然後重試。 若要深入瞭解，請參閱 [核准裝置](concepts-get-connected.md#connect-without-registering-devices) |
| 無 | 裝置未與裝置範本建立關聯。 | 將裝置與裝置範本建立關聯，讓 IoT Central 知道如何剖析資料。 |

深入瞭解 [裝置狀態碼](concepts-get-connected.md#device-status-values)。

### <a name="error-codes"></a>錯誤碼

如果您仍然無法診斷資料為什麼未顯示在中 `monitor-events` ，下一步是尋找您的裝置所報告的錯誤碼。

在您的裝置上啟動偵錯工具，或從您的裝置收集記錄。 檢查裝置所報告的任何錯誤碼。

下表顯示常見的錯誤碼，以及可減輕的可能動作。

如果您看到與驗證流程相關的問題：

| 錯誤碼 | 描述 | 可能的緩和措施 |
| - | - | - |
| 400 | 要求的主體無效。 例如，無法剖析，或無法驗證物件。 | 請確定您是以證明流程的一部分傳送正確的要求本文，或使用裝置 SDK。 |
| 401 | 無法驗證授權權杖。 例如，它已過期或不適用於要求的 URI。 此錯誤碼也會傳回給裝置，作為 TPM 證明流程的一部分。 | 確定您的裝置具有正確的認證。 |
| 404 | 裝置布建服務實例或資源（例如註冊）不存在。 | 提出[客戶支援的票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 |
| 412 | `ETag`要求中的不符合 `ETag` 現有資源的，如每個 >rfc7232。 | 提出[客戶支援的票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。 |
| 429 | 服務正在節流作業。 如需特定服務限制，請參閱 [IoT 中樞裝置布建服務限制](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits)。 | 減少訊息頻率，在更多裝置上分割責任。 |
| 500 | 發生內部錯誤。 | 向[客戶支援提出票證](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)，以查看是否能進一步協助您。 |

## <a name="payload-shape-issues"></a>裝載圖形問題

當您已建立裝置將資料傳送至 IoT Central 時，下一步是確定您的裝置是以有效的格式傳送資料。

常見的問題有兩個主要類別，導致裝置資料不會出現在 IoT Central 中：

- 裝置資料的裝置範本不符：
  - 命名不相符，例如輸入錯誤或符合大小寫的問題。
  - 未在裝置範本中定義架構的未模型化屬性。
  - 架構不相符，例如範本中定義的類型 `boolean` ，但資料為字串。
  - 相同的遙測名稱定義于多個介面中，但裝置不 IoT 隨插即用符合規範。
- 資料圖形是不正確 JSON。 若要深入瞭解，請參閱 [遙測、屬性和命令](concepts-telemetry-properties-commands.md)承載。

若要偵測您的問題所在的類別，請針對您的案例執行最適當的命令：

- 若要驗證遙測，請使用預覽命令：

    ```cmd/bash
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- 若要驗證屬性更新，請使用預覽命令

    ```cmd/bash
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

當 `uamqp` 您第一次執行命令時，系統可能會提示您安裝程式庫 `validate` 。

下列輸出顯示來自 validate 命令的範例錯誤和警告訊息：

```cmd/bash
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

如果您想要使用 GUI，請使用 IoT Central **原始資料** 視圖，以查看是否有未模型化的內容。 如果裝置正在傳送格式不正確的 JSON， **原始資料** 視圖就不會偵測到。

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="原始資料檢視的螢幕擷取畫面":::

當您偵測到問題時，您可能需要更新裝置固件，或建立新的裝置範本，以模型化先前未清查的資料。

如果您選擇建立可正確建立資料模型的新範本，請從舊範本將裝置遷移至新的範本。 若要深入瞭解，請參閱 [在您的 Azure IoT Central 應用程式中管理裝置](howto-manage-devices.md)。

## <a name="next-steps"></a>接下來的步驟

如果您需要更多協助，您可以聯繫 [MSDN azure 和 Stack Overflow 論壇](https://azure.microsoft.com/support/community/)上的 azure 專家。 或者，您也可以提出 [Azure 支援的票證](https://portal.azure.com/#create/Microsoft.Support)。

如需詳細資訊，請參閱 [Azure IoT 支援和協助選項](../../iot-fundamentals/iot-support-help.md)。
