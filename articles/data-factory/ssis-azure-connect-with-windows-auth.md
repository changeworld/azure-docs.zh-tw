---
title: 使用 Windows 認證存取資料儲存和檔案分享
description: 了解如何在 Azure SQL Database 中與 Azure Data Factory 中設定 SSIS 目錄和 Azure-SSIS Integration Runtime，以執行利用 Windows 驗證來連線至資料來源和檔案共用的套件。
ms.date: 3/22/2018
ms.topic: conceptual
ms.prod: sql
ms.prod_service: integration-services
ms.custom: ''
ms.technology: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: maghan
ms.openlocfilehash: 0c4cdc3481fb58efd8eaa4cd83e1d6167f203a4e
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81760182"
---
# <a name="access-data-stores-and-file-shares-with-windows-authentication-from-ssis-packages-in-azure"></a>在 Azure 中從 SSIS 套件使用 Windows 驗證來存取資料存放區和檔案共用

您可以使用 Windows 驗證從 Azure Data Factory (ADF) 中 Azure-SSIS Integration Runtime (IR) 上執行的 SSIS 套件存取資料存放區，例如 SQL 伺服器、檔案共用、Azure 檔案等。 您的資料存放區可以是內部部署、裝載在 Azure 虛擬機器 (VM)上，也可以作為受控服務在 Azure 中執行。 如果它們位於內部,則需要將 Azure-SSIS IR 加入連接到本地網路的虛擬網路(Microsoft Azure 虛擬網路),請參閱[將 Azure-SSIS IR 加入 Microsoft Azure 虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。 從 Azure-SSIS IR 上執行的 SSIS 套件中使用 Windows 驗證存取資料存放區有四種方法：

| 連線方法 | 有效範圍 | 設定步驟 | 套件中的存取方法 | 認證組數和已連線資源 | 已連線資源的類型 | 
|---|---|---|---|---|---|
| 設定活動層級的執行內容 | 每個執行 SSIS 套件活動 | 在執行 SSIS 套件作為 ADF 管線中的執行 SSIS 套件活動時，設定 **Windows 驗證**屬性來設定 "Execution/Run as" 內容。<br/><br/> 如需詳細資訊，請參閱[設定執行 SSIS 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。 | 透過 UNC 路徑直接在套件中存取資源,例如,如果您使用檔案分享或`\\YourFileShareServerName\YourFolderName`Azure 檔案:`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | 針對所有已連線的資源只支援一組認證 | - 內部部署/Azure VM 上的檔案共用<br/><br/> - Azure 檔案，請參閱[使用 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - 內部部署 SQL Server/Azure VM 搭配 Windows 驗證<br/><br/> - 其他資源搭配 Windows 驗證 |
| 設定目錄層級的執行內容 | 每個 Azure-SSIS IR,但在設定活動級執行上下文時被覆寫(請參閱上文) | 執行 SSISDB `catalog.set_execution_credential` 預存程序，以設定 "Execution/Run as" 內容。<br/><br/> 如需詳細資訊，請參閱本文下方的其餘部分。 | 透過 UNC 路徑直接在套件中存取資源,例如,如果您使用檔案分享或`\\YourFileShareServerName\YourFolderName`Azure 檔案:`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | 針對所有已連線的資源只支援一組認證 | - 內部部署/Azure VM 上的檔案共用<br/><br/> - Azure 檔案，請參閱[使用 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - 內部部署 SQL Server/Azure VM 搭配 Windows 驗證<br/><br/> - 其他資源搭配 Windows 驗證 |
| 透過 `cmdkey` 命令保存認證 | 每個 Azure-SSIS IR,但在設定活動/目錄層執行上下文時被覆寫(請參閱上文) | 在`cmdkey`預先使用 Azure-SSIS`main.cmd`IR 時, 在自訂設定文稿 () 執行命令,例如,如果您`cmdkey /add:YourFileShareServerName /user:YourDomainName\YourUsername /pass:YourPassword``cmdkey /add:YourAzureStorageAccountName.file.core.windows.net /user:azure\YourAzureStorageAccountName /pass:YourAccessKey`使用檔案共享或 Azure 檔:或 。<br/><br/> 如需詳細資訊，請參閱[自訂 Azure SSIS IR 的安裝](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)。 | 透過 UNC 路徑直接在套件中存取資源,例如,如果您使用檔案分享或`\\YourFileShareServerName\YourFolderName`Azure 檔案:`\\YourAzureStorageAccountName.file.core.windows.net\YourFolderName` | 支援針對不同的連線資源使用多組認證 | - 內部部署/Azure VM 上的檔案共用<br/><br/> - Azure 檔案，請參閱[使用 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) <br/><br/> - 內部部署 SQL Server/Azure VM 搭配 Windows 驗證<br/><br/> - 其他資源搭配 Windows 驗證 |
| 在套件執行期間裝載磁碟機 (非持續性) | 每個套件 | 以在套件控制流程一開始加入的「執行處理工作」執行 `net use` 命令，例如：`net use D: \\YourFileShareServerName\YourFolderName` | 透過對應磁碟機存取檔案共用 | 支援不同的檔案共用使用多個磁碟機 | - 內部部署/Azure VM 上的檔案共用<br/><br/> - Azure 檔案，請參閱[使用 Azure 檔案共用](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) |
|||||||

> [!WARNING]
> 如果不使用上述任何方法使用 Windows 身份驗證存取數據存儲,則依賴於 Windows 身份驗證的包無法存取它們並在運行時失敗。 

本文的其餘部分會說明如何設定 Azure SQL Database 伺服器/受控執行個體中裝載的 SSIS 目錄 (SSISDB)，以在 Azure SSIS IR 上執行使用 Windows 驗證來存取資料存放區的套件。 

## <a name="you-can-only-use-one-set-of-credentials"></a>您只能使用一組認證

當您在 SSIS 套件中使用 Windows 驗證時，您只能使用一組認證。 遵循本文步驟將所提供的網域認證套用於 Azure-SSIS IR 上的所有套件執行 (互動或排程)，直到您變更或移除這些認證為止。 如果您的套件必須使用不同組認證連線到多個資料存放區，您應該考慮上述替代方法。

## <a name="provide-domain-credentials-for-windows-authentication"></a>提供 Windows 驗證的網域認證

若要提供網域認證，讓套件使用 Windows 驗證以存取內部部署資料存放區，請執行下列動作：

1. 使用 SQL Server Management Studio (SSMS) 或其他工具，連接到裝載 SSISDB 的 Azure SQL Database 伺服器/受控執行個體。 如需詳細資訊，請參閱[連線至 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)。

2. 使用 SSISDB 作為目前的資料庫，開啟查詢視窗。

3. 執行下列預存程序，並提供適當的網域認證：

   ```sql
   catalog.set_execution_credential @user='<your user name>', @domain='<your domain name>', @password='<your password>'
   ```

4. 執行 SSIS 套件。 這些包使用您提供的認證具有 Windows 驗證的本地資料儲存。

### <a name="view-domain-credentials"></a>檢視網域認證

若要檢視作用中的網域認證，請執行下列動作：

1. 使用 SSMS 或其他工具，連接到裝載 SSISDB 的 Azure SQL Database 伺服器/受控執行個體。 如需詳細資訊，請參閱[連線至 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)。

2. 使用 SSISDB 作為目前的資料庫，開啟查詢視窗。

3. 執行下列預存程序並檢查輸出：

   ```sql
   SELECT * 
   FROM catalog.master_properties
   WHERE property_name = 'EXECUTION_DOMAIN' OR property_name = 'EXECUTION_USER'
   ```

### <a name="clear-domain-credentials"></a>清除網域認證
若要清除並移除本文中所述您所提供的認證，請執行下列動作：

1. 使用 SSMS 或其他工具，連接到裝載 SSISDB 的 Azure SQL Database 伺服器/受控執行個體。 如需詳細資訊，請參閱[連線至 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)。

2. 使用 SSISDB 作為目前的資料庫，開啟查詢視窗。

3. 執行下列預存程序：

   ```sql
   catalog.set_execution_credential @user='', @domain='', @password=''
   ```

## <a name="connect-to-a-sql-server-on-premises"></a>連線到內部部署 SQL Server

若要檢查是否可以連線至內部部署 SQL Server，請執行下列動作：

1. 若要執行這項測試，請尋找未加入網域的電腦。

2. 在未加入網域的電腦上，執行下列命令，以您想要使用的網域認證啟動 SSMS：

   ```cmd
   runas.exe /netonly /user:<domain>\<username> SSMS.exe
   ```

3. 從 SSMS 中，檢查您是否可以連線至內部部署 SQL Server。

### <a name="prerequisites"></a>Prerequisites

若要從在 Azure 中執行的套件存取內部部署 SQL Server，請執行下列動作：

1.  在 SQL Server 組態管理員中，啟用 TCP/IP 通訊協定。

2. 允許通過 Windows 防火牆進行存取。 如需詳細資訊，請參閱[設定 Windows 防火牆以存取 SQL Server](https://docs.microsoft.com/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access)。

3. 將 Azure-SSIS IR 加入連接到本地連接到 SQL 伺服器的 Microsoft Azure 虛擬網路。  有關詳細資訊,請參閱將[Azure-SSIS IR 加入 Microsoft Azure 虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

4. 使用 SSISDB `catalog.set_execution_credential` 預存程序提供本文所述的認證。

## <a name="connect-to-a-file-share-on-premises"></a>連線至內部部署檔案共用

若要檢查是否可以連線至內部部署檔案共用，請執行下列動作：

1. 若要執行這項測試，請尋找未加入網域的電腦。

2. 在未加入網域的電腦上，執行下列命令。 這些命令會使用您要使用的網域認證開啟命令提示字元視窗，然後藉由取得目錄清單來測試與內部部署檔案共用的連線。

   ```cmd
   runas.exe /netonly /user:<domain>\<username> cmd.exe
   dir \\fileshare
   ```

3. 檢查是否會針對內部部署檔案共用傳回目錄清單。

### <a name="prerequisites"></a>Prerequisites

若要從 Azure 中執行的套件存取內部部署檔案共用，請執行下列動作：

1. 允許通過 Windows 防火牆進行存取。

2. 將 Azure-SSIS IR 加入連接到本地檔案共用的 Microsoft Azure 虛擬網路。  有關詳細資訊,請參閱將[Azure-SSIS IR 加入 Microsoft Azure 虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

3. 使用 SSISDB `catalog.set_execution_credential` 預存程序提供本文所述的認證。

## <a name="connect-to-a-file-share-on-azure-vm"></a>連線至 Azure VM 上的檔案共用

若要從 Azure 中執行的套件存取 Azure VM 上的檔案共用，請執行下列動作：

1. 使用 SSMS 或其他工具，連接到裝載 SSISDB 的 Azure SQL Database 伺服器/受控執行個體。 如需詳細資訊，請參閱[連線至 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)。

2. 使用 SSISDB 作為目前的資料庫，開啟查詢視窗。

3. 執行下列預存程序，並提供適當的網域認證：

   ```sql
   catalog.set_execution_credential @domain = N'.', @user = N'username of local account on Azure virtual machine', @password = N'password'
   ```

## <a name="connect-to-a-file-share-in-azure-files"></a>連線至 Azure 檔案中的檔案共用

如需 Azure 檔案的詳細資訊，請參閱 [Azure 檔案](https://azure.microsoft.com/services/storage/files/)。

若要從 Azure 中執行的套件存取 Azure Files 中的檔案共用，請執行下列動作：

1. 使用 SSMS 或其他工具，連接到裝載 SSISDB 的 Azure SQL Database 伺服器/受控執行個體。 如需詳細資訊，請參閱[連線至 Azure 中的 SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)。

2. 使用 SSISDB 作為目前的資料庫，開啟查詢視窗。

3. 執行下列預存程序，並提供適當的網域認證：

   ```sql
   catalog.set_execution_credential @domain = N'Azure', @user = N'<storage-account-name>', @password = N'<storage-account-key>'
   ```

## <a name="next-steps"></a>後續步驟

- 部署您的套件。 如需詳細資訊，請參閱[使用 SSMS 將 SSIS 專案部署到 Azure](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)。
- 執行您的套件。 如需詳細資訊，請參閱[在 Azure 中使用 SSMS 執行 SSIS 套件](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)。
- 排程您的套件。 如需詳細資訊，請參閱[在 Azure 中排程 SSIS 套件](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)。