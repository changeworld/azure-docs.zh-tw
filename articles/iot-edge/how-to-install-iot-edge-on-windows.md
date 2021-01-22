---
title: 在 Windows 上安裝適用于 Linux 的 Azure IoT Edge |Microsoft Docs
description: 在 Windows 裝置上 Azure IoT Edge 安裝指示
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e603e99c1aa9c49d20bdb281bbfd96131ae2efdd
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663616"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>在 Windows 裝置上安裝和布建適用于 Linux 的 Azure IoT Edge (Preview) 

Azure IoT Edge 執行階段可將裝置變成 IoT Edge 裝置。 執行時間可以部署在電腦類別到工業伺服器的裝置上。 在裝置上設定 IoT Edge 執行階段後，就可以開始從雲端將商務邏輯部署到裝置上。 若要深入瞭解，請參閱 [瞭解 Azure IoT Edge 執行時間及其架構](iot-edge-runtime.md)。

Windows 上適用于 Linux 的 Azure IoT Edge 可讓您使用 Linux 虛擬機器，在 Windows 裝置上使用 Azure IoT Edge。 Linux 版本的 Azure IoT Edge 以及任何部署的 Linux 模組都會在虛擬機器上執行。 從該處，Windows 應用程式和程式碼以及 IoT Edge 執行時間和模組都可以自由地彼此互動。

本文列出在 Windows 裝置上設定 IoT Edge 的步驟。 這些步驟會部署 Linux 虛擬機器，其中包含要在您的 Windows 裝置上執行的 IoT Edge 執行時間，然後使用其 IoT 中樞裝置身分識別來布建裝置。

>[!NOTE]
>Windows 上適用于 Linux 的 IoT Edge 處於 [公開預覽](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)狀態。

## <a name="prerequisites"></a>必要條件

* 具有有效訂用帳戶的 Azure 帳戶。 如果您沒有 [Azure 訂用帳戶](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

* 在 Azure 中擁有免費或標準層的 [IoT 中樞](../iot-hub/iot-hub-create-through-portal.md)。

* 具有下列最低系統需求的 Windows 裝置：

  * Windows 10 1809 版或更新版本;組建17763或更新版本
  * Professional、Enterprise 或 Server 版本
  * RAM 下限： 4 GB (8 GB 的建議) 
  * 儲存體下限： 10 GB

* 使用已安裝 Windows Admin Center 的 Azure IoT Edge 擴充功能來存取 Windows Admin Center insider build：  <!-- The link below needs the language localization to work; otherwise broken -->
   1. 造訪 [Windows Insider Preview](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewserver)。

   1. 在 [預覽] 下拉式清單中，選取 **Windows Admin Center Preview-組建 2012**]，然後選擇 [ **確認**]。

      ![從可用預覽的下拉式功能表中選擇 [Windows Admin Center Preview-組建 2012]。](./media/how-to-install-iot-edge-on-windows/select-windows-admin-center-preview-build.png)

   1. 在 [ **選取語言** ] 下拉式清單中，選擇 [ **英文**]，然後選擇 [ **確認**]。

   1. 選擇 [ **立即下載** ] 以下載 *WindowsAdminCenterPreview2012.msi*。

   1. 執行 *WindowsAdminCenterPreview2012.msi* ，並遵循安裝精靈提示安裝 Windows Admin Center。 安裝之後，開啟 Windows Admin Center。

   1. 第一次使用 Windows Admin Center 時，系統會提示您選取要使用的憑證。 選取 [ **Windows Admin Center 用戶端** ] 作為您的憑證。

   1. 這是安裝 Azure IoT Edge 擴充功能的時候了。 選取 Windows Admin Center 儀表板右上角的齒輪圖示。

      ![選取儀表板右上方的齒輪圖示來存取設定。](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. 在 [ **設定** ] 功能表的 [ **閘道**] 底下，選取 [ **擴充** 功能]。

   1. 選取 [ **摘要** ] 索引標籤，然後選取 [ **新增**]。

   1. https://aka.ms/wac-insiders-feed在文字方塊中輸入，然後選取 [**加入**]。

   1. 新增摘要之後，請流覽至 [可用的 **擴充** 功能] 索引標籤。更新擴充功能清單可能需要一些時間。

   1. 在 [ **可用的擴充** 功能] 索引標籤上，尋找擴充功能清單中的 **Azure IoT Edge** 。 選擇它，然後選取延伸模組清單上方的 [ **安裝** ] 提示。

   1. 安裝完成之後，您應該會在 [ **已安裝的擴充** 功能] 索引標籤上看到已安裝的擴充功能清單中 Azure IoT Edge。

## <a name="choose-your-provisioning-method"></a>選擇您的布建方法

Windows 上適用于 Linux 的 Azure IoT Edge 支援下列布建方法：

* 使用 IoT Edge 裝置的連接字串進行手動布建。 若要使用此方法，請使用在 [IoT 中樞註冊 IoT Edge 裝置中](how-to-register-device.md)的步驟來註冊您的裝置，並取得連接字串。
  * 選擇對稱金鑰驗證選項，因為 Windows 上的 Linux IoT Edge 目前不支援 x.509 自我簽署憑證。
* 使用裝置布建服務 (DPS) 和對稱金鑰進行自動布建。 深入瞭解如何 [使用 DPS 和對稱金鑰建立和布建 IoT Edge 裝置](how-to-auto-provision-symmetric-keys.md)。
* 使用 DPS 和 x.509 憑證的自動布建。 深入瞭解如何 [使用 DPS 和 x.509 憑證來建立和布建 IoT Edge 裝置](how-to-auto-provision-x509-certs.md)。

手動布建可讓您更輕鬆地開始使用幾個裝置。 裝置布建服務很適合用來布建許多裝置。

如果您計畫使用其中一個 DPS 方法來布建您的裝置或裝置，請依照上面連結的適當文章中的步驟建立 DPS 的實例、將 DPS 實例連結至 IoT 中樞，以及建立 DPS 註冊。 您可以針對單一裝置或一組裝置的 *群組註冊*，建立 *個別的註冊*。 如需註冊類型的詳細資訊，請造訪 [Azure IoT 中樞裝置](https://docs.microsoft.com/azure/iot-dps/concepts-service#enrollment)布建服務的概念。

## <a name="create-a-new-deployment"></a>建立新的部署

在目標裝置上建立適用于 Linux 的 Azure IoT Edge 部署至您的目標裝置。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

在 Windows Admin Center 開始] 頁面的連線清單下方，您會看到本機主機連接，代表您執行 Windows Admin Center 的電腦。 您管理的任何其他伺服器、電腦或叢集也會顯示在此處。

您可以使用 Windows Admin Center，在您的本機裝置或遠端受管理的裝置上，針對 Windows 上的 Linux 進行安裝和管理 Azure IoT Edge。 在本指南中，本機主機連接將作為在 Windows 上部署 Linux Azure IoT Edge 的目標裝置。

如果您想要部署到遠端目標裝置，而不是您的本機裝置，而且在清單中看不到您想要的目標裝置，請依照 [指示新增您的裝置](https://docs.microsoft.com/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)。

   ![列出目標裝置的初始 Windows Admin Center 儀表板](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. 選取 [新增]  。

1. 在 [ **新增或建立資源** ] 窗格中，找出 **Azure IoT Edge** 圖格。 選取 [ **建立新** 的]，在裝置上安裝適用于 Linux 的新 Azure IoT Edge 實例。

   如果您已經在裝置上執行 Windows 的 Linux IoT Edge，您可以選取 [ **新增** ]，以連線到現有的 IoT Edge 裝置，並使用 Windows Admin Center 進行管理。

   ![在 Windows Admin Center 的 Azure IoT Edge 圖格上選取 [建立新的]](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. [ **在 Windows 部署中建立 Linux 的 Azure IoT Edge** ] 窗格隨即開啟。 在 **1 上。開始使用** ] 索引標籤上，確認您的目標裝置符合最低需求，然後選取 **[下一步]**。

1. 檢查授權條款，選取 [ **我接受**]，然後選取 **[下一步]**。

1. 您可以根據您的喜好設定，開啟或關閉 **選擇性的診斷資料** 。

1. 選取 **[下一步：部署]**。

   ![在切換選用的診斷資料至喜好設定之後，請選取 [下一步：部署] 按鈕](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. 在 **2。[部署** ] 索引標籤的 [ **選取目標裝置**] 底下，按一下您列出的裝置，以驗證其是否符合最低需求。 當其狀態確認為支援之後，請選取 **[下一步]**。

   ![選取您的裝置來確認其是否受支援](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. 接受 [ **2.2 設定** ] 索引標籤上的預設設定。

1. 在 [ **2.3 部署** ] 索引標籤上，您可以監看部署的進度。 完整的套裝程式括下載 Windows 套件上的 Linux Azure IoT Edge、安裝套件、設定主機裝置，以及設定 Linux 虛擬機器。 此程式可能需要幾分鐘的時間才能完成。 成功的部署如下圖所示。

   ![成功的部署將會以綠色核取記號和「完整」標籤顯示每個步驟](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

部署完成後，您就可以開始布建您的裝置。 選取 **[下一步]：連接** 以繼續進行 **3。** 用來處理 Azure IoT Edge 裝置布建的 [連接] 索引標籤。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

如果您尚未安裝，請將 Windows 上的 Linux IoT Edge 安裝到目標裝置。

> [!NOTE]
> 下列 PowerShell 程式概述如何在 Windows 上建立適用于 Linux 的 Azure IoT Edge 本機主機部署。 若要使用 PowerShell 建立遠端目標裝置的部署，您可以使用 [遠端 PowerShell](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_remote) 來建立與遠端裝置的連線，並在該裝置上遠端執行這些命令。

1. 在提高許可權的 PowerShell 會話中，執行下列每個命令以下載 Windows 上的 Linux IoT Edge。

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzureEdgeForLinuxOnWindowsMSI" -OutFile $msiPath
   ```

1. 在您裝置上的 Windows 上安裝適用于 Linux 的 IoT Edge。

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > 您可以將 INSTALLDIR = "<FULLY_QUALIFIED_PATH>" 和 VHDXDIR = "<FULLY_QUALIFIED_PATH>" 參數新增至上述安裝命令，以在 Windows 安裝和 VHDX 目錄上指定 Linux 的自訂 IoT Edge。

1. 若要讓部署成功執行，您必須將目標裝置上的執行原則設定為（ `AllSigned` 如果尚未這麼做）。 您可以使用下列命令，在提高許可權的 PowerShell 提示字元中檢查目前的執行原則：

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   如果的執行原則 `local machine` 不是 `AllSigned` ，您可以使用下列程式來設定執行原則：

   ```azurepowershell-interactive
   Set-ExecutionPolicy - ExecutionPolicy AllSigned -Force
   ```

1. 在 Windows 部署上建立適用于 Linux 的 IoT Edge。

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   <!-- Most likely temporary until cmdlet is fully documented -->
   > [!NOTE]
   > 您可以不使用參數來執行此命令，也可以選擇性地使用參數自訂部署。 檢查 PowerShell 模組 AzureEFLOW. .psm1 以查看參數及其意義 (請參閱 C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW) 。

1. 輸入 ' Y ' 以接受授權條款。

1. 輸入 ' O ' 或 ' R ' 可根據您的喜好設定，開啟或關閉 **選擇性的診斷資料** 。 成功的部署如下圖所示。

   ![成功的部署將會在訊息結尾顯示「部署成功」](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

部署完成後，您就可以開始布建您的裝置。

---

若要布建您的裝置，您可以遵循下列連結，跳至所選布建方法的區段：

* [選項1：使用 IoT Edge 裝置的連接字串進行手動布建](#option-1-provisioning-manually-using-the-connection-string)
* [選項2：使用裝置布建服務的自動布建 (DPS) 和對稱金鑰](#option-2-provisioning-via-dps-using-symmetric-keys)
* [選項3：使用 DPS 和 x.509 憑證的自動布建](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>布建您的裝置

選擇用來布建裝置的方法，並依照適當的章節中的指示進行。 您可以使用 Windows Admin Center 或提高許可權的 PowerShell 會話來布建您的裝置。

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>選項1：使用連接字串手動布建

本節說明如何使用 Azure IoT Edge 裝置的連接字串，以手動方式布建您的裝置。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. 在 [ **Azure IoT Edge 裝置** 布建] 窗格的 [布建方法] 下拉式清單中，選取 [ **連接字串] (手動)** 。

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，流覽至 IoT 中樞的 [ **IoT Edge** ] 索引標籤。

1. 按一下裝置的裝置識別碼。 複製 [ **主要連接字串** ] 欄位。

1. 將它貼入 Windows Admin Center 中的 [裝置連接字串] 欄位。 然後，選擇 **[使用選取的方法** 布建]。

   ![在貼上您裝置的連接字串之後，選擇以選取的方法布建](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. 布建完成後，請選取 **[完成]**。 系統會將您帶回主要儀表板。 現在，您應該會看到列出的新裝置，其類型為 `IoT Edge Devices` 。 您可以選取 IoT Edge 裝置來與其連線。 一旦在其 **[總覽** ] 頁面上，您就可以查看 **IoT Edge 的模組清單** ，並 IoT Edge 裝置的 **狀態** 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，流覽至 IoT 中樞的 [ **IoT Edge** ] 索引標籤。

1. 按一下裝置的裝置識別碼。 複製 [ **主要連接字串** ] 欄位。

1. 貼上下列命令中的預留位置文字，然後在目標裝置上提高許可權的 PowerShell 會話中執行。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>選項2：使用對稱金鑰透過 DPS 布建

本節說明如何使用 DPS 和對稱金鑰自動布建您的裝置。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. 在 [ **Azure IoT Edge 裝置** 布建] 窗格的 [布建方法] 下拉式清單中，選取 [ **對稱金鑰] (DPS)** 。

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，流覽至您的 DPS 實例。

1. 在 [ **總覽** ] 索引標籤上，複製 [ **識別碼範圍** ] 值。 將它貼入 Windows Admin Center 的 [範圍識別碼] 欄位中。

1. 在 Azure 入口網站的 [ **管理註冊** ] 索引標籤上，選取您所建立的註冊。 複製 [註冊詳細資料] 中的 **主要金鑰** 值。 將它貼入 Windows Admin Center 中的 [對稱金鑰] 欄位。

1. 在 Windows Admin Center 的 [註冊識別碼] 欄位中提供裝置的註冊識別碼。

1. 選擇 **[使用選取的方法** 布建]。

   ![在填寫對稱金鑰布建的必要欄位之後，選擇以選取的方法布建](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. 布建完成後，請選取 **[完成]**。 系統會將您帶回主要儀表板。 現在，您應該會看到列出的新裝置，其類型為 `IoT Edge Devices` 。 您可以選取 IoT Edge 裝置來與其連線。 一旦在其 **[總覽** ] 頁面上，您就可以查看 **IoT Edge 的模組清單** ，並 IoT Edge 裝置的 **狀態** 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 將下列命令複製到文字編輯器。 將預留位置文字取代為您的資訊（詳細資訊）。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，流覽至您的 DPS 實例。

1. 在 [ **總覽** ] 索引標籤上，複製 [ **識別碼範圍** ] 值。 將它貼到命令中適當的預留位置文字上。

1. 在 Azure 入口網站的 [ **管理註冊** ] 索引標籤上，選取您所建立的註冊。 複製 [註冊詳細資料] 中的 **主要金鑰** 值。 將它貼到命令中適當的預留位置文字上。

1. 提供裝置的註冊識別碼，以取代命令中適當的預留位置文字。

1. 在目標裝置上提高許可權的 PowerShell 會話中執行命令。

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>選項3：使用 x.509 憑證透過 DPS 布建

本節說明如何使用 DPS 和 x.509 憑證自動布建您的裝置。

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. 在 [ **Azure IoT Edge 裝置** 布建] 窗格的 [布建方法] 下拉式清單中，選取 [ **x.509 CERTIFICATE] (DPS)** 。

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，流覽至您的 DPS 實例。

1. 在 [ **總覽** ] 索引標籤上，複製 [ **識別碼範圍** ] 值。 將它貼入 Windows Admin Center 的 [範圍識別碼] 欄位中。

1. 在 Windows Admin Center 的 [註冊識別碼] 欄位中提供裝置的註冊識別碼。

1. 上傳您的憑證和私密金鑰檔案。

1. 選擇 **[使用選取的方法** 布建]。

   ![在填入 x.509 憑證布建的必要欄位之後，選擇以選取的方法布建](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. 布建完成後，請選取 **[完成]**。 系統會將您帶回主要儀表板。 現在，您應該會看到列出的新裝置，其類型為 `IoT Edge Devices` 。 您可以選取 IoT Edge 裝置來與其連線。 一旦在其 **[總覽** ] 頁面上，您就可以查看 **IoT Edge 的模組清單** ，並 IoT Edge 裝置的 **狀態** 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. 將下列命令複製到文字編輯器。 將預留位置文字取代為您的資訊（詳細資訊）。

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocWin <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. 在 [Azure 入口網站](https://ms.portal.azure.com/)中，流覽至您的 DPS 實例。

1. 在 [ **總覽** ] 索引標籤上，複製 [ **識別碼範圍** ] 值。 將它貼到命令中適當的預留位置文字上。

1. 提供裝置的註冊識別碼，以取代命令中適當的預留位置文字。

1. 將適當的預留位置文字取代為憑證檔案的絕對來源路徑。

1. 將適當的預留位置文字取代為私密金鑰檔案的絕對來源路徑。

1. 在目標裝置上提高許可權的 PowerShell 會話中執行命令。

---

## <a name="verify-successful-configuration"></a>確認設定成功

確認已成功在您的 IoT Edge 裝置上安裝和設定適用于 Windows 的 Linux IoT Edge。

1. 從 Windows Admin Center 中連接的裝置清單中選取您的 IoT Edge 裝置，以連線到該裝置。
1. 裝置總覽頁面會顯示裝置的一些資訊：

    1. **IoT Edge 模組清單**] 區段會顯示裝置上的執行中模組。 當 IoT Edge 服務第一次啟動時，您應該只會看到 **edgeAgent** 模組正在執行。 EdgeAgent 模組預設會執行，且有助於安裝及啟動您部署至裝置的任何其他模組。
    1. [ **IoT Edge 狀態** ] 區段會顯示服務狀態，而且應該會報告 **正在執行)** 的作用中 (。

1. 如果您需要針對 IoT Edge 服務進行疑難排解，請使用 [裝置] 頁面上的 **命令 Shell** 工具來 ssh (安全的 shell) 至虛擬機器，然後執行 Linux 命令。

    1. 如果您需要對服務進行疑難排解，請擷取服務記錄。

       ```bash
       journalctl -u iotedge
       ```

    2. 您可以使用此 `check` 工具來確認裝置的設定和連接狀態。

       ```bash
       sudo iotedge check
       ```

## <a name="next-steps"></a>下一步

繼續 [部署 IoT Edge 課程模組](how-to-deploy-modules-portal.md) ，以瞭解如何將模組部署到您的裝置。
