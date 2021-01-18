---
title: 使用 Azure SQL 受控執行個體代理程式執行 SSIS 套件
description: 瞭解如何使用 Azure SQL 受控執行個體代理程式來執行 SSIS 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: 5f8b18375c517ab5b620b2d6d897f5133b14705d
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556455"
---
# <a name="run-ssis-packages-by-using-azure-sql-managed-instance-agent"></a>使用 Azure SQL 受控執行個體代理程式來執行 SSIS 套件

本文說明如何使用 Azure SQL 受控執行個體代理程式來執行 SQL Server Integration Services 的 (SSIS) 套件。 當您在內部部署環境中使用 SQL Server Agent 來排程 SSIS 套件時，這項功能會提供類似的行為。

您可以使用這項功能，在 SQL 受控執行個體、檔案系統（例如 Azure 檔案儲存體或 Azure SSIS integration runtime 套件存放區）中，執行儲存在 SSISDB 中的 SSIS 封裝。

## <a name="prerequisites"></a>Prerequisites

若要使用這項功能，請 [下載](/sql/ssms/download-sql-server-management-studio-ssms) 並安裝最新的 SQL SERVER MANAGEMENT STUDIO (SSMS) 。 版本支援詳細資料，如下所示：

- 若要在 SSISDB 或檔案系統中執行套件，請安裝 SSMS 18.5 版或更新版本。
- 若要在封裝存放區中執行封裝，請安裝 SSMS 18.6 版或更新版本。

您也需要在 Azure Data Factory 中布 [建 AZURE SSIS 整合運行](./tutorial-deploy-ssis-packages-azure.md) 時間。 它會使用 SQL 受控執行個體作為端點伺服器。

## <a name="run-an-ssis-package-in-ssisdb"></a>在 SSISDB 中執行 SSIS 套件

在此程式中，您會使用 SQL 受控執行個體 Agent 來叫用儲存在 SSISDB 中的 SSIS 封裝。

1. 在最新版本的 SSMS 中，連接到 SQL 受控執行個體。
1. 建立新的代理程式作業和新的作業步驟。 在 [ **SQL Server Agent**] 下，以滑鼠右鍵按一下 [ **作業** ] 資料夾，然後選取 [ **新增作業**]。

   ![建立新代理程式作業的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. 在 [ **新增作業步驟** ] 頁面上，選取 [ **SQL Server Integration Services 封裝** 做為類型。

   ![建立新 SSIS 作業步驟的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. 在 [ **封裝** ] 索引標籤上，選取 [ **SSIS 目錄** ] 作為封裝位置。
1. 由於 SSISDB 是在 SQL 受控執行個體中，因此您不需要指定驗證。
1. 從 SSISDB 指定 SSIS 套件。

   ![具有套件來源類型選取專案的 [封裝] 索引標籤](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. **在 [設定**] 索引標籤上，您可以：
  
   - 請在 **參數** 下指定參數值。
   - 覆寫 **連接管理員** 下的值。
   - 覆寫屬性，然後選擇 [ **Advanced**] 下的記錄層級。

   ![具有套件來源類型選取專案的 [設定] 索引標籤](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. 選取 **[確定]** 以儲存代理程式作業設定。
1. 啟動代理程式作業以執行 SSIS 套件。

## <a name="run-an-ssis-package-in-the-file-system"></a>在檔案系統中執行 SSIS 套件

在此程式中，您會使用 SQL 受控執行個體 Agent 來執行儲存在檔案系統中的 SSIS 封裝。

1. 在最新版本的 SSMS 中，連接到 SQL 受控執行個體。
1. 建立新的代理程式作業和新的作業步驟。 在 [ **SQL Server Agent**] 下，以滑鼠右鍵按一下 [ **作業** ] 資料夾，然後選取 [ **新增作業**]。

   ![建立新代理程式作業的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. 在 [ **新增作業步驟** ] 頁面上，選取 [ **SQL Server Integration Services 封裝** 做為類型。

   ![建立新 SSIS 作業步驟的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. 在 [ **封裝** ] 索引標籤上：

   1. 針對 [ **封裝位置**]，選取 [ **檔案系統**]。

   1. 針對檔案 **來源類型**：

      - 如果您的套件已上傳至 Azure 檔案儲存體，請選取 [ **Azure 檔案共用**]。

        ![檔案來源類型的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

        封裝路徑為 **`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`** 。

        在 [ **封裝檔案存取認證**] 底下，輸入 azure 檔案帳戶名稱和帳戶金鑰來存取 azure 檔案。 網域會設定為 **Azure**。

      - 如果您的套件已上傳至網路共用，請選取 [ **網路共用**]。

        套件路徑是套件檔案的 UNC 路徑，副檔名為 .dtsx。

        輸入對應的網域、使用者名稱和密碼以存取網路共用封裝檔案。
   1. 如果您的套件檔案以密碼加密，請選取 [ **加密密碼** ] 並輸入密碼。
1. 如果您需要執行 SSIS 套件的設定檔，請 **在 [設定** ] 索引標籤上輸入設定檔路徑。
   如果您將設定儲存在 Azure 檔案儲存體中，其設定路徑將會是 **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** 。
1. 在 [ **執行選項** ] 索引標籤上，您可以選擇是否要使用 **Windows 驗證** 或 **32 位運行** 時間來執行 SSIS 封裝。
1. 在 [ **記錄** ] 索引標籤上，您可以選擇記錄路徑和對應的記錄存取認證來儲存記錄檔。 
   根據預設，記錄路徑與封裝資料夾路徑相同，而且記錄存取認證與封裝存取認證相同。
   如果您將記錄儲存在 Azure 檔案儲存體中，您的記錄路徑將會是 **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** 。
1. 在 [ **設定值** ] 索引標籤上，您可以輸入屬性路徑和值來覆寫封裝屬性。

   例如，若要覆寫您的使用者變數值，請以下列格式輸入其路徑： **`\Package.Variables[User::<variable name>].Value`** 。
1. 選取 **[確定]** 以儲存代理程式作業設定。
1. 啟動代理程式作業以執行 SSIS 套件。

## <a name="run-an-ssis-package-in-the-package-store"></a>在封裝存放區中執行 SSIS 套件

在此程式中，您會使用 SQL 受控執行個體 Agent 來執行儲存在 Azure-SSIS IR 封裝存放區中的 SSIS 封裝。

1. 在最新版本的 SSMS 中，連接到 SQL 受控執行個體。
1. 建立新的代理程式作業和新的作業步驟。 在 [ **SQL Server Agent**] 下，以滑鼠右鍵按一下 [ **作業** ] 資料夾，然後選取 [ **新增作業**]。

   ![建立新代理程式作業的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. 在 [ **新增作業步驟** ] 頁面上，選取 [ **SQL Server Integration Services 封裝** 做為類型。

   ![建立新 SSIS 作業步驟的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. 在 [ **封裝** ] 索引標籤上：

   1. 針對 [ **封裝位置**]，選取 [ **封裝存放區**]。

   1. 針對 **套件路徑**：

      封裝路徑為 **`<package store name>\<folder name>\<package name>`** 。

      ![封裝存放區類型的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-package-store.png)

   1. 如果您的套件檔案以密碼加密，請選取 [ **加密密碼** ] 並輸入密碼。
1. 如果您需要執行 SSIS 套件的設定檔，請 **在 [設定** ] 索引標籤上輸入設定檔路徑。
   如果您將設定儲存在 Azure 檔案儲存體中，其設定路徑將會是 **`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`** 。
1. 在 [ **執行選項** ] 索引標籤上，您可以選擇是否要使用 **Windows 驗證** 或 **32 位運行** 時間來執行 SSIS 封裝。
1. 在 [ **記錄** ] 索引標籤上，您可以選擇記錄路徑和對應的記錄存取認證來儲存記錄檔。
   根據預設，記錄路徑與封裝資料夾路徑相同，而且記錄存取認證與封裝存取認證相同。
   如果您將記錄儲存在 Azure 檔案儲存體中，您的記錄路徑將會是 **`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`** 。
1. 在 [ **設定值** ] 索引標籤上，您可以輸入屬性路徑和值來覆寫封裝屬性。

   例如，若要覆寫您的使用者變數值，請以下列格式輸入其路徑： **`\Package.Variables[User::<variable name>].Value`** 。
1. 選取 **[確定]** 以儲存代理程式作業設定。
1. 啟動代理程式作業以執行 SSIS 套件。

## <a name="cancel-ssis-package-execution"></a>取消 SSIS 套件執行

若要從 SQL 受控執行個體 Agent 作業取消封裝執行，請執行下列步驟，而不是直接停止代理程式作業：

1. 從 **msdb.dbo.sys作業** 中尋找您的 SQL 代理程式 **作業**。
1. 使用下列查詢，根據作業識別碼尋找對應的 SSIS **executionId** ：
   ```sql
   select * from '{table for job execution}' where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
   如果您的 SSIS 套件位於 SSISDB 中，請使用 **ssisdb.internal.execution_parameter_values** 作為工作執行的資料表。 如果您的 SSIS 封裝位於檔案系統中，請使用 **ssisdb.internal.execution_parameter_values_noncatalog**。
1. 以滑鼠右鍵按一下 SSISDB 目錄，然後選取 [作用中的 **作業**]。

   ![SSISDB 目錄之快捷方式功能表上的 [作用中作業]](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. 根據 **executionId** 停止對應的操作。

## <a name="next-steps"></a>後續步驟
您也可以使用 Azure Data Factory 來排程 SSIS 封裝。 如需逐步指示，請參閱 [Azure Data Factory 事件觸發](how-to-create-event-trigger.md)程式。