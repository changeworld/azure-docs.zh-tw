---
title: 如何從頭開始部署適用于 Azure 的 OPC 對應項模組 |Microsoft Docs
description: 本文說明如何使用 Azure 入口網站的 IoT Edge 分頁，並使用 AZ CLI，從頭開始部署 OPC 對應項。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
ms.custom: devx-track-azurecli
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9ae3e9b4bb69bf0c85054b5d6144633923cac947
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91282063"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>從頭開始部署 OPC 對應項模組和相依性

> [!IMPORTANT]
> 當我們更新這篇文章時，請參閱 [Azure 產業 IoT](https://azure.github.io/Industrial-IoT/) 的最新內容。

OPC 對應項模組會在 IoT Edge 上執行，並為 OPC 裝置對應項和登錄服務提供數個 Edge 服務。 

有幾個選項可以將模組部署到您的 [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 閘道

- [從 Azure 入口網站的 IoT Edge blade 部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [使用 AZ CLI 進行部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> 如需部署詳細資料和指示的詳細資訊，請參閱 GitHub 存放 [庫](https://github.com/Azure/azure-iiot-components)。

## <a name="deployment-manifest"></a>部署資訊清單

所有模組都是使用部署資訊清單來部署。  部署 [Opc 發行者](https://github.com/Azure/iot-edge-opc-publisher) 和 [opc](https://github.com/Azure/azure-iiot-opc-twin-module) 對應項的範例資訊清單如下所示。

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
                "createOptions": ""
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "opctwin": {
              "version": "1.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-twin:latest",
                "createOptions": "{\"NetworkingConfig\": {\"EndpointsConfig\": {\"host\": {}}}, \"HostConfig\": {\"NetworkMode\": \"host\" }}"
              }
            },
            "opcpublisher": {
              "version": "2.0",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/iotedge/opc-publisher:latest",
                "createOptions": "{\"Hostname\":\"publisher\",\"Cmd\":[\"publisher\",\"--pf=./pn.json\",\"--di=60\",\"--to\",\"--aa\",\"--si=0\",\"--ms=0\"],\"ExposedPorts\":{\"62222/tcp\":{}},\"NetworkingConfig\":{\"EndpointsConfig\":{\"host\":{}}},\"HostConfig\":{\"NetworkMode\":\"host\",\"PortBindings\":{\"62222/tcp\":[{\"HostPort\":\"62222\"}]}}}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
            "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      }
    }
  }
}
```

## <a name="deploying-from-azure-portal"></a>從 Azure 入口網站部署

將模組部署到 Azure IoT Edge 閘道裝置最簡單的方式是透過 Azure 入口網站。  

### <a name="prerequisites"></a>Prerequisites

1. 部署 OPC 對應 [項](howto-opc-twin-deploy-dependencies.md) 相依性，並取得產生的檔案 `.env` 。 請注意產生的檔案中已部署的 `hub name` `PCS_IOTHUBREACT_HUB_NAME` 變數 `.env` 。

2. 註冊並啟動 [Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux) 或 [Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT Edge 閘道，並記下它 `device id` 。

### <a name="deploy-to-an-edge-device"></a>部署至 edge 裝置

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 IoT 中樞。

2. 從左側功能表中選取 [ **IoT Edge** ]。

3. 按一下裝置清單中目標裝置的識別碼。

4. 選取 [ **設定模組**]。

5. 在頁面的 [ **部署模組** ] 區段中，選取 [ **新增** ] 和 [ **IoT Edge 模組]。**

6. 在 [ **IoT Edge 自訂模組** ] 對話方塊中 `opctwin` ，使用做為模組的名稱，然後將容器 *映射 URI* 指定為

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   作為 *容器建立選項*，請使用下列 JSON：

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   視需要填寫選擇性欄位。 如需容器建立選項、重新啟動原則和所需狀態的詳細資訊，請參閱 [EdgeAgent 所需屬性](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)。 如需模組對應項的詳細資訊，請參閱[定義或更新所需屬性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

7. 選取 [ **儲存** ] 並重複步驟 **5**。  

8. 在 [IoT Edge 自訂模組] 對話方塊中，使用 `opcpublisher` 做為模組的名稱，並將容器 *映射 URI* 作為 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   作為 *容器建立選項*，請使用下列 JSON：

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. 選取 [ **儲存** ]，然後按 **[下一步]** 繼續前往 [路由] 區段。

10. 在 [路由] 索引標籤中，貼上下列 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    然後選取 **[下一步]**

11. 檢查您的部署資訊與資訊清單。  它看起來應該像上述的部署資訊清單。  選取 [提交]。

12. 一旦您將模組部署到您的裝置，就可以在入口網站的 [裝置詳細資料]**** 頁面中檢視所有項目。 此頁面會顯示每個已部署模組的名稱，以及像是部署狀態和結束代碼的實用資訊。

## <a name="deploying-using-azure-cli"></a>使用 Azure CLI 部署

### <a name="prerequisites"></a>Prerequisites

1. 從[這裡](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)安裝最新版本的[Azure 命令列介面 (AZ) ](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) 。

### <a name="quickstart"></a>快速入門

1. 將上述部署資訊清單儲存至檔案 `deployment.json` 。  

2. 使用下列命令，將組態套用至 IoT Edge 裝置：

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   `device id`參數會區分大小寫。 內容參數會指向您已儲存的部署資訊清單檔案。 
    ![az IoT Edge 設定模組輸出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. 將模組部署至裝置後，您可以使用下列命令檢視所有模組：

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   裝置識別碼參數會區分大小寫。 ![az iot hub module-identity list 輸出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>後續步驟

現在您已瞭解如何從頭開始部署 OPC 對應項，以下是建議的後續步驟：

> [!div class="nextstepaction"]
> [將 OPC 對應項部署至現有的專案](howto-opc-twin-deploy-existing.md)
