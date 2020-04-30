---
title: 針對網路 Proxy 設定裝置 - Azure IoT Edge | Microsoft Docs
description: 如何設定 Azure IoT Edge 執行階段及任何網際網路對應的 IoT Edge 模組，以透過 Proxy 伺服器進行通訊。
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 270e6a0173ed0088ff5d37c989947f5272634200
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81687190"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊

IoT Edge 裝置會傳送 HTTPS 要求以和 IoT 中樞通訊。 如果您的裝置是連線至使用 Proxy 伺服器的網路，便必須設定 IoT Edge 執行階段以透過伺服器進行通訊。 如果 IoT Edge 模組做出沒有透過 IoT Edge 中樞進行路由的 HTTP 或 HTTPS 要求，則 Proxy 伺服器也可能會影響這些個別的 IoT Edge 模組。

本文會逐步解說下列四個步驟，以設定並管理 proxy 伺服器後方的 IoT Edge 裝置：

1. **在裝置上安裝 IoT Edge 執行階段。**

   IoT Edge 安裝腳本會從網際網路提取套件和檔案，因此您的裝置必須透過 proxy 伺服器進行通訊，才能提出這些要求。 如需詳細步驟，請參閱本文的[透過 Proxy 安裝運行](#install-the-runtime-through-a-proxy)時間一節。 針對 Windows 裝置，安裝腳本也會提供[離線安裝](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)選項。

   此步驟是您第一次設定時，在 IoT Edge 裝置上執行的一次性程式。 當您更新 IoT Edge 執行時間時，也需要相同的連接。

2. **在您的裝置上設定 Docker daemon 和 IoT Edge daemon。**

   IoT Edge 在裝置上使用兩個守護程式，這兩者都需要透過 proxy 伺服器提出 web 要求。 IoT Edge daemon 負責與 IoT 中樞通訊。 Moby daemon 負責管理容器，因此會與容器登錄通訊。 如需詳細步驟，請參閱本文的[設定守護](#configure-the-daemons)程式一節。

   此步驟是您第一次設定時，在 IoT Edge 裝置上執行的一次性程式。

3. **在您裝置上的 yaml 檔案中設定 IoT Edge 代理程式屬性。**

   IoT Edge daemon 一開始會啟動 edgeAgent 模組，但是 edgeAgent 模組會負責從 IoT 中樞抓取部署資訊清單，並啟動所有其他模組。 若要讓 IoT Edge 代理程式進行 IoT 中樞的初始連線，請在裝置本身手動設定 edgeAgent 模組環境變數。 在初始連接之後，您可以從遠端設定 edgeAgent 模組。 如需詳細步驟，請參閱本文的[設定 IoT Edge 代理程式](#configure-the-iot-edge-agent)一節。

   此步驟是您第一次設定時，在 IoT Edge 裝置上執行的一次性程式。

4. **針對所有未來的模組部署，設定任何透過 proxy 進行通訊之模組的環境變數。**

   一旦您的 IoT Edge 裝置已設定並透過 proxy 伺服器連接到 IoT 中樞，您就必須在所有未來的模組部署中維護連接。 如需詳細步驟，請參閱本文的[設定部署資訊清單](#configure-deployment-manifests)一節。

   此步驟是從遠端執行的持續程式，讓每個新的模組或部署更新都能維護裝置透過 proxy 伺服器進行通訊的能力。

## <a name="know-your-proxy-url"></a>知道您的 Proxy URL

開始本文中的任何步驟之前，您必須先知道您的 proxy URL。

Proxy URL 採用下列格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol**是 HTTP 或 HTTPS。 Docker daemon 可以使用任一種通訊協定，視您的容器登錄設定而定，但 IoT Edge 的背景程式和執行時間容器應該一律使用 HTTP 來連線到 proxy。

* **proxy_host**是 Proxy 伺服器的位址。 如果您的 proxy 伺服器需要驗證，您可以使用下列格式，將您的認證提供為 proxy 主機的一部分： **user**：**password**\@**proxy_host**。

* **proxy_port**是 Proxy 回應網路流量的網路連接埠。

## <a name="install-the-runtime-through-a-proxy"></a>透過 proxy 安裝執行時間

無論您的 IoT Edge 裝置是否在 Windows 或 Linux 上執行，都必須透過 proxy 伺服器存取安裝套件。 視您的作業系統而定，請遵循步驟，透過 proxy 伺服器安裝 IoT Edge 執行時間。

### <a name="linux-devices"></a>Linux 裝置

若您正於 Linux 裝置上安裝 IoT Edge 執行階段，請設定套件管理員以 Proxy 伺服器存取安裝套件。 例如，[設定 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy) \(英文\)。 一旦設定您的套件管理員，請依照往常在[Linux 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-linux.md)中的指示進行。

### <a name="windows-devices"></a>Windows 裝置

如果您要在 Windows 裝置上安裝 IoT Edge 執行時間，則必須經過 proxy 伺服器兩次。 第一個連接會下載安裝程式腳本檔案，而第二個連接會在安裝期間下載必要的元件。 您可以在 Windows 設定中設定 proxy 資訊，或直接在 PowerShell 命令中包含您的 proxy 資訊。

下列步驟示範使用`-proxy`引數進行 windows 安裝的範例：

1. WebRequest 命令需要 proxy 資訊，才能存取安裝程式腳本。 然後，IoTEdge 命令需要 proxy 資訊來下載安裝檔案。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. IoTEdge 命令不需要經過 proxy 伺服器，因此第二個步驟只需要 WebRequest 的 proxy 資訊。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

如果您的 Proxy 伺服器有複雜的認證而無法包含在 URL 中，請在 `-InvokeWebRequestParameters` 中使用 `-ProxyCredential` 參數。 例如，

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

如需 Proxy 參數的詳細資訊，請參閱 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 如需 Windows 安裝選項的詳細資訊，包括離線安裝，請參閱[在 windows 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-windows.md)。

## <a name="configure-the-daemons"></a>設定精靈

IoT Edge 依賴在 IoT Edge 裝置上執行的兩個守護程式。 Moby daemon 會發出 web 要求，以從容器登錄提取容器映射。 IoT Edge 精靈會發出 Web 要求，以和 IoT 中樞通訊。

Moby 和 IoT Edge 的守護程式都必須設定為使用 proxy 伺服器來進行持續的裝置功能。 此步驟會在初始裝置安裝期間，于 IoT Edge 裝置上進行。

### <a name="moby-daemon"></a>Moby daemon

由於 Moby 是以 Docker 為基礎建立，請參閱 Docker 檔，以使用環境變數設定 Moby daemon。 大部分的容器登錄 (包括 Docker Hub 和 Azure Container Registry) 皆支援 HTTPS 要求，因此您應該設定的參數為 **HTTPS_PROXY**。 如果您是從不支援傳輸層安全性 (TLS) 的登錄提取映像，則應該設定 **HTTP_PROXY** 參數。

選擇適用于您 IoT Edge 裝置作業系統的文章：

* [在 Linux 上設定 Docker daemon](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)Linux 裝置上的 Moby daemon 會保留 Docker 的名稱。
* [在 Windows 上設定 Docker daemon](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)Windows 裝置上的 Moby daemon 稱為 iotedge-Moby。 名稱會不同，因為可以在 Windows 裝置上同時執行 Docker Desktop 和 Moby。

### <a name="iot-edge-daemon"></a>IoT Edge 精靈

IoT Edge 守護程式會以類似的方式設定 Moby daemon。 請根據您的作業系統，使用下列相對應的步驟來針對服務設定環境變數。

IoT Edge daemon 一律會使用 HTTPS 將要求傳送至 IoT 中樞。

#### <a name="linux"></a>Linux

在終端機中開啟編輯器以設定 IoT 精靈。

```bash
sudo systemctl edit iotedge
```

輸入下列文字，以您的 proxy 伺服器位址和埠取代** \<proxy URL>** 。 然後儲存並結束。

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

重新整理服務管理員以取得適用於 IoT Edge 的新設定。

```bash
sudo systemctl daemon-reload
```

重新啟動 IoT Edge 來讓變更生效。

```bash
sudo systemctl restart iotedge
```

確認您的環境變數已經建立，並已載入新的設定。

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

以系統管理員身分開啟 PowerShell 視窗，並執行下列命令以搭配新的環境變數編輯登錄。 以您的 proxy 伺服器位址和埠取代** \<proxy url>** 。

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

重新啟動 IoT Edge 來讓變更生效。

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>設定 IoT Edge 代理程式

IoT Edge 代理程式是在任何 IoT Edge 裝置上皆應第一個啟動的模組。 它會根據 IoT Edge config.yaml 檔案中的資訊首次啟動。 IoT Edge 代理程式接著會連線至 IoT 中樞以擷取部署資訊清單，該資訊清單會宣告有哪些其他模組應部署於裝置上。

此步驟會在初始裝置安裝期間，于 IoT Edge 裝置上進行一次。

1. 在您的 IoT Edge 裝置上開啟 config.yaml 檔案。 在 Linux 系統上，此檔案位於 **/etc/iotedge/config.yaml**。 在 Windows 系統上，此檔案位於 **C:\ProgramData\iotedge\config.yaml**。 該設定檔是受保護的，因此您需要系統管理權限以開啟它。 在 Linux 系統上，使用`sudo`命令，然後在您慣用的文字編輯器中開啟檔案。 在 Windows 上，以系統管理員身分開啟 [記事本] 之類的文字編輯器，然後開啟檔案。

2. 在 config.yaml 檔案中，找到 **Edge Agent module spec** 區段。 IoT Edge 代理程式定義包含 **env** 參數，可供您於該處新增環境變數。

3. 移除作為 env 參數之預留位置的大括號，然後將新的變數加入至新的一行上。 請記得 YAML 中的縮排為兩個空格。

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. IoT Edge 執行階段預設會使用 AMQP 來與 IoT 中樞通訊。 某些 Proxy 伺服器會封鎖 AMQP 連接埠。 針對那種情況，您也必須設定 edgeAgent 以使用 AMQP，而非 WebSocket。 新增第二個環境變數。

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![具環境變數的 edgeAgent 定義](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. 儲存對 config.yaml 所做的變更，然後關閉編輯器。 重新啟動 IoT Edge 來讓變更生效。

   * Linux：

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows：

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>設定部署資訊清單  

當您的 IoT Edge 裝置設定為使用 proxy 伺服器時，您必須繼續在未來的部署資訊清單中宣告 HTTPS_PROXY 環境變數。 您可以使用 Azure 入口網站 wizard 或編輯部署資訊清單 JSON 檔案來編輯部署資訊清單。

請一律設定兩個執行階段模組 (edgeAgent 和 edgeHub) 透過 Proxy 伺服器進行通訊，以維持與 IoT 中樞的連線。 如果您從 edgeAgent 模組移除 proxy 資訊，重新建立連線的唯一方式是編輯裝置上的 yaml 檔案，如上一節中所述。

除了 edgeAgent 和 edgeHub 模組之外，其他模組也可能需要 proxy 設定。 這些模組除了 IoT 中樞以外，還需要存取 Azure 資源（例如 blob 儲存體），而且必須在部署資訊清單檔中為該模組指定 HTTPS_PROXY 變數。

下列程式適用于 IoT Edge 裝置的整個生命週期。

### <a name="azure-portal"></a>Azure 入口網站

當您使用 [設定模組]**** 精靈來針對 IoT Edge 裝置建立部署時，每個模組都會有 [環境變數]**** 區段可供您用來設定 Proxy 伺服器連線。

若要設定 IoT Edge 代理程式和 IoT Edge 中樞模組，請在嚮導的第一個步驟中選取 [**執行時間設定**]。

![設定進階 Edge 執行階段設定](./media/how-to-configure-proxy-support/configure-runtime.png)

將 **https_proxy** 環境變數同時新增至 IoT Edge 代理程式和 IoT Edge 中樞模組定義。 如果您已在 IoT Edge 裝置上的 config.yaml 檔案中包含 **UpstreamProtocol** 環境變數，請同時將該環境變數新增至 IoT Edge 代理程式模組定義。

![設定 https_proxy 環境變數](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

所有其他新增至部署資訊清單的模組都會遵循相同的模式。 在您設定模組名稱和映像的頁面中，會有一個環境變數區段。

### <a name="json-deployment-manifest-files"></a>JSON 部署資訊清單檔案

如果您是使用 Visual Studio Code 中的範本，或是手動建立 JSON 檔案來針對 IoT Edge 裝置建立部署，您可以將環境變數直接新增至每個模組定義中。

使用下列 JSON 格式：

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

在包含環境變數的情況下，您的模組定義看起來應該會和下列 edgeHub 範例類似：

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

如果您已在 IoT Edge 裝置上的 confige.yaml 檔案中包含 **UpstreamProtocol** 環境變數，請同時將該環境變數新增至 IoT Edge 代理程式模組定義。

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>後續步驟

深入了解 [IoT Edge 執行階段](iot-edge-runtime.md)的角色。

使用 [Azure IoT Edge 的常見問題和解決方案](troubleshoot.md)來對安裝和設定錯誤進行疑難排解
