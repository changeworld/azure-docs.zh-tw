---
title: 使用已啟用 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services (SSIS) 套件
description: 瞭解如何使用已啟用 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services (SSIS) 套件。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927074"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>使用已啟用 Azure 的 dtexec 公用程式來執行 SQL Server Integration Services 套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明啟用 Azure 的 dtexec (AzureDTExec) 命令提示字元公用程式。 它是用來在 (的 Azure-SSIS Integration Runtime) IR Azure Data Factory 上執行 SQL Server Integration Services (SSIS) 套件。

傳統的 dtexec 公用程式隨附于 SQL Server。 如需詳細資訊，請參閱 [dtexec 公用程式](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 它通常是由協力廠商協調器或排程器叫用，例如 ActiveBatch 和 Control-M，以在內部部署執行 SSIS 套件。 

新式 AzureDTExec 公用程式隨附 SQL Server Management Studio (SSMS) 工具。 您也可以透過協力廠商協調器或排程器來叫用它，以在 Azure 中執行 SSIS 套件。 它可加速將 SSIS 套件放入和轉移至雲端。 在遷移之後，如果您想要在日常作業中繼續使用協力廠商協調器或排程器，他們現在可以叫用 AzureDTExec 而非 dtexec。

AzureDTExec 會執行您的封裝，做為 Data Factory 管線中的執行 SSIS 套件活動。 如需詳細資訊，請參閱以 [Azure Data Factory 活動的形式執行 SSIS 套件](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。 

您可以透過 SSMS 設定 AzureDTExec，以使用在您的 data factory 中產生管線的 Azure Active Directory (Azure AD) 應用程式。 您也可以將它設定為存取檔案系統、檔案共用，或儲存封裝的 Azure 檔案儲存體。 根據您為其調用選項提供的值，AzureDTExec 會產生並執行唯一的 Data Factory 管線，其中包含執行 SSIS 套件活動。 以相同的值叫用 AzureDTExec，會重新叫用現有的管線。

## <a name="prerequisites"></a>必要條件
若要使用 AzureDTExec，請下載並安裝最新版本的 SSMS，也就是18.3 版或更新版本。 請從[此網站](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下載。

## <a name="configure-the-azuredtexec-utility"></a>設定 AzureDTExec 公用程式
在本機電腦上安裝 SSMS 也會安裝 AzureDTExec。 若要設定其設定，請使用 [以 **系統管理員身分執行** ] 選項啟動 SSMS。 然後選取**Tools**[  >  **遷移至 azure 的工具]，以**  >  **設定啟用 azure 的 DTExec**。

![設定啟用 Azure 的 dtexec 功能表](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

此動作會開啟 **AzureDTExecConfig** 視窗，此視窗需要以系統管理許可權開啟，才能寫入 *AzureDTExec 配置* 檔中。 如果您尚未以系統管理員身分執行 SSMS，則會開啟 (UAC) 視窗的使用者帳戶控制。 輸入您的系統管理員密碼，以提升您的許可權。

![設定啟用 Azure 的 dtexec 設定](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

在 [ **AzureDTExecConfig** ] 視窗中，輸入您的設定設定，如下所示：

- **ApplicationId**：輸入您所建立之 Azure AD 應用程式的唯一識別碼，以在您的 data factory 中產生管線的正確許可權。 如需詳細資訊，請參閱透過 [Azure 入口網站建立 Azure AD 應用程式和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。
- **AuthenticationKey**：輸入 Azure AD 應用程式的驗證金鑰。
- **TenantId**：輸入用來建立 Azure AD 應用程式之 Azure AD 租使用者的唯一識別碼。
- **DataFactory**：輸入您的資料處理站名稱，在其中使用執行 SSIS 套件活動的唯一管線會根據您叫用 AzureDTExec 時所提供的選項值產生。
- **IRName**：輸入您的 data factory 中 Azure-SSIS IR 的名稱，當您叫用 AzureDTExec 時，將會在其通用命名慣例 (UNC) 路徑中指定的套件。
- **PipelineNameHashStrLen**：輸入當您叫用 AzureDTExec 時所提供的選項值所產生的雜湊字串長度。 這些字串是用來為在 Azure-SSIS IR 上執行套件 Data Factory 管線形成唯一的名稱。 通常長度為32個字元就已足夠。
- **ResourceGroup**：輸入您的 data factory 建立所在的 Azure 資源組名。
- **SubscriptionId**：輸入用來建立 data Factory 的 Azure 訂用帳戶的唯一識別碼。
- **LogAccessDomain**：當您寫入記錄檔時，請輸入用來存取記錄檔資料夾的網域認證，當指定 **LogPath** 時，這是必要的，且 **LogLevel** 不是 **null**。
- **LogAccessPassword**：當您寫入記錄檔時，請輸入用來存取記錄檔資料夾的密碼認證，當指定 **LogPath** 時，這是必要的，且 **LogLevel** 不是 **null**。
- **LogAccessUserName**：當您寫入記錄檔時，請輸入使用者名稱認證，以在其 UNC 路徑中存取您的記錄檔資料夾，當指定 **LogPath** 時，這是必要的，且 **LogLevel** 不是 **null**。
- **LogLevel**：針對 Azure-SSIS IR 上套件執行的預先定義 **null**、 **基本**、 **詳細**資訊或 **效能** 選項，輸入選取的記錄範圍。
- **LogPath**：輸入記錄檔資料夾的 UNC 路徑，這是寫入 Azure-SSIS IR 上封裝執行的記錄檔。
- **PackageAccessDomain**：輸入網域認證，以在您叫用 AzureDTExec 時所指定的 UNC 路徑中存取您的套件。
- **PackageAccessPassword**：輸入密碼認證，以在您叫用 AzureDTExec 時所指定的 UNC 路徑中存取您的套件。
- **PackageAccessUserName**：輸入使用者名稱認證，以在您叫用 AzureDTExec 時所指定的 UNC 路徑中存取您的套件。

若要將封裝和記錄檔儲存在內部部署的檔案系統或檔案共用中，請將您的 Azure-SSIS IR 加入連線到內部部署網路的虛擬網路，讓它可以提取您的套件並寫入記錄檔。 如需詳細資訊，請參閱將 [Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

為了避免以純文字顯示寫入 *AzureDTExec* 檔的機密值，我們會將它們編碼成 Base64 編碼的字串。 當您叫用 AzureDTExec 時，所有 Base64 編碼字串都會解碼回其原始值。 您可以藉由限制可以存取的帳戶，進一步保護 *AzureDTExec 配置* 檔案。

## <a name="invoke-the-azuredtexec-utility"></a>叫用 AzureDTExec 公用程式
您可以在命令列提示字元中叫用 AzureDTExec，並在您的使用案例中提供特定選項的相關值。

公用程式會安裝在 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn` 。 您可以將其路徑新增至「路徑」環境變數，以便從任何地方叫用它。

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

叫用 AzureDTExec 提供類似于叫用 dtexec 的選項。 如需詳細資訊，請參閱 [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 以下是目前支援的選項：

- **/F [ile]**：載入儲存在檔案系統、檔案共用或 Azure 檔案儲存體中的封裝。 您可以使用 .dtsx 副檔名，在檔案系統、檔案共用或 Azure 檔案儲存體中指定封裝檔案的 UNC 路徑作為此選項的值。 如果指定的 UNC 路徑包含任何空格，請在整個路徑前後加上引號。
- **/Conf [igFile]**：指定要從中解壓縮值的設定檔。 您可以使用這個選項來設定封裝的執行時間設定，此設定與設計階段所指定的不同。 您可以在 XML 設定檔中儲存不同的設定，然後在封裝執行之前載入它們。 如需詳細資訊，請參閱 [SSIS 封裝](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)設定。 若要指定此選項的值，請使用檔案系統、檔案共用或 Azure 檔案儲存體中設定檔的 UNC 路徑（副檔名為 Ssistutorial.dtsconfig）。 如果指定的 UNC 路徑包含任何空格，請在整個路徑前後加上引號。
- **/Conn [ection]**：指定封裝中現有連接管理員的連接字串。 使用這個選項，您可以針對封裝中現有的連線管理員，設定與設計階段所指定之連接管理員不同的執行時間連接字串。 指定此選項的值，如下所示： `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]` 。
- **/Set**：覆寫封裝中參數、變數、屬性、容器、記錄提供者、Foreach 列舉值或連接的設定。 您可以多次指定這個選項。 指定此選項的值，如下所示： `property_path;value` 。 例如，會 `\package.variables[counter].Value;1` 將變數的值覆寫 `counter` 為1。 您可以使用 [ **套件** 設定向導]， `property_path` 針對您要覆寫其值的套件中的專案尋找、複製及貼上的值。 如需詳細資訊，請參閱 [套件設定 wizard](https://docs.microsoft.com/sql/integration-services/packages/legacy-package-deployment-ssis)。
- **/De [crypt]**：設定以 **>encryptallwithpassword** / **EncryptSensitiveWithPassword**保護等級設定之套件的解密密碼。

> [!NOTE]
> 使用新的選項值叫用 AzureDTExec，會產生新的管線，但選項 **/De [腳本 c ...]** 除外。

## <a name="next-steps"></a>後續步驟

當您叫用 AzureDTExec 時，會產生並執行具有「執行 SSIS 套件」活動的唯一管線之後，您可以在 Data Factory 入口網站上監視它們。 如果您想要使用 Data Factory 來協調/排程它們，您也可以將 Data Factory 觸發程式指派給它們。 如需詳細資訊，請參閱以 [Data Factory 活動的形式執行 SSIS 套件](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

> [!WARNING]
> 產生的管線預期只能由 AzureDTExec 使用。 它的屬性或參數未來可能會變更，因此請勿修改或重複使用它們來因應其他用途。 修改可能會中斷 AzureDTExec。 如果發生這種情況，請刪除管線。 AzureDTExec 會在下一次叫用時產生新的管線。