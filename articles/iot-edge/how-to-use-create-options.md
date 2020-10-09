---
title: 適用于模組的寫入 createOptions-Azure IoT Edge |Microsoft Docs
description: 如何使用部署資訊清單中的 createOptions 在執行時間設定模組
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550340"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>如何設定 IoT Edge 模組的容器建立選項

部署資訊清單中的 **createOptions** 參數可讓您在執行時間設定模組容器。 此參數可讓您控制模組的控制權，並允許允許或限制模組存取主機裝置資源或設定網路功能之類的工作。

IoT Edge 模組會實作為 IoT Edge 裝置上的 Docker 相容容器。 Docker 提供許多建立容器的選項，而這些選項也適用于 IoT Edge 模組。 如需詳細資訊，請參閱 [Docker 容器建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)。

## <a name="format-create-options"></a>格式化建立選項

IoT Edge 部署資訊清單接受格式化為 JSON 的建立選項。 例如，針對每個 edgeHub 模組，取得自動包含的建立選項：

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

此 edgeHub 範例會使用 **HostConfig. 即可** 參數，將容器上公開的埠對應至主機裝置上的埠。

如果您使用 Visual Studio 或 Visual Studio Code 的 Azure IoT Tools 擴充功能，您可以在檔案的 **deployment.template.js** 中撰寫 JSON 格式的建立選項。 然後，當您使用擴充功能來建立 IoT Edge 的解決方案或產生部署資訊清單時，它會以 IoT Edge 執行時間所預期的格式 json.stringify JSON。 例如：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

撰寫建立選項的其中一個秘訣是使用 `docker inspect` 命令。 在開發過程中，請使用在本機執行模組 `docker run <container name>` 。 一旦您的模組以您想要的方式運作，請執行 `docker inspect <container name>` 。 此命令會以 JSON 格式輸出模組詳細資料。 尋找您設定的參數，然後複製 JSON。 例如：

[![Docker 檢查 edgeHub ](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) 的結果](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>常見的案例

容器建立選項可啟用許多案例，但以下是建立 IoT Edge 解決方案時最常出現的部分：

* [提供模組存取權給主機儲存體](how-to-access-host-storage-from-module.md)
* [將主機埠對應至模組埠](#map-host-port-to-module-port)
* [限制模組記憶體和 CPU 使用量](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>將主機埠對應至模組埠

如果您的模組需要與 IoT Edge 解決方案之外的服務通訊，而且未使用訊息路由來進行這項作業，則您需要將主機埠對應至模組埠。

>[!TIP]
>相同裝置上的模組對模組通訊不需要此埠對應。 如果模組 A 需要查詢裝載在模組 B 上的 API，就可以在沒有任何埠對應的情況下進行。 模組 B 需要在其 dockerfile 中公開端口，例如： `EXPOSE 8080` 。 然後，模組 A 可以使用模組 B 的名稱查詢 API，例如： `http://ModuleB:8080/api` 。

首先，請確定已公開模組內的埠來接聽連接。 您可以使用 dockerfile 中的 [公開](https://docs.docker.com/engine/reference/builder/#expose) 指令來完成這項作業。 例如： `EXPOSE 8080` 。 如果未指定，則公開指令預設為 TCP 通訊協定，或您可以指定 UDP。

然後，使用 Docker 容器的**HostConfig**群組中的**即可**設定來[建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)，以將模組中的公開端口對應至主機裝置上的埠。 例如，如果您在模組內部公開端口8080，並想要將其對應至主機裝置的埠80，則 template.js檔案中的 [建立] 選項看起來會類似下列範例：

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

Stringified 部署資訊清單之後，相同的設定看起來會如下列範例所示：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>限制模組記憶體和 CPU 使用量

您可以宣告模組可使用的主機資源數量。 此控制項有助於確保某個模組無法耗用太多記憶體或 CPU 使用量，並防止其他進程在裝置上執行。 您可以使用**HostConfig**群組中的[Docker 容器建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)來管理這些設定，包括：

* **記憶體**：記憶體限制（以位元組為單位）。 例如，268435456位元組 = 256 MB。
* **MemorySwap**：記憶體總計限制 (記憶體 + 交換) 。 例如，536870912位元組 = 512 MB
* **CpuPeriod**： CPU 期間的長度（以微秒為單位）。 預設值為100000，因此，例如，25000值會將容器限制為 CPU 資源的25%。

在 template.js的格式中，這些值看起來會像下列範例：

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

針對最終部署資訊清單 stringified 之後，這些值看起來會像下列範例：

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>後續步驟

如需建立選項的更多範例，請參閱下列 IoT Edge 範例：

* [Raspberry Pi 3 上的自訂視覺和 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge blob 儲存體範例](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
