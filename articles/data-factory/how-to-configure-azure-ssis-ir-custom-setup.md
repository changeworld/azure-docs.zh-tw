---
title: 自訂 Azure SSIS Integration Runtime 的設定
description: 本文說明如何使用 Azure SSIS Integration Runtime 的自訂安裝介面來安裝其他元件或變更設定
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
ms.openlocfilehash: c4502cc4a808b4a44f70c1f96fe38fd0ddeebdee
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187718"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>自訂 Azure SSIS Integration Runtime 的設定

Azure SQL Server Integration Services Integration Runtime （Azure SSIS IR）的自訂設定提供介面，可在您的 Azure SSIS IR 的安裝或重新設定期間新增您自己的步驟。 

藉由使用自訂安裝，您可以將預設的作業設定或環境修改為，例如，啟動其他 Windows 服務或保存檔案共用的存取認證。 或者，您可以在 Azure SSIS IR 的每個節點上安裝其他元件，例如元件、驅動程式或擴充功能。

您可以透過下列兩種方式之一，在 Azure SSIS IR 上執行自訂的程式： 
* **無腳本的快速自訂安裝**：執行一些常用的系統設定和 Windows 命令，或安裝一些熱門或建議的其他元件，而不使用任何腳本。
* **使用腳本的標準自訂安裝**：準備腳本及其相關聯的檔案，並將它們全部上傳到 Azure 儲存體帳戶中的 blob 容器。 接著，您會在安裝或重新設定 Azure SSIS IR 時，為您的容器提供共用存取簽章（SAS）統一資源識別項（URI）。 然後，Azure SSIS IR 的每個節點都會從您的容器下載腳本及其相關聯的檔案，並以更高的許可權執行您的自訂安裝程式。 當您的自訂設定完成時，每個節點會將執行和其他記錄的標準輸出上傳至您的容器。

您可以使用快速和標準的自訂安裝，同時安裝免費、未授權的元件和付費的授權元件。 如果您是獨立軟體廠商（ISV），請參閱[開發 AZURE SSIS IR 的付費或授權元件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> 因為 Azure SSIS IR 的 v2 系列節點不適用於自訂設定，所以請改用 v3 系列節點。 如果您已經在使用 v2 系列節點，請儘快切換至 v3 系列節點。

## <a name="current-limitations"></a>目前的限制

下列限制僅適用于標準的自訂設置：

- 如果您想要在腳本中使用*gacutil*來安裝全域組件快取（GAC）中的元件，則必須提供*gacutil*做為自訂安裝程式的一部分。 或者，您可以使用我們的*公用預覽*容器中提供的複本，稍後會在「指示」一節中討論。

- 如果您想要參考腳本中的子資料夾， *msiexec*不支援參考根資料夾的 `.\` 標記法。 使用類似 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 的命令，而不是 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`。

- Azure SSIS IR 目前不支援由 Windows 自動建立的系統管理共用或隱藏的網路共用。

- Azure SSIS IR 不支援 IBM iSeries Access ODBC 驅動程式。 您可能會在自訂安裝期間看到安裝錯誤。 如果您這樣做，請聯絡 IBM 支援以尋求協助。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要自訂您的 Azure SSIS IR，您需要下列專案：

- [Azure 訂用帳戶](https://azure.microsoft.com/)

- [佈建您的 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 儲存體帳戶](https://azure.microsoft.com/services/storage/)。 快速自訂設置不需要。 針對標準的自訂設定，您可以上傳並將自訂安裝腳本及其相關聯的檔案儲存在 blob 容器中。 自訂安裝程序也會將其執行記錄上傳至相同的 Blob 容器。

## <a name="instructions"></a>Instructions

1. 如果您想要使用 PowerShell 來設定或重新設定 Azure SSIS IR，請下載並安裝[Azure PowerShell](/powershell/azure/install-az-ps)。 若是快速的自訂設置，請跳到步驟4。

1. 準備您的自訂安裝指令碼和及相關聯的檔案 (例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 檔案)。

   * 您必須擁有名為*main .cmd*的腳本檔案，這是您自訂安裝程式的進入點。  
   * 為確保腳本可以無訊息的方式執行，建議您先在本機電腦上進行測試。  
   * 如果您想要將其他工具（例如*msiexec*）所產生的其他記錄上傳至您的容器，請指定預先定義的環境變數 `CUSTOM_SETUP_SCRIPT_LOG_DIR`，做為腳本中的記錄檔資料夾（例如， *msiexec/i xxx .msi/quiet/lv% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install .log*）。

1. 下載、安裝和開啟[Azure 儲存體總管](https://storageexplorer.com/)。 若要這樣做：

   a. 在 **[（本機和已連接）** ] 底下，以滑鼠右鍵按一下 [**儲存體帳戶**]，然後選取 **[連線到 Azure 儲存體]** 。

      ![連線到 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 選取 [**使用儲存體帳戶名稱和金鑰**]，然後選取 **[下一步]** 。

      ![使用儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. 輸入您的 Azure 儲存體帳戶名稱和金鑰，選取 **[下一步**]，然後選取 **[連線]** 。

      ![提供儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 在您已連線的 Azure 儲存體帳戶底下，以滑鼠右鍵按一下 [ **Blob 容器**]，選取 [**建立 Blob 容器**]，並將新容器命名為。

      ![建立 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 選取新的容器，並上傳您的自訂安裝腳本及其相關聯的檔案。 請確定您在容器的最上層上傳了*cmd.exe* ，而不是在任何資料夾中。 此外，請確定您的容器只包含必要的自訂安裝檔案，如此一來，稍後將其下載到您的 Azure SSIS IR 將不會花很長的時間。 自訂安裝程式的最長持續時間目前設定為45分鐘的時間。這包括從您的容器下載所有檔案，並將它們安裝在 Azure SSIS IR 上的時間。 如果安裝程式需要更多時間，請提出支援票證。

      ![將檔案上傳至 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 以滑鼠右鍵按一下容器，然後選取 [**取得共用存取**簽章]。

      ![取得容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 使用夠長的到期時間和讀取/寫入/列出許可權，為您的容器建立 SAS URI。 每當重新安裝映射或重新開機您的 Azure SSIS IR 的任何節點時，您都需要 SAS URI 來下載並執行您的自訂安裝腳本及其相關聯的檔案。 您必須具備寫入權限才能上傳安裝執行記錄。

      > [!IMPORTANT]
      > 請確定 SAS URI 未過期，而且自訂安裝資源一律可在您的 Azure SSIS IR 的整個生命週期中使用，從建立到刪除，特別是如果您在這段期間定期停止並啟動您的 Azure SSIS IR。

      ![產生容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 複製並儲存容器 SAS URI。

      ![複製並儲存共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 當您使用 data factory UI 來設定或重新設定 Azure SSIS IR 時，可以在 [**整合執行時間設定**] 窗格的 [**高級設定**] 區段中選取 [**使用額外的系統組態/元件安裝自訂您的 azure ssis Integration Runtime** ] 核取方塊，以新增或移除自訂設定。 

   如果您想要新增標準的自訂設定，請在 [**自訂安裝容器 SAS uri** ] 方塊中輸入容器的 SAS uri。 
   
   如果您想要新增快速自訂設定，請選取 [新增] 以開啟 [**新增** **快速自訂安裝**] 窗格，然後在 [**快速自訂安裝類型**] 下拉式清單中選取類型：

   * 如果您選取 [**執行 cmdkey] 命令**類型，您可以在 [ **/add**]、[ **/user**] 和 [ **/Pass** ] 方塊中輸入您的目標電腦名稱稱或功能變數名稱、帳戶名稱或使用者名稱，以及帳戶金鑰或密碼，以保存您的檔案共用或 Azure SSIS IR 上 Azure 檔案儲存體共用的存取認證。 這類似于在本機電腦上執行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey)命令。
   
   * 如果您選取 [**新增環境變數**類型]，您可以在 [**變數名稱**] 和 [**變數值**] 方塊中輸入您的環境變數名稱和值，以新增要在 Azure SSIS IR 上執行之套件中使用的 Windows 環境變數。 這類似于在本機電腦上執行 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1)命令。

   * 如果您選取 [**安裝授權] 元件**類型，則可以在 [**元件名稱**] 下拉式清單中，從我們的 ISV 合作夥伴選取整合元件：

     * 如果您選取**SentryOne 的工作 factory**元件，您可以從 AZURE SSIS IR 上的 SentryOne 安裝元件的工作[factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components)套件，方法是輸入您在 [**授權金鑰**] 方塊中購買的產品授權金鑰。 目前的整合版本是**2019.4.3**。

     * 如果您選取 [ **oh22's] HEDDA。IO**元件，您可以安裝[HEDDA。](https://hedda.io/ssis-component/)購買服務之後，AZURE SSIS IR 上 oh22 的 IO 資料品質/清理元件。 目前的整合版本是**1.0.13**。

      * 如果您選取**oh22's SQLPhonetics.NET**元件，您可以從 AZURE SSIS IR 上的 oh22 安裝[SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/)資料品質/比對元件，方法是輸入您在 [**授權金鑰**] 方塊中購買的產品授權金鑰。 目前的整合版本是**1.0.43**。
   
   您新增的快速自訂設定將會出現在 [ **Advanced Settings** ] 區段中。 若要移除它們，請選取其核取方塊，然後選取 [**刪除**]。

   ![使用自訂設定的 Advanced settings](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. 當您使用 PowerShell 來設定或重新設定 Azure SSIS IR 時，您可以在啟動 Azure SSIS IR 之前，先執行 `Set-AzDataFactoryV2IntegrationRuntime` Cmdlet 來新增或移除自訂設定。
   
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
   
   當您的標準自訂安裝程式完成且您的 Azure SSIS IR 啟動之後，您可以在儲存體容器的*主要 .cmd*資料夾中，找到*主要 .cmd*和其他執行記錄的標準輸出。

1. 若要查看標準自訂設置的一些範例，請使用 Azure 儲存體總管連接到我們的公用預覽容器。

   a. 在 [(本機與已連結)] 下方，以滑鼠右鍵按一下 [儲存體帳戶]，然後依序選取 [連線到 Azure 儲存體]、[使用連接字串或共用存取簽章 URI] 和 [下一步]。

      ![使用共用存取簽章連線至 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. 選取 [**使用 SAS uri** ]，然後在 [ **URI** ] 方塊中輸入下列 SAS uri：

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![提供容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 選取 **[下一步**]，然後選取 **[連線]** 。

   d. 在左窗格中選取 [已連線的**publicpreview** ] 容器，然後按兩下 [ *CustomSetupScript* ] 資料夾。 此資料夾中包含下列項目：

      * *範例*資料夾，其中包含自訂安裝程式，可在您的 AZURE SSIS IR 的每個節點上安裝基本工作。 此工作不會執行任何動作，而會休眠數秒鐘。 資料夾也包含*gacutil*資料夾，其完整內容（*gacutil .exe*、 *gacutil*和*1033 \ gacutlrc .dll*）可以依原樣複製到您的容器。

      * *UserScenarios*資料夾，其中包含來自實際使用者案例的數個自訂安裝程式範例。

        ![公開預覽容器的內容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. 按兩下 [ *UserScenarios* ] 資料夾以尋找下列專案：

      * *.NET FRAMEWORK 3.5*資料夾，其中包含自訂安裝程式，可安裝舊版的 .NET Framework，在您的 AZURE SSIS IR 的每個節點上可能需要此版本的自訂群組件。

      * *BCP*資料夾，其中包含自訂安裝程式，可在您的 AZURE SSIS IR 的每個節點上安裝 SQL Server 命令列公用程式（*MsSqlCmdLnUtils*），包括大量複製程式（*BCP*）。

      * *EXCEL*資料夾，其中包含自訂安裝腳本（*main .cmd*），用以安裝C#您可以在腳本工作中使用的元件和程式庫，以便在您的 Azure SSIS IR 的每個節點上動態讀取及寫入 EXCEL 檔案。 
      
        首先，下載[*ExcelDataReader*](https://www.nuget.org/packages/ExcelDataReader/)和[*DocumentFormat*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)，然後將它們全部一起上*傳至您的容器*。 或者，如果您只想要使用標準 Excel 連線管理員、Excel 來源和 Excel 目的地，您的 Azure SSIS IR 上已預先安裝必要的存取可轉散發套件，因此您不需要任何自訂設定。
      
      * *MYSQL odbc*資料夾，其中包含自訂安裝腳本（*main .cmd*），可在您的 Azure SSIS IR 的每個節點上安裝 MYSQL odbc 驅動程式。 此設定可讓您使用 ODBC 連線管理員、來源和目的地來連接至 MySQL 伺服器。 
     
        首先，[下載最新的64位和32位版本的 MYSQL ODBC 驅動程式安裝](https://dev.mysql.com/downloads/connector/odbc/)程式（例如， *mysql-connector-odbc-8.0.13-winx64 .msi*和*mysql-connector-odbc-8.0.13-win32 .msi*），然後將其連同*main .cmd*一起上傳至您的容器。

      * *ORACLE ENTERPRISE*資料夾，其中包含自訂安裝腳本（*主要 .cmd*）和無訊息安裝設定檔（*用戶端 .rsp*），以在您的 Azure SSIS IR 企業版的每個節點上安裝 ORACLE 連接器和 OCI 驅動程式。 此設定可讓您使用 Oracle 連線管理員、來源和目的地來連接到 Oracle 伺服器。 
      
        首先，從[Microsoft 下載中心](https://www.microsoft.com/en-us/download/details.aspx?id=55179)下載 microsoft connector 5.0 for Oracle （*AttunitySSISOraAdaptersSetup .msi*和*AttunitySSISOraAdaptersSetup64*），並從[oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)下載最新的 oracle 用戶端（例如， *winx64_12102_client .zip*），*然後將它們*全部一起上傳至*您的容器*。 如果您使用 TNS 連接到 Oracle，您也需要下載*tnsnames.ora. tnsnames.ora*、編輯它，然後將它上傳至您的容器，以便在安裝期間將它複製到 Oracle 安裝資料夾。

      * *ORACLE STANDARD ADO.NET*資料夾，其中包含自訂安裝腳本（*main .cmd*），可在您的 Azure SSIS IR 的每個節點上安裝 ORACLE ODP.NET 驅動程式。 此設定可讓您使用 ADO.NET 連線管理員、來源和目的地來連接到 Oracle 伺服器。 
      
        首先，[下載最新的 Oracle ODP.NET 驅動程式](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)（例如， *ODP. NET_Managed_ODAC122cR1 .zip*），然後將它連同*main .cmd*一起上傳至您的容器。
       
      * *ORACLE STANDARD ODBC*資料夾，其中包含自訂安裝腳本（*main .cmd*），用以安裝 ORACLE ODBC 驅動程式，並在您的 Azure SSIS IR 的每個節點上設定資料來源名稱（DSN）。 此設定可讓您使用 ODBC 連線管理員、來源和目的地，或 Power Query 連線管理員和來源與 ODBC 資料來源類型連接到 Oracle 伺服器。 
      
        首先，下載最新的 Oracle 立即用戶端（基本套件或基本 Lite 套件）和 ODBC 封裝，然後將它們全部一起上*傳到您的容器*：
        * [下載64位套件](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)（基本套件： *instantclient-basic-windows. x64-18.3.0.0.0 dbru .zip*;基本 Lite 套件： *instantclient-basiclite-windows. x64-18.3.0.0.0 dbru .zip*;ODBC 封裝： *instantclient-odbc-windows. x64-18.3.0.0.0 dbru .zip*） 
        * [下載32位套件](https://www.oracle.com/technetwork/topics/winsoft-085727.html)（基本套件： *instantclient-basic-nt-18.3.0.0.0 dbru .zip*;基本 Lite 套件： *instantclient-basiclite-nt-18.3.0.0.0 dbru .zip*;ODBC 封裝： *instantclient-odbc-nt-18.3.0.0.0 dbru .zip*）

      * *ORACLE STANDARD OLEDB*資料夾，其中包含自訂安裝腳本（*main .cmd*），可在您的 Azure SSIS IR 的每個節點上安裝 ORACLE OLEDB 驅動程式。 此設定可讓您使用 OLEDB 連線管理員、來源和目的地來連接到 Oracle 伺服器。 
     
        首先，[下載最新的 ORACLE OLEDB 驅動程式](https://www.oracle.com/partners/campaign/index-090165.html)（例如， *ODAC122010Xcopy_x64 .zip*），然後將它連同*main .cmd*一起上傳至您的容器。

      * *于 POSTGRESQL ODBC*資料夾，其中包含自訂安裝腳本（*main .cmd*），可在您的 Azure SSIS IR 的每個節點上安裝于 postgresql ODBC 驅動程式。 此設定可讓您使用 ODBC 連線管理員、來源和目的地來連接到于 postgresql 伺服器。 
     
        首先，[下載最新的64位和32位版本的于 POSTGRESQL ODBC 驅動程式安裝程式](https://www.postgresql.org/ftp/odbc/versions/msi/)（例如， *psqlodbc_x64 .msi*和*psqlodbc_x86 .msi*），然後將它們全部一起上*傳至您的容器*。

      * 包含自訂安裝*腳本（librfc32.dll）的* *SAP BW*資料夾，用來在您的 Azure SSIS IR 企業版的每個節點上安裝 SAP .net connector 元件（ *.dll*）。 此設定可讓您使用 SAP Business 倉儲（BW）連線管理員、來源和目的地來連接到 SAP BW 伺服器。 
      
        首先，將64位或32位版本的*librfc32.dll*從 SAP 安裝資料夾連同*main. cmd*上傳至您的容器。 接著，腳本會在安裝期間將 SAP 元件複製到 *%windir%\SysWow64*或 *%windir%\System32*資料夾。

      * *儲存體*資料夾，其中包含自訂安裝程式，可在您的 AZURE SSIS IR 的每個節點上安裝 Azure PowerShell。 此設定可讓您部署及執行執行 PowerShell 腳本的 SSIS 套件，[以操作您的 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 
      
        將*main .cmd*、範例*azurepowershell 來*（或使用最新版本）和*存放區*複製到您的容器中。 使用 *.dtsx*作為封裝的範本。 套件範本結合了[Azure Blob 下載](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)工作，此工作會下載*儲存體. ps1*做為可修改的 PowerShell 腳本，以及[執行處理](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)工作，這會在每個節點上執行腳本。

      * *TERADATA*資料夾，其中包含自訂安裝腳本（*主要 .cmd*）、其相關聯的檔案（*install .cmd*）和安裝程式套件（ *.msi*）。 這些檔案會在您的 Azure SSIS IR Enterprise Edition 的每個節點上安裝 Teradata 連接器、Teradata Parallel Transporter （TPT） API 和 ODBC 驅動程式。 此設定可讓您使用 Teradata 連線管理員、來源和目的地來連接到 Teradata 伺服器。 
      
        首先，[下載 Teradata 工具和公用程式 15. x zip](http://partnerintelligence.teradata.com)檔案（例如， *TeradataToolsAndUtilitiesBase__windows_indep. 15.10.22.00*），然後將它與先前提及的 *.cmd*和 *.msi*檔案一起上傳至您的容器。

      * 包含自訂安裝腳本（*主要 .cmd*）和 PowerShell 檔案（*install_openjdk Ps1*）的*祖魯 OPENJDK*資料夾，可在您的 Azure SSIS IR 的每個節點上安裝祖魯 OPENJDK。 此設定可讓您使用 Azure Data Lake 存放區和彈性的檔案連接器來處理 ORC 和 Parquet 檔案。 如需詳細資訊，請參閱[適用于 Integration Services 的 Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 
      
        首先，[下載最新的祖魯 OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) （例如，*祖魯 8.33.0.1-jdk 8.0.192-win_x64 .zip*），然後將它連同*主要 .cmd*和*install_openjdk*上傳至您的容器。

        ![使用者案例資料夾中的資料夾](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 若要嘗試這些自訂安裝範例，請將所選資料夾中的內容複寫到您的容器。
   
      當您使用 Data Factory UI 來設定或重新設定 Azure SSIS IR 時，請選取 [ **Advanced Settings** ] （新增設定）區段上的 [**使用其他系統組態/元件安裝自訂您的 azure ssis Integration Runtime** ] 核取方塊，然後在 [**自訂安裝容器 sas uri** ] 方塊中輸入容器的 SAS URI。
   
      當您使用 PowerShell 來設定或重新設定 Azure SSIS IR 時，請使用您容器的 SAS URI 來執行 `Set-AzDataFactoryV2IntegrationRuntime` Cmdlet，做為 `SetupScriptContainerSasUri` 參數的值。

## <a name="next-steps"></a>後續步驟

- [設定 Azure SSIS Integration Runtime 的 Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [開發適用于 Azure SSIS Integration Runtime 的付費或授權自訂群組件](how-to-develop-azure-ssis-ir-licensed-components.md)
