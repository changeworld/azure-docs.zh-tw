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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687190"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>設定 IoT Edge 裝置以透過 Proxy 伺服器進行通訊

IoT Edge 裝置會傳送 HTTPS 要求以和 IoT 中樞通訊。 如果您的裝置是連線至使用 Proxy 伺服器的網路，便必須設定 IoT Edge 執行階段以透過伺服器進行通訊。 如果 IoT Edge 模組做出沒有透過 IoT Edge 中樞進行路由的 HTTP 或 HTTPS 要求，則 Proxy 伺服器也可能會影響這些個別的 IoT Edge 模組。

本文介紹以下四個步驟,以配置並隨後管理代理伺服器後面的 IoT Edge 裝置:

1. **在裝置上安裝 IoT Edge 執行階段。**

   IoT Edge 安裝文本從 Internet 中提取包和檔,因此您的設備需要透過代理伺服器進行通訊才能發出這些請求。 有關詳細步驟,請參閱本文[的代理部分安裝執行時](#install-the-runtime-through-a-proxy)。 對於 Windows 設備,安裝腳本還提供[離線安裝](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)選項。

   此步驟是首次設置 IoT Edge 設備上執行的一次性過程。 更新 IoT Edge 運行時也需要相同的連接。

2. **在設備上配置 Docker 守護程序和 IoT 邊緣守護程式。**

   IoT Edge 在裝置上使用兩個守護程式,這兩個守護進程都需要透過代理伺服器發出 Web 請求。 IoT 邊緣守護程式負責與 IoT 中心的通信。 Moby 守護進程負責容器管理,因此與容器註冊表通信。 有關詳細步驟,請參閱本文[的"配置守護進程](#configure-the-daemons)"部分。

   此步驟是首次設置 IoT Edge 設備上執行的一次性過程。

3. **在裝置上的 config.yaml 檔中設定 IoT Edge 代理屬性。**

   IoT Edge 守護程式最初啟動邊緣代理模組,但隨後邊緣代理模組負責從 IoT 中心檢索部署清單並啟動所有其他模組。 要使 IoT Edge 代理與 IoT 中心進行初始連接,請手動在裝置本身配置 EdgeAgent 模組環境變數。 初始連接後,您可以遠端配置邊緣代理模組。 有關詳細步驟,請參閱本文[的"配置IoT邊緣代理"](#configure-the-iot-edge-agent)部分。

   此步驟是首次設置 IoT Edge 設備上執行的一次性過程。

4. **對於所有將來的模組部署,為通過代理通信的任何模組設置環境變數。**

   通過代理伺服器設定 IoT Edge 設備並連接到 IoT 中心後,您需要在以後的所有模組部署中維護連接。 有關詳細步驟,請參閱本文的「[配置部署清單」](#configure-deployment-manifests)部分。

   此步驟是遠端執行的持續過程,以便每個新模組或部署更新都維護設備通過代理伺服器進行通信的能力。

## <a name="know-your-proxy-url"></a>知道您的 Proxy URL

在開始本文中的任何步驟之前,您需要瞭解代理 URL。

Proxy URL 採用下列格式：**protocol**://**proxy_host**:**proxy_port**。

* **protocol**是 HTTP 或 HTTPS。 Docker 守護程式可以使用任一協定,具體取決於容器註冊表設置,但 IoT Edge 守護行程和運行時容器應始終使用 HTTP 連接到代理。

* **proxy_host**是 Proxy 伺服器的位址。 若代理伺服器需要認證,則可以將認證為代理主機的一部分提供以下格式:**使用者**:**密碼**\@**proxy_host**。

* **proxy_port**是 Proxy 回應網路流量的網路連接埠。

## <a name="install-the-runtime-through-a-proxy"></a>透過代理安裝執行時

無論您的 IoT Edge 裝置在 Windows 還是 Linux 上運行,都需要透過代理伺服器存取安裝套件。 根據您的作業系統,按照以下步驟通過代理伺服器安裝 IoT Edge 運行時。

### <a name="linux-devices"></a>Linux 裝置

若您正於 Linux 裝置上安裝 IoT Edge 執行階段，請設定套件管理員以 Proxy 伺服器存取安裝套件。 例如，[設定 apt-get 以使用 http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy) \(英文\)。 配置包管理員後,請像往常一樣按照[Linux 上安裝 Azure IoT 邊緣運行時](how-to-install-iot-edge-linux.md)的說明進行操作。

### <a name="windows-devices"></a>Windows 裝置

如果要在 Windows 設備上安裝 IoT Edge 運行時,則需要透過代理伺服器兩次。 第一個連接下載安裝程式文本檔,第二個連接是在安裝過程中下載必要的元件。 您可以在 Windows 設定中設定代理資訊,也可以直接在 PowerShell 命令中包括代理資訊。

以下步驟展示了參數`-proxy`安裝視窗的範例:

1. 呼叫 WebRequest 命令需要代理資訊才能存取安裝程式文稿。 然後,Deploy-IoTEdge 命令需要代理資訊來下載安裝檔。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. 初始化-IoTEdge 命令不需要透過代理伺服器,因此第二步只需要 Invoke-Web 請求的代理資訊。

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

如果您的 Proxy 伺服器有複雜的認證而無法包含在 URL 中，請在 `-InvokeWebRequestParameters` 中使用 `-ProxyCredential` 參數。 例如，

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

如需 Proxy 參數的詳細資訊，請參閱 [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest)。 有關 Windows 安裝選項(包括離線安裝)的詳細資訊,請參閱[在 Windows 上安裝 Azure IoT 邊緣執行時](how-to-install-iot-edge-windows.md)。

## <a name="configure-the-daemons"></a>設定精靈

IoT Edge 依賴於在 IoT Edge 設備上運行的兩個守護程式。 Moby 守護行程發出 Web 請求,從容器註冊錶中提取容器映射。 IoT Edge 精靈會發出 Web 要求，以和 IoT 中樞通訊。

Moby 和 IoT Edge 守護程序都需要設定為使用代理伺服器來進行正在進行的裝置功能。 在初始設備設置期間,此步驟在IoT邊緣設備上執行。

### <a name="moby-daemon"></a>莫比守護神

由於 Moby 構建在 Docker 上,請參閱 Docker 文件以使用環境變數配置 Moby 守護程式。 大部分的容器登錄 (包括 Docker Hub 和 Azure Container Registry) 皆支援 HTTPS 要求，因此您應該設定的參數為 **HTTPS_PROXY**。 如果您是從不支援傳輸層安全性 (TLS) 的登錄提取映像，則應該設定 **HTTP_PROXY** 參數。

選擇適用於 IoT Edge 裝置作業系統的文章:

* [Linux 上設定 Docker 守護程式](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)Linux 裝置上的 Moby 守護行程保留 Docker 的名稱。
* [在 Windows 上設定 Docker 守護程式](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)Windows 設備上的 Moby 守護行程稱為 iotedge-moby。 名稱不同,因為可以在 Windows 設備上並行運行 Docker Desktop 和 Moby。

### <a name="iot-edge-daemon"></a>IoT Edge 精靈

IoT 邊緣守護程式配置方式與 Moby 守護程序類似。 請根據您的作業系統，使用下列相對應的步驟來針對服務設定環境變數。

IoT 邊緣守護程式始終使用 HTTPS 向 IoT 中心發送請求。

#### <a name="linux"></a>Linux

在終端機中開啟編輯器以設定 IoT 精靈。

```bash
sudo systemctl edit iotedge
```

輸入以下文字,將**\<代理 URL>** 替換為代理伺服器位址和埠。 然後儲存並結束。

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

以系統管理員身分開啟 PowerShell 視窗，並執行下列命令以搭配新的環境變數編輯登錄。 將**\<代理>** 替換為代理伺服器位址和埠。

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

重新啟動 IoT Edge 來讓變更生效。

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>設定 IoT Edge 代理程式

IoT Edge 代理程式是在任何 IoT Edge 裝置上皆應第一個啟動的模組。 它會根據 IoT Edge config.yaml 檔案中的資訊首次啟動。 IoT Edge 代理程式接著會連線至 IoT 中樞以擷取部署資訊清單，該資訊清單會宣告有哪些其他模組應部署於裝置上。

在初始設備設置期間,此步驟在IoT邊緣設備上執行一次。

1. 在您的 IoT Edge 裝置上開啟 config.yaml 檔案。 在 Linux 系統上，此檔案位於 **/etc/iotedge/config.yaml**。 在 Windows 系統上，此檔案位於 **C:\ProgramData\iotedge\config.yaml**。 該設定檔是受保護的，因此您需要系統管理權限以開啟它。 在 Linux 系統`sudo`上, 在首選文字編輯器中打開檔之前,請使用該命令。 在 Windows 上,打開像記事本這樣的文字編輯器作為管理員,然後打開該檔。

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

將 IoT Edge 裝置配置為與代理伺服器配合使用後,需要在將來的部署清單中繼續聲明HTTPS_PROXY環境變數。 您可以使用 Azure 門戶向導或透過編輯部署清單 JSON 檔編輯部署清單。

請一律設定兩個執行階段模組 (edgeAgent 和 edgeHub) 透過 Proxy 伺服器進行通訊，以維持與 IoT 中樞的連線。 如果從 edgeAgent 模組中刪除代理資訊,則重新建立連接的唯一方法是編輯設備上的 config.yaml 檔,如上一節所述。

除了邊緣代理和邊緣Hub模組外,其他模組可能需要代理配置。 這些是需要存取IoT中心之外的Azure資源的模組,如blob儲存,並且必須在部署清單檔中為該模組指定HTTPS_PROXY變數。

以下過程適用於 IoT 邊緣設備的整個生命週期。

### <a name="azure-portal"></a>Azure 入口網站

當您使用 [設定模組]**** 精靈來針對 IoT Edge 裝置建立部署時，每個模組都會有 [環境變數]**** 區段可供您用來設定 Proxy 伺服器連線。

要配置 IoT 邊緣代理和 IoT Edge 中心模組,請在嚮導的第一步中選擇 **「執行時設定**」。

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
