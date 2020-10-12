---
title: 常見錯誤-Azure IoT Edge |Microsoft Docs
description: 使用本文來解決部署 IoT Edge 解決方案時遇到的常見問題
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ed93d24bc06a6622a8ace2b0ab6b44582da001c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "82783743"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常見問題和解決方案

您可以使用本文來尋找解決部署 IoT Edge 解決方案時可能遇到之常見問題的步驟。 如果您需要瞭解如何尋找 IoT Edge 裝置的記錄和錯誤，請參閱針對 [您的 IoT Edge 裝置進行疑難排解](troubleshoot.md)。

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge 代理程式在大約一分鐘後停止

**看到的行為：**

EdgeAgent 模組會啟動並順利執行大約一分鐘，然後停止。 記錄指出 IoT Edge 代理程式嘗試透過 AMQP 連線到 IoT 中樞，然後嘗試使用 AMQP 透過 WebSocket 連線。 當該作業失敗時，IoT Edge 代理程式就會結束。

範例 edgeAgent 記錄：

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**根源：**

主機網路上的網路設定會導致 IoT Edge 代理程式無法到達網路。 代理程式首先嘗試透過 AMQP (連接埠 5671) 進行連線。 如果連線失敗，代理程式會嘗試 Websocket (連接埠 443)。

IoT Edge 執行階段會為每個模組設定要在其中通訊的網路。 在 Linux 上，此網路是橋接網路。 在 Windows 上則是使用 NAT。 此問題較常見於使用 Windows 容器 (使用 NAT 網路) 的 Windows 裝置。

**解決方法：**

確認指派給此橋接器/NAT 網路的 IP 位址能路由至網際網路。 有時候主機上的 VPN 組態會覆寫 IoT Edge 網路。

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge 代理程式無法存取模組的映射 (403) 

**看到的行為：**

容器無法執行，且 edgeAgent 記錄顯示403錯誤。

**根源：**

IoT Edge 代理程式沒有存取模組映射的許可權。

**解決方法：**

請確定您的部署資訊清單中已正確指定您的登錄認證。

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge 代理程式模組報告「空白設定檔」，且裝置上未啟動任何模組

**看到的行為：**

裝置在啟動部署中定義的模組時遇到問題。 只有 edgeAgent 正在執行，但持續報告「空白設定檔 ...」。

**根源：**

根據預設，IoT Edge 會在其專屬的隔離容器網路中啟動模組。 裝置在這個私人網路內可能會發生 DNS 名稱解析問題。

**解決方法：**

**選項1：在容器引擎設定中設定 DNS 伺服器**

在容器引擎設定中為您的環境指定 DNS 伺服器，這會套用到引擎啟動的所有容器模組。 建立名為的檔案， `daemon.json` 以指定要使用的 DNS 伺服器。 例如：

```json
{
    "dns": ["1.1.1.1"]
}
```

上述範例會將 DNS 伺服器設定為可公開存取的 DNS 服務。 如果 edge 裝置無法從其環境存取此 IP，請將它取代為可存取的 DNS 伺服器位址。

放 `daemon.json` 在適合您平臺的位置：

| 平台 | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows 主機與 Windows 容器 | `C:\ProgramData\iotedge-moby\config` |

如果位置已包含檔案 `daemon.json` ，請將 **dns** 金鑰新增至該檔案，然後儲存檔案。

重新開機容器引擎，更新才會生效。

| 平台 | Command |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (管理 PowerShell)  | `Restart-Service iotedge-moby -Force` |

**選項2：在每個模組的 IoT Edge 部署中設定 DNS 伺服器**

您可以在 IoT Edge 部署中，為每個模組的 *createOptions* 設定 DNS 伺服器。 例如：

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

也請務必為 *edgeAgent* 和 *edgeHub* 模組設定此設定。

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub 無法啟動

**看到的行為：**

EdgeHub 模組無法啟動。 您可能會在記錄中看到類似下列其中一個錯誤的訊息：

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

或者

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**根源：**

主機電腦上的其他進程已系結 edgeHub 模組嘗試系結的埠。 IoT Edge 中樞會對應埠443、5671和8883以用於閘道案例。 如果另一個進程已系結其中一個埠，則模組無法啟動。

**解決方法：**

您可以透過兩種方式解決此問題：

如果 IoT Edge 裝置作為閘道裝置運作，您需要尋找並停止使用埠443、5671或8883的進程。 埠443的錯誤通常表示另一個進程是 web 伺服器。

如果您不需要使用 IoT Edge 裝置作為閘道，您可以從 edgeHub 的模組建立選項中移除埠系結。 您可以在 Azure 入口網站中或直接在檔案 deployment.js中變更建立選項。

在 Azure 入口網站中：

1. 流覽至您的 IoT 中樞，然後選取 [ **IoT Edge**]。

2. 選取您要更新的 IoT Edge 裝置。

3. 選取 [ **設定模組**]。

4. 選取 [ **執行時間設定**]。

5. 在 **Edge 中樞** 模組設定中，從 [ **建立選項** ] 文字方塊中刪除所有專案。

6. 儲存您的變更並建立部署。

在 [檔案 deployment.js：

1. 開啟您套用至 IoT Edge 裝置的 deployment.js檔案。

2. `edgeHub`在 [edgeAgent 所需屬性] 區段中尋找設定：

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. 移除 `createOptions` 該行，然後在該行的結尾加上尾端逗號 `image` ：

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. 儲存檔案，並再次將它套用到您的 IoT Edge 裝置。

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 安全性精靈因主機名稱無效而失敗

**看到的行為：**

嘗試 [檢查 IoT Edge 安全性管理員記錄](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) 檔失敗，並列印下列訊息：

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**根源：**

IoT Edge 執行階段只能支援少於 64 個字元的主機名稱。 實體機器通常不需要很長的主機名稱，但問題在虛擬機器上更常發生。 在 Azure 中代管的 Windows 虛擬機器自動產生的主機名稱通常很長。

**解決方法：**

當發現這個錯誤時，可以設定虛擬機器的 DNS 名稱，然後將 DNS 名稱設定為安裝命令中的主機名稱來解決。

1. 在 Azure 入口網站中，瀏覽至虛擬機器的概觀頁面。
2. 選取 DNS 名稱下的 [設定]****。 如果虛擬機器已設定 DNS 名稱，則不需要設定新的名稱。

   ![設定虛擬機器的 DNS 名稱](./media/troubleshoot/configure-dns.png)

3. 提供 **DNS 名稱標籤**的值，並選取 [儲存]****。
4. 複製新的 DNS 名稱，**其格式應為。 \<DNSnamelabel\> \<vmlocation\>cloudapp.azure.com**。
5. 在虛擬機器中，使用下列命令以您的 DNS 名稱設定 IoT Edge 執行階段：

   * 在 Linux 上：

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * 在 Windows 上：

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>無法在 Windows 上取得 IoT Edge 精靈記錄 (daemon log)

**看到的行為：**

在 Windows 上使用時，您會收到 EventLogException `Get-WinEvent` 。

**根源：**

`Get-WinEvent` PowerShell 命令須依賴存在的登錄項目，才能根據特定 `ProviderName` 尋找記錄。

**解決方法：**

設定 IoT Edge 精靈的登錄項目。 建立具有下列內容的 **iotedge.reg** 檔案，然後按兩下此檔案或使用 `reg import iotedge.reg` 命令將檔案匯入至 Windows 登錄：

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>較小型裝置的穩定性問題

**看到的行為：**

您可能會在資源受限的裝置（例如 Raspberry Pi）上遇到穩定性問題，尤其是做為閘道使用。 徵兆包含 IoT Edge 中樞模組中的記憶體不足例外狀況、下游裝置無法連線，或在幾個小時後無法傳送遙測訊息。

**根源：**

IoT Edge hub （IoT Edge 執行時間的一部分）依預設會針對效能優化，而且會嘗試配置大型的記憶體區塊。 此最佳化不適合用於受限邊緣裝置，而且可能會造成穩定性問題。

**解決方法：**

針對 IoT Edge 中樞，將環境變數 **將 optimizeforperformance** 設定為 **false**。 有兩種方式可以設定環境變數：

在 Azure 入口網站中：

在 IoT 中樞中，選取您的 IoT Edge 裝置，然後從 [裝置詳細資料] 頁面選取 [**設定模組**  >  **運行**時間設定]。 針對稱為 *將 optimizeforperformance* 的 IoT Edge 中樞模組建立環境變數，並將其設定為 *false*。

![將 OptimizeForPerformance 設定為 false](./media/troubleshoot/optimizeforperformance-false.png)

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

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge 模組無法將訊息傳送至 edgeHub，發生404錯誤

**看到的行為：**

自訂 IoT Edge 模組無法將訊息傳送至 IoT Edge 中樞，發生 404 `Module not found` 錯誤。 IoT Edge 精靈會將下列訊息輸出至記錄：

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**根源：**

基於安全考量，IoT Edge 精靈會針對連線至 edgeHub 的所有模組強制執行處理序識別。 它會確認模組傳送的所有訊息都來自該模組的主要處理序識別碼。 如果傳送訊息之模組的來源處理序識別碼與最初確立的識別碼不同，它就會發出 404 錯誤訊息來拒絕該訊息。

**解決方法：**

從版本1.0.7，所有模組處理常式都有權連接。 如需詳細資訊，請參閱 [1.0.7 release 變更記錄](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)。

如果無法升級至1.0.7，請完成下列步驟。 請確定自訂 IoT Edge 模組一律使用相同的處理序識別碼將訊息傳送給 edgeHub。 例如，請確定您的 `ENTRYPOINT` Docker 檔案中的不是 `CMD` 命令。 此 `CMD` 命令會導致模組有一個處理序識別碼，而 bash 命令的另一個處理常式識別碼則會 `ENTRYPOINT` 產生單一處理序識別碼。

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge 模組部署成功，然後從裝置中消失

**看到的行為：**

設定 IoT Edge 裝置的模組之後，會成功部署模組，但會在幾分鐘後從裝置和裝置的詳細資料 Azure 入口網站中消失。 其他模組（而非所定義的模組）也會出現在裝置上。

**根源：**

如果自動部署以裝置為目標，則會優先于手動設定單一裝置的模組。 在 Visual Studio Code 中 Azure 入口網站或**建立單一裝置功能部署**的**設定模組**功能，將會在一段時間後生效。 您會看到您所定義的模組在裝置上啟動。 然後，自動部署的優先順序就會開始，並覆寫裝置的所需屬性。

**解決方法：**

每個裝置只使用一種部署機制，也就是自動部署或個別裝置部署。 如果您有多個以裝置為目標的自動部署，您可以變更優先順序或目標描述，以確定正確的設定適用于指定的裝置。 您也可以更新裝置對應項，使其不再符合自動部署的目標描述。

如需詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

## <a name="next-steps"></a>接下來的步驟

您在 IoT Edge 平台中發現到錯誤嗎？ [提交問題](https://github.com/Azure/iotedge/issues)，讓我們可以持續進行改善。

如果您有其他問題，請建立[支援要求](https://portal.azure.com/#create/Microsoft.Support)以取得協助。
