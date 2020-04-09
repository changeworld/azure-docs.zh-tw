---
title: 將本地 SQL 伺服器整合服務 (SSIS) 工作移至 Azure 資料工廠
description: 本文介紹如何使用 SQL 伺服器管理工作室將 SQL 伺服器整合服務 (SSIS) 作業遷移到 Azure 資料工廠管道/活動/觸發器。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/7/2020
ms.openlocfilehash: c65fbd9bbd83db9c7c8ec0e9041d08372243b72f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887641"
---
# <a name="migrate-on-premises-ssis-jobs-to-azure-data-factory"></a>將本地 SSIS 工作移至 Azure 資料工廠

在遷移 SSIS 套件後[,將本地 SQL 伺服器整合服務 (SSIS) 工作負載遷移到 ADF 中的 SSIS](scenario-ssis-migration-overview.md)時,可以透過 SQL 伺服器管理工作室 (SSMS) **SSIS 作業遷移向導**對 SQL Server 代理作業進行批處理遷移,該作業步驟類型為 SQL Server 整合服務包到 Azure 數據工廠 (ADF) 管道/活動/計劃觸發器。

通常,對於具有適用作業步驟類型的選定 SQL 代理作業 **,SSIS 作業遷移嚮導**可以:

- 將本地 SSIS 套件位置映射到包遷移到的位置,SSIS 可在 ADF 中存取這些位置。
    > [!NOTE]
    > 僅支援檔案系統的包位置。
- 將具有適用作業步驟的適用作業遷移到相應的 ADF 資源,如下所示:

|SQL 代理工作物件  |ADF 資源  |注意|
|---------|---------|---------|
|SQL 代理程式|管線     |將產生導管的名稱 *,\<用於工作名稱>*。 <br> <br> 內部建代理作業不適用: <li> SSIS 伺服器維護工作 <li> syspolicy_purge_history <li> collection_set_* <li> mdw_purge_data_* <li> sysutility_*|
|SSIS 工作步驟|執行 SSIS 套件活動|<li> 活動的名稱將為\<>步骤名称。 <li> 作業步驟中使用的代理帳戶將作為此活動的 Windows 身份驗證進行遷移。 <li> 除了在作業步驟中定義的*使用 32 位元執行時*之外,*執行選項*將在遷移中忽略。 <li> 在作業步驟中定義的*驗證*將在遷移中忽略。|
|schedule      |排程觸發程序        |排程名稱>將*\<產生計畫觸發器名稱*。 <br> <br> SQL 代理作業計劃中的以下選項將在移轉中忽略: <li> 第二級間隔。 <li> *當 SQL Server Agent 啟動時自動啟動* <li> *只要 CPU 閒置就啟動* <li> *工作日和**週末*<time zone> <br> 以下是 SQL 代理作業計劃移至 ADF 計劃觸發器後的差異: <li> ADF 計劃觸發器後續運行獨立於前觸發運行的執行狀態。 <li> ADF 計劃觸發器定期配置不同於 SQL 代理作業中的每日頻率。|

- 在本地輸出資料夾中生成 Azure 資源管理員 (ARM) 範本,並直接或以後手動部署到資料工廠。 有關 ADF 資源管理員樣本的詳細資訊,請參閱[Microsoft.DataFactory 資源類型](https://docs.microsoft.com/azure/templates/microso.ft.datafactory/allversions)。

## <a name="prerequisites"></a>Prerequisites

本文中描述的功能需要 SQL Server 管理工作室版本 18.5 或更高版本。 若要取得最新版的 SSMS，請參閱[下載 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)。

## <a name="migrate-ssis-jobs-to-adf"></a>將 SSIS 工作移至 ADF

1. 在 SSMS 中, 在物件資源管理員中,選擇 SQL 伺服器代理,選擇作業,然後右鍵按一下,**並選擇將 SSIS 作業移到 ADF**。
![選單](media/how-to-migrate-ssis-job-ssms/menu.png)

1. 登錄 Azure,選擇 Azure 訂閱、數據工廠和整合式執行時。 Azure 儲存是可選的,如果要遷移的 SSIS 作業具有 SSIS 檔案系統包,則在包位置映射步驟中使用 Azure 儲存。
![選單](media/how-to-migrate-ssis-job-ssms/step1.png)

1. 將 SSIS 作業中的 SSIS 包和設定檔的路徑映射到遷移管道可以存取的目標路徑。 在此映射步驟中,您可以:

    1. 選擇來源資料夾,然後**新增映射**。
    1. 更新源資料夾路徑。 有效路徑是包的資料夾路徑或父資料夾路徑。
    1. 更新目標資料夾路徑。 默認值是預設存儲帳戶的相對路徑,在步驟 1 中選擇。
    1. 使用**映射**刪除選取的對應。
![步驟2](media/how-to-migrate-ssis-job-ssms/step2.png)
![步驟2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 選擇要遷移的適用作業,並配置相應*執行 SSIS 包活動的*設置。

    - *默認設置*,默認情況下適用於所有選定的步驟。 關於每個屬性的詳細資訊,請參閱在套件位置為*檔案系統 (包)* 時[執行 SSIS 套件活動的](how-to-invoke-ssis-package-ssis-activity.md)*「設定」選項卡*。
    ![步驟3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *步驟設置*,配置所選步驟的設置。
        
        **應用程式預設設定**:選擇預設值。 取消選擇以僅配置所選步驟的設置。  
        關於其他屬性的詳細資訊,請參考套件位置為*檔案系統 (套件)*[時執行 SSIS 套件活動的](how-to-invoke-ssis-package-ssis-activity.md)*「設定」選項卡*。
    ![步驟3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. 生成和部署 ARM 範本。
    1. 為遷移的 ADF 管道的 ARM 範本選擇或輸入輸出路徑。 如果不存在,將自動創建資料夾。
    2. 選擇將**ARM 樣本部署到資料工廠**的選項:
        - 預設值未選中。 您可以稍後手動部署生成的 ARM 範本。
        - 選擇該選擇可直接將生成的 ARM 範本部署到數據工廠。
    ![步驟 4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 遷移,然後檢查結果。
![步驟5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>後續步驟

[執行及監控導管](how-to-invoke-ssis-package-ssis-activity.md)
