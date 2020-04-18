---
title: 自訂 Azure-SSIS 整合時的設定
description: 本文介紹如何使用 Azure-SSIS 整合執行時的自訂設定介面來安裝其他元件或更改設定
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
ms.date: 04/15/2020
ms.openlocfilehash: ab2ba31d6b712bd3399bc8bf5b491337d462dac9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606209"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>自訂 Azure-SSIS 整合時的設定

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure-SQL 伺服器整合服務整合式執行時 (Azure-SSIS IR) 的自訂設定提供了一個介面,用於在 Azure-SSIS IR 的設定或重新設定期間添加您自己的步驟。 

通過使用自定義設置,可以更改預設的操作配置或環境,例如,啟動其他 Windows 服務、保留檔共享的存取認證或使用強加密/更安全的網路協定 (TLS 1.2)。 或者,您可以在 Azure-SSIS IR 的每個節點上安裝其他元件,如程式集、驅動程式或擴展。

您可以透過兩種方式在 Azure-SSIS IR 上執行自訂設定: 
* **無需文稿即可快速設置**:運行一些常見的系統配置和 Windows 命令,或者安裝一些常用或建議的其他元件,而無需使用任何腳本。
* **使用文稿的標準自定義設置**:準備腳本及其關聯檔,並將它們一起上載到 Azure 儲存帳戶中的 Blob 容器。 然後,在設置或重新配置 Azure-SSIS IR 時,為容器提供共用存取簽名 (SAS) 統一資源標識符 (URI)。 然後,Azure-SSIS IR 的每個節點都會從容器下載腳本及其關聯檔,並運行具有提升許可權的自定義設置。 自定義設置完成後,每個節點都會將執行和其他日誌的標準輸出上載到容器。

您可以安裝免費、未經許可的元件和付費許可元件,並安裝快速和標準的自定義設置。 如果您是獨立的軟體供應商 (ISV),請參閱[為 Azure-SSIS IR 開發付費或許可元件](how-to-develop-azure-ssis-ir-licensed-components.md)。

> [!IMPORTANT]
> 為了從將來的增強功能中獲益,我們建議使用 v3 或更高版本的一系列節點來設置 Azure-SSIS IR。

## <a name="current-limitations"></a>目前的限制

以下限制僅適用於標準自訂設定:

- 如果要在腳本中使用*gacutil.exe*在全域程式集緩存 (GAC) 中安裝程式集,則需要在自定義設置中提供*gacutil.exe。* 或者,您可以使用我們的 *「公共預覽」* 容器中提供的副本,稍後在「說明」部分中討論。

- 如果要引用腳本中的子資料夾 *,msiexec.exe*不支援引用`.\`根資料夾 的表示法。 使用 指令(`msiexec /i "MySubfolder\MyInstallerx64.msi" ...`如`msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`而不是 。

- Azure-SSIS IR 目前不支援由 Windows 自動建立的管理共享或隱藏的網路共用。

- Azure-SSIS IR 不支援 IBM iSeries 訪問 ODBC 驅動程式。 在自訂設定期間,您可能會看到安裝錯誤。 如果是,請聯繫 IBM 支援人員尋求説明。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

要自訂 Azure-SSIS IR,您需要以下專案:

- [Azure 訂閱](https://azure.microsoft.com/)

- [佈建您的 Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 儲存帳戶](https://azure.microsoft.com/services/storage/)。 不需要快速自定義設置。 對於標準自定義設置,您將上載自定義設置腳本及其關聯的檔案並存儲在 Blob 容器中。 自訂安裝程序也會將其執行記錄上傳至相同的 Blob 容器。

## <a name="instructions"></a>Instructions

1. 如果要使用 PowerShell 設定或重新設定 Azure-SSIS IR,請下載並安裝[Azure PowerShell](/powershell/azure/install-az-ps)。 對於快速自定義設置,請跳到步驟 4。

1. 準備您的自訂安裝指令碼和及相關聯的檔案 (例如 .bat、.cmd、.exe、.dll、.msi 或 .ps1 檔案)。

   * 您必須有一個名為*main.cmd*的文稿檔,這是自訂設置的入口點。  
   * 為了確保腳本可以靜默執行,我們建議您先在本地電腦上測試它。  
   * 如果希望將其他工具(例如*msiexec.exe*)生成的其他日誌上載到容器,請將預定義的環境`CUSTOM_SETUP_SCRIPT_LOG_DIR`變數 指定為腳本中的日誌資料夾(例如 *,msiexec /i xxx.msi/quiet/lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log)。*

1. 下載、安裝與開啟[Azure 儲存資源管理員](https://storageexplorer.com/)。 若要這樣做：

   a. 在 **「本地」和「附加」** 下,右鍵按下 **「儲存帳戶**」,然後選擇「**連接到 Azure 儲存**」 。。

      ![連線到 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. 選擇 **「使用儲存帳戶名稱和密鑰**」,然後選擇 **「下一步**」 。

      ![使用儲存體帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. 輸入 Azure 儲存帳戶名稱和密鑰,選擇 **「下一步**」,然後選擇「**連接**」 。。

      ![提供儲存帳戶名稱和金鑰](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 在連接的 Azure 儲存帳戶下,右鍵單擊**Blob 容器**,選擇 **「創建 Blob 容器**」,然後命名新容器。

      ![建立 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 選擇新容器,然後上載自定義設置腳本及其關聯檔。 請確保在容器的頂層上載*main.cmd,* 而不是在任何資料夾中。 還要確保容器僅包含必要的自定義設定檔,以便以後將它們下載到 Azure-SSIS IR 不會花很長時間。 自定義設置的最大持續時間當前設置為超時前 45 分鐘。這包括從容器下載所有檔並將其安裝在 Azure-SSIS IR 上的時間。 如果設置需要更多時間,則提高支援票證。

      ![將檔案上傳至 Blob 容器](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 右鍵單擊容器,然後選擇 **「獲取共享存取簽名**」。

      ![取得容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 使用足夠長的過期時間和讀取/寫入/清單許可權為容器創建 SAS URI。 每當重新映射或重新啟動 Azure-SSIS IR 的任何節點時,都需要 SAS URI 下載並運行自訂設定文本及其關聯檔。 您必須具備寫入權限才能上傳安裝執行記錄。

      > [!IMPORTANT]
      > 確保 SAS URI 不會過期,並且自定義設置資源始終在 Azure-SSIS IR 的整個生命週期中可用,從創建到刪除,尤其是在在此期間定期停止並啟動 Azure-SSIS IR 時。

      ![產生容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 複製並儲存容器 SAS URI。

      ![複製並儲存共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 使用資料出廠 UI 設定或重新配置 Azure-SSIS IR 時,可以透過在 **「整合時設定****」窗格的進階設定**部分中選擇 **「自訂 Azure-SSIS 整合執行時」複選框**來添加或刪除自訂設定。 

   如果要添加標準自定義設置,請在 **「自定義設定容器 SAS URI」** 框中輸入容器的 SAS URI。 
   
   如果要新增快速自訂設定,請選擇 **「新建」** 以開啟 **「 新增快速自訂設定**」 ,然後在 **「快速自訂設定類型**」下拉清單中選擇一種類型:

   * 如果選擇**Run cmdkey 命令**類型,則可以通過在 **/Add、/User**和 **/Pass**框中輸入目標計算機名稱或功能變數名稱、帳戶名稱或使用者名以及帳戶金鑰或密碼,在 **/User**Azure-SSIS IR 上保留 檔案共用或 Azure 檔案共用的訪問認證。 這類似於在本地電腦上運行 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey)命令。
   
   * 如果選擇 **「添加環境變數**類型」,則可以通過在**變數名稱**和**變數值**框中輸入環境變數名稱和值,添加 Windows 環境變數,以在 Azure-SSIS IR 上運行的套件中使用。 這類似於在本地電腦上運行 Windows[集](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1)命令。

   * 如果選擇 **「安裝授權元件**類型」,則可以在 **「元件名稱**下拉清單」中從我們的 ISV 合作夥伴中選擇整合元件:

     * 如果選擇**SentryOne 的任務工廠**元件,則可以透過在**許可證金鑰**框中輸入從 SentryOne 購買的產品許可證金鑰,在 Azure-SSIS IR 上安裝 SentryOne 中[的任務工廠](https://www.sentryone.com/products/task-factory/high-performance-ssis-components)元件套件。 目前的整合版本是**2019.4.3**。

     * 如果選擇**oh22 的 HEDDA。IO**元件,您可以安裝[HEDDA。購買](https://hedda.io/ssis-component/)服務後,Azure-SSIS IR 上的 oh22 中的 IO 資料品質/清理元件。 目前整合版本為**1.0.13**。

     * 如果選擇**oh22 的SQLPhonetics.NET**元件,則可以通過在 **「許可證」密鑰**框中輸入從它們購買的產品許可證金鑰,在 Azure-SSIS IR 上安裝[SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/)資料品質/匹配元件。 目前整合版本為**1.0.43**。

     * 如果選擇**KingswaySoft 的 SSIS 整合工具套件元件**,則可以通過在 Azure-SSIS IR 上輸入從它們購買的產品許可證金鑰,在 Azure-SSIS IR 上輸入從它們購買的產品許可證密鑰,從 KingswaySoft 安裝用於 CRM/ERP/行銷/協作應用的[SSIS 整合工具套件](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365)套件,例如**License key**Microsoft 動態/SharePoint/專案伺服器、Oracle/Salesforce 行銷雲等。 目前的整合版本是**2019.2。**

     * 如果選擇**KingswaySoft 的 SSIS 生產力包**元件,則可以通過在**許可證金鑰**框中輸入從它們購買的產品許可證金鑰,在 Azure-SSIS IR 上安裝來自金斯威軟體的[SSIS 生產力包](https://www.kingswaysoft.com/products/ssis-productivity-pack)元件套件。 目前整合版本為**10.0**。

     * 如果選擇**Theobald 軟體的 Xtract IS**元件,則可以通過拖動從 Azure-SSIS IR 上從 Theobald 軟體安裝 SAP 系統的[Xtract IS](https://theobald-software.com/en/xtract-is/)連接器套件(ERP、S/4HANA、BW),從而將從它們購買的產品許可證檔拖放到**許可證檔**框中&。 當前整合版本為**6.1.1.3**。

   您新增的快速自訂設定將顯示在 **「進階設定」 部份中**。 要刪除它們,請選擇其複選框,然後選擇"**刪除**"。

   ![具有自訂設定的進階設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. 使用 PowerShell 設定或重新配置 Azure-SSIS IR 時,可以透過在啟動`Set-AzDataFactoryV2IntegrationRuntime`Azure-SSIS IR 之前執行 cmdlet 來添加或刪除自訂設定。
   
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
   
   在標準自定義設定完成並啟動 Azure-SSIS IR 後,您可以在儲存容器的主 *.cmd.log*資料夾中找到*main.cmd*和其他執行日誌的標準輸出。

1. 要查看標準自定義設置的一些範例,請使用 Azure 儲存資源管理員連接到我們的公共預覽容器。

   a. 在 [(本機與已連結)]**** 下方，以滑鼠右鍵按一下 [儲存體帳戶]****，然後依序選取 [連線到 Azure 儲存體]****、[使用連接字串或共用存取簽章 URI]**** 和 [下一步]****。

      ![使用共用存取簽章連線至 Azure 儲存體](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. 選擇 **「 使用 SAS URI」** 的, 然後在**URI**框中輸入以下 SAS URI:

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![提供容器的共用存取簽章](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. 選擇 **「下一步**」,然後選擇「**連接**」。

   d. 在左側窗格中,選擇已連接**的公共預覽**容器,然後按兩下*自定義設置腳本*資料夾。 此資料夾中包含下列項目：

      * *範例*資料夾,其中包含用於在 Azure-SSIS IR 的每個節點上安裝基本任務的自訂設置。 此工作不會執行任何動作，而會休眠數秒鐘。 該資料夾還包含一個*gacutil*資料夾,其全部內容(gacutil.exe、gacutil.exe.config 和*1033_gacutlrc.dll)* 可以像一樣*gacutil.exe**gacutil.exe.config*複製到您的容器。

      * *UserScenarios*資料夾,其中包含來自真實使用者方案的多個自定義設置示例。

        ![公開預覽容器的內容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. 按下 *「使用者機制」* 資料夾可搜尋以下專案:

      * *.NET FRAMEWORK 3.5*資料夾,其中包含自定義設置腳本 *(main.cmd),* 用於安裝 Azure-SSIS IR 的每個節點上的自定義元件可能需要的早期版本.NET Framework。

      * *BCP*資料夾,其中包含一個自定義設定腳本 *(main.cmd*),用於在 Azure-SSIS IR 的每個節點上安裝 SQL Server 命令列實用程式 *(MsSqlCmdLnUtils.msi),* 包括批量複製程式 *(bcp*)。

      * *EXCEL*資料夾,其中包含用於安裝 C# 程式集和庫的自定義設定文稿 *(main.cmd),* 可用於在文稿任務中動態讀取和寫入 Azure-SSIS IR 的每個節點上的 Excel 檔。 
      
        首先,下載[*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/)和[*DocumentFormat.OpenXml.dll,*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)然後將它們連同*main.cmd*一起上傳到您的容器。 或者,如果只想使用標準 Excel 連接管理器、Excel 源和 Excel 目標,則所需的可再分發訪問已在 Azure-SSIS IR 上預裝,因此不需要任何自定義設置。
      
      * *MYSQL ODBC*資料夾,其中包含自定義設置腳本 *(main.cmd*),用於在 Azure-SSIS IR 的每個節點上安裝 MySQL ODBC 驅動程式。 此設定允許您使用 ODBC 連接管理員、源和目標連接到 MySQL 伺服器。 
     
        首先,[下載 MySQL ODBC 驅動程式安裝程式的最新 64 位元和 32 位元(](https://dev.mysql.com/downloads/connector/odbc/)例如 *,mysql 連接器-odbc-8.0.13-winx64.msi*和*mysql 連接器-odbc-8.0.13-win32.msi),* 然後將它們與*main.cmd*一起上載到您的容器。

      * *ORACLE ENTERPRISE*資料夾,其中包含自定義設定腳本 *(main.cmd*) 和靜默安裝配置檔 *(client.rsp),* 用於在 Azure-SSIS IR 企業版的每個節點上安裝 Oracle 連接器和 OCI 驅動程式。 此設定允許您使用 Oracle 連接管理員、源和目標連接到 Oracle 伺服器。 
      
        首先,從[微軟下載中心和](https://www.microsoft.com/en-us/download/details.aspx?id=55179)最新的 Oracle 用戶端(例如*winx64_12102_client.zip)* 下載 Oracle 的 Microsoft 連接器 v5.0(AtunitySISOraAdaptersSetup.msi 和*AtunitySISOraAdaptersSetup64.msi),* 然後將它們連同*main.cmd*和*client.rsp*一起上傳到您的容器。* * [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) 如果使用 TNS 連接到 Oracle,則還需要下載*tnsnames.ora,* 對其進行編輯,並將其上載到容器,以便在安裝期間將其複製到 Oracle 安裝資料夾。

      * *ORACLE 標準ADO.NET*資料夾,其中包含自定義設置腳本 *(main.cmd*),用於在 Azure-SSIS IR 的每個節點上安裝 Oracle ODP.NET驅動程式。 此設定允許您使用ADO.NET連接管理器、源和目標連接到 Oracle 伺服器。 
      
        首先,[下載最新的 Oracle ODP.NET驅動程式](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)(例如*ODP.NET_Managed_ODAC122cR1.zip),* 然後將它與*main.cmd*一起上傳到您的容器。
       
      * *ORACLE 標準 ODBC*資料夾,其中包含自定義設定文稿 *(main.cmd*),用於在 Azure-SSIS IR 的每個節點上安裝 Oracle ODBC 驅動程式並配置數據源名稱 (DSN)。 此設定允許您使用 ODBC 連接管理員、源和目標或電源查詢連接管理器以及具有 ODBC 資料來源類型的來源連接到 Oracle 伺服器。 
      
        首先,下載最新的 Oracle 即時用戶端(基本套件或基本精簡版套件)和 ODBC 套件,然後將它們連同*main.cmd*一起上傳到您的容器:
        * [下載 64 位元套件](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)(基本包:*即時用戶端-基本視窗.x64-18.3.0.0dbru.zip;* 基本精簡版封裝:*即時用戶端-基本精簡視窗.x64-18.3.0.0dbru.zip;* ODBC 封裝:*即時用戶端-odbc-windows.x64-18.3.0.0dbru.zip*) 
        * [下載 32 位包](https://www.oracle.com/technetwork/topics/winsoft-085727.html)(基本包:*即時用戶端-基本 nt-18.3.0.0dbru.zip;* 基本精簡包:*即時用戶端-基本精簡-nt-nt-18.3.0.0dbru.zip;* ODBC 封裝:*即時客戶端-odbc-nt-18.3.0.0dbru.zip*)

      * *ORACLE 標準 OLEDB*資料夾,其中包含自定義設置腳本 *(main.cmd*),用於在 Azure-SSIS IR 的每個節點上安裝 Oracle OLEDB 驅動程式。 此設定允許您使用 OLEDB 連接管理員、源和目標連接到 Oracle 伺服器。 
     
        首先,[下載最新的 Oracle OLEDB 驅動程式](https://www.oracle.com/partners/campaign/index-090165.html)(例如 *,ODAC122010Xcopy_x64.zip),* 然後將其與*main.cmd*一起上傳到容器。

      * *POSTGRESQL ODBC*資料夾,其中包含自定義設置腳本 *(main.cmd*),用於在 Azure-SSIS IR 的每個節點上安裝 PostgreSQL ODBC 驅動程式。 此設定允許您使用 ODBC 連接管理員、源和目標連接到 PostgreSQL 伺服器。 
     
        首先,[下載最新的 64 位元和 32 位元版本的 PostgreSQL ODBC 驅動程式安裝程式](https://www.postgresql.org/ftp/odbc/versions/msi/)(例如 *,psqlodbc_x64.msi*和*psqlodbc_x86.msi),* 然後將它們連同*main.cmd*一起上載到您的容器。

      * *SAP BW*資料夾,其中包含自定義設置腳本 *(main.cmd*),用於在 Azure-SSIS IR 企業版的每個節點上安裝 SAP .NET 連接器程式集 *(librfc32.dll)。* 此設定允許您使用 SAP 業務主目錄 (BW) 連接管理員、源和目標連接到 SAP BW 伺服器。 
      
        首先,將 64 位元或 32 位元版本的*librfc32.dll*從 SAP 安裝資料夾中連同*main.cmd*一起上傳到容器。 在設定期間將 SAP 程式集複製到 *%windir%_SysWow64*或 *%windir%_System32*資料夾。

      * *儲存*資料夾,其中包含自定義設置腳本 *(main.cmd*),用於在 Azure-SSIS IR 的每個節點上安裝 Azure PowerShell。 這個設定讓您部署與執行 PowerShell 文稿的 SSIS[套件 來操作 Azure 儲存帳戶](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)。 
      
        將*main.cmd*,*範例 AzurePowerShell.msi(* 或使用最新版本)和*儲存.ps1*複製到容器。 使用*PowerShell.dtsx*作為包的範本。 包範本結合了[Azure Blob 下載任務](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task),該任務將*儲存.ps1*下載為可修改的 PowerShell 腳本,另一個[程式任務 (執行行程任務](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/))在每個節點上執行腳本。

      * *一個TERADATA*資料夾,其中包含一個自定義設置腳本 *(main.cmd*),其關聯的檔 *(install.cmd)* 和安裝程式包 *(.msi*)。 這些檔在 Azure-SSIS IR 企業版的每個節點上安裝 Teradata 連接器、Teradata 並行傳輸器 (TPT) API 和 ODBC 驅動程式。 此設定允許您使用 Teradata 連接管理員、源和目標連接到 Teradata 伺服器。 
      
        首先,[下載 Teradata 工具和實用程式 15.x zip 檔](http://partnerintelligence.teradata.com)(例如 *,TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip),* 然後將它與前面提到的 *.cmd*和 *.msi*檔一起上載到容器。

      * *TLS 1.2*資料夾,其中包含自定義設置腳本 *(main.cmd*),用於使用強加密/更安全的網路協定 (TLS 1.2),並在 Azure-SSIS IR 的每個節點上禁用較舊的 SSL/TLS 版本。

      * *ZULU OPENJDK*資料夾,其中包含自定義設定腳本 *(main.cmd*) 和 PowerShell 檔 *(install_openjdk.ps1),* 用於在 Azure-SSIS IR 的每個節點上安裝祖魯 OpenJDK。 此設定允許您使用 Azure 資料湖儲存和靈活檔案連接器來處理 ORC 和 Parquet 檔案。 有關詳細資訊,請參閱[整合服務的 Azure 功能包](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)。 
      
        首先,[下載最新的祖魯開放JDK(](https://www.azul.com/downloads/zulu/zulu-windows/)例如 *,zulu8.33.0.1-jdk8.0.192-win_x64.zip),* 然後連同*main.cmd*和*install_openjdk.ps1*一起上傳到您的容器。

        ![使用者案例資料夾中的資料夾](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 要嘗試這些自定義設置範例,請將內容從所選資料夾複製到容器。
   
      使用資料出廠 UI 設定或重新配置 Azure-SSIS IR 時,請在「進階設定」部分選中「**自訂 Azure-SSIS 整合執行時」** 複選框,然後在 **「進階設定」** 部分輸入容器的 SAS URI。 **Custom setup container SAS URI**
   
      使用 PowerShell 設定或重新配置 Azure-SSIS`Set-AzDataFactoryV2IntegrationRuntime`IR 時, 使用容器的`SetupScriptContainerSasUri`SAS URI 運行 cmdlet 作為參數值。

## <a name="next-steps"></a>後續步驟

- [設定 Azure-SSIS 整合執行時的企業版](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [為 Azure-SSIS 整合執行時開發付費或許可的自訂元件](how-to-develop-azure-ssis-ir-licensed-components.md)
