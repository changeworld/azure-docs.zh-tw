---
title: 快速入門 - 使用 C 將模擬 X.509 裝置佈建到 Azure IoT 中樞
description: 本快速入門使用個別註冊。 在此快速入門中，您將使用適用於 Azure IoT 中樞裝置佈建服務 (DPS) 的 C 裝置 SDK 來建立及佈建模擬 X.509 裝置。
author: wesmc7777
ms.author: wesmc
ms.date: 04/26/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 42636695113dd824797d1a837e21ab2901d87738
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91323858"
---
# <a name="quickstart-provision-an-x509-simulated-device-using-the-azure-iot-c-sdk"></a>快速入門：使用 Azure IoT C SDK 佈建 X.509 模擬裝置

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

在本快速入門中，您將了解如何在 Windows 開發機器上建立和執行 X.509 裝置模擬器。 您將設定此模擬裝置，以使用裝置佈建服務執行個體的註冊將其指派給 IoT 中樞。 來自 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的程式碼範例將用來模擬裝置的開機順序。 裝置會根據佈建服務的註冊來通過辨識，並指派到 IoT 中樞。

如果您不熟悉自動佈建程序，請檢閱[佈建](about-iot-dps.md#provisioning-process)概觀。 此外，繼續進行此快速入門之前，請務必完成[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](quick-setup-auto-provision.md)中的步驟。 

Azure IoT 裝置佈建服務支援兩種類型的註冊：

* [註冊群組](concepts-service.md#enrollment-group)：用來註冊多個相關的裝置。
* [個別註冊](concepts-service.md#individual-enrollment)：用來註冊單一裝置。

本文將示範個別註冊。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

下列必要條件適用於 Windows 開發環境。 針對 Linux 或 macOS，請參閱 SDK 文件中[準備您的開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的適當章節。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 並啟用[使用 C++ 的桌面開發](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作負載。 也會支援 Visual Studio 2015 和 Visual Studio 2017。

* 已安裝最新版的 [Git](https://git-scm.com/download/)。

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>準備適用於 Azure IoT C SDK 的開發環境

在本節中，您會準備開發環境，以用來建置 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)，其中包括 X.509 開機順序的範例程式碼。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始安裝 `CMake`**之前**，請務必將 Visual Studio 先決條件 (Visual Studio 和「使用 C++ 進行桌面開發」工作負載) 安裝在您的機器上。 在符合先決條件，並且驗證過下載項目之後，請安裝 CMake 建置系統。

2. 尋找[最新版本](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK 的標籤名稱。

3. 開啟命令提示字元或 Git Bash 殼層。 執行下列命令以複製最新版的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫。 使用您在上一個步驟中找到的標籤作為 `-b` 參數的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    預期此作業需要幾分鐘的時間才能完成。

4. 在 git 存放庫的根目錄中建立 `cmake` 子目錄，並瀏覽至該資料夾。 從 `azure-iot-sdk-c` 目錄執行下列命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 程式碼範例會使用 X.509 憑證來提供透過 X.509 驗證的證明。 執行下列命令以建置開發平台專屬的 SDK 版本，其包含裝置佈建用戶端。 `cmake` 目錄中會產生模擬裝置的 Visual Studio 解決方案。

    ```cmd
    cmake -Duse_prov_client:BOOL=ON ..
    ```

    如果 `cmake` 找不到 C++ 編譯，您在執行上述命令時，可能會收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)中執行此命令。

    建置成功後，最後幾行輸出會類似於下列輸出：

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: C:/code/azure-iot-sdk-c/cmake
    ```

## <a name="create-a-self-signed-x509-device-certificate"></a>建立自我簽署的 X.509 裝置憑證

在本節中，您會使用自我簽署 X.509 憑證，請務必記住下列幾點：

* 自我簽署憑證僅適用於測試，不應該用於生產環境。
* 自我簽署憑證的預設到期日為一年。

您會使用 Azure IoT C SDK 中的範例程式碼建立憑證，以便與模擬裝置的個別註冊項目搭配使用。

1. 啟動 Visual Studio，並開啟名為 `azure_iot_sdks.sln` 的新解決方案檔案。 此解決方案檔案位於您先前在 azure-iot-sdk-c git 存放庫根目錄中建立的 `cmake` 資料夾內。

2. 在 Visual Studio 功能表中，選取 [建置]   > [建置解決方案]  ，以建置方案中的所有專案。

3. 在 Visual Studio 的 [方案總管]  視窗中，瀏覽至 **Provision\_Tools** 資料夾。 以滑鼠右鍵按一下 **dice\_device\_enrollment** 專案，然後選取 [設為起始專案]  。

4. 在 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以執行解決方案。 在輸出視窗中，當出現提示時，針對個別註冊輸入 **i**。

    輸出視窗會顯示本機為模擬裝置產生的自我簽署 X.509 憑證。 將輸出複製到剪貼簿 (從 **-----BEGIN CERTIFICATE-----** 開始並以第一個 **-----END CERTIFICATE-----** 作為結束)，請確定包含這兩行文字。 您只需要輸出視窗中的第一個憑證。

5. 使用文字編輯器，將憑證儲存為名為 **_X509testcert.pem_** 的新檔案。

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>在入口網站中建立裝置註冊項目

1. 登入 Azure 入口網站，選取左側功能表上的 [所有資源]  按鈕，然後開啟您的裝置佈建服務。

2. 選取 [管理註冊]  索引標籤，然後選取頂端的 [新增個別註冊]  按鈕。

3. 在 [新增註冊]  面板中，輸入下列資訊，然後按 [儲存]  按鈕。

    * **機制：** 選取 [X.509]  作為身分識別證明「機制」  。
    * **主要憑證 .pem 或 .cer 檔案：** 選取 [選取檔案]  以選取您先前建立的 X509testcert.pem 憑證檔案。
    * **IoT 中樞裝置識別碼：** 輸入 **test-docs-cert-device** 作為裝置的識別碼。

      [![在入口網站中新增 X.509 證明的個別註冊](./media/quick-create-simulated-device-x509/device-enrollment.png)](./media/quick-create-simulated-device-x509/device-enrollment.png#lightbox)

      註冊成功時，您的 X.509 裝置會在 [個別註冊]  索引標籤之下的 [註冊識別碼]  資料行中顯示為 **riot-device-cert**。 

## <a name="simulate-first-boot-sequence-for-the-device"></a>模擬裝置的第一個開機順序

在本節中，您可以更新範例程式碼來將裝置的開機順序傳送至裝置佈建服務執行個體。 此開機順序會使裝置經過辨識，並指派給連結至裝置佈建服務執行個體的 IoT 中樞。

1. 在 Azure 入口網站中，選取您裝置佈建服務的 [概觀]  索引標籤，並記下 [識別碼範圍]  值。

    ![從入口網站刀鋒視窗擷取裝置佈建服務端點資訊](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 的 [方案總管]  視窗中，瀏覽至 **Provision\_Samples** 資料夾。 展開名為 **prov\_dev\_client\_sample** 的範例專案。 展開 [來源檔案]  ，然後開啟 **prov\_dev\_client\_sample.c**。

3. 找出 `id_scope` 常數，並將其值取代為您先前複製的 [識別碼範圍]  值。 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. 在相同的檔案中找出 `main()` 函式的定義。 請確定 `hsm_type` 變數已設為 `SECURE_DEVICE_TYPE_X509`，而不是 `SECURE_DEVICE_TYPE_TPM`，如下所示。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. 以滑鼠右鍵按一下 **prov\_dev\_client\_sample** 專案，然後選取 [設定為起始專案]  。

6. 在 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以執行解決方案。 出現重新建置專案的提示時，選取 [是]  ，以在執行前重新建置專案。

    下列輸出範例指出佈建裝置用戶端範例已成功啟動，並且已連線至佈建服務執行個體，可取得 IoT 中樞資訊並進行註冊：

    ```cmd
    Provisioning API Version: 1.2.7

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-cert-device
    ```

7. 在入口網站中，瀏覽到連結至您佈建服務的 IoT 中樞並選取 [IoT 裝置]  索引標籤。X.509 模擬裝置成功佈建到 IoT 中樞時，其裝置識別碼會出現在 [IoT 裝置]  刀鋒視窗上，且 [狀態]  顯示為 [已啟用]  。 您可能需要按頂端的 [重新整理]  按鈕。 

    ![已向 IoT 中樞註冊裝置](./media/quick-create-simulated-device-x509/hub-registration.png) 

## <a name="clean-up-resources"></a>清除資源

如果您打算繼續使用並探索裝置用戶端範例，請勿清除在此快速入門中建立的資源。 如果您不打算繼續，請使用下列步驟來刪除本快速入門建立的所有資源。

1. 在您的電腦上關閉裝置用戶端範例輸出視窗。
1. 從 Azure 入口網站的左側功能表中，選取 [所有資源]  ，然後選取您的裝置佈建服務。 為您的服務開啟 [管理註冊]  ，然後選取 [個別註冊]  索引標籤。選取您在本快速入門中所註冊裝置的 [註冊識別碼]  旁的核取方塊，然後按窗格頂端的 [刪除]  按鈕。 
1. 從 Azure 入口網站的左側功能表中，選取 [所有資源]  ，然後選取您的 IoT 中樞。 開啟您中樞的 [IoT 裝置]  ，選取您在本快速入門所註冊裝置的 [裝置識別碼]  旁的核取方塊，然後按窗格頂端的 [刪除]  按鈕。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已在 Windows 電腦上建立 X.509 模擬裝置，並使用入口網站上的 Azure IoT 中樞裝置佈建服務，將它佈建到 IoT 中樞。 若要了解如何以程式設計方式註冊您的 X.509 裝置，請繼續閱讀以程式設計方式註冊 X.509 裝置的快速入門。 

> [!div class="nextstepaction"]
> [Azure 快速入門 - 向 Azure IoT 中樞裝置佈建服務註冊 X.509 裝置](quick-enroll-device-x509-java.md)
