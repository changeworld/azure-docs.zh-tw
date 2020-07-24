---
title: 使用 Azure SSIS 管理封裝 Integration Runtime 封裝存放區
description: 瞭解如何使用 Azure SSIS Integration Runtime 封裝存放區來管理套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/20/2020
ms.openlocfilehash: 6455c186e05fc98b1ec340c152f9b3e5710f1dd5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087897"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>使用 Azure SSIS 管理封裝 Integration Runtime 封裝存放區

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

若要讓 & 將您的內部部署 SQL Server Integration Services （SSIS）工作負載轉移至雲端，您可以在 Azure Data Factory （ADF）中布建 Azure SSIS Integration Runtime （IR）。 如需詳細資訊，請參閱布[建 AZURE SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)。 Azure-SSIS IR 可支援：

- 執行已部署到 SSIS 目錄 (SSISDB) 的套件，此目錄由 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 裝載
- 執行已部署到 Azure SQL 受控執行個體 (套件部署模型) 所裝載檔案系統、Azure 檔案儲存體或 SQL Server 資料庫 (MSDB) 中的套件

當您使用封裝部署模型時，您可以選擇是否要使用套件存放區布建您的 Azure SSIS IR。 它們會在 Azure SQL 受控執行個體所裝載的檔案系統、Azure 檔案儲存體或 MSDB 之上提供套件管理層。 Azure SSIS IR 套件存放區可讓您透過與[舊版 SSIS 套件存放區](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)類似的 SQL SERVER MANAGEMENT STUDIO （SSMS），匯入/匯出/刪除/執行封裝，以及監視/停止執行封裝。 

## <a name="connect-to-azure-ssis-ir"></a>連接到 Azure SSIS IR

布建您的 Azure SSIS IR 之後，您可以連線到它，以流覽其 SSMS 上的封裝存放區。

![連接到 Azure SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

在 SSMS 的 [**物件總管**] 視窗中，選取 [連線] 下拉式功能表中的 [ **Azure SSIS Integration Runtime** **]** 。 接下來，請登入 Azure，然後選取您使用封裝存放區布建的相關訂用帳戶、ADF 和 Azure SSIS IR。 您的 Azure SSIS IR 會出現並顯示 [執行中的**套件**] 和 [已**儲存的封裝**] 節點。 展開 [已**儲存的封裝**] 節點，以查看您的封裝存放區。 展開您的封裝存放區，以查看底下的資料夾和套件。 如果 SSMS 無法自動連接到您的套件存放區，系統可能會要求您輸入其存取認證。 例如，如果您展開 MSDB 頂端的封裝存放區，系統可能會要求您先連接到您的 Azure SQL 受控執行個體。

![連接到 Azure SQL 受控執行個體](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>管理資料夾和套件

連接到 SSMS 上的 Azure SSIS IR 之後，您可以在任何套件存放區、資料夾或套件上按一下滑鼠右鍵，以顯示功能表，然後選取 [**新增資料夾**]、[匯**入封裝**]、[**匯出封裝**]、[**刪除** **] 或 [** 重新整理]。

   ![管理資料夾和套件](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  選取 [**新增資料夾**]，為匯入的封裝建立新的資料夾。

   *  選取 [匯**入封裝**]，將封裝從**檔案系統**、 **SQL Server** （MSDB）或舊版**SSIS 封裝存放區**匯入到您的封裝存放區。

      ![匯入套件](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      視要匯入的來源**套件位置**而定，選取相關的**伺服器** / **驗證類型**，並在必要時輸入存取認證，選取**封裝路徑**，然後輸入新的**封裝名稱**。 匯入封裝時，無法變更其保護層級。 若要變更它，請使用 SQL Server Data Tools （SSDT）或 `dtutil` 命令列公用程式。

      > [!NOTE]
      > 將 SSIS 套件匯入到 Azure SSIS IR 套件存放區只能逐一完成，並只會將它們複製到基礎 MSDB/檔案系統/Azure 檔案儲存體，同時保留其 SQL Server/SSIS 版本。 
      >
      > 由於 Azure SSIS IR 目前的預設相容性層級為140（等於**SQL Server 2017**），因此在其上執行舊版套件會在執行時間將其升級為 SSIS 2017 套件。 不支援執行較高版本的封裝。
      >
      > 此外，由於舊版 SSIS 封裝存放區系結至特定的 SQL Server 版本，而且只有在該版本的 SSMS 上才可存取，因此舊版 SSIS 封裝存放區中的較舊版本套件必須先使用指定的 SSMS 版本匯出至檔案系統，才能使用 SSMS 2019 或更新版本將其匯入至 Azure SSIS IR 封裝存放區。
      >
      > 或者，若要在切換其保護層級時，將多個 SSIS 套件匯入至 Azure SSIS IR 套件存放區，您可以使用[dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017)命令列公用程式，請參閱使用[dtutil 部署多個套件](#deploying-multiple-packages-with-dtutil)。

   *  選取 [**匯出封裝**]，將封裝存放區中的封裝匯出至**檔案系統**、 **SQL Server** （MSDB）或舊版**SSIS 封裝存放區**。

      ![匯出封裝](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      視要匯出的**套件位置**而定，選取相關的**伺服器** / **驗證類型**，如有必要，請輸入存取認證，然後選取**封裝路徑**。 匯出套件時，如果已加密封裝，請先輸入要解密的密碼，然後您可以變更其保護層級，例如避免儲存任何機密資料，或以使用者金鑰或密碼加密或所有資料。

      > [!NOTE]
      > 從 Azure 匯出 SSIS 套件-SSIS IR 套件存放區只能逐一執行，若不切換保護層級，則只會複製它們，同時保留其 SQL Server/SSIS 版本，否則會將它們升級為 SSIS 2019 或更新版本的套件。
      >
      > 由於 Azure SSIS IR 目前的預設相容性層級為140（等於**SQL Server 2017**），因此在其上執行舊版套件會在執行時間將其升級為 SSIS 2017 套件。 不支援執行較高版本的封裝。
      >
      > 或者，若要在切換其保護層級時，從 Azure SSIS IR 套件存放區匯出多個 SSIS 套件，您可以使用[dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017)命令列公用程式，請參閱使用[dtutil 部署多個套件](#deploying-multiple-packages-with-dtutil)。

   *  選取 [**刪除**]，從您的封裝存放區刪除現有的資料夾/套件。

   *  選取 **[** 重新整理] 以顯示封裝存放區中新增的資料夾/套件。

## <a name="execute-packages"></a>執行封裝

連接到 SSMS 上的 Azure SSIS IR 之後，您可以在任何已儲存的套件上按一下滑鼠右鍵，以顯示功能表並選取 [**執行套件**]。  這會開啟 [**執行封裝公用程式**] 對話方塊，您可以在其中將 AZURE SSIS IR 上的套件執行設定為 ADF 管線中的 Execute ssis 套件活動。

![執行封裝公用程式頁面 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![執行封裝公用程式頁面 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

[**執行封裝公用程式**] 對話方塊的 [**一般** **]、[設定]、[****執行選項**] **Settings**和 [**記錄**] 頁面會對應到 [執行 SSIS 套件活動 在這些頁面上，您可以輸入封裝的加密密碼，並存取封裝設定檔的資訊。 您也可以輸入您的封裝執行認證和屬性，以及記錄檔資料夾的存取訊號。  [**執行封裝公用程式**] 對話方塊的 [**設定值**] 頁面會對應到 [執行 SSIS 套件活動] 的 [**屬性覆寫**] 索引標籤，您可以在此輸入要覆寫的現有封裝屬性。 如需詳細資訊，請參閱[在 ADF 管線中將 ssis 套件執行為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

當您選取 [**執行**] 按鈕時，系統會自動產生並觸發具有 [執行 SSIS 套件] 活動的新 ADF 管線。 如果已有相同設定的 ADF 管線存在，則會重新執行，且不會產生新的管線。 「ADF 管線」和「執行 SSIS 套件」活動會分別命名為 `Pipeline_SSMS_YourPackageName_HashString` 和 `Activity_SSMS_YourPackageName` 。

![執行封裝公用程式按鈕](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![執行 SSIS 套件活動](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>監視和停止執行中的封裝

在 SSMS 上連線到您的 Azure SSIS IR 之後，您可以展開 [執行中的**封裝**] 節點，以查看您目前正在執行的套件。  以滑鼠右鍵按一下任何一個功能表，然後選取 [**停止** **] 或 [** 重新整理]。

   ![監視和停止執行中的封裝](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  選取 [**停止**]，以取消目前執行中的 ADF 管線，並以 Execute SSIS 套件活動的形式執行封裝。

   *  選取 **[** 重新整理]，以從您的封裝存放區顯示新執行的封裝。

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>監視 Azure SSIS IR 和編輯封裝存放區

在 SSMS 上連線到您的 Azure SSIS IR 之後，您可以在其上按一下滑鼠右鍵以顯示功能表，然後選取 [**移至 Azure Data Factory 入口網站** **] 或 [** 重新整理]。

   ![前往 ADF 入口網站](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  選取 [**移至 Azure Data Factory 入口網站**] 以開啟 [ADF 監視中樞] 的 [**整合運行**時間] 頁面，您可以在其中監視您的 Azure SSIS IR。 在 [**封裝存放區**] 圖格上，您可以看到附加至您的 AZURE SSIS IR 的封裝存放區數目。  選取該數位會顯示一個視窗，您可以在其中編輯 ADF 連結服務，以儲存您的封裝存放區的存取訊號。

      ![編輯封裝存放區](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  選取 [重新整理] 以顯示封裝存放區中新增的資料夾/**套件，以及**從您的封裝存放區執行封裝。

## <a name="deploying-multiple-packages-with-dtutil"></a>使用 dtutil 部署多個套件

若要讓 & 將您的內部部署 SSIS 工作負載轉移到 ADF 中的 SSIS，同時維持舊版套件部署模型，您必須從檔案系統、由 SQL Server 裝載的 MSDB 或舊版 SSIS 封裝存放區，將套件部署到 Azure SQL 受控執行個體或 Azure SSIS IR 封裝存放區所裝載的 Azure 檔案儲存體、MSDB 中。 同時，如果您尚未這麼做，您也應該將其保護層級從加密的使用者金鑰切換到未加密或加密的密碼。

您可以使用 SQL Server/SSIS 安裝隨附的[dtutil](https://docs.microsoft.com/sql/integration-services/dtutil-utility?view=sql-server-2017)命令列公用程式，以批次方式部署多個套件。 它會系結至特定 SSIS 版本，因此，如果您使用它來部署較低版本的套件，而不需要切換其保護層級，則只會複製它們，同時保留其 SSIS 版本。 如果您使用它來部署它們，並同時切換其保護層級，則會將它們升級為其 SSIS 版本。

 由於 Azure SSIS IR 目前的預設相容性層級為140（等於**SQL Server 2017**），因此在其上執行舊版套件會在執行時間將其升級為 SSIS 2017 套件。 不支援執行較高版本的封裝。

因此，若要避免執行時間升級，請在套件部署模型中部署要在 Azure SSIS IR 上執行的封裝，應使用 SQL Server/SSIS 2017 安裝隨附的 dtutil 2017。 您可以下載並安裝免費的[SQL Server/SSIS 2017 開發人員版本](https://go.microsoft.com/fwlink/?linkid=853016)，以用於此用途。 安裝之後，您可以在此資料夾上找到 dtutil 2017： `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` 。

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>使用 dtutil 將多個封裝從內部部署的檔案系統部署到 Azure 檔案儲存體

 若要將多個封裝從檔案系統部署到 Azure 檔案儲存體並同時切換其保護層級，您可以在命令提示字元中執行下列命令。 請取代您的案例特定的所有字串。
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

若要在批次檔中執行上述命令，請將取代 `%f` 為 `%%f` 。

若要從檔案系統上的舊版 SSIS 封裝存放區，將多個封裝部署到 Azure 檔案儲存體並同時切換其保護層級，您可以使用相同的命令，但將取代 `YourLocalDrive:\...\YourPackageFolder` 為舊版 SSIS 封裝存放區所使用的本機資料夾： `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` 。 例如，如果您的舊版 SSIS 封裝存放區系結至 SQL Server 2016，請移至 `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` 。  您可以 `YourSQLServerDefaultCompatibilityLevel` 從[SQL Server 預設相容性層級的清單](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#arguments)中，尋找的值。

如果您已在 Azure 檔案儲存體上設定 Azure SSIS IR 套件存放區，當您在 SSMS 2019 或更新版本上連線至 Azure SSIS IR 時，您已部署的套件將會出現在其中。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>使用 dtutil 將多個封裝從內部部署的 MSDB 部署到 Azure 中的 MSDB

 若要將 SQL Server 或舊版 SSIS 封裝所裝載的 MSDB 中的多個封裝，部署到 Azure SQL 受控執行個體所裝載的 msdb 中，並同時切換其保護層級，您可以連接到 SSMS 上的 SQL Server，以滑鼠右鍵按一下 `Databases->System Databases->msdb` ssms 的**物件總管**上的節點，以開啟**新的查詢**視窗，並執行下列 t-sql 腳本。 請取代您案例特定的所有字串：  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

若要使用 Azure SQL 受控執行個體的私用/公用端點，請 `YourSQLManagedInstanceEndpoint` 分別將取代為 `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` 。

腳本會為 MSDB 中的所有封裝產生 dtutil 命令列，您可以在其中進行多重選、複製 & 貼上，然後在命令提示字元執行。

![產生 dtutil 命令列](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

如果您已在 MSDB 之上設定 Azure SSIS IR 套件存放區，當您在 SSMS 2019 或更新版本上連線到您的 Azure SSIS IR 時，您已部署的套件將會出現在其中。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>使用 dtutil 將多個封裝從內部部署的 MSDB 部署到 Azure 檔案儲存體

 若要將 SQL Server 或舊版 SSIS 套件存放區的 MSDB 所裝載的多個封裝部署到 Azure 檔案儲存體並同時切換其保護層級，您可以連接到 SSMS 上的 SQL Server，以滑鼠右鍵按一下 `Databases->System Databases->msdb` ssms 的**物件總管**上的節點，以開啟**新的查詢**視窗，並執行下列 t-sql 腳本。 請取代您案例特定的所有字串：  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

腳本會為 MSDB 中的所有封裝產生 dtutil 命令列，您可以在其中進行多重選、複製 & 貼上，然後在命令提示字元執行。

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

如果您已在 Azure 檔案儲存體上設定 Azure SSIS IR 套件存放區，當您在 SSMS 2019 或更新版本上連線至 Azure SSIS IR 時，您已部署的套件將會出現在其中。

## <a name="next-steps"></a>後續步驟

您可以使用 Execute SSIS 套件活動來重新執行/編輯自動產生的 ADF 管線，或在 ADF 入口網站上建立新的。 如需詳細資訊，請參閱[在 ADF 管線中將 ssis 套件執行為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
