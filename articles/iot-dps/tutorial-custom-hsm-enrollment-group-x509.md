---
title: 教學課程 - 使用自訂硬體安全模組 (HSM) 將 x.509 裝置佈建到 Azure IoT 中樞
description: 本教學課程使用註冊群組。 在本教學課程中，您將了解如何使用自訂的硬體安全性模組 (HSM) 和適用於 Azure IoT 中樞裝置佈建服務 (DPS) 的 C 裝置 SDK 來佈建 x.509 裝置。
author: wesmc7777
ms.author: wesmc
ms.date: 11/18/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 566563dde26d2dd36f4358bc8c6dcdcfb5ba8465
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954846"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>教學課程：使用註冊群組佈建多個 x.509 裝置

在本教學課程中，您將了解如何佈建使用 x.509 憑證進行驗證的 IoT 裝置群組。 我們會使用 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 範例程式碼用將您的開發機器佈建為 IoT 裝置。 

Azure IoT 裝置佈建服務支援兩種類型的註冊：

* [註冊群組](concepts-service.md#enrollment-group)：用來註冊多個相關的裝置。
* [個別註冊](concepts-service.md#individual-enrollment)：用來註冊單一裝置。

本教學課程類似於先前的教學課程，示範如何使用註冊群組來佈建裝置集合。 不過，本教學課程將使用 x.509 憑證，而非對稱金鑰。 請檢閱本節中先前的教學課程，了解使用[對稱金鑰](./concepts-symmetric-key-attestation.md)的簡單方法。

本教學課程將示範[自訂 HSM 範例](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example)，其會提供可與硬體型安全存放裝置互動的存根執行。 [硬體安全模組 (HSM)](./concepts-service.md#hardware-security-module) 是用來保護裝置祕密的安全硬體型存放裝置。 HSM 可以搭配對稱金鑰、x.509 憑證或 TPM 證明使用，以提供祕密的安全儲存體。 裝置秘密不需要硬體型存儲存體，但強烈建議您協助保護敏感性資訊，例如裝置憑證的私密金鑰。

如果您不熟悉自動佈建程序，請檢閱[佈建](about-iot-dps.md#provisioning-process)概觀。 此外，繼續進行此教學課程之前，請務必完成[使用 Azure 入口網站設定 IoT 中樞裝置佈建服務](quick-setup-auto-provision.md)中的步驟。 


在本教學課程中，您將完成下列目標：

> [!div class="checklist"]
> * 建立信任鏈，以使用 x.509 憑證來組織一組裝置。
> * 使用與憑證鏈結搭配使用的簽署憑證來完成擁有權證明。
> * 建立使用憑證鏈結的新群組註冊
> * 從 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 使用程式碼設定開發環境以佈建裝置
> * 在 SDK 中使用憑證鏈結和自訂硬體安全模組 (HSM) 範例來佈建裝置。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prerequisites

下列必要條件適用於 Windows 開發環境。 針對 Linux 或 macOS，請參閱 SDK 文件中[準備您的開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)中的適當章節。

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 並啟用[使用 C++ 的桌面開發](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作負載。 也會支援 Visual Studio 2015 和 Visual Studio 2017。

* 已安裝最新版的 [Git](https://git-scm.com/download/)。

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>準備 Azure IoT C SDK 開發環境

在此節中，您會準備用來建置 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的開發環境。 SDK 包括使用 DPS 佈建 x.509 裝置所使用的範例程式碼和工具。

1. 下載 [CMake 建置系統](https://cmake.org/download/)。

    在開始安裝 `CMake` **之前**，請務必將 [Visual Studio](https://visualstudio.microsoft.com/vs/) 先決條件 (Visual Studio 和 [「使用 C++ 進行桌面開發」](/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)工作負載) 安裝在您的機器上。 在符合先決條件，並且驗證過下載項目之後，請安裝 CMake 建置系統。

2. 尋找[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) Azure IoT C SDK 的標籤名稱。

3. 開啟命令提示字元或 Git Bash 殼層。 執行下列命令以複製最新版的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存放庫。 使用您在上一個步驟中找到的標籤作為 `-b` 參數的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    預期此作業需要幾分鐘的時間才能完成。

4. 在 git 存放庫的根目錄中建立 `cmake` 子目錄，並瀏覽至該資料夾。 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 您建立的 `cmake` 目錄將包含自訂的 HSM 範例，以及使用自訂 HSM 來提供 x.509 驗證的範例裝置佈建程式碼。 

    在 `cmake` 目錄執行下列命令 (該命令會建置您開發平台特有的 SDK 版本)。 組建會包含自訂 HSM 範例的參考。 

    指定與下面 `-Dhsm_custom_lib` 搭配使用的路徑時，請務必使用與您先前建立的 `cmake` 目錄相對的路徑。 以下顯示的相對路徑只是一個範例。

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    如果 `cmake` 找不到 C++ 編譯，您在執行上述命令時，可能會收到建置錯誤。 如果發生這種情況，請嘗試在 [Visual Studio 命令提示字元](/dotnet/framework/tools/developer-command-prompt-for-vs)中執行此命令。

    建置成功之後，就會在您的 `cmake` 目錄中產生 Visual Studio 的解決方案。 最後幾行輸出會類似於下列輸出：

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>建立 X.509 憑證鏈結

在本節中，您將會產生三個憑證的 x.509 憑證鏈結，以便在本教學課程中進行測試。 憑證將具有下列階層。

![教學課程裝置憑證鏈結](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[根憑證](concepts-x509-attestation.md#root-certificate)：您將會完成[擁有權證明](how-to-verify-certificates.md)，以確認根憑證。 這項驗證會讓 DPS 信任該憑證，並驗證其所簽署的憑證。

[中繼憑證](concepts-x509-attestation.md#intermediate-certificate)：中繼憑證通常是用來根據產品線、公司部門或其他準則，以邏輯方式將裝置分組。 本教學課程將使用由一個中繼憑證組成的憑證鏈結。 根憑證將會簽署中繼憑證。 在 DPS 中建立的註冊群組也會使用此憑證，以邏輯方式將一組裝置分組。 此組態可讓您管理具有相同中繼憑證所簽署之裝置憑證的整個裝置群組。 您可以建立註冊群組來啟用或停用一組裝置。 如需停用裝置群組的詳細資訊，請參閱 [不允許使用註冊群組停用 x.509 中繼或根 CA 憑證](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group)

[裝置憑證](concepts-x509-attestation.md#end-entity-leaf-certificate)：裝置 (分葉) 憑證會由中繼憑證簽署，並連同其私密金鑰一起儲存在裝置上。 當您嘗試佈建時，裝置將會顯示此憑證和私密金鑰，以及憑證鏈結。 

若要建立憑證鏈結：

1. 開啟 Git Bash 命令提示字元。 使用[管理範例和教學課程的測試 CA 憑證](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials)中的 Bash shell 指示，完成步驟 1 和 2。

    此步驟會建立憑證指令碼的工作目錄，並使用 openssl 產生憑證鏈結的範例根和中繼憑證。 

    請注意，輸出中會顯示自我簽署根憑證的位置。 此憑證將會透過[擁有權證明](how-to-verify-certificates.md)，在稍後驗證擁有權。

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        

    請注意，輸出中會顯示根憑證簽署/發佈的中繼憑證位置。 此憑證將會用於您稍後建立的註冊群組。

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    

2. 接下來，執行下列命令來建立新的裝置/分葉憑證，其主體名稱為您提供的參數。 使用本教學課程所提供的範例主體名稱，`custom-hsm-device-01`。 此主體名稱將是您 IoT 裝置的裝置識別碼。 

    > [!WARNING]
    > 請勿在使用具有空格的主體名稱。 此主體名稱是所佈建 IoT 裝置的裝置識別碼。 名稱必須遵照裝置識別碼的規則。 如需詳細資訊，請參閱[裝置身分識別屬性](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties)。

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    請注意下列輸出，其中顯示新裝置憑證的所在位置。 中繼憑證會簽署 (發行) 裝置憑證。

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
3. 執行下列命令建立完整的憑證鏈結 .pem 檔案，其中包含新的裝置憑證。

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-full-chain.cert.pem && cd ..
    ```

    使用文字編輯器並開啟憑證鏈結檔案，./certs/new-device-full-chain.cert.pem。 憑證鏈結文字包含全部三個憑證的完整鏈結。 在本教學課程的後續內容中，您將使用此文字作為憑證鏈結與自訂 HSM 程式碼。

    完整的鏈結文字具有下列格式：
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

5. 請注意，新裝置憑證的私密金鑰會寫入 *./private/new-device.key.pem*。 裝置在佈建期間需要此金鑰的文字。 稍後將會在自訂的 HSM 範例中新增文字。

    > [!WARNING]
    > 憑證的文字僅包含公開金鑰資訊。 
    >
    > 不過，裝置也必須能夠存取裝置憑證的私密金鑰。 這是必要的，因為在嘗試佈建時，裝置必須在執行時間使用該金鑰來執行驗證。 此金鑰的敏感性是建議在實際的 HSM 中使用硬體型存放裝置，以協助保護私密金鑰的其中一個主要原因。



## <a name="configure-the-custom-hsm-stub-code"></a>設定自訂 HSM 虛設常式程式碼

與實際安全硬體型存放裝置互動的細節會因硬體而異。 因此，本教學課程中的裝置所使用的憑證鏈將會在自訂 HSM 虛設常式程式碼中進行硬式編碼。 在實際案例中，憑證鏈結會儲存在實際的 HSM 硬體中，為機密資訊提供更理想的安全性。 接著會執行與此範例中所示之虛設常式類似的方法，從該硬體型存放裝置讀取祕密。 

雖然不需要 HSM 硬體，但並不建議將敏感性資訊 (例如憑證的私密金鑰) 簽入原始程式碼。 這會將金鑰公開給任何可檢視程式碼的人。 此只會在本文中操作，目的是協助您學習。

若要更新本教學課程的自訂 HSM 虛設常式程式碼：

1. 啟動 Visual Studio 並開啟新的解決方案檔，此檔案是在您於 azure-iot-sdk-c git 存放庫之根目錄中建立的 `cmake` 目錄中建立。 解決方案檔命名為 `azure_iot_sdks.sln`。

2. 在 Visual Studio 的方案總管中，瀏覽至 **Provisioning_Samples > custom_hsm_example > 來源檔案** 並開啟 custom_hsm_example.c。

3. 使用您在產生裝置憑證時所使用的一般名稱，更新 `COMMON_NAME` 字串常數的字串值。

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

4. 在相同的檔案產生憑證之後中，您必須使用在 *./certs/new-device-full-chain.cert.pem* 中儲存的憑證鏈結文字更新 `CERTIFICATE` 常數位串的字串。

    憑證文字語法必須遵循下列模式，才不會有額外的空格，且 Visual Studio 也不會剖析。

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy\n"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh\n"
    "-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV\n"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB\n"
    "-----END CERTIFICATE-----";        
    ```

    在此步驟中正確地更新此字串值的步驟非常繁瑣，且可能會發生錯誤。 若要在您的 Git Bash 提示字元中產生適當的語法，請將下列 Bash 殼層命令複製並貼到您的 Git Bash 命令提示字元中，然後按 **ENTER**。 這些命令將會產生 `CERTIFICATE` 字串常數值的語法。

    ```Bash
    input="./certs/new-device-full-chain.cert.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    複製並貼上新常數值的輸出憑證文字。 


5. 在相同的檔案中，必須使用您裝置憑證的私密金鑰來更新 `PRIVATE_KEY` 常數的字串值。

    私密金鑰文字的語法必須遵循下列模式，才不會有額外的空格，且 Visual Studio 也不會剖析。

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U\n"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij\n"
    "-----END RSA PRIVATE KEY-----";
    ```

    在此步驟中正確地更新此字串值的步驟非常繁瑣，且可能會發生錯誤。 若要在您的 Git Bash 提示字元中產生適當的語法，請將下列 Bash 殼層命令複製並貼上，然後按 **ENTER**。 這些命令將會產生 `PRIVATE_KEY` 字串常數值的語法。

    ```Bash
    input="./private/new-device.key.pem"
    bContinue=true
    prev=
    while $bContinue; do
        if read -r next; then
          if [ -n "$prev" ]; then   
            echo "\"$prev\\n\""
          fi
          prev=$next  
        else
          echo "\"$prev\";"
          bContinue=false
        fi  
    done < "$input"
    ```

    複製並貼上新常數值的輸出私密金鑰。 

6. 儲存 custom_hsm_example.c。


## <a name="verify-ownership-of-the-root-certificate"></a>確認根憑證的擁有權

1. 使用 [註冊 x.509 憑證的公開部分並取得驗證碼](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code)，上傳根憑證 (`./certs/azure-iot-test-only.root.ca.cert.pem`) 並從 DPS 取得驗證碼。

2. 當根憑證有來自 DPS 的驗證碼後，請從您的憑證指令碼工作目錄執行下列命令，以產生驗證憑證。
 
    此處提供的驗證碼只是一個範例。 使用您從 DPS 產生的程式碼。    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    此指令碼會建立憑證，並以主體名稱設定為驗證碼的根憑證進行簽署。 此憑證可讓 DPS 驗證您是否有權存取根憑證的私密金鑰。 請注意指令碼輸出中的驗證憑證位置。 此憑證是以 `.pfx` 格式產生。

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. 如[上傳已簽署的驗證憑證](how-to-verify-certificates.md#upload-the-signed-verification-certificate)所述，上傳驗證憑證，然後按一下 DPS 中的[驗證] 以完成根憑證的擁有權證明。


## <a name="update-the-certificate-store-on-windows-based-devices"></a>在 Windows 型裝置上更新憑證存放區

在非 Windows 裝置上，您可以從程式碼將憑證鏈結傳遞為憑證存放區。

在 Windows 型裝置上，您必須將簽署憑證 (根和中繼憑證) 新增至 Windows [憑證存放區](/windows/win32/secauthn/certificate-stores)。 否則，系統將不會透過傳輸層安全性 (TLS) 安全通道，將簽署憑證傳輸至 DPS。

> [!TIP]
> 您也可以使用 OpenSSL，而不是使用安全通道 (Schannel) 搭配 C SDK。 如需使用 OpenSSL 的詳細資訊，請參閱在 [SDK 中使用 OpenSSL](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#using-openssl-in-the-sdk)。

若要將簽署憑證新增至 Windows 型裝置中的憑證存放區：

1. 在 Git bash 提示字元中，瀏覽至包含簽署憑證的 `certs` 子目錄並轉換成 `.pfx`，如下所示。

    根憑證：

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    中繼憑證：   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. 以滑鼠右鍵按一下 Windows [開始] 按鈕。 然後以滑鼠左鍵按一下 [執行]。 輸入 certmgr.mcs 然後按一下 [確定] 以啟動憑證管理員 MMC 嵌入式管理單元。

3. 在憑證管理員中的 **憑證 - 目前使用者** 下，按一下 [受信任的根憑證授權單位]。 然後在功能表上，按一下 [動作] > [所有工作] > [匯入] 以匯入 `root.pfx`。

    * 請務必依 **個人資訊交換 (.pfx) 搜尋**
    * 使用 `1234` 作為密碼。
    * 將憑證放在 **受信任的根憑證授權單位** 憑證存放區中。

4. 在憑證管理員中的 **憑證 - 目前使用者** 下，按一下 [中繼憑證授權單位]。 然後在功能表上，按一下 [動作] > [所有工作] > [匯入] 以匯入 `intermediate.pfx`。

    * 請務必依 **個人資訊交換 (.pfx) 搜尋**
    * 使用 `1234` 作為密碼。
    * 將憑證放在 **中繼憑證授權單位** 憑證存放區中。

您的簽署憑證現在已在 Windows 裝置上受到信任，可將完整鏈結傳輸至 DPS。



## <a name="create-an-enrollment-group"></a>建立註冊群組

1. 登入 Azure 入口網站，選取左側功能表上的 [所有資源]  按鈕，然後開啟您的裝置佈建服務。

2. 選取 [管理註冊] 索引標籤，然後選取頂端的 [新增註冊群組] 按鈕。

3. 在 [新增註冊群組] 面板中，輸入下列資訊，然後按 [儲存] 按鈕。

      ![在入口網站中新增 X.509 證明的註冊群組](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | 欄位        | 值           |
    | :----------- | :-------------- |
    | **群組名稱** | 在本教學課程中，請輸入 **custom-hsm-x509-devices** |
    | **證明類型** | 選取 [憑證] |
    | **IoT Edge 裝置** | 選取 [False] |
    | **憑證類型** | 選取 [中繼憑證] |
    | **主要憑證 .pem 或 .cer 檔案** | 瀏覽至 您稍早建立的中繼 (./certs/azure-iot-test-only.intermediate.cert.pem) |


## <a name="configure-the-provisioning-device-code"></a>設定佈建裝置程式碼

在本節中，您可以更新範例程式碼以裝置佈建服務執行個體佈建裝置。 如果裝置已通過驗證，則會指派給連結至裝置佈建服務執行個體的 IoT 中樞。

1. 在 Azure 入口網站中，選取您裝置佈建服務的 [概觀]  索引標籤，並記下 [識別碼範圍]  值。

    ![從入口網站刀鋒視窗擷取裝置佈建服務端點資訊](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. 在 Visual Studio 的方案總管中，瀏覽至 **Provisioning_Samples > prov_dev_client_sample > 來源檔案** 並開啟 prov_dev_client_sample.c。

3. 找出 `id_scope` 常數，並將其值取代為您先前複製的 [識別碼範圍]  值。 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

4. 在相同的檔案中找出 `main()` 函式的定義。 確定 `hsm_type` 變數已設定為 `SECURE_DEVICE_TYPE_X509`，如下所示。

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. 以滑鼠右鍵按一下 **prov\_dev\_client\_sample** 專案，然後選取 [設定為起始專案]  。

6. 在 Visual Studio 功能表中，選取 [偵錯]   > [啟動但不偵錯]  以執行解決方案。 出現重新建置專案的提示時，選取 [是]，以在執行前重新建置專案。

    下列輸出範例指出佈建裝置用戶端範例已成功啟動，並且已連線至佈建服務執行個體。 裝置已指派至 IoT 中樞資訊並已註冊：

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. 在入口網站中，瀏覽到連結至您佈建服務的 IoT 中樞並選取 [IoT 裝置] 索引標籤。X.509 裝置成功佈建到 IoT 中樞時，其裝置識別碼會出現在 **IoT 裝置** 刀鋒視窗上，且「狀態」顯示為 **已啟用**。 您可能需要按頂端的 [重新整理]  按鈕。 

    ![已向 IoT 中樞註冊自訂 HSM 裝置](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

## <a name="clean-up-resources"></a>清除資源

完成測試並探索此裝置用戶端範例時，請使用下列步驟來刪除本教學課程所建立的所有資源。

1. 在您的電腦上關閉裝置用戶端範例輸出視窗。
1. 從 Azure 入口網站的左側功能表中，選取 [所有資源]，然後選取您的裝置佈建服務。 為您的服務開啟 [管理註冊]，然後選取 [註冊群組]索引標籤。選取您在本教學課程中所建立之裝置群組的 [群組名稱] 旁的核取方塊，然後按窗格頂端的 [刪除] 按鈕。 
1. 按一下 DPS 中的 [憑證]。 針對您在本教學課程中上傳和驗證的每個憑證，按一下憑證，然後按一下 [刪除] 按鈕將其移除。
1. 從 Azure 入口網站的左側功能表中，選取 [所有資源]  ，然後選取您的 IoT 中樞。 開啟您中樞的 **IoT 裝置**。 選取您在本教學課程中註冊的裝置之 [裝置識別碼] 旁的核取方塊。 按一下窗格底部的 [刪除] 按鈕。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已使用自訂 HSM 將 x.509 裝置佈建到您的 IoT 中樞。 若要了解如何將 IoT 裝置佈建到多個中樞，請繼續進行下一個教學課程。 

> [!div class="nextstepaction"]
> [教學課程：跨負載平衡 IoT 中樞來佈建裝置](tutorial-provision-multiple-hubs.md)