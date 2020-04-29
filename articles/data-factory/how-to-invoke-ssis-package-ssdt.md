---
title: 從 SSDT 執行 SSIS 套件
description: 瞭解如何從 SSDT 在 Azure 中執行 SSIS 套件。
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
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399435"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>從 SSDT 在 Azure 中執行 SSIS 套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明 SQL Server Data Tools （SSDT）上啟用 Azure 的 SQL Server Integration Services （SSIS）專案的功能，可讓您在 Azure Data Factory （ADF）中的 Azure SSIS Integration Runtime （IR）上執行封裝。  您可以使用這項功能來測試現有的 SSIS 套件，然後再將 & 轉移/遷移至 Azure，或開發新的 SSIS 套件以在 Azure 中執行。

透過這項功能，您可以建立新的 Azure SSIS IR，或將現有的整合到 SSIS 專案，然後在其上執行您的封裝。  我們支援執行封裝，以部署至專案部署模型中的 SSIS 目錄（SSISDB），以及要部署到封裝部署模型中的檔案系統/檔案共用/Azure 檔案儲存體。 

## <a name="prerequisites"></a>先決條件
若要使用這項功能，請下載並安裝最新的 SSDT，其中包含 SSIS 專案延伸模組，適用于[此處](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)的 Visual Studio 或獨立安裝[程式。](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)

## <a name="azure-enable-ssis-projects"></a>Azure-啟用 SSIS 專案
在 SSDT 上，您可以使用**Integration Services 專案（Azure 啟用）** 範本來建立啟用 Azure 的新 SSIS 專案。

![啟用 Azure 的新 SSIS 專案](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

或者，您也可以在 SSDT 的 [方案總管] 面板中，以滑鼠右鍵按一下專案節點，然後選取 [**啟用 azure** ] 功能表項目，以啟用現有的 SSIS 專案。

![Azure-啟用現有的 SSIS 專案](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

Azure-若要啟用現有的 SSIS 專案，您必須將其目標伺服器版本設定為 Azure SSIS IR 支援的最新版本（目前**SQL Server 2017**），如果您尚未這麼做，則會顯示對話方塊視窗來執行這項操作。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>將已啟用 Azure 的專案連接到 Azure Data Factory 中的 SSIS
藉由將已啟用 Azure 的專案連接到 ADF 中的 SSIS，您可以將套件上傳至 Azure 檔案儲存體，並在 Azure SSIS IR 上執行。  若要這麼做，請遵循下列步驟：

1. 在 SSDT 的方案總管面板中，以滑鼠右鍵按一下專案或連結的 [ **Azure 資源**] 節點，以顯示功能表，然後選取 [在 Azure Data Factory] 功能表項目中的 [連線**到 ssis]** ，以**在 [ADF 連接嚮導] 中啟動 ssis**。

   ![連接至 ADF 中的 SSIS](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. 在 ADF 的 [**簡介**] 頁面中，檢查簡介，然後按 [**下一步]** 按鈕繼續進行。

   ![ADF 中的 SSIS 簡介](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. 在 [**選取 adf 中的 SSIS IR** ] 頁面上，選取您現有的 ADF 和 AZURE SSIS ir 來執行封裝，或建立新的（如果沒有的話）。
   - 若要選取現有的 Azure SSIS IR，請先選取相關的 Azure 訂用帳戶和 ADF。
   - 如果您選取現有的 ADF，但沒有任何 Azure SSIS IR，請按一下 [**建立 SSIS ir** ] 按鈕，在 adf 入口網站/應用程式上建立新的。
   - 如果您選取不具有任何 ADF 的現有 Azure 訂用帳戶，請按一下 [**建立 SSIS IR** ] 按鈕以啟動 [ **Integration Runtime 建立嚮導]**，您可以在此輸入位置和前置詞，以代表您自動建立新的 azure 資源群組、Data Factory 和 SSIS ir，並以下列模式命名： **YourPrefix-RG/DF/IR-YourCreationTime**。
   
   ![選取 ADF 中的 SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. 在 [**選取 Azure 儲存體**] 頁面上，選取您現有的 Azure 儲存體帳戶，將封裝上傳至 Azure 檔案儲存體或建立新的套件（如果您沒有任何）。
   - 若要選取現有的 Azure 儲存體帳戶，請先選取相關的 Azure 訂用帳戶。
   - 如果您選取的 Azure 訂用帳戶與您的 Azure SSIS IR 沒有任何 Azure 儲存體帳戶，請按一下 [**建立 Azure 儲存體**] 按鈕，讓我們在與您的 AZURE ssis ir 相同的位置中，自動建立新的訂閱，方法是結合您的 AZURE ssis ir 名稱和其建立日期的前置詞來命名。
   - 如果您選取不同的 Azure 訂用帳戶，但沒有任何 Azure 儲存體帳戶，請按一下 [**建立 Azure 儲存體**] 按鈕，在 Azure 入口網站上建立一個新的。
   
   ![選取 [Azure 儲存體]](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. 按一下 [連線 **]** 按鈕以完成連接。  我們會在 SSDT 方案總管面板中的 [**連結的 Azure 資源**] 節點底下，顯示您選取的 AZURE SSIS IR 和 Azure 儲存體帳戶。  我們也會重新整理您的 Azure SSIS IR 的狀態，您可以在節點上按一下滑鼠右鍵以顯示功能表，然後選取 [ **Start\Stop\Manage** ] 功能表項目，將您帶到 ADF 入口網站/應用程式來進行管理。

## <a name="execute-ssis-packages-in-azure"></a>在 Azure 中執行 SSIS 套件
### <a name="starting-package-executions"></a>啟動封裝執行
將您的專案連接到 ADF 中的 SSIS 之後，您可以在 Azure SSIS IR 上執行封裝。  您有兩個選項可啟動封裝執行：
-  按一下 [SSDT] 工具列中的 [**開始**] 按鈕，以下拉式功能表並選取 [**在 Azure 中執行**] 功能表項目 

   ![在 Azure 中執行](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  以滑鼠右鍵按一下 SSDT 方案總管面板中的 [套件] 節點，以彈出功能表並選取 [**在 Azure 中執行封裝**] 功能表項目。

   ![在 Azure 中執行套件](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> 在 Azure 中執行您的套件時，您必須擁有執行中的 Azure SSIS IR，因此如果您的 Azure SSIS IR 已停止，將會顯示對話方塊視窗來啟動它。  排除任何自訂的安裝時間，此程式應在5分鐘內完成，但 Azure SSIS IR 加入虛擬網路可能需要大約 20-30 分鐘。  在 Azure 中執行套件之後，您可以在 SSDT 的方案總管面板中，以滑鼠右鍵按一下其節點，以停止您的 Azure SSIS IR 來管理其執行成本，然後選取 [ **Start\Stop\Manage** ] 功能表項目，以將您帶到 ADF 入口網站/應用程式。

### <a name="checking-package-execution-logs"></a>檢查封裝執行記錄
當您啟動封裝執行時，我們會在 SSDT 的 [進度] 視窗中格式化並顯示其記錄。  針對長時間執行的封裝，我們會定期更新其記錄檔（以分鐘為單位）。  您可以按一下 [SSDT] 工具列中的 [**停止**] 按鈕，立即將它取消，以停止封裝執行。  您也可以暫時尋找其通用命名慣例（UNC）路徑中的記錄原始資料： `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`，但我們會在一天后將它清除。

### <a name="switching-package-protection-level"></a>正在切換套件保護層級
在 Azure 中執行 SSIS 套件不支援**EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey**保護層級。  因此，如果您的套件是以這些專案進行設定，我們會暫時將它們分別切換至**EncryptSensitiveWithPassword**/**EncryptAllWithPassword**，並在我們將套件上傳至 Azure 檔案儲存體以在您的 Azure SSIS IR 上執行時，使用隨機產生的密碼。

> [!NOTE]
> 如果您的套件包含的「執行封裝」工作參考到以**EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey**保護層級設定的其他套件，您必須在執行封裝之前，手動將其他套件重新設定為使用**EncryptSensitiveWithPassword**/**EncryptAllWithPassword**。

如果您的套件已設定**EncryptSensitiveWithPassword**/**EncryptAllWithPassword**保護層級，我們會將其保持不變，但當我們將套件上傳到 Azure 檔案儲存體以在您的 Azure SSIS IR 上執行時，仍會使用隨機產生的密碼。

### <a name="using-package-configuration-file"></a>使用封裝設定檔
如果您在封裝部署模型中使用封裝設定檔來變更執行時間的變數值，我們會自動將這些檔案連同您的套件一起上傳到 Azure 檔案儲存體，以便在您的 Azure SSIS IR 上執行。

### <a name="using-execute-package-task"></a>使用執行封裝工作
如果您的封裝包含的「執行封裝」工作參照儲存在本機檔案系統上的其他封裝，您需要執行下列額外的作業：

1. 將其他封裝上傳到與您的專案連線的相同 Azure 儲存體帳戶底下的 Azure 檔案儲存體，並取得其新的 UNC 路徑，例如`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 以新的 UNC 路徑取代執行封裝工作的檔案連接管理員中其他封裝的檔案路徑
   - 如果執行 SSDT 的電腦無法存取新的 UNC 路徑，您可以在 [檔案] 連線管理員的 [屬性] 面板上變更檔案路徑。
   - 或者，您可以使用檔案路徑的變數，在執行時間指派正確的值。

如果您的套件包含參考相同專案中其他封裝的「執行封裝」工作，則不需要進行額外的設定。

## <a name="next-steps"></a>後續步驟
當您滿意從 SSDT 在 Azure 中執行套件時，您可以將其部署並執行為 ADF 管線中的 Execute SSIS 套件活動，請參閱[在 adf 管線中將 ssis 套件執行為 EXECUTE Ssis 套件活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)。
