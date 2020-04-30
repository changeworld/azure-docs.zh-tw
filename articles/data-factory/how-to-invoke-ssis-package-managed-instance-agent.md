---
title: 使用 Azure SQL Database 受控執行個體代理程式執行 SSIS 套件
description: 瞭解如何使用 Azure SQL Database 受控執行個體代理程式來執行 SSIS 封裝。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: fcbfeb5ab3a3a80fdb8f7e355f290451d4afe804
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144810"
---
# <a name="run-ssis-packages-by-using-azure-sql-database-managed-instance-agent"></a>使用 Azure SQL Database 受控執行個體代理程式執行 SSIS 套件
本文說明如何使用 Azure SQL Database 受控執行個體 Agent 來執行 SQL Server Integration Services （SSIS）封裝。 當您使用內部部署環境中的 SQL Server Agent 來排定 SSIS 封裝時，此功能提供的行為類似。

有了這項功能，您就可以在 Azure SQL Database 受控實例或檔案系統（例如 Azure 檔案儲存體）中，執行儲存在 SSISDB 中的 SSIS 封裝。

## <a name="prerequisites"></a>先決條件
若要使用這項功能，請[下載](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)並安裝最新版本的 SQL SERVER MANAGEMENT STUDIO （SSMS），也就是18.5 版。

您也需要在 Azure Data Factory 中布[建 AZURE SSIS 整合運行](tutorial-create-azure-ssis-runtime-portal.md)時間。 它會使用 Azure SQL Database 受控實例作為端點伺服器。 

## <a name="run-an-ssis-package-in-ssisdb"></a>在 SSISDB 中執行 SSIS 套件
在此程式中，您會使用 Azure SQL Database 受控執行個體 Agent 來叫用儲存在 SSISDB 中的 SSIS 封裝。

1. 在最新版本的 SSMS 中，連接到 Azure SQL Database 受控實例。
1. 建立新的代理程式作業和新的作業步驟。 在 [ **SQL Server Agent**] 底下，以滑鼠右鍵按一下 [**作業**] 資料夾，然後選取 [**新增作業**]。

   ![建立新代理程式作業的選取專案](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. 在 [**新增作業步驟**] 頁面上，選取 [ **SQL Server Integration Services 封裝**] 作為類型。

   ![建立新 SSIS 作業步驟的選取專案](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. 在 [**封裝**] 索引標籤上，選取 [ **SSIS 目錄**] 作為 [套件來源類型]。
1. 因為 SSISDB 位於 Azure SQL Database 受控實例中，所以您不需要指定驗證。
1. 從 SSISDB 指定 SSIS 封裝。

   ![套件來源類型選項的套件索引標籤](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

1. **在 [設定**] 索引標籤上，您可以：
  
   - 在 [**參數**] 下指定參數值。
   - 覆寫 [**連接管理員**] 底下的值。
   - 覆寫屬性，並選擇 [ **Advanced**] 底下的記錄層級。

   ![具有套件來源類型選項的設定索引標籤](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

1. 選取 **[確定]** 以儲存代理程式作業設定。
1. 啟動 agent 作業以執行 SSIS 封裝。


## <a name="run-an-ssis-package-in-the-file-system"></a>在檔案系統中執行 SSIS 套件
在此程式中，您會使用 Azure SQL Database 受控執行個體 Agent 來執行儲存在檔案系統中的 SSIS 封裝。

1. 在最新版本的 SSMS 中，連接到 Azure SQL Database 受控實例。
1. 建立新的代理程式作業和新的作業步驟。 在 [ **SQL Server Agent**] 底下，以滑鼠右鍵按一下 [**作業**] 資料夾，然後選取 [**新增作業**]。

   ![建立新代理程式作業的選取專案](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

1. 在 [**新增作業步驟**] 頁面上，選取 [ **SQL Server Integration Services 封裝**] 作為類型。

   ![建立新 SSIS 作業步驟的選取專案](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

1. 在 [**封裝**] 索引標籤上：

   1. 針對 [**套件來源**]，選取 [**檔案系統**]。
   
   1. 針對 [檔案**來源類型**]：   

      - 如果您的套件已上傳至 Azure 檔案儲存體，請選取 [ **Azure 檔案共用**]。

        ![檔案來源類型的選項](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)
      
        封裝路徑為** \\ <storage account name>. file.core.windows.net\<檔案共用名稱\<>封裝名稱>. .dtsx**。
      
        在 [**套件檔案存取認證**] 底下，輸入 azure 檔案帳戶名稱和帳戶金鑰以存取 azure 檔案。 網域已設定為**Azure**。

      - 如果您的套件已上傳到網路共用，請選取 [**網路共用**]。
      
        封裝路徑是封裝檔案的 UNC 路徑，副檔名為 .dtsx。
      
        輸入對應的 [網域]、[使用者名稱] 和 [密碼]，以存取網路共用封裝檔案。
   1. 如果您的套件檔案是使用密碼進行加密，請選取 [**加密密碼**] 並輸入密碼。
1. 如果您需要設定檔來執行 SSIS**套件，請在 [設定**] 索引標籤上，輸入設定檔路徑。
1. 在 [**執行選項**] 索引標籤上，您可以選擇是否要使用**Windows 驗證**或**32 位運行**時間來執行 SSIS 封裝。
1. 在 [**記錄**] 索引標籤上，您可以選擇記錄路徑和對應的記錄存取認證來儲存記錄檔。 根據預設，記錄路徑與封裝資料夾路徑相同，而且記錄存取認證與封裝存取認證相同。
1. 在 [**設定值**] 索引標籤上，您可以輸入屬性路徑和值來覆寫封裝屬性。
 
   例如，若要覆寫使用者變數的值，請以下列格式輸入其路徑： **\Package.Variables [user：<variable name>：]。值**。
1. 選取 **[確定]** 以儲存代理程式作業設定。
1. 啟動 agent 作業以執行 SSIS 封裝。


## <a name="cancel-ssis-package-execution"></a>取消 SSIS 封裝執行
若要取消 Azure SQL Database 受控執行個體 Agent 作業的封裝執行，請採取下列步驟，而不是直接停止 agent 作業：

1. 從**sysjobs**尋找您的 SQL 代理程式**jobId** 。
1. 使用下列查詢，根據作業識別碼尋找對應的 SSIS **executionId** ：
   ```sql
   select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
   ```
1. 以滑鼠右鍵按一下 [SSISDB] 目錄，然後選取 [作用中**作業**]。

   ![SSISDB 目錄之快捷方式功能表上的 [作用中作業]](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

1. 根據**executionId**停止對應的作業。

## <a name="next-steps"></a>後續步驟
您也可以使用 Azure Data Factory 來排程 SSIS 封裝。 如需逐步指示，請參閱[Azure Data Factory 事件觸發](how-to-create-event-trigger.md)程式。 