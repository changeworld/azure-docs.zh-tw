---
title: 準備在生產環境中部署您的解決方案-Azure IoT Edge
description: 瞭解如何將您的 Azure IoT Edge 解決方案從開發環境移至生產環境，包括使用適當的憑證設定您的裝置，以及針對未來的程式碼更新進行部署計畫。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 7cabae837656611813d44017ce2e1112f06066ef
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89669607"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>準備在生產環境中部署 IoT Edge 解決方案

當您準備將 IoT Edge 解決方案從開發層面轉移到生產環境時，請確保其設定可以提供持續性效能。

本文所提供的資訊並不相同。 為了協助您設定優先權，每個區段都會以清單開始，並將工作分成兩個部分：生產前需完成的**重要**事項，或是您需要知道的**實用**事項。

## <a name="device-configuration"></a>裝置設定

IoT Edge 裝置可以是任何項目，包括 Raspberry Pi、膝上型電腦或伺服器上執行的虛擬機器。 您可以實際或透過虛擬連線存取裝置，或者裝置可能長時間受到隔離。 無論何種方式，您都想要確定它已設定為適當地運作。

* **重要**
  * 安裝生產憑證
  * 研擬裝置管理計畫
  * 使用 Moby 作為容器引擎

* **實用**
  * 選擇上游通訊協定

### <a name="install-production-certificates"></a>安裝生產憑證

生產環境中的每個 IoT Edge 裝置都要安裝裝置憑證授權單位 (CA) 的憑證。 此 CA 憑證接著會在 config.yaml 檔案的 IoT Edge 執行階段中宣告。 針對開發和測試案例，如果 yaml 檔案中未宣告任何憑證，IoT Edge 執行時間就會建立暫時憑證。 不過，這些暫時憑證三個月後就會過期，而且對於生產情節並不安全。 針對生產案例，您應該從自我簽署的憑證授權單位單位提供您自己的裝置 CA 憑證，或從商業憑證授權單位單位購買。

> [!NOTE]
> 目前，libiothsm 中的限制會防止使用在2038年1月1日或之後過期的憑證。

若要了解裝置 CA 憑證的角色，請參閱 [Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

如需有關如何在 IoT Edge 裝置上安裝憑證，並從 yaml 檔案參考這些憑證的詳細資訊，請參閱 [管理 IoT Edge 裝置上的憑證](how-to-manage-device-certificates.md)。

### <a name="have-a-device-management-plan"></a>研擬裝置管理計畫

在生產環境中設置任何裝置前，應先了解如何管理未來的更新作業。 若是 IoT Edge 裝置，要更新的元件清單可能包含：

* 裝置韌體
* 作業系統程式庫
* 容器引擎，例如 Moby
* IoT Edge 精靈
* CA 憑證

如需詳細資訊，請參閱 [更新 IoT Edge 運行](how-to-update-iot-edge.md)時間。 目前更新 IoT Edge 精靈的方法需要實際存取或透過 SSH 存取 IoT Edge 裝置。 如果您有許多裝置要更新，請考慮將更新步驟新增至腳本，或使用 Ansible 之類的自動化工具。

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作為容器引擎

容器引擎是任何 IoT Edge 裝置的必要條件。 生產環境僅支援 Moby 引擎。 Docker 等其他容器引擎可搭配 IoT Edge 使用，也可以使用這些引擎進行開發。 搭配 Azure IoT Edge 使用時可重新分配 Moby 引擎，而且 Microsoft 可提供此引擎的相關服務。

### <a name="choose-upstream-protocol"></a>選擇上游通訊協定

您可以設定通訊協定 (，以決定 IoT Edge 代理程式和 IoT Edge 中樞對 IoT 中樞進行上游通訊) 使用的埠。 預設的通訊協定是 AMQP，但您可以根據網路設定變更。

這兩個執行階段模組都有 **UpstreamProtocol** 環境變數。 有效的變數值為：

* MQTT
* AMQP
* MQTTWS
* AMQPWS

在裝置本身的 yaml 檔案中，為 IoT Edge 代理程式設定 >upstreamprotocol 變數。 例如，如果您的 IoT Edge 裝置位於封鎖 AMQP 埠的 proxy 伺服器後方，您可能需要將 IoT Edge 代理程式設定為使用 AMQP over WebSocket (AMQPWS) 來建立 IoT 中樞的初始連線。

一旦您的 IoT Edge 裝置連線，請務必在未來的部署中繼續設定這兩個執行階段模組的 UpstreamProtocol 變數。 [設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

## <a name="deployment"></a>部署

* **實用**
  * 與上游的通訊協定一致
  * 為系統模組設定主機存放裝置
  * 減少 IoT Edge 中樞所使用的記憶體空間
  * 請勿使用模組映像的偵錯版本

### <a name="be-consistent-with-upstream-protocol"></a>與上游的通訊協定一致

如果您在 IoT Edge 裝置上將 IoT Edge 代理程式設定為使用與預設 AMQP 不同的通訊協定，則您應該在所有未來的部署中宣告相同的通訊協定。 舉例來說，如果您的 IoT Edge 裝置位於封鎖 AMQP 連接埠的 Proxy 伺服器後方，可能是設定該裝置透過 WebSocket (AMQPWS) 使用 AMQP 連線。 當您將模組部署到裝置時，請為 IoT Edge 代理程式和 IoT Edge 中樞設定相同的 AMQPWS 通訊協定，否則預設 AMQP 將會覆寫設定，並防止您再次連接。

您只需要為 IoT Edge 代理程式和 IoT Edge 中樞模組設定 >upstreamprotocol 環境變數。 任何其他模組會採用執行階段模組中設定的任何通訊協定。

[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

### <a name="set-up-host-storage-for-system-modules"></a>為系統模組設定主機存放裝置

IoT Edge 的中樞和代理程式模組會使用本機儲存體來維護狀態，並啟用模組、裝置和雲端之間的通訊。 為了獲得更好的可靠性和效能，請將系統模組設定為使用主機檔案系統上的儲存體。

如需詳細資訊，請參閱 [主機存放裝置的系統模組](how-to-access-host-storage-from-module.md)。

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>減少 IoT Edge 中樞所使用的記憶體空間

如果您要部署的受限制裝置的記憶體有限，您可以設定 IoT Edge hub 以更簡化的容量執行，並使用較少的磁碟空間。 不過，這些設定會限制 IoT Edge 中樞的效能，因此請找出適合您解決方案的平衡。

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>請勿在受限裝置上進行效能最佳化

根據預設，IoT Edge 中樞已針對效能優化，因此它會嘗試配置大型的記憶體區塊。 這項設定會在 Raspberry Pi 等小型裝置上造成穩定性問題。 如果您要部署具有受限資源的裝置，您可能會想要在 IoT Edge 中樞上將 **將 optimizeforperformance** 環境變數設定為 **false** 。

當 **將 optimizeforperformance** 設定為 **TRUE**時，MQTT 通訊協定標頭會使用 PooledByteBufferAllocator，但其效能較佳，但會配置更多記憶體。 配置器在32位作業系統或記憶體不足的裝置上無法正常運作。 此外，針對效能優化時，RocksDb 會為其角色配置更多的記憶體作為本機儲存提供者。

如需詳細資訊，請參閱 [較小裝置上的穩定性問題](troubleshoot-common-errors.md#stability-issues-on-smaller-devices)。

#### <a name="disable-unused-protocols"></a>停用未使用的通訊協定

將 IoT Edge 中樞的效能優化並減少其記憶體使用量的另一種方式，是關閉您未在解決方案中使用的任何通訊協定的通訊協定標頭。

通訊協定標頭的設定方式是在部署資訊清單中設定 IoT Edge 中樞模組的布林環境變數。 這三個變數分別是：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

這三個變數都有*兩條底線*，而且可以設為 true 或 false。

#### <a name="reduce-storage-time-for-messages"></a>減少訊息的儲存時間

如果因為任何原因而無法將訊息傳遞至 IoT 中樞，IoT Edge 中樞模組會暫時儲存訊息。 您可以設定 IoT Edge 中樞保留給未傳遞訊息的時間長度，然後才讓它們到期。 如果您的裝置有記憶體問題，您可以降低 IoT Edge 中樞模組對應項中的 **timeToLiveSecs** 值。

timeToLiveSecs 參數的預設值是 7200 秒，也就是兩小時。

### <a name="do-not-use-debug-versions-of-module-images"></a>請勿使用模組映像的偵錯版本

從測試情節轉移至生產環境情節時，請務必移除部署資訊清單中的偵錯設定。 檢查部署資訊清單中沒有任何模組映射具有** \. debug**尾碼。 如果您已新增建立選項，以公開模組中的連接埠進行偵錯時，也請移除這些建立選項。

## <a name="container-management"></a>容器管理

* **重要**
  * 管理您的容器登錄存取權
  * 使用標籤來管理版本
* **實用**
  * 在您的私人登錄中儲存執行時間容器

### <a name="manage-access-to-your-container-registry"></a>管理您的容器登錄存取權

將模組部署到生產 IoT Edge 裝置之前，請務必控管容器登錄的存取權，避免他人存取或變更您的容器映像。 您可以使用非公開的私人容器登錄來管理容器映像。

在教學課程和其他說明文件中，我們會指示您在 IoT Edge 裝置使用與開發電腦上所用相同的容器登錄認證。 這些指示僅協助您更輕鬆地設定測試和開發環境，在生產情節中不應遵照這些指示。

若要更安全地存取您的登錄，您可以選擇 [驗證選項](../container-registry/container-registry-authentication.md)。 常見且建議的驗證是使用適用于應用程式或服務的 Active Directory 服務主體，以自動化或其他自動 (無) 外設方式提取容器映射，如同 IoT Edge 裝置一樣。

若要建立服務主體，請執行兩個腳本，如「 [建立服務主體](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal)」中所述。 這些腳本會執行下列工作：

* 第一個腳本會建立服務主體。 它會輸出服務主體識別碼和服務主體密碼。 將這些值安全地儲存在您的記錄中。

* 第二個腳本會建立要授與服務主體的角色指派，然後視需要執行。 建議您為參數套用 **acrPull** 使用者角色 `role` 。 如需角色清單，請參閱 [Azure Container Registry 角色和許可權](../container-registry/container-registry-roles.md)。

若要使用服務主體進行驗證，請提供您從第一個腳本取得的服務主體識別碼和密碼。 在部署資訊清單中指定這些認證。

* 針對使用者名稱或用戶端識別碼，指定服務主體識別碼。

* 針對密碼或用戶端密碼，指定服務主體密碼。

> [!NOTE]
> 在執行增強型安全性驗證之後，請停用系統 **管理使用者** 設定，如此就無法再使用預設的使用者名稱/密碼存取。 在 Azure 入口網站的容器登錄中，從左窗格功能表的 [ **設定**] 底下，選取 [ **存取金鑰**]。

### <a name="use-tags-to-manage-versions"></a>使用標籤來管理版本

標記是 docker 概念，您可以用來區別 docker 容器的版本。 標籤是類似 **1.0** 的尾碼，位於容器存放庫的末端。 例如 **mcr.microsoft.com/azureiotedge-agent:1.0**。 標籤可以變動，而且可以隨時更改並指向另一個容器，因此當您更新模組映像時，您的團隊應同意要遵循的慣例。

標籤也可協助您強制更新 IoT Edge 裝置。 當您將更新版模組推送至容器登錄時，請以遞增方式處理標記。 接著，將新部署和遞增的標籤一併推送到您的裝置。 容器引擎會將遞增的標籤辨識為新版本，並將最新的模組版本向下提取至您的裝置。

如需標籤慣例的範例，請參閱[更新 IoT Edge 執行階段](how-to-update-iot-edge.md#understand-iot-edge-tags)，了解 IoT Edge 如何使用循環標籤 (rolling tag) 和特定標籤來追蹤版本。

### <a name="store-runtime-containers-in-your-private-registry"></a>在您的私人登錄中儲存執行時間容器

您瞭解如何將自訂程式碼模組的容器映射儲存在私用 Azure 登錄中，但您也可以使用它來儲存公用容器映射，例如 edgeAgent 和 edgHub 執行時間模組。 如果您有嚴格的防火牆限制，則可能需要這麼做，因為這些執行時間容器會儲存在 Microsoft Container Registry (MCR) 中。

使用 Docker pull 命令取得映射，以放置在您的私人登錄中。 請注意，您必須使用每個新版本的 IoT Edge 執行時間來更新映射。

| IoT Edge 執行時間容器 | Docker pull 命令 |
| --- | --- |
| [Azure IoT Edge 代理程式](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge 中樞](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

接下來，請務必更新 edgeAgent 和 edgeHub 系統模組的檔案 deployment.template.js中的映射參考。 `mcr.microsoft.com`以您的登錄名稱和伺服器取代這兩個模組。

* EdgeAgent

    `"image": "<registry name and server>/azureiotedge-agent:1.0",`

* EdgeHub

    `"image": "<registry name and server>/azureiotedge-hub:1.0",`

## <a name="networking"></a>網路

* **實用**
  * 檢閱輸出/輸入設定
  * 允許來自 IoT Edge 裝置的連接
  * 設定 Proxy 通訊

### <a name="review-outboundinbound-configuration"></a>檢閱輸出/輸入設定

Azure IoT 中樞和 IoT Edge 之間的通訊通道一律會設定為輸出。 在大部分的 IoT Edge 情節下，必要的連線只有三個。 容器引擎必須和容納模組映像的容器登錄連線。 IoT Edge 執行階段需要和 IoT 中樞連線才能擷取裝置組態資訊，以及傳送訊息和遙測。 如果您使用自動佈建，IoT Edge 精靈必須連線至裝置佈建服務。 如需詳細資訊，請參閱[防火牆和連接埠組態規則](troubleshoot.md#check-your-firewall-and-port-configuration-rules)。

### <a name="allow-connections-from-iot-edge-devices"></a>允許來自 IoT Edge 裝置的連接

如果您的網路設定要求您明確允許從 IoT Edge 裝置進行連線，請參閱下列 IoT Edge 元件清單：

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
   | \*.docker.io  | 443 | Docker Hub 存取 (選擇性)  |

其中某些防火牆規則會繼承自 Azure Container Registry。 如需詳細資訊，請參閱 [設定規則以存取防火牆後方的 Azure container registry](../container-registry/container-registry-firewall-access-rules.md)。

> [!NOTE]
> 為了在 REST 和資料端點之間提供一致的 FQDN，從 **2020 年6月15日** 起，Microsoft Container Registry 資料端點會從變更 `*.cdn.mscr.io` 為 `*.data.mcr.microsoft.com`  
> 如需詳細資訊，請參閱[Microsoft Container Registry 用戶端防火牆規則](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)設定

如果您不想要將防火牆設定為允許存取公用容器登錄，您可以將映射儲存在私人容器登錄中，如 [您私人登錄中的存放執行時間容器中](#store-runtime-containers-in-your-private-registry)所述。

### <a name="configure-communication-through-a-proxy"></a>設定 Proxy 通訊

如果要在使用 Proxy 伺服器的網路上部署您的裝置，裝置必須能夠透過 Proxy 通訊，以便觸達 IoT 中樞和容器登錄。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解決方案管理

* **實用**
  * 設定記錄與診斷
  * 請考量測試和 CI/CD 管線

### <a name="set-up-logs-and-diagnostics"></a>設定記錄與診斷

在 Linux 上，IoT Edge daemon 會使用日誌作為預設記錄驅動程式。 您可以使用命令列工具 `journalctl` 查詢精靈記錄。 在 Windows 中，IoT Edge 精靈會使用 PowerShell 診斷。 使用 `Get-IoTEdgeLog` 查詢精靈記錄。 IoT Edge 模組會使用 JSON 驅動程式進行記錄，這是預設值。  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

正在測試 IoT Edge 部署時，通常可以存取您的裝置來擷取記錄並進行疑難排解。 在部署情節中，可能不提供該選項。 請考慮要如何收集生產環境中的裝置相關資訊。 其中一個選項是使用能夠收集其他模組資訊，並將資訊傳送至雲端的記錄模組。 其中一個記錄模組範例是 [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics)，您也可以設計自己的專用模組。

### <a name="place-limits-on-log-size"></a>限制記錄大小

根據預設，Moby 容器引擎不會設定容器記錄大小的限制。 經過一段時間後，可能會導致裝置填滿記錄並用盡磁碟空間。 請考慮下列選項以避免此情況：

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>選項：設定適用于所有容器模組的全域限制

您可以限制容器引擎記錄檔選項中所有容器記錄檔的大小。 下列範例會將記錄驅動程式設定為 `json-file` (建議的) ，其中包含大小和檔案數目的限制：

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

將此資訊新增 (或附加) 至名為的檔案 `daemon.json` ，並將它放在裝置平臺的正確位置。

| 平台 | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

容器引擎必須重新開機，變更才會生效。

#### <a name="option-adjust-log-settings-for-each-container-module"></a>選項：調整每個容器模組的記錄設定

您可以在每個模組的 **createOptions** 中進行這項作業。 例如：

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

* 設定容器引擎，藉由將記錄檔 `systemd` [journal](https://docs.docker.com/config/containers/logging/journald/)設定 `journald` 為預設記錄驅動程式來將記錄傳送至日誌。

* 藉由安裝 logrotate 工具，從您的裝置定期移除舊的記錄檔。 使用下列檔案規格：

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

若要提高 IoT Edge 部署情節的效率，請考量將您的生產環境部署整合至您的測試和 CI/CD 管線。 Azure IoT Edge 支援 Azure DevOps 等多個 CI/CD 平台。 如需詳細資訊，請參閱 [Azure IoT Edge 的持續整合與持續部署](how-to-continuous-integration-continuous-deployment.md)。

## <a name="next-steps"></a>接下來的步驟

* 深入了解 [IoT Edge 自動部署](module-deployment-monitoring.md)。
* 了解 IoT Edge 如何支援[持續整合與持續部署](how-to-continuous-integration-continuous-deployment.md)。
