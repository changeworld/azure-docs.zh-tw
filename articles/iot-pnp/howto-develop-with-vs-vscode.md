---
title: 使用視覺化工作室和視覺工作室代碼構建 IoT 隨插即用預覽設備 |微軟文檔
description: 使用視覺化工作室和視覺化工作室代碼加快創作 IoT 隨插即用裝置模型並實現設備代碼。
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159229"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>使用視覺化工作室和視覺化工作室代碼構建 IoT 隨插即用裝置

適用于視覺化工作室代碼的 Azure IoT 工具為創作裝置功能模型 （DCM） 和介面、發佈到模型存儲庫以及生成骨架 C 代碼以實現設備應用程式提供了集成環境。

本文示範如何：

- 生成設備代碼和應用程式專案。
- 在設備專案中使用生成的代碼。
- 通過重新生成骨架代碼進行反覆運算。

要瞭解有關使用 VS 代碼開發 IoT 設備的更多詳細資訊[https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)，請參閱。

## <a name="prerequisites"></a>Prerequisites

安裝[視覺化工作室代碼](https://code.visualstudio.com/)。

使用以下步驟在 VS 代碼中安裝擴展包。

1. 在 VS 代碼中，選擇 **"擴展"** 選項卡。
1. 從應用商店中搜索並安裝**Azure IoT 工具**。

## <a name="generate-device-code-and-project"></a>生成設備代碼和專案

在 VS 代碼中，使用**Ctrl_Shift_P**打開命令調色板，輸入**IoT 隨插即用**，然後選擇 **"生成設備程式碼端**"來配置骨架代碼和專案類型。 下面的清單詳細介紹了每個步驟：

- **用於生成代碼的 DCM 檔**。 要生成骨架設備代碼，請打開包含它實現的 DCM 和介面檔的資料夾。 如果代碼產生器找不到 DCM 所需的介面，它將根據需要從模型存儲庫下載它。

- **設備代碼語言**。 目前，代碼產生器僅支援 ANSI C。

- **專案名稱**. 專案名稱用作生成的代碼和其他專案檔案的資料夾名稱。 預設情況下，該資料夾位於 DCM 檔旁邊。 為了避免在更新 DCM 並重新生成設備代碼時手動複製生成的代碼資料夾，請將 DCM 檔保留與專案資料夾相同的資料夾中。

- **連接到 Azure IoT 的方法**。 生成的檔還包含用於配置設備以連接到 Azure IoT 中心的代碼。 您可以選擇直接連接到[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)或使用[設備預配服務](https://docs.microsoft.com/azure/iot-dps)。

    - **通過 IoT 中心設備連接字串**：指定設備應用程式直接連接到 IoT 中心的設備連接字串。
    - **通過 DPS 對稱金鑰**：為使用 DPS 連接到 IoT 中心或 IoT 中心所需的設備應用程式指定**ID 範圍**、**對稱金鑰**和設備**ID。**

- **專案類型**. 代碼產生器還生成 CMake 或 Arduino 專案。 目前，支援的專案類型包括：

    - **在 Windows 上的"製作專案**"：用於在 Windows 上使用[CMake](https://cmake.org/)作為生成系統的設備專案。 此選項與`CMakeLists.txt`C 代碼在同一資料夾中的設備 SDK 配置一起生成。
    - **在Linux上的CMake專案**：用於在Linux上使用[CMake](https://cmake.org/)作為構建系統的設備專案。 此選項與`CMakeLists.txt`C 代碼在同一資料夾中的設備 SDK 配置一起生成。
    - **MXChip IoT DevKit 專案**：用於在[MXChip IoT DevKit](https://aka.ms/iot-devkit)設備上運行的設備專案。 此選項生成 Arduino 專案，您可以在[VS 代碼](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)或 Arduino IDE 中使用該專案在 IoT DevKit 設備上生成和運行。

- **設備 SDK 類型**。 如果選擇 CMake 作為專案類型，則這是配置生成的代碼將如何在 以下部分`CMakeLists.txt`中包括 Azure IoT C 設備 SDK 的步驟：

    - **通過原始程式碼**：生成的代碼依賴于設備 SDK[原始程式碼](https://github.com/Azure/azure-iot-sdk-c)來包含在中並與之一起構建。 自訂設備 SDK 原始程式碼時，建議這樣做。
    - **通過 Vcpkg**：生成的代碼依賴于[設備 SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)來包含在中並與之一起構建。 對於運行 Windows、Linux 或 macOS 的設備，這是推薦的方法。

    > [!NOTE]
    > 對 Azure IoT C 設備 SDK Vcpkg 的 macOS 支援正在進行中。

代碼產生器嘗試使用位於本地資料夾中的 DCM 和介面檔。 如果介面檔不在本地資料夾中，代碼產生器在公共模型存儲庫或公司模型存儲庫中查找它們。 [公共介面檔](./concepts-common-interfaces.md)存儲在公共模型存儲庫中。

代碼生成完成後，擴展將打開一個帶有代碼的新 VS 代碼視窗。 如果打開生成的檔（如**main.c，** 您可能會發現 IntelliSense）報告它無法打開 C SDK 原始檔案。 要啟用正確的 IntelliSense 和代碼導航，請使用以下步驟包括 C SDK 源：

1. 在 VS 代碼中，使用**Ctrl_Shift_P**打開命令調色板，鍵入並選擇**C/C++：編輯配置 （JSON）** 以打開**c_cpp_properties.json**檔。

1. 在以下部分中添加設備 SDK`includePath`的路徑：

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. 儲存檔案。

## <a name="use-the-generated-code"></a>使用生成的代碼

以下說明描述了如何在不同開發機器平臺上的設備上使用生成的代碼。 這些說明假定您使用的是帶有生成的代碼的 IoT 中心設備連接字串：

### <a name="linux"></a>Linux

在 Linux 環境中使用 CMake（如 Ubuntu 或 Debian）中的 CMake 與設備 C SDK Vcpkg 一起構建設備代碼，請：

1. 打開終端應用程式。

1. 使用`apt-get`命令安裝`cmake`**GCC** **、Git**和所有依賴項：

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

    然後，要連接使用者範圍[的集成](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)，運行：

    ```bash
    ./vcpkg integrate install
    ```

1. 安裝 Azure IoT C 裝置 SDK Vcpkg：

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. 在資料夾中`cmake`創建子目錄包含生成的程式碼端，並導航到該資料夾：

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 運行以下命令以使用 CMake 構建設備 SDK 和生成的程式碼端：

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. 生成成功後，運行指定 IoT 中心設備連接字串作為參數的應用程式。

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

要使用 CMake 和命令列中的 Visual Studio C/C++ 編譯器在 Windows 上構建設備代碼，請參閱[IoT 隨插即用快速入門](./quickstart-create-pnp-device-windows.md)。 以下步驟演示如何在 Visual Studio 中將設備 C SDK Vcpkg 作為 CMake 專案一起構建設備代碼。

1. 按照[快速入門](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment)中的步驟通過 Vcpkg 安裝 C 的 Azure IoT 設備 SDK。

1. 安裝[Visual Studio 2019（社區、專業版或企業版）](https://visualstudio.microsoft.com/downloads/) - 確保您包括**NuGet 包管理器**元件和**具有C++工作負載的桌面開發**。

1. 打開視覺化工作室，選擇 **"打開檔>>"完成"，** 以`CMakeLists.txt`打開資料夾中包含生成的代碼。

1. 在 **"常規**"工具列中，查找 **"配置"** 下拉清單。 選擇 **"管理配置**"可為專案添加"製作"設置。

    ![管理配置](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. 在 **"CMake 設置"** 中，添加新配置並選擇**x86-Debug**作為目標。

1. 在 **"CMake 命令參數"中**，添加以下行：

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. 儲存檔案。

1. 在 **"配置"** 下拉下下拉清單中切換到**x86-Debug。** 它需要幾秒鐘的 CMake 來為其生成緩存。 查看"輸出"視窗以查看進度。

    ![CMake 輸出](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. 在**解決方案資源管理器**中，按右鍵`CMakeLists.txt`根資料夾中的，然後從內容功能表中選擇 **"生成"** 以使用設備 SDK 構建生成的程式碼端。

1. 生成成功後，在命令提示符下，運行應用程式，指定 IoT 中心設備連接字串作為參數。

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> 要瞭解有關在視覺化工作室中使用"單行"的更多詳細資訊，請參閱[構建"構建單行"專案](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)。

### <a name="macos"></a>macOS

以下步驟演示如何使用 CMake 在 macOS 上構建設備代碼和設備 C SDK 原始程式碼：

1. 開放式終端應用程式。

1. 使用[Homebrew](https://homebrew.sh)安裝所有依賴項：

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. 驗證[CMake](https://cmake.org/)是否至少是版本**2.8.12**：

    ```bash
    cmake --version
    ```

1. [將 CURL 修補](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os)到最新版本。

1. 在包含生成代碼的資料夾中，克隆[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存儲庫：

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    預期此作業需要幾分鐘的時間才能完成。

1. 在包含生成的代碼`cmake`的資料夾下創建一個資料夾，然後導航到該資料夾。

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 運行以下命令以使用 CMake 構建設備 SDK 和生成的程式碼端：

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. 生成成功後，運行指定 IoT 中心設備連接字串作為參數的應用程式。

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>通過重新生成骨架代碼進行反覆運算

如果更新 DCM 或介面檔，代碼產生器可以重新生成代碼。 假設您已經從 DCM 檔生成了設備代碼，以便重新生成代碼：

1. 開啟含有 DCM 檔案的資料夾，使用 **Ctrl+Shift+P** 開啟命令選擇區，輸入 **IoT 隨插即用**，然後選取 [產生裝置程式碼 Stub]****。

1. 選擇您更新的 DCM 檔。

1. 選擇**重新生成 [專案名稱] 的代碼**。

1. 代碼產生器使用您配置的上一個設置並重新生成代碼。 但是，它不會覆蓋可能包含使用者代碼（如`main.c`和`{project_name}_impl.c`） 的檔。

> [!NOTE]
> 如果更新介面檔中的 URN ID，則將其視為新介面。 重新生成代碼時，代碼產生器會為介面生成代碼，但不會覆蓋`{project_name}_impl.c`檔中的原始代碼。

## <a name="problems-and-feedback"></a>問題和回饋

Azure IoT 工具是 GitHub 上的開源專案。 對於任何問題和功能請求，您可以在[GitHub 上創建問題](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)。

## <a name="next-steps"></a>後續步驟

在本文中，您已經瞭解如何使用 Visual Studio 和 Visual Studio 代碼生成骨架 C 代碼來實現設備應用程式。 建議的下一步是瞭解如何[安裝和使用 Azure IoT 資源管理器](./howto-install-iot-explorer.md)工具。
