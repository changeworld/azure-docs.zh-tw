---
title: 使用 X.509 憑證自動預配具有 DPS 的設備 - Azure IoT 邊緣 |微軟文檔
description: 使用 X.509 憑證使用設備預配服務測試 Azure IoT 邊緣的自動設備預配
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 48c8179af4a4b69924fb943ac98918b48d3a2008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537358"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>使用 X.509 憑證創建和預配 IoT 邊緣設備

使用[Azure IoT 中心設備預配服務 （DPS），](../iot-dps/index.yml)可以使用 X.509 憑證自動預配 IoT 邊緣設備。 如果您不熟悉自動預配過程，請查看[自動預配概念](../iot-dps/concepts-auto-provisioning.md)，然後再繼續。

本文演示如何在 IoT Edge 設備上使用 X.509 憑證創建設備預配服務註冊，並執行以下步驟：

* 生成證書和金鑰。
* 為設備創建單個註冊，或為一組設備創建組註冊。
* 安裝 IoT 邊緣運行時，並將設備註冊到 IoT 中心。

使用 X.509 憑證作為證明機制是調整生產環境並簡化裝置佈建的絕佳方式。 通常，X.509 憑證排列在信任憑證連結中。 從自簽名或受信任的根憑證開始，鏈中的每個證書都會對下一個較低證書進行簽名。 此模式創建一個委託信任鏈，從根憑證向下通過每個中繼憑證到設備上安裝的最終"葉"證書。

## <a name="prerequisites"></a>Prerequisites

* 使用中的 IoT 中樞。
* 物理或虛擬裝置，作為 IoT 邊緣設備。
* 已安裝的最新版本的[Git。](https://git-scm.com/download/)
* 連接到 IoT 中心功能中心的 Azure 中 IoT 中心設備佈建服務的實例。
  * 如果沒有設備預配服務實例，請按照[設置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md)中的說明操作。
  * 裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="generate-device-identity-certificates"></a>生成設備標識證書

設備標識證書是一個葉證書，通過證書信任鏈連接到頂級 X.509 憑證頒發機構 （CA） 證書。 設備標識證書必須將其通用名稱 （CN） 設置為您希望設備在 IoT 中心中具有的裝置識別碼。

設備標識證書僅用於預配 IoT 邊緣設備和使用 Azure IoT 中心對設備進行身份驗證。 它們不是證書簽名，這與 IoT Edge 設備向模組或葉設備提供用於驗證的 CA 憑證不同。 有關詳細資訊，請參閱[Azure IoT 邊緣證書使用方式詳細資訊](iot-edge-certs.md)。

創建設備標識證書後，應具有兩個檔：包含證書公共部分的 .cer 或 .pem 檔，以及包含證書私密金鑰的 .cer 或 .pem 檔。 如果計畫在 DPS 中使用組註冊，則還需要同一證書信任鏈中中間或根 CA 憑證的公共部分。

### <a name="use-test-certificates"></a>使用測試憑證

如果沒有可用於創建新標識證書的憑證授權單位，並且想要嘗試此方案，Azure IoT Edge git 存儲庫包含可用於生成測試憑證的腳本。 這些證書僅用於開發測試，不得在生產中使用。

要創建測試憑證，請按照[創建演示證書中的步驟來測試 IoT Edge 裝置功能](how-to-create-test-certificates.md)。 完成設置證書生成腳本和創建根 CA 憑證所需的兩個部分。 然後，按照步驟創建設備標識證書。 完成後，應具有以下憑證連結和金鑰組：

Linux：

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

在 IoT 邊緣設備上需要這兩個證書。 如果要在 DPS 中使用個人註冊，則將上載 .cert.pem 檔。 如果要在 DPS 中使用組註冊，則還需要在同一證書信任鏈中進行中間或根 CA 憑證才能上載。 如果您使用的是演示證書，`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`請使用證書進行組註冊。

## <a name="create-a-dps-individual-enrollment"></a>創建 DPS 個人註冊

使用生成的證書和金鑰為單個 IoT Edge 設備在 DPS 中創建單個註冊。 單個註冊獲取設備標識證書的公共部分，並將其與設備上的證書匹配。

如果要預配多個 IoT Edge 設備，請按照下一節中的步驟操作，[創建 DPS 組註冊](#create-a-dps-group-enrollment)。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。

有關設備預配服務中的註冊的詳細資訊，請參閱[如何管理設備註冊](../iot-dps/how-to-manage-enrollments.md)。

1. 在[Azure 門戶](https://portal.azure.com)中，導航到 IoT 中心設備預配服務的實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選取 [新增個別註冊]****，然後完成下列步驟以設定註冊：  

   * **機制**： 選擇**X.509**。

   * **主證書 .pem 或 .cer 檔**：從設備標識證書上載公共檔。 如果使用腳本生成測試憑證，請選擇以下檔：

      `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`

   * **IoT 中心裝置識別碼**：如果您願意，請提供設備的 ID。 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 如果不提供裝置識別碼，則使用 X.509 憑證中的通用名稱 （CN）。

   * **IoT 邊緣設備**：選擇**True**以聲明註冊是針對 IoT 邊緣設備的。

   * **選擇此設備可以分配給的 IoT 中心**：選擇要將設備連接到的連結 IoT 中心。 您可以選擇多個集線器，並且設備將根據所選的分配策略分配給其中一個集線器。

   * **初始設備孿生狀態**：添加要添加到設備孿生標記值（如果需要）。 您可以使用標記來定位設備組以進行自動部署。 例如：

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

1. 選取 [儲存]****。

現在，此設備存在註冊，IoT Edge 運行時可以在安裝過程中自動預配設備。 繼續安裝[IoT 邊緣運行時](#install-the-iot-edge-runtime)部分以設置 IoT 邊緣設備。

## <a name="create-a-dps-group-enrollment"></a>創建 DPS 組註冊

使用生成的證書和金鑰為多個 IoT 邊緣設備在 DPS 中創建組註冊。 組註冊使用用於生成單個設備標識證書的證書信任鏈的中間或根 CA 憑證。

如果要改為預配單個 IoT Edge 設備，請按照上一節中的步驟操作，[創建 DPS 個人註冊](#create-a-dps-individual-enrollment)。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。

### <a name="verify-your-root-certificate"></a>驗證根憑證

創建註冊組時，可以選擇使用已驗證的證書。 您可以通過證明您擁有根憑證的擁有權來驗證具有 DPS 的證書。 有關詳細資訊，請參閱[如何為 X.509 CA 憑證執行佔有證明](../iot-dps/how-to-verify-certificates.md)。

1. 在[Azure 門戶](https://portal.azure.com)中，導航到 IoT 中心設備預配服務的實例。

1. 從左側功能表中選擇 **"證書**"。

1. 選擇 **"添加**"以添加新證書。

1. 輸入證書的易記名稱，然後流覽到表示 X.509 憑證公共部分的 .cer 或 .pem 檔。

   如果您使用的是演示證書，請`<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem`上傳證書。

1. 選取 [儲存]****。

1. 您的證書現在應列在 **"證書"** 頁上。 選擇它以打開證書詳細資訊。

1. 選擇 **"生成驗證代碼"，** 然後複製生成的代碼。

1. 無論您是自帶 CA 憑證還是正在使用演示證書，您都可以使用 IoT Edge 存儲庫中提供的驗證工具來驗證擁有證明。 驗證工具使用 CA 憑證簽署具有提供的驗證碼作為主題名稱的新證書。

   * Windows：

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux：

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. 在 Azure 門戶中的同一證書詳細資訊頁中，上載新生成的驗證憑證。

1. 選取 [驗證] ****。

### <a name="create-enrollment-group"></a>創建註冊組

有關設備預配服務中的註冊的詳細資訊，請參閱[如何管理設備註冊](../iot-dps/how-to-manage-enrollments.md)。

1. 在[Azure 門戶](https://portal.azure.com)中，導航到 IoT 中心設備預配服務的實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選擇 **"添加註冊組**"，然後完成以下步驟以配置註冊：

   * **組名稱**：為此組註冊提供令人難忘的名稱。

   * **證明類型**：選擇**證書**。

   * **IoT 邊緣設備**：選擇**True**。 對於組註冊，所有設備都必須是 IoT Edge 設備，或者它們都不能是。

   * **證書類型**：如果您有與 DPS 一起存儲的已驗證 CA 憑證，請選擇**CA 憑證**;如果只想上載新檔，請選擇 **"中繼憑證**"，僅此註冊。

   * **主證書**：如果在上一節中選擇了 CA 憑證，請從下拉清單中選擇證書。 如果選擇中繼憑證，請從用於生成設備標識證書的證書信任鏈中的 CA 憑證上載公共檔。

   * **選擇此設備可以分配給的 IoT 中心**：選擇要將設備連接到的連結 IoT 中心。 您可以選擇多個集線器，並且設備將根據所選的分配策略分配給其中一個集線器。

   * **初始設備孿生狀態**：添加要添加到設備孿生標記值（如果需要）。 您可以使用標記來定位設備組以進行自動部署。 例如：

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

1. 選取 [儲存]****。

現在，此設備存在註冊，IoT Edge 運行時可以在安裝過程中自動預配設備。 繼續下一節以設置 IoT 邊緣設備。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。

X.509 與 DPS 的預配僅在 IoT 邊緣版本 1.0.9 或更高版本中支援。

預配設備時，您需要以下資訊：

* DPS **ID 範圍**值。 可以從 Azure 門戶中的 DPS 實例的概述頁檢索此值。
* 設備上的設備標識證書檔。
* 設備上的設備標識金鑰檔。
* 可選的註冊 ID（如果未提供，則從設備標識證書中的公用名稱提取）。

### <a name="linux-device"></a>Linux 設備

使用以下連結使用適合設備體系結構的命令在設備上安裝 Azure IoT Edge 運行時。 當您進入有關配置安全守護進程的部分時，為 X.509 自動（而不是手動）預配配置 IoT Edge 運行時。 在完成本文的前幾節後，您應該擁有所需的所有資訊和證書檔。

[在 Linux 上安裝 Azure IoT 邊緣運行時](how-to-install-iot-edge-linux.md)

將 X.509 憑證和金鑰資訊添加到 config.yaml 檔時，路徑應作為檔 URI 提供。 例如：

* `file:///<path>/identity_certificate.pem`
* `file:///<path>/identity_key.pem`

X.509 自動預配的設定檔中的部分如下所示：

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

將 的`scope_id``identity_cert`預留位置值`identity_pk`替換為 DPS 實例中的範圍 ID，將 URI 替換為設備上的證書和金鑰檔位置。 如果需要，`registration_id`請為設備提供 a，或保留此行注釋，以便使用標識證書的 CN 名稱註冊設備。

更新 config.yaml 檔後，始終重新開機安全守護進程。

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Windows 裝置

在為其生成標識證書和標識金鑰的設備上安裝 IoT Edge 運行時。 您將為自動（而不是手動）預配配置 IoT Edge 運行時。

有關在 Windows 上安裝 IoT Edge 的更多詳細資訊，包括管理容器和更新 IoT Edge 等任務的先決條件和說明，請參閱[在 Windows 上安裝 Azure IoT 邊緣運行時](how-to-install-iot-edge-windows.md)。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 安裝物聯網邊緣時，請務必使用 AMD64 電源外殼會話，而不是 PowerShell （x86）。

1. **Deploy-IoTEdge**命令檢查 Windows 電腦是否位於受支援的版本上，打開容器功能，然後下載 moby 運行時和 IoT Edge 運行時。 該命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此時，IoT Core 設備可能會自動重新開機。 其他 Windows 10 或 Windows 伺服器設備可能會提示您重新開機。 如果是，請立即重新開機設備。 設備準備就緒後，再次以管理員身份運行 PowerShell。

1. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 該命令預設為手動預配，`-Dps`除非您使用標誌使用自動預配。

   將`{scope_id}`的預留位置值`{identity cert path}`替換為`{identity key path}`中的預留位置值，並將 DPS 實例中的相應值和設備上的檔路徑替換。 如果要指定註冊 ID，請也包括`-RegistrationId {registration_id}`，請酌情替換預留位置。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >config.yaml 檔將您的證書和金鑰資訊存儲為檔 URI。 但是，初始化-IoTEdge 命令可為您處理此格式化步驟，因此您可以提供設備上證書和金鑰檔的絕對路徑。

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至 IoT 中樞，並開始將 IoT Edge 模組部署到您的裝置。

您可以驗證您在設備預配服務中創建的單個註冊是否已使用。 導航到 Azure 門戶中的設備預配服務實例。 打開您創建的單個註冊的註冊詳細資訊。 請注意，已**分配**註冊狀態並列出裝置識別碼。

請在您的裝置上使用下列命令，確認執行階段已成功安裝並啟動。

### <a name="linux-device"></a>Linux 設備

檢查 IoT Edge 服務的狀態。

```cmd/sh
systemctl status iotedge
```

檢查服務日誌。

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

檢查服務日誌。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出執行中的模組。

```powershell
iotedge list
```

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 瞭解如何使用 Azure 門戶或使用[Azure CLI](how-to-deploy-monitor-cli.md)[大規模部署和監視 IoT 邊緣模組](how-to-deploy-monitor.md)。
