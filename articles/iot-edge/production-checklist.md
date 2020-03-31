---
title: 準備在生產中部署解決方案 - Azure IoT 邊緣
description: 瞭解如何將 Azure IoT Edge 解決方案從開發轉移到生產，包括使用適當的證書設置設備，以及為未來的代碼更新制定部署計畫。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5320c9d7f1ea5ae882c67ee631f5bbafbf97b039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530864"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>準備在生產環境中部署 IoT Edge 解決方案

當您準備將 IoT Edge 解決方案從開發層面轉移到生產環境時，請確保其設定可以提供持續性效能。

本文提供的資訊並不完全相等。 為了協助您設定優先權，每個區段都會以清單開始，並將工作分成兩個部分：生產前需完成的**重要**事項，或是您需要知道的**實用**事項。

## <a name="device-configuration"></a>裝置設定

IoT Edge 裝置可以是任何項目，包括 Raspberry Pi、膝上型電腦或伺服器上執行的虛擬機器。 您可以實際或透過虛擬連線存取裝置，或者裝置可能長時間受到隔離。 無論採用哪種方式，您都希望確保將其配置為正常工作。

* **重要**
  * 安裝生產憑證
  * 研擬裝置管理計畫
  * 使用 Moby 作為容器引擎

* **實用**
  * 選擇上游通訊協定

### <a name="install-production-certificates"></a>安裝生產憑證

生產環境中的每個 IoT Edge 裝置都要安裝裝置憑證授權單位 (CA) 的憑證。 此 CA 憑證接著會在 config.yaml 檔案的 IoT Edge 執行階段中宣告。 對於開發和測試方案，如果 config.yaml 檔中未聲明證書，IoT Edge 運行時將創建臨時證書。 不過，這些暫時憑證三個月後就會過期，而且對於生產情節並不安全。

若要了解裝置 CA 憑證的角色，請參閱 [Azure IoT Edge 如何使用憑證](iot-edge-certs.md)。

有關如何在 IoT Edge 設備上安裝證書並從 config.yaml 檔中引用證書的詳細資訊，請參閱在[IoT Edge 設備上安裝生產證書](how-to-manage-device-certificates.md)。

### <a name="have-a-device-management-plan"></a>研擬裝置管理計畫

在生產環境中設置任何裝置前，應先了解如何管理未來的更新作業。 若是 IoT Edge 裝置，要更新的元件清單可能包含：

* 裝置軔體
* 作業系統程式庫
* 容器引擎，例如 Moby
* IoT Edge 精靈
* CA 憑證

有關詳細資訊，請參閱更新[IoT 邊緣運行時](how-to-update-iot-edge.md)。 目前更新 IoT Edge 精靈的方法需要實際存取或透過 SSH 存取 IoT Edge 裝置。 如果有許多設備要更新，請考慮將更新步驟添加到腳本或使用像 Ansible 這樣的自動化工具。

### <a name="use-moby-as-the-container-engine"></a>使用 Moby 作為容器引擎

容器引擎是任何 IoT 邊緣設備的先決條件。 生產環境僅支援 Moby 引擎。 Docker 等其他容器引擎可搭配 IoT Edge 使用，也可以使用這些引擎進行開發。 搭配 Azure IoT Edge 使用時可重新分配 Moby 引擎，而且 Microsoft 可提供此引擎的相關服務。

### <a name="choose-upstream-protocol"></a>選擇上游通訊協定

您可以為 IoT Edge 代理和 IoT Edge 中心配置與 IoT 中心進行上游通信的協定（確定使用的埠）。 預設的通訊協定是 AMQP，但您可以根據網路設定變更。

這兩個執行階段模組都有 **UpstreamProtocol** 環境變數。 有效的變數值為：

* MQTT
* AMQP
* MQTTWS
* AMQPWS

在設備本身的 config.yaml 檔中為 IoT 邊緣代理配置的上游協定變數。 例如，如果您的 IoT Edge 設備位於阻止 AMQP 埠的代理伺服器後面，則可能需要將 IoT Edge 代理配置為通過 WebSocket （AMQPWS） 使用 AMQP 來建立與 IoT 中心的初始連接。

一旦您的 IoT Edge 裝置連線，請務必在未來的部署中繼續設定這兩個執行階段模組的 UpstreamProtocol 變數。 [設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

## <a name="deployment"></a>部署

* **實用**
  * 與上游的通訊協定一致
  * 為系統模組設置主機存儲
  * 減少 IoT 邊緣中心使用的記憶體空間
  * 請勿使用模組映像的偵錯版本

### <a name="be-consistent-with-upstream-protocol"></a>與上游的通訊協定一致

如果在 IoT Edge 設備上配置 IoT Edge 代理使用與預設 AMQP 不同的協定，則應在以後的所有部署中聲明相同的協定。 舉例來說，如果您的 IoT Edge 裝置位於封鎖 AMQP 連接埠的 Proxy 伺服器後方，可能是設定該裝置透過 WebSocket (AMQPWS) 使用 AMQP 連線。 將模組部署到設備時，為 IoT 邊緣代理和 IoT Edge 集線器配置相同的 AMQPWS 協定，否則預設 AMQP 將覆蓋設置並阻止您再次連接。

您只需要為 IoT 邊緣代理和 IoT Edge 中心模組配置上游協定環境變數。 任何其他模組會採用執行階段模組中設定的任何通訊協定。

[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)中提供此程序的範例。

### <a name="set-up-host-storage-for-system-modules"></a>為系統模組設置主機存儲

IoT Edge 集線器和代理模組使用本機存放區來維護狀態，並在模組、設備和雲之間啟用消息傳遞。 為了更好的可靠性和性能，請將系統模組配置為在主機檔案系統上使用存儲。

有關詳細資訊，請參閱[系統模組的主機存儲](how-to-access-host-storage-from-module.md)。

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>減少 IoT 邊緣集線器使用的記憶體空間

如果要部署可用記憶體有限的受約束設備，則可以將 IoT Edge 集線器配置為以更精簡的容量運行，並使用更少的磁碟空間。 但是，這些配置確實限制了 IoT Edge 中心的性能，因此請找到適合您的解決方案的正確平衡。

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>請勿在受限裝置上進行效能最佳化

預設情況下，IoT Edge 中心針對性能進行了優化，因此它嘗試分配大塊記憶體。 這項設定會在 Raspberry Pi 等小型裝置上造成穩定性問題。 如果要部署資源受限的設備，則可能需要在 IoT Edge 中心將 **"優化性能"** 環境變數設置為**false。**

當 **"優化性能**"設置為**true**時，MQTT 協定頭使用池位元組緩衝器，該分配器具有更好的性能，但分配了更多記憶體。 分配器在 32 位作業系統或記憶體不足的設備上工作正常。 此外，當針對性能進行優化時，RocksDb 會為其作為本機存放區提供程式的角色指派更多記憶體。

如需詳細資訊，請參閱[資源受限裝置的穩定性問題](troubleshoot.md#stability-issues-on-resource-constrained-devices)。

#### <a name="disable-unused-protocols"></a>停用未使用的通訊協定

優化 IoT Edge 集線器性能並減少其記憶體使用量的另一種方法是關閉解決方案中未使用的任何協定的協定頭。

協定負責人是通過在部署清單中為 IoT Edge 中心模組設置布林環境變數來配置的。 這三個變數分別是：

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

這三個變數都有*兩條底線*，而且可以設為 true 或 false。

#### <a name="reduce-storage-time-for-messages"></a>減少訊息的儲存時間

如果由於任何原因無法將消息傳遞到 IoT 中心，IoT Edge 中心模組將臨時存儲消息。 您可以配置 IoT Edge 中心在未傳遞消息過期之前保留多長時間。 如果設備上有記憶體問題，則可以降低 IoT Edge 中心模組孿生中**的時間到LiveSecs**值。

timeToLiveSecs 參數的預設值是 7200 秒，也就是兩小時。

### <a name="do-not-use-debug-versions-of-module-images"></a>請勿使用模組映像的偵錯版本

從測試情節轉移至生產環境情節時，請務必移除部署資訊清單中的偵錯設定。 檢查部署清單中的模組映射中沒有任何**\.調試**尾碼。 如果您已新增建立選項，以公開模組中的連接埠進行偵錯時，也請移除這些建立選項。

## <a name="container-management"></a>容器管理

* **重要**
  * 管理您的容器登錄存取權
  * 使用標籤來管理版本

### <a name="manage-access-to-your-container-registry"></a>管理您的容器登錄存取權

將模組部署到生產 IoT Edge 裝置之前，請務必控管容器登錄的存取權，避免他人存取或變更您的容器映像。 您可以使用非公開的私人容器登錄來管理容器映像。

在教學課程和其他說明文件中，我們會指示您在 IoT Edge 裝置使用與開發電腦上所用相同的容器登錄認證。 這些指示僅協助您更輕鬆地設定測試和開發環境，在生產情節中不應遵照這些指示。 當應用程式或服務以自動化或其他自動方式提取容器映像時，Azure Container Registry 建議[使用服務主體進行驗證](../container-registry/container-registry-auth-service-principal.md)，如同 IoT Edge 裝置一般。 為容器登錄建立具備唯讀存取權的服務主體，並且在部署資訊清單中提供該使用者名稱和密碼。

### <a name="use-tags-to-manage-versions"></a>使用標籤來管理版本

標記是一個 docker 概念，可用於區分 docker 容器的版本。 標籤是類似 **1.0** 的尾碼，位於容器存放庫的末端。 例如 **mcr.microsoft.com/azureiotedge-agent:1.0**。 標籤可以變動，而且可以隨時更改並指向另一個容器，因此當您更新模組映像時，您的團隊應同意要遵循的慣例。

標籤也可協助您強制更新 IoT Edge 裝置。 當您將更新版模組推送至容器登錄時，請以遞增方式處理標記。 接著，將新部署和遞增的標籤一併推送到您的裝置。 容器引擎會將遞增的標籤辨識為新版本，並將最新的模組版本向下提取至您的裝置。

如需標籤慣例的範例，請參閱[更新 IoT Edge 執行階段](how-to-update-iot-edge.md#understand-iot-edge-tags)，了解 IoT Edge 如何使用循環標籤 (rolling tag) 和特定標籤來追蹤版本。

## <a name="networking"></a>網路

* **實用**
  * 檢閱輸出/輸入設定
  * 允許來自 IoT 邊緣設備的連接
  * 設定 Proxy 通訊

### <a name="review-outboundinbound-configuration"></a>檢閱輸出/輸入設定

Azure IoT 中樞和 IoT Edge 之間的通訊通道一律會設定為輸出。 在大部分的 IoT Edge 情節下，必要的連線只有三個。 容器引擎必須和容納模組映像的容器登錄連線。 IoT Edge 執行階段需要和 IoT 中樞連線才能擷取裝置組態資訊，以及傳送訊息和遙測。 如果您使用自動佈建，IoT Edge 精靈必須連線至裝置佈建服務。 如需詳細資訊，請參閱[防火牆和連接埠組態規則](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)。

### <a name="allow-connections-from-iot-edge-devices"></a>允許來自 IoT 邊緣設備的連接

如果您的網路設置要求您顯式允許從 IoT Edge 設備進行連接，請查看以下 IoT Edge 元件清單：

* **IoT Edge 代理程式**可能會透過 WebSockets 持續以 AMQP/MQTT 連線方式連線到 IoT 中樞。
* **IoT Edge 中樞**可能會透過 WebSockets 持續以 AMQP 連線或多種 MQTT 連線方式連線到 IoT 中樞。
* **IoT Edge 精靈**對 IoT 中樞進行 HTTPS 間歇性呼叫。

在這三個案例中，DNS 名稱會符合模式 \*.azure-devices.net。

此外，**容器引擎**會透過 HTTPS 呼叫容器登錄。 若要擷取 IoT Edge 執行階段容器映像，DNS 名稱為 mcr.microsoft.com。 容器引擎會依部署中的設定，連線到其他登錄。

此檢查清單是防火牆規則的起始點：

   | URL (\* = 萬用字元) | 輸出 TCP 連接埠 | 使用量 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft 容器登錄 |
   | global.azure-devices-provisioning.net  | 443 | DPS 存取 (選用) |
   | \*.azurecr.io | 443 | 個人和協力廠商集裝箱註冊表 |
   | \*.blob.core.windows.net | 443 | 從 Blob 存儲下載 Azure 容器註冊表映射增量 |
   | \*.azure-devices.net | 5671、8883、443 | IoT 中樞存取 |
   | \*.docker.io  | 443 | 碼頭中心訪問（可選） |

其中一些防火牆規則是從 Azure 容器註冊表繼承的。 有關詳細資訊，請參閱[配置規則以訪問防火牆後面的 Azure 容器註冊表](../container-registry/container-registry-firewall-access-rules.md)。

### <a name="configure-communication-through-a-proxy"></a>設定 Proxy 通訊

如果要在使用 Proxy 伺服器的網路上部署您的裝置，裝置必須能夠透過 Proxy 通訊，以便觸達 IoT 中樞和容器登錄。 如需詳細資訊，請參閱[設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊](how-to-configure-proxy-support.md)。

## <a name="solution-management"></a>解決方案管理

* **實用**
  * 設定記錄與診斷
  * 請考量測試和 CI/CD 管線

### <a name="set-up-logs-and-diagnostics"></a>設定記錄與診斷

在 Linux 上，IoT Edge 守護進程使用日誌作為預設日誌記錄驅動程式。 您可以使用命令列工具 `journalctl` 查詢精靈記錄。 在 Windows 中，IoT Edge 精靈會使用 PowerShell 診斷。 使用 `Get-IoTEdgeLog` 查詢精靈記錄。 IoT Edge 模組使用 JSON 驅動程式進行日誌記錄，這是預設值。  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

正在測試 IoT Edge 部署時，通常可以存取您的裝置來擷取記錄並進行疑難排解。 在部署情節中，可能不提供該選項。 請考慮要如何收集生產環境中的裝置相關資訊。 其中一個選項是使用能夠收集其他模組資訊，並將資訊傳送至雲端的記錄模組。 其中一個記錄模組範例是 [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics)，您也可以設計自己的專用模組。

### <a name="place-limits-on-log-size"></a>對日誌大小進行限制

預設情況下，Moby 容器引擎不設置容器日誌大小限制。 隨著時間的推移，這可能導致設備充滿日誌和磁碟空間耗盡。 請考慮以下選項以防止出現這種情況：

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>選項：設置適用于所有容器模組的全域限制

您可以限制容器引擎日誌選項中所有容器日誌檔的大小。 以下示例將日誌驅動程式設置為`json-file`（建議），對檔的大小和數量有限制：

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

將此資訊添加到（或追加）到名為`daemon.json`的檔，並將其放置在設備平臺的正確位置。

| Platform | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

必須重新開機容器引擎，更改才能生效。

#### <a name="option-adjust-log-settings-for-each-container-module"></a>選項：調整每個容器模組的日誌設置

您可以在每個模組的**創建選項**中執行此操作。 例如：

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

* 通過將日誌設置為`journald`預設日誌記錄驅動程式，將`systemd`容器引擎配置為將日誌發送到[日誌](https://docs.docker.com/config/containers/logging/journald/)。

* 通過安裝日誌旋轉工具，定期從設備中刪除舊日誌。 使用下列檔案規格：

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
