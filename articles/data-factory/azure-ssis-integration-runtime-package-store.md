---
title: 使用 Azure-SSIS Integration Runtime 套件存放區管理套件
description: 瞭解如何使用 Azure-SSIS Integration Runtime 套件存放區管理套件。
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
ms.date: 09/29/2020
ms.openlocfilehash: c7fc1a6c6aa29bfbc074bfa797f31ca8ee4e8fec
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556396"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>使用 Azure-SSIS Integration Runtime 套件存放區管理套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

為了增益 & 將您的內部部署 SQL Server Integration Services (SSIS) 工作負載移至雲端，您可以在 Azure-SSIS Integration Runtime (ADF) 中布建 Azure Data Factory (IR) 。 如需詳細資訊，請參閱布建 [Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md)。 Azure-SSIS IR 可支援：

- 執行已部署到 SSIS 目錄 (SSISDB) 的套件，此目錄由 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 裝載
- 執行已部署到 Azure SQL 受控執行個體 (套件部署模型) 所裝載檔案系統、Azure 檔案儲存體或 SQL Server 資料庫 (MSDB) 中的套件

當您使用封裝部署模型時，您可以選擇是否要使用套件存放區來布建您的 Azure-SSIS IR。 它們會在 Azure SQL 受控執行個體所裝載的檔案系統、Azure 檔案儲存體或 MSDB 之上提供封裝管理層。 Azure-SSIS IR 套件存放區可讓您匯入/匯出/刪除/執行封裝，以及透過與 [舊版 SSIS 封裝存放區](/sql/integration-services/service/package-management-ssis-service)) 類似的 SQL SERVER MANAGEMENT STUDIO (SSMS 來監視/停止執行封裝。 

## <a name="connect-to-azure-ssis-ir"></a>連接到 Azure-SSIS IR

布建您的 Azure-SSIS IR 之後，您就可以連線到它，以流覽其在 SSMS 上的套件存放區。

![連接到 Azure-SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

在 SSMS 的 [**物件總管**] 視窗中，選取 [連線 **]** 下拉式功能表中的 [ **Azure-SSIS Integration Runtime** ]。 接著，登入 Azure，然後選取您已使用套件存放區布建的相關訂用帳戶、ADF 和 Azure-SSIS IR。 您的 Azure-SSIS IR 將會顯示在下方執行 **封裝** 和 **儲存的封裝** 節點。 展開 [ **儲存的封裝** ] 節點，以查看下方的封裝存放區。 展開您的封裝存放區，以查看下方的資料夾和套件。 如果 SSMS 無法自動連接到您的套件存放區，系統可能會要求您輸入其存取認證。 例如，如果您在 MSDB 頂端擴充套件存放區，系統可能會要求您先連接到 Azure SQL 受控執行個體。

![連接到 Azure SQL 受控執行個體](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>管理資料夾和套件

連線到 SSMS 上的 Azure-SSIS IR 之後，您可以在任何封裝存放區、資料夾或套件上按一下滑鼠右鍵，以顯示功能表並選取 [**新增資料夾**]、[匯 **入封裝**]、[**匯出封裝**]、[**刪除**] 或 [重新整理 **]。**

   ![管理資料夾和套件](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  選取 [ **新增資料夾** ]，為匯入的封裝建立新的資料夾。

   *  選取 [匯 **入封裝** ] 以將封裝從 **檔案系統**、 **SQL Server** (MSDB) 或舊版 **SSIS 封裝存放區** 匯入至封裝存放區。

      ![匯入套件](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      根據要匯入的來源 **套件位置**，選取相關的 **伺服器** / **驗證類型**、輸入存取認證（如有必要）、選取 **封裝路徑**，然後輸入新的 **封裝名稱**。 匯入封裝時，無法變更其保護層級。 若要變更它，請使用 SQL Server Data Tools (SSDT) 或 `dtutil` 命令列公用程式。

      > [!NOTE]
      > 將 SSIS 套件匯入 Azure-SSIS IR 的封裝存放區只能逐一完成，而且只會將它們複製到基礎 MSDB/檔案系統/Azure 檔案儲存體，同時保留其 SQL Server/SSIS 版本。 
      >
      > 由於 Azure-SSIS IR 目前是以 **SQL Server 2017** 為基礎，因此在其上執行較低版本的套件會在執行時間將它們升級為 SSIS 2017 套件。 不支援執行較高版本的套件。
      >
      > 此外，由於舊版 SSIS 封裝存放區系結至特定的 SQL Server 版本，而且只可在該版本的 SSMS 上存取，舊版 SSIS 封裝存放區中的較低版本套件必須先使用指定的 SSMS 版本匯出至檔案系統，才能匯入至使用 SSMS 2019 或更新版本的 Azure-SSIS IR 封裝存放區。
      >
      > 或者，若要在切換其保護層級時，將多個 SSIS 套件匯入 Azure-SSIS IR 套件存放區，您可以使用 [dtutil](/sql/integration-services/dtutil-utility) 命令列公用程式，請參閱使用 [dtutil 部署多個套件](#deploying-multiple-packages-with-dtutil)。

   *  選取 [ **匯出封裝** ] 將封裝從封裝存放區匯出至 **檔案系統**、 **SQL Server** (MSDB) 或舊版 **SSIS 封裝存放區**。

      ![匯出封裝](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      根據要匯出至的 **封裝位置**，選取相關的 **伺服器** / **驗證類型**，並視需要輸入存取認證，然後選取 **封裝路徑**。 匯出封裝時，如果它們已加密，請先輸入要解密的密碼，然後您可以變更其保護等級，例如避免儲存任何敏感性資料，或使用使用者金鑰或密碼將其加密或所有資料。

      > [!NOTE]
      > 從 Azure-SSIS IR 套件存放區匯出 SSIS 套件只可逐一完成，而不需切換保護層級，就只會複製它們，同時保留其 SQL Server/SSIS 版本，否則會將它們升級為 SSIS 2019 或更新版本的套件。
      >
      > 由於 Azure-SSIS IR 目前是以 **SQL Server 2017** 為基礎，因此在其上執行較低版本的套件會在執行時間將它們升級為 SSIS 2017 套件。 不支援執行較高版本的套件。
      >
      > 或者，若要在切換其保護層級時從 Azure-SSIS IR 套件存放區匯出多個 SSIS 套件，您可以使用 [dtutil](/sql/integration-services/dtutil-utility) 命令列公用程式，請參閱使用 [dtutil 部署多個套件](#deploying-multiple-packages-with-dtutil)。

   *  選取 [ **刪除** ] 以從您的封裝存放區中刪除現有的資料夾/套件。

   *  選取 [重新整理]，在您的封裝存放區中顯示新 **加入的資料夾** /套件。

## <a name="execute-packages"></a>執行封裝

連線到 SSMS 上的 Azure-SSIS IR 之後，您可以用滑鼠右鍵按一下任何已儲存的封裝，以顯示功能表並選取 [ **執行封裝**]。  這會開啟 [ **執行封裝公用程式** ] 對話方塊，您可以在其中設定在 ADF 管線中執行 SSIS 套件活動 Azure-SSIS IR 上的封裝執行。

![執行封裝公用程式頁面 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![執行封裝公用程式第3頁 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**執行封裝公用程式**] 對話方塊的 [**一般**]、[設定 **]、[****執行選項**] 和 [**記錄**] 頁面，會對應到 [執行 SSIS 封裝活動] 的 [**設定**] 在這些頁面上，您可以輸入套件的加密密碼，並存取套件設定檔的資訊。 您也可以輸入套件執行認證和屬性，以及記錄資料夾的存取訊號。  **執行封裝公用程式** 對話方塊的 [**設定值**] 頁面對應到 [執行 SSIS 封裝活動] 的 [**屬性覆寫**] 索引標籤，您可以在其中輸入要覆寫的現有封裝屬性。 如需詳細資訊，請參閱 [在 ADF 管線中將 ssis 套件執行為執行 Ssis 套件活動](./how-to-invoke-ssis-package-ssis-activity.md)。

當您選取 [ **執行** ] 按鈕時，將會自動產生並觸發具有「執行 SSIS 套件」活動的新 ADF 管線。 如果具有相同設定的 ADF 管線已經存在，則會重新執行，且不會產生新的管線。 ADF 管線和執行 SSIS 套件活動會分別命名為 `Pipeline_SSMS_YourPackageName_HashString` 和 `Activity_SSMS_YourPackageName` 。

![執行封裝公用程式按鈕](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![執行 SSIS 套件活動](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>監視和停止執行中的封裝

連線到 SSMS 上的 Azure-SSIS IR 之後，您可以展開 [正在執行的 **封裝** ] 節點，以查看目前正在執行的封裝。  以滑鼠右鍵按一下任何一個功能表，然後選取 [**停止**] 或 [重新整理 **]。**

   ![監視和停止執行中的封裝](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  選取 [ **停止** ] 以取消目前執行中的 ADF 管線，該管線會執行封裝作為執行 SSIS 套件活動。

   *  選取 [重新整理] 以顯示封裝存放區中新 **執行的封裝** 。

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>監視 Azure-SSIS IR 和編輯套件存放區

連線到 SSMS 上的 Azure-SSIS IR 之後，您可以在其上按一下滑鼠右鍵以顯示功能表，然後選取 [ **移至 Azure Data Factory 入口網站** **] 或 [** 重新整理]。

   ![前往 ADF 入口網站](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  選取 [ **移至 Azure Data Factory 入口網站** ] 以開啟 ADF 監視中樞的 [ **整合運行** 時間] 頁面，您可以在其中監視 Azure-SSIS IR。 在 [ **封裝存放區** ] 圖格上，您可以看到附加至 Azure-SSIS IR 的封裝存放區數目。  選取該數位將會顯示一個視窗，您可以在其中編輯儲存封裝存放區存取訊號的 ADF 連結服務。

      ![編輯封裝存放區](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  選取 [重新整理]，在您的封裝存放區中顯示新加入的資料夾/ **套件，並** 從封裝存放區執行套件。

## <a name="deploying-multiple-packages-with-dtutil"></a>使用 dtutil 部署多個套件

為了增益 & 將內部部署 SSIS 工作負載移至 ADF 中的 SSIS，同時維持舊版套件部署模型，您需要將封裝從檔案系統、SQL Server 所裝載的 MSDB 或舊版 SSIS 封裝存放區，部署到 Azure SQL 受控執行個體或 Azure-SSIS IR 封裝存放區所裝載的 Azure 檔案儲存體、MSDB 中。 同時，如果您尚未這麼做，您也應該將使用者金鑰加密的保護層級切換為未加密或密碼加密。

您可以使用 SQL Server/SSIS 安裝隨附的 [dtutil](/sql/integration-services/dtutil-utility) 命令列公用程式，以批次方式部署多個套件。 它會系結至特定 SSIS 版本，因此，如果您使用它來部署較低版本的套件，而不切換其保護層級，則只會複製它們，同時保留其 SSIS 版本。 如果您使用它來部署它們，並同時切換其保護層級，則會將它們升級為 SSIS 版本。

 由於 Azure-SSIS IR 目前是以 **SQL Server 2017** 為基礎，因此在其上執行較低版本的套件會在執行時間將它們升級為 SSIS 2017 套件。 不支援執行較高版本的套件。

因此，若要避免執行時間升級，部署套件以在封裝部署模型的 Azure-SSIS IR 上執行，應使用 SQL Server/SSIS 2017 安裝隨附的 dtutil 2017。 您可以下載並安裝免費的 [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) 以供此用途。 安裝之後，您可以在此資料夾中找到 dtutil 2017： `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` 。

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>使用 dtutil 從內部部署的檔案系統將多個封裝部署到 Azure 檔案儲存體

 若要將多個封裝從檔案系統部署到 Azure 檔案儲存體並同時切換其保護層級，您可以在命令提示字元中執行下列命令。 請取代您案例特有的所有字串。
  
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

若要將多個封裝從檔案系統上的舊版 SSIS 封裝存放區部署到 Azure 檔案儲存體並同時切換其保護層級，您可以使用相同的命令，但將取代 `YourLocalDrive:\...\YourPackageFolder` 為舊版 SSIS 封裝存放區所使用的本機資料夾： `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` 。 例如，如果您的舊版 SSIS 封裝存放區系結至 SQL Server 2016，請移至 `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` 。  您可以從 [SQL Server 預設相容性層級清單](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#arguments)中尋找 `YourSQLServerDefaultCompatibilityLevel` 的值。

如果您已在 Azure 檔案儲存體上設定 Azure-SSIS IR 套件存放區，當您連線到 SSMS 2019 或更新版本上的 Azure-SSIS IR 時，您已部署的套件將會出現在這些存放區中。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>使用 dtutil 將多個封裝從 MSDB 內部部署部署至 Azure 中的 MSDB

 若要從 SQL Server 或舊版 SSIS 套件存放區所裝載的 MSDB 將多個封裝部署至 Azure SQL 受控執行個體所裝載的 MSDB 中，並同時切換其保護層級，您可以連線到 SSMS 上的 SQL Server，以滑鼠右鍵按一下 `Databases->System Databases->msdb` ssms **物件總管** 上的節點，以開啟 **新的查詢** 視窗，然後執行下列 t-sql 腳本。 請取代您案例所特有的所有字串：  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

若要使用 Azure SQL 受控執行個體的私人/公用端點，請將取代 `YourSQLManagedInstanceEndpoint` 為 `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` 。

腳本會為 MSDB 中的所有封裝產生 dtutil 命令列，您可以在其中進行多重選、複製 & 貼上，然後在命令提示字元執行。

![產生 dtutil 命令列](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

如果您已在 MSDB 之上設定 Azure-SSIS IR 套件存放區，當您在 SSMS 2019 或更新版本上連線到您的 Azure-SSIS IR 時，您已部署的套件將會出現在這些存放區中。

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>使用 dtutil 從內部部署的 MSDB 將多個封裝部署到 Azure 檔案儲存體

 若要從 SQL Server 或舊版 SSIS 封裝存放區所裝載的 MSDB 將多個封裝部署到 Azure 檔案儲存體並同時切換其保護層級，您可以連線到 SSMS 上的 SQL Server，以滑鼠右鍵按一下 `Databases->System Databases->msdb` ssms **物件總管** 上的節點，以開啟新的 **查詢** 視窗，然後執行下列 t-sql 腳本。 請取代您案例所特有的所有字串：  
  
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

如果您已在 Azure 檔案儲存體上設定 Azure-SSIS IR 套件存放區，當您連線到 SSMS 2019 或更新版本上的 Azure-SSIS IR 時，您已部署的套件將會出現在這些存放區中。

## <a name="next-steps"></a>後續步驟

您可以使用執行 SSIS 套件活動來重新執行/編輯自動產生的 ADF 管線，或在 ADF 入口網站上建立新的 ADF 管線。 如需詳細資訊，請參閱 [在 ADF 管線中將 ssis 套件執行為執行 Ssis 套件活動](./how-to-invoke-ssis-package-ssis-activity.md)。