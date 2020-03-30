---
title: 如何從頭開始為 Azure 部署 OPC 雙子模組 |微軟文檔
description: 本文介紹如何使用 Azure 門戶的 IoT 邊緣邊邊邊邊邊選項卡以及使用 AZ CLI 從頭開始部署 OPC Twin。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 6c8ceeaf49d8ebfa15a83118e8b518190f6ff85e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241064"
---
# <a name="deploy-opc-twin-module-and-dependencies-from-scratch"></a>從頭開始部署 OPC 雙子模組和依賴項

OPC 雙子模組在 IoT 邊緣上運行，並為 OPC 設備孿生和註冊表服務提供多項邊緣服務。 

有幾個選項可以將模組部署到[Azure IoT 邊緣](https://azure.microsoft.com/services/iot-edge/)閘道，其中包括

- [從 Azure 門戶的 IoT 邊緣邊邊邊邊邊邊邊選項卡進行部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-portal)
- [使用 AZ CLI 進行部署](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-monitor-cli)

> [!NOTE]
> 有關部署詳細資訊和說明的詳細資訊，請參閱 GitHub[存儲庫](https://github.com/Azure/azure-iiot-components)。

## <a name="deployment-manifest"></a>部署資訊清單

所有模組都使用部署清單部署。  下面顯示了部署[OPC 發佈伺服器](https://github.com/Azure/iot-edge-opc-publisher)和[OPC 孿生](https://github.com/Azure/azure-iiot-opc-twin-module)的示例清單。

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

## <a name="deploying-from-azure-portal"></a>從 Azure 門戶部署

將模組部署到 Azure IoT 邊緣閘道設備的最簡單方法是通過 Azure 門戶。  

### <a name="prerequisites"></a>Prerequisites

1. 部署 OPC 孿生[依賴項](howto-opc-twin-deploy-dependencies.md)並獲取`.env`生成的檔。 請注意在生成的`hub name``.env`檔中部署`PCS_IOTHUBREACT_HUB_NAME`的變數。

2. 註冊並啟動[Linux](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-linux)或[Windows](https://docs.microsoft.com/azure/iot-edge/how-to-install-iot-edge-windows) IoT 邊緣`device id`閘道並記下其 。

### <a name="deploy-to-an-edge-device"></a>部署到邊緣設備

1. 登入 [Azure 入口網站](https://portal.azure.com/)，然後瀏覽至 IoT 中樞。

2. 從左側功能表中選擇**IoT 邊緣**。

3. 按一下裝置清單中目標裝置的識別碼。

4. 選擇**設置模組**。

5. 在頁面的 **"部署"模組**部分中，選擇 **"添加**"和 **"IoT 邊緣"模組。**

6. 在**IoT 邊緣自訂模組**對話方塊`opctwin`中用作模組的名稱，然後將容器*映射 URI*指定為

   ```bash
   mcr.microsoft.com/iotedge/opc-twin:latest
   ```

   作為*容器創建選項*，請使用以下 JSON：

   ```json
   {"NetworkingConfig": {"EndpointsConfig": {"host": {}}}, "HostConfig": {"NetworkMode": "host" }}
   ```

   視需要填寫選擇性欄位。 如需容器建立選項、重新啟動原則和所需狀態的詳細資訊，請參閱 [EdgeAgent 所需屬性](https://docs.microsoft.com/azure/iot-edge/module-edgeagent-edgehub#edgeagent-desired-properties)。 如需模組對應項的詳細資訊，請參閱[定義或更新所需屬性](https://docs.microsoft.com/azure/iot-edge/module-composition#define-or-update-desired-properties)。

7. 選擇 **"保存"** 並重複步驟**5**。  

8. 在 IoT 邊緣自訂模組對話方塊中`opcpublisher`，用作模組的名稱，將容器*映射 URI*用作 

   ```bash
   mcr.microsoft.com/iotedge/opc-publisher:latest
   ```

   作為*容器創建選項*，請使用以下 JSON：

   ```json
   {"Hostname":"publisher","Cmd":["publisher","--pf=./pn.json","--di=60","--to","--aa","--si=0","--ms=0"],"ExposedPorts":{"62222/tcp":{}},"HostConfig":{"PortBindings":{"62222/tcp":[{"HostPort":"62222"}] }}}
   ```

9. 選擇 **"保存****"，然後選擇"下一步**"以繼續到路徑部分。

10. 在路由選項卡中，粘貼以下內容 

    ```json
    {
      "routes": {
        "opctwinToIoTHub": "FROM /messages/modules/opctwin/* INTO $upstream",
        "opcpublisherToIoTHub": "FROM /messages/modules/opcpublisher/* INTO $upstream"
      }
    }
    ```

    並選擇 **"下一步"**

11. 查看部署資訊和清單。  它應類似于上述部署清單。  選取 [提交]****。

12. 一旦您將模組部署到您的裝置，就可以在入口網站的 [裝置詳細資料]**** 頁面中檢視所有項目。 此頁面會顯示每個已部署模組的名稱，以及像是部署狀態和結束代碼的實用資訊。

## <a name="deploying-using-azure-cli"></a>使用 Azure CLI 進行部署

### <a name="prerequisites"></a>Prerequisites

1. [從這裡](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)安裝最新版本的[Azure 命令列介面 （AZ）。](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

### <a name="quickstart"></a>快速入門

1. 將上述部署清單保存到檔中`deployment.json`。  

2. 使用下列命令，將組態套用至 IoT Edge 裝置：

   ```azurecli
   az iot edge set-modules --device-id [device id] --hub-name [hub name] --content ./deployment.json
   ```

   參數`device id`區分大小寫。 內容參數會指向您已儲存的部署資訊清單檔案。 
    ![az IoT 邊緣設置模組輸出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/set-modules.png)

3. 將模組部署至裝置後，您可以使用下列命令檢視所有模組：

   ```azurecli
   az iot hub module-identity list --device-id [device id] --hub-name [hub name]
   ```

   裝置識別碼 參數區分大小寫。 ![az iot hub module-identity list 輸出](https://docs.microsoft.com/azure/iot-edge/media/how-to-deploy-cli/list-modules.png)

## <a name="next-steps"></a>後續步驟

現在，您已經瞭解如何從頭開始部署 OPC Twin，下面是建議的下一步：

> [!div class="nextstepaction"]
> [將 OPC 孿生部署到現有專案](howto-opc-twin-deploy-existing.md)
