---
title: 將內部部署 SQL Server Integration Services （SSIS）作業遷移至 Azure Data Factory
description: 本文說明如何使用 SQL Server Management Studio，將 SQL Server Integration Services （SSIS）作業遷移至 Azure Data Factory 管線/活動/觸發程式。
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
ms.openlocfilehash: b27fe2abc50396b527e61487acf9797db59c1cce
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627580"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>使用 SSMS 將 SQL Server Agent 作業遷移至 ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

將內部[部署 SQL Server Integration Services （SSIS）工作負載遷移至 ADF 中的 ssis](scenario-ssis-migration-overview.md)時，在 ssis 封裝遷移之後，您可以透過 SQL SERVER MANAGEMENT STUDIO （SSMS） **SSIS 作業遷移嚮導**，將作業步驟類型為 SQL Server Integration Services 套件的 SQL Server Agent 作業進行批次遷移至 Azure Data Factory （ADF）管線/活動/排程觸發程式。

一般來說，對於選取的 SQL 代理程式作業，以及適用的作業步驟類型， **SSIS 作業遷移 Wizard**可以：

- 將內部部署 SSIS 套件位置對應至封裝遷移至其中的位置，這可由 ADF 中的 SSIS 存取。
    > [!NOTE]
    > 僅支援檔案系統的封裝位置。
- 使用適用的作業步驟將適用的作業遷移至對應的 ADF 資源，如下所示：

|SQL 代理程式工作物件  |ADF 資源  |備忘錄|
|---------|---------|---------|
|SQL 代理程式作業|管線     |將會*為\<作業名稱>產生*管線的名稱。 <br> <br> 內建代理程式作業不適用： <li> SSIS 伺服器維護作業 <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|SSIS 作業步驟|執行 SSIS 套件活動|<li> 活動的名稱會是\<> 的步驟名稱。 <li> 作業步驟中使用的 Proxy 帳戶將會遷移為此活動的 Windows 驗證。 <li> 在作業步驟中定義的 [*使用32位運行*時間] 以外的*執行選項*，將會在遷移時遭到忽略。 <li> 在作業步驟中定義的*驗證*將會在遷移時遭到忽略。|
|schedule      |排程觸發程序        |排程*名稱>會產生\<* 排程觸發程式的名稱。 <br> <br> 在 [SQL 代理程式作業排程] 中的下列選項會在遷移中被忽略： <li> 第二層間隔。 <li> *當 SQL Server Agent 啟動時自動啟動* <li> *只要 CPU 閒置就啟動* <li> *工作日*和*週末*<time zone> <br> 以下是將 SQL Agent 作業排程遷移至 ADF 排程觸發程式後的差異： <li> ADF 排程觸發程式後續執行與上一次觸發執行的執行狀態無關。 <li> ADF 排程觸發週期設定與 SQL 代理程式作業中的每日頻率不同。|

- 在本機輸出檔案夾中產生 Azure Resource Manager （ARM）範本，並直接或稍後手動部署至 data factory。 如需 ADF Resource Manager 範本的詳細資訊，請參閱[DataFactory 資源類型](https://docs.microsoft.com/azure/templates/microsoft.datafactory/allversions)。

## <a name="prerequisites"></a>Prerequisites

本文所述的功能需要18.5 或更高版本的 SQL Server Management Studio。 若要取得最新版的 SSMS，請參閱[下載 SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)。

## <a name="migrate-ssis-jobs-to-adf"></a>將 SSIS 作業遷移至 ADF

1. 在 SSMS 的物件總管中，選取 [SQL Server Agent]，選取 [作業]，然後以滑鼠右鍵按一下並選取 [**將 SSIS 作業遷移至 ADF**]。
![下拉式功能表](media/how-to-migrate-ssis-job-ssms/menu.png)

1. 登入 Azure、選取 [Azure 訂用帳戶]、[Data Factory] 和 [Integration Runtime]。 Azure 儲存體是選擇性的，如果要遷移的 SSIS 作業具有 SSIS 檔案系統封裝，則會在封裝位置對應步驟中使用。
![下拉式功能表](media/how-to-migrate-ssis-job-ssms/step1.png)

1. 將 ssis 作業中的 SSIS 封裝和設定檔路徑對應到遷移的管線可以存取的目的地路徑。 在此對應步驟中，您可以：

    1. 選取源資料夾，然後按一下 [**新增對應**]。
    1. 更新源資料夾路徑。 有效的路徑為資料夾路徑或封裝的父資料夾路徑。
    1. 更新目的地資料夾路徑。 預設為預設儲存體帳戶的相對路徑（在步驟1中選取）。
    1. 透過**刪除對應**刪除選取的對應。
![步驟 2](media/how-to-migrate-ssis-job-ssms/step2.png)
![步驟 2-1](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 選取適用的作業來進行遷移，並設定對應的已*執行 SSIS 套件活動*的設定。

    - *預設設定*，預設會套用至所有選取的步驟。 如需每個屬性的詳細資訊，請參閱當封裝位置為*檔案系統（封裝）* 時，[執行 SSIS 套件活動](how-to-invoke-ssis-package-ssis-activity.md)的 [*設定]* 索引標籤。
    ![步驟 3-1](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *步驟設定*，針對選取的步驟進行設定。
        
        套用**預設設定**：預設為已選取。 取消選取此選項，即可針對選取的步驟進行設定。  
        如需其他屬性的詳細資訊，請參閱當封裝位置為*檔案系統（封裝）* 時，[執行 SSIS 套件活動](how-to-invoke-ssis-package-ssis-activity.md)的 [*設定]* 索引標籤。
    ![步驟 3-2](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. 產生並部署 ARM 範本。
    1. 選取或輸入所遷移 ADF 管線之 ARM 範本的輸出路徑。 如果不存在，則會自動建立資料夾。
    2. 選取 [將**ARM 範本部署到您的 data factory**] 選項：
        - 預設值為未選取。 您稍後可以手動部署產生的 ARM 範本。
        - 選取即可將產生的 ARM 範本直接部署到 data factory。
    ![步驟 4](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 遷移，然後檢查結果。
![步驟5](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>後續步驟

[執行和監視管線](how-to-invoke-ssis-package-ssis-activity.md)
