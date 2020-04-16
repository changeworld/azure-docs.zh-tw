---
title: 依 Azure SQL 託管實體代理化 SSIS 套件
description: 瞭解如何按 Azure SQL 託管實例代理執行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394718"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>依 Azure SQL 託管實體代理化 SSIS 套件
本文介紹如何使用 Azure SQL 託管實例代理運行 SQL 伺服器整合服務 (SSIS) 包。 此功能提供類似行為,就像在預環境中按 SQL Server 代理計畫 SSIS 包一樣。

使用此功能,可以運行存儲在 Azure SQL 託管實例或檔案系統(如 Azure 檔)SSISDB 中的 SSIS 套件。

## <a name="prerequisites"></a>Prerequisites
要使用此功能,請下載並安裝最新版本的 SSMS,即版本 18.5 或更高版本。 請從[此網站](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下載。

還需要在 Azure 資料工廠中預配 Azure-SSIS 整合執行時,該執行時使用 Azure SQL 託管實例作為終結點伺服器。 如果尚未預配它,請按照[教程](tutorial-create-azure-ssis-runtime-portal.md)中的說明進行預配。 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>依 Azure SQL 託管實體代理在 SSISDB 中執行 SSIS 套件
在此步驟中,使用 Azure SQL 託管實例代理調用儲存在 Azure SQL 託管實例中的 SSISDB 中的 SSIS 包。
1. 在最新版本的 SSMS 中,連接到 Azure SQL 託管實例。
2. 創建新的代理作業和新的作業步驟。

![新增代理程式](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. 在 **「新建作業步驟」** 頁中,選擇**SQL 伺服器整合服務套件**類型。

![新的 SSIS 工作步驟](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. 在 **「包」** 選項卡中,選擇**SSIS 目錄**作為套件源類型。
5. 由於 SSISDB 位於同一 Azure SQL 託管實例中,因此無需指定身份驗證。
6. 從 SSISDB 指定 SSIS 包。

![套件來源型態 - SSIS 目錄](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. 在 **「設定」** 選項卡中,可以指定**參數**值、覆**寫連線管理員**中的值、覆**寫 屬性**並選擇**紀錄紀錄等級**。

![套件來源型態 - SSIS 目錄設定](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. 完成上述所有配置后,按兩下 **「確定」** 以保存代理作業配置。
9. 啟動代理作業以執行 SSIS 包。


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>以 Azure SQL 管理管實體代理在檔案系統中執行 SSIS 套件
在此步驟中,您可以使用 Azure SQL 託管實例代理呼叫儲存在檔案系統中執行的 SSIS 套件。
1. 在最新版本的 SSMS 中,連接到 Azure SQL 託管實例。
2. 創建新的代理作業和新的作業步驟。

   ![新增代理程式](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. 在 **「新建作業步驟」** 頁中,選擇**SQL 伺服器整合服務套件**類型。

   ![新的 SSIS 工作步驟](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. 在 **「包」** 選項卡中,選擇**檔案系統**作為套件源類型。

   ![套件來源型態 - 檔案系統](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. 如果包上載到 Azure 檔,請選擇**Azure 檔共享**作為檔案源類型。
      - 包路徑是**\\<storage account name>.file.core.windows.net\<檔案\<共用名>包名称>.dtsx**
      - 在**包檔案存取認證中**鍵入 Azure 檔案帳戶名稱和帳戶金鑰以存取 Azure 檔。 網域設定為**Azure**。
   2. 如果包上載到網路共用,請選擇 **「網路共享**」作為檔案源類型。
      - 包路徑是包檔的**UNC 路徑**及其 dtsx 副檔名。
      - 鍵入相應的**網域**、**使用者名稱**和**密碼**以存取網路共享包檔。
   3. 如果您的套件檔使用密碼加密,請選擇 **「加密密碼**」並鍵入密碼。

 5. 在 **「設定」** 選項卡中,如果需要設定檔來執行 SSIS 套件,請鍵入**設定檔案路徑**。
 6. 在 **「執行」選項**選項卡中,您可以選擇是使用**視窗身份驗證**還是**使用 32 位元執行時**來執行 SSIS 套件。
 7. 在 **「日誌記錄」** 選項卡中,您可以選擇**日誌記錄路徑**和相應的紀錄記錄存取認證檔。 默認情況下,日誌記錄路徑將與包資料夾路徑相同,日誌記錄訪問憑據將與包訪問憑據相同。
 8. 在 **「設定值」** 選項卡中,可以在**屬性路徑**和**值**中鍵入以覆蓋包屬性。
 例如,要覆蓋使用者變數的值,請輸入其路徑,其格式為以下: **\Package.變數[使用者::<variable name>]數值**。
 9. 完成上述所有配置后,按兩下 **「確定」** 以保存代理作業配置。
 10. 啟動代理作業以執行 SSIS 包。


 ## <a name="cancel-ssis-package-execution"></a>取消 SSIS 套件執行
 要取消 Azure SQL 託管代理作業中的包執行,應遵循以下步驟,而不是直接停止代理作業。
 1. 從**msdb.dbo.sysjobs**尋找 SQL 代理**作業 ID。**
 2. 依作業識別碼尋找 SSIS**執行識別碼,** 透過以下查詢:
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. 在 SSIS 目錄下選擇 **「活動操作**」。

    ![套件來源型態 - 檔案系統](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. 停止基於**執行ID的**相應操作。

## <a name="next-steps"></a>後續步驟
 您還可以使用 Azure 數據工廠計畫 SSIS 包。 有關分步說明,請參閱[Azure 資料工廠事件觸發器](how-to-create-event-trigger.md)。 