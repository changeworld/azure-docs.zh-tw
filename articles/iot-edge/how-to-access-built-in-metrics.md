---
title: 存取內建計量-Azure IoT Edge
description: 從 IoT Edge 執行時間元件遠端存取內建計量
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 10/05/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: df904e183d3f77751d86d0cefab5423d753f146b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979465"
---
# <a name="access-built-in-metrics"></a>存取內建計量

IoT Edge 執行時間元件（IoT Edge 中樞和 IoT Edge 代理程式）會以 [Prometheus 展示格式](https://prometheus.io/docs/instrumenting/exposition_formats/)產生內建的度量。 從遠端存取這些計量，以監視及瞭解 IoT Edge 裝置的健康情況。

從 release 1.0.10，計量預設會自動在**edgeHub**和**edgeAgent**模組的**埠 9600**上公開 (`http://edgeHub:9600/metrics` 和 `http://edgeAgent:9600/metics`) 。 預設不會將埠對應到主機。

藉由公開並對應模組的計量埠，從主機存取計量 `createOptions` 。 下列範例會將預設計量埠對應至主機上的埠9601：

```
{
  "ExposedPorts": {
    "9600/tcp": {},
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

如果您要對應 edgeHub 和 edgeAgent 的計量端點，請選擇不同且唯一的主機埠號碼。

> [!NOTE]
> 如果您想要停用計量，請將 `MetricsEnabled` 環境變數設為以 `false` 進行 **edgeAgent**。

## <a name="available-metrics"></a>可用的計量

計量包含的標記可協助您識別所收集之計量的本質。 所有度量都包含下列標記：

| Tag | 描述 |
|-|-|
| iothub | 裝置正在交談的中樞 |
| edge_device | 目前裝置的識別碼 |
| instance_number | 表示目前執行時間的 GUID。 重新開機時，將會重設所有計量。 此 GUID 可讓您更輕鬆地協調重新開機。 |

在 Prometheus 展示格式中，有四種核心計量類型：計數器、量測計、長條圖和摘要。 如需不同度量類型的詳細資訊，請參閱 [Prometheus 計量類型檔](https://prometheus.io/docs/concepts/metric_types/)。

針對內建長條圖和摘要度量提供的分量為0.1、0.5、0.9 和0.99。

**EdgeHub**模組會產生下列計量：

| Name | 維度 | 描述 |
|-|-|-|
| `edgehub_gettwin_total` | `source` (操作來源) <br> `id` (模組識別碼)  | 類型：計數器<br> GetTwin 呼叫總數 |
| `edgehub_messages_received_total` | `route_output` (傳送訊息的輸出) <br> `id` | 類型：計數器<br> 從用戶端接收的訊息總數 |
| `edgehub_messages_sent_total` | `from` (訊息來源) <br> `to` (訊息目的地) <br>`from_route_output`<br> `to_route_input` (訊息目的地輸入) <br> `priority` (訊息優先順序設為目的地)  | 類型：計數器<br> 傳送至用戶端或上游的訊息總數<br> `to_route_input` 當為時，為空白 `to` $upstream |
| `edgehub_reported_properties_total` | `target` (更新目標) <br> `id` | 類型：計數器<br> 報告的屬性更新呼叫總數 |
| `edgehub_message_size_bytes` | `id`<br> | 類型：摘要<br> 來自用戶端的訊息大小<br> 您可以將值回報為 `NaN` 在一段時間內不會回報任何新的度量 (目前為10分鐘) ; 針對 `summary` 類型 `_count` ， `_sum` 則會發出對應的和計數器。 |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | 類型：摘要<br> 取得對應項作業所花費的時間 |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | 類型：摘要<br> 傳送訊息所花費的時間 |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | 類型：摘要<br> 處理來自佇列的訊息所花費的時間 |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | 類型：摘要<br> 更新報告屬性所花費的時間 |
| `edgehub_direct_method_duration_seconds` | `from` (呼叫端) <br> `to` (接收器)  | 類型：摘要<br> 解決直接訊息所花費的時間 |
| `edgehub_direct_methods_total` | `from`<br> `to` | 類型：計數器<br> 傳送的直接訊息總數 |
| `edgehub_queue_length` | `endpoint` (訊息來源) <br> `priority` (佇列優先順序)  | 類型：量測計<br> 指定優先順序的目前 edgeHub 佇列長度 |
| `edgehub_messages_dropped_total` | `reason` (no_route ttl_expiry) <br> `from` <br> `from_route_output` | 類型：計數器<br> 因為原因而移除的訊息總數 |
| `edgehub_messages_unack_total` | `reason` (storage_failure) <br> `from`<br> `from_route_output` | 類型：計數器<br> 因儲存失敗而未認可的訊息總數 |
| `edgehub_offline_count_total` | `id` | 類型：計數器<br> EdgeHub 離線的總次數 |
| `edgehub_offline_duration_seconds`| `id` | 類型：摘要<br> 時間 edge 中樞離線 |
| `edgehub_operation_retry_total` | `id`<br> `operation` (操作名稱)  | 類型：計數器<br> EdgeHub 作業重試的總次數 |
| `edgehub_client_connect_failed_total` | `id` <br> `reason` (未經過驗證) <br> | 類型：計數器<br> 用戶端無法連接到 edgeHub 的總次數 |

**EdgeAgent**模組會產生下列計量：

| Name | 維度 | 描述 |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | 類型：量測計<br> 模組在部署中指定的時間量，且處於執行中狀態 |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | 類型：量測計<br> 模組在部署中指定的時間量 |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | 類型：計數器<br> EdgeAgent 要求 docker 啟動模組的次數 |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | 類型：計數器<br> EdgeAgent 要求 docker 停止模組的次數 |
| `edgeAgent_command_latency_seconds` | `command` | 類型：量測計<br> Docker 執行指定命令所花的時間。 可能的命令為：建立、更新、移除、啟動、停止、重新開機 |
| `edgeAgent_iothub_syncs_total` | | 類型：計數器<br> EdgeAgent 嘗試同步處理其對應項與 iotHub 的次數，成功和不成功。 此數目包括要求對應項的代理程式，以及對對應項更新的中樞通知 |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | 類型：計數器<br> EdgeAgent 無法與 iotHub 同步其對應項的次數。 |
| `edgeAgent_deployment_time_seconds` | | 類型：計數器<br> 在收到變更之後完成新部署所花費的時間量。 |
| `edgeagent_direct_method_invocations_count` | `method_name` | 類型：計數器<br> 呼叫內建 edgeAgent 直接方法的次數，例如 Ping 或重新開機。 |
| `edgeAgent_host_uptime_seconds` | | 類型：量測計<br> 主機的執行時間 |
| `edgeAgent_iotedged_uptime_seconds` | | 類型：量測計<br> Iotedged 的執行時間 |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | 類型：量測計<br> 磁片上剩餘的空間量 |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| 類型：量測計<br> 磁片的大小 |
| `edgeAgent_used_memory_bytes` | `module_name` | 類型：量測計<br> 所有進程使用的 RAM 數量 |
| `edgeAgent_total_memory_bytes` | `module_name` | 類型：量測計<br> 可用的 RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | 類型：長條圖<br> 所有進程所使用的 cpu 百分比 |
| `edgeAgent_created_pids_total` | `module_name` | 類型：量測計<br> 容器已建立的進程或執行緒數目 |
| `edgeAgent_total_network_in_bytes` | `module_name` | 類型：量測計<br> 從網路接收的位元組數 |
| `edgeAgent_total_network_out_bytes` | `module_name` | 類型：量測計<br> 傳送至網路的位元組數目 |
| `edgeAgent_total_disk_read_bytes` | `module_name` | 類型：量測計<br> 從磁片讀取的位元組數目 |
| `edgeAgent_total_disk_write_bytes` | `module_name` | 類型：量測計<br> 寫入磁片的位元組數目 |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | 類型：量測計<br> 裝置的一般中繼資料。 此值一律為0，資訊會在標記中進行編碼。 注意 `experimental_features` 和 `host_information` 是 json 物件。 `host_information` 看起來像這樣 ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` 。 請注意， `ServerVersion` Docker 版本是 `Version` IoT Edge 的安全 daemon 版本。 |

## <a name="next-steps"></a>後續步驟

* [了解 Azure IoT Edge 執行階段和架構](iot-edge-runtime.md)
* [IoT Edge 代理程式和 IoT Edge 中樞模組對應項的屬性](module-edgeagent-edgehub.md)
