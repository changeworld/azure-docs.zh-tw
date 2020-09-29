---
title: 使用對稱金鑰證明來布建裝置-Azure IoT Edge
description: 使用對稱金鑰證明以裝置布建服務測試 Azure IoT Edge 的自動裝置布建
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4c44ad91b4fb8581a67ea67e09faca4a9d96df91
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447765"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>使用對稱金鑰證明來建立和布建 IoT Edge 裝置

Azure IoT Edge 裝置可以使用裝置布建 [服務](../iot-dps/index.yml) 來自動布建，就像未啟用 Edge 的裝置一樣。 如果您不熟悉自動布建程式，請先參閱布建 [總覽，](../iot-dps/about-iot-dps.md#provisioning-process) 再繼續進行。

本文說明如何使用下列步驟，在 IoT Edge 裝置上使用對稱金鑰證明來建立裝置布建服務個別註冊：

* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 建立裝置的個別註冊。
* 安裝 IoT Edge 執行時間，並連接到 IoT 中樞。

對稱金鑰證明是驗證裝置與裝置佈建服務執行個體的簡單方法。 對於不熟悉裝置佈建或沒有嚴格安全性需求的開發人員，這個證明方法代表 "Hello world" 經驗。 使用 [TPM](../iot-dps/concepts-tpm-attestation.md) 或 [x.509 憑證](../iot-dps/concepts-security.md#x509-certificates) 的裝置證明更為安全，而且應該用於更嚴格的安全性需求。

## <a name="prerequisites"></a>必要條件

* 主動 IoT 中樞
* 實體或虛擬裝置

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="choose-a-unique-registration-id-for-the-device"></a>為裝置選擇唯一的註冊識別碼

必須定義唯一的註冊識別碼以識別每個裝置。 您可以使用 MAC 位址、序號或裝置的任何唯一資訊。

在此範例中，我們會使用 MAC 位址和序號的組合，形成註冊識別碼的下列字串： `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` 。

為您的裝置建立唯一的註冊識別碼。 有效字元為小寫英數字元與破折號 ('-')。

## <a name="create-a-dps-enrollment"></a>建立 DPS 註冊

使用您裝置的註冊識別碼，在 DPS 中建立個別的註冊。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-at-scale.md)。

> [!TIP]
> 使用對稱金鑰證明時，也可以使用群組註冊，並與個別註冊包含相同的決策。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置布建服務實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選取 [新增個別註冊]****，然後完成下列步驟以設定註冊：  

   1. 針對 [ **機制**] 選取 [ **對稱金鑰**]。

   1. 選取 [ **自動產生金鑰** ] 核取方塊。

   1. 提供您為裝置建立的 **註冊識別碼** 。

   1. 如果您想要的話，請為您的裝置提供 **IoT 中樞裝置識別碼** 。 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 如果您未提供裝置識別碼，則會使用註冊識別碼。

   1. 選取 [ **True** ] 以宣告註冊適用于 IoT Edge 裝置。 針對群組註冊，所有裝置都必須是 IoT Edge 的裝置，否則不能是任何裝置。

   > [!TIP]
   > 在 Azure CLI 中，您可以建立 [註冊](/cli/azure/ext/azure-iot/iot/dps/enrollment) 或 [註冊群組](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) ，並使用 **啟用 edge** 的旗標來指定裝置或裝置群組是 IoT Edge 裝置。

   1. 接受裝置布建服務配置原則中的預設值，以瞭解 **您要如何將裝置指派給中樞** ，或選擇此註冊特定的不同值。

   1. 選擇您的裝置所要連接的連結 **IoT 中樞**。 您可以選擇多個中樞，並根據所選的配置原則將裝置指派給其中一個。

   1. 選擇當裝置第一次要求布建時， **您要如何在重新布建時處理裝置資料** 。

   1. 視需要將標記值新增至 [初始裝置對應項狀態]****。 您可以使用標記將裝置群組設定為模組部署的目標。 例如：

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

   1. 確定 [ **啟用專案** ] 設定為 [ **啟用**]。

   1. 選取 [儲存]。

現在此裝置已有註冊，IoT Edge 執行時間可以在安裝期間自動布建裝置。 請務必複製您註冊的 **主要金鑰** 值，以在安裝 IoT Edge 執行時間時使用，或者，如果您要建立與群組註冊搭配使用的裝置金鑰。

## <a name="derive-a-device-key"></a>衍生裝置金鑰

> [!NOTE]
> 只有在使用群組註冊時，才需要此區段。

每個裝置都會使用其衍生的裝置金鑰搭配您的唯一註冊識別碼，以在布建期間搭配註冊來執行對稱金鑰證明。 若要產生裝置金鑰，請使用您從 DPS 註冊複製的金鑰來計算裝置的唯一註冊識別碼的 [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) ，然後將結果轉換為 Base64 格式。

請勿在您的裝置程式碼中包含您註冊的主要或次要金鑰。

### <a name="linux-workstations"></a>Linux 工作站

如果您使用 Linux 工作站，您可以使用 openssl 來產生衍生的裝置金鑰，如下列範例所示。

將 **KEY** 的值取代為您先前記下的 [主要金鑰]****。

將 **REG_ID** 的值取代為您裝置的註冊識別碼。

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>以 Windows 為基礎的工作站

如果您使用以 Windows 為基礎的工作站，您可以使用 PowerShell 來產生衍生的裝置金鑰，如下列範例所示。

將 **KEY** 的值取代為您先前記下的 [主要金鑰]****。

將 **REG_ID** 的值取代為您裝置的註冊識別碼。

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會部署在所有 IoT Edge 裝置上。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。

布建您的裝置時，您將需要下列資訊：

* DPS **識別碼範圍** 值
* 您建立的裝置**註冊識別碼**
* 您從 DPS 註冊複製的**主要金鑰**

> [!TIP]
> 針對群組註冊，您需要每個裝置的 [衍生金鑰](#derive-a-device-key) ，而不是 DPS 註冊金鑰。

### <a name="linux-device"></a>Linux 裝置

遵循您裝置架構的指示。 請務必將 IoT Edge 執行階段設定為自動佈建，而不是手動佈建。

[在 Linux 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)

對稱金鑰布建的設定檔中的區段如下所示：

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "<SCOPE_ID>"
   attestation:
      method: "symmetric_key"
      registration_id: "<REGISTRATION_ID>"
      symmetric_key: "<SYMMETRIC_KEY>"
```

將、和的預留位置值取代為 `<SCOPE_ID>` `<REGISTRATION_ID>` 您稍 `<SYMMETRIC_KEY>` 早收集到的資料。 請確定布建 **：** 行沒有先前的空格，而且嵌套的專案是以兩個空格縮排。

### <a name="windows-device"></a>Windows 裝置

在您產生衍生裝置金鑰的裝置上安裝 IoT Edge 執行時間。 您將設定 IoT Edge 執行時間進行自動、非手動、布建。

如需有關在 Windows 上安裝 IoT Edge 的詳細資訊，包括管理容器和更新 IoT Edge 等工作的必要條件和指示，請參閱 [在 windows 上安裝 Azure IoT Edge 執行時間](how-to-install-iot-edge-windows.md)。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 安裝 IoT Edge 時，請務必使用 PowerShell 的 AMD64 會話，而不是 PowerShell (x86) 。

1. **IoTEdge**命令會檢查您的 Windows 電腦是否位於支援的版本、開啟容器功能，然後下載 moby 執行時間和 IoT Edge 執行時間。 命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此時，IoT 核心裝置可能會自動重新開機。 其他 Windows 10 或 Windows Server 裝置可能會提示您重新開機。 若是如此，請立即重新開機您的裝置。 當您的裝置準備就緒之後，請再次以系統管理員身分執行 PowerShell。

1. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 除非您使用 `-Dps` 旗標來使用自動布建，否則命令會預設為使用 Windows 容器進行手動布建。

   將、和的預留位置值取代為 `{scope_id}` `{registration_id}` 您稍 `{symmetric_key}` 早收集到的資料。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至 IoT 中樞，並開始將 IoT Edge 模組部署到您的裝置。 請在您的裝置上使用下列命令，確認執行階段已成功安裝並啟動。

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

您可以確認已使用您在裝置布建服務中建立的個別註冊。 在 Azure 入口網站中，流覽至您的裝置布建服務實例。 開啟您所建立個別註冊的註冊詳細資料。 請注意，會 **指派** 註冊的狀態並列出裝置識別碼。

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 瞭解如何使用 Azure 入口網站或[使用 Azure CLI](how-to-deploy-cli-at-scale.md)，[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)。
