---
title: 將 IoT 隨插即用預覽版範例裝置程式碼連線至 IoT 中樞 (Windows) | Microsoft Docs
description: 在 Windows 上建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 080820024db1302ff5a841761428442396b90040
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81769855"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>快速入門：將在 Windows 上執行的範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞 (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 檔案總管工具來檢視它傳送至中樞的資訊。 範例應用程式是以 C 撰寫的，並且包含在 Azure IoT 中樞裝置 C SDK 中。 解決方案開發人員可使用 Azure IoT 檔案總管工具直接了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您必須在本機電腦上安裝下列軟體：

* [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) - 在安裝 Visual Studio 時，請確實包含 **NuGet 套件管理員**元件和**使用 C++ 的桌面開發**工作負載。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="install-the-azure-iot-explorer"></a>安裝 Azure IoT 檔案總管

從工具的 [存放庫](https://github.com/Azure/azure-iot-explorer/releases) 頁面下載並安裝最新版的 **Azure IoT 檔案總管**，方法是，選取 [資產] 下的 .msi 檔案，以取得最新的更新。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_ (請記下以供後續使用)：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>準備開發環境

在本快速入門中，您會準備可用來複製及建置 Azure IoT 中樞裝置 C SDK 的開發環境。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將 [Azure IoT C SDK 和程式庫](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫複製到下列位置：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

預期此作業需要幾分鐘的時間才能完成。

## <a name="build-the-code"></a>建置程式碼

您可以使用裝置 SDK 建置包含的範例程式碼。 您所建置的應用程式會模擬連線至 IoT 中樞的裝置。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在裝置 SDK 根資料夾中建立 `cmake` 子目錄，並瀏覽至該資料夾：

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以建置裝置 SDK 和產生的程式碼 Stub：

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > 如果 cmake 找不到您的 C++ 編譯器，您會在執行先前的命令時收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)上執行此命令。

## <a name="run-the-device-sample"></a>執行裝置範例

在 SDK 中執行範例應用程式，以模擬將遙測傳送到 IoT 中樞的 IoT 隨插即用裝置。 若要執行範例應用程式，請使用下列命令，並傳入_裝置連接字串_作為參數。

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

裝置現在已可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 檔案總管來驗證程式碼

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. 若要確保工具可從您的裝置讀取介面模型定義，請選取 [設定]  。 在 [設定] 功能表中，[在已連線的裝置上]  可能已出現在 [隨插即用設定] 中；如果沒有，請選取 [+ 新增模組定義來源]  ，然後選取 [在已連線的裝置上]  加以新增。

1. 回到**裝置**概觀頁面，尋找您先前建立的裝置身分識別。 當裝置應用程式仍在命令提示字元中執行時，請在 Azure IoT 檔案總管中檢查裝置的**線上狀態**是否會回報為「已連線」  (如果不是，請按 **重新整理**，直到變成已連線為止)。 選取裝置以檢視更多詳細資料。

1. 展開識別碼為 **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** 的介面，以顯示介面和 IoT 隨插即用基本項目 — 屬性、命令和遙測資料。

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線至 IoT 隨插即用預覽版裝置並與其互動](howto-develop-solution.md)
