---
title: 準備在生產環境中部署您的解決方案-Azure IoT Edge
description: 瞭解如何將您的 Azure IoT Edge 解決方案從開發到生產環境，包括以適當的憑證設定您的裝置，以及針對未來的程式碼更新進行部署計畫。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 4/25/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 173e663b66eeca676e8120dd46e8eca8b0126a17
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204197"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>準備在生產環境中部署 IoT Edge 解決方案

當您準備將 IoT Edge 解決方案從開發層面轉移到生產環境時，請確保其設定可以提供持續性效能。

本文所提供的資訊並不完全相同。 為了協助您設定優先權，每個區段都會以清單開始，並將工作分成兩個部分：生產前需完成的**重要**事項，或是您需要知道的**實用**事項。

## <a name="device-configuration"></a>裝置設定

IoT Edge 裝置可以是任何項目，包括 Raspberry Pi、膝上型電腦或伺服器上執行的虛擬機器。 您可以實際或透過虛擬連線存取裝置，或者裝置可能長時間受到隔離。 無論如何，您都想要確定它已設定為適當運作。

* **重要**
  * 安裝生產憑證
  * 研擬裝置管理計畫
  * 使用 Moby 作為容器引擎

* **實用**
  * 選擇上游通訊協定

### <a name="install-production-certificates"></a>安裝生產憑證

生產環境中的每個 IoT Edge 裝置都要安裝裝置憑證授權單位 (CA) 的憑證。 此 CA 憑證接著會在 config.yaml 檔案的 IoT Edge 執行階段中宣告。 在開發和測試案例中，如果 yaml 檔案中未宣告任何憑證，則 IoT Edge 執行時間會建立暫時憑證。 不過，這些暫時憑證三個月後就會過期，而且對於生產情節並不安全。

若要了解裝置 CA 憑證的角色，請參閱 [Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

如需如何在 IoT Edge 裝置上安裝憑證，並從 yaml 檔案參考它們的詳細資訊，請參閱[在 IoT Edge 裝置上安裝生產憑證](how-to-manage-device-certificates.md)。

### <a name="have-a-device-management-plan"></a>研擬裝置管理計畫

在生產環境中設置任何裝置前，應先了解如何管理未來的更新作業。 若是 IoT Edge 裝置，要更新的元件清單可能包含：

* 裝置軔體
* 作業系統程式庫
* 容器引擎，例如 Moby
* IoT Edge 精靈
* CA 憑證

如需詳細資訊，請參閱[更新 IoT Edge 運行](how-to-update-iot-edge.md)時間。 目前更新 IoT Edge 精靈的方法需要實際存取或透過 SSH 存取 IoT Edge 裝置。 如果您有許多要更新的裝置，請考慮將更新步驟新增至腳本，或使用自動化工具，例如 Ansible。

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作為容器引擎

容器引擎是任何 IoT Edge 裝置的必要條件。 生產環境僅支援 Moby 引擎。 Docker 等其他容器引擎可搭配 IoT Edge 使用，也可以使用這些引擎進行開發。 搭配 Azure IoT Edge 使用時可重新分配 Moby 引擎，而且 Microsoft 可提供此引擎的相關服務。

### <a name="choose-upstream-protocol"></a>選擇上游通訊協定

您可以設定通訊協定（決定所使用的埠），以便進行上游通訊，以針對 IoT Edge 代理程式和 IoT Edge 中樞 IoT 中樞。 預設的通訊協定是 AMQP，但您可以根據網路設定變更。

這兩個執行階段模組都有 **UpstreamProtocol** 環境變數。 有效的變數值為：

* MQTT
* AMQP
* MQTTWS
* AMQPWS

在裝置本身的 yaml 檔案中設定 IoT Edge 代理程式的 UpstreamProtocol 變數。 例如，如果您的 IoT Edge 裝置位於封鎖 AMQP 埠的 proxy 伺服器後方，您可能需要將 IoT Edge 代理程式設定為使用 AMQP over WebSocket （AMQPWS），以建立 IoT 中樞的初始連接。

一旦您的 IoT Edge 裝置連線，請務必在未來的部署中繼續設定這兩個執行階段模組的 UpstreamProtocol 變數。 [設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

## <a name="deployment"></a>部署

* **實用**
  * 與上游的通訊協定一致
  * 設定系統模組的主機存放裝置
  * 減少 IoT Edge 中樞所使用的記憶體空間
  * 請勿使用模組映像的偵錯版本

### <a name="be-consistent-with-upstream-protocol"></a>與上游的通訊協定一致

如果您將 IoT Edge 裝置上的 IoT Edge 代理程式設定為使用與預設 AMQP 不同的通訊協定，您應該在所有未來的部署中宣告相同的通訊協定。 舉例來說，如果您的 IoT Edge 裝置位於封鎖 AMQP 連接埠的 Proxy 伺服器後方，可能是設定該裝置透過 WebSocket (AMQPWS) 使用 AMQP 連線。 當您將模組部署至裝置時，請為 IoT Edge 代理程式和 IoT Edge 中樞設定相同的 AMQPWS 通訊協定，否則預設 AMQP 會覆寫設定，並防止您再次連線。

您只需要為 IoT Edge 代理程式和 IoT Edge 中樞模組設定 UpstreamProtocol 環境變數。 任何其他模組會採用執行階段模組中設定的任何通訊協定。

[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

### <a name="set-up-host-storage-for-system-modules"></a>設定系統模組的主機存放裝置

IoT Edge 的中樞和代理程式模組會使用本機儲存體來維護狀態，並啟用模組、裝置和雲端之間的訊息傳遞。 為獲得更好的可靠性和效能，請將系統模組設定為使用主機檔案系統上的存放裝置。

如需詳細資訊，請參閱[系統模組的主機存放裝置](how-to-access-host-storage-from-module.md)。

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>減少 IoT Edge 中樞所使用的記憶體空間

如果您要部署具有可用記憶體有限的受限裝置，您可以將 IoT Edge hub 設定為以更簡化的容量執行，並使用較少的磁碟空間。 不過，這些設定會限制 IoT Edge 中樞的效能，因此請尋找適用于您解決方案的正確平衡。

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>請勿在受限裝置上進行效能最佳化

根據預設，IoT Edge 中樞已針對效能優化，因此它會嘗試配置大量的記憶體區塊。 這項設定會在 Raspberry Pi 等小型裝置上造成穩定性問題。 如果您要部署具有受限制資源的裝置，您可能會想要在 IoT Edge 中樞上將**OptimizeForPerformance**環境變數設定為**false** 。

當**OptimizeForPerformance**設定為**TRUE**時，MQTT 通訊協定標頭會使用 PooledByteBufferAllocator，其具有較佳的效能，但會配置更多記憶體。 配置器無法在32位作業系統或記憶體不足的裝置上順利運作。 此外，針對效能進行優化時，RocksDb 會為其角色配置更多記憶體以作為本機儲存提供者。

如需詳細資訊，請參閱[資源受限裝置的穩定性問題](troubleshoot.md#stability-issues-on-resource-constrained-devices)。

#### <a name="disable-unused-protocols"></a>停用未使用的通訊協定

另一種優化 IoT Edge 中樞效能並降低其記憶體使用量的方法，就是關閉您的解決方案中未使用之任何通訊協定的通訊協定標頭。

通訊協定標頭是藉由在部署資訊清單中設定 IoT Edge 中樞模組的布林環境變數來設定。 這三個變數分別是：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

這三個變數都有*兩條底線*，而且可以設為 true 或 false。

#### <a name="reduce-storage-time-for-messages"></a>減少訊息的儲存時間

IoT Edge 中樞模組會暫時儲存訊息（如果因為任何原因而無法傳遞到 IoT 中樞）。 您可以設定 IoT Edge 中樞在未傳遞的訊息過期之前，保留的時間長度。 如果您的裝置上有記憶體疑慮，您可以降低 IoT Edge 中樞模組對應項中的**timeToLiveSecs**值。

timeToLiveSecs 參數的預設值是 7200 秒，也就是兩小時。

### <a name="do-not-use-debug-versions-of-module-images"></a>請勿使用模組映像的偵錯版本

從測試情節轉移至生產環境情節時，請務必移除部署資訊清單中的偵錯設定。 檢查部署資訊清單中沒有任何模組映射具有** \.debug**尾碼。 如果您已新增建立選項，以公開模組中的連接埠進行偵錯時，也請移除這些建立選項。

## <a name="container-management"></a>容器管理

* **重要**
  * 管理您的容器登錄存取權
  * 使用標籤來管理版本
* **實用**
  * 在您的私用登錄中儲存執行時間容器

### <a name="manage-access-to-your-container-registry"></a>管理您的容器登錄存取權

將模組部署到生產 IoT Edge 裝置之前，請務必控管容器登錄的存取權，避免他人存取或變更您的容器映像。 您可以使用非公開的私人容器登錄來管理容器映像。

在教學課程和其他說明文件中，我們會指示您在 IoT Edge 裝置使用與開發電腦上所用相同的容器登錄認證。 這些指示僅協助您更輕鬆地設定測試和開發環境，在生產情節中不應遵照這些指示。

為了更安全地存取您的登錄，您可以選擇[驗證選項](../container-registry/container-registry-authentication.md)。 常見且建議的驗證是使用適用于應用程式或服務的 Active Directory 服務主體，以自動化或其他自動（無周邊）的方式來提取容器映射，如同 IoT Edge 的裝置一樣。

若要建立服務主體，請執行這兩個腳本，如[建立服務主體](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal)中所述。 這些腳本會執行下列工作：

* 第一個腳本會建立服務主體。 它會輸出服務主體識別碼和服務主體密碼。 將這些值安全地儲存在記錄中。

* 第二個腳本會建立要授與服務主體的角色指派，以便後續視需要執行。 我們建議您為**acrPull** `role`參數套用 acrPull 使用者角色。 如需角色清單，請參閱[Azure Container Registry 角色和許可權](../container-registry/container-registry-roles.md)。

若要使用服務主體進行驗證，請提供您從第一個腳本取得的服務主體識別碼和密碼。 在部署資訊清單中指定這些認證。

* 針對 [使用者名稱] 或 [用戶端識別碼]，指定服務主體識別碼。

* 針對 [密碼] 或 [用戶端秘密]，指定服務主體密碼。

> [!NOTE]
> 在執行增強的安全性驗證之後，請停用系統**管理使用者**設定，讓預設的使用者名稱/密碼存取無法再使用。 在 Azure 入口網站的容器登錄中，從左側窗格功能表的 [**設定**] 底下，選取 [**存取金鑰**]。

### <a name="use-tags-to-manage-versions"></a>使用標籤來管理版本

標記是一個 docker 概念，可用來區分 docker 容器的版本。 標籤是類似 **1.0** 的尾碼，位於容器存放庫的末端。 例如 **mcr.microsoft.com/azureiotedge-agent:1.0**。 標籤可以變動，而且可以隨時更改並指向另一個容器，因此當您更新模組映像時，您的團隊應同意要遵循的慣例。

標籤也可協助您強制更新 IoT Edge 裝置。 當您將更新版模組推送至容器登錄時，請以遞增方式處理標記。 接著，將新部署和遞增的標籤一併推送到您的裝置。 容器引擎會將遞增的標籤辨識為新版本，並將最新的模組版本向下提取至您的裝置。

如需標籤慣例的範例，請參閱[更新 IoT Edge 執行階段](how-to-update-iot-edge.md#understand-iot-edge-tags)，了解 IoT Edge 如何使用循環標籤 (rolling tag) 和特定標籤來追蹤版本。

### <a name="store-runtime-containers-in-your-private-registry"></a>在您的私用登錄中儲存執行時間容器

您知道如何將自訂程式碼模組的容器映射儲存在私人 Azure 登錄中，但您也可以使用它來儲存公用容器映射，例如適用于 edgeAgent 和 edgHub 執行時間模組。 如果您因為這些執行時間容器儲存在 Microsoft Container Registry （MCR）中，而有非常嚴格的防火牆限制，可能就需要這麼做。

取得具有 Docker pull 命令的映射，以放入您的私用登錄中。 請注意，您必須使用 IoT Edge 執行時間的每個新版本來更新映射。

| IoT Edge 執行時間容器 | Docker pull 命令 |
| --- | --- |
| [Azure IoT Edge 代理程式](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge 中樞](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

接下來，請務必更新 edgeAgent 和 edgeHub 系統模組的 deployment. template. json 檔案中的映射參考。 以`mcr.microsoft.com`您的登錄名稱取代這兩個模組的伺服器。

* EdgeAgent

    `"image": "<registry name and server>/azureiotedge-agent:1.0",`

* EdgeHub

    `"image": "<registry name and server>/azureiotedge-hub:1.0",`

## <a name="networking"></a>網路功能

* **實用**
  * 檢閱輸出/輸入設定
  * 允許來自 IoT Edge 裝置的連線
  * 設定 Proxy 通訊

### <a name="review-outboundinbound-configuration"></a>檢閱輸出/輸入設定

Azure IoT 中樞和 IoT Edge 之間的通訊通道一律會設定為輸出。 在大部分的 IoT Edge 情節下，必要的連線只有三個。 容器引擎必須和容納模組映像的容器登錄連線。 IoT Edge 執行階段需要和 IoT 中樞連線才能擷取裝置組態資訊，以及傳送訊息和遙測。 如果您使用自動佈建，IoT Edge 精靈必須連線至裝置佈建服務。 如需詳細資訊，請參閱[防火牆和連接埠組態規則](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)。

### <a name="allow-connections-from-iot-edge-devices"></a>允許來自 IoT Edge 裝置的連線

如果您的網路設定要求您明確地允許從 IoT Edge 裝置進行的連線，請參閱下列 IoT Edge 元件清單：

* **IoT Edge 代理程式**可能會透過 WebSockets 持續以 AMQP/MQTT 連線方式連線到 IoT 中樞。
* **IoT Edge 中樞**可能會透過 WebSockets 持續以 AMQP 連線或多種 MQTT 連線方式連線到 IoT 中樞。
* **IoT Edge 精靈**對 IoT 中樞進行 HTTPS 間歇性呼叫。

在這三個案例中，DNS 名稱會符合模式 \*.azure-devices.net。

此外，**容器引擎**會透過 HTTPS 呼叫容器登錄。 若要擷取 IoT Edge 執行階段容器映像，DNS 名稱為 mcr.microsoft.com。 容器引擎會依部署中的設定，連線到其他登錄。

此檢查清單是防火牆規則的起始點：

   | URL (\* = 萬用字元) | 輸出 TCP 連接埠 | 使用方式 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft 容器登錄 |
   | global.azure-devices-provisioning.net  | 443 | DPS 存取 (選用) |
   | \*.azurecr.io | 443 | 個人和協力廠商容器登錄 |
   | \*.blob.core.windows.net | 443 | 從 blob 儲存體下載 Azure Container Registry 映射差異 |
   | \*.azure-devices.net | 5671、8883、443 | IoT 中樞存取 |
   | \*.docker.io  | 443 | Docker Hub 存取（選擇性） |

其中一些防火牆規則會繼承自 Azure Container Registry。 如需詳細資訊，請參閱[設定規則以存取防火牆後方的 Azure container registry](../container-registry/container-registry-firewall-access-rules.md)。

如果您不想要將防火牆設定為允許存取公用容器登錄，您可以將映射儲存在私人容器登錄中，如在[私人登錄中儲存執行時間容器](#store-runtime-containers-in-your-private-registry)中所述。

### <a name="configure-communication-through-a-proxy"></a>設定 Proxy 通訊

如果要在使用 Proxy 伺服器的網路上部署您的裝置，裝置必須能夠透過 Proxy 通訊，以便觸達 IoT 中樞和容器登錄。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解決方案管理

* **實用**
  * 設定記錄與診斷
  * 請考量測試和 CI/CD 管線

### <a name="set-up-logs-and-diagnostics"></a>設定記錄與診斷

在 Linux 上，IoT Edge daemon 會使用日誌做為預設記錄驅動程式。 您可以使用命令列工具 `journalctl` 查詢精靈記錄。 在 Windows 中，IoT Edge 精靈會使用 PowerShell 診斷。 使用 `Get-IoTEdgeLog` 查詢精靈記錄。 IoT Edge 模組會使用 JSON 驅動程式來進行記錄，這是預設值。  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

正在測試 IoT Edge 部署時，通常可以存取您的裝置來擷取記錄並進行疑難排解。 在部署情節中，可能不提供該選項。 請考慮要如何收集生產環境中的裝置相關資訊。 其中一個選項是使用能夠收集其他模組資訊，並將資訊傳送至雲端的記錄模組。 其中一個記錄模組範例是 [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics)，您也可以設計自己的專用模組。

### <a name="place-limits-on-log-size"></a>記錄大小的限制

根據預設，Moby 容器引擎不會設定容器記錄大小限制。 經過一段時間，這可能會導致裝置填滿記錄檔並耗盡磁碟空間。 請考慮下列選項來避免這種情況：

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>選項：設定適用于所有容器模組的全域限制

您可以限制容器引擎記錄檔選項中所有容器日誌的大小。 下列範例會將記錄驅動程式設定`json-file`為（建議），並限制檔案大小和數目：

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

將此資訊新增（或附加）至名`daemon.json`為的檔案，並將它放在您裝置平臺的正確位置。

| 平台 | 位置 |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

容器引擎必須重新開機，變更才會生效。

#### <a name="option-adjust-log-settings-for-each-container-module"></a>選項：調整每個容器模組的記錄檔設定

您可以在每個模組的**createOptions**中執行此動作。 例如：

```yml
"createOptions": {
    "HostConfig": {
        "LogConfig": {
            "Type": "json-file",
            "Config": {
                "max-size": "10m",
                "max-file": "3"
            }
        }
    }
}
```

#### <a name="additional-options-on-linux-systems"></a>Linux 系統上的其他選項

* 將設定為預設記錄驅動程式， `systemd`將容器引擎`journald`設定為將記錄傳送到[日誌](https://docs.docker.com/config/containers/logging/journald/)。

* 藉由安裝 logrotate 工具，定期從您的裝置移除舊的記錄檔。 使用下列檔案規格：

   ```txt
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>請考量測試和 CI/CD 管線

若要提高 IoT Edge 部署情節的效率，請考量將您的生產環境部署整合至您的測試和 CI/CD 管線。 Azure IoT Edge 支援 Azure DevOps 等多個 CI/CD 平台。 如需詳細資訊，請參閱 [Azure IoT Edge 的持續整合與持續部署](how-to-ci-cd.md)。

## <a name="next-steps"></a>後續步驟

* 深入了解 [IoT Edge 自動部署](module-deployment-monitoring.md)。
* 了解 IoT Edge 如何支援[持續整合與持續部署](how-to-ci-cd.md)。
