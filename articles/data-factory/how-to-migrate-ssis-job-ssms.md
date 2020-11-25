---
title: 將內部部署 SQL Server Integration Services (SSIS) 作業遷移至 Azure Data Factory
description: 本文描述如何使用 SQL Server Management Studio，將 SQL Server Integration Services (SSIS) 作業遷移至 Azure Data Factory 管線/活動/觸發程式。
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
ms.openlocfilehash: 5566717387f6da375129a0e70c9ad825198d66b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005702"
---
# <a name="migrate-sql-server-agent-jobs-to-adf-with-ssms"></a>使用 SSMS 將 SQL Server Agent 作業遷移至 ADF

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

當您將內部 [部署 SQL Server Integration Services (ssis) 工作負載遷移至 ADF 中的 ssis](scenario-ssis-migration-overview.md)時，遷移 ssis 封裝之後，您可以使用 SQL Server Integration Services 封裝的作業步驟類型來執行 SQL Server Agent 作業的批次遷移，以 AZURE DATA FACTORY (SSMS) **SSIS 作業遷移 WIZARD** SQL Server Management Studio (ADF) 管線/活動/排程觸發程式。

一般情況下，針對具有適用作業步驟類型的已選取 SQL agent 作業， **SSIS 作業遷移嚮導** 可以：

- 將內部部署 SSIS 套件位置對應到要將套件遷移至其中的位置，這可由 ADF 中的 SSIS 來存取。
    > [!NOTE]
    > 僅支援檔案系統的封裝位置。
- 使用適用的作業步驟將適用的作業遷移至對應的 ADF 資源，如下所示：

|SQL Agent 工作物件  |ADF 資源  |注意|
|---------|---------|---------|
|SQL Agent 作業|管線     |將 *產生 \<job name>* 管線的名稱。 <br> <br> 內建的代理程式作業不適用： <li> SSIS 伺服器維護作業 <li> syspolicy_purge_history <li> collection_set_ * <li> mdw_purge_data_ * <li> sysutility_ *|
|SSIS 作業步驟|執行 SSIS 套件活動|<li> 活動的名稱將是 \<step name> 。 <li> 在作業步驟中使用的 Proxy 帳戶將會遷移為此活動的 Windows 驗證。 <li> 在作業步驟中定義的 *使用32位運行* 時間以外的 *執行選項* 會在遷移時忽略。 <li> 在作業步驟中定義的 *驗證* 將會在遷移時忽略。|
|schedule      |排程觸發程序        |將產生排程觸發 *程式 \<schedule name>* 的名稱。 <br> <br> 下列 SQL Agent 作業排程中的選項將在遷移時忽略： <li> 第二層間隔。 <li> *當 SQL Server Agent 啟動時自動啟動* <li> *只要 CPU 閒置就啟動* <li> *工作日* 與 *週末*<time zone> <br> 以下是將 SQL Agent 作業排程遷移至 ADF 排程觸發程式之後的差異： <li> ADF 排程觸發程式的後續執行與之前觸發執行的執行狀態無關。 <li> ADF 排程觸發程式迴圈設定與 SQL 代理程式作業中的每日頻率不同。|

- 在本機輸出檔案夾中產生 Azure Resource Manager (ARM) 範本，並以手動方式或稍後手動部署至 data factory。 如需 ADF Resource Manager 範本的詳細資訊，請參閱 [DataFactory 資源類型](/azure/templates/microsoft.datafactory/allversions)。

## <a name="prerequisites"></a>Prerequisites

本文所述的功能需要 SQL Server Management Studio 18.5 版或更高版本。 若要取得最新版的 SSMS，請參閱[下載 SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15)。

## <a name="migrate-ssis-jobs-to-adf"></a>將 SSIS 作業遷移至 ADF

1. 在 SSMS 的物件總管中，選取 [SQL Server Agent]，選取 [作業]，然後以滑鼠右鍵按一下並選取 [ **將 SSIS 作業遷移至 ADF**]。
![螢幕擷取畫面顯示 SQL Server Management Studio 物件總管，您可以在其中選取作業，然後將 S I S 的作業遷移至 D F。](media/how-to-migrate-ssis-job-ssms/menu.png)

1. 登入 Azure，並選取 Azure 訂用帳戶、Data Factory 和 Integration Runtime。 Azure 儲存體是選擇性的，如果要遷移的 SSIS 作業具有 SSIS 檔案系統封裝，則會在封裝位置對應步驟中使用。
![功能表](media/how-to-migrate-ssis-job-ssms/step1.png)

1. 將 ssis 作業中的 SSIS 封裝和設定檔路徑，對應至遷移的管線可以存取的目的地路徑。 在此對應步驟中，您可以：

    1. 選取源資料夾，然後 **加入對應**。
    1. 更新源資料夾路徑。 有效的路徑是資料夾路徑或封裝的上層資料夾路徑。
    1. 更新目的地資料夾路徑。 預設值是在步驟1中選取的預設儲存體帳戶的相對路徑。
    1. 透過 **刪除對應** 來刪除選取的對應。
![螢幕擷取畫面顯示 [地圖 S s 封裝和設定路徑] 頁面，您可以在其中新增對應。 ](media/how-to-migrate-ssis-job-ssms/step2.png)
 ![螢幕擷取畫面顯示 [對應 S s 封裝和設定路徑] 頁面，您可以在其中更新來源和目的地資料夾路徑。](media/how-to-migrate-ssis-job-ssms/step2-1.png)

1. 選取要遷移的適用作業，並設定對應的已 *執行 SSIS 套件活動* 的設定。

    - *預設設定*，預設會套用至所有選取的步驟。 如需每個屬性的詳細資訊，請參閱當封裝位置是 *檔案系統 (封裝* 時，[執行 SSIS 套件活動](how-to-invoke-ssis-package-ssis-activity.md)的 [*設定]* 索引標籤) 。
    ![螢幕擷取畫面顯示 [選取我的工作] 頁面，您可以在其中設定對應的已執行 SSIS 套件活動的設定。](media/how-to-migrate-ssis-job-ssms/step3-1.png)
    - *步驟設定*，設定所選步驟的設定。
        
        套用 **預設設定**：預設為已選取。 取消選取以設定所選步驟的設定。  
        如需其他屬性的詳細資訊，請參閱當封裝位置是 *檔案系統 (封裝* 時，[執行 SSIS 套件活動](how-to-invoke-ssis-package-ssis-activity.md)的 [*設定]* 索引標籤) 。
    ![螢幕擷取畫面顯示 [選取我的工作] 頁面，您可以在其中套用預設設定。](media/how-to-migrate-ssis-job-ssms/step3-2.png)

1. 產生和部署 ARM 範本。
    1. 針對已遷移的 ADF 管線，選取或輸入 ARM 範本的輸出路徑。 如果不存在，則會自動建立資料夾。
    2. 選取 [將 **ARM 範本部署到您的 data factory**] 選項：
        - 預設為未選取。 您可以稍後手動部署產生的 ARM 範本。
        - 選取即可直接將產生的 ARM 範本部署至 data factory。
    ![螢幕擷取畫面顯示 [設定遷移] 頁面，您可以在其中選取或輸入已遷移 ADF 管線的 ARM 範本輸出路徑，並選取 [將 ARM 範本部署至您的資料處理站] 選項。](media/how-to-migrate-ssis-job-ssms/step4.png)

1. 遷移，然後檢查結果。
![螢幕擷取畫面顯示 [遷移結果] 頁面，其中顯示遷移進度。](media/how-to-migrate-ssis-job-ssms/step5.png)

## <a name="next-steps"></a>後續步驟

[執行及監視管線](how-to-invoke-ssis-package-ssis-activity.md)