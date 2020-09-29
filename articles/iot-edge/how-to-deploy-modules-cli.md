---
title: 從 Azure CLI 命令列部署模組 - Azure IoT Edge
description: 搭配 Azure IoT 延伸模組使用 Azure CLI，將 IoT Edge 模組如部署資訊清單所設定，從 IoT 中樞推送至 IoT Edge 裝置。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/16/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 24630295eaea04044273c412760d25bcddf41335
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91439681"
---
# <a name="deploy-azure-iot-edge-modules-with-azure-cli"></a>使用 Azure CLI 部署 Azure IoT Edge 模組

只要您使用商務邏輯建立 IoT Edge 模組，便需要將這些模組部署到裝置以在邊緣操作。 如果您有多個模組共同運作以收集及處理資料，您可以一次部署所有模組，並宣告這些模組的路由規則。

[Azure CLI](/cli/azure) 是一個開放原始碼跨平台命令列工具，用來管理 Azure 資源 (例如 IoT Edge)。 它可讓您管理 Azure IoT 中樞資源、裝置佈建服務執行個體，以及現成的連結中樞。 新的 IoT 擴充功能會以裝置管理和完整 IoT Edge 功能等功能來擴充 Azure CLI 的功能。

本文說明如何建立 JSON 部署資訊清單，然後使用該檔案將部署發送到 IoT Edge 裝置。 如需根據裝置共用標籤，建立目標為多個裝置的部署資訊，請參閱[大規模部署和監視 IoT Edge 模組](how-to-deploy-cli-at-scale.md) (英文)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶中的 [IoT 中樞](../iot-hub/iot-hub-create-using-cli.md)。
* 已安裝 IoT Edge 執行階段的 [IoT Edge 裝置](how-to-register-device.md#register-with-the-azure-cli)。
* 您環境中的 [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI 版本至少必須是 2.0.70 或更新版本。 使用 `az --version` 進行驗證。 這個版本支援 az 擴充命令並引進 Knack 命令架構。
* [適用於 Azure CLI 的 IoT 擴充功能](https://github.com/Azure/azure-iot-cli-extension) \(英文\)。

## <a name="configure-a-deployment-manifest"></a>設定部署資訊清單

部署資訊清單為 JSON 文件，說明應部署的模組、資料如何在模組之間流動，以及想要的模組對應項需要的屬性。 如需部署資訊清單的功能，以及如何建立此類清單的詳細資訊，請參閱[了解如何使用、設定以及重複使用 IoT Edge 模組](module-composition.md) (英文)。

若要使用 Azure CLI 部署模組，請在本機上將部署資訊清單儲存成 .json 檔案。 若要執行命令以將設定套用至裝置，您會使用到下一節中的檔案路徑。

下面以具有一個模組的基本部署資訊清單為例：

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

## <a name="deploy-to-your-device"></a>部署至裝置

您可以透過套用您運用模組資訊設定的部署資訊清單，將模組部署到裝置上。

將目錄變更為儲存您部署資訊清單的資料夾。 如果您使用其中一個 VS Code IoT Edge 範本，請使用 `deployment.json` 檔案，此檔案位在解決方案目錄的 **config** 資料夾中，而不是使用 `deployment.template.json` 檔案。

使用下列命令，將組態套用至 IoT Edge 裝置：

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content [file path]
   ```

裝置識別碼參數會區分大小寫。 內容參數會指向您已儲存的部署資訊清單檔案。

   ![az iot edge set-modules 輸出](./media/how-to-deploy-cli/set-modules.png)

## <a name="view-modules-on-your-device"></a>在裝置上檢視模組

將模組部署至裝置後，您可以使用下列命令檢視所有模組：

在您的 IoT Edge 裝置上檢視模組：

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

裝置識別碼參數會區分大小寫。

   ![az iot hub module-identity list 輸出](./media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>後續步驟

了解如何[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md) (英文)
