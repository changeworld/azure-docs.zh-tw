---
title: 建立測試憑證-Azure IoT Edge |Microsoft Docs
description: 建立測試憑證，並瞭解如何將它們安裝在 Azure IoT Edge 裝置上，以準備進行生產環境部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/23/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9540913cd86b74fd51e96aa9d1d1dd34c5d60631
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82129801"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>建立示範憑證來測試 IoT Edge 裝置功能

IoT Edge 裝置需要在執行時間、模組和任何下游裝置之間進行安全通訊的憑證。
如果您沒有憑證授權單位單位來建立所需的憑證，您可以使用示範憑證來試用您的測試環境中 IoT Edge 功能。
本文說明 IoT Edge 提供給測試之憑證產生腳本的功能。

這些憑證會在30天后到期，且不應用於任何生產案例中。

您可以在任何電腦上建立憑證，然後將它們複製到您的 IoT Edge 裝置。
您可以更輕鬆地使用主要機器來建立憑證，而不是在您的 IoT Edge 裝置本身產生憑證。
藉由使用您的主要電腦，您可以設定腳本一次，然後重複此程式來建立多個裝置的憑證。

請遵循下列步驟來建立示範憑證，以測試您的 IoT Edge 案例：

1. [設定腳本](#set-up-scripts)，以在您的裝置上產生憑證。
2. 建立用來為您的案例簽署所有其他憑證的[根 CA 憑證](#create-root-ca-certificate)。
3. 針對您想要測試的案例產生所需的憑證：
   * [建立 IoT Edge 裝置身分識別憑證](#create-iot-edge-device-identity-certificates)，以測試 IoT 中樞裝置佈建服務的自動布建。
   * [建立 IoT Edge 裝置 CA 憑證](#create-iot-edge-device-ca-certificates)，以測試生產案例或閘道案例。
   * [建立下游裝置憑證](#create-downstream-device-certificates)來測試驗證下游裝置，以在閘道案例中 IoT 中樞。

## <a name="prerequisites"></a>先決條件

已安裝 Git 的開發電腦。

## <a name="set-up-scripts"></a>設定指令碼

GitHub 上的 IoT Edge 存放庫包含憑證產生腳本，可讓您用來建立示範憑證。
本節提供在 Windows 或 Linux 上準備腳本以在電腦上執行的指示。
如果您是在 Linux 電腦上，請直接跳至 [[在 linux 上設定](#set-up-on-linux)]。

### <a name="set-up-on-windows"></a>在 Windows 上設定

若要在 Windows 裝置上建立示範憑證，您必須先安裝 OpenSSL，然後再複製產生腳本，並將其設定為在 PowerShell 中于本機執行。

#### <a name="install-openssl"></a>安裝 OpenSSL

在您用來產生憑證的電腦上，安裝 Windows 適用的 OpenSSL。
如果您已在 Windows 裝置上安裝 OpenSSL，則可以略過此步驟，但請確定您的 PATH 環境變數中可使用 OpenSSL。

有幾種方式可以安裝 OpenSSL，包括下列選項：

* **更簡單：** 下載並安裝任何[協力廠商 OpenSSL 二進位](https://wiki.openssl.org/index.php/Binaries)檔，例如，來自[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)。 將 openssl.exe 的完整路徑加入至您的 PATH 環境變數。

* **建議**：自行下載 OpenSSL 原始程式碼，並在您的電腦上建置二進位檔，或是透過 [vcpkg](https://github.com/Microsoft/vcpkg) 來執行。 下列指示會使用 vcpkg 下載原始程式碼，並在 Windows 電腦上編譯並安裝 OpenSSL，且各項作業都能以輕鬆的步驟完成。

   1. 瀏覽至要安裝 vcpkg 的目錄。 依照指示下載並安裝 [vcpkg](https://github.com/Microsoft/vcpkg)。

   2. 安裝 vcpkg 之後，請從 PowerShell 提示字元執行下列命令，以安裝適用于 Windows x64 的 OpenSSL 套件。 安裝通常需要大約5分鐘的時間才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. 將 `<vcpkg path>\installed\x64-windows\tools\openssl` 新增至您的 PATH 環境變數，讓 openssl.exe 檔案可供叫用。

#### <a name="prepare-scripts-in-powershell"></a>在 PowerShell 中準備腳本

Azure IoT Edge git 存放庫包含可用來產生測試憑證的腳本。
在本節中，您會複製 IoT Edge 存放庫並執行腳本。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。

2. 複製 IoT Edge git 存放庫，其中包含用來產生示範憑證的腳本。 使用 `git clone` 命令或[下載 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 瀏覽至您要使用的目錄。 在本文中，我們將>呼叫此目錄* \<WRKDIR *。 所有憑證和金鑰都會在此工作目錄中建立。

4. 將設定檔案中的設定和腳本檔案複製到您的工作目錄。

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果您將存放庫下載為 ZIP，則資料夾名稱會是`iotedge-master` ，而且路徑的其餘部分會相同。

5. 啟用 PowerShell 執行指令碼。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 將腳本使用的函式帶入 PowerShell 的全域命名空間。

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 視窗會顯示警告，指出此腳本所產生的憑證僅供測試之用，不應用於實際執行的案例。

7. 確認 OpenSSL 已正確安裝，並確定不會與現有憑證產生名稱衝突。 如果有問題，腳本輸出應該會說明如何在您的系統上加以修正。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>在 Linux 上設定

若要在 Windows 裝置上建立示範憑證，您需要複製產生腳本，並將其設定為在 bash 中于本機執行。

1. 複製 IoT Edge git 存放庫，其中包含用來產生示範憑證的腳本。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 瀏覽至您要使用的目錄。 我們將在本文中參考此目錄，做為* \<WRKDIR>*。 所有憑證和金鑰檔案都會在此目錄中建立。
  
3. 將設定檔案和腳本檔案從複製的 IoT Edge 存放庫複製到您的工作目錄。

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>建立根 CA 憑證

根 CA 憑證是用來將所有其他示範憑證用於測試 IoT Edge 案例。
您可以繼續使用相同的根 CA 憑證，為多個 IoT Edge 或下游裝置提出示範憑證。

如果您的工作資料夾中已經有一個根 CA 憑證，請勿建立一個新的。
新的根 CA 憑證將會覆寫舊的，而且從舊憑證進行的任何下游憑證都會停止運作。
如果您想要多個根 CA 憑證，請務必在不同的資料夾中管理它們。

繼續進行本節中的步驟之前，請遵循[設定腳本](#set-up-scripts)一節中的步驟，使用示範憑證產生腳本來準備工作目錄。

### <a name="windows"></a>Windows

1. 流覽至您放置憑證產生腳本的工作目錄。

1. 建立根 CA 憑證，並讓它簽署一個中繼憑證。 憑證全都放在您的工作目錄中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此指令碼命令會建立數個憑證和金鑰檔案，但當文章要求**根 CA 憑證**時，請使用下列檔案：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 流覽至您放置憑證產生腳本的工作目錄。

1. 建立根 CA 憑證和一個中繼憑證。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此指令碼命令會建立數個憑證和金鑰檔案，但當文章要求**根 CA 憑證**時，請使用下列檔案：

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>建立 IoT Edge 裝置 CA 憑證

進入生產環境的每個 IoT Edge 裝置都需要 yaml 檔案所參考的裝置 CA 憑證。
裝置 CA 憑證負責為裝置上執行的模組建立憑證。
這也是 IoT Edge 裝置連線到下游裝置時驗證其身分識別的方式。

裝置 CA 憑證會移至 IoT Edge 裝置上 yaml 檔案的**Certificate**區段中。

繼續進行本節中的步驟之前，請遵循[設定腳本](#set-up-scripts)和[建立根 CA 憑證](#create-root-ca-certificate)一節中的步驟。

### <a name="windows"></a>Windows

1. 流覽至具有 [憑證產生腳本] 和 [根 CA 憑證] 的工作目錄。

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱，例如**MyEdgeDeviceCA**，用來命名輸出檔案。

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到 IoT Edge 裝置上，並在 yaml 檔案中參考：

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

傳入這些腳本的閘道裝置名稱不應與 yaml 中的 "hostname" 參數相同，或 IoT 中樞中的裝置識別碼。
如果使用者在兩個位置中使用相同的名稱設定 IoT Edge，則這些指令碼可將「.ca」字串附加到閘道裝置名稱上來避免名稱衝突，進而發生問題。
不過，最好避免使用相同的名稱。

### <a name="linux"></a>Linux

1. 流覽至具有 [憑證產生腳本] 和 [根 CA 憑證] 的工作目錄。

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱，例如**MyEdgeDeviceCA**，用來命名輸出檔案。

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到 IoT Edge 裝置上，並在 yaml 檔案中參考：

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

傳入這些腳本的閘道裝置名稱不應與 yaml 中的 "hostname" 參數相同，或 IoT 中樞中的裝置識別碼。
如果使用者在兩個位置中使用相同的名稱設定 IoT Edge，則這些指令碼可將「.ca」字串附加到閘道裝置名稱上來避免名稱衝突，進而發生問題。
不過，最好避免使用相同的名稱。

## <a name="create-iot-edge-device-identity-certificates"></a>建立 IoT Edge 裝置身分識別憑證

裝置身分識別憑證是用來透過[Azure IoT 中樞裝置布建服務（DPS）](../iot-dps/index.yml)布建 IoT Edge 裝置。

裝置身分識別憑證會進入 IoT Edge 裝置上 yaml**檔案的布建區段。**

繼續進行本節中的步驟之前，請遵循[設定腳本](#set-up-scripts)和[建立根 CA 憑證](#create-root-ca-certificate)一節中的步驟。

### <a name="windows"></a>Windows

使用下列命令建立 IoT Edge 裝置身分識別憑證和私密金鑰：

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

您傳入此命令的名稱將是 IoT 中樞中 IoT Edge 裝置的裝置識別碼。

新的裝置身分識別命令會建立數個憑證和金鑰檔案，包括在 DPS 中建立個別註冊並安裝 IoT Edge 執行時間時，將使用的三個：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

使用下列命令建立 IoT Edge 裝置身分識別憑證和私密金鑰：

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

您傳入此命令的名稱將是 IoT 中樞中 IoT Edge 裝置的裝置識別碼。

此腳本會建立數個憑證和金鑰檔案，包括在 DPS 中建立個別註冊並安裝 IoT Edge 執行時間時，將使用的三個：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>建立下游裝置憑證

如果您要為閘道案例設定下游 IoT 裝置，您可以產生 x.509 authentication 的示範憑證。
使用 x.509 憑證驗證 IoT 裝置的方法有兩種：使用自我簽署憑證，或使用憑證授權單位單位（CA）簽署憑證。
針對 x.509 自我簽署驗證，有時也稱為指紋驗證，您必須建立新的憑證以放在您的 IoT 裝置上。
這些憑證的指紋會與您共用 IoT 中樞以進行驗證。
針對 x.509 憑證授權單位單位（CA）簽署的驗證，您需要在 IoT 中樞中註冊的根 CA 憑證，以用來簽署 IoT 裝置的憑證。
任何使用由根 CA 憑證或其中繼憑證所發行之憑證的裝置，都將允許進行驗證。

憑證產生腳本可協助您進行示範憑證，以測試其中一種驗證案例。

繼續進行本節中的步驟之前，請遵循[設定腳本](#set-up-scripts)和[建立根 CA 憑證](#create-root-ca-certificate)一節中的步驟。

### <a name="self-signed-certificates"></a>自我簽署憑證

當您使用自我簽署憑證來驗證 IoT 裝置時，您必須根據解決方案的根 CA 憑證來建立裝置憑證。
然後，您會從要提供給 IoT 中樞的憑證中取出十六進位「指紋」。
您的 IoT 裝置也需要其裝置憑證的複本，才能使用 IoT 中樞進行驗證。

#### <a name="windows"></a>Windows

1. 流覽至具有 [憑證產生腳本] 和 [根 CA 憑證] 的工作目錄。

2. 建立下游裝置的兩個憑證（主要和次要）。 簡單的命名慣例是使用 IoT 裝置的名稱，然後是主要或次要標籤來建立憑證。 例如：

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連接到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 從每個憑證取出 SHA1 指紋（在 IoT 中樞內容中稱為指紋）。 指紋是40的十六進位字元字串。 使用下列 openssl 命令來查看憑證並尋找指紋：

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   執行此命令兩次，一次用於主要憑證，一次用於次要憑證。 當您使用自我簽署的 x.509 憑證註冊新的 IoT 裝置時，您會為這兩個憑證提供指紋。

#### <a name="linux"></a>Linux

1. 流覽至具有 [憑證產生腳本] 和 [根 CA 憑證] 的工作目錄。

2. 建立下游裝置的兩個憑證（主要和次要）。 簡單的命名慣例是使用 IoT 裝置的名稱，然後是主要或次要標籤來建立憑證。 例如：

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連接到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 從每個憑證取出 SHA1 指紋（在 IoT 中樞內容中稱為指紋）。 指紋是40的十六進位字元字串。 使用下列 openssl 命令來查看憑證並尋找指紋：

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   當您使用自我簽署的 x.509 憑證註冊新的 IoT 裝置時，您會提供主要和次要指紋。

### <a name="ca-signed-certificates"></a>CA 簽署的憑證

當您使用自我簽署憑證驗證 IoT 裝置時，您需要將解決方案的根 CA 憑證上傳至 IoT 中樞。
然後，您會執行驗證，以證明您擁有根 CA 憑證 IoT 中樞。
最後，您會使用相同的根 CA 憑證來建立裝置憑證，以放入您的 IoT 裝置上，讓它可以 IoT 中樞進行驗證。

本節中的憑證適用于在[您的 Azure IoT 中樞中設定 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)中的步驟。

#### <a name="windows"></a>Windows

1. 從您的工作目錄， `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`將根 CA 憑證檔案上傳到您的 IoT 中樞。

2. 使用 Azure 入口網站中提供的程式碼，確認您擁有該根 CA 憑證。

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 建立下游裝置的憑證鏈。 在 IoT 中樞中，使用與註冊裝置相同的裝置識別碼。

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連接到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 從您的工作目錄， `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`將根 CA 憑證檔案上傳到您的 IoT 中樞。

2. 使用 Azure 入口網站中提供的程式碼，確認您擁有該根 CA 憑證。

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 建立下游裝置的憑證鏈。 在 IoT 中樞中，使用與註冊裝置相同的裝置識別碼。

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連接到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
