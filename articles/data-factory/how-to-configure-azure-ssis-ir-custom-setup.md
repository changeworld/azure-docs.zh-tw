---
title: 自訂 Azure-SSIS Integration Runtime 的安裝
description: 此文章描述如何使用 Azure-SSIS Integration Runtime 的自訂安裝介面來安裝其他元件或變更設定
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
ms.date: 07/09/2020
ms.openlocfilehash: 8b29708dbcc6bbb0d127b4277fd4b2d962da6e6a
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986384"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>自訂 Azure-SSIS Integration Runtime 的安裝

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 () ADF Azure Data Factory 中，Azure SQL Server Integration Services (SSIS) Integration Runtime (IR) 的自訂設定會提供介面，以在布建或重新設定 Azure SSIS IR 期間新增您自己的步驟。 

您可以使用自訂安裝，變更預設作業組態或環境 (例如，用以啟動其他 Windows 服務)、保存檔案共用的存取認證，或使用強式密碼編譯/更安全的網路通訊協定 (TLS 1.2)。 或者，您可以在 Azure SSIS IR 的每個節點上安裝其他自訂/協力廠商元件，例如元件、驅動程式或擴充功能。 如需內建/預先安裝元件的詳細資訊，請參閱 [Azure-SSIS IR 上的內建/預先安裝元件](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime) \(英文\)。

您可以透過下列兩種方式之一，在 Azure-SSIS IR 上執行自訂安裝程式： 
* **使用指令碼的標準自訂安裝**：準備指令碼及其相關聯的檔案，並將其一起上傳至您 Azure 儲存體帳戶中的 Blob 容器。 接著，當您安裝或重新設定 Azure-SSIS IR 時，您可以提供容器的共用存取簽章 (SAS) 統一資源識別項 (URI)。 之後，Azure-SSIS IR 的每個節點都會從您的容器下載指令碼及其相關聯的檔案，並使用提高的權限執行您的自訂安裝。 自訂安裝完成後，每個節點都會將執行的標準輸出和其他記錄上傳到您的容器中。
* **不使用指令碼的快速自訂安裝**：執行一些常見的系統組態和 Windows 命令，或安裝一些熱門或建議的其他元件，而不使用任何指令碼。

您可以使用標準和快速的自訂安裝，同時安裝免費、未授權的元件和付費的授權元件。 如果您是 (ISV) 的獨立軟體廠商，請參閱[開發 AZURE SSIS IR 的付費或授權元件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> 若要從之後的增強功能中獲益，建議您使用適用於 Azure-SSIS IR 的 v3 或更新的節點系列搭配自訂安裝。

## <a name="current-limitations"></a>目前的限制

下列限制僅適用於標準自訂安裝：

- 如果您想要使用指令碼中的 *gacutil.exe*，在全域組件快取 (GAC) 中安裝組件，您必須在自訂安裝的過程中提供 *gacutil.exe*。 或者，您可以使用我們*公開預覽*容器中提供的複本，稍後這會在「指示」一節中討論。

- 如果您想要參考指令碼中的子資料夾，*msiexec.exe* 不支援以 `.\` 標記法參考根資料夾。 請使用諸如 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 而非 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` 的命令。

- Azure-SSIS IR 目前不支援由 Windows 自動建立的系統管理共用或隱藏的網路共用。

- Azure-SSIS IR 不支援 IBM iSeries Access ODBC 驅動程式。 您可能會在自訂安裝期間看到安裝錯誤。 此時，請連絡 IBM 支援以尋求協助。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自訂 Azure-SSIS IR，您必須具備下列項目：

- [Azure 訂用帳戶](https://azure.microsoft.com/)

- [佈建您的 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)。 快速自訂安裝不需要。 針對標準自訂安裝，您可以上傳自訂安裝指令碼及其相關聯的檔案，並將其儲存在 Blob 容器中。 自訂安裝程序也會將其執行記錄上傳至相同的 Blob 容器。

## <a name="instructions"></a>Instructions

您可以使用 ADF UI 上的自訂設定來布建或重新設定 Azure SSIS IR。 如果您想要使用 PowerShell 執行相同的動作，請下載並安裝[Azure PowerShell](/powershell/azure/install-az-ps)。

### <a name="standard-custom-setup"></a>標準的自訂設定

若要使用標準的自訂設定來布建或重新設定您的 Azure SSIS IR，請完成下列步驟。

1. 準備您的自訂安裝指令碼和及相關聯的檔案 (例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 檔案)。

   * 您必須擁有名為 *main.cmd* 的指令碼檔案，這是您自訂安裝的進入點。  
   * 為確保指令碼可以無訊息的方式執行，建議您先在本機電腦上進行測試。  
   * 如果您想要將其他工具 (例如 *msiexec.exe*) 所產生的其他記錄上傳到您的容器中，請將預先定義的環境變數 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 指定為指令碼中的記錄資料夾 (例如 *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*)。

1. 下載、安裝並開啟 [Azure 儲存體總管](https://storageexplorer.com/)。

   a. 在 [(本機與已連結)] 下方，以滑鼠右鍵按一下 [儲存體帳戶]，然後選取 [連線到 Azure 儲存體]。

      ![連線到 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 選取 [使用儲存體帳戶名稱和金鑰]，然後選取 [下一步]。

      ![使用儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. 輸入您的 Azure 儲存體帳戶名稱和金鑰，選取 [下一步]，然後選取 [連線]。

      ![提供儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 在已連線的 Azure 儲存體帳戶下，以滑鼠右鍵按一下 [Blob 容器]，選取 [建立 Blob 容器]，然後為新容器命名。

      ![建立 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 選取新容器，並上傳您的自訂安裝指令碼及其相關聯的檔案。 請務必將 *main.cmd* 上傳至容器的最上層，而非任何資料夾中。 此外，也請確認您的容器只包含必要的自訂安裝檔，如此一來，稍後將其下載到您的 Azure-SSIS IR 時，才不會花費很長的時間。 自訂安裝的最長持續時間目前設定為 45 分鐘後逾時。這包括從您的容器下載所有檔案，並將其安裝在 Azure-SSIS IR 上的時間。 如果安裝需要更多時間，請提出支援票證。

      ![將檔案上傳至 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 以滑鼠右鍵按一下容器，然後選取 [取得共用存取簽章]。

      ![取得容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 為您的容器建立到期時間夠長且具備讀取/寫入/列出權限的 SAS URI。 您需要有 SAS URI，才能在 Azure-SSIS IR 的任何節點重新製作映像或重新啟動時，下載並執行您的自訂安裝指令碼及其相關聯的檔案。 您必須具備寫入權限才能上傳安裝執行記錄。

      > [!IMPORTANT]
      > 如果您會在這段時間內定期停止和啟動 Azure-SSIS IR，請確定在 Azure-SSIS IR 的整個生命週期內 (從建立到刪除)，SAS URI 不會到期，且自訂安裝資源永遠可供使用。

      ![產生容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 複製並儲存容器 SAS URI。

      ![複製並儲存共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 當您在 ADF UI 上布建或重新設定 Azure SSIS IR 時，請選取 [ **Integration Runtime 設定**] 窗格的 [**設定**] 頁面上的 [**自訂您的 azure ssis Integration Runtime** ]，然後在 [**自訂安裝程式容器 sas URI** ] 方塊中輸入容器的 SAS uri。

   ![自訂安裝的進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

### <a name="express-custom-setup"></a>快速自訂安裝

若要使用快速自訂設定來布建或重新設定 Azure SSIS IR，請完成下列步驟。

1. 當您在 ADF UI 上布建或重新設定 Azure SSIS IR 時，請在 [ **Integration Runtime 設定**] 窗格的 [**設定**] 頁面上，選取 [**使用額外的系統組態/元件安裝自訂您的 azure ssis Integration Runtime** ] 核取方塊。 

1. 選取 [**新增**] 以開啟 [新增**快速自訂安裝**] 窗格，然後在 [**快速自訂安裝類型**] 下拉式清單中選取類型：

   * 如果您選取 [執行 cmdkey 命令] 類型，可以在 [/新增]、[/使用者] 和 [/通過] 方塊中，輸入您的目標電腦名稱或網域名稱、帳戶名稱或使用者名稱，以及帳戶金鑰或密碼，以便在 Azure-SSIS IR 上保存檔案共用或 Azure 檔案共用的存取認證。 這類似於在本機電腦上執行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) 命令。
   
   * 如果您選取 [新增環境變數] 類型，可以在 [變數名稱] 與 [變數值] 方塊中，輸入您的環境變數名稱與值，以新增要在 Azure-SSIS IR 上執行之套件中使用的 Windows 環境變數。 這類似於在本機電腦上執行 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) 命令。

   * 如果您選取 [安裝授權元件] 類型，則可以在 [元件名稱] 下拉式清單中，從我們的 ISV 合作夥伴選取整合式元件：

     * 如果您選取 [SentryOne 的工作 Factory] 元件，可以在 [授權金鑰] 方塊中輸入您向 SentryOne 購買的產品授權金鑰，以便在 Azure-SSIS IR 上安裝 SentryOne 元件的[工作 Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 套件。 目前的整合版本是**2020.1.3**。

     * 如果您選取 **oh22's HEDDA.IO** 元件，可以在購買其服務之後，在 Azure-SSIS IR 上安裝 oh22 的 [HEDDA.IO](https://hedda.io/ssis-component/) 資料品質/清理元件。 目前的整合版本是**1.0.14**。

     * 如果您選取 [oh22 的 SQLPhonetics.NET] 元件，可以在 [授權金鑰] 方塊中輸入您向 oh22 購買的產品授權金鑰，以便在 Azure-SSIS IR 上安裝 oh22 的 [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) 資料品質/比對元件。 目前的整合版本是**1.0.45**。

     * 如果您選取 [KingswaySoft 的 SSIS 整合工具組] 元件，可以在 [授權金鑰] 方塊中輸入您向 KingswaySoft 購買的產品授權金鑰，以便在 Azure-SSIS IR 上，為 KingswaySoft 的 CRM/ERP/行銷/共同作業應用程式 (例如 Microsoft Dynamics/SharePoint/Project Server、Oracle/Salesforce Marketing Cloud 等)，安裝 [SSIS 整合工具組](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365)連接器套件。 目前的整合版本是 **2019.2**。

     * 如果您選取 [KingswaySoft 的 SSIS Productivity Pack] 元件，可以在 [授權金鑰] 方塊中輸入您向 KingswaySoft 購買的產品授權金鑰，以便在 Azure-SSIS IR 上安裝 KingswaySoft 元件的 [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) 套件。 目前的整合版本是 **10.0**。

     * 如果您選取 [Theobald Software 的 Xtract IS] 元件，可以將您向 Theobald Software 購買的產品授權檔案拖放/上傳到 [授權檔案] 方塊中，以便從 Azure-SSIS IR 上的 Theobald Software 安裝 SAP 系統 (ERP、S/4HANA、BW) 連接器的 [Xtract IS](https://theobald-software.com/en/xtract-is/) 套件。 目前的整合版本是 **6.1.1.3**。

您新增的快速自訂設定將會出現在 [ **Advanced settings** ] 頁面上。 若要將其移除，請選取其核取方塊，然後選取 [刪除]。

### <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 來布建或重新設定具有自訂設定的 Azure SSIS IR，請完成下列步驟。

1. 如果您的 Azure SSIS IR 已啟動/執行，請先將它停止。

1. 接著，您可以執行 Cmdlet 來新增或移除自訂的執行，然後 `Set-AzDataFactoryV2IntegrationRuntime` 再啟動您的 AZURE SSIS IR。

   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

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
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
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

1. 當您的標準自訂安裝程式完成且您的 Azure SSIS IR 啟動之後，您可以在容器的*主要 .log*資料夾中，找到*主要 .cmd*和其他執行記錄的標準輸出。

### <a name="standard-custom-setup-samples"></a>標準自訂安裝範例

若要查看並重複使用標準自訂設置的一些範例，請完成下列步驟。

1. 使用 Azure 儲存體總管連接到我們的公用預覽容器。

   a. 在 [(本機與已連結)] 下方，以滑鼠右鍵按一下 [儲存體帳戶]，然後依序選取 [連線到 Azure 儲存體]、[使用連接字串或共用存取簽章 URI] 和 [下一步]。

      ![使用共用存取簽章連線至 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. 選取 [使用 SAS URI]，然後在 [URI] 方塊中，輸入下列 SAS URI：

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![提供容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 選取 [下一步]，然後選取 [連線]。

   d. 在左窗格中，選取已連線的 **publicpreview** 容器，然後按兩下 [CustomSetupScript] 資料夾。 此資料夾中包含下列項目：

      * [Sample] 資料夾，其中包含自訂安裝程式，用以在 Azure-SSIS IR 的每個節點上安裝基本工作。 此工作不會執行任何動作，而會休眠數秒鐘。 此資料夾也包含 [gacutil] 資料夾，其完整內容 (*gacutil .exe*、*gacutil .exe .config* 和 *1033\gacutlrc.dll*) 可以依原樣複製至您的容器。

      * [UserScenarios] 資料夾，其中包含實際使用者案例的數個自訂安裝範例。

        ![公開預覽容器的內容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. 按兩下 [UserScenarios] 資料夾以尋找下列項目：

      * [.NET FRAMEWORK 3.5] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以安裝 Azure-SSIS IR 每個節點上的自訂元件可能需要的舊版 .NET Framework。

      * [BCP] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR 的每個節點上安裝 SQL Server 命令列公用程式 (*MsSqlCmdLnUtils.msi*)，包括大量複製程式 (*bcp*)。

      * [EXCEL] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以安裝您可以在指令碼工作中使用的 C# 組件和程式庫，以便在 Azure-SSIS IR 的每個節點上動態讀取及寫入 EXCEL 檔案。 
      
        首先，下載 [*ExcelDataReader*](https://www.nuget.org/packages/ExcelDataReader/) 與 [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)，然後將其與 *main.cmd* 全部一起上傳至您的容器。 或者，如果您只想要使用標準 Excel 連線管理員、Excel 來源和 Excel 目的地，則您的 Azure-SSIS IR 上已經預先安裝所需的 Access 可轉散發套件，因此您不需要任何自訂安裝。
      
      * [MYSQL ODBC] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODBC 驅動程式。 此安裝可讓您使用 ODBC 連線管理員、來源和目的地，連線到 MySQL 伺服器。 
     
        首先，[下載最新的 64 位元和 32 位元版本的 MySQL ODBC 驅動程式安裝程式](https://dev.mysql.com/downloads/connector/odbc/) (例如 *mysql-connector-odbc-8.0.13-winx64.msi* 和 *mysql-connector-odbc-8.0.13-win32.msi*)，然後將其與 *main.cmd* 全部一起上傳至您的容器。

      * [ORACLE ENTERPRISE] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*) 和無訊息安裝組態檔 (*client.rsp*)，用以在 Azure-SSIS IR Enterprise Edition 的每個節點上安裝 Oracle 連接器和 OCI 驅動程式。 此安裝可讓您使用 Oracle 連線管理員、來源和目的地，連線到 Oracle 伺服器。 
      
        首先，從 [Microsoft 下載中心](https://www.microsoft.com/en-us/download/details.aspx?id=55179)下載適用於 Oracle 的 Microsoft Connectors v5.0 (*AttunitySSISOraAdaptersSetup.msi* 和 *AttunitySSISOraAdaptersSetup64.msi*)，並從 [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 下載最新的 Oracle 用戶端 (例如 *winx64_12102_client.zip*)，然後將其與 *main.cmd* 和 *client.rsp* 全部一起上傳至您的容器中。 如果您使用 TNS 連線至 Oracle，則也必須下載 *tnsnames.ora*、加以編輯，然後將其上傳到您的容器中，使其能夠在安裝期間複製到 Oracle 安裝資料夾中。

      * [ORACLE STANDARD ADO.NET] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODP.NET 驅動程式。 此安裝可讓您使用 ADO.NET 連線管理員、來源和目的地，連線到 Oracle 伺服器。 
      
        首先，[下載最新的 Oracle ODP.NET 驅動程式](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (例如 *ODP.NET_Managed_ODAC122cR1.zip*)，然後將其與 *main.cmd* 一起上傳至您的容器。
       
      * [ORACLE STANDARD ODBC] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle ODBC 驅動程式及設定資料來源名稱 (DSN)。 此安裝可讓您使用 ODBC 連線管理員、來源和目的地或 Power Query 連線管理員和來源，搭配 ODBC 資料來源類型，連線到 Oracle 伺服器。 
      
        首先，下載最新的 Oracle Instant Client (基本套件或基本精簡套件) 和 ODBC 套件，然後將其們與 *main.cmd* 全部一起上傳至您的容器：
        * [下載 64 位元套件](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (基本套件：*instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*；基本精簡套件：*instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*；ODBC 套件：*instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [下載 32 位元套件](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (基本套件：*instantclient-basic-nt-18.3.0.0.0dbru.zip*；基本精簡套件：*instantclient-basiclite-nt-18.3.0.0.0dbru.zip*；ODBC 套件：*instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * [ORACLE STANDARD OLEDB] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR 的每個節點上安裝 Oracle OLEDB 驅動程式。 此安裝可讓您使用 OLEDB 連線管理員、來源和目的地，連線到 Oracle 伺服器。 
     
        首先，[下載最新的 Oracle OLEDB 驅動程式](https://www.oracle.com/partners/campaign/index-090165.html) (例如 *ODAC122010Xcopy_x64.zip*)，然後將其與 *main.cmd* 一起上傳至您的容器。

      * [POSTGRESQL ODBC] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR 的每個節點上安裝 PostgreSQL ODBC 驅動程式。 此安裝可讓您使用 ODBC 連線管理員、來源和目的地，連線到 PostgreSQL 伺服器。 
     
        首先，[下載最新的 64 位元和 32 位元版本的 PostgreSQL ODBC 驅動程式安裝程式](https://www.postgresql.org/ftp/odbc/versions/msi/) (例如 *psqlodbc_x64.msi* 和 *psqlodbc_x86.msi*)，然後將其與 *main.cmd* 全部一起上傳至您的容器。

      * [SAP BW] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR Enterprise Edition 的每個節點上安裝 SAP .NET 連接器組件 (*librfc32.dll*)。 此安裝可讓您使用 SAP Business Warehouse (BW) 連線管理員、來源和目的地，連線到 SAP BW 伺服器。 
      
        首先，從 SAP 安裝資料夾將 64 位元或 32 位元版本的 *librfc32.dll* 與 *main.cmd* 一起上傳至您的容器中。 接著，指令碼會在安裝期間，將 SAP 組件複製到 *%windir%\SysWow64* 或 *%windir%\System32* 資料夾。

      * [STORAGE] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以在 Azure-SSIS IR 的每個節點上安裝 Azure PowerShell。 此安裝可讓您部署和執行 SSIS 套件，以執行 [PowerShell 指令碼來操作您的 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 
      
        將 *main.cmd*、範例 *AzurePowerShell.msi* (或使用最新版本) 和 *storage.ps1* 複製至您的容器。 使用 *PowerShell.dtsx* 作為您套件的範本。 此套件範本結合了 [Azure Blob 下載工作](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task) (可下載 *storage.ps1* 作為可修改的 PowerShell 指令碼) 與[執行處理工作](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/) (可在每個節點上執行指令碼)。

      * [TERADATA] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)、其相關聯的檔案 (*install.cmd*)，以安裝程式套件 ( *.msi*)。 這些檔案會在 Azure-SSIS IR Enterprise Edition 的每個節點上安裝 Teradata 連接器、Teradata Parallel Transporter (TPT) API 和 ODBC 驅動程式。 此安裝可讓您使用 Teradata 連線管理員、來源和目的地，連線到 Teradata 伺服器。 
      
        首先，[下載 Teradata Tools and Utilities 15.x zip 檔案](http://partnerintelligence.teradata.com) (例如，*TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*)，然後將其與先前提到的 *.cmd* 和 *.msi* 檔案一起上傳至您的容器。

      * [TLS 1.2] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*)，用以使用強式密碼編譯/更安全的網路通訊協定 (TLS 1.2)，並在 Azure-SSIS IR 的每個節點上停用較舊的 SSL/TLS 版本。

      * [ZULU OPENJDK] 資料夾，其中包含自訂安裝指令碼 (*main.cmd*) 和 PowerShell 檔案 (*install_openjdk.ps1*)，用以在 Azure-SSIS IR 的每個節點上安裝 Zulu OpenJDK。 此安裝可讓您使用 Azure Data Lake Store 和彈性檔案連接器處理 ORC 和 Parquet 檔案。 如需詳細資訊，請參閱 [Azure Feature Pack for Integration Services](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 
      
        首先，[下載最新的 Zulu OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (例如，*zulu8.33.0.1-jdk8.0.192-win_x64.zip*)，然後將其與 *main.cmd* 和 *install_openjdk.ps1* 一起上傳至您的容器。

        ![使用者案例資料夾中的資料夾](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 若要重複使用這些標準自訂安裝範例，請將所選資料夾的內容複寫到您的容器。

1. 當您在 ADF UI 上布建或重新設定 Azure SSIS IR 時，請選取 [ **Integration Runtime 設定**] 窗格的 [**設定**] 頁面上的 [**自訂您的 azure ssis Integration Runtime** ]，然後在 [**自訂安裝程式容器 sas URI** ] 方塊中輸入容器的 SAS uri。
   
1. 當您使用 Azure PowerShell 布建或重新設定 Azure SSIS IR 時，如果它已啟動/執行，請將它停止， `Set-AzDataFactoryV2IntegrationRuntime` 並以容器的 SAS URI 作為參數的值 `SetupScriptContainerSasUri` ，然後啟動您的 AZURE ssis ir。

1. 當您的標準自訂安裝程式完成且您的 Azure SSIS IR 啟動之後，您可以在容器的*主要 .log*資料夾中，找到*主要 .cmd*和其他執行記錄的標準輸出。

## <a name="next-steps"></a>後續步驟

- [設定 Azure SSIS IR 的企業版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [開發 Azure SSIS IR 的付費或授權元件](how-to-develop-azure-ssis-ir-licensed-components.md)
