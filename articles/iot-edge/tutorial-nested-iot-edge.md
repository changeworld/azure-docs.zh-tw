---
title: 教學課程 - 建立 IoT Edge 裝置的階層架構 - Azure IoT Edge
description: 本教學課程說明如何使用閘道建立 IoT Edge 裝置的階層式結構。
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 28b34ecaf51406b35c67d3838714691390f5adf7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453056"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>教學課程：建立 IoT Edge 裝置的階層架構 (預覽)

跨網路部署以階層式層級組織的 Azure IoT Edge 節點。 階層中的每一層都是一個閘道裝置，可處理來自其下層中裝置的訊息和要求。

>[!NOTE]
>這項功能需要目前處於公開預覽狀態，且執行 Linux 容器的 IoT Edge 1.2 版本。

您可以建立裝置階層的結構，讓只有最上層能夠連線到雲端，而較低層只能與相鄰的北端和南端層通訊。 此網路分層是大部分產業網路的基礎，遵循 [ISA-95 標準](https://en.wikipedia.org/wiki/ANSI/ISA-95)。

本教學課程的目的是要建立模擬生產環境之 IoT Edge 裝置的階層。 最後，您會透過階層下載容器映像，將[模擬的溫度感應器模組](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor)部署到較低層的裝置，而不需要存取網際網路。

為了達成此目標，本教學課程將逐步引導您建立 IoT Edge 裝置的階層、將 IoT Edge 執行時間容器部署到您的裝置，以及在本機設定裝置。 在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> * 在 IoT Edge 裝置的階層中建立和定義關聯性。
> * 在階層中的裝置上設定 IoT Edge 執行階段。
> * 在您的裝置階層中安裝一致的憑證。
> * 將工作負載新增至階層中的裝置。
> * 使用 API proxy 模組，透過來自較低層裝置的單一連接埠安全地路由傳送 HTTP 流量。

在本教學課程中，會定義下列網路層：

* **最上層**：這一層的 IoT Edge 裝置可以直接連線到雲端。

* **較低層**：這一層的 IoT Edge 裝置無法直接連線到雲端。 這些裝置需要經過一或多個中繼 IoT Edge 裝置，才能傳送和接收資料。

為了簡單起見，本教學課程使用兩個裝置階層。 一部裝置 (**topLayerDevice**) 代表位於階層最上層的裝置，可直接連線到雲端。 此裝置也會稱為 **父裝置**。 一部裝置 (**lowerLayerDevice**) 代表位於階層較低層的裝置，無法直接連線到雲端。 此裝置也會稱為 **子裝置**。 您可以新增其他較低層的裝置來代表您的生產環境。 任何其他較低層裝置的設定將會遵循 **lowerLayerDevice** 的設定。

## <a name="prerequisites"></a>必要條件

若要建立 IoT Edge 裝置的階層架構，您需要：

* 具有網際網路連線能力的電腦 (Windows 或 Linux)。
* 兩部要設定為 IoT Edge 裝置的 Linux 裝置。 如果您沒有可用的裝置，可以使用 [Azure 虛擬機器](../virtual-machines/linux/index.yml)。
* 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有 [Azure 訂用帳戶](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。
* 在 Azure 中擁有免費或標準層的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。
* 已安裝 Azure IoT 擴充功能 v0.10.6 或更新版本的 Azure CLI v2.3.1。 本教學課程會使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 如果您不熟悉 Azure Cloud Shell，請[參閱快速入門以取得詳細資料](./quickstart-linux.md#use-azure-cloud-shell)。

您也可以遵循經指令碼編寫的[適用於工業 IoT 的 Azure IoT Edge 範例](https://aka.ms/iotedge-nested-sample)試用此案例，其會將 Azure 虛擬機器部署為預先設定的裝置，以模擬工廠環境。

## <a name="configure-your-iot-edge-device-hierarchy"></a>設定您的 IoT Edge 裝置階層

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>建立 IoT Edge 裝置的階層架構

建立 IoT Edge 裝置的第一個步驟，可以透過 Azure 入口網站或 Azure CLI 來完成。 本教學課程將建立兩個 IoT Edge 裝置的階層：**topLayerDevice** 及其子系 **lowerLayerDevice**。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，瀏覽至您的 IoT 中樞。

1. 從左窗格上的功能表，在 [自動裝置管理] 下方選取 [IoT Edge]。

1. 選取 [+新增 IoT Edge 裝置]。 此裝置將會是最上層裝置，因此請輸入適當的唯一裝置識別碼。 選取 [儲存]。

1. 再次選取 [+新增 IoT Edge 裝置]。 此裝置將會是較低層邊緣裝置，因此請輸入適當的唯一裝置識別碼。

1. 選擇 [設定父裝置]，從裝置清單中選擇您的最上層裝置，然後選取 [確定]。 選取 [儲存]。

   ![設定較低層裝置的父裝置](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中輸入下列命令，以在中樞內建立 IoT Edge 裝置。 此裝置將會是最上層裝置，因此請輸入適當的唯一裝置識別碼：

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. 輸入下列命令來建立您的子 IoT Edge 裝置，並在裝置之間建立父系與子系的關聯性：

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

請記下每個 IoT Edge 裝置的連接字串。 稍後將會用到。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，瀏覽至 IoT 中樞的 IoT Edge 區段。

1. 從裝置清單中按一下其中一個裝置的裝置識別碼。

1. 選取 **主要連接字串** 欄位中的 [複製]，將資料記錄在您選擇的位置。

1. 對其他所有裝置重複執行此動作。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中，針對每個裝置輸入下列命令來取出裝置的連接字串，並將其記錄在您選擇的位置：

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

### <a name="create-certificates"></a>建立憑證

[閘道案例](iot-edge-as-gateway.md)中的所有裝置都需要共用憑證，才能設定兩者之間的安全連線。 在此案例中，請使用下列步驟來建立這兩個裝置的示範憑證。

若要在 Linux 裝置上建立示範憑證，您需要複製產生指令碼，並設定為在 Bash 中於本機執行。

1. 複製包含指令碼的 Git 存放庫來產生非生產環境憑證。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. 瀏覽至您要使用的目錄。 所有憑證和金鑰檔案都會建立在此目錄中。

1. 將組態和指令檔複製到您的工作目錄。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. 建立根 CA 憑證和一個中繼憑證。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此指令碼命令會建立數個憑證和金鑰檔案，但我們會使用下列檔案作為閘道階層的 **根 CA 憑證**：

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. 使用下列命令建立兩組 IoT Edge 裝置 CA 憑證和私密金鑰：一組用於最上層裝置，另一組用於較低層的裝置。 為 CA 憑證提供易記的名稱，以區別彼此。

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   此指令碼命令會建立數個憑證和金鑰檔案，但我們會在每個 IoT Edge 裝置上使用下列憑證和金鑰組，並在 config.yaml 檔案中參考：

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

每個裝置都需要根 CA 憑證的複本，以及自己裝置的 CA 憑證和私密金鑰複本。 您可以使用 USB 磁碟機或[安全的檔案複製](https://www.ssh.com/ssh/scp/)，將憑證移至每個裝置。

1. 憑證傳輸之後，請為每個裝置安裝根 CA。

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   此命令應該會提供輸出內容，顯示已在 /etc/ssl/certs 中新增一個憑證。

### <a name="install-iot-edge-on-the-devices"></a>在裝置上安裝 IoT Edge

在這兩個裝置上遵循下列步驟，以安裝 IoT Edge。

1. 更新裝置上的套件清單。

   ```bash
   sudo apt-get update
   ```

1. 安裝 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

1. 安裝 hsmlib 和 IoT Edge 精靈 <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/libiothsm-std_1.2.0.rc2-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc2/iotedge_1.2.0_rc2-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>設定 IoT Edge 執行階段

在這兩個裝置上執行下列步驟，以設定 IoT Edge 執行階段。 為您的裝置設定 IoT Edge 執行階段共包含四個步驟，全部都是藉由編輯 IoT Edge 組態檔來完成：

1. 藉由將裝置的連接字串新增至設定檔，手動佈建每個裝置。

1. 將設定檔指向裝置 CA 憑證、裝置 CA 私密金鑰和根 CA 憑證，完成設定裝置的憑證。

1. 使用 IoT Edge 代理程式啟動系統。

1. 為您的 **最上層** 裝置更新 **主機名稱** 參數，並更新 **較低層** 裝置的 **主機名稱** 參數和 **parent_hostname** 參數。

完成這些步驟，然後將 IoT Edge 服務重新開機以設定您的裝置。

1. 開啟每個裝置的 IoT Edge 組態檔。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 尋找檔案的佈建組態，然後將 **使用連接字串手動佈建組態** 區段取消註解。

1. 以來自 IoT Edge 裝置的連接字串更新 **device_connection_string** 的值。 請確定已標註任何其他佈建區段。請確定 **佈建：** 行前面沒有空白字元，且巢狀項目會以兩個空格縮排。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >將剪貼簿內容貼入 Nano `Shift+Right Click` 或按下 `Shift+Insert`。

1. 尋找 **憑證** 區段。 取消註解並更新三個憑證欄位，以指向您在上一節中建立並移至 IoT Edge 裝置的憑證。 提供檔案 URI 路徑，格式為 `file:///<path>/<filename>`。

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. 尋找 **最上層** 裝置的 **主機名稱** 參數。 將值更新為 IoT Edge 裝置的完整網域名稱 (FQDN) 或 IP 位址。 在階層中的裝置上，使用您所選擇的任何相同值。

   ```yml
   hostname: <device fqdn or IP>
   ```

1. 更新 **較低層** 中 IoT Edge 裝置的設定檔以指向父裝置的 FQDN 或 IP，並比對父裝置 **主機名稱** 欄位中的所有內容。 對於 **最上層** 中的 IoT Edge 裝置，請將此參數保留為已註解。

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > 針對具有多個較低層級的階層，立即使用上述層中裝置的 FQDN，更新「parent_hostname」欄位。

1. 尋找您 **最上層** 裝置的 **代理程式** yaml 區段，並將映像值更新為正確版本的 IoT Edge 代理程式。 在此情況下，我們會將最上層的 IoT Edge 代理程式指向 Azure Container Registry，並提供 IoT Edge 代理程式映像的公開預覽版本。

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. 針對 **較低層** 中的 IoT Edge 裝置，請將映像值的網域名稱更新為指向父裝置的 FQDN 或 IP，後面接著 API Proxy 連接埠 8000。 您將在下一節中新增 API Proxy 模組。

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. 儲存並關閉檔案。

   `CTRL + X`, `Y`, `Enter`

1. 在組態檔中輸入佈建資訊之後，請重新啟動精靈：

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>將模組部署到最上層裝置

設定 IoT Edge 執行階段和部署工作負載的其餘步驟，必須透過 Azure 入口網站或 Azure CLI 才能完成。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 [Azure 入口網站](https://ms.portal.azure.com/)：

1. 瀏覽至您的 IoT 中樞。

1. 從左窗格上的功能表，在 [自動裝置管理] 下方選取 [IoT Edge]。

1. 從裝置清單中按一下 **最上層** 邊緣裝置的裝置識別碼。

1. 在上方列中，選取 [設定模組]。

1. 選取齒輪圖示旁邊的 [執行階段設定]。

1. 在 **Edge 中樞** 下的映像欄位中，輸入 `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2`。

   ![編輯 Edge 中樞的映像](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. 將下列環境變數新增至 Edge 中樞模組：

    | 名稱 | 值 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![編輯 Edge 中樞的環境變數](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. 在 **Edge 代理程式** 下的映像欄位中，輸入 `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2`。 選取 [儲存]。

1. 將 Docker 登錄模組新增至您的最上層裝置。 選取 [+ 新增]，然後從下拉式清單中選擇 [IoT Edge 模組]。 提供 Docker 登錄模組 `registry` 名稱 ，並輸入映像 URI `registry:latest`。 接下來，新增環境變數並建立選項，以指向 Microsoft 容器登錄上的本機登錄模組，以便下載容器映像，並在 registry:5000 提供這些映像。

1. 在環境變數索引標籤下，輸入下列環境變數名稱-值配對：

    | 名稱 | 值 |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. 在容器建立選項索引標籤下，輸入下列 JSON：

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. 接下來，將 API Proxy 模組新增至您的最上層裝置。 選取 [+ 新增]，然後從下拉式清單中選擇 [Marketplace 模組]。 搜尋 `IoT Edge API Proxy` 並選取該模組。 IoT Edge API Proxy 會使用連接埠 8000，並且預設會設定為使用您在連接埠 5000 上名為 `registry` 的登錄模組。

1. 選取 [檢閱 + 建立]，然後選取 [建立] 完成部署。 可存取網際網路的最上層裝置 IoT Edge 執行階段會提取並執行 IoT Edge 中樞和 IoT Edge 代理程式的 **公用預覽** 。

   ![包含 Edge 中樞、Edge 代理程式、登錄模組和 API Proxy 模組的完整部署](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中輸入下列命令，以建立 deployment.json 檔案：

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. 將以下 JSON 的內容複寫到您的 deployment.json 中，加以儲存並關閉。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "dockerContainerRegistry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 輸入下列命令，以建立最上層邊緣裝置的部署：

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

## <a name="deploy-modules-to-the-lower-layer-device"></a>將模組部署到較低層裝置

您可以使用 Azure 入口網站和 Azure CLI，將工作負載從雲端部署到 **較低層** 裝置。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 [Azure 入口網站](https://ms.portal.azure.com/)：

1. 瀏覽至您的 IoT 中樞。

1. 從左窗格上的功能表，在 [自動裝置管理] 下方選取 [IoT Edge]。

1. 從 IoT Edge 裝置清單中按一下較低層裝置的裝置識別碼。

1. 在上方列中，選取 [設定模組]。

1. 選取齒輪圖示旁邊的 [執行階段設定]。

1. 在 **Edge 中樞** 下的映像欄位中，輸入 `$upstream:8000/azureiotedge-hub:1.2.0-rc2`。

1. 將下列環境變數新增至 Edge 中樞模組：

    | 名稱 | 值 |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. 在 **Edge 代理程式** 下的映像欄位中，輸入 `$upstream:8000/azureiotedge-agent:1.2.0-rc2`。 選取 [儲存]。

1. 新增溫度感應器模組。 選取 [+ 新增]，然後從下拉式清單中選擇 [Marketplace 模組]。 搜尋 `Simulated Temperature Sensor` 並選取該模組。

1. 在 **IoT Edge 模組** 中選取您剛才新增的 `Simulated Temperature Sensor` 模組，並將其映像 URI 更新為指向 `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0`。

1. 選取 [儲存]，然後選取 [檢閱 + 建立]，再選取 [建立] 完成部署。

   ![包含 Edge 中樞、Edge 代理程式和模擬溫度感應器的完整部署](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 在 [Azure Cloud Shell](https://shell.azure.com/) 中輸入下列命令，以建立 deployment.json 檔案：

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. 將以下 JSON 的內容複寫到您的 deployment.json 中，加以儲存並關閉。

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25",
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. 輸入下列命令，以建立較低層邊緣裝置的模組設定部署：

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## View generated data

The **Simulated Temperature Sensor** module that you pushed generates sample environment data. It sends messages that include ambient temperature and humidity, machine temperature and pressure, and a timestamp.

You can watch the messages arrive at your IoT hub by using the [Azure IoT Hub extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

You can also view these messages through the [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>清除資源

您可以刪除您在本文中建立的本機設定和 Azure 資源，以避免產生費用。

若要刪除資源：

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後選取 [資源群組]  。

2. 選取您的 IoT Edge 測試資源所屬的資源群組名稱。 

3. 檢閱您的資源群組中包含的資源清單。 若要將其全部刪除，您可以選取 [刪除資源群組]。 如果只要刪除某些部分，您可以按一下各個資源將其個別刪除。 

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已將兩個 IoT Edge 裝置設定為閘道，並將其中之一設為另一個的父裝置。 然後，您示範了如何透過閘道將容器映像提取到子裝置上。 您也可以遵循經指令碼編寫的[適用於工業 IoT 的 Azure IoT Edge 範例](https://aka.ms/iotedge-nested-sample)試用此案例，其會將 Azure 虛擬機器部署為預先設定的裝置，以模擬工廠環境。

若要進一步了解 Azure IoT Edge 可如何為您的企業建立解決方案，請繼續閱讀其他教學課程。

> [!div class="nextstepaction"]
> [將 Azure Machine Learning 模型部署為模組](tutorial-deploy-machine-learning.md)