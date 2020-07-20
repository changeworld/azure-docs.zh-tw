---
title: 移難排解 - Azure IoT Edge | Microsoft Docs
description: 使用本文來瞭解 Azure IoT Edge 的標準診斷技能，例如，抓取元件狀態和記錄
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9b6265bed138960a3839091ed1593413fc85710a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82858588"
---
# <a name="troubleshoot-your-iot-edge-device"></a>針對您的 IoT Edge 裝置進行疑難排解

如果您在環境中執行 Azure IoT Edge 時遇到問題，請使用這篇文章做為疑難排解和診斷的指南。

## <a name="run-the-check-command"></a>執行 ' check ' 命令

疑難排解 IoT Edge 的第一個步驟應該是使用 `check` 命令，它會針對常見問題執行設定和連線測試的集合。 此 `check` 命令可在[release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7)和更新版本中取得。

>[!NOTE]
>如果 IoT Edge 裝置位於 proxy 伺服器後方，則疑難排解工具無法執行連線檢查。

您可以如下所示執行 `check` 命令，或包含 `--help` 旗標來查看完整的選項清單：

在 Linux 上：

```bash
sudo iotedge check
```

在 Windows 上：

```powershell
iotedge check
```

疑難排解工具會執行許多排序為下列三種類別的檢查：

* 設定*檢查*會檢查可能導致 IoT Edge 裝置無法連線到雲端的詳細資料，包括*yaml*和容器引擎的問題。
* 連線*檢查*會確認 IoT Edge 執行時間可以存取主機裝置上的埠，而且所有 IoT Edge 元件都可以連接到 IoT 中樞。 如果 IoT Edge 裝置位於 proxy 後方，這組檢查會傳回錯誤。
* *生產環境就緒性檢查*會尋找建議的生產最佳做法，例如裝置憑證授權單位單位（CA）憑證和模組記錄檔設定的狀態。

如需此工具執行之每項診斷檢查的相關資訊，包括當您收到錯誤或警告時該怎麼辦，請參閱[IoT Edge 疑難排解檢查](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)。

## <a name="gather-debug-information-with-support-bundle-command"></a>使用 [支援配套] 命令收集調試資訊

當您需要從 IoT Edge 裝置收集記錄時，最方便的方式是使用 `support-bundle` 命令。 根據預設，此命令會收集模組、IoT Edge 的安全性管理員和容器引擎記錄、 `iotedge check` JSON 輸出，以及其他有用的偵錯工具資訊。 它會將其壓縮成單一檔案，以方便共用。 此 `support-bundle` 命令可在[release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9)和更新版本中取得。

`support-bundle`使用旗標來執行命令 `--since` ，以指定過去要取得記錄的時間長度。 例如，自過去六個小時以來，自過去六分鐘起就 `6h` 會取得記錄 `6d` `6m` 。 包含 `--help` 旗標，以查看完整的選項清單。

在 Linux 上：

```bash
sudo iotedge support-bundle --since 6h
```

在 Windows 上：

```powershell
iotedge support-bundle --since 6h
```

> [!WARNING]
> 命令的輸出 `support-bundle` 可以包含主機、裝置和模組名稱、模組所記錄的資訊等。如果在公用論壇中共用輸出，請注意這一點。

## <a name="check-your-iot-edge-version"></a>檢查您的 IoT Edge 版本

如果您執行的是舊版 IoT Edge，則升級可能會解決您的問題。 此 `iotedge check` 工具會檢查 IoT Edge 安全性守護程式是否為最新版本，但不會檢查 IoT Edge 中樞和代理程式模組的版本。 若要檢查您裝置上的執行時間模組版本，請使用命令 `iotedge logs edgeAgent` 和 `iotedge logs edgeHub` 。 當模組啟動時，會在記錄中宣告版本號碼。

如需有關如何更新您的裝置的指示，請參閱[更新 IoT Edge 安全性守護程式和運行](how-to-update-iot-edge.md)時間。

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>檢查 IoT Edge 安全性管理員及其記錄的狀態

「 [IoT Edge 安全性管理員](iot-edge-security-manager.md)」負責在啟動和布建裝置時初始化 IoT Edge 系統的作業。 如果 IoT Edge 未啟動，安全性管理員記錄檔可能會提供有用的資訊。

在 Linux 上：

* 查看 IoT Edge 安全性管理員的狀態：

   ```bash
   sudo systemctl status iotedge
   ```

* 查看 IoT Edge 安全性管理員的記錄：

    ```bash
    sudo journalctl -u iotedge -f
    ```

* 查看更詳細的 IoT Edge 安全性管理員的記錄：

  * 編輯 IoT Edge daemon 設定：

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

* 查看 IoT Edge 安全性管理員的狀態：

   ```powershell
   Get-Service iotedge
   ```

* 查看 IoT Edge 安全性管理員的記錄：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* 僅查看 IoT Edge 安全性管理員記錄的過去5分鐘：

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* 查看更詳細的 IoT Edge 安全性管理員的記錄：

  * 新增系統層級環境變數：

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "edgelet=debug", [EnvironmentVariableTarget]::Machine)
      ```

  * 重新啟動 IoT Edge 安全性精靈：

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>如果 IoT Edge 安全性管理員未執行，請驗證您的 yaml 設定檔

> [!WARNING]
> YAML 檔案不能包含索引標籤做為縮排。 請改用 2 個空格。 最上層元素不應該有開頭空白。

在 Linux 上：

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

在 Windows 上：

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
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

## <a name="check-container-logs-for-issues"></a>檢查容器記錄的問題

IoT Edge 的安全性背景程式執行之後，請查看容器的記錄來偵測問題。 從您已部署的容器開始，然後查看組成 IoT Edge 執行時間的容器： edgeAgent 和 edgeHub。 IoT Edge 代理程式記錄通常會提供每個容器生命週期的相關資訊。 IoT Edge 中樞記錄檔會提供訊息和路由的相關資訊。

```cmd
iotedge logs <container name>
```

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>查看通過 IoT Edge 中樞的訊息

您可以查看通過 IoT Edge 中樞的訊息，並從執行時間容器中收集詳細資訊記錄的深入解析。 若要開啟這些容器上的詳細資訊記錄，請在 yaml 組態檔中設定 `RuntimeLogLevel`。 若要開啟檔案：

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
   > YAML 檔案不可包含使用 Tab 鍵的縮排。 請改用 2 個空格。 最上層專案的開頭不能是空白。

儲存檔案並重新啟動 IoT Edge 安全性管理員。

您也可以檢查在 IoT 中樞與 IoT Edge 裝置之間傳送的訊息。 使用[Visual Studio Code 的 Azure IoT 中樞延伸](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)模組來查看這些訊息。 如需詳細資訊，請參閱[使用 Azure IoT 進行開發時的便利工具](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)。

## <a name="restart-containers"></a>重新啟動容器

檢查過記錄和訊息並取得資訊後，您可以嘗試重新啟動容器：

```cmd
iotedge restart <container name>
```

重新啟動 IoT Edge 執行階段容器：

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>檢查您的防火牆和埠設定規則

Azure IoT Edge 允許使用支援的 IoT 中樞通訊協定，從內部部署伺服器到 Azure 雲端的通訊，請參閱[選擇通訊協定](../iot-hub/iot-hub-devguide-protocols.md)。 為了加強安全性，Azure IoT Edge 和 Azure IoT 中樞之間的通訊通道一律會設定為輸出。 此設定根據[服務輔助通訊模式](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)，可有效減少惡意實體要探索的攻擊面。 僅當 Azure IoT 中樞需要將訊息推送到 Azure IoT Edge 裝置的特定案例時，才需要輸入通信。 使用安全的 TLS 通道保護雲端到裝置訊息，並且可以使用 X.509 憑證和 TPM 裝置模組進一步保護雲端。 「Azure IoT Edge 安全性管理員」會控管此通訊的建立方式，請參閱 [IoT Edge 安全性管理員](../iot-edge/iot-edge-security-manager.md)。

雖然 IoT Edge 提供增強的設定來保護 Azure IoT Edge 執行階段和已部署的模組，但它仍然倚賴基礎的機器和網路設定。 因此，請務必確定已設定適當的網路和防火牆規則，以提供安全的邊緣對雲端通訊。 當裝載 Azure IoT Edge 執行時間的基礎伺服器設定防火牆規則時，可以使用下表做為指導方針：

|通訊協定|Port|傳入|傳出|指引|
|--|--|--|--|--|
|MQTT|8883|已封鎖 (預設值)|已封鎖 (預設值)|<ul> <li>使用 MQTT 作為通訊協定時，請將「傳出」(輸出) 設定為「開放」。<li>IoT Edge 不支援適用於 MQTT 的 1883。 <li>應該將傳入 (輸入) 連線封鎖。</ul>|
|AMQP|5671|已封鎖 (預設值)|開放 (預設值)|<ul> <li>IoT Edge 的預設通訊協定。 <li> 如果未設定 Azure IoT Edge 使用其他支援的通訊協定，或 AMQP 是所需的通訊協定，則必須設定為「開放」。<li>IoT Edge 不支援適用於 AMQP 的 5672。<li>當 Azure IoT Edge 使用不同的 IoT Hub 已支援通訊協定時，請封鎖此連接埠。<li>應該將傳入 (輸入) 連線封鎖。</ul></ul>|
|HTTPS|443|已封鎖 (預設值)|開放 (預設值)|<ul> <li>設定連出 (輸出) 在 443 上開啟以進行 IoT Edge 佈建。 使用手動指令碼或 Azure IoT 裝置佈建服務 (DPS) 時，就需要此設定。 <li>「傳入」(輸入) 連線應該只有針對特定案例才設定為「開放」： <ul> <li>  如果您有透明閘道，而此閘道具有可能傳送方法要求的分葉裝置。 在此情況下，無須對外部網路開放連接埠 443，即可連線至 IoTHub 或透過 Azure IoT Edge 提供 IoTHub 服務。 因此，可將傳入規則限制成只從內部網路開放「傳入」(輸入)。 <li> 針對「用戶端到裝置」(C2D) 案例。</ul><li>IoT Edge 不支援適用於 HTTP 的 80。<li>如果無法在企業中設定非 HTTP 通訊協定 (例如 AMQP 或 MQTT)；則可透過 WebSocket 傳送訊息。 在該情況下，會使用連接埠 443 來進行 WebSocket 通訊。</ul>|

## <a name="next-steps"></a>後續步驟

您在 IoT Edge 平台中發現到錯誤嗎？ [提交問題](https://github.com/Azure/iotedge/issues)，讓我們可以持續進行改善。

如果您有其他問題，請建立[支援要求](https://portal.azure.com/#create/Microsoft.Support)以取得協助。
