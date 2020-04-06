---
title: 使用對稱金鑰認證預配裝置 - Azure IoT 邊緣
description: 使用對稱金鑰認證使用裝置預先服務測試 Azure IoT 邊緣的自動裝置設定
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 4/3/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 139a2cafe137d000b991cbad8b8567e005ffc728
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668662"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>使用對稱金鑰認證建立與預配 IoT 邊緣裝置

可以使用[設備預配服務](../iot-dps/index.yml)自動預配 Azure IoT 邊緣設備,就像未啟用邊緣的設備一樣。 如果您不熟悉自動預配過程,請查看[自動預配概念](../iot-dps/concepts-auto-provisioning.md),然後再繼續。

本文展示如何在 IoT Edge 裝置上使用對稱金鑰認證創建設備預配服務個人註冊,並執行以下步驟:

* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 建立裝置的個別註冊。
* 安裝 IoT 邊緣執行時並連接到 IoT 中心。

對稱金鑰證明是驗證裝置與裝置佈建服務執行個體的簡單方法。 對於不熟悉裝置佈建或沒有嚴格安全性需求的開發人員，這個證明方法代表 "Hello world" 經驗。 使用[TPM](../iot-dps/concepts-tpm-attestation.md)或[X.509 憑證](../iot-dps/concepts-security.md#x509-certificates)的設備證明更安全,應用於更嚴格的安全要求。

## <a name="prerequisites"></a>Prerequisites

* 活動 IoT 中心
* 實體或虛擬裝置

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="choose-a-unique-registration-id-for-the-device"></a>為裝置選擇唯一的註冊識別碼

必須定義唯一的註冊識別碼以識別每個裝置。 您可以使用 MAC 位址、序號或裝置的任何唯一資訊。

這個選項, 您使用 MAC 位址與序列號的組合,組成以下字串的註冊代碼: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6`。

為您的裝置建立唯一的註冊識別碼。 有效字元為小寫英數字元與破折號 ('-')。

## <a name="create-a-dps-enrollment"></a>建立 DPS 註冊

使用設備的註冊ID在DPS中創建個人註冊。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。

> [!TIP]
> 使用對稱密鑰證明時,也可以進行組註冊,並且涉及與單個註冊相同的決策。

1. 在[Azure 門戶](https://portal.azure.com)中,導航到 IoT 中心設備預配服務的實例。

1. 在 [設定]**** 下方，選取 [管理註冊]****。

1. 選取 [新增個別註冊]****，然後完成下列步驟以設定註冊：  

   1. 對**機制**,選擇**對稱鍵**。

   1. 選擇 **「自動產生金鑰**」 「複選框」。

   1. 提供您為設備創建的**註冊ID。**

   1. 如果需要,可為設備提供**IoT中心設備ID。** 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 如果您不提供設備 ID,則使用註冊 ID。

   1. 選擇**True**可聲明註冊適用於 IoT 邊緣設備。 對於組註冊,所有設備都必須是 IoT Edge 設備,或者它們都不能是。

   > [!TIP]
   > 在 Azure CLI 中,您可以建立[註冊](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment)或[註冊組](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group),並使用**啟用邊緣**的標誌指定設備或設備組是 IoT Edge 裝置。

   1. 接受裝置預配服務分配策略中的預設值,**瞭解如何將設備分配給集線器**或選擇特定於此註冊的不同值。

   1. 選擇您的裝置所要連接的連結 **IoT 中樞**。 您可以選擇多個集線器,並且設備將根據所選的分配策略分配給其中一個集線器。

   1. 選擇您希望在裝置設定預先設定時 **, 在重新預先使用裝置資料**。

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

   1. 開啟**此選項,** 啟用項目設定為**開啟**。

   1. 選取 [儲存]  。

現在,此設備存在註冊,IoT Edge 運行時可以在安裝過程中自動預配設備。 請務必複製註冊的主**密鑰**值,以便安裝 IoT Edge 運行時時使用,或者如果要創建設備密鑰以與組註冊一起使用。

## <a name="derive-a-device-key"></a>衍生裝置金鑰

> [!NOTE]
> 僅當使用組註冊時,才需要此部分。

每個設備使用其派生的設備密鑰與唯一的註冊 ID 在預配期間對註冊執行對稱密鑰認證。 要生成設備密鑰,請使用從 DPS 註冊中複製的密鑰來計算裝置的唯一註冊 ID 的[HMAC-SHA256,](https://wikipedia.org/wiki/HMAC)並將結果轉換為 Base64 格式。

請勿在設備代碼中包含註冊的主密鑰或輔助密鑰。

### <a name="linux-workstations"></a>Linux 工作站

如果您使用 Linux 工作站，您可以使用 openssl 來產生衍生的裝置金鑰，如下列範例所示。

將 **KEY** 的值取代為您先前記下的 [主要金鑰]****。

將**REG_ID**的值替換為設備的註冊 ID。

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

將**REG_ID**的值替換為設備的註冊 ID。

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

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。

預配裝置時,您需要以下資訊:

* DPS **ID 範圍**值
* 您建立的裝置**註冊識別碼**
* 從 DPS 註冊複製**的主金鑰**

> [!TIP]
> 對於組註冊,您需要每個設備的[派生密鑰](#derive-a-device-key),而不是 DPS 註冊金鑰。

### <a name="linux-device"></a>Linux 裝置

按照設備體系結構的說明進行操作。 請務必將 IoT Edge 執行階段設定為自動佈建，而不是手動佈建。

[在 Linux 安裝 Azure IoT 邊緣執行時](how-to-install-iot-edge-linux.md)

用於對稱金鑰預配的設定檔中的部分如下所示:

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

將`<SCOPE_ID>`的占位符值`<REGISTRATION_ID>`替換`<SYMMETRIC_KEY>`為 前面收集的數據。 確保**預配:** 行沒有前面的空格,並且嵌套項由兩個空格縮進。

### <a name="windows-device"></a>Windows 裝置

在為其生成派生設備密鑰的設備上安裝 IoT Edge 運行時。 您將為自動(而不是手動)預配配置 IoT Edge 執行時。

有關在 Windows 上安裝 IoT Edge 的詳細資訊,包括管理容器和更新 IoT Edge 等工作的先決條件和說明,請參閱[在 Windows 上安裝 Azure IoT 邊緣執行時](how-to-install-iot-edge-windows.md)。

1. 在系統管理員模式下開啟 [Azure PowerShell] 視窗。 安裝物聯網邊緣時,請務必使用 AMD64 電源外殼會話,而不是 PowerShell (x86)。

1. **Deploy-IoTEdge**命令檢查 Windows 電腦是否位於受支援的版本上,打開容器功能,然後下載 moby 運行時和 IoT Edge 運行時。 該命令預設為使用 Windows 容器。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. 此時,IoT Core 設備可能會自動重新啟動。 其他 Windows 10 或 Windows 伺服器設備可能會提示您重新啟動。 如果是,請立即重新啟動設備。 設備準備就緒后,再次以管理員身份運行 PowerShell。

1. **Initialize-IoTEdge** 命令會設定機器的 IoT Edge 執行階段。 該指令預設為手動預配 Windows`-Dps`容器, 除非您使用該標誌使用自動預配。

   將`{scope_id}`的占位符值`{registration_id}`替換`{symmetric_key}`為 前面收集的數據。

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

您可以驗證您在設備預配服務中創建的單個註冊是否已使用。 導航到 Azure 門戶中的設備預配服務實例。 打開您創建的單個註冊的註冊詳細資訊。 請注意,已**分配**註冊狀態並列出設備 ID。

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 瞭解如何使用 Azure 門戶或使用[Azure CLI](how-to-deploy-monitor-cli.md)[大規模部署與監視 IoT 邊緣模組](how-to-deploy-monitor.md)。
