---
title: 如何安裝及執行空間分析容器-電腦視覺
titleSuffix: Azure Cognitive Services
description: 空間分析容器可讓您偵測人員和距離。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: aahi
ms.openlocfilehash: f41e513ee0f2755c446a9cb95465c1f636fe5a7a
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606261"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>安裝並執行空間分析容器 (預覽版) 

空間分析容器可讓您分析即時串流影片，以瞭解人員之間的空間關聯性、其移動和實體環境中物件的互動。 容器非常適合用於特定的安全性和資料控管需求。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="建立電腦視覺資源"  target="_blank">建立電腦視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您將需要您所建立資源的金鑰和端點，以執行空間分析容器。 您稍後將會使用您的金鑰和端點。


### <a name="spatial-analysis-container-requirements"></a>空間分析容器需求

若要執行空間分析容器，您需要具有 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/)的計算裝置。 建議您搭配使用 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) 與 GPU 加速，不過，容器會在任何其他符合最低需求的桌上型電腦上執行。 我們會將此裝置稱為主機電腦。

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 裝置](#tab/azure-stack-edge)

Azure Stack Edge 是具有網路資料傳輸功能的硬體即服務解決方案，以及具備 AI 功能的邊緣計算裝置。 如需詳細的準備和設定指示，請參閱 [Azure Stack Edge 檔](../../databox-online/azure-stack-edge-deploy-prep.md)。

#### <a name="desktop-machine"></a>[桌上型電腦](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>最小硬體需求

* 4 GB 系統 RAM
* 4 GB 的 GPU RAM
* 8核心 CPU
* 1個 NVIDIA Tesla T4 GPU
* 20 GB 的 HDD 空間

#### <a name="recommended-hardware"></a>建議的硬體

* 32 GB 系統 RAM
* 16 GB 的 GPU RAM
* 8核心 CPU
* 2個 NVIDIA Tesla T4 Gpu
* 50 GB 的 SSD 空間

在本文中，您將下載並安裝下列軟體套件。 主機電腦必須能夠執行下列 (請參閱下面的指示) ：

* [Nvidia 圖形驅動程式](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)和[nvidia CUDA 工具](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)組
* [NVIDIA mp](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (多進程服務) 的設定。
* [DOCKER CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) 和 [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 執行時間。

---

| 需求 | 描述 |
|--|--|
| 相機 | 空間分析容器未系結至特定的相機品牌。 攝影機裝置必須：支援 Real-Time 串流通訊協定 (RTSP) 和 h.264 編碼，可供主機電腦存取，而且能夠以15FPS 和1080p 解析度進行串流處理。 |
| Linux 作業系統 | 您必須在主機電腦上安裝[Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) 。  |


## <a name="request-approval-to-run-the-container"></a>要求核准以執行容器

填寫並提交 [要求表單](https://aka.ms/csgate) ，以要求核准以執行容器。

該表格需要有關您本身、您的公司，以及您將會使用該容器之使用者情節的資訊。 在您提交表單之後，Azure 認知服務小組將會進行審查，並以電子郵件傳送您的決策。

> [!IMPORTANT]
> * 在表單上，您必須使用與 Azure 訂用帳戶識別碼相關聯的電子郵件地址。
> * 您用來執行容器的電腦視覺資源，必須使用已核准的 Azure 訂用帳戶識別碼來建立。

經過核准之後，您將能夠在從 Microsoft Container Registry (MCR) 下載容器之後執行該容器，如本文稍後所述。

如果您的 Azure 訂用帳戶尚未核准，您將無法執行容器。

## <a name="set-up-the-host-computer"></a>設定主機電腦

建議您針對主機電腦使用 Azure Stack Edge 裝置。 如果您正在設定不同的裝置，請按一下 [ **桌上型電腦** ]。

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 裝置](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>在 Azure Stack Edge 入口網站上設定計算 
 
空間分析會使用 Azure Stack Edge 的計算功能來執行 AI 解決方案。 若要啟用計算功能，請確定： 

* 您已 [連線並啟用](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) 您的 Azure Stack Edge 裝置。 
* 您有一個執行 PowerShell 5.0 或更新版本的 Windows 用戶端系統來存取裝置。  
* 若要部署 Kubernetes 叢集，您必須透過 [Azure 入口網站](https://portal.azure.com/)上的 **本機 UI** 來設定您的 Azure Stack Edge 裝置： 
  1. 在您的 Azure Stack Edge 裝置上啟用計算功能。 若要啟用計算，請移至您裝置的 web 介面中的 [ **計算** ] 頁面。 
  2. 選取您要為計算啟用的網路介面，然後按一下 [ **啟用**]。 這會在您的裝置上，于該網路介面上建立虛擬交換器。
  3. 將 [Kubernetes test node IP 位址] 和 [Kubernetes external services IP 位址] 保留空白。
  4. 按一下 [套用]  。 這種作業可能需要大約兩分鐘的時間。 

![設定計算](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>設定 Edge 計算角色並建立 IoT 中樞資源

在 [Azure 入口網站](https://portal.azure.com/)中，流覽至您的 Azure Stack Edge 資源。 在 [ **總覽** ] 頁面或導覽清單中，按一下 [ **Edge 計算開始** 使用] 按鈕。 在 [ **設定 Edge 計算**]   磚中，按一下 [**設定**]。 

![連結](media/spatial-analysis/configure-edge-compute-tile.png)

在 [ **設定 Edge 計算**]   頁面中，選擇現有的 IoT 中樞，或選擇建立一個新的。 根據預設，會使用標準 (S1) 定價層來建立 IoT 中樞資源。 若要使用免費層 IoT 中樞資源，請建立一個，然後選取它。 IoT 中樞資源使用 Azure Stack Edge 資源所使用的相同訂用帳戶和資源群組 

按一下 [建立]  。 建立 IoT 中樞資源可能需要幾分鐘的時間。 建立 IoT 中樞資源之後，[ **設定 Edge 計算** ] 圖格將會更新以顯示新的設定。 若要確認已設定 Edge 計算角色，請選取 [ **設定計算**] 磚上的 [ **View config** ]   。

在 Edge 裝置上設定 Edge 計算角色時，其會建立兩個裝置：一個 IoT 裝置和一個 IoT Edge 裝置。 這兩個裝置都可以在 IoT 中樞資源中檢視。 Azure IoT Edge 執行時間將已在 IoT Edge 裝置上執行。

> [!NOTE]
> * 目前只有 Linux 平臺支援 IoT Edge 裝置。 如需針對 Azure Stack Edge 裝置進行疑難排解的協助，請參閱 [記錄和疑難排解](spatial-analysis-logging.md) 文章。
> * 若要深入瞭解如何設定 IoT Edge 裝置以透過 proxy 伺服器進行通訊，請參閱 [設定 IoT Edge 裝置以透過 proxy 伺服器進行通訊](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>在 Azure Stack Edge 上啟用 MP 

1. 以系統管理員身分執行 Windows PowerShell 會話。 

2. 確定您的用戶端上正在執行 Windows 遠端管理服務。 在 PowerShell 終端機中，使用下列命令 
    
    ```powershell
    winrm quickconfig
    ```
    
    如果您看到有關防火牆例外狀況的警告，請檢查您的網路連線類型，並查看 [Windows 遠端管理](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management) 檔。

3. 將變數指派給裝置 IP 位址。 
    
    ```powershell
    $ip = "" Replace with the IP address of your device. 
    ```
    
4. 若要將裝置的 IP 位址新增至用戶端的信任主機清單，請使用下列命令： 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. 在裝置上啟動 Windows PowerShell 會話。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. 出現提示時，請提供密碼。 使用用來登入本機 web UI 的相同密碼。 預設的本機 web UI 密碼為 `Password1` 。

輸入 `Start-HcsGpuMPS` 以啟動裝置上的 mp 服務。 

如需針對 Azure Stack Edge 裝置進行疑難排解的協助，請參閱 [Azure Stack Edge 裝置疑難排解](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[桌上型電腦](#tab/desktop-machine)

如果您的主機電腦不是 Azure Stack Edge 裝置，請遵循下列指示。

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>在主機電腦上安裝 NVIDIA CUDA 工具組和 Nvidia graphics 驅動程式

使用下列 bash 腳本來安裝必要的 Nvidia 圖形驅動程式和 CUDA 工具組。

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

重新開機電腦，然後執行下列命令。

```bash
nvidia-smi
```

您應該會看見下列輸出。

![NVIDIA 驅動程式輸出](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>在主機電腦上安裝 Docker CE 和 nvidia-docker2

在主機電腦上安裝 Docker CE。

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

安裝 *nvidia-docker-2* 軟體套件。

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>在主機電腦上啟用 NVIDIA MP

> [!TIP]
> * 如果您的 GPU 計算功能小於 7.x (預先 Volta) ，請勿安裝 MP。 請參閱 [CUDA 相容](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title) 性以取得參考。 
> * 從主機電腦上的終端機視窗執行 MP 指令。 不在您的 Docker 容器實例內。

為了達到最佳效能和使用率，請設定主機電腦的 GPU (s) 適用于 NVIDIA 可執行 [服務 (mp) ](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)。 從主機電腦上的終端機視窗執行 MP 指令。

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>設定主機電腦上的 Azure IoT Edge

若要在主機電腦上部署空間分析容器，請使用標準 (S1) 或免費 (F0) 定價層來建立 [Azure IoT 中樞](../../iot-hub/iot-hub-create-through-portal.md) 服務的實例。 如果您的主機電腦是 Azure Stack Edge，請使用 Azure Stack Edge 資源所使用的相同訂用帳戶和資源群組。

使用 Azure CLI 建立 Azure IoT 中樞的實例。 將參數取代為適當的。 或者，您也可以在 [Azure 入口網站](https://portal.azure.com/)上建立 Azure IoT 中樞。

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login
az account set --subscription <name or ID of Azure Subscription>
az group create --name "test-resource-group" --location "WestUS"

az iot hub create --name "test-iot-hub-123" --sku S1 --resource-group "test-resource-group"

az iot hub device-identity create --hub-name "test-iot-hub-123" --device-id "my-edge-device" --edge-enabled
```

如果主機電腦不是 Azure Stack Edge 裝置，您將需要安裝 [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 版本1.0.9。 遵循下列步驟下載正確的版本：

Ubuntu Server 18.04：
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

複製產生的清單。
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

安裝 Microsoft GPG 公開金鑰。

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

更新您裝置上的套件清單。

```bash
sudo apt-get update
```

安裝1.0.9 版本：

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

接下來，使用 [連接字串](../../iot-edge/how-to-manual-provision-symmetric-key.md?view=iotedge-2018-06)，在您的 IoT 中樞實例中將主機電腦註冊為 IoT Edge 裝置。

您必須將 IoT Edge 裝置連線到您的 Azure IoT 中樞。 您必須從您稍早建立的 IoT Edge 裝置複製連接字串。 或者，您可以在 Azure CLI 中執行下列命令。

```bash
az iot hub device-identity show-connection-string --device-id my-edge-device --hub-name test-iot-hub-123
```

在主機電腦上開啟  `/etc/iotedge/config.yaml` 以供編輯。 取代 `ADD DEVICE CONNECTION STRING HERE` 為連接字串。 儲存並關閉檔案。 執行此命令，以重新開機主機電腦上的 IoT Edge 服務。

```bash
sudo systemctl restart iotedge
```

您可以從 [Azure 入口網站](../../iot-edge/how-to-deploy-modules-portal.md) 或 [Azure CLI](../../iot-edge/how-to-deploy-modules-cli.md)，將空間分析容器部署為主電腦上的 IoT 模組。 如果您使用的是入口網站，請將映射 URI 設定為 Azure Container Registry 的位置。 

使用下列步驟，使用 Azure CLI 部署容器。

---

### <a name="iot-deployment-manifest"></a>IoT 部署資訊清單

若要簡化多部主機電腦上的容器部署，您可以建立部署資訊清單檔來指定容器的建立選項和環境變數。 您可以在 Github 上找到 [Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179) 和  [其他桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 的部署資訊清單範例。

下表顯示 IoT Edge 模組所使用的各種環境變數。 您也可以使用中的屬性，在上述連結的部署資訊清單中設定它們 `env` `spatialanalysis` ：

| 設定名稱 | 值 | 描述|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | 更多資訊詳細 | 記錄層級，請選取兩個值的其中一個|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | 不要修改|
| ARCHON_PERF_MARKER| false| 將此值設定為 true 以進行效能記錄，否則為 false。| 
| ARCHON_NODES_LOG_LEVEL | 更多資訊詳細 | 記錄層級，請選取兩個值的其中一個|
| OMP_WAIT_POLICY | 被動 | 不要修改|
| QT_X11_NO_MITSHM | 1 | 不要修改|
| API_KEY | 您的 API 金鑰| 從電腦視覺資源的 Azure 入口網站收集此值。 您可以在資源的 [ **金鑰和端點** ] 區段中找到它。 |
| BILLING_ENDPOINT | 您的端點 URI| 從電腦視覺資源的 Azure 入口網站收集此值。 您可以在資源的 [ **金鑰和端點** ] 區段中找到它。|
| 使用者授權合約 | accept | 此值必須設定為 [ *接受* ]，容器才能執行 |
| DISPLAY | ：1 | 此值必須與主機電腦上的輸出相同 `echo $DISPLAY` 。 Azure Stack Edge 裝置沒有顯示。 這項設定不適用|


> [!IMPORTANT]
> 必須指定 `Eula`、`Billing` 及 `ApiKey` 選項以執行容器，否則容器將不會啟動。  如需詳細資訊，請參閱[帳單](#billing)。

當您使用自己的設定和作業選取來更新 [Azure Stack Edge 裝置](https://go.microsoft.com/fwlink/?linkid=2142179) 或 [桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) 的部署資訊清單之後，您可以使用下列 [Azure CLI](../../iot-edge/how-to-deploy-modules-cli.md) 命令，將容器部署在主機電腦上，作為 IoT Edge 模組。

```azurecli
az login
az extension add --name azure-iot
az iot edge set-modules --hub-name "<IoT Hub name>" --device-id "<IoT Edge device name>" --content DeploymentManifest.json --subscription "<subscriptionId>"
```

|參數  |描述  |
|---------|---------|
| `--hub-name` | 您的 Azure IoT 中樞名稱。 |
| `--content` | 部署檔案的名稱。 |
| `--target-condition` | 主機電腦的 IoT Edge 裝置名稱。 |
| `-–subscription` | 訂用帳戶識別碼或名稱。 |

此命令會開始部署。 流覽至 Azure 入口網站中 Azure IoT 中樞實例的頁面，以查看部署狀態。 狀態可能會顯示為 *417-裝置的部署設定* 在裝置完成下載容器映射並開始執行之前未設定。

## <a name="validate-that-the-deployment-is-successful"></a>驗證部署是否成功

有數種方式可驗證容器正在執行。 在 Azure 入口網站上 Azure IoT 中樞實例中空間分析模組的 **IoT Edge 模組設定** 中找出 *執行時間狀態*。 驗證 *執行時間狀態* 所 **需的值** 和 **回報值***是否正在* 執行。

![部署驗證範例](./media/spatial-analysis/deployment-verification.png)

一旦部署完成且容器正在執行， **主機電腦** 就會開始將事件傳送至 Azure IoT 中樞。 如果您使用的是此 `.debug` 版本的作業，您會看到您在部署資訊清單中設定的每個相機的視覺化視窗。 您現在可以在部署資訊清單中定義您想要監視的行和區域，並遵循指示重新部署。 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>設定空間分析所執行的作業

您將需要使用 [空間分析作業](spatial-analysis-operations.md) ，將容器設定為使用連接攝影機、設定作業等等。 針對您設定的每個相機裝置，空間分析的作業會產生 JSON 訊息的輸出資料流程，並傳送至您的 Azure IoT 中樞實例。

## <a name="redeploy-or-delete-the-deployment"></a>重新部署或刪除部署

如果您需要更新部署，您必須確定已成功部署先前的部署，或您需要刪除未完成的 IoT Edge 裝置部署。 否則，這些部署會繼續進行，讓系統處於不良狀態。 您可以使用 Azure 入口網站或 [Azure CLI](/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment)。

## <a name="use-the-output-generated-by-the-container"></a>使用容器所產生的輸出

如果您想要開始使用容器所產生的輸出，請參閱下列文章：

*   針對您所選擇的程式設計語言使用 Azure 事件中樞 SDK，以連接到 Azure IoT 中樞端點並接收事件。 如需詳細資訊，請參閱 [從內建端點讀取裝置到雲端訊息](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) 。 
*   在您的 Azure IoT 中樞上設定訊息路由，以將事件傳送至其他端點，或將事件儲存至 Azure Blob 儲存體等等。如需詳細資訊，請參閱 [IoT 中樞訊息路由](../../iot-hub/iot-hub-devguide-messages-d2c.md) 。 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>使用錄製的影片檔案執行空間分析

您可以使用已錄製或即時影片的空間分析。 若要針對錄製的影片使用空間分析，請嘗試錄製影片檔案，並將它儲存為有空間的檔案。 在 Azure 中建立 blob 儲存體帳戶，或使用現有的帳戶。 然後，更新 Azure 入口網站中的下列 blob 儲存體設定：
    1. 變更 **停用****所需的安全傳輸**
    2. 變更 **允許 Blob 公用存取****啟用**

流覽至 [ **容器** ] 區段，並建立新的容器或使用現有的容器。 然後將影片檔案上傳至容器。 展開所上傳檔案的檔案設定，然後選取 [ **產生 SAS**]。 請務必將 **到期日** 設定為足夠的時間，以涵蓋測試期間。 ) 不支援將 **允許的通訊協定** 設定為 *HTTP* (*HTTPS* 。

按一下 [ **產生 SAS 權杖和 url** ]，然後複製 BLOB SAS URL。 取代開頭 `https` 為的 `http` ，並在支援影片播放的瀏覽器中測試 URL。

`VIDEO_URL`在您的[Azure Stack Edge 裝置](https://go.microsoft.com/fwlink/?linkid=2142179)的部署資訊清單中，或使用您為其建立之 URL 的其他[桌上型電腦](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json)，取代為所有圖形。 將設定 `VIDEO_IS_LIVE` 為 `false` ，並使用更新的資訊清單重新部署空間分析容器。 請參閱下方的範例。

空間分析模組會開始取用影片檔案，而且也會持續自動重新執行。


```json
"zonecrossing": {
  "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
  "version": 1,
  "enabled": true,
  "parameters": {
      "VIDEO_URL": "Replace http url here",
      "VIDEO_SOURCE_ID": "personcountgraph",
      "VIDEO_IS_LIVE": false,
        "VIDEO_DECODE_GPU_INDEX": 0,
      "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0 }",
      "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"threshold\":35.0}]}"
    }
  },

```

## <a name="troubleshooting"></a>疑難排解

如果您在啟動或執行容器時遇到問題，請參閱 [遙測和疑難排解](spatial-analysis-logging.md) 常見問題的步驟。 本文也包含產生和收集記錄檔以及收集系統健康情況的資訊。

## <a name="billing"></a>計費

空間分析容器會使用您 Azure 帳戶上的電腦視覺資源，將計費資訊傳送至 Azure。 在公開預覽中使用空間分析目前免費。 

Azure 認知服務容器不會授權執行，而不會連線到計量/帳單端點。 您必須讓容器隨時都能與計量端點進行帳單資訊的通訊。 認知服務容器不會將客戶資料（例如正在分析的影片或影像）傳送給 Microsoft。


## <a name="summary"></a>摘要

在本文中，您已瞭解下載、安裝及執行空間分析容器的概念和工作流程。 摘要說明：

* 空間分析是適用于 Docker 的 Linux 容器。
* 您可以從 Microsoft Container Registry 下載容器映射。
* 容器映射在 Azure IoT Edge 中會以 IoT 模組的形式執行。
* 如何設定容器並將其部署在主機電腦上。

## <a name="next-steps"></a>後續步驟

* [部署人員計數 web 應用程式](spatial-analysis-web-app.md)
* [設定空間分析作業](spatial-analysis-operations.md)
* [記錄和疑難排解](spatial-analysis-logging.md)
* [攝影機放置指南](spatial-analysis-camera-placement.md)
* [區域和行放置指南](spatial-analysis-zone-line-placement.md)
