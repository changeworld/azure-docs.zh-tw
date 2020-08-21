---
title: 在 Linux VM 上使用虛擬 TPM 布建裝置-Azure IoT Edge
description: 在 Linux VM 上使用模擬的 TPM 來測試 Azure IoT Edge 的 Azure 裝置佈建服務
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3bc9344459802f4bb4268093d905a051525d78dc
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684451"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>在 Linux 上建立和布建具有 TPM 的 IoT Edge 裝置

本文說明如何使用 (TPM) 的可信賴平臺模組，在 Linux IoT Edge 裝置上測試自動布建。 您可以使用裝置布建 [服務](../iot-dps/index.yml)來自動布建 Azure IoT Edge 裝置。 如果您不熟悉自動布建程式，請先參閱自動布 [建的概念](../iot-dps/concepts-auto-provisioning.md) ，再繼續進行。

工作如下所示：

1. 使用保護硬體的模擬信任平台模組 (TPM)，在 Hyper-V 中建立 Linux 虛擬機器 (VM)。
1. 建立 IoT 中樞裝置佈建服務 (DPS) 的執行個體。
1. 建立裝置的個別註冊。
1. 安裝 IoT Edge 執行階段，並將裝置連線到 IoT 中樞。

> [!TIP]
> 本文說明如何使用 TPM 模擬器測試 DPS 布建，但其中大部分適用于實體 TPM 硬體，例如 [INFINEON OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board)、Azure IoT 認證裝置。
>
> 如果您使用的是實體裝置，可以直接跳到本文中的 [從實體裝置取出](#retrieve-provisioning-information-from-a-physical-device) 布建資訊一節。

## <a name="prerequisites"></a>先決條件

* [已啟用 Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 的 Windows 開發機器。 本文使用執行 Ubuntu Server VM 的 Windows 10。
* 使用中的 IoT 中樞。

> [!NOTE]
> 使用 TPM 證明搭配 DPS 時需要 TPM 2.0，而且只能用來建立個別的（而非群組）註冊。

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>使用虛擬 TPM 建立 Linux 虛擬機器

在本節中，您會在 Hyper-v 上建立新的 Linux 虛擬機器。 您可以使用模擬的 TPM 來設定此虛擬機器，以測試自動布建搭配 IoT Edge 的運作方式。

### <a name="create-a-virtual-switch"></a>建立虛擬交換器

虛擬交換器可讓您的虛擬機器連線至實體網路。

1. 在 Windows 電腦上開啟 [Hyper-v 管理員]。

2. 在 [動作]**** 功能表中，選取 [虛擬交換器管理員]****。

3. 選擇 [外部]**** 虛擬交換器，然後選取 [建立虛擬交換器]****。

4. 為新的虛擬交換器指定名稱，例如 **EdgeSwitch**。 確定連線類型設定為 [外部網路]****，然後選取 [確定]****。

5. 快顯視窗會警告您網路連線可能會中斷。 選取 [是] 以繼續。

如果您在建立新的虛擬交換器時看到錯誤，請確定沒有其他交換器正在使用乙太網路配接器，且沒有其他參數使用相同的名稱。

### <a name="create-virtual-machine"></a>建立虛擬機器

1. 下載要用於虛擬機器的磁碟映像檔，並將其儲存於本機。 例如， [Ubuntu server 18.04](http://releases.ubuntu.com/18.04/)。 如需 IoT Edge 裝置支援之作業系統的詳細資訊，請參閱 [Azure IoT Edge 支援的系統](support.md)。

2. 在 [hyper-v 管理員] 中，選取**Action**  >  **New**  >  [**動作**] 功能表中的 [動作新增**虛擬機器**]。

3. 使用下列特定組態完成 [新增虛擬機器精靈]****：

   1. **指定世代**：選取 [第 2 代]****。 第2代虛擬機器已啟用「嵌套虛擬化」，這是在虛擬機器上執行 IoT Edge 所需的虛擬機器。
   2. **設定網路功能**：將 [連線]**** 的值設定為您在上一節中建立的虛擬交換器。
   3. **安裝選項**：選取 [從可開機映像檔安裝作業系統]****，並瀏覽至您在本機儲存的磁碟映像檔。

4. 選取嚮導中的 **[完成** ]，以建立虛擬機器。

系統可能需要幾分鐘的時間來建立新的 VM。

### <a name="enable-virtual-tpm"></a>啟用虛擬 TPM

一旦建立 VM 之後，請開啟其設定，以啟用虛擬的可信賴平臺模組 (TPM) 可讓您自動布建裝置。

1. 在 [Hyper-v 管理員] 中，以滑鼠右鍵按一下 VM，然後選取 [ **設定**]。

2. 瀏覽至 [安全性]。

3. 取消核取 [啟用安全開機]****。

4. 核取 [啟用信賴平台模組]****。

5. 按一下 [確定]。  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>啟動虛擬機器，並收集 TPM 資料

在虛擬機器中，建立可用來取得裝置 **註冊識別碼** 和 **簽署金鑰**的工具。

1. 在 [Hyper-v 管理員] 中，啟動 VM 並聯機至 VM。

1. 遵循虛擬機器中的提示來完成安裝程式，並重新啟動電腦。

1. 登入您的 VM，然後依照 [設定 Linux 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 中的步驟來安裝和建立適用于 C 的 Azure IOT 裝置 SDK。

   >[!TIP]
   >在本文中，您將會在虛擬機器上複製並貼上，而這不容易透過 Hyper-v 管理員連線應用程式。 您可能想要透過 Hyper-v 管理員連接到虛擬機器一次，以抓取其 IP 位址。 先執行 `sudo apt install net-tools` ，然後執行 `hostname -I` 。 然後，您可以使用 IP 位址透過 SSH 連接： `ssh <username>@<ipaddress>` 。

1. 執行下列命令來建立 SDK 工具，以從 TPM 抓取您的裝置布建資訊。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. [輸出] 視窗會顯示裝置的 **註冊識別碼** 和 **簽署金鑰**。 複製這些值，以在稍後為您的裝置建立個別註冊時使用。

擁有註冊識別碼和簽署金鑰之後，請繼續進行「[設定 IoT 中樞裝置](#set-up-the-iot-hub-device-provisioning-service)布建服務」一節。

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>從實體裝置取出布建資訊

如果您使用的是實體 IoT Edge 裝置，而不是 VM，請建立一個可供您用來取得裝置布建資訊的工具。

1. 依照 [設定 Linux 開發環境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 中的步驟來安裝和建立適用于 C 的 Azure IOT 裝置 SDK。

1. 執行下列命令來建立 SDK 工具，以從 TPM 裝置抓取您的裝置布建資訊。

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 複製 [ **註冊識別碼** ] 和 [ **簽署金鑰**] 的值。 您可以使用這些值為 DPS 中的裝置建立個別的註冊。

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>設定 IoT 中樞裝置佈建服務

在 Azure 中建立 IoT 中樞裝置佈建服務的新執行個體，並將其連結至您的 IoT 中樞。 您可以依照[設定 IoT 中樞 DPS](../iot-dps/quick-setup-auto-provision.md) 中的指示操作。

裝置佈建服務開始執行之後，請從 [概觀] 頁面複製 [識別碼範圍]**** 的值。 當您設定 IoT Edge 執行階段時會用到此值。

## <a name="create-a-dps-enrollment"></a>建立 DPS 註冊

從您的虛擬機器擷取佈建資訊，並使用該資訊在裝置佈建服務中建立個別的註冊。

在 DPS 中建立註冊時，您就有機會宣告**初始裝置對應項狀態**。 在裝置對應項中，您可以根據解決方案中需要的任何計量 (例如區域、環境、位置或裝置類型) 來設定標記，進而將裝置分組。 這些標記會用來建立[自動部署](how-to-deploy-at-scale.md)。

> [!TIP]
> 在 Azure CLI 中，您可以建立 [註冊](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) 並使用 **啟用 edge** 的旗標來指定裝置為 IoT Edge 裝置。

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您的 IoT 中樞裝置布建服務實例。

2. 在 [設定]**** 下方，選取 [管理註冊]****。

3. 選取 [新增個別註冊]****，然後完成下列步驟以設定註冊：  

   1. 針對 [機制]****，選取 [TPM]****。

   2. 提供您從虛擬機器複製的 **簽署金鑰** 和 **註冊識別碼** 。

      > [!TIP]
      > 如果您使用的是實體 TPM 裝置，您需要判斷 **簽署金鑰**，這對每個 tpm 晶片而言是唯一的，而且是從與其相關聯的 tpm 晶片製造商取得。 例如，您可以建立 TPM 裝置的唯一 **註冊識別碼** ，例如建立簽署金鑰的 256 sha-1 雜湊。

   3. 視需要提供裝置的識別碼。 如果您未提供裝置識別碼，則會使用註冊識別碼。

   4. 選取 [ **True** ] 以宣告此虛擬機器是 IoT Edge 裝置。

   5. 選擇您想要與裝置連線的連結 IoT 中樞，或選取 [ **連結至新的 Iot 中樞**]。 您可以選擇多個中樞，並根據所選取的指派原則，將裝置指派給其中一個。

   6. 視需要將標記值新增至 [初始裝置對應項狀態]****。 您可以使用標記將裝置群組設定為模組部署的目標。 如需詳細資訊，請參閱 [大規模部署 IoT Edge 模組](how-to-deploy-at-scale.md)。

   7. 選取 [儲存]。

現在此裝置已有註冊，IoT Edge 執行時間可以在安裝期間自動布建裝置。

## <a name="install-the-iot-edge-runtime"></a>安裝 IoT Edge 執行階段

IoT Edge 執行階段會在所有 IoT Edge 裝置上部署。 其元件會在容器中執行，並可讓您將其他容器部署到裝置，以便您在 Edge 上執行程式碼。 在虛擬機器上安裝 IoT Edge 執行階段。

開始閱讀您的裝置類型適用的文章之前，請先了解您的 DPS [識別碼範圍]**** 和裝置的 [註冊識別碼]****。 如果您安裝了範例 Ubuntu Server，請使用 **x64** 指示。 請務必將 IoT Edge 執行階段設定為自動佈建，而不是手動佈建。

當您進入設定安全 daemon 的步驟時，請務必選擇 [ [選項2自動](how-to-install-iot-edge-linux.md#option-2-automatic-provisioning) 布建] 並設定 TPM 證明。

[在 Linux 上安裝 Azure IoT Edge 執行階段](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>為 IoT Edge 指定對 TPM 的存取權

IoT Edge 執行時間需要存取 TPM，才能自動布建您的裝置。

您可以藉由覆寫 systemd 設定讓 TPM 存取 IoT Edge 執行時間，讓 `iotedge` 服務具有根許可權。 如果不想提高服務權限，也可以使用下列步驟，手動提供 TPM 存取權。

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
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
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

   成功的輸出會如下所示：

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

您可以確認已使用您在裝置布建服務中建立的個別註冊。 在 Azure 入口網站中，流覽至您的裝置布建服務實例。 開啟您所建立個別註冊的註冊詳細資料。 請注意，會 **指派** 註冊的狀態並列出裝置識別碼。

## <a name="next-steps"></a>後續步驟

DPS 註冊程式可讓您在布建新裝置時，同時設定裝置識別碼和裝置對應項標記。 您可以使用這些值來鎖定要使用自動裝置管理的個別裝置或裝置群組。 瞭解如何使用 Azure 入口網站或[使用 Azure CLI](how-to-deploy-cli-at-scale.md)，[大規模部署和監視 IoT Edge 模組](how-to-deploy-at-scale.md)。
