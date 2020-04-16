---
title: 建立測試憑證 - Azure IoT 邊緣 |微軟文件
description: 創建測試證書並瞭解如何在 Azure IoT 邊緣設備上安裝這些證書,以便為生產部署做好準備。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 364846f6cef196f6cefa7872af48f262b387db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393828"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>建立展示憑證以測試 IoT 邊緣裝置功能

IoT Edge 設備需要證書才能在運行時、模組和任何下游設備之間進行安全通信。
如果沒有證書頒發機構來創建所需的證書,則可以使用演示證書在測試環境中試用 IoT Edge 功能。
本文介紹了 IoT Edge 為測試提供的證書生成腳本的功能。

這些證書將在 30 天后過期,不應在任何生產方案中使用。

您可以在任何電腦上創建證書,然後將它們複製到 IoT Edge 設備。
使用主電腦創建證書比在 IoT Edge 裝置本身上生成證書更容易。
通過使用主電腦,可以設置腳本一次,然後重複此過程為多個設備創建證書。

依以下步驟建立測試 IoT Edge 專案的展示憑證:

1. 為裝置上的憑證產生[設定文稿](#set-up-scripts)。
2. [創建](#create-root-ca-certificate)用於對方案的所有其他證書進行簽名的根 CA 證書。
3. 產生要測試的專案所需的憑證:
   * [創建 IoT 邊緣設備識別證書](#create-iot-edge-device-identity-certificates),以使用 IoT 中心設備配置服務測試自動預配。
   * [創建 IoT Edge 裝置 CA 憑證](#create-iot-edge-device-ca-certificates)以測試生產方案或閘道方案。
   * [創建下游設備證書](#create-downstream-device-certificates),以在閘道方案中測試向 IoT 中心驗證下游設備。

## <a name="prerequisites"></a>Prerequisites

安裝了 Git 的開發電腦。

## <a name="set-up-scripts"></a>設定指令碼

GitHub 上的 IoT Edge 儲存庫包括可用於建立示範證書的證書生成腳本。
本節提供有關準備腳本在 Windows 或 Linux 上運行的腳本的說明。
如果你在Linux電腦上,跳過前到[設置Linux。](#set-up-on-linux)

### <a name="set-up-on-windows"></a>在 Windows 上設定

要在 Windows 設備上創建演示證書,您需要安裝 OpenSSL,然後複製式文稿並將其設定為在 PowerShell 中本地執行。

#### <a name="install-openssl"></a>安裝 OpenSSL

在您用來產生憑證的電腦上，安裝 Windows 適用的 OpenSSL。
如果您的 Windows 設備上已經安裝了 OpenSSL,則可以跳過此步驟,但請確保 openssl.exe 在您的 PATH 環境變數中可用。

有幾種方法可以安裝 OpenSSL,包括以下選項:

* **更簡單:** 下載並安裝任何[第三方 OpenSSL 二進位檔案](https://wiki.openssl.org/index.php/Binaries),例如,從[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)下載和安裝。 將 openssl.exe 的完整路徑加入至您的 PATH 環境變數。

* **建議**：自行下載 OpenSSL 原始程式碼，並在您的電腦上建置二進位檔，或是透過 [vcpkg](https://github.com/Microsoft/vcpkg) 來執行。 下列指示會使用 vcpkg 下載原始程式碼，並在 Windows 電腦上編譯並安裝 OpenSSL，且各項作業都能以輕鬆的步驟完成。

   1. 瀏覽至要安裝 vcpkg 的目錄。 依照指示下載並安裝 [vcpkg](https://github.com/Microsoft/vcpkg)。

   2. 安裝 vcpkg 後,請從 PowerShell 提示 符執行以下命令,以安裝適用於 Windows x64 的 OpenSSL 包。 安裝通常需要大約5分鐘才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. 將 `<vcpkg path>\installed\x64-windows\tools\openssl` 新增至您的 PATH 環境變數，讓 openssl.exe 檔案可供叫用。

#### <a name="prepare-scripts-in-powershell"></a>在 PowerShell 中準備文稿

Azure IoT Edge git 儲存函式庫包含可用於生成測試證書的文稿。
在本節中,您將克隆 IoT 邊緣回購並執行腳本。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。

2. 克隆 IoT 邊緣 git 儲存庫,其中包含用於生成演示證書的腳本。 使用 `git clone` 命令或[下載 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 瀏覽至您要使用的目錄。 在本文中,我們將呼叫此目錄*\<WRKDIR>*。 所有證書和密鑰都將在此工作目錄中創建。

4. 將複製的回購中的配置和文稿檔案複製到工作目錄中。

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果以 ZIP 形式下載回購,則資料夾`iotedge-master`名稱為 ,路徑的其餘部分相同。

5. 啟用 PowerShell 執行指令碼。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. 將文本使用的函數引入 PowerShell 的全域命名空間。

   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 視窗將顯示一個警告,指出此腳本生成的證書僅用於測試目的,不應在生產方案中使用。

7. 驗證 OpenSSL 是否正確安裝,並確保不會與現有證書發生名稱衝突。 如果出現問題,腳本輸出應描述如何在系統上修復它們。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>在 Linux 上設定

要在 Windows 設備上建立示範證書,您需要複製產生文稿並將其設定為在 bash 本地執行。

1. 克隆 IoT 邊緣 git 儲存庫,其中包含用於生成演示證書的腳本。

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 瀏覽至您要使用的目錄。 我們將在整個文章中引用此目錄作為*\<WRKDIR>*。 所有證書和密鑰檔都將在此目錄中創建。
  
3. 將設定檔和文本檔從複製的 IoT Edge 回購複製到工作目錄中。

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

## <a name="create-root-ca-certificate"></a>建立根憑證

根 CA 憑證用於製作用於測試 IoT Edge 方案的所有其他演示證書。
您可以繼續使用相同的根 CA 證書為多個 IoT 邊緣或下游設備製作演示證書。

如果工作資料夾中已有一個根 CA 證書,請不要創建新的 CA 證書。
新的根 CA 證書將覆蓋舊證書,而從舊證書製作的任何下游證書都將停止工作。
如果需要多個根 CA 證書,請確保在單獨的資料夾中管理它們。

在繼續本節中的步驟之前,請按照[「設置腳本」](#set-up-scripts)部分中的步驟準備具有演示證書生成腳本的工作目錄。

### <a name="windows"></a>Windows

1. 導航到放置證書生成腳本的工作目錄。

1. 建立根 CA 證書,並讓它對一個中間證書進行簽名。 證書都放在工作目錄中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此文稿指令建立多個憑證和金鑰檔,但當文章要求**根 CA 憑證**時,請使用以下檔案:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. 導航到放置證書生成腳本的工作目錄。

1. 建立根 CA 憑證和一個中間證書。

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此文稿指令建立多個憑證和金鑰檔,但當文章要求**根 CA 憑證**時,請使用以下檔案:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>建立 IoT 邊緣裝置認證

每個即將生產的 IoT Edge 裝置都需要從 config.yaml 檔案引用的裝置 CA 證書。
設備 CA 憑證負責為設備上運行的模組創建證書。
這也是 IoT Edge 設備在連接到下游設備時驗證其識別的方式。

裝置 CA 憑證位於 IoT 邊緣裝置上 config.yaml 檔案的 **「憑證」** 部分中。

在繼續本節中的步驟之前,請按照[「設置腳本」](#set-up-scripts)中的步驟和[創建根 CA 證書](#create-root-ca-certificate)部分進行操作。

### <a name="windows"></a>Windows

1. 導航到具有證書生成腳本和根 CA 證書的工作目錄。

2. 使用以下命令創建 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱,例如**MyEdgeDeviceCA,** 用於命名輸出檔。

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   此文稿命令建立多個證書和密鑰檔。 以下憑證與金鑰對需要複製到 IoT Edge 裝置,並在 config.yaml 檔案中參考:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

傳遞到這些文本中的閘道裝置名稱不應與 config.yaml 中的「主機名」參數或 IoT 中心中的設備的 ID 相同。
如果使用者在兩個位置中使用相同的名稱設定 IoT Edge，則這些指令碼可將「.ca」字串附加到閘道裝置名稱上來避免名稱衝突，進而發生問題。
不過，最好避免使用相同的名稱。

### <a name="linux"></a>Linux

1. 導航到具有證書生成腳本和根 CA 證書的工作目錄。

2. 使用以下命令創建 IoT Edge 裝置 CA 憑證和私密金鑰。 提供 CA 憑證的名稱,例如**MyEdgeDeviceCA,** 用於命名輸出檔。

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   此文稿命令建立多個證書和密鑰檔。 以下憑證與金鑰對需要複製到 IoT Edge 裝置,並在 config.yaml 檔案中參考:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

傳遞到這些文本中的閘道裝置名稱不應與 config.yaml 中的「主機名」參數或 IoT 中心中的設備的 ID 相同。
如果使用者在兩個位置中使用相同的名稱設定 IoT Edge，則這些指令碼可將「.ca」字串附加到閘道裝置名稱上來避免名稱衝突，進而發生問題。
不過，最好避免使用相同的名稱。

## <a name="create-iot-edge-device-identity-certificates"></a>建立 IoT 邊緣裝置識別憑證

設備識別證書用於透過[Azure IoT 中心裝置配置服務 (DPS)](../iot-dps/index.yml)預配 IoT Edge 裝置。

設備識別憑證位於 IoT Edge 裝置上的 config.yaml 檔的**預配**部分中。

在繼續本節中的步驟之前,請按照[「設置腳本」](#set-up-scripts)中的步驟和[創建根 CA 證書](#create-root-ca-certificate)部分進行操作。

### <a name="windows"></a>Windows

使用以下指令建立 IoT Edge 裝置識別憑證與私密金鑰:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

傳遞給此命令的名稱將是 IoT 中心中的 IoT 邊緣設備的設備 ID。

新的裝置識別命令建立多個憑證和金鑰檔,包括三個在 DPS 中建立單個註冊和安裝 IoT Edge 執行時時會使用的檔案:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

使用以下指令建立 IoT Edge 裝置識別憑證與私密金鑰:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

傳遞給此命令的名稱將是 IoT 中心中的 IoT 邊緣設備的設備 ID。

該文稿建立多個憑證和金鑰檔,包括三個在 DPS 中建立單個註冊和安裝 IoT Edge 執行時時將使用的檔案:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>建立下遊裝置憑證

如果要為閘道方案設置下游IoT設備,則可以為X.509身份驗證生成演示證書。
使用 X.509 憑證對 IoT 設備進行身份驗證的方法有兩種:使用自簽名證書或使用證書頒發機構 (CA) 簽署證書。
對於 X.509 自簽署身份驗證(有時稱為指紋身份驗證),您需要創建新證書以放置在 IoT 設備上。
這些證書有一個指紋,您與IoT中心共用以進行身份驗證。
對於 X.509 憑證頒發機構 (CA) 簽署身份驗證,您需要在 IoT 中心註冊的根 CA 證書,用於為 IoT 設備簽名證書。
任何使用根 CA 證書或其任何中間證書頒發的證書的設備都允許進行身份驗證。

證書生成腳本可以説明您製作演示證書來測試這些身份驗證方案之一。

在繼續本節中的步驟之前,請按照[「設置腳本」](#set-up-scripts)中的步驟和[創建根 CA 證書](#create-root-ca-certificate)部分進行操作。

### <a name="self-signed-certificates"></a>自我簽署憑證

使用自簽署證書對 IoT 設備進行身份驗證時,需要基於解決方案的根 CA 憑證創建設備證書。
然後,從證書中檢索十六進制"指紋",以便提供給 IoT 中心。
IoT 設備還需要其設備證書的副本,以便可以使用IoT中心進行身份驗證。

#### <a name="windows"></a>Windows

1. 導航到具有證書生成腳本和根 CA 證書的工作目錄。

2. 為下游設備創建兩個證書(主證書和輔助證書)。 一個易於使用的命名約定是創建具有 IoT 設備名稱的證書,然後創建主標籤或輔助標籤。 例如：

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   此文稿命令建立多個證書和密鑰檔。 以下憑證與金鑰對需要複製到下游 IoT 裝置,並在連接到 IoT 中心的應用程式中引用:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. 從每個證書檢索 SHA1 指紋(在 IoT 中心上下文中稱為指紋)。 指紋是一個 4xa 十進位元字串。 使用以下 openssl 指令檢視憑證並找到指紋:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   運行此命令兩次,一次用於主證書,一次用於輔助證書。 當您使用自簽名 X.509 憑證註冊新的 IoT 裝置時,您可以為兩個證書提供指紋。

#### <a name="linux"></a>Linux

1. 導航到具有證書生成腳本和根 CA 證書的工作目錄。

2. 為下游設備創建兩個證書(主證書和輔助證書)。 一個易於使用的命名約定是創建具有 IoT 設備名稱的證書,然後創建主標籤或輔助標籤。 例如：

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   此文稿命令建立多個證書和密鑰檔。 以下憑證與金鑰對需要複製到下游 IoT 裝置,並在連接到 IoT 中心的應用程式中引用:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. 從每個證書檢索 SHA1 指紋(在 IoT 中心上下文中稱為指紋)。 指紋是一個 4xa 十進位元字串。 使用以下 openssl 指令檢視憑證並找到指紋:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   使用自簽名 X.509 憑證註冊新的 IoT 設備時,提供主指紋和輔助指紋。

### <a name="ca-signed-certificates"></a>CA 簽署憑證

使用自簽名證書對 IoT 設備進行身份驗證時,需要將解決方案的根 CA 憑證上載到 IoT 中心。
然後,執行驗證,向IoT中心證明您擁有根CA證書。
最後,您可以使用相同的根 CA 憑證創建設備證書,使其放在 IoT 設備上,以便它可以使用 IoT 中心進行身份驗證。

本節中的證書用於[Azure IoT 中心中設置 X.509 安全性中](../iot-hub/iot-hub-security-x509-get-started.md)的步驟。

#### <a name="windows"></a>Windows

1. 將根 CA 憑證檔從`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`工作目錄 上傳到 IoT 中心。

2. 使用 Azure 入口中提供的代碼驗證您是否擁有該根 CA 憑證。

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. 為下游設備創建證書鏈。 使用設備在IoT中心中註冊的設備ID。

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   此文稿命令建立多個證書和密鑰檔。 以下憑證與金鑰對需要複製到下游 IoT 裝置,並在連接到 IoT 中心的應用程式中引用:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. 將根 CA 憑證檔從`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`工作目錄 上傳到 IoT 中心。

2. 使用 Azure 入口中提供的代碼驗證您是否擁有該根 CA 憑證。

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. 為下游設備創建證書鏈。 使用設備在IoT中心中註冊的設備ID。

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   此文稿命令建立多個證書和密鑰檔。 以下憑證與金鑰對需要複製到下游 IoT 裝置,並在連接到 IoT 中心的應用程式中引用:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
