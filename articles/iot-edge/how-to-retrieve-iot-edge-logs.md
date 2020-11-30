---
title: 取出 IoT Edge 記錄-Azure IoT Edge
description: IoT Edge 模組記錄檔抓取，並上傳至 Azure Blob 儲存體。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: ad990f10c611c5ca5bb8a8d053ee4d59b6f05c83
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96326993"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>從 IoT Edge 的部署取出記錄

使用 IoT Edge agent 模組中包含的直接方法，從您的 IoT Edge 部署取出記錄，而不需要對裝置進行實體或 SSH 存取。 直接方法會在裝置上執行，然後可以從雲端叫用。 IoT Edge 代理套裝程式含可協助您從遠端監視和管理 IoT Edge 裝置的直接方法。 本文中討論的直接方法已在1.0.10 版本中正式推出。

如需直接方法、如何使用它們，以及如何在自己的模組中執行這些方法的詳細資訊，請參閱 [瞭解並從 IoT 中樞叫用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

這些直接方法的名稱會以區分大小寫的方式處理。

## <a name="recommended-logging-format"></a>建議的記錄格式

雖然並非必要，但為了與這項功能的最佳相容性，建議的記錄格式為：

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` 應遵循 [Syslog 嚴重性層級格式](https://wikipedia.org/wiki/Syslog#Severity_lnevel) ，且 `{Timestamp}` 應格式化為 `yyyy-mm-dd hh:mm:ss.fff zzz` 。

[IoT Edge 中的記錄器類別](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs)可作為標準的實作為。

## <a name="retrieve-module-logs"></a>取出模組記錄

您可以使用 **GetModuleLogs** 直接方法來取出 IoT Edge 模組的記錄檔。

這個方法會接受具有下列架構的 JSON 承載：

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "int",
                "until": "int",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名稱 | 類型 | 說明 |
|-|-|-|
| schemaVersion | 字串 | 設定為 `1.0` |
| 項目 | JSON 陣列 | 具有 `id` 和 `filter` 元組的陣列。 |
| 識別碼 | 字串 | 提供模組名稱的正則運算式。 它可以符合 edge 裝置上的多個模組。 需要[.Net 正則運算式](/dotnet/standard/base-types/regular-expressions)格式。 |
| filter | JSON 區段 | 要套用至符合 `id` 元組中正則運算式之模組的記錄篩選。 |
| 尾巴 | integer | 過去從最新的開始取出的記錄行數。 選擇性。 |
| 自 | integer | 只會在這段時間之後傳回記錄， (1 d、90 m、2天3小時2分鐘) 、rfc3339 時間戳記或 UNIX 時間戳記。  如果同時 `tail` 指定了和 `since` ，則會先使用值抓取記錄 `since` 。 然後，此 `tail` 值會套用到結果，並傳回最終結果。 選擇性。 |
| 直到 | integer | 只會在指定的時間之前傳回記錄，以 rfc3339 時間戳記、UNIX 時間戳記或持續時間 (1 d、90 m、2天3小時2分鐘) 。 選擇性。 |
| 記錄層級 | integer | 篩選記錄行小於或等於指定的記錄層級。 記錄行應遵循建議的記錄格式，並使用 [Syslog 嚴重性等級](https://en.wikipedia.org/wiki/Syslog#Severity_level) 標準。 選擇性。 |
| RegEx | 字串 | 使用 [.Net 正則運算式](/dotnet/standard/base-types/regular-expressions) 格式篩選具有符合指定正則運算式之內容的記錄行。 選擇性。 |
| 編碼 | 字串 | `gzip` 或 `none`。 預設為 `none`。 |
| ContentType | 字串 | `json` 或 `text`。 預設為 `text`。 |

> [!NOTE]
> 如果記錄內容超過直接方法的回應大小限制（目前為 128 KB），回應會傳回錯誤。

成功抓取記錄會傳回 **"status"： 200** ，後面接著包含從模組取出之記錄的承載，並依您在要求中指定的設定進行篩選。

例如：

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

在 Azure 入口網站中，使用方法名稱和下列 JSON 承載來叫用方法 `GetModuleLogs` ：

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![在 Azure 入口網站中叫用直接方法 ' GetModuleLogs '](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

您也可以使用管線將 CLI 輸出傳送至 Linux 公用程式（例如 [gzip](https://en.wikipedia.org/wiki/Gzip)）來處理壓縮的回應。 例如：

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>上傳模組記錄

使用 **UploadModuleLogs** 直接方法將要求的記錄傳送至指定的 Azure Blob 儲存體容器。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 如果您想要從閘道裝置後方的裝置上傳記錄，您必須在最上層裝置上設定 [API proxy 和 blob 儲存體模組](how-to-configure-api-proxy-module.md) 。 這些模組會透過閘道裝置，將來自較低層裝置的記錄路由傳送至雲端中的儲存體。

::: moniker-end

這個方法會接受類似 **GetModuleLogs** 的 JSON 承載，並新增 "sasUrl" 索引鍵：

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "int",
                "until": "int",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| 名稱 | 類型 | 說明 |
|-|-|-|
| sasURL | 字串 (URI)  | [具有 Azure Blob 儲存體容器之寫入存取權的共用存取](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer)簽章 URL。 |

成功的上傳記錄要求會傳回 **"status"： 200** ，後面接著具有下列架構的承載：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名稱 | 類型 | 描述 |
|-|-|-|
| status | 字串 | 、、、或的其中一個 `NotStarted` `Running` `Completed` `Failed` `Unknown` 。 |
| message | 字串 | 如果發生錯誤，則為訊息，否則為空字串。 |
| correlationId | 字串   | 要查詢上傳要求狀態的識別碼。 |

例如：

下列調用會以壓縮的 JSON 格式，從所有模組上傳最後100的記錄行：

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

下列調用會將來自 edgeAgent 和 edgeHub 的最後100個記錄行，從 tempSensor 模組的最後1000記錄行上傳到未壓縮的文字格式：

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

在 Azure 入口網站中，使用您的資訊填入 sasURL 之後，以方法名稱和下列 JSON 承載叫用方法 `UploadModuleLogs` ：

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![在 Azure 入口網站中叫用直接方法 ' UploadModuleLogs '](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>上傳支援套件組合診斷

使用 **UploadSupportBundle** 直接方法，將 IoT Edge 模組記錄檔的 zip 檔案組合並上傳至可用的 Azure Blob 儲存體容器。 此直接方法會 [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) 在 IoT Edge 裝置上執行命令，以取得記錄。

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> 如果您想要從閘道裝置後方的裝置上傳記錄，您必須在最上層裝置上設定 [API proxy 和 blob 儲存體模組](how-to-configure-api-proxy-module.md) 。 這些模組會透過閘道裝置，將來自較低層裝置的記錄路由傳送至雲端中的儲存體。

::: moniker-end

這個方法會接受具有下列架構的 JSON 承載：

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| 名稱 | 類型 | 說明 |
|-|-|-|
| schemaVersion | 字串 | 設定為 `1.0` |
| sasURL | 字串 (URI)  | [具有 Azure Blob 儲存體容器之寫入存取權的共用存取簽章 URL](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| 自 | integer | 只會在這段時間之後傳回記錄， (1 d、90 m、2天3小時2分鐘) 、rfc3339 時間戳記或 UNIX 時間戳記。 選擇性。 |
| 直到 | integer | 只會在指定的時間之前傳回記錄，以 rfc3339 時間戳記、UNIX 時間戳記或持續時間 (1 d、90 m、2天3小時2分鐘) 。 選擇性。 |
| edgeRuntimeOnly | boolean | 若為 true，則只會從 Edge 代理程式、Edge 中樞和 Edge 安全性 Daemon 傳回記錄。 預設：false。  選擇性。 |

> [!IMPORTANT]
> IoT Edge 支援套件組合可能包含個人識別資訊。

成功的上傳記錄要求會傳回 **"status"： 200** ，後面接著具有與 **UploadModuleLogs** 回應相同架構的承載：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名稱 | 類型 | 描述 |
|-|-|-|
| status | 字串 | 、、、或的其中一個 `NotStarted` `Running` `Completed` `Failed` `Unknown` 。 |
| message | 字串 | 如果發生錯誤，則為訊息，否則為空字串。 |
| correlationId | 字串   | 要查詢上傳要求狀態的識別碼。 |

例如：

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

在 Azure 入口網站中，使用您的資訊填入 sasURL 之後，以方法名稱和下列 JSON 承載叫用方法 `UploadSupportBundle` ：

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![在 Azure 入口網站中叫用直接方法 ' UploadSupportBundle '](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>取得上傳要求狀態

使用 **GetTaskStatus** 直接方法來查詢上傳記錄要求的狀態。 **GetTaskStatus** 要求承載會使用 `correlationId` 上傳記錄要求的來取得工作的狀態。 `correlationId`會傳回，以回應 **UploadModuleLogs** 直接方法呼叫。

這個方法會接受具有下列架構的 JSON 承載：

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

成功的上傳記錄要求會傳回 **"status"： 200** ，後面接著具有與 **UploadModuleLogs** 回應相同架構的承載：

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| 名稱 | 類型 | 描述 |
|-|-|-|
| status | 字串 | 、、、或的其中一個 `NotStarted` `Running` `Completed` `Failed` `Unknown` 。 |
| message | 字串 | 如果發生錯誤，則為訊息，否則為空字串。 |
| correlationId | 字串   | 要查詢上傳要求狀態的識別碼。 |

例如：

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

在 Azure 入口網站中，使用您的資訊填入 GUID 之後，以方法名稱和下列 JSON 承載叫用方法 `GetTaskStatus` ：

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![在 Azure 入口網站中叫用直接方法 ' GetTaskStatus '](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>後續步驟

[IoT Edge 代理程式和 IoT Edge 中樞模組對應項的屬性](module-edgeagent-edgehub.md)
