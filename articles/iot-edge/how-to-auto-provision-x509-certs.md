---
title: 使用 x.509 憑證自動布建具有 DPS 的裝置-Azure IoT Edge |Microsoft Docs
description: 使用 x.509 憑證來測試裝置布建服務 Azure IoT Edge 的自動裝置布建
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c54690645286a4fceb3fd786d85652b1cf77d7aa
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260041"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>使用 x.509 憑證建立和布建 IoT Edge 裝置

透過[Azure IoT 中樞裝置布建服務 (DPS) ](../iot-dps/index.yml)，您可以使用 x.509 憑證自動布建 IoT Edge 裝置。 如果您不熟悉自動布建程式，請先參閱自動布[建概念](../iot-dps/concepts-auto-provisioning.md)，再繼續進行。

本文說明如何使用 x.509 憑證，在 IoT Edge 裝置上建立裝置布建服務註冊，並執行下列步驟：

* 產生憑證和金鑰。
* 建立裝置的個別註冊，或一組裝置的群組註冊。
* 安裝 IoT Edge 執行時間，並向 IoT 中樞註冊裝置。

使用 X.509 憑證作為證明機制是調整生產環境並簡化裝置佈建的絕佳方式。 一般來說，x.509 憑證會以信任鏈的方式排列。 從自我簽署或受信任的根憑證開始，連鎖中的每個憑證都會簽署下一個較低的憑證。 此模式會從根憑證向下到裝置上安裝的最終「分葉」憑證，建立委派的信任鏈。

## <a name="prerequisites"></a>必要條件

* 使用中的 IoT 中樞。
* 做為 IoT Edge 裝置的實體或虛擬裝置。
* 已安裝最新版本的[Git](https://git-scm.com/download/) 。
* Azure 中 IoT 中樞裝置佈建服務的實例，連結至您的 IoT 中樞。
  * 如果您沒有裝置布建服務實例，請依照[設定 IOT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md)中的指示進行。
  * 裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="generate-device-identity-certificates"></a>產生裝置身分識別憑證

裝置身分識別憑證是分葉憑證，會透過憑證信任鏈連線到 (CA) 憑證的前 x.509 憑證授權單位單位。 裝置身分識別憑證必須將其一般名稱 (CN) 設定為您希望裝置在 IoT 中樞內擁有的裝置識別碼。

裝置身分識別憑證僅用於布建 IoT Edge 裝置，以及使用 Azure IoT 中樞驗證裝置。 它們不會簽署憑證，不同于 IoT Edge 裝置向模組或分葉裝置出示以進行驗證的 CA 憑證。 如需詳細資訊，請參閱[Azure IoT Edge 憑證使用方式詳細資料](iot-edge-certs.md)。

建立裝置身分識別憑證之後，您應該有兩個檔案： .cer 或 pem 檔案，其中包含憑證的公開部分，以及具有憑證私密金鑰的 .cer 或 pem 檔案。 如果您打算在 DPS 中使用群組註冊，則也需要相同憑證信任鏈中中繼或根 CA 憑證的公開部分。

您需要下列檔案，才能使用 x.509 來設定自動布建：

* 裝置身分識別憑證和其私密金鑰憑證。 如果您建立個別註冊，裝置身分識別憑證會上傳至 DPS。 私用金鑰會傳遞至 IoT Edge 執行時間。
* 完整鏈憑證，其中應該至少有裝置身分識別和中繼憑證。 完整的連鎖憑證會傳遞至 IoT Edge 執行時間。
* 憑證信任鏈中的中繼或根 CA 憑證。 如果您建立群組註冊，則會將此憑證上傳至 DPS。

> [!NOTE]
> 目前，libiothsm 的限制可防止使用2050年1月1日之後到期的憑證。

### <a name="use-test-certificates"></a>使用測試憑證

如果您沒有可用來建立新身分識別憑證的憑證授權單位單位，而且想要嘗試此案例，則 Azure IoT Edge git 存放庫包含可用於產生測試憑證的腳本。 這些憑證僅供開發測試之用，不得用於生產環境。

若要建立測試憑證，請依照[建立示範憑證中的步驟來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 完成兩個必要區段來設定憑證產生腳本，並建立根 CA 憑證。 然後，遵循步驟來建立裝置身分識別憑證。 當您完成時，您應該會有下列憑證鏈和金鑰組：

Linux：

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

您在 IoT Edge 裝置上需要這兩個憑證。 如果您要在 DPS 中使用個別的註冊，則您將上傳 cert pem 檔案。 如果您要在 DPS 中使用群組註冊，則您也需要相同憑證信任鏈中的中繼或根 CA 憑證，才能上傳。 如果您使用的是示範憑證，請使用 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 適用于群組註冊的憑證。

## <a name="create-a-dps-individual-enrollment"></a>建立 DPS 個別註冊

使用您產生的憑證和金鑰，在 DPS 中為單一 IoT Edge 裝置建立個別註冊。 個別註冊會採用裝置身分識別憑證的公開部分，並將其與裝置上的憑證相符。

如果您想要布建多個 IoT Edge 裝置，請依照下一節[建立 DPS 群組註冊](#create-a-dps-group-enrollment)中的步驟進行。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-at-scale.md)。

如需裝置布建服務中註冊的詳細資訊，請參閱[如何管理裝置註冊](../iot-dps/how-to-manage-enrollments.md)。

   > [!TIP]
   > 在 Azure CLI 中，您可以建立[註冊](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment)或[註冊群組](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group)，並使用**具備 edge 功能**的旗標來指定裝置或裝置群組是 IoT Edge 裝置。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置佈建服務實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選取 [新增個別註冊]****，然後完成下列步驟以設定註冊：  

   * **機制**：選取 [ **x.509**]。

   * **主要憑證 pem 或 .cer**檔案：從裝置身分識別憑證上傳公用檔案。 如果您使用腳本來產生測試憑證，請選擇下列檔案：

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT 中樞裝置識別碼**：如有需要，請提供您裝置的識別碼。 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 如果您未提供裝置識別碼，則會使用 x.509 憑證中 (CN) 的一般名稱。

   * **IoT Edge 裝置**：選取 [ **True** ] 以宣告註冊適用于 IoT Edge 裝置。

   * **選取可指派此裝置的 iot 中樞**：選擇您想要將裝置連線到的已連結 iot 中樞。 您可以選擇多個中樞，並根據選取的配置原則，將裝置指派給其中一個。

   * **初始裝置**對應項狀態：如有需要，請新增要新增至裝置對應項的標記值。 您可以使用標籤來鎖定裝置群組，以進行自動部署。 例如︰

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. 選取 [儲存]。

現在此裝置已有註冊，IoT Edge 執行時間可以在安裝期間自動布建裝置。 繼續進行[安裝 IoT Edge 運行](#install-the-iot-edge-runtime)時間一節，以設定您的 IoT Edge 裝置。

## <a name="create-a-dps-group-enrollment"></a>建立 DPS 群組註冊

使用您產生的憑證和金鑰，在 DPS 中建立多個 IoT Edge 裝置的群組註冊。 群組註冊會使用憑證信任鏈中的中繼或根 CA 憑證，以用來產生個別裝置身分識別憑證。

如果您想要改為布建單一 IoT Edge 裝置，請依照上一節[建立 DPS 個別註冊](#create-a-dps-individual-enrollment)中的步驟進行。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-at-scale.md)。

### <a name="verify-your-root-certificate"></a>驗證您的根憑證

當您建立註冊群組時，您可以選擇使用已驗證的憑證。 您可以藉由證明您擁有根憑證的擁有權，來驗證具有 DPS 的憑證。 如需詳細資訊，請參閱[如何對 X.509 CA 憑證執行擁有權證明](../iot-dps/how-to-verify-certificates.md)。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置佈建服務實例。

1. 從左側功能表中選取 [**憑證**]。

1. 選取 **[新增]** 以新增憑證。

1. 為您的憑證輸入易記的名稱，然後流覽至代表 x.509 憑證公開部分的 .cer 或 pem 檔案。

   如果您使用的是示範憑證，請上傳 `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 憑證。

1. 選取 [儲存]。

1. 您的憑證現在應該會列在 [**憑證**] 頁面上。 選取它以開啟憑證詳細資料。

1. 選取 [**產生驗證碼**]，然後複製產生的程式碼。

1. 無論您是否自備 CA 憑證，或使用示範憑證，都可以使用 IoT Edge 存放庫中提供的驗證工具來驗證擁有權證明。 驗證工具會使用您的 CA 憑證來簽署新的憑證，並以提供的驗證碼做為主體名稱。

   * Windows：

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux：

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. 在 Azure 入口網站的相同 [憑證詳細資料] 頁面上，上傳新產生的驗證憑證。

1. 選取 [驗證]。

### <a name="create-enrollment-group"></a>建立註冊群組

如需裝置布建服務中註冊的詳細資訊，請參閱[如何管理裝置註冊](../iot-dps/how-to-manage-enrollments.md)。

1. 在 [ [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置佈建服務實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選取 [**新增註冊群組**]，然後完成下列步驟以設定註冊：

   * **組名**：為此群組註冊提供易記的名稱。

   * **證明類型**：選取 [**憑證**]。

   * **IoT Edge 裝置**：選取 [ **True**]。 若為群組註冊，所有裝置都必須 IoT Edge 裝置，或兩者都不可以。

   * **憑證類型**：如果您有已驗證的 ca 憑證與 DPS 一起儲存，請選取 [ **ca 憑證**]，如果您只想要上傳此註冊的新檔案，請選取 [**中繼憑證**]。

   * **主要憑證**：如果您在上一節中選擇 [CA 憑證]，請從下拉式清單中選擇您的憑證。 如果您選擇中繼憑證，請從用來產生裝置身分識別憑證的信任憑證連結中的 CA 憑證上傳公用檔案。

   * **選取可指派此裝置的 iot 中樞**：選擇您想要將裝置連線到的已連結 iot 中樞。 您可以選擇多個中樞，並根據選取的配置原則，將裝置指派給其中一個。

   * **初始裝置**對應項狀態：如有需要，請新增要新增至裝置對應項的標記值。 您可以使用標籤來鎖定裝置群組，以進行自動部署。 例如︰

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. 選取 [儲存]。

現在此裝置已有註冊，IoT Edge 執行時間可以在安裝期間自動布建裝置。 繼續進行下一節，以設定您的 IoT Edge 裝置。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會部署在所有 IoT Edge 裝置上。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。

X.509 使用 DPS 布建，只有在 IoT Edge 版本1.0.9 或更新版本中才支援。

布建您的裝置時，您將需要下列資訊：

* DPS**識別碼範圍**值。 您可以從 Azure 入口網站中 DPS 實例的 [總覽] 頁面抓取此值。
* 裝置上的裝置身分識別憑證鏈檔案。
* 裝置上的裝置身分識別金鑰檔。
* 選擇性的註冊識別碼 (從裝置身分識別憑證中的一般名稱提取（如果未提供) ）。

### <a name="linux-device"></a>Linux 裝置

使用下列連結，在您的裝置上安裝 Azure IoT Edge 執行時間，並使用適用于您裝置架構的命令。 當您進入設定安全性守護程式的一節時，請針對 x.509 自動（而非手動）布建，設定 IoT Edge 執行時間。 完成本文的前幾節之後，您應該會擁有所有需要的資訊和憑證檔案。

[在 Linux 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)

當您將 x.509 憑證和金鑰資訊新增至 yaml 檔案時，應該將路徑提供為檔案 Uri。 例如︰

* `file:///<path>/identity_certificate_chain.pem`
* `file:///<path>/identity_key.pem`

X.509 自動布建的設定檔中的區段看起來像這樣：

```yaml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

將、、的預留位置值取代為 `scope_id` `identity_cert` 您的 `identity_pk` DPS 實例中的範圍識別碼，以及您裝置上的憑證鏈和金鑰檔案位置的 uri。 如有 `registration_id` 需要，請為裝置提供，或將這一行標示為批註，以向身分識別憑證的 CN 名稱註冊裝置。

更新 yaml 檔案之後，請一律重新開機安全性守護程式。

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows 裝置

在您產生身分識別憑證鏈和身分識別金鑰的裝置上安裝 IoT Edge 執行時間。 您會將 IoT Edge 執行時間設定為自動布建，而不是手動布建。

如需有關在 Windows 上安裝 IoT Edge 的詳細資訊，包括管理容器和更新 IoT Edge 等工作的必要條件和指示，請參閱[在 windows 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-windows.md)。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 安裝 IoT Edge 時，請務必使用 PowerShell 的 AMD64 會話，而不是 PowerShell (x86) 。

1. **IoTEdge**命令會檢查您的 Windows 電腦是否在支援的版本上，開啟 [容器] 功能，然後下載 moby 執行時間和 IoT Edge 執行時間。 命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此時，IoT 核心版裝置可能會自動重新開機。 其他 Windows 10 或 Windows Server 裝置可能會提示您重新開機。 若是如此，請立即重新開機您的裝置。 一旦您的裝置準備就緒，請再次以系統管理員身分執行 PowerShell。

1. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 除非您使用 `-Dps` 旗標來使用自動布建，否則命令會預設為手動布建。

   將、和的預留位置值取代為 `{scope_id}` `{identity cert chain path}` `{identity key path}` 您的 DPS 實例中的適當值和裝置上的檔案路徑。 如果您想要指定註冊識別碼，請同時包含 `-RegistrationId {registration_id}` ，並視情況取代預留位置。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Yaml 檔案會將您的憑證和金鑰資訊儲存為檔案 Uri。 不過，IoTEdge 命令會為您處理此格式化步驟，因此您可以提供裝置上憑證和金鑰檔案的絕對路徑。

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至 IoT 中樞，並開始將 IoT Edge 模組部署到您的裝置。

您可以確認已使用您在裝置布建服務中建立的個別註冊。 在 Azure 入口網站中，流覽至您的裝置布建服務實例。 開啟您所建立之個別註冊的註冊詳細資料。 請注意，註冊的狀態會被**指派**，且會列出裝置識別碼。

請在您的裝置上使用下列命令，確認執行階段已成功安裝並啟動。

### <a name="linux-device"></a>Linux 裝置

檢查 IoT Edge 服務的狀態。

```cmd/sh
systemctl status iotedge
```

檢查服務記錄。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

列出執行中的模組。

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows 裝置

檢查 IoT Edge 服務的狀態。

```powershell
Get-Service iotedge
```

檢查服務記錄。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出執行中的模組。

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 瞭解如何使用 Azure 入口網站或[使用 Azure CLI](how-to-deploy-cli-at-scale.md)，[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)。
