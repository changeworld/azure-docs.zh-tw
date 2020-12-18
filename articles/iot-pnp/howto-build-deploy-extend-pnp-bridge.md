---
title: 如何建立、部署及擴充 IoT 隨插即用 bridge |Microsoft Docs
description: 識別 IoT 隨插即用 bridge 元件。 瞭解如何擴充橋接器，以及如何在 IoT 裝置、閘道和 IoT Edge 模組上執行。
author: usivagna
ms.author: ugans
ms.date: 12/11/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ece9f62e64eb64b1f34af46b42d57ec583f8f214
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675834"
---
# <a name="build-deploy-and-extend-the-iot-plug-and-play-bridge"></a>建立、部署及擴充 IoT 隨插即用橋接器

IoT 隨插即用橋接器可讓您將連接至閘道的現有裝置連線到 IoT 中樞。 您可以使用橋接器將 IoT 隨插即用介面對應到連接的裝置。 IoT 隨插即用介面會定義裝置所傳送的遙測、裝置與雲端之間的屬性同步，以及裝置所回應的命令。 您可以在 Windows 或 Linux 閘道上安裝和設定開放原始碼橋接器應用程式。

本文詳細說明如何：

- 設定橋接器。
- 藉由建立新的介面卡來擴充橋接器。
- 如何在各種環境中建立並執行橋接器。

如需示範如何使用橋接器的簡單範例，請參閱 [如何將在 Linux 或 Windows 上執行的 IoT 隨插即用 bridge 範例連線到 IoT 中樞](howto-use-iot-pnp-bridge.md)。

本文中的指引和範例假設您對 [Azure 數位 Twins](../digital-twins/overview.md) 和 [IoT 隨插即用](overview-iot-plug-and-play.md)有基本的熟悉度。

## <a name="general-prerequisites"></a>一般先決條件

### <a name="supported-os-platforms"></a>支援的 OS 平臺

支援的作業系統平臺和版本如下：

|平台  |支援的版本  |
|---------|---------|
|Windows 10 |     所有 Windows Sku 都受到支援。 例如： IoT Enterprise、Server、Desktop、IoT Core。 *針對相機健康情況監視功能，建議使用20H1 或更新版本的組建。所有其他功能都可在所有 Windows 10 組建上取得。*  |
|Linux     |在 Ubuntu 18.04 上測試及支援，其他散發套件上的功能尚未經過測試。         |
||

### <a name="hardware"></a>硬體

- 任何支援上述作業系統 Sku 和版本的硬體平臺。
- 以原生方式支援序列、USB、藍牙和相機週邊設備和感應器。 您可以擴充 IoT 隨插即用橋接器，以支援任何自訂週邊或感應器。

### <a name="azure-iot-products-and-tools"></a>Azure IoT 產品和工具

- **Azure IoT 中樞** -您需要 azure 訂用帳戶中的 [azure IoT 中樞](../iot-hub/index.yml) ，才能將您的裝置連線至。 如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。 如果您沒有 IoT 中樞，請 [依照下列指示建立一個](../iot-hub/iot-hub-create-using-cli.md)。 IoT 隨插即用支援不包含在基本層 IoT 中樞內。
- **Azure iot explorer** -若要與您的 IoT 隨插即用裝置互動，您可以使用 azure iot explorer 工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。 設定 Azure IoT explorer 工具以連線到您的 IoT 中樞，並在您所複製的 **iot 隨插即用橋接器** 的 *pnpbridge\docs\schemas* 資料夾中尋找模型定義。

## <a name="configure-a-bridge"></a>設定橋接器

有兩種方式可以設定橋接器：

- 如果橋接器是在 IoT 裝置或閘道上以原生方式執行，請使用設定檔案。 此案例可以使用 Linux 或 Windows 電腦。
- 如果橋接器在 IoT Edge 執行時間中以 IoT Edge 模組的形式執行，請使用模組對應項所需屬性。 此案例假設 IoT Edge 執行時間裝載于 Linux 環境中。

### <a name="configuration-file"></a>組態檔

當隨插即用橋接器在 IoT 裝置或閘道上以原生方式執行時，它會使用設定檔來：

- 取得 IoT Central 或 IoT 中樞連接資訊。
- 設定用來發佈 IoT 隨插即用介面的裝置。

您可以使用下列其中一個選項，將設定檔提供給橋接器：

- 將設定檔的路徑以命令列參數的形式傳遞至橋接器可執行檔。
- 使用 *pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* 資料夾中的現有 *config.json* file。

[設定檔架構](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/pnpbridge/src/pnpbridge_config_schema.json)可在 GitHub 存放庫中取得。

> [!TIP]
> 如果您在 VS Code 中編輯橋接器設定檔，您可以使用此架構檔案來驗證您的檔案。

### <a name="iot-edge-module-configuration"></a>IoT Edge 模組設定

當橋接器在 IoT Edge 執行時間以 IoT Edge 模組的形式執行時，會從雲端傳送設定檔以作為 `PnpBridgeConfig` 所需屬性的更新。 橋接器會在設定介面卡和元件之前，先等候這個屬性更新。

## <a name="extend-the-bridge"></a>擴充橋接器

若要擴充橋接器的功能，您可以撰寫自己的橋接器介面卡。

橋接器使用介面卡來：

- 建立裝置與雲端之間的連接。
- 啟用裝置與雲端之間的資料流程。
- 從雲端啟用裝置管理。

每個橋接器介面卡都必須：

- 建立數位 twins 介面。
- 使用介面將裝置端功能系結至雲端式功能，例如遙測、屬性和命令。
- 使用裝置硬體或固件來建立控制和資料通訊。

每個橋接器介面卡都會根據介面卡連線到裝置的方式與裝置互動，與特定類型的裝置互動。 即使與裝置的通訊使用信號交換通訊協定，橋接器介面卡還是可以有多種方式來解讀裝置中的資料。 在此案例中，橋接器介面卡會使用設定檔案中的介面卡資訊，來判斷介面卡應用來剖析資料的 *介面* 設定。

若要與裝置互動，橋接器介面卡會使用裝置所支援的通訊協定，以及基礎作業系統或裝置廠商所提供的 Api。

為了與雲端互動，橋接器介面卡會使用 Azure IoT 裝置 C SDK 提供的 Api 來傳送遙測、建立數位對應項介面、傳送屬性更新，以及建立屬性更新和命令的回呼函數。

### <a name="create-a-bridge-adapter"></a>建立橋接器介面卡

橋接器需要橋接器介面卡來執行 [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) 介面中定義的 api：

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

在此介面中：

- `PNPBRIDGE_ADAPTER_CREATE` 建立介面卡，並設定介面管理資源。 介面卡也可能依賴全域介面卡參數來建立介面卡。 此函式會針對單一介面卡呼叫一次。
- `PNPBRIDGE_COMPONENT_CREATE` 建立數位對應項用戶端介面，並系結回呼函數。 介面卡會起始裝置的通道。 介面卡可設定資源以啟用遙測流程，但在呼叫之前，不會開始報告遙測 `PNPBRIDGE_COMPONENT_START` 。 此函式會針對設定檔中的每個介面元件呼叫一次。
- `PNPBRIDGE_COMPONENT_START` 呼叫以讓橋接器介面卡開始將遙測從裝置轉送到數位對應項用戶端。 此函式會針對設定檔中的每個介面元件呼叫一次。
- `PNPBRIDGE_COMPONENT_STOP` 停止遙測流程。
- `PNPBRIDGE_COMPONENT_DESTROY` 終結數位對應項用戶端和相關聯的介面資源。 當橋接器中斷或發生嚴重錯誤時，設定檔中的每個介面元件都會呼叫此函式一次。
- `PNPBRIDGE_ADAPTER_DESTROY` 清除橋接器介面卡資源。

### <a name="bridge-core-interaction-with-bridge-adapters"></a>使用橋接器介面卡橋接核心互動

下列清單概述橋接器啟動時會發生什麼事：

1. 橋接器開始時，橋接器介面卡管理員會查看設定檔中定義的每個介面元件，並 `PNPBRIDGE_ADAPTER_CREATE` 在適當的介面卡上呼叫。 介面卡可能會使用全域介面卡設定參數來設定資源，以支援各種 *介面* 設定。
1. 針對設定檔中的每個裝置，橋接器管理員會 `PNPBRIDGE_COMPONENT_CREATE` 在適當的橋接器介面卡中呼叫，以起始介面建立。
1. 介面卡會接收介面元件的任何選用介面卡設定，並使用此資訊來設定裝置的連線。
1. 介面卡會建立數位對應項用戶端介面，並系結屬性更新和命令的回呼函數。 建立裝置連線時，不應該在數位對應項介面建立成功之後封鎖回呼的回呼。 作用中的裝置連線與橋接器所建立的 active interface 用戶端無關。 如果連接失敗，介面卡會假設裝置處於非使用中狀態。 橋接器介面卡可以選擇重試進行此連接。
1. 在橋接器介面卡管理員建立設定檔中指定的所有介面元件之後，它會向 Azure IoT 中樞註冊所有介面。 註冊是封鎖的非同步呼叫。 當呼叫完成時，它會觸發橋接器介面卡中的回呼，然後開始處理雲端中的屬性和命令回呼。
1. 橋接器介面卡管理員接著會 `PNPBRIDGE_INTERFACE_START` 在每個元件上呼叫，且橋接器介面卡會開始向數位對應項用戶端報告遙測。

### <a name="design-guidelines"></a>設計指導方針

當您開發新的橋接器介面卡時，請遵循下列指導方針：

- 判斷支援哪些裝置功能，以及使用此介面卡的元件介面定義看起來會像這樣。
- 判斷介面卡需要在設定檔中定義的介面和全域參數。
- 識別支援元件屬性和命令所需的低層級裝置通訊。
- 判斷介面卡如何剖析裝置中的原始資料，並將它轉換成 IoT 隨插即用介面定義所指定的遙測類型。
- 執行先前所述的橋接器介面卡介面。
- 將新的介面卡新增至介面卡資訊清單，並建立橋接器。

### <a name="enable-a-new-bridge-adapter"></a>啟用新的橋接器介面卡

您可以在 [adapter_manifest](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c)中新增參考，藉以啟用橋接器中的介面卡：

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> 系統會依序叫用橋接器介面卡回呼。 介面卡不應封鎖回呼，因為這可防止橋接器核心進行進度。

### <a name="sample-camera-adapter"></a>範例攝影機介面卡

[攝影機介面卡讀我檔案](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md)說明您可以啟用的範例攝影機介面卡。

## <a name="build-and-run-the-bridge-on-an-iot-device-or-gateway"></a>在 IoT 裝置或閘道上建立並執行橋接器

| 平台 | 支援 |
| :-----------: | :-----------: |
| Windows |  是 |
| Linux | 是 |

### <a name="prerequisites"></a>必要條件

若要完成本節，您需要在本機電腦上安裝下列軟體：

- 支援編譯 c + + 的開發環境，例如 [Visual Studio (的社區、專業或企業)](https://visualstudio.microsoft.com/downloads/) -請確定您在安裝 Visual Studio 時，包含 NuGet 套件管理員元件和 **使用 c + + 的桌面開發** 工作負載。
- [CMake](https://cmake.org/download/) -當您安裝 CMake 時，請選取 [ **將 CMake 新增至系統路徑**] 選項。
- 如果您是在 Windows 上建立的，您也必須下載 [windows 17763 SDK](https://developer.microsoft.com/windows/downloads/windows-10-sdk)。

### <a name="source-code"></a>原始程式碼

將 [IoT 隨插即用橋接器](https://github.com/Azure/iot-plug-and-play-bridge) 儲存機制複製到您的本機電腦：

```cmd/sh
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

預期先前的命令需要幾分鐘的時間來執行。

> [!NOTE]
> 如果您遇到 Windows 失敗的問題 `git submodule update` ，請嘗試下列命令來解決此問題： `git config --system core.longpaths true` 。

### <a name="build-the-bridge-on-windows"></a>在 Windows 上建立橋接器

開啟 **VS 2019 的開發人員命令提示字元** ，然後流覽至包含您所複製之存放庫的資料夾，然後執行下列命令：

```cmd
cd pnpbridge\scripts\windows

build.cmd
```

預期先前的命令需要幾分鐘的時間來執行。

（選擇性）您可以在 Visual Studio 中開啟產生的 *pnpbridge\cmake\ pnpbridge_x86 \ azure_iot_pnp_bridge .sln* 方案檔，以編輯、重建和偵錯工具代碼。

> [!TIP]
> 此專案會使用 CMAKE 來產生專案檔。 如果未更新適當的 CMAKE 檔案，您對 Visual Studio 中的專案所做的任何修改都可能會遺失。

### <a name="build-the-bridge-on-linux"></a>在 Linux 上建立橋接器

使用 bash shell，流覽至包含您所複製之存放庫的資料夾，然後執行下列命令：

```bash
cd scripts/linux

./setup.sh

./build.sh
```

### <a name="configure-the-generic-sensors"></a>設定一般感應器

在 `pnp_bridge_connection_parameters` windows 的 *iot-plug-and-play-bridge\pnpbridge\cmake\ pnpbridge_x86 \src\pnpbridge\samples\console* 資料夾或 windows 中的 [ *iot-隨插即用-bridge/pnpbridge/cmake/pnpbridge_linux \ src/pnpbridge/samples/主控台*] 資料夾中，修改位於 *config.js* 之節點下的下列參數：

如果您使用連接字串來連線到 IoT 中樞：

```JSON
{
  "connection_parameters": {
    "connection_type" : "connection_string",
    "connection_string" : "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "root_interface_model_id": "[To fill in]",
    "auth_parameters": {
      "auth_type": "symmetric_key",
      "symmetric_key": "[To fill in]"
    }
  }
}
```

> [!TIP]
> 此 `symmetric_key` 值必須符合連接字串中的 SAS 金鑰。

如果您使用 DPS 來連線至 IoT 中樞或 IoT Central 應用程式：

```JSON
{
  "connection_parameters": {
    "connection_type" : "dps",
    "root_interface_model_id": "dtmi:com:example:RootPnpBridgeSampleDevice;1",
    "auth_parameters" : {
      "auth_type" : "symmetric_key",
      "symmetric_key" : "[DEVICE KEY]"
    },
    "dps_parameters" : {
      "global_prov_uri" : "[GLOBAL PROVISIONING URI] - typically it is global.azure-devices-provisioning.net",
      "id_scope": "[IoT Central / IoT Hub ID SCOPE]",
      "device_id": "[DEVICE ID]"
    }
  }
}
```

檢查設定檔的其餘部分，以查看在此範例中設定的介面元件和全域參數。

### <a name="start-the-bridge-in-windows"></a>在 Windows 中啟動橋接器

藉由在命令提示字元中執行橋接器來啟動橋接器：

```cmd
cd iot-plug-and-play-bridge\pnpbridge\cmake\pnpbridge_x86\src\pnpbridge\samples\console

Debug\pnpbridge_bin.exe
```

> [!TIP]
> 若要使用不同位置的設定檔，請在執行橋接器時傳遞其路徑作為命令列參數。
  
> [!TIP]
> 如果您有內建的相機或 USB 攝影機連接到您的電腦，您可以啟動使用相機的應用程式，例如內建的 **相機** 應用程式。 當 **相機** 應用程式正在執行時，橋接器主控台輸出會顯示監視統計資料，並透過數位對應項介面向 IoT 中樞回報畫面播放速率。

## <a name="build-and-run-the-bridge-as-an-iot-edge-module"></a>以 IoT Edge 模組形式建立並執行橋接器

| 平台 | 支援 |
| :-----------: | :-----------: |
| Windows |  否 |
| Linux | 是 |

### <a name="prerequisites"></a>必要條件

若要完成本節，您需要免費或標準層的 Azure IoT 中樞。 若要深入瞭解如何建立 IoT 中樞，請參閱 [建立 iot 中樞](../iot-hub/iot-hub-create-through-portal.md)。

本節中的步驟假設您在 Windows 10 電腦上有下列開發環境。 這些工具可讓您建立 IoT Edge 模組並部署到您的 IoT Edge 裝置：

- Windows 子系統 Linux 版 (WSL) 2 （執行 Ubuntu 18.04 LTS）。 若要深入瞭解，請參閱 [Windows 子系統 Linux 版的 Windows 10 安裝指南](https://docs.microsoft.com/windows/wsl/install-win10)。
- 適用于 Windows 的 Docker Desktop 已設定為使用 WSL 2。 若要深入瞭解，請參閱 [Docker DESKTOP WSL 2 後端](https://docs.docker.com/docker-for-windows/wsl/)。
- [Visual Studio Code 安裝在您的 Windows 環境中](https://code.visualstudio.com/docs/setup/windows) ，並安裝下列三個擴充功能：

  - [遠端開發延伸模組套件](https://aka.ms/vscode-remote/download/extension) -此延伸模組可讓您在 WSL 2 中建立和執行程式碼。
  - [適用于 Visual Studio Code 的 Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) -必須在 VS Code 的 **WSL： Ubuntu-18.04** 環境中啟用此延伸模組。
  - [適用于 Visual Studio Code 的 Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) -必須在 VS Code 的 **WSL： Ubuntu-18.04** 環境中啟用此延伸模組。

- 在您的 WSL 2 環境中執行下列命令，以安裝必要的組建工具：

  ```bash
  sudo apt-get update
  sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
  ```

- [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest&preserve-view=true)安裝在 WSL 2 環境中，以管理您的 Azure 資源。

  > [!TIP]
  > 如果您想要的話，也可以 `az` 在預先安裝 CLI 的 [Azure Cloud Shell](https://shell.azure.com/) 中執行命令。

### <a name="create-an-iot-edge-device"></a>建立 IoT Edge 裝置

此處的命令會建立在 Azure 虛擬機器中執行的 IoT Edge 裝置。 如果您沒有實際裝置的存取權，則在虛擬機器中執行 IoT Edge 裝置適用于測試您的部署。

若要在 IoT 中樞內建立 IoT Edge 裝置註冊，請在您的 WSL 2 環境中執行下列命令。 使用 `az login` 命令來登入您的 Azure 訂用帳戶：

```bash
az iot hub device-identity create --device-id bridge-edge-device --edge-enabled true --hub-name {your IoT hub name}
```

若要建立已安裝 IoT Edge 執行時間的 Azure 虛擬機器，請執行下列命令。 以適當的值更新預留位置：

```bash
az group create --name bridge-edge-resources --location eastus
az deployment group create \
--resource-group bridge-edge-resources \
--template-uri "https://aka.ms/iotedge-vm-deploy" \
--parameters dnsLabelPrefix='{unique DNS name for virtual machine}' \
--parameters adminUsername='{admin user name}' \
--parameters deviceConnectionString=$(az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name} -o tsv) \
--parameters authenticationType='password' \
--parameters adminPasswordOrKey="{admin password for virtual machine}"
```

您現在已有在虛擬機器中執行的 IoT Edge 執行時間。 您可以使用下列命令來確認 **$edgeAgent** 和 **$edgeHub** 正在裝置上執行：

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

> [!CAUTION]
> 當此虛擬機器正在執行時，您會向您收費。 當您未使用它時，請務必將其關閉，並在完成時將其移除。

### <a name="download-the-source-code"></a>下載原始程式碼

在下列步驟中，您會在 WSL 2 環境中建立 Docker 映射。 若要複製 **iot 隨插即用橋接器** 存放庫，請在適當資料夾中的 WSL 2 bash shell 中執行下列命令：

```bash
git clone https://github.com/Azure/iot-plug-and-play-bridge.git

cd iot-plug-and-play-bridge

git submodule update --init --recursive
```

預期先前的命令需要幾分鐘的時間來執行。

### <a name="update-the-dockerfile"></a>更新 *Dockerfile*

啟動 VS Code，開啟命令選擇區，輸入 *REMOTE WSL： WSL 中的 open folder*，然後開啟包含複製之存放庫的 *iot 隨插即用橋接器* 資料夾。

開啟 *pnpbridge\Dockerfile.amd64* 檔案。 編輯環境變數定義，如下所示：

```dockerfile
ENV IOTHUB_DEVICE_CONNECTION_STRING="{Add your device connection string here}"
ENV PNP_BRIDGE_ROOT_MODEL_ID="dtmi:com:example:RootPnpBridgeSampleDevice;1"
ENV PNP_BRIDGE_HUB_TRACING_ENABLED="false"
ENV IOTEDGE_WORKLOADURI="something"
```

> [!TIP]
> 您可以使用下列命令來取出裝置連接字串： `az iot hub device-identity connection-string show --device-id bridge-edge-device --hub-name {your IoT hub name}`

還有其他架構的範例 *dockerfile* 。

### <a name="build-the-iot-plug-and-play-bridge-module-image"></a>建立 IoT 隨插即用 bridge 模組映射

在 VS Code 中，開啟 [命令選擇區]，輸入 *docker registry：登入 DOCKER CLI*，然後選取您的 Azure 訂用帳戶和 container registry。 此命令可讓 Docker 連接到您的容器登錄並上傳模組映射。

開啟檔案 *上的 pnpbridge/module.js* 。 新增 `{your container registry name}.azurecr.io/iotpnpbridge` 為容器映射存放庫和 `v1` 版本。

在 VS Code 中，以滑鼠右鍵按一下 [ **Explorer** ] pnpbridge 中的 [檔案] */[module.js* ]，然後選取 [**建立並推送 IoT Edge 模組映射**]，然後選取 [ **amd64** ] 作為平臺。

在 VS Code 中，您可以在 **Docker** view 中流覽容器登錄的內容，其中現在包含 **iotpnpbridge： V1-amd64** 模組映射。

### <a name="create-a-container-registry"></a>建立容器登錄

IoT Edge 裝置會從容器登錄下載其模組映射。 此範例會使用 Azure container registry。

在 **bridge-edge 資源** 資源群組中建立 Azure container registry。 然後，啟用容器登錄的系統管理員存取權，並取得您的 IoT Edge 裝置下載模組映射所需的認證：

```bash
az acr create -g bridge-edge-resources --sku Basic -n {your container registry name}
az acr update --admin-enabled true -n {your container registry name}
az acr credential show -n {your container registry name}
```

### <a name="create-the-deployment-manifest"></a>建立部署資訊清單

IoT Edge 部署資訊清單會指定要部署到 IoT Edge 裝置的模組和設定值。

在 VS Code 中，開啟檔案 *上的 pnpbridge/deployment.template.js* ：

- `registryCredentials`使用上一個命令中的值更新。 `address`值看起來會像這樣 `{your container registry name}.azurecr.io` 。
- 更新的 `image` 值 `ModulePnpBridge` 。 模組映射看起來像這樣： `{your container registry}.azurecr.io/iotpnpbridge:v1-amd64` 。
- `PnPBridgeConfig`以下列 JSON 取代，以設定 CO2 偵測介面卡：

  ```json
  "PnpBridgeConfig": {
    "pnp_bridge_interface_components": [
      {
        "_comment": "Component 1 - Modbus Device",
        "pnp_bridge_component_name": "Co2Detector1",
        "pnp_bridge_adapter_id": "modbus-pnp-interface",
        "pnp_bridge_adapter_config": {
          "unit_id": 1,
          "tcp": {
            "host": "10.159.29.2",
            "port": 502
          },
          "modbus_identity": "DL679"
        }
      }
    ],
    "pnp_bridge_adapter_global_configs": {
      "modbus-pnp-interface": {
        "DL679": {
          "telemetry": {
            "co2": {
              "startAddress": "40001",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 5000,
              "conversionCoefficient": 1
            },
            "temperature": {
              "startAddress": "40003",
              "length": 1,
              "dataType": "decimal",
              "defaultFrequency": 5000,
              "conversionCoefficient": 0.01
            }
          },
          "properties": {
            "firmwareVersion": {
              "startAddress": "40482",
              "length": 1,
              "dataType": "hexstring",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "modelName": {
              "startAddress": "40483",
              "length": 2,
              "dataType": "string",
              "defaultFrequency": 60000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmStatus_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "boolean",
              "defaultFrequency": 1000,
              "conversionCoefficient": 1,
              "access": 1
            },
            "alarmThreshold_co2": {
              "startAddress": "40225",
              "length": 1,
              "dataType": "integer",
              "defaultFrequency": 30000,
              "conversionCoefficient": 1,
              "access": 2
            }
          },
          "commands": {
            "clearAlarm_co2": {
              "startAddress": "00305",
              "length": 1,
              "dataType": "flag",
              "conversionCoefficient": 1
            }
          }
        }
      }
    }
  }
  ```

  儲存變更。

在 VS Code 中，以滑鼠右鍵按一下 [ **Explorer** ] 視圖中的 [ *pnpbridge]/[deployment.template.js* 檔案]，然後選取 [**產生 IoT Edge 部署資訊清單**]。 此命令會產生部署資訊清單 *上的 pnpbridge/config/deployment.amd64.js* 。

### <a name="deploy-the-bridge-to-your-iot-edge-device"></a>將橋接器部署到您的 IoT Edge 裝置

在 VS Code 中，開啟命令選擇區，輸入 *Azure IoT 中樞：選取 [Iot 中樞*]，然後選取您的訂用帳戶和 IoT 中樞。

在 VS Code 中，以滑鼠右鍵按一下 [ **Explorer** ] pnpbridge 中的 [檔案]/[*開啟]/[deployment.amd64.js* ]，選取 [**建立單一裝置的部署**]，然後選取 [ **bridge-edge 裝置**]。

若要查看裝置上的模組狀態，請執行下列命令：

```bash
az iot hub module-identity list --device-id bridge-edge-device -o table --hub-name {your IoT hub name}
```

執行中的模組清單現在包含已設定為使用 CO2 偵測介面卡的 **ModulePnpBridge** 模組。

### <a name="tidy-up"></a>整理一下

若要從您的 Azure 訂用帳戶移除虛擬機器和 container registry，請執行下列命令：

```bash
az group delete -n bridge-edge-resources
```

## <a name="folder-structure"></a>資料夾結構

*pnpbridge\deps\azure-iot-sdk-c-pnp*：包含適用于 c Sdk 的 Azure IOT 裝置 sdk 的 Git 子模組。

*pnpbridge\scripts*：組建腳本。

*pnpbridge\src*： IoT 隨插即用橋接器核心的原始程式碼。

*pnpbridge\src\adapters*：各種 IoT 隨插即用 bridge 介面卡的原始程式碼。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 IoT 隨插即用橋接器，請造訪 [IoT 隨插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 存放庫。
