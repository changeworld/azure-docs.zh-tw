---
title: 常見錯誤-Azure IoT Edge |Microsoft Docs
description: 使用本文來解決部署 IoT Edge 解決方案時所遇到的常見問題
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82783743"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge 的常見問題和解決方案

使用本文來尋找解決部署 IoT Edge 解決方案時可能會遇到之常見問題的步驟。 如果您需要瞭解如何從 IoT Edge 裝置尋找記錄檔和錯誤，請參閱針對[您的 IoT Edge 裝置進行疑難排解](troubleshoot.md)。

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge 代理程式會在大約一分鐘後停止

**看到的行為：**

EdgeAgent 模組會啟動並順利執行約一分鐘，然後停止。 記錄指出 IoT Edge 代理程式會嘗試透過 AMQP 連線到 IoT 中樞，然後嘗試使用 AMQP over WebSocket 進行連接。 當失敗時，IoT Edge 代理程式就會結束。

範例 edgeAgent 記錄：

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**根本原因：**

主機網路上的網路設定導致 IoT Edge 代理程式無法到達網路。 代理程式首先嘗試透過 AMQP (連接埠 5671) 進行連線。 如果連線失敗，代理程式會嘗試 Websocket (連接埠 443)。

IoT Edge 執行階段會為每個模組設定要在其中通訊的網路。 在 Linux 上，此網路是橋接網路。 在 Windows 上則是使用 NAT。 此問題較常見於使用 Windows 容器 (使用 NAT 網路) 的 Windows 裝置。

**解決方法：**

確認指派給此橋接器/NAT 網路的 IP 位址能路由至網際網路。 有時候主機上的 VPN 組態會覆寫 IoT Edge 網路。

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge 代理程式無法存取模組的映射（403）

**看到的行為：**

容器無法執行，且 edgeAgent 記錄顯示403錯誤。

**根本原因：**

IoT Edge 代理程式沒有存取模組映射的許可權。

**解決方法：**

請確定您的部署資訊清單中已正確指定您的登錄認證。

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge 代理程式模組報告 [空白的設定檔]，且裝置上沒有任何模組啟動

**看到的行為：**

裝置在啟動部署中定義的模組時遇到問題。 只有 edgeAgent 正在執行，但持續回報「空的設定檔 ...」。

**根本原因：**

根據預設，IoT Edge 會在自己的隔離容器網路中啟動模組。 裝置在此私人網路內可能會發生 DNS 名稱解析問題。

**解決方法：**

**選項1：在容器引擎設定中設定 DNS 伺服器**

在 [容器引擎設定] 中，為您的環境指定 DNS 伺服器，這會套用至引擎啟動的所有容器模組。 建立名為的檔案， `daemon.json` 並指定要使用的 DNS 伺服器。 例如：

```json
{
    "dns": ["1.1.1.1"]
}
```

上述範例會將 DNS 伺服器設定為可公開存取的 DNS 服務。 如果 edge 裝置無法從其環境存取此 IP，請將它取代為可存取的 DNS 伺服器位址。

放 `daemon.json` 在您平臺的正確位置：

| 平台 | Location |
| --------- | -------- |
| Linux | `/etc/docker` |
| 具有 Windows 容器的 windows 主機 | `C:\ProgramData\iotedge-moby\config` |

如果位置已經包含檔案 `daemon.json` ，請在其中新增**dns**金鑰並儲存檔案。

重新開機容器引擎，更新才會生效。

| 平台 | Command |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows （系統管理員 PowerShell） | `Restart-Service iotedge-moby -Force` |

**選項2：在每個模組的 IoT Edge 部署中設定 DNS 伺服器**

您可以在 IoT Edge 部署中，為每個模組的*createOptions*設定 DNS 伺服器。 例如：

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

也請務必為*edgeAgent*和*edgeHub*模組設定此設定。

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub 無法啟動

**看到的行為：**

EdgeHub 模組無法啟動。 您可能會在記錄檔中看到類似下列其中一個錯誤的訊息：

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Or

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**根本原因：**

主機電腦上的其他進程已經系結了 edgeHub 模組嘗試系結的埠。 IoT Edge 中樞會對應埠443、5671和8883，以在閘道案例中使用。 如果另一個進程已經系結了其中一個埠，模組就無法啟動。

**解決方法：**

您可以透過兩種方式解決此問題：

如果 IoT Edge 裝置做為閘道裝置運作，則您需要尋找並停止使用埠443、5671或8883的進程。 埠443的錯誤通常表示另一個進程是 web 伺服器。

如果您不需要使用 IoT Edge 裝置作為閘道，則可以從 edgeHub 的模組建立選項中移除埠系結。 您可以在 [Azure 入口網站中，或直接在檔案 deployment.js中變更 [建立] 選項。

在 Azure 入口網站中：

1. 流覽至您的 IoT 中樞，然後選取 [ **IoT Edge**]。

2. 選取您要更新的 IoT Edge 裝置。

3. 選取 [**設定模組**]。

4. 選取 [**執行時間設定**]。

5. 在**Edge 中樞**模組設定中，從 [**建立選項**] 文字方塊中刪除所有專案。

6. 儲存您的變更並建立部署。

在 [deployment.js檔案：

1. 開啟您已套用至 IoT Edge 裝置的 deployment.js檔案。

2. 在 `edgeHub` [edgeAgent 所需屬性] 區段中尋找設定：

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

3. 移除 `createOptions` 行，並在行尾的後面加上尾端逗號 `image` ：

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

4. 儲存檔案，然後再次將它套用到您的 IoT Edge 裝置。

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 安全性精靈因主機名稱無效而失敗

**看到的行為：**

嘗試[檢查 IoT Edge 安全性管理員記錄](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs)檔失敗，並列印下列訊息：

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**根本原因：**

IoT Edge 執行階段只能支援少於 64 個字元的主機名稱。 實體機器通常不需要很長的主機名稱，但問題在虛擬機器上更常發生。 在 Azure 中代管的 Windows 虛擬機器自動產生的主機名稱通常很長。

**解決方法：**

當發現這個錯誤時，可以設定虛擬機器的 DNS 名稱，然後將 DNS 名稱設定為安裝命令中的主機名稱來解決。

1. 在 Azure 入口網站中，瀏覽至虛擬機器的概觀頁面。
2. 選取 DNS 名稱下的 [設定]****。 如果虛擬機器已設定 DNS 名稱，則不需要設定新的名稱。

   ![設定虛擬機器的 DNS 名稱](./media/troubleshoot/configure-dns.png)

3. 提供 **DNS 名稱標籤**的值，並選取 [儲存]****。
4. 複製新的 DNS 名稱，其格式應為.。 ** \<DNSnamelabel\> \<vmlocation\>cloudapp.azure.com**。
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

在 Windows 上使用時，您會取得 EventLogException `Get-WinEvent` 。

**根本原因：**

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

## <a name="stability-issues-on-smaller-devices"></a>較小裝置上的穩定性問題

**看到的行為：**

您可能會在資源受限的裝置（例如 Raspberry Pi）上遇到穩定性問題，尤其是做為閘道使用時。 徵兆包括 IoT Edge 中樞模組中的記憶體不足例外狀況、下游裝置無法連線，或裝置在幾小時之後無法傳送遙測訊息。

**根本原因：**

IoT Edge 中樞（屬於 IoT Edge 執行時間的一部分）預設會針對效能進行優化，並嘗試配置大型記憶體區塊。 此最佳化不適合用於受限邊緣裝置，而且可能會造成穩定性問題。

**解決方法：**

針對 IoT Edge 中樞，將環境變數**OptimizeForPerformance**設定為**false**。 有兩種方式可以設定環境變數：

在 Azure 入口網站中：

在您的 IoT 中樞中，選取您的 IoT Edge 裝置，然後從 [裝置詳細資料] 頁面選取 [**設定模組**  >  **執行時間設定**]。 針對稱為*OptimizeForPerformance*的 IoT Edge 中樞模組建立環境變數，並將其設定為*false*。

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

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge 模組無法傳送訊息至 edgeHub，發生404錯誤

**看到的行為：**

自訂 IoT Edge 模組無法傳送訊息至 IoT Edge 中樞，發生 404 `Module not found` 錯誤。 IoT Edge 精靈會將下列訊息輸出至記錄：

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**根本原因：**

基於安全考量，IoT Edge 精靈會針對連線至 edgeHub 的所有模組強制執行處理序識別。 它會確認模組傳送的所有訊息都來自該模組的主要處理序識別碼。 如果傳送訊息之模組的來源處理序識別碼與最初確立的識別碼不同，它就會發出 404 錯誤訊息來拒絕該訊息。

**解決方法：**

從版本1.0.7，所有模組進程都有權連接。 如需詳細資訊，請參閱[1.0.7 release 變更記錄](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)。

如果無法升級至1.0.7，請完成下列步驟。 請確定自訂 IoT Edge 模組一律使用相同的處理序識別碼將訊息傳送給 edgeHub。 例如，請確定您的 `ENTRYPOINT` Docker 檔案中的不是 `CMD` 命令。 此 `CMD` 命令會導致模組的一個處理序識別碼，以及一個執行主要程式的 bash 命令的另一個處理序識別碼，但會 `ENTRYPOINT` 導致單一處理序識別碼。

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge 模組部署成功，然後從裝置中消失

**看到的行為：**

設定 IoT Edge 裝置的模組之後，會成功部署模組，但在幾分鐘後，就會從裝置和 Azure 入口網站的裝置詳細資料中消失。 其他尚未定義的模組也可能會出現在裝置上。

**根本原因：**

如果自動部署是以裝置為目標，則會優先于手動設定單一裝置的模組。 Azure 入口網站中的 [**設定模組**] 功能或 [在 Visual Studio Code 中**建立單一裝置**功能的部署] 將會立即生效。 您會看到您在裝置上開始定義的模組。 然後，自動部署的優先順序會在中啟動，並覆寫裝置的所需屬性。

**解決方法：**

每個裝置只會使用一種類型的部署機制，也就是自動部署或個別裝置部署。 如果您有多個以裝置為目標的自動部署，您可以變更 [優先順序] 或 [目標] 描述，以確保正確的部署適用于指定的裝置。 您也可以更新裝置對應項，使其不再符合自動部署的目標描述。

如需詳細資訊，請參閱[了解單一裝置或大規模的 IoT Edge 自動部署](module-deployment-monitoring.md)。

## <a name="next-steps"></a>後續步驟

您在 IoT Edge 平台中發現到錯誤嗎？ [提交問題](https://github.com/Azure/iotedge/issues)，讓我們可以持續進行改善。

如果您有其他問題，請建立[支援要求](https://portal.azure.com/#create/Microsoft.Support)以取得協助。
