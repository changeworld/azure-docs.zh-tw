---
title: 如何將 Linux 或 Windows 上執行的 IoT 隨插即用 bridge 範例連線到 IoT 中樞 |Microsoft Docs
description: 在連接到 IoT 中樞的 Linux 或 Windows 上建立並執行 IoT 隨插即用橋接器。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4c84fbb131809c92d597b46d18c53e46b636f8ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91613470"
---
# <a name="how-to-connect-an--iot-plug-and-play-bridge-sample-running-on-linux-or-windows-to-iot-hub"></a>如何將在 Linux 或 Windows 上執行的 IoT 隨插即用 bridge 範例連線到 IoT 中樞

本 how to 說明如何建立 IoT 隨插即用橋接器的範例環境介面卡、將它連線到您的 IoT 中樞，以及使用 Azure IoT explorer 工具來查看它所傳送的遙測。 IoT 隨插即用橋接器是以 C 撰寫，並且包含適用于 C 的 Azure IoT 裝置 SDK。在本教學課程結束時，您應該能夠執行 IoT 隨插即用橋接器，並在 Azure IoT explorer 中查看其報告遙測： :::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-explorer-telemetry.png" alt-text="顯示 Azure iot explorer 的螢幕擷取畫面，其中包含回報的遙測資料表 (濕度、來自 IoT 隨插即用 bridge 的溫度) 。":::

## <a name="prerequisites"></a>必要條件

您可以在 Linux 或 Windows 上執行本快速入門。 本操作指南中的 shell 命令遵循路徑分隔符號 ' ' 的 Windows 慣例 `\` ，如果您在 Linux 上遵循，請務必將這些分隔符號換成 ' `/` '。

必要條件會因作業系統而有所不同：

### <a name="linux"></a>Linux

本快速入門假設您使用的是 Ubuntu Linux。 本快速入門中的步驟已使用 Ubuntu 18.04 進行測試。

若要在 Linux 上完成本快速入門，您必須在本機 Linux 環境上安裝下列軟體：

使用 `apt-get` 命令，安裝 **GCC**、**Git**、**cmake** 和所有必要的相依性：

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

確認 `cmake` 的版本高於 **2.8.12**，而且 **GCC** 的版本高於 **4.4.7**。

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

若要在 Windows 上完成本快速入門，請在您的本機 Windows 環境上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在[安裝](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) Visual Studio 時，請確實包含**使用 C++ 的桌面開發**工作負載。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="azure-iot-explorer"></a>Azure IoT 總管

若要在本快速入門的第二個部分與範例裝置互動，您可以使用 **Azure IoT 總管**工具。 針對您的作業系統[下載並安裝最新版的 Azure IoT 總管](./howto-use-iot-explorer.md)。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令以取得中樞的 _IoT 中樞連接字串_ 。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> 您也可以使用 Azure IoT 總管工具來尋找 IoT 中樞連接字串。

執行下列命令，針對您新增至中樞的裝置取得「裝置連接字串」。 記下此連接字串，您稍後會在本快速入門中用到：

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

## <a name="view-the-model"></a>查看模型

在稍後的步驟中，您將使用 Azure IoT Explorer 來查看裝置連線到您的 IoT 中樞。 Azure IoT Explorer 需要模型檔案的本機複本，以符合您的裝置所傳送的 **模型識別碼** 。 模型檔案可讓 IoT Explorer 顯示您裝置所執行的遙測、屬性和命令。

當您下載以下步驟中的程式碼時，它會在資料夾下包含範例模型檔案 `pnpbridge/docs/schema` 。 若要準備 Azure IoT explorer：

1. 在本機電腦上建立名為 *models* 的資料夾。
1. 查看 [EnvironmentalSensor.js](https://aka.ms/iot-pnp-bridge-env-model) ，並將 JSON 檔案儲存至 [ *模型* ] 資料夾
1. 查看 [RootBridgeSampleDevice.js](https://aka.ms/iot-pnp-bridge-root-model) ，並將 JSON 檔案儲存至 [ *模型* ] 資料夾。

## <a name="download-the-code"></a>下載程式碼

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將 [IoT 隨插即用 bridge](https://aka.ms/iotplugandplaybridge) GitHub 存放庫複製到下列位置：

```cmd
git clone https://github.com/Azure/iot-plug-and-play-bridge.git
```

將 IoT 隨插即用橋接器儲存機制複製到您的電腦之後，請開啟系統管理命令提示字元，然後流覽至複製的存放庫目錄：

```cmd
cd pnpbridge
git submodule update --init --recursive
```

預期此作業需要幾分鐘的時間才能完成。

>[!NOTE]
> 如果您遇到 git 複製子模組更新失敗的問題，這是 Windows 檔案路徑和 git 的已知問題，請參閱： https://github.com/msysgit/git/pull/110 。 您可以嘗試下列命令來解決此問題： `git config --system core.longpaths true`

## <a name="setting-up-the-configuration-json"></a>設定 JSON 設定

將 IoT 隨插即用橋接器存放庫複製到您的電腦之後，請流覽至 `pnpbridge/docs/schema` 複製的存放庫目錄，您可以在其中找到設定 [JSON](https://aka.ms/iot-pnp-bridge-env-config) 或 `config.json` 橋接器的環境感應器範例。 您可以在 [IoT 隨插即用 bridge 概念](concepts-iot-pnp-bridge.md)檔中深入瞭解設定檔。

針對此 `root-_interface_model_id` 欄位，您將需要複製可識別裝置模型的 IoT 隨插即用模型識別碼。 在此範例中為 `dtmi:com:example:SampleDevice;1`。 修改下列 **pnp_bridge_parameters** 節點下 `config.json` 的參數： '：

  使用連接字串 (附注： symmetric_key 必須符合連接字串) 中的 SAS 金鑰：

  ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "[To fill in]",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
  ```

 填妥檔案之後，檔案 `config.json` 應該類似：

   ```JSON
    {
      "connection_parameters": {
        "connection_type" : "connection_string",
        "connection_string" : "[CONNECTION STRING]",
        "root_interface_model_id": "dtmi:com:example:SampleDevice;1",
        "auth_parameters" : {
          "auth_type" : "symmetric_key",
          "symmetric_key" : "[SYMMETRIC KEY]"
        }
      }
    }
  }
```

 建立橋接器之後，您必須將它放 `config.json` 在與橋接器相同的目錄中，或在執行時指定它的路徑。

## <a name="build-the-iot-plug-and-play-bridge"></a>建立 IoT 隨插即用橋接器

流覽至存放庫目錄中的 *pnpbridge* 資料夾。

若為 Windows，請在 [Visual Studio 的開發人員命令提示字元](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)中執行下列程式：

```cmd
cd scripts\windows
build.cmd
```

同樣地，若為 Linux，請執行下列動作：

```bash
cd scripts/linux
./setup.sh
./build.sh
```

>[!TIP]
>在 Windows 上，您可以在 Visual Studio 2019 中開啟 cmake 命令所產生的解決方案。 開啟 cmake 目錄中的 *azure_iot_pnp_bridge .sln* 專案檔，並將 *pnpbridge_bin* 專案設定為方案中的啟始專案。 您現在可以在 Visual Studio 中建置範例，並於偵錯模式中執行。

## <a name="start-the-iot-plug-and-play-bridge"></a>啟動 IoT 隨插即用橋接器

 流覽至 *pnpbridge* 資料夾，並在命令提示字元中執行下列命令，以啟動環境感應器的 IoT 隨插即用橋接器範例：

```bash
 cd cmake/pnpbridge_x86/src/adaptors/samples/environmental_sensor/
./pnpbridge_environmentalsensor

```

```cmd
REM Windows
cd cmake\pnpbridge_x86\src\adaptors\samples\environmental_sensor
Debug\pnpbridge_environmentalsensor.exe
```

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 總管來驗證程式碼

當裝置用戶端範例啟動之後，請使用 Azure IoT 總管工具來驗證其是否正確運作。

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

* [什麼是 IoT 隨插即用橋接器](./concepts-iot-pnp-bridge.md)
* [請參閱 IoT 隨插即用橋接器的 GitHub 開發人員參考](https://aka.ms/iot-pnp-bridge-dev-doc)
* [在 GitHub 上 IoT 隨插即用 bridge](https://aka.ms/iotplugandplaybridge)
