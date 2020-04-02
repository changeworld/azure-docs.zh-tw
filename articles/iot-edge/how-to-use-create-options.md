---
title: 為模組寫入建立選項 - Azure IoT 邊緣 |微軟文件
description: 如何使用部署清單中的 create 選項在執行時設定模組
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550340"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>如何設定 IoT 邊緣模組的容器建立選項

部署清單中的**createOptions**參數使您能夠在執行時設定模組容器。 此參數擴展了您對模組的控制,並允許執行允許或限制模組訪問主機設備資源或配置網路等任務。

IoT Edge 模組在 IoT Edge 設備上作為與 Docker 相容的容器實現。 Docker 提供了許多用於創建容器的選項,這些選項也適用於 IoT Edge 模組。 關於詳細資訊,請參考[Docker 容器建立選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)。

## <a name="format-create-options"></a>格式建立選項

IoT Edge 部署清單接受格式化為 JSON 的創建選項。 例如,以每個邊緣Hub模組自動包含的建立選項為例:

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

此 EdgeHub 範例使用**HostConfig.PortBindings**參數將容器上的暴露埠映射到主機裝置上的連接埠。

如果對 Visual Studio 或可視化工作室代碼使用 Azure IoT 工具擴展名,則可以在**部署.template.json**檔中以 JSON 格式編寫創建選項。 然後,當您使用擴展生成 IoT Edge 解決方案或生成部署清單時,它將以 IoT Edge 執行時期望的格式為您字串化 JSON。 例如：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

編寫建立選項的一個提示是使用`docker inspect`該命令。 作為開發過程的一部分,請使用 在本地`docker run <container name>`使用運行模組。 一旦模組以您想要的方式工作,執行`docker inspect <container name>`。 此命令以 JSON 格式輸出模組詳細資訊。 尋找您配置的參數,並複製 JSON。 例如：

[![碼頭檢查邊緣 Hub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)的結果](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>常見的案例

容器建立選項支援許多方案,但以下是建構 IoT Edge 解決方案時最常出現的方案:

* [為模組提供對主機儲存的存取權限](how-to-access-host-storage-from-module.md)
* [將主機連接埠映射到模組連接埠](#map-host-port-to-module-port)
* [限制模組記憶體和 CPU 使用率](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>將主機連接埠映射到模組連接埠

如果您的模組需要與 IoT Edge 解決方案外部的服務通訊,並且不使用消息路由來執行此操作,則需要將主機埠映射到模組埠。

>[!TIP]
>在同一設備上的模組到模組通信不需要此埠映射。 如果模組 A 需要查詢模組 B 上託管的 API,則可以在沒有任何埠映射的情況下執行此操作。 模組 B 需要公開 docker 檔案中的連接`EXPOSE 8080`埠,例如 : 。 然後模組 A 可以使用模組 B 的名稱查詢`http://ModuleB:8080/api`API,例如: 。

首先,確保模組內的埠公開以偵聽連接。 您可以使用 docker 檔中的[EXPOSE](https://docs.docker.com/engine/reference/builder/#expose)指令執行此操作。 例如： `EXPOSE 8080` 。 如果未指定,則公開指令預設為 TCP 協定,或者您可以指定 UDP。

然後,使用 Docker 容器**的 HostConfig**組中的**PortBindings**設定[創建選項](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate),將模組中的外露埠映射到主機設備上的連接埠。 例如,如果在模組內公開埠 8080,並希望將埠映射到主機設備的埠 80,則範本.json 檔中的創建選項類似於以下範例:

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

為部署清單進行字串化後,相同的配置將類似於以下範例:

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>限制模組記憶體和 CPU 使用率

您可以聲明模組可以使用的主機資源量。 此控制項有助於確保一個模組不會消耗過多的記憶體或 CPU 使用率,並防止其他進程在設備上運行。 您可以使用[Docker 容器在](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) **HostConfig**群組建立選項來管理這些設定,包括:

* **記憶體**: 記憶體限制(以位元組為單位)。 例如,268435456 位元組 = 256 MB。
* **記憶體交換**:總記憶體限制(記憶體 + 交換)。 例如,536870912 位元組 = 512 MB
* **Cpu 週期**:CPU 週期的長度(以微秒為單位)。 默認值為 100000,因此,例如,值 25000 將容器限制為 CPU 資源的 25%。

在樣本.json 格式中,這些值類似於以下範例:

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

為最終部署清單進行字串化後,這些值將類似於以下範例:

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>後續步驟

有關建立操作選項的更多範例,請參閱以下 IoT 邊緣範例:

* [樹莓派 3 上的自訂視覺和 Azure IoT 邊緣](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT 邊緣 Blob 儲存範例](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
