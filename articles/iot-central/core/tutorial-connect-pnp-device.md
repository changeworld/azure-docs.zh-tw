---
title: 教學課程 - 將 IoT 隨插即用 (預覽) 裝置連線至 Azure IoT Central
description: 本教學課程說明如何使用裝置功能模型來產生裝置程式碼。 然後，執行裝置程式碼，確認裝置已連線至您的 IoT Central 應用程式，並使用自動產生的檢視。
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 42098d54725cc12691839b63c508efbecf042aa0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80064425"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>教學課程：使用裝置功能模型建立 IoT 隨插即用 (預覽) 裝置，並將其連線至您的 IoT Central 應用程式

_裝置功能模型_ (DCM) 可說明 [IoT 隨插即用](../../iot-pnp/overview-iot-plug-and-play.md) (預覽) 裝置的功能。 IoT Central 可在裝置第一次連接時，使用 DCM 來建立裝置的裝置範本和視覺效果。

[IoT 隨插即用](../../iot-pnp/overview-iot-plug-and-play.md) 為預覽版本，僅在數個區域中支援。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 Visual Studio Code 中使用 DCM 建立 IoT 隨插即用 (預覽) 裝置。
> * 在 Windows 中執行裝置程式碼，並查看它是否連線至您的 IoT Central 應用程式。
> * 檢視裝置所傳送的模擬遙測。

## <a name="prerequisites"></a>Prerequisites

完成[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)快速入門，以使用 [自訂應用程式] > [自訂應用程式]  範本建立 IoT Central 應用程式。

若要完成本教學課程，您必須在本機電腦上安裝下列軟體：

* 具有 **C++ 建置工具**和 **NuGet 套件管理員元件**工作負載的 [Visual Studio 建置工具](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16)。 或者如果您已安裝具有相同工作負載的 [Visual Studio (Community、Professional 或 Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019、2017 或 2015。
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/) - 當您安裝 **CMake** 時，請選取 [將 CMake 新增至系統路徑]  選項。
* [Visual Studio Code](https://code.visualstudio.com/) \(英文\)。
* [Node.js](https://nodejs.org/)
* `dps-keygen` 公用程式：

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>安裝 Azure IoT Tools

請使用下列步驟安裝在 VS Code 中安裝 Azure IoT Tools 擴充套件：

1. 在 VS Code 中，選取 [延伸模組]  索引標籤。
1. 搜尋 **Azure IoT Tools**。
1. 選取 [安裝]  。

## <a name="prepare-the-development-environment"></a>準備開發環境

在本教學課程中，您會使用 [Vcpkg](https://github.com/microsoft/vcpkg) 程式庫管理員，在您的開發環境中安裝 Azure IoT C 裝置 SDK。

1. 開啟命令提示字元。 執行下列命令來安裝 Vcpkg：

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    接著，若要連結使用者範圍的[整合](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)，請執行下列命令。 第一次執行此命令時，需要系統管理員權限：

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. 安裝 Azure IoT C 裝置 SDK Vcpkg：

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>產生裝置金鑰

若要將裝置連線至 IoT Central 應用程式，您必須要有裝置金鑰。 若要產生裝置金鑰：

1. 登入到您在[建立 Azure IoT Central 應用程式](./quick-deploy-iot-central.md)快速入門中使用 [自訂應用程式]  範本所建立的 IoT Central 應用程式。

1. 移至 [管理]  頁面，然後選取 [裝置連線]  。

1. 記下您在選取 [檢視金鑰]  時所看到的 [識別碼範圍]  和 [主要金鑰]  。 您在本教學課程後續的內容中，會用到這些值。

    ![裝置連線](./media/tutorial-connect-pnp-device/device-connection.png)

1. 開啟命令提示字元並執行下列命令，以產生裝置金鑰：

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    記下產生的_裝置金鑰_，您將在本教學課程的後續步驟中使用此值。

## <a name="download-your-model"></a>下載您的模型

在本教學課程中，您會使用 MxChip IoT DevKit 裝置的公用 DCM。 您不需要以實際的 DevKit 裝置執行程式碼；在本教學課程中，您會編譯要在 Windows 上執行的程式碼。

1. 建立名為 `central_app` 的資料夾，並在 VS Code 中開啟。

1. 使用 **Ctrl+Shift+P** 開啟命令選擇區，輸入 **IoT 隨插即用**，然後選取 [開啟模型存放庫]  。 選取 [公用存放庫]  。 VS Code 會顯示公用模型存放庫中的 DCM 清單。

1. 選取識別碼為 `urn:mxchip:mxchip_iot_devkit:1` 的 **MXChip IoT DevKit** DCM。 然後，選取 [下載]  。 現在，您在 `central_app` 資料夾中已有 DCM 的複本。

![模型存放庫和 DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> 若要使用 IoT Central，裝置功能模型必須具有在相同檔案中以內嵌方式定義的所有介面。

## <a name="generate-the-c-code-stub"></a>產生 C 程式碼 Stub

現在您已有 **MXChip IoT DevKit** DCM 及其相關聯的介面，您可以產生實作模型的裝置程式碼。 若要在 VS Code 中產生 C 程式碼 Stub：

1. 開啟含有 DCM 檔案的資料夾，使用 **Ctrl+Shift+P** 開啟命令選擇區，輸入 **IoT 隨插即用**，然後選取 [產生裝置程式碼 Stub]  。

    > [!NOTE]
    > 第一次使用 IoT 隨插即用程式碼產生器公用程式時，需要幾秒鐘的時間才能下載。

1. 選取您剛才下載的 **MXChip IoT DevKit** DCM 檔案。

1. 輸入專案名稱 **devkit_device**。

1. 選擇 [ANSI C]  作為您的語言。

1. 選擇 [透過 DPS (裝置佈建服務) 對稱金鑰]  作為連線方法。

1. 選擇 [Windows 上的 CMake 專案]  作為專案類型。 請勿選擇 [MXChip IoT DevKit 專案]  ，此選項適用於您有實際的 DevKit 裝置時。

1. 選擇 [透過 Vcpkg]  作為包含 SDK 的方式。

1. VS Code 會開啟新視窗，其中包含在 `devkit_device` 資料夾中產生的裝置程式碼 Stub 檔案。

![產生的裝置程式碼](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>建置程式碼

您可以使用裝置 SDK 來建置產生的裝置程式碼 Stub。 您建置的應用程式會模擬 **MXChip IoT DevKit** 裝置，並連線至您的 IoT Central 應用程式。 應用程式會傳送遙測資料和屬性，並接收命令。

1. 在命令提示字元中，於 `devkit_device` 資料夾中建立一個 `cmake` 子目錄，並瀏覽至該資料夾：

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以建置產生的程式碼 Stub。 將 `<directory of your Vcpkg repo>` 預留位置取代為 **Vcpkg** 存放庫複本的路徑：

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    如果您使用的是 Visual Studio 2017 或 2015，則需要根據您所使用的建置工具來指定 CMake 產生器：

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. 組件順利完成後，請在相同的命令提示字元中執行您的應用程式。 以您先前記下的值取代 `<scopeid>` 和 `<devicekey>`：

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <devicekey>
    ```

1. 裝置應用程式會開始將資料傳送至 IoT 中樞。 有時候，您會在第一次執行前一個命令時看到 `Error registering device for DPS` 錯誤。 如果您看到此錯誤，請重試命令。

## <a name="view-the-device"></a>檢視裝置

當您的裝置程式碼連線至 IoT Central 之後，您就可以檢視它所傳送的屬性和遙測資料：

1. 在您的 IoT Central 應用程式中，移至 [裝置]  頁面，然後選取 **mxchip-01** 裝置。 此裝置會在裝置程式碼連線時自動新增：

    ![概觀分頁](./media/tutorial-connect-pnp-device/overview-page.png)

    幾分鐘後，此頁面會顯示裝置所傳送之遙測資料的圖表。

1. 選取 [關於]  頁面，以查看裝置所傳送的屬性值。

1. 選取 [命令]  頁面，以在裝置上呼叫命令。 您可以在執行裝置程式碼的命令提示字元中查看裝置回應。

1. 移至 [裝置範本]  頁面，以查看 IoT Central 從公用存放庫中的 DCM 建立的範本：

    ![裝置範本頁面](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何連接從公用模型存放庫中的 DCM 產生的 IoT 隨插即用 (預覽) 裝置。

若要深入了解 DCM 以及如何建立您自己的模型，請繼續進行操作指南：

> [!div class="nextstepaction"]
> [定義新的 IoT 裝置類型](./howto-set-up-template.md)
