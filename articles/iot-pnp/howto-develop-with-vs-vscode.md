---
title: 使用 Visual Studio 和 Visual Studio Code 來建立 IoT 隨插即用預覽裝置 |Microsoft Docs
description: 使用 Visual Studio 和 Visual Studio Code 來加速撰寫 IoT 隨插即用裝置模型及執行裝置程式碼。
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159229"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>使用 Visual Studio 和 Visual Studio Code 來建立 IoT 隨插即用裝置

Visual Studio Code 的 Azure IoT Tools 提供整合式環境，可撰寫裝置功能模型（DCM）和介面、發行至模型存放庫，以及產生基本架構 C 程式碼來執行裝置應用程式。

本文示範如何：

- 產生裝置程式碼和應用程式專案。
- 在您的裝置專案中使用產生的程式碼。
- 重新產生基本架構程式碼來進行反復執行。

若要深入瞭解如何使用 VS Code 來開發 IoT 裝置，請[https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)參閱。

## <a name="prerequisites"></a>先決條件

安裝 [Visual Studio Code](https://code.visualstudio.com/)。

使用下列步驟，在 VS Code 中安裝延伸模組套件。

1. 在 VS Code 中，選取 [**擴充**功能] 索引標籤。
1. 從 marketplace 搜尋並安裝**Azure IoT Tools** 。

## <a name="generate-device-code-and-project"></a>產生裝置程式碼和專案

在 VS Code 中，使用**Ctrl + Shift + P**開啟命令選擇區，輸入**IoT 隨插即用**，然後選取 [**產生裝置程式碼 Stub** ] 來設定基本架構程式碼和專案類型。 下列清單詳細說明每個步驟：

- **要用於產生程式碼的 DCM**檔案。 若要產生基本架構裝置程式碼，請開啟包含它所執行之 DCM 和介面檔案的資料夾。 如果程式碼產生器找不到 DCM 所需的介面，它會視需要從模型存放庫下載它。

- **裝置程式碼語言**。 目前，程式碼產生器僅支援 ANSI C。

- **專案名稱**。 專案名稱是用來做為所產生程式碼和其他專案檔的資料夾名稱。 根據預設，資料夾會放在 DCM 檔案的旁邊。 若要避免在每次更新您的 DCM 並重新產生裝置程式碼時，手動複製所產生的程式碼資料夾，請將您的 DCM 檔案保存在與專案資料夾相同的資料夾中。

- **連接到 Azure IoT 的方法**。 產生的檔案也包含程式碼，可將裝置設定為連接到 Azure IoT 中樞。 您可以選擇直接連接到[Azure IoT 中樞](https://docs.microsoft.com/azure/iot-hub)或使用裝置布建[服務](https://docs.microsoft.com/azure/iot-dps)。

    - 透過**IoT 中樞裝置連接字串**：指定裝置應用程式要直接連接到 IoT 中樞的裝置連接字串。
    - 透過**dps 對稱金鑰**：指定使用 DPS 連線到 IoT 中樞或 IoT Central 所需之裝置應用程式的**識別碼範圍**、**對稱金鑰**和**裝置識別碼**。

- **專案類型**。 程式碼產生器也會產生 CMake 或 Arduino 專案。 目前支援的專案類型為：

    - **Windows 上的 CMake 專案**：適用于在 windows 上使用[CMake](https://cmake.org/)做為組建系統的裝置專案。 此選項會`CMakeLists.txt`在與 C 程式碼相同的資料夾中，產生裝置 SDK 設定。
    - **Linux 上的 CMake 專案**：適用于在 linux 上使用[CMake](https://cmake.org/)做為組建系統的裝置專案。 此選項會`CMakeLists.txt`在與 C 程式碼相同的資料夾中，產生裝置 SDK 設定。
    - **MXChip Iot DevKit 專案**：適用于在[MXChip IoT DevKit](https://aka.ms/iot-devkit)裝置上執行的裝置專案。 此選項會產生 Arduino 專案，您可以[在 VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)或在 Arduino IDE 中用來在 IoT DevKit 裝置上建立和執行。

- **裝置 SDK 類型**。 如果您選取 [CMake] 做為 [ `CMakeLists.txt`專案類型]，這就是在中設定產生的程式碼將如何包含 Azure IoT C 裝置 SDK 的步驟：

    - 透過**原始程式碼**：產生的程式碼會依賴[裝置 SDK 原始程式碼](https://github.com/Azure/azure-iot-sdk-c)以包含在中，並與它一起建立。 當您自訂裝置 SDK 原始程式碼時，建議使用此選項。
    - 透過**Vcpkg**：產生的程式碼依賴[裝置 SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)以包含在中，並與它一起建立。 這是執行 Windows、Linux 或 macOS 之裝置的建議方式。

    > [!NOTE]
    > Azure IoT C 裝置 SDK Vcpkg 的 macOS 支援正在進行中。

程式碼產生器會嘗試使用位於本機資料夾中的 DCM 和介面檔案。 如果介面檔案不在本機資料夾中，程式碼產生器會在公用模型存放庫或公司模型存放庫中尋找這些檔案。 [通用介面](./concepts-common-interfaces.md)檔案會儲存在公用模型存放庫中。

程式碼產生完成之後，延伸模組會使用程式碼開啟新的 VS Code 視窗。 如果您開啟產生的檔案（如**main. c**），您可能會發現 IntelliSense 回報它無法開啟 c SDK 原始程式檔。 若要啟用正確的 IntelliSense 和程式碼導覽，請使用下列步驟來包含 C SDK 來源：

1. 在 VS Code 中，使用**Ctrl + Shift + P**開啟命令選擇區，輸入並選取 [ **C/c + +：編輯設定（JSON）** ] 以開啟**c_cpp_properties JSON**檔案。

1. 在`includePath`區段中新增裝置 SDK 的路徑：

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. 儲存檔案。

## <a name="use-the-generated-code"></a>使用產生的程式碼

下列指示說明如何在不同的開發電腦平臺上，使用您自己的裝置專案中產生的程式碼。 指示會假設您使用 IoT 中樞的裝置連接字串搭配產生的程式碼：

### <a name="linux"></a>Linux

若要在 Linux 環境（例如 Ubuntu 或 Debian）中使用 CMake，搭配裝置 C SDK Vcpkg 來建立裝置程式碼：

1. 開啟終端機應用程式。

1. `apt-get`使用命令來安裝**GCC**、 **Git**、 `cmake`和所有相依性：

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    確認 `cmake` 的版本高於 **2.8.12**，而且 **GCC** 的版本高於 **4.4.7**。

    ```bash
    cmake --version
    gcc --version
    ```

1. 安裝 Vcpkg：

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    然後，若要連結使用者範圍的[整合](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)，請執行：

    ```bash
    ./vcpkg integrate install
    ```

1. 安裝 Azure IoT C 裝置 SDK Vcpkg：

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. 在資料夾`cmake`中建立子目錄，其中包含產生的程式碼 stub，並流覽至該資料夾：

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以使用 CMake 來建立裝置 SDK 和產生的程式程式碼端：

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. 組建成功之後，請執行應用程式，並將 IoT 中樞裝置連接字串指定為參數。

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

若要使用 Visual Studio CMake 與 Windows 上的裝置 C SDK 建立裝置程式碼，請在命令列中，參閱[IoT 隨插即用快速入門](./quickstart-create-pnp-device-windows.md)。 下列步驟示範如何在 Visual Studio 中，將裝置程式碼與裝置 C SDK Vcpkg 做為 CMake 專案一起建立。

1. 請依照[快速入門](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment)中的步驟，透過 Vcpkg 安裝適用于 C 的 Azure IOT 裝置 SDK。

1. 安裝[Visual Studio 2019 （「社區」、「專業」或「企業」）](https://visualstudio.microsoft.com/downloads/) -請確定您包含**NuGet 套件管理員**元件和**使用 c + + 的桌面開發**工作負載。

1. 開啟 Visual Studio，選擇 [檔案] **> 開啟 > CMake ...** ]，以`CMakeLists.txt`開啟資料夾中包含所產生程式碼的。

1. 在 [**一般** **] 工具列中，尋找**[設定] 下拉式清單。 選取 [**管理**設定] 以新增專案的 CMake 設定。

    ![管理設定](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. 在 [ **CMake 設定**] 中，新增設定並選取 [ **x86-Debug** ] 做為 [目標]。

1. 在**CMake 命令引數**中，新增下列程式程式碼：

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. 儲存檔案。

1. 切換**至 [設定**] 下拉式清單中的 [ **x86-Debug** ]。 需要幾秒鐘的時間，CMake 才會產生快取。 查看 [輸出] 視窗以查看進度。

    ![CMake 輸出](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. 在 [**方案總管**中，以滑鼠右鍵按一下`CMakeLists.txt`根資料夾中的，然後從內容功能表中選取 [**組建**]，以使用裝置 SDK 建立產生的程式程式碼端。

1. 組建成功之後，在命令提示字元中，執行應用程式，並將 IoT 中樞裝置連接字串指定為參數。

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> 若要深入瞭解如何在 Visual Studio 中使用 CMake，請參閱[組建 CMake 專案](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)。

### <a name="macos"></a>macOS

下列步驟示範如何使用 CMake，在 macOS 上建立裝置程式碼和裝置 C SDK 原始程式碼：

1. 開啟 [終端機應用程式]。

1. 使用[Homebrew](https://homebrew.sh)來安裝所有相依性：

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. 確認[CMake](https://cmake.org/)至少為版本**2.8.12**：

    ```bash
    cmake --version
    ```

1. [修補程式會捲曲](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os)到可用的最新版本。

1. 在包含所產生程式碼的資料夾中，複製[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存放庫：

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    預期此作業需要幾分鐘的時間才能完成。

1. 在包含所產生`cmake`程式碼的資料夾底下，建立名為的資料夾，然後流覽至該資料夾。

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 執行下列命令，以使用 CMake 來建立裝置 SDK 和產生的程式程式碼端：

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. 組建成功之後，請執行應用程式，並將 IoT 中樞裝置連接字串指定為參數。

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>重新產生基本架構程式碼以進行反覆運算

如果您更新您的 DCM 或介面檔案，程式碼產生器可以重新產生程式碼。 假設您已經從 DCM 檔案產生您的裝置程式碼，以重新產生程式碼：

1. 開啟含有 DCM 檔案的資料夾，使用 **Ctrl+Shift+P** 開啟命令選擇區，輸入 **IoT 隨插即用**，然後選取 [產生裝置程式碼 Stub]****。

1. 選擇您更新的 DCM 檔案。

1. 選取 [**重新產生 {project name} 的程式碼**]。

1. 程式碼產生器會使用您設定的先前設定，並重新產生程式碼。 不過，它不會覆寫可能包含使用者程式碼（例如`main.c`和`{project_name}_impl.c`）的檔案。

> [!NOTE]
> 如果您在介面檔案中更新 URN 識別碼，則會將它視為新的介面。 當您重新產生程式碼時，程式碼產生器會產生介面的程式碼，但不會覆`{project_name}_impl.c`寫檔案中的原始檔案。

## <a name="problems-and-feedback"></a>問題與意見反應

Azure IoT Tools 是 GitHub 上的開放原始碼專案。 針對任何問題和功能要求，您可以[在 GitHub 上建立問題](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)。

## <a name="next-steps"></a>後續步驟

在此操作說明文章中，您已瞭解如何使用 Visual Studio 和 Visual Studio Code 來產生基本架構 C 程式碼，以執行裝置應用程式。 建議的下一個步驟是瞭解如何[安裝和使用 Azure IoT explorer](./howto-install-iot-explorer.md)工具。
