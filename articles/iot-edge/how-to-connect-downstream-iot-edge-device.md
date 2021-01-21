---
title: 連接下游 IoT Edge 裝置-Azure IoT Edge |Microsoft Docs
description: 如何設定 IoT Edge 裝置以連線到 Azure IoT Edge 閘道裝置。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1258fd4b5c69b399b70d1f2db1be63765771e631
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629398"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>將下游 IoT Edge 裝置連線至 Azure IoT Edge 閘道 (預覽) 

本文提供在 IoT Edge 閘道和下游 IoT Edge 裝置之間建立信任連線的指示。

>[!NOTE]
>這項功能需要目前處於公開預覽狀態，且執行 Linux 容器的 IoT Edge 1.2 版本。

在閘道案例中，IoT Edge 的裝置可以是閘道和下游裝置。 多個 IoT Edge 閘道可以分層來建立裝置的階層。 下游 (或子) 裝置可透過其閘道 (或父) 裝置來驗證及傳送或接收訊息。

閘道階層中的 IoT Edge 裝置有兩種不同的設定，而這兩者都有解決。 第一個是 IoT Edge 裝置的 **上層層** 。 當多個 IoT Edge 裝置彼此連線時，任何沒有父代裝置但直接連線至 IoT 中樞的裝置都會被視為在最上層。 此裝置負責處理來自其下所有裝置的要求。 另一項設定會套用至階層中 **較低層** 的任何 IoT Edge 裝置。 這些裝置可能是其他下游 IoT 和 IoT Edge 裝置的閘道，但也需要透過自己的父裝置路由傳送任何通訊。

某些網路架構需要階層中的最上層 IoT Edge 裝置才能連線至雲端。 在此設定中，階層較低層級中的所有 IoT Edge 裝置，只能與其閘道 (或父) 裝置，以及任何下游 (或子) 裝置通訊。

本文中的所有步驟都是以設定 [IoT Edge 裝置作為透明閘道的方式來](how-to-create-transparent-gateway.md)建立，其會將 IoT Edge 裝置設定為下游 IoT 裝置的閘道。 相同的基本步驟適用于所有閘道案例：

* **驗證**：為閘道階層中的所有裝置建立 IoT 中樞身分識別。
* **授權**：設定 IoT 中樞內的父子式關聯性，以授權子裝置連線至其父裝置，如同連線到 IoT 中樞。
* **閘道探索**：確定子裝置可以在區域網路上找到其父裝置。
* **安全** 連線：使用屬於相同鏈的受信任憑證建立安全連線。

## <a name="prerequisites"></a>必要條件

* 免費或標準的 IoT 中樞。
* 至少兩部 **IoT Edge 裝置**，一個是最上層裝置和一或多個較低層的裝置。 如果您沒有可用的 IoT Edge 裝置，可以 [在 Ubuntu 虛擬機器上執行 Azure IoT Edge](how-to-install-iot-edge-ubuntuvm.md)。
* 如果您使用 Azure CLI 來建立和管理裝置，請在安裝 Azure IoT 擴充功能 v 0.10.6 或更新版本時，使用 Azure CLI 的2.3.1。

本文提供詳細的步驟和選項，協助您為您的案例建立正確的閘道階層。 如需引導式教學課程，請參閱 [使用閘道建立 IoT Edge 裝置](tutorial-nested-iot-edge.md)的階層。

## <a name="create-a-gateway-hierarchy"></a>建立閘道階層

您可以藉由定義案例中 IoT Edge 裝置的父/子關聯性，來建立 IoT Edge 閘道階層。 當您建立新的裝置身分識別時，可以設定父裝置，也可以管理現有裝置身分識別的父系和子系。

設定父系/子系關聯性的步驟會授權子裝置連線至其父裝置，如同連線到 IoT 中樞一樣。

只有 IoT Edge 裝置可以是父裝置，但 IoT Edge 裝置和 IoT 裝置都可以是子系。 父系可以有許多子系，但子系只能有一個父系。 閘道階層的建立方式是將父/子集合連結在一起，讓某個裝置的子系為另一個裝置的父系。

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，您可以在建立新的裝置身分識別，或藉由編輯現有的裝置來管理父/子關聯性。

當您建立新的 IoT Edge 裝置時，您可以選擇從該中樞內現有的 IoT Edge 裝置清單中選擇父裝置和子裝置。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞。
1. 從導覽功能表中選取 [ **IoT Edge** ]。
1. 選取 [新增 IoT Edge 裝置]。
1. 除了設定裝置識別碼和驗證設定之外，您還可以 **設定父裝置** 或 **選擇子裝置**。
1. 選擇您想要作為父系或子系的裝置。

您也可以建立或管理現有裝置的父/子關聯性。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞。
1. 從導覽功能表中選取 [ **IoT Edge** ]。
1. 從 **IoT Edge 裝置** 清單中選取您想要管理的裝置。
1. 選取 [ **設定父裝置** ] 或 [ **管理子裝置**]。
1. 新增或移除任何父系或子裝置。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

適用于 Azure CLI 的 [azure iot 擴充功能](/cli/azure/ext/azure-iot) 提供管理 iot 資源的命令。 當您建立新的裝置身分識別或編輯現有的裝置時，您可以管理 IoT 和 IoT Edge 裝置的父/子關聯性。

[Az iot hub 裝置-](/cli/azure/ext/azure-iot/iot/hub/device-identity)一組命令可讓您管理指定裝置的父/子關聯性。

此 `create` 命令包含用於在裝置建立時新增子裝置和設定父裝置的參數。

其他裝置身分識別命令（包括 `add-children` 、 `list-children` 、和 `remove-children` 或 `get-parent` 和 `set-parent` ）可讓您管理現有裝置的父/子關聯性。

---

## <a name="prepare-certificates"></a>準備憑證

您必須在相同閘道階層中的所有裝置上安裝一致的憑證鏈，才能在本身之間建立安全通訊。 階層中的每個裝置（無論是 IoT Edge 裝置或 IoT 分葉裝置）都需要相同根 CA 憑證的複本。 階層中的每個 IoT Edge 裝置會接著使用根 CA 憑證作為其裝置 CA 憑證的根。

使用此設定時，每個下游 IoT Edge 裝置或 IoT 分葉裝置都可以驗證其父系的身分識別，方法是確認它們所連線的 edgeHub 具有由共用根 CA 憑證所簽署的伺服器憑證。

<!-- TODO: certificate graphic -->

建立下列憑證：

* **根 CA 憑證**，這是指定閘道階層中所有裝置最上層的共用憑證。 此憑證會安裝在所有裝置上。
* 任何您想要包含在根憑證鏈中的 **中繼憑證** 。
* 由根和中繼憑證所產生的 **裝置 CA 憑證** 及其 **私密金鑰**。 閘道階層中的每個 IoT Edge 裝置都需要一個唯一的裝置 CA 憑證。

>[!NOTE]
>目前，libiothsm 中的限制會防止使用在2038年1月1日或之後過期的憑證。

您可以使用自我簽署的憑證授權單位單位，也可以從信任的商業憑證授權單位單位（例如巴爾的摩、Verisign、Digicert 或 GlobalSign）購買一個。

如果您沒有自己的憑證可供使用，您可以 [建立示範憑證來測試 IoT Edge 的裝置功能](how-to-create-test-certificates.md)。 遵循該文章中的步驟來建立一組根和中繼憑證，然後為每個裝置建立 IoT Edge 的裝置 CA 憑證。

## <a name="configure-iot-edge-on-devices"></a>設定裝置上的 IoT Edge

將 IoT Edge 設定為閘道的步驟，與將 IoT Edge 設定為下游裝置的步驟非常類似。

若要啟用閘道探索，必須設定每個 IoT Edge 閘道裝置的 **主機名稱** ，以供其子裝置在區域網路上尋找它。 每個下游 IoT Edge 裝置都必須設定要連接的 **parent_hostname** 。 如果單一 IoT Edge 裝置同時為父裝置和子裝置，則需要這兩個參數。

若要啟用安全連線，閘道案例中的每個 IoT Edge 裝置都必須使用唯一的裝置 CA 憑證，以及閘道階層中所有裝置共用的根 CA 憑證複本進行設定。

您應該已在裝置上安裝 IoT Edge。 如果沒有，請遵循下列步驟 [在 IoT 中樞註冊 IoT Edge 裝置](how-to-register-device.md) ，然後 [安裝 Azure IoT Edge 運行](how-to-install-iot-edge.md)時間。

本節中的步驟會參考本文稍早所討論的 **根 CA 憑證** 和 **裝置 CA 憑證和私密金鑰** 。 如果您在不同的裝置上建立這些憑證，請在此裝置上提供這些憑證。 您可以使用像是 [Azure Key Vault](../key-vault/general/overview.md)的服務，或使用像是 [安全檔案複製](https://www.ssh.com/ssh/scp/)的功能，在實際的情況下傳輸檔案，例如使用 USB 磁片磁碟機。

使用下列步驟，在您的裝置上設定 IoT Edge。

在 Linux 上，請確定使用者 **iotedge** 具有保存憑證和金鑰之目錄的讀取權限。

1. 在此 IoT Edge 裝置上安裝 **根 CA 憑證** 。

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. 更新憑證存放區。

   ```bash
   sudo update-ca-certificates
   ```

   此命令應該會輸出一個憑證已新增至/etc/ssl/certs。

1. 開啟 IoT Edge 的安全性守護程式設定檔。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 在 yaml 檔案中尋找 [ **憑證** ] 區段。 更新三個憑證欄位以指向您的憑證。 提供檔案 URI 路徑，格式為 `file:///<path>/<filename>`。

   * **device_ca_cert**：此裝置唯一的裝置 ca 憑證的檔案 URI 路徑。
   * **device_ca_pk**：此裝置唯一的裝置 ca 私密金鑰的檔案 URI 路徑。
   * **trusted_ca_certs**：閘道階層中所有裝置共用之根 ca 憑證的檔案 URI 路徑。

1. 在 yaml 檔案中尋找 **hostname** 參數。 將主機名稱更新為 (FQDN) 或 IoT Edge 裝置 IP 位址的完整功能變數名稱。

   此參數的值是下游裝置將用來連線到此閘道的值。 主機名稱預設會採用電腦名稱稱，但必須要有 FQDN 或 IP 位址才能連接下游裝置。

   使用短于64個字元的主機名稱，也就是伺服器憑證一般名稱的字元限制。

   在閘道階層中與主機名稱模式保持一致。 請使用 Fqdn 或 IP 位址，但不能同時使用兩者。

1. **如果此裝置是子裝置**，請尋找 **parent_hostname** 參數。 將 **parent_hostname** 欄位更新為父裝置的 FQDN 或 IP 位址，以符合在父 yaml 檔中提供的主機名稱。

1. 雖然這項功能處於公開預覽狀態，但您必須將 IoT Edge 裝置設定為在啟動時使用 IoT Edge 代理程式的公開預覽版本。

   尋找 [ **代理程式** yaml] 區段，並將映射值更新為公開預覽影像：

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. 儲存 (`Ctrl+O`) 並關閉 (`Ctrl+X`) yaml 檔案。

1. 如果您之前已使用任何其他憑證進行 IoT Edge，請刪除下列兩個目錄中的檔案，以確定已套用新的憑證：

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. 重新開機 IoT Edge 服務，以套用您的變更。

   ```bash
   sudo systemctl restart iotedge
   ```

1. 檢查設定中是否有任何錯誤。

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >IoT Edge 檢查工具會使用容器來執行某些診斷檢查。 如果您想要在下游 IoT Edge 裝置上使用這項工具，請確定它們可以存取 `mcr.microsoft.com/azureiotedge-diagnostics:latest` ，或在您的私人容器登錄中擁有容器映射。

## <a name="configure-runtime-modules-for-public-preview"></a>為公開預覽設定執行時間模組

雖然這項功能處於公開預覽狀態，但您必須將 IoT Edge 裝置設定為使用 IoT Edge 執行時間模組的公開預覽版本。 上一節提供在啟動時設定 edgeAgent 的步驟。 您也需要在裝置的部署中設定執行時間模組。

1. 將 edgeHub 模組設定為使用公開預覽映射： `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` 。

1. 設定 edgeHub 模組的下列環境變數：

   | Name | 值 |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. 將 edgeAgent 模組設定為使用公開預覽映射： `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` 。

## <a name="network-isolate-downstream-devices"></a>網路隔離下游裝置

本文中到目前為止的步驟會將 IoT Edge 裝置設定為閘道或下游裝置，並在兩者之間建立信任的連接。 閘道裝置會處理下游裝置與 IoT 中樞之間的互動，包括驗證和訊息路由。 部署到下游 IoT Edge 裝置的模組仍然可以建立自己的雲端服務連線。

某些網路架構（例如遵循 ISA-95 標準的架構）會尋找最小化網際網路連線的數目。 在這些情況下，您可以設定下游 IoT Edge 裝置，而不需要直接連線到網際網路。 除了透過其閘道裝置路由傳送 IoT 中樞通訊之外，下游 IoT Edge 裝置也可以依賴閘道裝置來進行所有雲端連線。

此網路設定要求閘道階層最上層的 IoT Edge 裝置必須直接連線到雲端。 較低層的 IoT Edge 裝置只能與其父裝置或任何子裝置通訊。 閘道裝置上的特殊模組可啟用此案例，包括：

* 在其下有另一個 IoT Edge 裝置的任何 IoT Edge 閘道上，都需要 **API proxy 模組** 。 這表示它必須位在閘道階層的 *每一層* ，但底端層除外。 此課程模組會使用 [nginx](https://nginx.org) 反向 proxy，透過單一端口將 HTTP 資料透過網路層路由傳送。 其可透過其模組對應項和環境變數進行高度設定，因此可以調整以符合您的閘道案例需求。

* **Docker 登錄模組** 可以部署在閘道階層 *最上層* 的 IoT Edge 閘道上。 此模組負責代表較低層中的所有 IoT Edge 裝置，來取得和快取容器映射。 在最上層部署此模組的替代方式是使用本機登錄，或手動將容器映射載入至裝置，並將模組提取原則設定為 [ **永不**]。

* IoT Edge 上的 **Azure Blob 儲存體** 可以部署在閘道階層 *最上層* 的 IoT Edge 閘道上。 此模組負責代表較低層中的所有 IoT Edge 裝置上傳 blob。 上傳 blob 的能力也可針對較低層的 IoT Edge 裝置啟用有用的疑難排解功能，例如模組記錄上傳和支援套件組合上傳。

### <a name="network-configuration"></a>網路組態

針對最上層的每個閘道裝置，網路操作員必須：

* 提供靜態 IP 位址或完整功能變數名稱)  (FQDN。
* 透過埠 443 (HTTPS) 和 5671 (AMQP) ，將此 IP 位址的輸出通訊授權給您的 Azure IoT 中樞主機名稱。
* 將此 IP 位址的輸出通訊授權給 Azure Container Registry 主機名稱的埠 443 (HTTPS) 。

  API proxy 模組一次只能處理一個容器登錄的連接。 建議您將所有容器映射（包括 Microsoft Container Registry 所提供的公用映射） (mcr.microsoft.com) 儲存在私人容器登錄中。

針對較低層的每個閘道裝置，網路操作員必須：

* 提供靜態 IP 位址。
* 透過埠 443 (HTTPS) 和 5671 (AMQP) ，將此 IP 位址的輸出通訊授權給父閘道的 IP 位址。

### <a name="deploy-modules-to-top-layer-devices"></a>將模組部署到最上層裝置

閘道階層最上層的 IoT Edge 裝置，除了您可能在裝置上執行的任何工作負載模組之外，還有一組必須部署至其中的必要模組。

API proxy 模組的設計目的是要自訂以處理最常見的閘道案例。 本文提供的範例說明如何在基本設定中設定模組。 如需詳細資訊和範例，請參閱 [設定閘道階層案例的 API proxy 模組](how-to-configure-api-proxy-module.md) 。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞。
1. 從導覽功能表中選取 [ **IoT Edge** ]。
1. 從 **IoT Edge 裝置** 清單中選取您要設定的最上層裝置。
1. 選取 [設定模組]。
1. 在 [ **IoT Edge 模組** ] 區段中，選取 [ **新增** ]，然後選擇 [ **Marketplace 模組**]。
1. 搜尋並選取 **IOT EDGE API Proxy** 模組。
1. 從已部署的模組清單中選取 API proxy 模組的名稱，並更新下列模組設定：
   1. 在 [ **環境變數** ] 索引標籤中，將 **NGINX_DEFAULT_PORT** 的值更新為 `443` 。
   1. 在 [ **容器建立選項** ] 索引標籤中，將埠系結更新為參考埠443。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   這些變更會將 API proxy 模組設定為在埠443上接聽。 若要防止埠系結衝突，您必須將 edgeHub 模組設定為不接聽埠443。 相反地，API proxy 模組會路由埠443上的任何 edgeHub 流量。

1. 選取 [ **執行時間設定** ]，並尋找 [edgeHub] 模組建立選項。 刪除埠443的埠系結，並離開埠5671和8883的系結。

   ```json
   {
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
         ]
       }
     }
   }
   ```

1. 選取 [ **儲存** ] 以儲存對執行時間設定的變更。
1. 再次選取 [ **新增** ]，然後選擇 [ **IoT Edge 模組**]。
1. 提供下列值，以將 Docker 登錄模組新增至您的部署：
   1. **IoT Edge 模組名稱**： `registry`
   1. 在 [ **模組設定** ] 索引標籤上， **映射 URI**： `registry:latest`
   1. 在 [ **環境變數** ] 索引標籤上，新增下列環境變數：

      * **Name**： `REGISTRY_PROXY_REMOTEURL` **Value**：您希望這個登錄模組對應的容器登錄 URL。 例如： `https://myregistry.azurecr` 。

        登錄模組只能對應至一個容器登錄，因此建議您將所有容器映射都包含在單一私人容器登錄中。

      * **Name**： `REGISTRY_PROXY_USERNAME` **Value**：要向容器登錄進行驗證的使用者名稱。

      * **Name**： `REGISTRY_PROXY_PASSWORD` **Value**：驗證容器登錄的密碼。

   1. 在 [ **容器建立選項** ] 索引標籤上，貼上：

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

1. 選取 [ **新增** ]，將模組新增至部署。
1. 選取 **[下一步：路由** ] 以移至下一個步驟。
1. 若要啟用從下游裝置到 IoT 中樞的裝置到雲端訊息，請包含會將所有訊息傳遞至 IoT 中樞的路由。 例如：
    1. **名稱**：`Route`
    1. **值**：`FROM /messages/* INTO $upstream`
1. 選取 [ **審核 + 建立** ] 以移至最後一個步驟。
1. 選取 [ **建立** ] 以部署至您的裝置。

### <a name="deploy-modules-to-lower-layer-devices"></a>將模組部署到較低層的裝置

除了您可能在裝置上執行的任何工作負載模組之外，閘道階層較低層級的 IoT Edge 裝置也有一個必要模組必須部署至它們。

#### <a name="route-container-image-pulls"></a>路由容器映射提取

在討論閘道階層中 IoT Edge 裝置所需的 proxy 模組之前，請務必瞭解較低層的 IoT Edge 裝置如何取得其模組映射。

如果您的較低層裝置無法連線到雲端，但您想要讓它們照常提取模組映射，則必須設定閘道階層的最上層裝置來處理這些要求。 最上層裝置必須執行對應至容器登錄 **的 Docker 登錄** 模組。 然後，設定 API proxy 模組以將容器要求路由傳送給它。 本文的前面幾節將討論這些詳細資料。 在此設定中，較低層的裝置不應指向雲端容器登錄，而是指向在上一層中執行的登錄。

例如， `mcr.microsoft.com/azureiotedge-api-proxy:latest` 較低層的裝置應該呼叫，而不是呼叫 `$upstream:443/azureiotedge-api-proxy:latest` 。

**$Upstream** 參數指向較低層裝置的父系，因此要求會路由傳送所有層級，直到到達最上層，而此層級的 proxy 環境會將容器要求路由至登錄模組。 `:443`此範例中的埠應取代為父裝置上的 API proxy 模組正在接聽的任何埠。

API proxy 模組只能路由至一個登錄模組，而且每個登錄模組只能對應至一個容器登錄。 因此，任何較低層裝置需要提取的映射都必須儲存在單一容器登錄中。

如果您不想讓較低層的裝置透過閘道階層進行模組提取要求，另一個選項是管理本機登錄解決方案。 或者，在建立部署之前將模組映射推送至裝置，然後將 **imagePullPolicy** 設定為 [ **永不**]。

#### <a name="bootstrap-the-iot-edge-agent"></a>啟動 IoT Edge 代理程式

IoT Edge 代理程式是要在任何 IoT Edge 裝置上啟動的第一個執行時間元件。 您必須確定任何下游 IoT Edge 裝置在啟動時可以存取 edgeAgent 模組映射，然後它們可以存取部署並啟動其餘的模組映射。

當您進入 IoT Edge 裝置上的 yaml 檔案，以提供其驗證資訊、憑證和父系主機名稱時，也請更新 edgeAgent 容器映射。

如果最上層閘道裝置已設定為可處理容器映射要求，請 `mcr.microsoft.com` 以父系主機名稱和 API proxy 接聽埠取代。 在部署資訊清單中，您可以使用 `$upstream` 做為快捷方式，但需要 edgeHub 模組來處理路由，而且該模組目前尚未啟動。 例如：

```yml
agent:
  name: "edgeAgent"
  type: "docker"
  env: {}
  config:
    image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc2"
    auth: {}
```

如果您使用本機容器登錄，或在裝置上手動提供容器映射，請據以更新 yaml 檔案。

#### <a name="configure-runtime-and-deploy-proxy-module"></a>設定執行時間和部署 proxy 模組

需要 **API proxy 模組** ，才能路由雲端與任何下游 IoT Edge 裝置之間的所有通訊。 階層底層的 IoT Edge 裝置（沒有下游 IoT Edge 裝置）不需要此課程模組。

API proxy 模組的設計目的是要自訂以處理最常見的閘道案例。 本文簡要說明在基本設定中設定模組的步驟。 如需詳細資訊和範例，請參閱 [設定閘道階層案例的 API proxy 模組](how-to-configure-api-proxy-module.md) 。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞。
1. 從導覽功能表中選取 [ **IoT Edge** ]。
1. 從 **IoT Edge 裝置** 清單中，選取您要設定的較低層裝置。
1. 選取 [設定模組]。
1. 在 [ **IoT Edge 模組** ] 區段中，選取 [ **新增** ]，然後選擇 [ **Marketplace 模組**]。
1. 搜尋並選取 **IOT EDGE API Proxy** 模組。
1. 從已部署的模組清單中選取 API proxy 模組的名稱，並更新下列模組設定：
   1. 在 [ **模組設定** ] 索引標籤中，更新 **映射 URI** 的值。 將 `mcr.microsoft.com` 取代為 `$upstream:443`。
   1. 在 [ **環境變數** ] 索引標籤中，將 **NGINX_DEFAULT_PORT** 的值更新為 `443` 。
   1. 在 [ **容器建立選項** ] 索引標籤中，將埠系結更新為參考埠443。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   這些變更會將 API proxy 模組設定為在埠443上接聽。 若要防止埠系結衝突，您必須將 edgeHub 模組設定為不接聽埠443。 相反地，API proxy 模組會路由埠443上的任何 edgeHub 流量。

1. 選取 [ **執行時間設定**]。
1. 更新 edgeHub 模組設定：

   1. 在 [ **影像** ] 欄位中，將取代 `mcr.microsoft.com` 為 `$upstream:443` 。
   1. 在 [ **建立選項** ] 欄位中，刪除埠443的埠系結，並離開埠5671和8883的系結。

   ```json
   {
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
         ]
       }
     }
   }
   ```

1. 更新 edgeAgent 模組設定：
   1. 在 [ **影像** ] 欄位中，將取代 `mcr.microsoft.com` 為 `$upstream:443` 。

1. 選取 [ **儲存** ] 以儲存對執行時間設定的變更。
1. 選取 **[下一步：路由** ] 以移至下一個步驟。
1. 若要啟用從下游裝置到 IoT 中樞的裝置到雲端訊息，請包含將所有訊息傳遞至的路由 `$upstream` 。 如果是較低層的裝置，上游參數會指向父裝置。 例如：
    1. **名稱**：`Route`
    1. **值**：`FROM /messages/* INTO $upstream`
1. 選取 [ **審核 + 建立** ] 以移至最後一個步驟。
1. 選取 [ **建立** ] 以部署至您的裝置。

## <a name="next-steps"></a>下一步

[如何使用 IoT Edge 裝置作為閘道](iot-edge-as-gateway.md)

[為您的閘道階層案例設定 API proxy 模組](how-to-configure-api-proxy-module.md)