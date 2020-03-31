---
title: 從 SSDT 執行 SSIS 包
description: 瞭解如何從 SSDT 在 Azure 中執行 SSIS 包。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 5f21623af9b89bbb020063dfb72f7b60e65a6ebe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927713"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>從 SSDT 在 Azure 中執行 SSIS 包
本文介紹 SQL Server 資料工具 （SSDT） 上啟用 Azure 的 SQL 伺服器整合服務 （SSIS） 專案的功能，它允許您在 Azure 資料工廠 （ADF） 中運行 Azure-SSIS 集成運行時 （IR） 上的包。  在提升&移位/遷移到 Azure 或開發要在 Azure 中運行的新 SSIS 包之前，可以使用此功能測試現有 SSIS 包。

使用此功能，您可以創建新的 Azure-SSIS IR 或將現有 IR 附加到 SSIS 專案，然後執行其包。  我們支援在專案部署模型中將正在運行的包部署到 SSIS 目錄 （SSISDB） 中，支援在包部署模型中將運行的包部署到檔案系統/檔共用/Azure 檔中。 

## <a name="prerequisites"></a>Prerequisites
要使用此功能，請[在此處](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下載並安裝最新的 SSDT 與 Visual Studio 的 SSIS 專案擴展，或[在此處](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)作為獨立安裝程式。

## <a name="azure-enable-ssis-projects"></a>啟用 Azure 的 SSIS 專案
在 SSDT 上，可以使用**整合服務專案（啟用 Azure）** 範本創建新啟用 Azure 的 SSIS 專案。

![啟用 Azure 的新 SSIS 專案](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

或者，可以通過按右鍵 SSDT 解決方案資源管理器面板中的專案節點來 Azure 啟用現有 SSIS 專案，以彈出功能表，然後選擇啟用**Azure 的**功能表項目。

![Azure 啟用現有 SSIS 專案](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

啟用 Azure 的現有 SSIS 專案需要將其目標伺服器版本設置為 Azure-SSIS IR 支援的最新版本，當前為**SQL Server 2017，** 因此，如果尚未啟用，將彈出一個對話方塊視窗來執行此操作。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>將啟用 Azure 的專案連接到 Azure 資料工廠中的 SSIS
通過將啟用 Azure 的專案連接到 ADF 中的 SSIS，可以將包上載到 Azure 檔中，並在 Azure-SSIS IR 上運行它們。  為此，請按照以下步驟操作：

1. 按右鍵 SSDT 解決方案資源管理器面板中的專案或**連結 Azure 資源**節點以彈出功能表，並在 Azure**資料工廠功能表項目中選擇"連接到 SSIS"** 以**在 ADF 連接嚮導中啟動 SSIS。**

   ![連接到 ADF 中的 SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. 在**ADF 簡介中的 SSIS**頁面上，查看簡介並按一下"**下一步**"按鈕以繼續。

   ![ADF 簡介中的 SSIS](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. 在**ADF 頁的"選擇 SSIS IR"上**，選擇現有的 ADF 和 Azure-SSIS IR 以運行包或創建新包（如果沒有）。
   - 要選擇現有的 Azure-SSIS IR，請先選擇相關的 Azure 訂閱和 ADF。
   - 如果選擇沒有任何 Azure-SSIS IR 的現有 ADF，請按一下 **"創建 SSIS IR"** 按鈕在 ADF 門戶/應用中創建新的 ADF。
   - 如果選擇沒有任何 ADF 的現有 Azure 訂閱，請按一下 **"創建 SSIS IR"** 按鈕以啟動**集成運行時創建嚮導**，您可以在其中輸入位置和首碼，以便我們代表您自動創建新的 Azure 資源組、資料工廠和 SSIS IR，以以下模式命名：**您的首碼-RG/DF/IR-您的創建時間**。
   
   ![在 ADF 中選擇 SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. 在 **"選擇 Azure 存儲**"頁上，選擇現有 Azure 存儲帳戶以將包上載到 Azure 檔中，或者創建一個新包（如果沒有）。
   - 要選擇現有的 Azure 存儲帳戶，請先選擇相關的 Azure 訂閱。
   - 如果選擇與 Azure-SSIS IR 相同的 Azure 訂閱，而 Azure-SSIS IR 沒有任何 Azure 存儲帳戶，請按一下"**創建 Azure 存儲**"按鈕，以自動在 Azure-SSIS IR 相同的位置代表您創建新訂閱，該訂閱通過合併 Azure-SSIS IR 名稱的首碼及其創建日期而命名。
   - 如果選擇不包含任何 Azure 存儲帳戶的不同 Azure 訂閱，請按一下"創建**Azure 存儲**"按鈕在 Azure 門戶上創建新訂閱。
   
   ![選取 [Azure 儲存體]](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. 按一下"**連接**"按鈕完成連接。  我們將在 SSDT 解決方案資源管理器面板中的 **"連結 Azure 資源**"節點下顯示所選的 Azure-SSIS IR 和 Azure 存儲帳戶。  我們還將刷新 Azure-SSIS IR 的狀態，同時可以通過按右鍵其節點來彈出功能表，然後選擇 **"開始\停止\管理"** 功能表項目來執行此操作，從而對其進行管理。

## <a name="execute-ssis-packages-in-azure"></a>在 Azure 中執行 SSIS 包
### <a name="starting-package-executions"></a>啟動包執行
在 ADF 中將專案連接到 SSIS 後，可以在 Azure-SSIS IR 上執行包。  有兩個選項可以啟動包執行：
-  按一下 SSDT 工具列中的 **"開始"** 按鈕，下拉式功能表並選擇**Azure 功能表項目中的"執行"** 

   ![在 Azure 中執行](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  按右鍵 SSDT 解決方案資源管理器面板中的包節點以彈出功能表並選擇 Azure 功能表**項中的"執行包**"。

   ![在 Azure 中執行包](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> 在 Azure 中執行包需要具有正在運行的 Azure-SSIS IR，因此，如果 Azure-SSIS IR 已停止，將彈出一個對話方塊視窗來啟動它。  排除任何自訂設置時間，此過程應在 5 分鐘內完成，但 Azure-SSIS IR 加入虛擬網路可能需要大約 20 - 30 分鐘。  在 Azure 中執行包後，可以通過按右鍵 SSDT 解決方案資源管理器面板中的節點以彈出功能表，然後選擇 **"開始\停止\管理"** 功能表項目來阻止 Azure-SSIS IR 來管理其運行成本。

### <a name="checking-package-execution-logs"></a>檢查包執行日誌
當您開始執行包時，我們將在 SSDT 的"進度"視窗中格式化並顯示其日誌。  對於長時間運行的包，我們將按分鐘定期更新其日誌。  您可以通過按一下 SSDT 工具列中的 **"停止"** 按鈕來阻止包執行，該按鈕將立即取消它。  您還可以在其通用命名慣例 （UNC） 路徑中暫時找到日誌原始資料： `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`，但我們將在一天后清理它。

### <a name="switching-package-protection-level"></a>切換封裝保護層級
在 Azure 中執行 SSIS 包不支援**加密敏感使用者金鑰**/**加密AllUserKey**保護層級。  因此，如果包配置了這些包，我們將在將包上載到 Azure 檔以在 Azure-SSIS IR 上執行時，分別用隨機生成的密碼將它們切換到**加密敏感密碼**/**加密所有密碼**。

> [!NOTE]
> 如果包包含引用使用**加密敏感與UserKey**/**EncryptAllUserKey**保護層級配置的其他包的執行包任務，則需要在執行包之前手動重新配置這些其他包以分別使用**加密敏感密碼**/**加密AllWithPassword。**

如果您的包已配置了**加密敏感密碼**/**加密所有密碼**保護層級，我們將保持它們不變，但在將包上載到 Azure 檔中以在 Azure-SSIS IR 上執行時，仍將使用隨機生成的密碼。

### <a name="using-package-configuration-file"></a>使用包設定檔
如果在包部署模型中使用包設定檔在運行時更改變數值，我們將會自動將包含包的檔上載到 Azure 檔中，以便在 Azure-SSIS IR 上執行。

### <a name="using-execute-package-task"></a>使用執行包任務
如果包包含引用存儲在本地檔案系統上的其他包的執行包任務，則需要執行以下其他設置：

1. 將其他包上載到連接到專案的同一 Azure 存儲帳戶下的 Azure 檔中，並獲取其新的 UNC 路徑，例如。`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 將執行包任務的檔連線管理員中這些其他包的檔路徑替換為其新的 UNC 路徑
   - 如果運行 SSDT 的電腦無法訪問新的 UNC 路徑，則可以更改檔連線管理員的"屬性"面板上的檔路徑
   - 或者，您可以使用變數進行檔路徑，在運行時分配正確的值

如果包包含引用同一專案中其他包的執行包任務，則無需其他設置。

## <a name="next-steps"></a>後續步驟
從 SSDT 在 Azure 中運行包後，可以在 ADF 管道中部署並運行這些包作為執行 SSIS 包活動，請參閱[在 ADF 管道中運行 SSIS 包活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
