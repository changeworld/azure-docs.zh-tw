---
title: Linux VM 上使用虛擬 TPM 預配裝置 - Azure IoT 邊緣
description: 在 Linux VM 上使用模擬的 TPM 來測試 Azure IoT Edge 的 Azure 裝置佈建服務
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/2/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b62f551e2532e0205159358b3618695524ae85c8
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666695"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Linux 虛擬機器上使用虛擬 TPM 建立與預配 IoT 邊緣裝置

Azure IoT 邊緣裝置可以使用[設備預配服務](../iot-dps/index.yml)自動預配。 如果您不熟悉自動預配過程,請查看[自動預配概念](../iot-dps/concepts-auto-provisioning.md),然後再繼續。

本文展示如何在類比 IoT Edge 裝置上測試自動設定,這些步驟包括:

* 使用保護硬體的模擬信任平台模組 (TPM)，在 Hyper-V 中建立 Linux 虛擬機器 (VM)。
* 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
* 建立裝置的個別註冊
* 安裝 IoT Edge 執行階段，並將裝置連線至 IoT 中樞

> [!TIP]
> 本文介紹如何使用 TPM 模擬器測試 DPS 預配,但大部分都適用於物理 TPM 硬體,如用於 IoT 設備的 Azure 認證的[Itfineon&trade; OPTIGA TPM。](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)
>
> 如果您使用的是物理設備,則可以跳到本文中[的物理設備部分的"檢索預配資訊](#retrieve-provisioning-information-from-a-physical-device)"。

## <a name="prerequisites"></a>Prerequisites

* [已啟用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 開發機器。 本文使用執行 Ubuntu Server VM 的 Windows 10。
* 使用中的 IoT 中樞。
* 如果使用類比 TPM,Visual [Studio](https://visualstudio.microsoft.com/vs/) 2015 或更高版本啟用[了"啟用了C++桌面開發"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作負載。

> [!NOTE]
> 在 DPS 中使用 TPM 認證時,需要 TPM 2.0,只能用於創建單個註冊,而不是組註冊。

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>使用虛擬 TPM 建立 Linux 虛擬機器

在本節中,您將在 Hyper-V 上創建新的 Linux 虛擬機。 使用類比 TPM 設定此虛擬機器,以便使用它測試自動預配如何與 IoT Edge 配合使用。

### <a name="create-a-virtual-switch"></a>建立虛擬交換器

虛擬交換器可讓您的虛擬機器連線至實體網路。

1. 在 Windows 電腦上打開超 V 管理器。

2. 在 [動作]**** 功能表中，選取 [虛擬交換器管理員]****。

3. 選擇 [外部]**** 虛擬交換器，然後選取 [建立虛擬交換器]****。

4. 為新的虛擬交換器指定名稱，例如 **EdgeSwitch**。 確定連線類型設定為 [外部網路]****，然後選取 [確定]****。

5. 快顯視窗會警告您網路連線可能會中斷。 選擇 **「是**」以繼續。

如果您在建立新的虛擬交換器時看到錯誤，請確定沒有其他交換器正在使用乙太網路配接器，且沒有其他參數使用相同的名稱。

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 下載要用於虛擬機器的磁碟映像檔，並將其儲存於本機。 例如 [Ubuntu Server](https://www.ubuntu.com/download/server)。

2. 再次在 Hyper-V 管理器中,在 **「操作」** 選單中選擇 **「新** > **虛擬機**」。

3. 使用下列特定組態完成 [新增虛擬機器精靈]****：

   1. **指定世代**：選取 [第 2 代]****。 第 2 代虛擬機器已啟用嵌套虛擬化,這是在虛擬機器上執行 IoT Edge 所必需的。
   2. **設定網路功能**：將 [連線]**** 的值設定為您在上一節中建立的虛擬交換器。
   3. **安裝選項**：選取 [從可開機映像檔安裝作業系統]****，並瀏覽至您在本機儲存的磁碟映像檔。

4. 在嚮導中選擇 **「完成」** 以創建虛擬機器。

系統可能需要幾分鐘的時間來建立新的 VM。

### <a name="enable-virtual-tpm"></a>啟用虛擬 TPM

創建 VM 後,請打開其設置以啟用虛擬受信任的平臺模組 (TPM),該模組允許您自動預配裝置。

1. 選擇虛擬機器,然後開啟其**設定**。

2. 瀏覽至 [安全性]****。

3. 取消核取 [啟用安全開機]****。

4. 核取 [啟用信賴平台模組]****。

5. 按一下 [確定]  。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>啟動虛擬機器，並收集 TPM 資料

在虛擬機中,產生一個工具,可用於檢索裝置的**註冊 ID**和**背書金鑰**。

1. 啟動虛擬機並連接到虛擬機器。

1. 按照虛擬機中的提示完成安裝過程並重新啟動計算機。

1. 登錄到 VM,然後按照[設定 Linux 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步驟安裝和建構用於 C 的 Azure IoT 設備 SDK。

   >[!TIP]
   >在本文中,您將從虛擬機器複製和貼上,這很難透過 Hyper-V 管理器連接應用程式。 您可能希望透過 Hyper-V 管理員連接到虛擬機器一次,以檢索其`ifconfig`IP 位址: 。 然後,您可以使用 IP 位址透過 SSH 連線`ssh <username>@<ipaddress>`: 。

1. 運行以下命令以建構 SDK 工具,該工具從 TPM 模擬器檢索設備預配資訊。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 從命令視窗導航到`azure-iot-sdk-c`目錄並運行 TPM 模擬器。 它會透過連接埠 2321年和 2322 上的通訊端接聽。 不要關閉此命令視窗;因此,不要關閉此命令視窗。您將需要保持此模擬器運行。

   從目錄執行`azure-iot-sdk-c`以下指令來啟動模擬器:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. 使用 Visual Studio,打開`cmake`在`azure_iot_sdks.sln`名為的 目錄中生成的解決方案,並使用 **「生成」** 功能表上的**生成解決方案**命令生成它。

1. 在 Visual Studio 的 [方案總管]**** 窗格中，瀏覽至 **Provision\_Tools** 資料夾。 以滑鼠右鍵按一下 **tpm_device_provision** 專案，然後選取 [設為起始專案]****。

1. 使用 **「調試」** 選單上的「**開始」** 命令之一運行解決方案。 輸出視窗顯示 TPM 模擬器的**註冊 ID**和**背書密鑰**,稍後在設備創建個人註冊時應複製該密鑰,您可以在其中關閉此視窗(使用註冊 ID 和背書密鑰),但保持 TPM 模擬器視窗運行。

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>從物理裝置檢索預配資訊

在設備上,生成可用於檢索設備的預配資訊的工具。

1. 按照[設置 Linux 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步驟安裝和建構用於 C 的 Azure IoT 裝置 SDK。

1. 運行以下命令以建構 SDK 工具,該工具從 TPM 設備檢索設備預配資訊。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 複製**註冊識別碼**和**背書金鑰**的值。 您可以使用這些值為 DPS 中的裝置建立個別的註冊。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="create-a-dps-enrollment"></a>建立 DPS 註冊

從您的虛擬機器擷取佈建資訊，並使用該資訊在裝置佈建服務中建立個別的註冊。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-monitor.md)。

> [!TIP]
> 在 Azure CLI 中,您可以建立[註冊](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment)或[註冊組](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group),並使用**啟用邊緣**的標誌指定設備或設備組是 IoT Edge 裝置。

1. 在[Azure 門戶](https://portal.azure.com)中,導航到 IoT 中心設備預配服務的實例。

2. 在 [設定]**** 下方，選取 [管理註冊]****。

3. 選取 [新增個別註冊]****，然後完成下列步驟以設定註冊：  

   1. 針對 [機制]****，選取 [TPM]****。

   2. 提供從虛擬機複製**的背書密鑰**和**註冊ID。**

      > [!TIP]
      > 如果您使用的是物理 TPM 設備,則需要確定**背書密鑰**,這是每個 TPM 晶片的唯一密鑰,並且是從與其關聯的 TPM 晶片製造商獲得的。 例如,您可以通過創建背書密鑰的 SHA-256 哈希來派生 TPM 設備的唯一**註冊 ID。**

   3. 選擇**True**可聲明此虛擬機器是 IoT 邊緣裝置。

   4. 選擇您的裝置所要連接的連結 **IoT 中樞**。 您可以選擇多個集線器,並且設備將根據所選的分配策略分配給其中一個集線器。

   5. 視需要提供裝置的識別碼。 您可以使用裝置識別碼，將個別裝置設為模組部署的目標。 如果您不提供設備 ID,則使用註冊 ID。

   6. 視需要將標記值新增至 [初始裝置對應項狀態]****。 您可以使用標記將裝置群組設定為模組部署的目標。 例如：

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

   7. 選取 [儲存]  。

現在,此設備存在註冊,IoT Edge 運行時可以在安裝過程中自動預配設備。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。 在虛擬機器上安裝 IoT Edge 執行階段。

開始閱讀您的裝置類型適用的文章之前，請先了解您的 DPS [識別碼範圍]**** 和裝置的 [註冊識別碼]****。 如果您安裝了範例 Ubuntu Server，請使用 **x64** 指示。 請務必將 IoT Edge 執行階段設定為自動佈建，而不是手動佈建。

[在 Linux 安裝 Azure IoT 邊緣執行時](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>為 IoT Edge 指定對 TPM 的存取權

若要讓 IoT Edge 執行階段自動佈建您的裝置，該執行階段必須能夠存取 TPM。

您可以覆寫 systemd 設定，授予對 IoT Edge 執行階段的 TPM 存取權，讓 **iotedge** 服務有根權限。 如果不想提高服務權限，也可以使用下列步驟，手動提供 TPM 存取權。

1. 尋找 TPM 硬體模組在您裝置上的檔案路徑，並將其儲存為本機變數。

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. 建立讓 IoT Edge 執行階段能夠存取 tpm0 的新規則。

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. 開啟規則檔案。

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. 將下列存取資訊複製到規則檔案中。

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. 儲存並結束檔案。

6. 觸發程序 udev 系統以評估新規則。

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. 確認規則已成功套用。

   ```bash
   ls -l /dev/tpm0
   ```

   成功的輸出顯示如下：

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   如果未發現正確權限套用完成，請嘗試重新啟動電腦，以重新整理 udev。

## <a name="restart-the-iot-edge-runtime"></a>重新啟動 IoT Edge 執行階段

重新啟動 IoT Edge 執行階段，使其可取得您對裝置所做的所有組態變更。

   ```bash
   sudo systemctl restart iotedge
   ```

確認 IoT Edge 執行階段正在執行。

   ```bash
   sudo systemctl status iotedge
   ```

如果您看到佈建錯誤，可能是因為組態變更尚未生效。 請嘗試重新啟動 IoT Edge 精靈。

   ```bash
   sudo systemctl daemon-reload
   ```

或者，請嘗試重新啟動虛擬機器，以確認變更是否會在重新啟動後生效。

## <a name="verify-successful-installation"></a>確認安裝成功

如果執行階段順利啟動，您可以移至您的 IoT 中樞，並看到新裝置已自動佈建。 現在您的裝置已準備好執行 IoT Edge 模組。

檢查 IoT Edge 精靈的狀態。

```cmd/sh
systemctl status iotedge
```

檢查精靈記錄。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

列出執行中的模組。

```cmd/sh
iotedge list
```

您可以驗證您在設備預配服務中創建的單個註冊是否已使用。 導航到 Azure 門戶中的設備預配服務實例。 打開您創建的單個註冊的註冊詳細資訊。 請注意,已**分配**註冊狀態並列出設備 ID。

## <a name="next-steps"></a>後續步驟

佈建新裝置時，裝置佈建服務註冊程序可讓您同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 瞭解如何使用 Azure 門戶或使用[Azure CLI](how-to-deploy-monitor-cli.md)[大規模部署與監視 IoT 邊緣模組](how-to-deploy-monitor.md)。
