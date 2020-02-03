---
title: 自訂 Azure-SSIS 整合執行階段的安裝
description: 本文說明如何使用 Azure-SSIS 整合執行階段 (IR) 的自訂安裝介面來安裝其他元件或變更設定
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/01/2020
ms.openlocfilehash: e85ef22542fc162648dbfc637892cf7e580c6aac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964544"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>自訂 Azure-SSIS 整合執行階段的安裝

Azure-SSIS 整合執行階段的自訂安裝介面所提供的介面，可讓您自行新增在佈建或重新設定 Azure-SSIS IR 期間的安裝步驟。 

自訂安裝可讓您變更預設作業組態或環境 (例如，用以啟動其他 Windows 服務或保存檔案共用的存取認證)，或是在 Azure-SSIS IR 的每個節點上安裝其他元件 (例如組件、驅動程式或擴充功能)。

有兩種方式可在您的 Azure SSIS IR 上執行自訂的程式：無腳本的快速自訂，以及使用腳本的標準自訂設置。

透過快速自訂安裝，您可以執行一些常見的系統設定/Windows 命令，或安裝一些熱門/建議的其他元件，而不需使用任何腳本。  

透過標準的自訂設置，您需要準備腳本及其相關聯的檔案，並將它們全部一起上傳到您 Azure 儲存體帳戶中的 blob 容器。 接著，您會在布建或重新設定 Azure SSIS IR 時，為您的容器提供共用存取簽章（SAS）統一資源識別項（URI）。 您的 Azure SSIS IR 的每個節點都會從您的容器下載腳本及其相關聯的檔案，並以更高的許可權執行您的自訂安裝程式。 當您的自訂設定完成時，每個節點會將執行和其他記錄的標準輸出上傳至您的容器。

您可以使用快速/標準自訂安裝，同時安裝免費/未授權和付費/授權元件。 如果您是 ISV，請參閱我們的檔，[以瞭解如何開發 AZURE SSIS IR 的付費或授權元件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> Azure-SSIS IR 的 v2 系列節點不適用於自訂安裝，因此請改用 v3 系列節點。  如果您已經使用 v2 系列節點，請儘快切換成使用 v3 系列節點。

## <a name="current-limitations"></a>目前的限制

下列限制僅適用于標準的自訂設置：

- 如果您想要在腳本中使用 `gacutil.exe` 來安裝全域組件快取（GAC）中的元件，您必須提供自訂安裝程式中的 `gacutil.exe`，或使用我們的公用預覽容器中提供的複本。

- 如果您想要參考指令碼中的子資料夾，`msiexec.exe` 不支援以 `.\` 標記法參考根資料夾。 使用類似 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 的命令，而不是 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

- Azure SSIS IR 目前不支援系統管理共用（亦即 Windows 自動建立的隱藏網路共用）。

- Azure SSIS IR 不支援 IBM iSeries Access ODBC 驅動程式。 您可能會在自訂安裝期間看到安裝錯誤。 請洽詢 IBM 支援以取得協助。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自訂 Azure-SSIS IR，您必須具備下列條件：

- [Azure 訂用帳戶](https://azure.microsoft.com/)

- [布建您的 Azure SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)。 快速自訂設置不需要。 針對標準的自訂設定，您可以上傳並將自訂安裝腳本及其相關聯的檔案儲存在 blob 容器中。 自訂安裝程序也會將其執行記錄上傳至相同的 Blob 容器。

## <a name="instructions"></a>Instructions

1. 如果您想要使用 PowerShell 布建或重新設定 Azure SSIS IR，請下載並安裝[Azure PowerShell](/powershell/azure/install-az-ps)。 若是快速的自訂設置，請跳到步驟4。

1. 準備您的自訂安裝指令碼和及相關聯的檔案 (例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 檔案)。

   1. 您必須具有名為 `main.cmd` 的指令碼檔案，這是您自訂安裝的進入點。

   1. 您必須確定腳本可以無訊息的方式執行，建議您先在本機電腦上測試腳本。

   1. 如果您想要將其他工具 (例如 `msiexec.exe`) 所產生的其他記錄上傳到您的容器中，請將預先定義的環境變數 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 指定為指令碼中的記錄資料夾 (例如 `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`)。

1. 下載、安裝並啟動 [Azure 儲存體總管](https://storageexplorer.com/)。

   1. 在 [(本機與已連結)] 下方，以滑鼠右鍵選取 [儲存體帳戶]，然後選取 [連線到 Azure 儲存體]。

      ![連線到 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. 選取 [使用儲存體帳戶名稱和金鑰]，然後選取 [下一步]。

      ![使用儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. 輸入您的 Azure 儲存體帳戶名稱和金鑰，選取 [下一步]，然後選取 [連線]。

      ![提供儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. 在已連線的 Azure 儲存體帳戶下，以滑鼠右鍵按一下 [Blob 容器]，選取 [建立 Blob 容器]，然後為新容器命名。

      ![建立 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. 選取新容器，並上傳您的自訂安裝指令碼及其相關聯的檔案。 請務必將 `main.cmd` 上傳至容器的最上層，而非任何資料夾中。 此外，也請確定您的容器只包含必要的自訂安裝檔，如此一來，稍後將它們下載到您的 Azure-SSIS IR 時，才不會花費很長的時間。 自訂安裝程式的最長期間目前設定為45分鐘的時間，這包括從您的容器下載所有檔案，並將它們安裝在 Azure SSIS IR 的時間。 如果需要較長的時間，請提出支援票證。

      ![將檔案上傳至 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. 以滑鼠右鍵按一下容器，然後選取 [取得共用存取簽章]。

      ![取得容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. 為您的容器建立具有夠長的到期時間和讀取 + 寫入 + 列出權限的 SAS URI。 您必須具有 SAS URI，才能在 Azure-SSIS IR 的任何節點重新安裝映像/重新啟動時，下載並執行您的自訂安裝指令碼，及其相關聯的檔案。 您必須具備寫入權限才能上傳安裝執行記錄。

      > [!IMPORTANT]
      > 請確定 SAS URI 不會過期，而且自訂安裝資源一律可在您的 Azure SSIS IR 的整個生命週期中使用，從建立到刪除，特別是如果您在這段期間定期停止並啟動您的 Azure SSIS IR。

      ![產生容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. 複製並儲存容器 SAS URI。

      ![複製並儲存共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 當您使用 Data Factory UI 布建或重新設定 Azure SSIS IR 時，您可以在整合執行時間設定面板的 [**高級設定**] 區段上，選取 [**使用額外的系統組態/元件安裝自訂您的 azure ssis Integration Runtime** ] 核取方塊，以新增/移除自訂設定。 

   如果您想要新增標準的自訂設定，請在 [**自訂安裝容器 SAS uri** ] 欄位中輸入容器的 SAS uri。 
   
   如果您想要新增快速自訂設定，請選取 [**新增**] 以開啟 [新增**快速自訂安裝**面板]，然後選取 [**快速自訂安裝類型**] 下拉式功能表底下的任何類型：

   1. 如果您選取 [**執行 cmdkey] 命令**類型，您可以在 [ **/add**]、[ **/user**] 和 [ **/Pass** ] 欄位中分別輸入目的電腦/功能變數名稱、帳戶名稱/使用者名稱和帳戶金鑰/密碼，以保存您的檔案共用/Azure 檔案儲存體在 Azure SSIS IR 上的存取認證。 這類似于在本機電腦上執行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey)命令。
   
   1. 如果您選取 [**新增環境變數**類型]，則可以分別在 [**變數名稱**] 和 [**變數值**] 欄位中輸入環境變數名稱和值，以新增要在 Azure SSIS IR 上執行的套件中使用的 Windows 環境變數。 這類似于在本機電腦上執行 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1)命令。

   1. 如果您選取 [**安裝授權] 元件**類型，則可以從 [**元件名稱**] 下拉式功能表下的 ISV 合作夥伴選取任何整合元件：

      1. 如果您選取**SentryOne 的工作 factory**元件，您可以從 AZURE SSIS IR 上的 SentryOne 安裝元件的工作[factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components)套件，方法是輸入您在 [**授權金鑰**] 欄位中購買的產品授權金鑰。 目前的整合版本是**2019.4.3**。

      1. 如果您選取 [ **oh22's] HEDDA。IO**元件，您可以安裝[HEDDA。](https://hedda.io/ssis-component/)購買服務之後，AZURE SSIS IR 上 oh22 的 IO 資料品質/清理元件。 目前的整合版本是**1.0.13**。

      1. 如果您選取**oh22's SQLPhonetics.NET**元件，您可以從 AZURE SSIS IR 上的 oh22 安裝[SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/)資料品質/比對元件，方法是輸入您在 [**授權金鑰**] 欄位中購買的產品授權金鑰。 目前的整合版本是**1.0.43**。
   
   您新增的快速自訂設定會出現在 [ **Advanced Settings** ] 區段。 若要移除它們，您可以選取其核取方塊，然後選取 [**刪除**]。

   ![使用自訂設定的 Advanced settings](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. 當您使用 PowerShell 布建或重新設定 Azure SSIS IR 時，您可以執行 `Set-AzDataFactoryV2IntegrationRuntime` Cmdlet 來新增/移除自訂設定，然後再啟動您的 Azure SSIS IR。
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   當您的標準自訂安裝程式完成且您的 Azure SSIS IR 啟動之後，您可以在儲存體容器的 `main.cmd.log` 資料夾中找到 `main.cmd` 和其他執行記錄的標準輸出。

1. 若要查看標準自訂設置的一些範例，請使用 Azure 儲存體總管連接到我們的公用預覽容器。

   1. 在 [(本機與已連結)] 下方，以滑鼠右鍵按一下 [儲存體帳戶]，然後依序選取 [連線到 Azure 儲存體]、[使用連接字串或共用存取簽章 URI] 和 [下一步]。

      ![使用共用存取簽章連線至 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. 選取 [使用 SAS URI]，並為公用預覽容器輸入下列 SAS URI。 選取 **[下一步**]，然後選取 **[連線]** 。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![提供容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. 選取已連線的公用預覽容器，然後按兩下 `CustomSetupScript` 資料夾。 此資料夾中包含下列項目：

      1. 一個 `Sample` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝基本工作。 此工作不會執行任何動作，而會休眠數秒鐘。 此資料夾也包含 `gacutil` 資料夾，其中的整個內容 (`gacutil.exe`、`gacutil.exe.config` 和 `1033\gacutlrc.dll`) 可以原封不動地複製到您的容器。

      1. `UserScenarios` 資料夾，其中包含來自實際使用者案例的數個自訂安裝範例。

      ![公開預覽容器的內容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. 按兩下 [`UserScenarios`] 資料夾以尋找下列專案：

      1. 一個 `.NET FRAMEWORK 3.5` 資料夾，其中包含要安裝舊版 .NET Framework (Azure-SSIS IR 每個節點上的自訂元件可能需要它) 的自訂安裝。

      1. 一個 `BCP` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝 SQL Server 命令列公用程式 (`MsSqlCmdLnUtils.msi`)，包括大量複製程式 (`bcp`)。

      1. `EXCEL` 資料夾，其中包含自訂安裝腳本（`main.cmd`），用以安裝C#您可以在腳本工作中用來在 AZURE SSIS IR 的每個節點上動態讀取/寫入 Excel 檔案的元件/程式庫。 首先，從[這裡](https://www.nuget.org/packages/ExcelDataReader/)下載 `ExcelDataReader.dll` 並從[這裡](https://www.nuget.org/packages/DocumentFormat.OpenXml/)`DocumentFormat.OpenXml.dll`，然後將這些資訊連同 `main.cmd` 一起上傳到您的容器中。 或者，如果您只想要使用標準 Excel 連線管理員/來源/目的地，則您的 Azure SSIS IR 上已預先安裝必要的存取可轉散發套件，因此您不需要任何自訂設定。
      
      1. `MYSQL ODBC` 資料夾，其中包含自訂安裝腳本（`main.cmd`），可在您的 Azure SSIS IR 的每個節點上安裝 MySQL ODBC 驅動程式。 此設定可讓您使用 ODBC 連線管理員/來源/目的地來連接至 MySQL 伺服器。 首先，請下載最新的64位和32位版本的 MySQL ODBC 驅動程式安裝程式-例如，從[mysql](https://dev.mysql.com/downloads/connector/odbc/)`mysql-connector-odbc-8.0.13-winx64.msi` 和 `mysql-connector-odbc-8.0.13-win32.msi`，然後將這些專案連同 `main.cmd` 一起上傳到您的容器中。

      1. `ORACLE ENTERPRISE` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`) 和無訊息安裝組態檔 (`client.rsp`)，用以在 Azure-SSIS IR Enterprise Edition 的每個節點上安裝 Oracle 連接器和 OCI 驅動程式。 此設定可讓您使用 Oracle 連線管理員/來源/目的地來連接到 Oracle 伺服器。 首先，從 [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=55179) 下載 Microsoft Connectors v5.0 for Oracle (`AttunitySSISOraAdaptersSetup.msi` 和 `AttunitySSISOraAdaptersSetup64.msi`)，並從 [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)，下載最新的 Oracle 用戶端 (例如，`winx64_12102_client.zip`)，然後將它們全部一起與 `main.cmd` 和 `client.rsp` 上傳到您的容器。 如果您使用 TNS 連線至 Oracle，則也必須下載 `tnsnames.ora`、加以編輯，然後將其上傳到您的容器中，使其能夠在安裝期間複製到 Oracle 安裝資料夾中。

      1. 一個 `ORACLE STANDARD ADO.NET` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODP.NET 驅動程式。 此設定可讓您使用 ADO.NET 連線管理員/來源/目的地來連接到 Oracle 伺服器。 首先，下載最新的 Oracle ODP.NET 驅動程式（例如，從[oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)`ODP.NET_Managed_ODAC122cR1.zip`），然後將它連同 `main.cmd` 一起上傳到您的容器中。
       
      1. 一個 `ORACLE STANDARD ODBC` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODBC 驅動程式及設定 DSN。 此安裝可讓您使用 ODBC 連線管理員/來源/目的地或 Power Query 連線管理員/來源搭配 ODBC 資料來源種類，來連線到 Oracle 伺服器。 首先，下載最新的 Oracle 立即用戶端（基本封裝或基本 Lite 套件）和 ODBC 封裝，例如來自[此處](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)的64位套件（基本套件： `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`、基本 lite 套件： `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`、ODBC 封裝： `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`）或32位[套件（基本](https://www.oracle.com/technetwork/topics/winsoft-085727.html)套件： `instantclient-basic-nt-18.3.0.0.0dbru.zip`、基本精簡套件： `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`、ODBC 封裝： `instantclient-odbc-nt-18.3.0.0.0dbru.zip`），然後將這些封裝連同 `main.cmd` 一起上傳到您的容器中。

      1. `ORACLE STANDARD OLEDB` 資料夾，其中包含自訂安裝腳本（`main.cmd`），可在您的 Azure SSIS IR 的每個節點上安裝 Oracle OLEDB 驅動程式。 此設定可讓您使用 OLEDB 連線管理員/來源/目的地來連接到 Oracle 伺服器。 首先，下載最新的 Oracle OLEDB 驅動程式-例如，從[oracle](https://www.oracle.com/partners/campaign/index-090165.html)`ODAC122010Xcopy_x64.zip`，然後將它連同 `main.cmd` 一起上傳到您的容器中。

      1. `POSTGRESQL ODBC` 資料夾，其中包含自訂安裝腳本（`main.cmd`），可在您的 Azure SSIS IR 的每個節點上安裝于 postgresql ODBC 驅動程式。 此設定可讓您使用 ODBC 連線管理員/來源/目的地來連接到于 postgresql 伺服器。 首先，下載最新的64位和32位版本的于 postgresql ODBC 驅動程式安裝程式-例如，`psqlodbc_x64.msi` 和 `psqlodbc_x86.msi` 自[于 postgresql](https://www.postgresql.org/ftp/odbc/versions/msi/)，然後將這些專案連同 `main.cmd` 一起上傳到您的容器中。

      1. `SAP BW` 資料夾，其中包含自訂安裝腳本（`main.cmd`），可在您的 Azure SSIS IR 企業版的每個節點上安裝 SAP .NET connector 元件（`librfc32.dll`）。 此設定可讓您使用 SAP BW 連線管理員/來源/目的地來連接到 SAP BW 伺服器。 首先，將64位或32位版本的 `librfc32.dll` 從 SAP 安裝資料夾連同 `main.cmd` 上傳至您的容器。 接著，此指令碼會在安裝期間將 SAP 組件複製到 `%windir%\SysWow64` 或 `%windir%\System32` 資料夾中。

      1. 一個 `STORAGE` 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝 Azure PowerShell。 此安裝可讓您部署和執行 SSIS 套件，以執行 [PowerShell 指令碼來管理您的 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 複製 `main.cmd`、範例 `AzurePowerShell.msi` （或使用最新版本），並 `storage.ps1` 至您的容器。 使用 PowerShell.dtsx 作為您套件的範本。 此套件範本結合了 [Azure Blob 下載工作](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task) (會下載 `storage.ps1` 作為可修改的 PowerShell 指令碼) 和[執行程序工作](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) (會在每個節點上執行指令碼)。

      1. `TERADATA` 資料夾，其中包含自訂安裝指令碼 (`main.cmd`) 及其相關聯的檔案 (`install.cmd`)，和安裝程式套件 (`.msi`)。 這些檔案會在您的 Azure SSIS IR Enterprise Edition 的每個節點上安裝 Teradata 連接器、TPT API 和 ODBC 驅動程式。 此設定可讓您使用 Teradata 連線管理員/來源/目的地來連接到 Teradata 伺服器。 首先，從[Teradata](http://partnerintelligence.teradata.com)下載 Teradata 工具和公用程式 15. x zip 檔案（例如，`TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`），然後將它與上述 `.cmd` 和 `.msi` 檔案一起上傳到您的容器中。

      1. `ZULU OPENJDK` 資料夾，其中包含自訂安裝腳本（`main.cmd`）和 PowerShell 檔案（`install_openjdk.ps1`），以在您的 Azure SSIS IR 的每個節點上安裝祖魯 OpenJDK。 此設定可讓您使用 Azure Data Lake 存放/彈性檔案連接器來處理 ORC/Parquet 檔案，如需詳細資訊，請參閱[這裡](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 首先，下載最新的祖魯[OpenJDK （例如 `zulu8.33.0.1-jdk8.0.192-win_x64.zip`），然後](https://www.azul.com/downloads/zulu/zulu-windows/)將它連同 `main.cmd` 和 `install_openjdk.ps1` 一起上傳到您的容器中。

      ![使用者案例資料夾中的資料夾](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. 若要嘗試這些自訂安裝範例，請將所選資料夾中的內容複寫並貼到您的容器中。
   
      當您使用 Data Factory UI 布建或重新設定 Azure SSIS IR 時，請選取 [ **Advanced Settings** ] （設定）區段中的 [**使用其他系統設定/元件安裝自訂您的 azure ssis Integration Runtime** ] 核取方塊，然後在 [**自訂安裝容器 sas uri** ] 欄位中輸入容器的 SAS URI。
   
      當您使用 PowerShell 布建或重新設定 Azure SSIS IR 時，請使用您容器的 SAS URI 來執行 `Set-AzDataFactoryV2IntegrationRuntime` Cmdlet，做為 `SetupScriptContainerSasUri` 參數的值。

## <a name="next-steps"></a>後續步驟

-   [Azure-SSIS 整合執行階段的 Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [如何開發 Azure-SSIS 整合執行階段的付費或授權自訂元件](how-to-develop-azure-ssis-ir-licensed-components.md)
