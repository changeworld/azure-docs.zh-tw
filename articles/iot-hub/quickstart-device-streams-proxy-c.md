---
title: 適用於 SSH 和 RDP 的 Azure IoT 中樞裝置串流 C 快速入門
description: 在本快速入門中，您會執行範例 C 應用程式，作為透過 IoT 中樞裝置串流進行 SSH 和 RDP 輸送的 Proxy。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 91cf594e74d91b8b4df2108d23459016fa843700
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586606"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>快速入門：使用 C Proxy 應用程式透過 IoT 中樞裝置串流進行 SSH 和 RDP 輸送 (預覽)

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT 中樞目前支援裝置串流作為[預覽功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[IoT 中樞裝置串流](./iot-hub-device-streams-overview.md)可讓服務和裝置應用程式以安全且便於設定防火牆的方式進行通訊。 如需設定的概觀，請參閱[本機 Proxy 範例頁面](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)。

本快速入門說明透過裝置串流來輸送安全殼層 (SSH) 流量 (使用連接埠 22) 的設定。 遠端桌面通訊協定 (RDP) 流量的設定也相類似，但需要稍微變更組態。 因為裝置串流與應用程式或通訊協定無關，因此您可以修改本快速入門，以用於其他類型的應用程式流量。

## <a name="how-it-works"></a>運作方式

下圖說明裝置和服務本機 Proxy 程式如何在 SSH 用戶端與 SSH 精靈程序之間啟用端對端連線。 在公開預覽期間，C SDK 僅支援裝置端上的裝置串流。 因此，本快速入門僅提供執行服務本機 Proxy 應用程式的指示。 若要建置及執行隨附的服務端應用程式，請遵循下列其中一個快速入門中的指示：

* [使用 C# Proxy 透過 IoT 中樞裝置串流進行 SSH/RDP 輸送](./quickstart-device-streams-proxy-csharp.md)
* [使用 NodeJS Proxy 透過 IoT 中樞裝置串流進行 SSH/RDP 輸送](./quickstart-device-streams-proxy-nodejs.md)。

![本機 Proxy 設定](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. 服務本機 Proxy 連線至 IoT 中樞，並啟動對目標裝置的裝置串流。

2. 裝置本機 Proxy 完成串流起始交握，並透過 IoT 中樞對服務端的串流端點建立端對端串流通道。

3. 裝置本機 Proxy 會連線接聽裝置上連接埠 22 的 SSH 精靈。 此設定可加以設定，如「執行裝置本機 Proxy 應用程式」一節所述。

4. 服務本機 Proxy 藉由接聽指定的連接埠 (在此案例中為連接埠 2222)，等候來自使用者的新 SSH 連線。 此設定可加以設定，如「執行裝置本機 Proxy 應用程式」一節所述。 當使用者透過 SSH 用戶端連線時，通道可讓 SSH 應用程式流量能夠在 SSH 用戶端與伺服器程式之間傳輸。

> [!NOTE]
> 透過裝置串流傳送的 SSH 流量會經由 IoT 中樞的串流端點進行輸送，而不是直接在服務與裝置之間傳送。 如需詳細資訊，請參閱[使用 IoT 中樞裝置串流的優點](iot-hub-device-streams-overview.md#benefits)。 此外，圖中也說明在與裝置本機 Proxy 相同的裝置 (或機器) 上執行的 SSH 精靈。 在本快速入門中，提供 SSH 精靈的 IP 位址也可讓裝置本機 Proxy 與精靈在不同的機器上執行。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* 裝置串流的預覽版目前僅支援在下列區域建立的 IoT 中樞：

  * 美國中部
  * 美國中部 EUAP
  * 北歐
  * 東南亞

* 安裝 [Visual Studio 2019](https://www.visualstudio.com/vs/) 並啟用[使用 C++ 的桌面開發](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作負載。
* 安裝最新版的 [Git](https://git-scm.com/download/)。

* 執行下列命令，將適用於 Azure CLI 的 Azure IoT 擴充功能新增至您的 Cloud Shell 執行個體。 IoT 擴充功能可將 IoT 中樞、IoT Edge 和 IoT 裝置佈建服務的特定命令新增至 Azure CLI。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>準備開發環境

針對此快速入門，您會使用[適用於 C 的 Azure IoT 裝置 SDK](iot-hub-device-sdk-c-intro.md)。您會準備用來從 GitHub 複製並建置 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) \(英文\) 的開發環境。 GitHub 上的 SDK 包括此快速入門中使用的範例程式碼。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始安裝 CMake「之前」   ，請務必將 Visual Studio 必要條件 (Visual Studio 和「使用 C++ 的桌面開發」工作負載) 安裝在您的機器上。 符合先決條件並驗證過下載項目之後，您可以安裝 CMake 建置系統。

1. 開啟命令提示字元或 Git Bash 殼層。 執行下列命令以複製 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫：

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    這項作業應該需要幾分鐘的時間。

1. 在 git 存放庫的根目錄中建立 *cmake* 子目錄，並瀏覽至該資料夾。 從 *azure-iot-sdk-c* 目錄執行下列命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. 從 cmake  目錄執行下列命令，以建置您開發用戶端平台特有的 SDK 版本。

   * 在 Linux 中：

      ```bash
      cmake ..
      make -j
      ```

   * 在 Windows 中，請在 Visual Studio 2015 或 2017 的開發人員命令提示字元中執行下列命令。 cmake  目錄中會產生模擬裝置的 Visual Studio 解決方案。

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>建立 IoT 中樞

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>註冊裝置

裝置必須向的 IoT 中樞註冊，才能進行連線。 在本節中，您會使用 Azure Cloud Shell 搭配 [IoT 擴充功能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)來註冊模擬裝置。

1. 若要建立裝置身分識別，請在 Cloud Shell 中執行下列命令：

   > [!NOTE]
   > * 以您為 IoT 中樞選擇的名稱取代 YourIoTHubName  預留位置。
   > * 如需您所註冊的裝置名稱，建議使用如下所示的 *MyDevice*。 如果您為裝置選擇不同的名稱，請在本文中使用該名稱，並先在應用程式範例中更新該裝置名稱，再執行應用程式。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 若要針對您剛註冊的裝置取得「裝置連接字串」  ，請在 Azure Cloud Shell 中執行下列命令：

   > [!NOTE]
   > 以您為 IoT 中樞選擇的名稱取代 YourIoTHubName  預留位置。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    請記下所傳回的裝置連接字串，以供稍後在本快速入門中使用。 看起來會像下列範例：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="ssh-to-a-device-via-device-streams"></a>透過裝置串流使用 SSH 連線至裝置

在本節中，您會建立端對端串流來以通道輸送 SSH 流量。

### <a name="run-the-device-local-proxy-application"></a>執行裝置本機 Proxy 應用程式

1. 編輯資料夾 `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` 中的來源檔案 **iothub_client_c2d_streaming_proxy_sample.c**，並提供您的裝置連接字串、目標裝置的 IP/主機名稱，以及 SSH 連接埠 22：

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. 編譯範例：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. 在裝置上執行已編譯的程式：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>執行服務本機 Proxy 應用程式

如「運作方式」一節所討論，建立用來輸送 SSH 流量的端對端串流，兩端 (即服務和裝置端) 都必須要有本機 Proxy。 在公開預覽期間，IoT 中樞 C SDK 僅支援裝置端上的裝置串流。 若要建置及執行服務本機 Proxy，請遵循下列其中一個快速入門中的指示：

   * [使用 C# Proxy 應用程式透過 IoT 中樞裝置串流進行 SSH/RDP 輸送](./quickstart-device-streams-proxy-csharp.md)
   * [使用 Node.js Proxy 應用程式透過 IoT 中樞裝置串流進行 SSH/RDP 輸送](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>建立 SSH 工作階段

當裝置和服務本機 Proxy 皆在執行中之後，請使用 SSH 用戶端程式，並經由連接埠 2222 連線至服務本機 Proxy (而不是直接使用 SSH 精靈)。

```cmd/sh
ssh {username}@localhost -p 2222
```

此時，SSH 登入視窗會提示您輸入認證。

下圖顯示經由 `IP_address:22` 連線至 SSH 精靈的裝置本機 Proxy 上的主控台輸出：

![裝置本機 Proxy 輸出](./media/quickstart-device-streams-proxy-c/device-console-output.png)

下圖顯示 SSH 用戶端程式和主控台輸出。 SSH 用戶端可藉由連線至服務本機 Proxy 所接聽的連接埠 22，與 SSH 精靈通訊：

![SSH 用戶端輸出](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您會設定 IoT 中樞、註冊裝置、部署裝置和服務本機 Proxy 程式以透過 IoT 中樞建立裝置串流，並使用 Proxy 輸送 SSH 流量。

若要深入了解裝置串流，請參閱：

> [!div class="nextstepaction"]
> [裝置串流概觀](./iot-hub-device-streams-overview.md)
