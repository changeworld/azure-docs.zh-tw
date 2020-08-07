---
title: 使用具備 Azure 功能的 dtexec 公用程式來執行 SQL Server Integration Services (SSIS) 套件
description: 瞭解如何使用支援 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services (SSIS) 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 94b581f677e370911a60db08276ff7dd0eb45486
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927074"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>使用啟用 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services 套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明 (AzureDTExec) 命令提示字元公用程式中啟用 Azure 的 dtexec。 它可用來在 (的 Azure SSIS Integration Runtime) IR Azure Data Factory 上執行 SQL Server Integration Services (SSIS) 套件。

傳統的 dtexec 公用程式隨附 SQL Server。 如需詳細資訊，請參閱[dtexec 公用程式](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 它通常是由協力廠商協調器或排程器叫用，例如 ActiveBatch 和 Control-M，以在內部部署環境中執行 SSIS 套件。 

新式 AzureDTExec 公用程式隨附 SQL Server Management Studio (SSMS) 工具。 它也可以由協力廠商協調器或排程器叫用，以便在 Azure 中執行 SSIS 套件。 它可協助將您的 SSIS 套件隨即轉移或遷移至雲端。 在遷移之後，如果您想要在日常作業中繼續使用協力廠商協調器或排程器，他們現在可以叫用 AzureDTExec，而不是 dtexec。

AzureDTExec 會以 Data Factory 管線中的執行 SSIS 套件活動來執行您的封裝。 如需詳細資訊，請參閱將[SSIS 套件當做 Azure Data Factory 活動執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。 

AzureDTExec 可以透過 SSMS 設定，以使用 Azure Active Directory (Azure AD) 應用程式，以在您的 data factory 中產生管線。 您也可以將它設定為存取檔案系統、檔案共用，或 Azure 檔案儲存體儲存封裝的位置。 根據您為其叫用選項提供的值，AzureDTExec 會產生並執行唯一 Data Factory 管線，並在其中執行 SSIS 套件活動。 以相同的選項值叫用 AzureDTExec 時，會重新叫用現有的管線。

## <a name="prerequisites"></a>必要條件
若要使用 AzureDTExec，請下載並安裝最新版本的 SSMS，也就是18.3 版或更新版本。 請從[此網站](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下載。

## <a name="configure-the-azuredtexec-utility"></a>設定 AzureDTExec 公用程式
在您的本機電腦上安裝 SSMS 也會安裝 AzureDTExec。 若要設定其設定，請使用 [以**系統管理員身分執行**] 選項來啟動 SSMS。 然後選取 [**工具**] [  >  **遷移至 azure**] [  >  **設定啟用 azure 的 DTExec**]。

![設定啟用 Azure 的 dtexec 功能表](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

此動作會開啟一個**AzureDTExecConfig**視窗，必須以系統管理許可權開啟，才能寫入*AzureDTExec 配置*檔案。 如果您尚未以系統管理員身分執行 SSMS，則會開啟 [ (UAC) ] 視窗的 [使用者帳戶控制]。 輸入您的系統管理員密碼以提升您的許可權。

![設定啟用 Azure 的 dtexec 設定](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

在 [ **AzureDTExecConfig** ] 視窗中，輸入您的設定，如下所示：

- **ApplicationId**：輸入您所建立之 Azure AD 應用程式的唯一識別碼，其具有在資料處理站中產生管線的適當許可權。 如需詳細資訊，請參閱透過[Azure 入口網站建立 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。
- **AuthenticationKey**：輸入 Azure AD 應用程式的驗證金鑰。
- **TenantId**：輸入 Azure AD 租使用者的唯一識別碼，用來建立您的 Azure AD 應用程式。
- **DataFactory**：輸入您的資料處理站名稱，其中會根據您叫用 AzureDTExec 時所提供的選項值，產生其中具有 Execute SSIS 套件活動的唯一管線。
- **IRName**：輸入您的 data factory 中的 AZURE SSIS IR 名稱，其通用命名慣例中所指定的套件 (UNC) 路徑會在您叫用 AzureDTExec 時執行。
- **PipelineNameHashStrLen**：輸入當您叫用 AzureDTExec 時，要從您提供的選項值產生的雜湊字串長度。 這些字串是用來針對在 Azure SSIS IR 上執行封裝的 Data Factory 管線，形成唯一的名稱。 通常長度為32個字元就已足夠。
- **ResourceGroup**：輸入您的 data factory 建立所在的 Azure 資源組名。
- **SubscriptionId**：輸入 Azure 訂用帳戶的唯一識別碼，以在其下建立您的 data factory。
- **LogAccessDomain**：當您寫入記錄檔時，請輸入網域認證以存取其 UNC 路徑中的記錄檔資料夾，而當您指定**LogPath**時，這是必要的，而且**LogLevel**不是**null**。
- **LogAccessPassword**：當您寫入記錄檔時，請輸入密碼認證以存取其 UNC 路徑中的記錄檔資料夾，但在指定**LogPath**時，這是必要的，而且**LogLevel**不是**null**。
- **LogAccessUserName**：當您寫入記錄檔時，請輸入使用者名稱認證以存取其 UNC 路徑中的記錄檔資料夾，而當您指定**LogPath**時，這是必要的，而且**LogLevel**不是**null**。
- **LogLevel**：針對您在 AZURE SSIS IR 上執行的封裝，輸入從預先定義的**null**、**基本**、**詳細**資訊或**效能**選項中選取的記錄範圍。
- **LogPath**：輸入記錄檔資料夾的 UNC 路徑，以在其中寫入 AZURE SSIS IR 上的封裝執行記錄檔。
- **PackageAccessDomain**：輸入網域認證，以存取您叫用 AzureDTExec 時指定的 UNC 路徑中的封裝。
- **PackageAccessPassword**：輸入密碼認證，以在您叫用 AzureDTExec 時指定的 UNC 路徑中存取您的封裝。
- **PackageAccessUserName**：輸入使用者名稱認證，以在您叫用 AzureDTExec 時指定的 UNC 路徑中存取您的封裝。

若要將封裝和記錄檔儲存在內部部署的檔案系統或檔案共用中，請將您的 Azure SSIS IR 加入連線到內部部署網路的虛擬網路，讓它可以提取您的封裝並寫入您的記錄檔。 如需詳細資訊，請參閱將[AZURE SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

若要避免以純文字顯示寫入*AzureDTExec*檔案中的機密值，我們會將它們編碼成 Base64 編碼的字串。 當您叫用 AzureDTExec 時，所有 Base64 編碼的字串都會解碼回其原始值。 您可以藉由限制可存取的帳戶，進一步保護*AzureDTExec 的配置*檔案。

## <a name="invoke-the-azuredtexec-utility"></a>叫用 AzureDTExec 公用程式
您可以在命令列提示字元中叫用 AzureDTExec，並針對使用案例中的特定選項提供相關的值。

公用程式是安裝在 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` 。 您可以將其路徑新增至「路徑」環境變數，以便從任何地方叫用它。

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

叫用 AzureDTExec 會提供類似的選項，以叫用 dtexec。 如需詳細資訊，請參閱 [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 以下是目前支援的選項：

- **/F [ile]**：載入儲存在檔案系統、檔案共用或 Azure 檔案儲存體中的封裝。 作為此選項的值，您可以在 [檔案系統]、[檔案共用] 或 [Azure 檔案儲存體] 中，指定封裝檔案的 UNC 路徑，其副檔名為 .dtsx。 如果指定的 UNC 路徑包含任何空格，請在整個路徑前後加上引號。
- **/Conf [igFile]**：指定要從中解壓縮值的設定檔。 使用此選項，您可以針對封裝設定與設計階段所指定不同的執行時間設定。 您可以將不同的設定儲存在 XML 設定檔中，然後在封裝執行之前載入它們。 如需詳細資訊，請參閱[SSIS 封裝](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)設定。 若要指定此選項的值，請在檔案系統、檔案共用或 Azure 檔案儲存體的設定檔案中使用 UNC 路徑，其副檔名為 Ssistutorial.dtsconfig。 如果指定的 UNC 路徑包含任何空格，請在整個路徑前後加上引號。
- **/Conn [ection]**：指定封裝中現有連接管理員的連接字串。 使用此選項，您可以針對封裝中現有的連線管理員，設定與設計階段所指定不同的執行時間連接字串。 指定此選項的值，如下所示： `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` 。
- **/Set**：覆寫封裝中參數、變數、屬性、容器、記錄提供者、Foreach 列舉值或連接的設定。 可以多次指定這個選項。 指定此選項的值，如下所示： `property_path;value` 。 例如，會 `\package.variables[counter].Value;1` 將變數的值覆寫 `counter` 為1。 您可以使用 [**套件**設定] wizard 來尋找、複製和貼上您的 `property_path` 封裝中要覆寫其值之專案的值。 如需詳細資訊，請參閱[Package Configuration wizard](https://docs.microsoft.com/sql/integration-services/packages/legacy-package-deployment-ssis)。
- **/De [crypt]**：設定以**EncryptAllWithPassword** / **EncryptSensitiveWithPassword**保護等級設定之套件的解密密碼。

> [!NOTE]
> 以新值叫用 AzureDTExec 的選項會產生新的管線，但選項 **/De [腳本]** 除外。

## <a name="next-steps"></a>後續步驟

當您叫用 AzureDTExec 時，會產生並執行在其中具有 Execute SSIS 套件活動的唯一管線之後，您可以在 Data Factory 入口網站上監視它們。 如果您想要使用 Data Factory 來協調/排程它們，您也可以將 Data Factory 觸發程式指派給它們。 如需詳細資訊，請參閱將[SSIS 套件當做 Data Factory 活動執行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

> [!WARNING]
> 產生的管線預期只能由 AzureDTExec 使用。 其屬性或參數可能會在未來變更，因此請不要修改或重複使用它們來進行任何其他用途。 修改可能會中斷 AzureDTExec。 如果發生這種情況，請刪除管線。 AzureDTExec 會在下一次叫用時產生新的管線。