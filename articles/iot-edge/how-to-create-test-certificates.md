---
title: 建立測試憑證-Azure IoT Edge |Microsoft Docs
description: 建立測試憑證，並瞭解如何將它們安裝在 Azure IoT Edge 裝置上，以準備進行生產部署。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66c8f72c82e04bafe9582c4a5dc6967e5470d3ea
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147886"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>建立示範憑證來測試 IoT Edge 裝置功能

IoT Edge 裝置需要憑證，才能在執行時間、模組和任何下游裝置之間進行安全通訊。
如果您沒有憑證授權單位單位可以建立所需的憑證，您可以使用示範憑證來試用您測試環境中的 IoT Edge 功能。
本文描述 IoT Edge 提供用於測試的憑證產生腳本的功能。

這些憑證將于30天內到期，而且不應該用於任何生產案例。

您可以在任何電腦上建立憑證，然後將憑證複製到您的 IoT Edge 裝置。
您可以更輕鬆地使用您的主要電腦來建立憑證，而不是在 IoT Edge 裝置本身產生憑證。
使用您的主要電腦時，您可以設定腳本一次，然後使用它們來建立多個裝置的憑證。

遵循下列步驟來建立示範憑證，以測試您的 IoT Edge 案例：

1. 在您的裝置上設定產生憑證的[腳本](#set-up-scripts)。
2. 建立用來為您的案例簽署所有其他憑證的[根 CA 憑證](#create-root-ca-certificate)。
3. 針對您想要測試的案例產生您需要的憑證：
   * [建立 IoT Edge 的裝置身分識別憑證](#create-iot-edge-device-identity-certificates) ，以使用 IoT 中樞裝置布建服務進行自動布建。
   * 在閘道案例中，為 IoT Edge 裝置[建立 IoT Edge 的裝置 CA 憑證](#create-iot-edge-device-ca-certificates)。
   * [建立下游裝置憑證](#create-downstream-device-certificates) 來驗證閘道案例中的下游裝置。

## <a name="prerequisites"></a>必要條件

已安裝 Git 的開發電腦。

## <a name="set-up-scripts"></a>設定指令碼

GitHub 上的 IoT Edge 存放庫包含可供您用來建立示範憑證的憑證產生腳本。
本節提供的指示可讓您在 Windows 或 Linux 上準備要在電腦上執行的腳本。
如果您是在 Linux 機器上，請直接跳至 [linux 上的設定](#set-up-on-linux)。

### <a name="set-up-on-windows"></a>在 Windows 上設定

若要在 Windows 裝置上建立示範憑證，您需要安裝 OpenSSL，然後複製產生腳本，並將其設定為在本機 PowerShell 中執行。

#### <a name="install-openssl"></a>安裝 OpenSSL

在您用來產生憑證的電腦上，安裝 Windows 適用的 OpenSSL。
如果您已在 Windows 裝置上安裝 OpenSSL，請確定您的 PATH 環境變數中有可用的 openssl.exe。

有幾種方式可安裝 OpenSSL，包括下列選項：

* **更簡單：** 下載並安裝任何 [協力廠商 OpenSSL 二進位](https://wiki.openssl.org/index.php/Binaries)檔，例如，從 [OpenSSL on SourceForge](https://sourceforge.net/projects/openssl/)。 將 openssl.exe 的完整路徑加入至您的 PATH 環境變數。

* **建議**：自行下載 OpenSSL 原始程式碼，並在您的電腦上建置二進位檔，或是透過 [vcpkg](https://github.com/Microsoft/vcpkg) 來執行。 下列指示會使用 vcpkg 下載原始程式碼，並在 Windows 電腦上編譯並安裝 OpenSSL，且各項作業都能以輕鬆的步驟完成。

   1. 瀏覽至要安裝 vcpkg 的目錄。 依照指示下載並安裝 [vcpkg](https://github.com/Microsoft/vcpkg)。

   2. Vcpkg 安裝完成後，請從 PowerShell 提示字元執行下列命令，以安裝適用于 Windows x64 的 OpenSSL 套件。 安裝通常需要約5分鐘的時間才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. 將 `<vcpkg path>\installed\x64-windows\tools\openssl` 新增至您的 PATH 環境變數，讓 openssl.exe 檔案可供叫用。

#### <a name="prepare-scripts-in-powershell"></a>在 PowerShell 中準備腳本

Azure IoT Edge 的 git 存放庫包含可供您用來產生測試憑證的腳本。
在本節中，您會複製 IoT Edge 存放庫，並執行腳本。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。

2. 複製 IoT Edge 的 git 存放庫，其中包含用來產生示範憑證的腳本。 使用 `git clone` 命令或[下載 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 瀏覽至您要使用的目錄。 在本文中，我們將會呼叫此目錄 *\<WRKDIR>* 。 所有憑證和金鑰都會建立在此工作目錄中。

4. 將設定檔案中的設定和腳本檔案複製到您的工作目錄。

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果您將存放庫下載為 ZIP，則資料夾名稱是 `iotedge-master` ，且路徑的其餘部分相同。

5. 啟用 PowerShell 執行指令碼。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 將腳本所使用的函式導入 PowerShell 的全域命名空間。

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 視窗會顯示一則警告，指出此腳本產生的憑證僅供測試用途，而且不應該用於生產案例。

7. 確認 OpenSSL 已正確安裝，並確定沒有與現有憑證的名稱衝突。 如果有問題，腳本輸出應該會說明如何在您的系統上修正這些問題。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>在 Linux 上設定

若要在 Windows 裝置上建立示範憑證，您需要複製產生腳本，並將其設定為在 bash 中于本機執行。

1. 複製 IoT Edge 的 git 存放庫，其中包含用來產生示範憑證的腳本。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 瀏覽至您要使用的目錄。 在本文中，我們會將此目錄參考為 *\<WRKDIR>* 。 所有憑證和金鑰檔案都會在這個目錄中建立。
  
3. 從複製的 IoT Edge 存放庫將設定和腳本檔案複製到您的工作目錄中。

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

根 CA 憑證是用來做為測試 IoT Edge 案例的所有其他示範憑證。
您可以繼續使用相同的根 CA 憑證，來為多個 IoT Edge 或下游裝置建立示範憑證。

如果您的工作資料夾中已經有一個根 CA 憑證，請勿建立一個新的。
新的根 CA 憑證將會覆寫舊的憑證，而從舊憑證建立的任何下游憑證將會停止運作。
如果您想要多個根 CA 憑證，請務必在不同的資料夾中管理它們。

繼續進行本節中的步驟之前，請依照「 [設定腳本](#set-up-scripts) 」一節中的步驟，使用示範憑證產生腳本來準備工作目錄。

### <a name="windows"></a>Windows

1. 流覽至您放置憑證產生腳本的工作目錄。

1. 建立根 CA 憑證，並將它簽署一個中繼憑證。 憑證全都放在您的工作目錄中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此指令碼命令會建立數個憑證和金鑰檔案，但當發行項要求 **根 CA 憑證**時，請使用下列檔案：

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 流覽至您放置憑證產生腳本的工作目錄。

1. 建立根 CA 憑證和一個中繼憑證。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此指令碼命令會建立數個憑證和金鑰檔案，但當發行項要求 **根 CA 憑證**時，請使用下列檔案：

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>建立 IoT Edge 的裝置身分識別憑證

裝置身分識別憑證可用來透過 Azure IoT 中樞裝置布建服務（ (DPS) ）布建 IoT Edge 裝置。

裝置身分識別憑證會進入 IoT Edge 裝置上 yaml**檔案的布建區段。**

繼續進行本節中的步驟之前，請先依照 [設定腳本](#set-up-scripts) 和 [建立根 CA 憑證](#create-root-ca-certificate) 一節中的步驟進行。

### <a name="windows"></a>Windows

使用下列命令建立 IoT Edge 裝置身分識別憑證和私密金鑰：

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

您傳入此命令的名稱將會是 IoT 中樞內 IoT Edge 裝置的裝置識別碼。

新的裝置身分識別命令會建立數個憑證和金鑰檔案，包括您在 DPS 中建立個別註冊和安裝 IoT Edge 執行時間時將使用的三個憑證和金鑰檔案：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

使用下列命令建立 IoT Edge 裝置身分識別憑證和私密金鑰：

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

您傳入此命令的名稱將會是 IoT 中樞內 IoT Edge 裝置的裝置識別碼。

此腳本會建立數個憑證和金鑰檔案，包括三個，您將在 DPS 中建立個別註冊並安裝 IoT Edge 執行時間時使用：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>建立 IoT Edge 的裝置 CA 憑證

進入生產環境的每個 IoT Edge 裝置都需要 yaml 檔案所參考的裝置 CA 憑證。
裝置 CA 憑證負責為在裝置上執行的模組建立憑證。
閘道案例也是必要的，因為裝置 CA 憑證是 IoT Edge 裝置向下游裝置驗證其身分識別的方式。

裝置 CA 憑證會移至 IoT Edge 裝置上 yaml 檔案的 [ **憑證** ] 區段中。

繼續進行本節中的步驟之前，請先依照 [設定腳本](#set-up-scripts) 和 [建立根 CA 憑證](#create-root-ca-certificate) 一節中的步驟進行。

### <a name="windows"></a>Windows

1. 流覽至具有憑證產生腳本和根 CA 憑證的工作目錄。

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱。

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   此命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到 IoT Edge 裝置，並在 yaml 檔案中參考：

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

傳遞至 **CACertsEdgeDevice** 命令的名稱不應與 yaml 中的 hostname 參數或 IoT 中樞內的裝置識別碼相同。

### <a name="linux"></a>Linux

1. 流覽至具有憑證產生腳本和根 CA 憑證的工作目錄。

2. 使用下列命令建立 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱。

   ```bash
   ./certGen.sh create_edge_device_certificate "<CA cert name>"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到 IoT Edge 裝置，並在 yaml 檔案中參考：

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

傳遞給 **create_edge_device_certificate** 命令的名稱不應與 yaml 中的 hostname 參數或 IoT 中樞內的裝置識別碼相同。

## <a name="create-downstream-device-certificates"></a>建立下游裝置憑證

如果您要為閘道案例設定下游 IoT 裝置，而且想要使用 x.509 驗證，您可以產生下游裝置的示範憑證。
如果您想要使用對稱金鑰驗證，則不需要為下游裝置建立額外的憑證。
使用 x.509 憑證驗證 IoT 裝置的方法有兩種：使用自我簽署憑證或使用憑證授權單位單位 (CA) 簽署的憑證。
針對 X.509 自我簽署驗證 (有時稱為指紋驗證)，您必須建立新的憑證以放在 IoT 裝置上。
這些憑證內含會與 IoT 中樞共用以進行驗證的指紋。
針對 X.509 憑證授權單位 (CA) 簽署驗證，您需要在 IoT 中樞中註冊根 CA 憑證，以用來簽署 IoT 裝置的憑證。
任何使用由根 CA 憑證或其中繼憑證所發行之憑證的裝置，都將允許進行驗證。

憑證產生腳本可協助您建立示範憑證，以測試其中任何一種驗證案例。

繼續進行本節中的步驟之前，請先依照 [設定腳本](#set-up-scripts) 和 [建立根 CA 憑證](#create-root-ca-certificate) 一節中的步驟進行。

### <a name="self-signed-certificates"></a>自我簽署憑證

當您使用自我簽署憑證驗證 IoT 裝置時，您需要根據解決方案的根 CA 憑證來建立裝置憑證。
然後，您會從要提供給 IoT 中樞的憑證中取出十六進位的「指紋」。
您的 IoT 裝置也需要其裝置憑證的複本，才能向 IoT 中樞進行驗證。

#### <a name="windows"></a>Windows

1. 流覽至具有憑證產生腳本和根 CA 憑證的工作目錄。

2. 建立兩個憑證 (下游裝置的主要和次要) 。 使用的簡單命名慣例是以 IoT 裝置的名稱建立憑證，然後使用主要或次要標籤。 例如：

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連線到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 在 IoT 中樞內容中，從每個憑證) 取得 SHA1 指紋 (稱為指紋。 指紋是40的十六進位字元字串。 使用下列 openssl 命令來檢視憑證並尋找指紋：

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   執行此命令兩次，一次用於主要憑證，一次用於次要憑證。 當使用 X.509 自我簽署憑證來註冊新的 IoT 裝置時，您會為這兩個憑證提供指紋。

#### <a name="linux"></a>Linux

1. 流覽至具有憑證產生腳本和根 CA 憑證的工作目錄。

2. 建立兩個憑證 (下游裝置的主要和次要) 。 使用的簡單命名慣例是以 IoT 裝置的名稱建立憑證，然後使用主要或次要標籤。 例如：

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連線到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 在 IoT 中樞內容中，從每個憑證) 取得 SHA1 指紋 (稱為指紋。 指紋是40的十六進位字元字串。 使用下列 openssl 命令來檢視憑證並尋找指紋：

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   當您使用自我簽署的 x.509 憑證註冊新的 IoT 裝置時，您會提供主要和次要指紋。

### <a name="ca-signed-certificates"></a>CA 簽署的憑證

當您使用自我簽署憑證驗證 IoT 裝置時，您必須將解決方案的根 CA 憑證上傳到 IoT 中樞。
然後，您會執行驗證，以證明您擁有根 CA 憑證的 IoT 中樞。
最後，您會使用相同的根 CA 憑證來建立裝置憑證，以放在您的 IoT 裝置上，使其可以向 IoT 中樞進行驗證。

本節中的憑證適用于在 [您的 Azure IoT 中樞中設定 x.509 安全性](../iot-hub/iot-hub-security-x509-get-started.md)的步驟。

#### <a name="windows"></a>Windows

1. 將根 CA 憑證檔案從您的工作目錄，上傳 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 至您的 IoT 中樞。

2. 使用 Azure 入口網站中提供的程式碼，確認您擁有該根 CA 憑證。

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 為下游裝置建立憑證鏈結。 使用裝置在 IoT 中樞註冊的相同裝置識別碼。

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連線到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 將根 CA 憑證檔案從您的工作目錄，上傳 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 至您的 IoT 中樞。

2. 使用 Azure 入口網站中提供的程式碼，確認您擁有該根 CA 憑證。

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 為下游裝置建立憑證鏈結。 使用裝置在 IoT 中樞註冊的相同裝置識別碼。

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   此指令碼命令會建立數個憑證和金鑰檔。 下列憑證和金鑰組必須複製到下游 IoT 裝置，並在連線到 IoT 中樞的應用程式中參考：

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
