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
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84198865"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>使用 Azure SSIS 管理封裝 Integration Runtime 封裝存放區

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

若要讓 & 將您的內部部署 SQL Server Integration Services （SSIS）工作負載轉移至雲端，您可以在 Azure Data Factory （ADF）中布建 Azure SSIS Integration Runtime （IR）。 如需詳細資訊，請參閱布[建 AZURE SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)。 Azure-SSIS IR 可支援：

- 執行已部署到 SSIS 目錄 (SSISDB) 的套件，此目錄由 Azure SQL Database 伺服器/受控執行個體 (專案部署模型) 裝載
- 執行已部署到 Azure SQL 受控執行個體 (套件部署模型) 所裝載檔案系統、Azure 檔案儲存體或 SQL Server 資料庫 (MSDB) 中的套件

當您使用套件部署模型時，您可以選擇是否要使用套件存放區布建您的 Azure SSIS IR，以便在 Azure SQL 受控執行個體所裝載的檔案系統/Azure 檔案儲存體/MSDB 之上提供套件管理層。 Azure SSIS IR 套件存放區可讓您透過與[舊版 SSIS 套件存放區](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)類似的 SQL SERVER MANAGEMENT STUDIO （SSMS），匯入/匯出/刪除/執行封裝，以及監視/停止執行封裝。 

## <a name="connect-to-azure-ssis-ir"></a>連接到 Azure SSIS IR

布建您的 Azure SSIS IR 之後，您可以連線到它，以流覽其 SSMS 上的封裝存放區。

![連接到 Azure SSIS IR](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

在 SSMS 的 [**物件總管**] 視窗中，選取 [連線] 下拉式功能表中的 [ **Azure SSIS Integration Runtime** **]** 。 接下來，請登入 Azure 並選取相關的訂用帳戶、ADF 和 Azure SSIS IR，以連接您的封裝存放區。 您的 Azure SSIS IR 會出現並顯示 [執行中的**套件**] 和 [已**儲存的封裝**] 節點。 展開 [已**儲存的封裝**] 節點，以查看您的封裝存放區。 展開您的封裝存放區，以查看底下的資料夾和套件。 如果 SSMS 無法自動連接到您的套件存放區，系統可能會要求您輸入其存取認證。 例如，如果您展開 MSDB 頂端的封裝存放區，系統可能會要求您先連接到您的 Azure SQL 受控執行個體。

![連接到 Azure SQL 受控執行個體](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>管理資料夾和套件

在 SSMS 上流覽您的 Azure SSIS IR 時，您可以在任何封裝存放區/資料夾/套件上按一下滑鼠右鍵，以顯示功能表，然後選取 [**新增資料夾**]、[匯**入封裝**]、[**匯出封裝**]、[**刪除** **] 或 [** 重新整理]。

   ![管理資料夾和套件](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  選取 [**新增資料夾**]，為匯入的封裝建立新的資料夾。

   *  選取 [匯**入封裝**]，將封裝從**檔案系統**、 **SQL Server** （MSDB）或舊版**SSIS 封裝存放區**匯入到您的封裝存放區。

      ![匯入套件](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      視要匯入的來源**套件位置**而定，選取相關的**伺服器** / **驗證類型**，並在必要時輸入存取認證，選取**封裝路徑**，然後輸入新的**封裝名稱**。 匯入封裝時，無法變更其保護層級。 若要變更它，請使用 SQL Server Data Tools （SSDT）或 `dtutil` 命令列公用程式。

   *  選取 [**匯出封裝**]，將封裝存放區中的封裝匯出至**檔案系統**、 **SQL Server** （MSDB）或舊版**SSIS 封裝存放區**。

      ![匯出封裝](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      視要匯出的**套件位置**而定，選取相關的**伺服器** / **驗證類型**，如有必要，請輸入存取認證，然後選取**封裝路徑**。 匯出套件時，如果它們已加密，請先輸入要解密的密碼，然後再變更其保護層級，例如，不要儲存機密資料，或以使用者金鑰/密碼加密所有/機密資料。

   *  選取 [**刪除**]，從您的封裝存放區刪除現有的資料夾/套件。

   *  選取 **[** 重新整理] 以顯示封裝存放區中新增的資料夾/套件。

## <a name="execute-packages"></a>執行封裝

流覽 SSMS 上的 Azure SSIS IR 時，您可以在任何已儲存的套件上按一下滑鼠右鍵，以顯示功能表並選取 [**執行套件**]。  這會開啟 [**執行封裝公用程式**] 對話方塊，您可以在其中將 AZURE SSIS IR 上的套件執行設定為 ADF 管線中的 Execute ssis 套件活動。

![執行封裝公用程式頁面 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![執行封裝公用程式頁面 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

[**執行封裝公用程式**] 對話方塊的 [**一般** **]、[設定]、[****執行選項**] 和 [**記錄**] 頁面會對應到 [執行 SSIS 套件活動] 的 [**設定**] 索引標籤，您可以在其中輸入封裝的加密密碼、存取封裝設定檔案的資訊、封裝執行認證/屬性，以及記錄檔資料夾的存取訊號。  [**執行封裝公用程式**] 對話方塊的 [**設定值**] 頁面會對應到 [執行 SSIS 套件活動] 的 [**屬性覆寫**] 索引標籤，您可以在此輸入要覆寫的現有封裝屬性。 如需詳細資訊，請參閱[在 ADF 管線中將 ssis 套件執行為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。

當您選取**執行封裝公用程式**] 對話方塊的 [**執行**] 按鈕時，系統會自動產生並觸發具有 [執行 SSIS 套件] 活動的新 ADF 管線。 如果具有相同封裝執行設定的 ADF 管線已經存在，將會重新執行它，而且不會產生新的管線。 「ADF 管線」和「執行 SSIS 套件」活動會分別命名為 `Pipeline_SSMS_YourPackageName_HashString` 和 `Activity_SSMS_YourPackageName` 。

![執行封裝公用程式按鈕](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![執行 SSIS 套件活動](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>監視和停止執行中的封裝

在 SSMS 上流覽您的 Azure SSIS IR 時，您可以展開 [執行中的**封裝**] 節點，以查看目前執行中的套件。  以滑鼠右鍵按一下任何一個功能表，然後選取 [**停止** **] 或 [** 重新整理]。

   ![監視和停止執行中的封裝](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  選取 [**停止**]，以取消目前執行中的 ADF 管線，並以 Execute SSIS 套件活動的形式執行封裝。

   *  選取 **[** 重新整理]，以從您的封裝存放區顯示新執行的封裝。

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>監視 Azure SSIS IR 和編輯封裝存放區

在 SSMS 上流覽您的 Azure SSIS IR 時，您可以在其上按一下滑鼠右鍵以顯示功能表，然後選取 [**移至 Azure Data Factory 入口網站**] 或 [重新整理 **]。**

   ![前往 ADF 入口網站](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  選取 [**移至 Azure Data Factory 入口網站**] 以開啟 [ADF 監視中樞] 的 [**整合運行**時間] 頁面，您可以在其中監視您的 Azure SSIS IR。 在 [**封裝存放區**] 圖格上，您可以看到附加至您的 AZURE SSIS IR 的封裝存放區數目。  選取該數位會顯示一個視窗，您可以在其中編輯 ADF 連結服務，以儲存您的封裝存放區的存取訊號。

      ![編輯封裝存放區](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  選取 [重新整理] 以顯示封裝存放區中新增的資料夾/**套件，以及**從您的封裝存放區執行封裝。

## <a name="next-steps"></a>後續步驟

您可以使用 Execute SSIS 套件活動來重新執行/編輯自動產生的 ADF 管線，或在 ADF 入口網站上建立新的。 如需詳細資訊，請參閱[在 ADF 管線中將 ssis 套件執行為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。