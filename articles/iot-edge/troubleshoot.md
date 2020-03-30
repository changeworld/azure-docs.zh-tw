---
title: 移難排解 - Azure IoT Edge | Microsoft Docs
description: 使用本文以了解 Azure IoT Edge 的標準診斷技巧，例如擷取元件狀態和記錄，以及解決常見的問題
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 13eab175356ed1ec20caa3263ba00d0563384f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064383"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常見問題和解決方案

如果您在您的環境中執行 Azure IoT Edge 時遇到問題，請使用本文作為疑難排解和解決方案的指引。

## <a name="run-the-iotedge-check-command"></a>運行 iotedge"檢查"命令

對 IoT Edge 進行故障排除的第一步應該是使用`check`該命令，該命令針對常見問題回合組態和連接測試的集合。 該`check`命令在版本[1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7)及更高版本中可用。

可以按照如下方式`check`運行該命令，`--help`或包括標誌以查看完整的選項清單：

* 在 Linux 上：

  ```bash
  sudo iotedge check
  ```

* 在 Windows 上：

  ```powershell
  iotedge check
  ```

該工具運行的檢查類型可以分為：

* 配置檢查：檢查可能阻止 Edge 設備連接到雲的詳細資訊，包括*config.yaml*和容器引擎的問題。
* 連接檢查：驗證 IoT Edge 運行時可以訪問主機設備上的埠，並且所有 IoT Edge 元件都可以連接到 IoT 中心。
* 生產就緒性檢查：查找推薦的生產最佳實踐，例如設備憑證授權單位 （CA） 證書的狀態和模組日誌檔配置。

有關診斷檢查的完整清單，請參閱[內置故障排除功能](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)。

## <a name="gather-debug-information-with-iotedge-support-bundle-command"></a>使用 iotedge"支援捆綁"命令收集調試資訊

當您需要從 IoT Edge 設備收集日誌時，最方便的方法是使用 該`support-bundle`命令。 預設情況下，此命令收集模組、IoT Edge 安全管理器和容器引擎日誌、"iotedge 檢查"JSON 輸出和其他有用的調試資訊。 它將它們壓縮到單個檔中，以便輕鬆共用。 該`support-bundle`命令在版本[1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9)及更高版本中可用。

使用`--since`標誌`support-bundle`運行該命令，以指定要獲取日誌的過去多長時間。 例如`6h`，將獲取自過去 6 小時以來`6d`的日誌，因為過去`6m`6 天，最近 6 分鐘，等等。 包括標誌`--help`以查看完整的選項清單。


* 在 Linux 上：

  ```bash
  sudo iotedge support-bundle --since 6h
  ```

* 在 Windows 上：

  ```powershell
  iotedge support-bundle --since 6h
  ```

> [!WARNING]
> 來自該命令`support-bundle`的輸出可以包含主機、設備和模組名稱、模組記錄的資訊等。如果在公共論壇中共用輸出，請注意這一點。

## <a name="standard-diagnostic-steps"></a>標準的診斷步驟

如果遇到問題，可以通過查看容器日誌和傳遞和從設備傳遞的消息來瞭解有關 IoT Edge 設備狀態的資訊。 可使用這一節中的命令與工具來收集資訊。

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>檢查 IoT 邊緣安全管理器及其日誌的狀態

在 Linux 上：

* 若要檢視 IoT Edge 安全性管理員的狀態：

   ```bash
   sudo systemctl status iotedge
   ```

* 若要檢視 IoT Edge 安全性管理員的記錄：

    ```bash
    sudo journalctl -u iotedge -f
    ```

* 若要檢視 IoT Edge 安全性管理員更詳細的記錄：

  * 編輯 iotedge 精靈設定：

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * 更新以下幾行：

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * 重新啟動 IoT Edge 安全性精靈：

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

在 Windows 上：

* 若要檢視 IoT Edge 安全性管理員的狀態：

   ```powershell
   Get-Service iotedge
   ```

* 若要檢視 IoT Edge 安全性管理員的記錄：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>如果 IoT Edge 安全性管理員未執行，請驗證您的 yaml 組態檔

> [!WARNING]
> YAML 檔不能包含縮進選項卡。 請改用 2 個空格。 頂級元素應沒有前導空格。

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>檢查容器記錄的問題

在 IoT Edge 安全性精靈開始執行後，請查看容器的記錄以偵測問題。 從已部署的容器開始，然後查看構成 IoT Edge 運行時的容器：邊緣代理和邊緣Hub。 IoT Edge 代理日誌通常提供有關每個容器生命週期的資訊。 IoT Edge 中心日誌提供有關消息傳遞和路由的資訊。

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>查看通過 IoT 邊緣中心的消息

您可以查看通過 IoT Edge 中心的消息，並從運行時容器收集詳細日誌的見解。 若要開啟這些容器上的詳細資訊記錄，請在 yaml 組態檔中設定 `RuntimeLogLevel`。 若要開啟檔案：

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

根據預設，`agent` 元素看起來如下範例所示：

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

將 `env: {}` 取代為：

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML 檔案不可包含使用 Tab 鍵的縮排。 請改用 2 個空格。 頂級專案不能具有前導空格。

儲存檔案並重新啟動 IoT Edge 安全性管理員。

您也可以檢查在 IoT 中樞與 IoT Edge 裝置之間傳送的訊息。 使用 Visual Studio[代碼的 Azure IoT 中心副檔名](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)查看這些消息。 如需詳細資訊，請參閱[使用 Azure IoT 進行開發時的便利工具](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)。

### <a name="restart-containers"></a>重新啟動容器

檢查過記錄和訊息並取得資訊後，您可以嘗試重新啟動容器：

```cmd
iotedge restart <container name>
```

重新啟動 IoT Edge 執行階段容器：

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>重新啟動 IoT Edge 安全性管理員

如果問題仍持續發生，您可以嘗試重新啟動 IoT Edge 安全性管理員。

在 Linux 上：

   ```cmd
   sudo systemctl restart iotedge
   ```

在 Windows 上：

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT 邊緣代理在大約一分鐘後停止

邊緣代理模組啟動並成功運行約一分鐘，然後停止。 日誌指示 IoT 邊緣代理嘗試通過 AMQP 連接到 IoT 中心，然後嘗試通過 WebSocket 使用 AMQP 進行連接。 當失敗時，IoT 邊緣代理將退出。

示例邊緣代理日誌：

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**根源**

主機網路上的網路設定阻止 IoT Edge 代理到達網路。 代理程式首先嘗試透過 AMQP (連接埠 5671) 進行連線。 如果連線失敗，代理程式會嘗試 Websocket (連接埠 443)。

IoT Edge 執行階段會為每個模組設定要在其中通訊的網路。 在 Linux 上，此網路是橋接網路。 在 Windows 上則是使用 NAT。 此問題較常見於使用 Windows 容器 (使用 NAT 網路) 的 Windows 裝置。

**解析度**

確認指派給此橋接器/NAT 網路的 IP 位址能路由至網際網路。 有時候主機上的 VPN 組態會覆寫 IoT Edge 網路。

## <a name="iot-edge-hub-fails-to-start"></a>IoT 邊緣中心無法啟動

EdgeHub 模組無法啟動，並將以下消息列印到日誌：

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**根源**

在主機機器上的某些其他程序已繫結連接埠 443。 IoT Edge 集線器映射埠 5671 和 443，用於閘道方案。 如果另一個程序已繫結此連接埠，則此連接埠對應會失敗。

**解析度**

尋找並停止正在使用連接埠 443 的程序。 此程序通常是網頁伺服器。

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT 邊緣代理無法訪問模組的圖像 （403）

容器無法運行，邊緣代理日誌顯示 403 錯誤。

**根源**

IoT Edge 代理無權訪問模組的映射。

**解析度**

確定您已在部署資訊清單中正確指定登錄認證

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 安全性精靈因主機名稱無效而失敗

命令 `sudo journalctl -u iotedge` 失敗，並列印下列訊息：

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**根源**

IoT Edge 執行階段只能支援少於 64 個字元的主機名稱。 實體機器通常不需要很長的主機名稱，但問題在虛擬機器上更常發生。 在 Azure 中代管的 Windows 虛擬機器自動產生的主機名稱通常很長。

**解析度**

當發現這個錯誤時，可以設定虛擬機器的 DNS 名稱，然後將 DNS 名稱設定為安裝命令中的主機名稱來解決。

1. 在 Azure 入口網站中，瀏覽至虛擬機器的概觀頁面。
2. 選取 DNS 名稱下的 [設定]****。 如果虛擬機器已設定 DNS 名稱，則不需要設定新的名稱。

   ![設定虛擬機器的 DNS 名稱](./media/troubleshoot/configure-dns.png)

3. 提供 **DNS 名稱標籤**的值，並選取 [儲存]****。
4. 複製新的 DNS 名稱，該名稱應採用**\<DNSname\>標籤\<的格式。vmlocation\>.cloudapp.azure.com**。
5. 在虛擬機器中，使用下列命令以您的 DNS 名稱設定 IoT Edge 執行階段：

   * 在 Linux 上：

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * 在 Windows 上：

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>資源受限裝置的穩定性問題

您可能會遇到受限裝置 (例如 Raspberry Pi) 的穩定性問題，尤其在該裝置當作閘道的時候。 徵兆包括 Edge 中樞模組的記憶體不足例外狀況，下游裝置無法連線，或裝置在幾小時後停止傳送遙測訊息。

**根源**

IoT Edge 中心是 IoT Edge 運行時的一部分，預設情況下針對性能進行了優化，並嘗試分配大量記憶體。 此最佳化不適合用於受限邊緣裝置，而且可能會造成穩定性問題。

**解析度**

對於 IoT 邊緣中心，將環境變數 **"優化性能"** 設置為**false**。 有兩種方法可以設置環境變數：

在 Azure 入口網站中：

在 IoT 中心中，從設備詳細資訊頁面選擇 IoT 邊緣設備，然後選擇 **"設置模組** > **運行時設置**"。 為邊緣中心模組創建一個環境變數，稱為 *"優化性能*"，該模組設置為*false*。

![將 OptimizeForPerformance 設定為 false](./media/troubleshoot/optimizeforperformance-false.png)

**或**

在部署資訊清單中：

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>無法在 Windows 上取得 IoT Edge 精靈記錄 (daemon log)

如果您在 Windows 上使用 `Get-WinEvent` 時收到 EventLogException，請檢查您的登錄項目。

**根源**

`Get-WinEvent` PowerShell 命令須依賴存在的登錄項目，才能根據特定 `ProviderName` 尋找記錄。

**解析度**

設定 IoT Edge 精靈的登錄項目。 建立具有下列內容的 **iotedge.reg** 檔案，然後按兩下此檔案或使用 `reg import iotedge.reg` 命令將檔案匯入至 Windows 登錄：

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge 模組因發生 404 錯誤而無法將訊息傳送給 edgeHub

自訂 IoT Edge 模組因發生 404 `Module not found` 錯誤而無法將訊息傳送給 edgeHub。 IoT Edge 精靈會將下列訊息輸出至記錄：

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**根源**

基於安全考量，IoT Edge 精靈會針對連線至 edgeHub 的所有模組強制執行處理序識別。 它會確認模組傳送的所有訊息都來自該模組的主要處理序識別碼。 如果傳送訊息之模組的來源處理序識別碼與最初確立的識別碼不同，它就會發出 404 錯誤訊息來拒絕該訊息。

**解析度**

從版本 1.0.7 開始，所有模組進程都有權連接。 如果無法升級到 1.0.7，則完成以下步驟。 有關詳細資訊，請參閱[1.0.7 版本更改日誌](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)。

請確定自訂 IoT Edge 模組一律使用相同的處理序識別碼將訊息傳送給 edgeHub。 例如`ENTRYPOINT`，請確保在 Docker 檔中`CMD`而不是命令，因為`CMD`這將導致模組的一個進程 ID 和運行主程式的 bash 命令的另一個進程 ID，`ENTRYPOINT`而將會導致單個進程 ID。

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>適用於 IoT Edge 部署的防火牆和連接埠設定規則

Azure IoT Edge 允許使用受支援的 IoT 中心協定從本機伺服器到 Azure 雲[進行通信，請參閱選擇通訊協定](../iot-hub/iot-hub-devguide-protocols.md)。 為了加強安全性，Azure IoT Edge 和 Azure IoT 中樞之間的通訊通道一律會設定為輸出。 此設定根據[服務輔助通訊模式](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)，可有效減少惡意實體要探索的攻擊面。 僅當 Azure IoT 中樞需要將訊息推送到 Azure IoT Edge 裝置的特定案例時，才需要輸入通信。 使用安全的 TLS 通道保護雲端到裝置訊息，並且可以使用 X.509 憑證和 TPM 裝置模組進一步保護雲端。 「Azure IoT Edge 安全性管理員」會控管此通訊的建立方式，請參閱 [IoT Edge 安全性管理員](../iot-edge/iot-edge-security-manager.md)。

雖然 IoT Edge 提供增強的設定來保護 Azure IoT Edge 執行階段和已部署的模組，但它仍然倚賴基礎的機器和網路設定。 因此，必須確保為安全邊緣到雲通訊設定適當的網路和防火牆規則。 當託管 Azure IoT Edge 運行時的基礎伺服器的配置防火牆規則時，下表可用作指南：

|通訊協定|連接埠|傳入|傳出|指引|
|--|--|--|--|--|
|MQTT|8883|已封鎖 (預設值)|已封鎖 (預設值)|<ul> <li>使用 MQTT 作為通訊協定時，請將「傳出」(輸出) 設定為「開放」。<li>IoT Edge 不支援適用於 MQTT 的 1883。 <li>應該將傳入 (輸入) 連線封鎖。</ul>|
|AMQP|5671|已封鎖 (預設值)|開放 (預設值)|<ul> <li>IoT Edge 的預設通訊協定。 <li> 如果未設定 Azure IoT Edge 使用其他支援的通訊協定，或 AMQP 是所需的通訊協定，則必須設定為「開放」。<li>IoT Edge 不支援適用於 AMQP 的 5672。<li>當 Azure IoT Edge 使用不同的 IoT Hub 已支援通訊協定時，請封鎖此連接埠。<li>應該將傳入 (輸入) 連線封鎖。</ul></ul>|
|HTTPS|443|已封鎖 (預設值)|開放 (預設值)|<ul> <li>設定連出 (輸出) 在 443 上開啟以進行 IoT Edge 佈建。 使用手動指令碼或 Azure IoT 裝置佈建服務 (DPS) 時，就需要此設定。 <li>「傳入」(輸入) 連線應該只有針對特定案例才設定為「開放」： <ul> <li>  如果您有透明閘道，而此閘道具有可能傳送方法要求的分葉裝置。 在此情況下，無須對外部網路開放連接埠 443，即可連線至 IoTHub 或透過 Azure IoT Edge 提供 IoTHub 服務。 因此，可將傳入規則限制成只從內部網路開放「傳入」(輸入)。 <li> 針對「用戶端到裝置」(C2D) 案例。</ul><li>IoT Edge 不支援適用於 HTTP 的 80。<li>如果無法在企業中設定非 HTTP 通訊協定 (例如 AMQP 或 MQTT)；則可透過 WebSocket 傳送訊息。 在該情況下，會使用連接埠 443 來進行 WebSocket 通訊。</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>邊緣代理模組不斷報告"空設定檔"，並且設備上沒有啟動模組

設備在部署中定義的啟動模組時遇到問題。 只有邊緣代理在運行，但不斷報告"空設定檔..."。

**根源**

預設情況下，IoT Edge 在各自的隔離容器網路中啟動模組。 設備可能在此私人網路絡中遇到 DNS 名稱解析問題。

**解析度**

**選項 1：在容器引擎設置中設置 DNS 伺服器**

在容器引擎設置中指定環境的 DNS 伺服器，該設置將應用於引擎啟動的所有容器模組。 創建指定要使用的`daemon.json`DNS 伺服器的檔。 例如：

```json
{
    "dns": ["1.1.1.1"]
}
```

上述示例將 DNS 伺服器設置為可公開訪問的 DNS 服務。 如果邊緣設備無法從其環境中訪問此 IP，請將其替換為可訪問的 DNS 伺服器位址。

放置在`daemon.json`適合您的平臺的位置：

| Platform | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| 帶 Windows 容器的 Windows 主機 | `C:\ProgramData\iotedge-moby\config` |

如果位置已包含`daemon.json`檔，則向其添加**dns**金鑰並保存該檔。

重新開機容器引擎，使更新生效。

| Platform | Command |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| 視窗（管理電源外殼） | `Restart-Service iotedge-moby -Force` |

**選項 2：將 DNS 伺服器設置為每個模組的 IoT 邊緣部署**

您可以在 IoT 邊緣部署中為每個模組的*創建選項*設置 DNS 伺服器。 例如：

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

請務必為*邊緣代理*和*邊緣Hub*模組設置此配置。

## <a name="next-steps"></a>後續步驟

您在 IoT Edge 平台中發現到錯誤嗎？ [提交問題](https://github.com/Azure/iotedge/issues)，讓我們可以持續進行改善。

如果您有其他問題，請建立[支援要求](https://portal.azure.com/#create/Microsoft.Support)以取得協助。
