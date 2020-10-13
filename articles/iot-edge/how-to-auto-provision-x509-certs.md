---
title: 使用 x.509 憑證自動布建具有 DPS 的裝置-Azure IoT Edge |Microsoft Docs
description: 使用 x.509 憑證測試裝置布建服務 Azure IoT Edge 的自動裝置布建
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 761b031916dd9ead71f5be6a6887208a1f200f58
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966129"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>使用 x.509 憑證來建立和布建 IoT Edge 裝置

使用 [Azure IoT 中樞裝置布建服務 (DPS) ](../iot-dps/index.yml)，您可以使用 x.509 憑證自動布建 IoT Edge 裝置。 如果您不熟悉自動佈建程序，請先檢閱[佈建](../iot-dps/about-iot-dps.md#provisioning-process)概觀，再繼續作業。

本文說明如何使用下列步驟，在 IoT Edge 裝置上使用 x.509 憑證來建立裝置布建服務註冊：

* 產生憑證和金鑰。
* 建立裝置的個別註冊或一組裝置的群組註冊。
* 安裝 IoT Edge 執行時間，並向 IoT 中樞註冊裝置。

使用 X.509 憑證作為證明機制是調整生產環境並簡化裝置佈建的絕佳方式。 一般而言，x.509 憑證會以信任的憑證鏈來排列。 從自我簽署或受信任的根憑證開始，鏈中的每個憑證都會簽署下一個較低的憑證。 此模式會從根憑證向下到安裝在裝置上的最終「分葉」憑證之間，建立委派的信任鏈。

## <a name="prerequisites"></a>必要條件

* 使用中的 IoT 中樞。
* 要成為 IoT Edge 裝置的實體或虛擬裝置。
* 已安裝最新版本的 [Git](https://git-scm.com/download/) 。
* Azure 中的 IoT 中樞裝置布建服務實例，連結至您的 IoT 中樞。
  * 如果您沒有裝置布建服務實例，請依照 [設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md)中的指示進行。
  * 裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="generate-device-identity-certificates"></a>產生裝置身分識別憑證

裝置身分識別憑證是透過憑證授權單位單位（CA） x.509 憑證授權單位單位 (CA) 憑證的分葉憑證。 裝置身分識別憑證必須將它的一般名稱 (CN) 設定為您想要讓裝置在 IoT 中樞內擁有的裝置識別碼。

裝置身分識別憑證僅用於布建 IoT Edge 裝置，以及使用 Azure IoT 中樞驗證裝置。 它們不會簽署憑證，與 IoT Edge 裝置提供給模組或分葉裝置以進行驗證的 CA 憑證不同。 如需詳細資訊，請參閱 [Azure IoT Edge 憑證使用方式詳細資料](iot-edge-certs.md)。

建立裝置身分識別憑證之後，您應該會有兩個檔案： .cer 或 pem 檔案，其中包含憑證的公開部分，以及副檔名為 .cer 或 pem 檔案的憑證的私密金鑰。 如果您打算在 DPS 中使用群組註冊，您也需要在相同的證書信任鏈中使用中繼或根 CA 憑證的公開部分。

您需要下列檔案，才能使用 x.509 設定自動布建：

* 裝置身分識別憑證與其私密金鑰憑證。 如果您建立個別註冊，則會將裝置身分識別憑證上傳至 DPS。 私密金鑰會傳遞至 IoT Edge 執行時間。
* 完整鏈憑證，其中至少應有裝置身分識別和中繼憑證。 完整鏈憑證會傳遞至 IoT Edge 執行時間。
* 憑證信任鏈中的中繼或根 CA 憑證。 如果您建立群組註冊，則會將此憑證上傳至 DPS。

> [!NOTE]
> 目前，libiothsm 中的限制會防止使用在2038年1月1日或之後過期的憑證。

### <a name="use-test-certificates"></a>使用測試憑證

如果您沒有可用來建立新身分識別憑證的憑證授權單位單位，而且想要試用此案例，Azure IoT Edge git 存放庫包含可供您用來產生測試憑證的腳本。 這些憑證是專為開發測試而設計的，不能用在生產環境中。

若要建立測試憑證，請依照 [建立示範憑證中的步驟來測試 IoT Edge 的裝置功能](how-to-create-test-certificates.md)。 完成兩個必要區段，以設定憑證產生腳本和建立根 CA 憑證。 然後，遵循下列步驟來建立裝置身分識別憑證。 當您完成時，您應該會有下列憑證鏈和金鑰組：

Linux：

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows：

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

您需要 IoT Edge 裝置上的這兩個憑證。 如果您要在 DPS 中使用個別註冊，則會上傳 cert. pem 檔案。 如果您要在 DPS 中使用群組註冊，則您也需要相同證書信任鏈中的中繼或根 CA 憑證上傳。 如果您使用的是示範憑證，請使用 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 憑證進行群組註冊。

## <a name="create-a-dps-individual-enrollment"></a>建立 DPS 個別註冊

使用您產生的憑證和金鑰，在 DPS 中建立單一 IoT Edge 裝置的個別註冊。 個別註冊會取得裝置身分識別憑證的公開部分，並符合裝置上的憑證。

如果您想要布建多個 IoT Edge 裝置，請依照下一節中的步驟 [建立 DPS 群組註冊](#create-a-dps-group-enrollment)。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-at-scale.md)。

如需裝置布建服務中註冊的詳細資訊，請參閱 [如何管理裝置註冊](../iot-dps/how-to-manage-enrollments.md)。

   > [!TIP]
   > 在 Azure CLI 中，您可以建立 [註冊](/cli/azure/ext/azure-iot/iot/dps/enrollment) 或 [註冊群組](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) ，並使用 **啟用 edge** 的旗標來指定裝置或裝置群組是 IoT Edge 裝置。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置布建服務實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選取 [新增個別註冊]****，然後完成下列步驟以設定註冊：  

   * **機制**：選取 [ **x.509**]。

   * **主要憑證 pem 或 .cer**檔案：從裝置身分識別憑證上傳公用檔案。 如果您使用腳本來產生測試憑證，請選擇下列檔案：

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT 中樞裝置識別碼**：如有需要，請提供您裝置的識別碼。 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 如果您未提供裝置識別碼，則會使用 x.509 憑證中 (CN) 的一般名稱。

   * **IoT Edge 裝置**：選取 [ **True** ] 以宣告註冊適用于 IoT Edge 裝置。

   * **選取可指派此裝置的 iot 中樞**：選擇您想要將裝置連線的連結 iot 中樞。 您可以選擇多個中樞，並根據所選的配置原則將裝置指派給其中一個。

   * **初始裝置**對應項狀態：如有需要，請新增要新增至裝置對應項的標記值。 您可以使用標記來鎖定裝置群組，以進行自動部署。 例如：

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

現在此裝置已有註冊，IoT Edge 執行時間可以在安裝期間自動布建裝置。 繼續進行 [IoT Edge 執行時間安裝](#install-the-iot-edge-runtime) 一節，以設定您的 IoT Edge 裝置。

## <a name="create-a-dps-group-enrollment"></a>建立 DPS 群組註冊

使用您產生的憑證和金鑰，在 DPS 中建立多個 IoT Edge 裝置的群組註冊。 群組註冊會使用憑證信任鏈中的中繼或根 CA 憑證，以用來產生個別的裝置身分識別憑證。

如果您想要改為布建單一 IoT Edge 裝置，請依照上一節中的步驟 [建立 DPS 個別註冊](#create-a-dps-individual-enrollment)。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-at-scale.md)。

### <a name="verify-your-root-certificate"></a>驗證您的根憑證

當您建立註冊群組時，您可以選擇使用已驗證的憑證。 您可以證明您擁有根憑證的擁有權，藉以使用 DPS 驗證憑證。 如需詳細資訊，請參閱 [如何對 X.509 CA 憑證的擁有權證明](../iot-dps/how-to-verify-certificates.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置布建服務實例。

1. 從左側功能表中選取 [ **憑證** ]。

1. 選取 **[新增]** 以新增憑證。

1. 輸入憑證的易記名稱，然後流覽至 .cer 或 pem 檔案，該檔案代表 x.509 憑證的公開部分。

   如果您使用的是示範憑證，請上傳 `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 憑證。

1. 選取 [儲存]。

1. 現在您的憑證應該會列在 [ **憑證** ] 頁面上。 選取它來開啟憑證詳細資料。

1. 選取 [ **產生驗證碼** ]，然後複製產生的程式碼。

1. 無論您是使用自己的 CA 憑證或使用示範憑證，您都可以使用 IoT Edge 存放庫中提供的驗證工具來確認擁有權證明。 驗證工具會使用您的 CA 憑證來簽署具有所提供驗證碼做為主體名稱的新憑證。

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

如需裝置布建服務中註冊的詳細資訊，請參閱 [如何管理裝置註冊](../iot-dps/how-to-manage-enrollments.md)。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置布建服務實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選取 [ **新增註冊群組** ]，然後完成下列步驟來設定註冊：

   * **組名**：為此群組註冊提供易記的名稱。

   * **證明類型**：選取 [ **憑證**]。

   * **IoT Edge 裝置**：選取 [ **True**]。 針對群組註冊，所有裝置都必須是 IoT Edge 的裝置，否則不能是任何裝置。

   * **憑證類型**：如果您有已驗證的 ca 憑證儲存在 DPS，或**中繼憑證**（如果您只想要上傳此註冊的新檔案），請選取 [ **ca 憑證**]。

   * **主要憑證**：如果您在上一節中選擇 [CA 憑證]，請從下拉式清單中選擇您的憑證。 如果您選擇中繼憑證，請從用來產生裝置身分識別憑證的證書信任鏈中的 CA 憑證上傳公用檔案。

   * **選取可指派此裝置的 iot 中樞**：選擇您想要將裝置連線的連結 iot 中樞。 您可以選擇多個中樞，並根據所選的配置原則將裝置指派給其中一個。

   * **初始裝置**對應項狀態：如有需要，請新增要新增至裝置對應項的標記值。 您可以使用標記來鎖定裝置群組，以進行自動部署。 例如：

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

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。

遵循 [安裝 Azure IoT Edge 運行](how-to-install-iot-edge.md)時間中的步驟，然後回到本文以布建裝置。

只有 IoT Edge 版本1.0.9 或更新版本才支援使用 DPS 布建 x.509。

## <a name="configure-the-device-with-provisioning-information"></a>使用布建資訊設定裝置

當執行時間安裝在您的裝置上之後，請使用它用來連線至裝置布建服務和 IoT 中樞的資訊來設定裝置。

準備好下列資訊：

* DPS **識別碼範圍** 值。 您可以從 Azure 入口網站中 DPS 實例的 [總覽] 頁面取得此值。
* 裝置上的裝置身分識別憑證鏈檔案。
* 裝置上的裝置身分識別金鑰檔。
* 選擇性的註冊識別碼。 如果未提供，則會從裝置身分識別憑證中的一般名稱提取識別碼。

### <a name="linux-device"></a>Linux 裝置

1. 開啟 IoT Edge 裝置上的設定檔。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 尋找檔案的 [布建設定] 區段。 將 DPS 對稱金鑰布建的行取消批註，並確定任何其他布建行都已加上批註。

   `provisioning:`該行不應該有空格，而且嵌套的專案應該以兩個空格縮排。

   ```yml
   # DPS TPM provisioning configuration
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

1. `scope_id` `identity_cert` `identity_pk` 使用 DPS 和裝置資訊來更新、和的值。

   當您將 x.509 憑證和金鑰資訊新增至 yaml 檔案時，應以檔案 Uri 的形式提供路徑。 例如：

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. `registration_id`如果您想要的話，請為裝置提供，或將這一行標記為批註，以將裝置註冊為身分識別憑證的 CN 名稱。

1. 重新啟動 IoT Edge 執行階段，使其可取得您對裝置所做的所有組態變更。

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="windows-device"></a>Windows 裝置

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 安裝 IoT Edge 時，請務必使用 PowerShell 的 AMD64 會話，而不是 PowerShell (x86) 。

1. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 此命令會預設為使用 Windows 容器進行手動布建，因此請使用旗標搭配 `-DpsX509` x.509 憑證驗證來使用自動布建。

   將、和的預留位置值取代為 `{scope_id}` `{identity cert chain path}` `{identity key path}` 您的 DPS 實例中的適當值，以及裝置上的檔案路徑。

   `-RegistrationId {registration_id}`如果您想要將裝置識別碼設定為身分識別憑證的 CN 名稱以外的名稱，請新增。

   `-ContainerOs Linux`如果您是在 Windows 上使用 Linux 容器，請新增參數。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Yaml 檔案會將您的憑證和金鑰資訊儲存為檔案 Uri。 不過，Initialize-IoTEdge 命令會為您處理此格式化步驟，因此您可以在裝置上提供憑證和金鑰檔案的絕對路徑。

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至 IoT 中樞，並開始將 IoT Edge 模組部署到您的裝置。

您可以確認已使用您在裝置布建服務中建立的個別註冊。 在 Azure 入口網站中，流覽至您的裝置布建服務實例。 開啟您所建立個別註冊的註冊詳細資料。 請注意，會 **指派** 註冊的狀態並列出裝置識別碼。

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
