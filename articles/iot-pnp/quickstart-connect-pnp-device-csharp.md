---
title: 將 IoT 隨插即用預覽版的程式碼連線至 IoT 中樞 - C# | Microsoft Docs
description: 使用 C# (.NET) 建置並執行連線至 IoT 中樞的 IoT 隨插即用預覽版範例裝置程式碼。 使用 Azure IoT 檔案總管工具，檢視裝置傳送至中樞的資訊。
author: dominicbetts
ms.author: dobett
ms.date: 12/27/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: d598bbb792c071db281ebdc562402ba0198305ab
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044191"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-c"></a>快速入門：將範例 IoT 隨插即用預覽版裝置應用程式連線至 IoT 中樞 (C#)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

本快速入門說明如何建置範例 IoT 隨插即用裝置應用程式、將其連線至您的 IoT 中樞，並使用 Azure IoT 檔案總管工具來檢視它傳送至中樞的資訊。 範例應用程式會以 C# (搭配 .NET) 撰寫，並在適用於 C# (.NET) 的 Azure IoT 範例集合中提供。 解決方案開發人員可使用 Azure IoT 檔案總管工具直接了解 IoT 隨插即用裝置的功能，而不需檢視任何裝置程式碼。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本快速入門，您的開發機器上必須安裝 .NET Core 3.0。 您可以從[下載 .NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)下載適用於多個平台的此 .NET Core SDK 版本。

您可以在本機終端機視窗中執行下列命令，確認開發機器上的 .NET 版本： 

```cmd/sh
dotnet --version
```

### <a name="install-the-azure-iot-explorer"></a>安裝 Azure IoT 檔案總管

從工具的 [存放庫](https://github.com/Azure/azure-iot-explorer/releases) 頁面下載並安裝最新版的 **Azure IoT 檔案總管**，方法是，選取 [資產] 下的 .msi 檔案，以取得最新的更新。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

執行下列命令，以取得中樞的 _IoT 中樞連接字串_ (請記下以供後續使用)：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>準備開發環境

您會在本快速入門中準備開發環境，以用來複製及建置適用於 C# (.NET) 的 Azure IoT 範例。

在您選擇的目錄中開啟命令提示字元。 執行下列命令，將[適用於 C# (.NET) 的 Azure IoT 範例](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存放庫複製到下列位置：

```cmd/sh
git clone https://github.com/Azure-Samples/azure-iot-samples-csharp
```

此作業可能需要幾分鐘的時間才能完成。

## <a name="run-the-device-sample"></a>執行裝置範例

您可以使用複製的範例程式碼來建置應用程式，以模擬連線到 IoT 中樞的裝置。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在本機終端機視窗中，移至您複製存放庫的資料夾，然後瀏覽至 **azure-iot-samples-csharp/digitaltwin/Samples/device/EnvironmentalSensorSample** 資料夾。 

1. 設定_裝置連接字串_：

    ```cmd/sh
    set DIGITAL_TWIN_DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
    ```

1. 執行範例應用程式，以模擬將遙測傳送到 IoT 中樞的 IoT 隨插即用裝置。 在同一個終端機視窗中，若要建置必要套件並執行範例應用程式，請使用下列命令：

    ```cmd\sh
    dotnet run --framework=netcoreapp3.0
    ```

您會看到訊息指出裝置已成功註冊，正在等候來自雲端的更新。 這表示裝置現在可接收命令和屬性更新，並已開始將遙測資料傳送至中樞。 完成後續步驟後，請讓範例保持執行狀態。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 檔案總管來驗證程式碼

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. 若要確保工具可從您的裝置讀取介面模型定義，請選取 [設定]  。 在 [設定] 功能表中，[在已連線的裝置上]  可能已出現在 [隨插即用設定] 中；如果沒有，請選取 [+ 新增模組定義來源]  ，然後選取 [在已連線的裝置上]  加以新增。

1. 回到**裝置**概觀頁面，尋找您先前建立的裝置身分識別。 當裝置應用程式仍在命令提示字元中執行時，請在 Azure IoT 檔案總管中檢查裝置的**線上狀態**是否會回報為「已連線」  (如果不是，請按 **重新整理**，直到變成已連線為止)。 選取裝置以檢視更多詳細資料。

1. 展開識別碼為 **urn:csharp_sdk_sample:EnvironmentalSensor:1** 的介面，以顯示介面和 IoT 隨插即用基本項目 — 屬性、命令和遙測資料。

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何將 IoT 隨插即用裝置連線至 IoT 中樞。 若要深入了解如何建置與 IoT 隨插即用裝置互動的解決方案，請參閱：

> [!div class="nextstepaction"]
> [操作說明：連線至 IoT 隨插即用預覽版裝置並與其互動](howto-develop-solution.md)
