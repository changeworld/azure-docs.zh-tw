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
ms.date: 09/06/2020
ms.openlocfilehash: a455d547fa3db2fd6e963458a29c77f516112e18
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556506"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>從 SSDT 在 Azure 中執行 SSIS 套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明在 SQL Server Data Tools (SSDT) 上，啟用 Azure 的 SQL Server Integration Services (SSIS) 專案的功能。 它可讓您評估 SSIS 套件的雲端相容性，並在 Azure Data Factory (ADF) 的 Azure-SSIS Integration Runtime (IR) 上執行它們。 您可以使用這項功能來測試現有的封裝，然後再將 & 轉移/遷移至 Azure，或開發要在 Azure 中執行的新套件。

您可以使用這項功能，將新建立的/現有 Azure-SSIS IR 附加至 SSIS 專案，然後在其上執行套件。  我們支援執行套件以部署到 SSIS 目錄 (SSISDB) 由您 Azure SQL Database 伺服器或專案部署模型中的受控實例所裝載。 我們也支援在封裝部署模型中，將封裝執行部署到 Azure SQL 受控實例所裝載的檔案系統/Azure 檔案儲存體/SQL Server 資料庫 (MSDB) 。 

## <a name="prerequisites"></a>Prerequisites

若要使用這項功能，請從 [這裡](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)下載並安裝最新的 SSDT 與 SSIS 專案擴充功能，以 VISUAL STUDIO (VS) 。 或者，您也可以從 [這裡](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)下載並安裝最新的 SSDT 做為獨立安裝程式。

## <a name="azure-enable-ssis-projects"></a>Azure-啟用 SSIS 專案

### <a name="creating-new-azure-enabled-ssis-projects"></a>建立已啟用 Azure 的新 SSIS 專案

在 SSDT 上，您可以使用 **Integration Services 專案 (啟用** azure 的) 範本來建立新的已啟用 azure SSIS 專案。

   ![已啟用 Azure 的新 SSIS 專案](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

建立啟用 Azure 的專案之後，系統會提示您連接到 Azure Data Factory 中的 SSIS。

   ![連接 Azure-SSIS IR 提示](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

如果您想要立即連接到您的 Azure-SSIS IR，請參閱 [連接到 Azure-SSIS IR](#connectssisir) ，以取得更多詳細資料。 您也可以在 SSDT 的 [方案總管] 視窗中，以滑鼠右鍵按一下專案節點，以顯示功能表。 接下來，**在 Azure Data Factory** 子功能表中，選取 ssis 中 Azure Data Factory 專案的 [**連接到 ssis]** 。

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure-啟用現有的 SSIS 專案

針對現有的 SSIS 專案，您可以遵循下列步驟來啟用 Azure：

1. 在 SSDT 的 [方案總管] 視窗中，以滑鼠右鍵按一下您的專案節點，以顯示功能表。 接下來，**在 Azure Data Factory** 子功能表中，選取 [SSIS 中 **已啟用 azure 的專案** 專案]，以啟動 **啟用 azure 的專案嚮導**。

   ![Azure-啟用現有的 SSIS 專案](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. 在 [ **選取 Visual Studio** 設定] 頁面上，選取您現有的 VS 設定，以將套件執行設定套用至 Azure。 如果尚未建立新的 VS 設定，您也可以建立新的 [VS](/visualstudio/ide/how-to-create-and-edit-configurations)設定。 建議您在本機和雲端環境中至少有兩個不同的 VS 設定來執行套件，以便 Azure 針對雲端設定啟用您的專案。 如此一來，如果您已參數化專案或封裝，您可以在執行時間根據不同的執行環境，將不同的值指派給您的專案或封裝參數 (在本機電腦或 Azure) 中。 例如，請參閱 [切換封裝執行環境](#switchenvironment)。

   ![選取 Visual Studio 設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure-啟用您現有的 SSIS 專案時，您必須將其目標伺服器版本設定為 Azure-SSIS IR 所支援的最新版本。 Azure-SSIS IR 目前以 **SQL Server 2017** 為基礎。 請確定您的套件未包含 SQL Server 2017 上不支援的其他元件。 此外，也請確定您的 Azure-SSIS IR 上也已透過自訂安裝程式安裝所有相容的其他元件，請參閱 [自訂您的 Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)。 選取 [ **下一步]** 按鈕以繼續。

   ![切換目標伺服器版本](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. 請參閱 [連接到 Azure-SSIS IR](#connectssisir) ，完成將專案連接到 Azure-SSIS IR。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> 在 Azure Data Factory 中將已啟用 Azure 的專案連線到 SSIS

藉由將已啟用 Azure 的專案連接至 ADF 中的 SSIS，您可以將套件上傳至 Azure 檔案儲存體，並在 Azure-SSIS IR 上執行。 您可以依照下列步驟執行這項操作：

1. 在 **ADF 簡介** 頁面的 SSIS 上，查看簡介，然後選取 [ **下一步]** 按鈕繼續進行。

   ![ADF 中的 SSIS 簡介](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. 在 [ **選取 adf 中的 SSIS IR** ] 頁面上，選取您現有的 ADF 和 Azure-SSIS IR 來執行套件。 您也可以建立新的專案（如果沒有的話）。
   - 若要選取現有的 Azure-SSIS IR，請先選取相關的 Azure 訂用帳戶和 ADF。
   - 如果您選取的現有 ADF 沒有任何 Azure-SSIS IR，請選取 [ **建立 SSIS IR** ] 按鈕，在 adf 入口網站上建立新的 adf。 建立之後，您可以返回此頁面來選取新的 Azure-SSIS IR。
   - 如果您選取現有的 Azure 訂用帳戶，但該訂用帳戶沒有任何 ADF，請選取 [ **建立 SSIS IR** ] 按鈕以啟動 [ **Integration Runtime 建立]**。 在嚮導上，您可以輸入指定的位置和前置詞，以代表您自動建立新的 Azure 資源群組、Data Factory 和 SSIS IR，並以下列模式命名： **YourPrefix-RG/DF/IR-YourCreationTime**。 建立之後，您可以返回此頁面來選取新的 ADF 和 Azure-SSIS IR。

   ![選取 ADF 中的 SSIS IR](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. 在 [ **選取 Azure 儲存體** ] 頁面上，選取您現有的 Azure 儲存體帳戶，以將套件上傳至 Azure 檔案儲存體。 您也可以建立一個新的（如果沒有的話）。
   - 若要選取現有的 Azure 儲存體帳戶，請先選取相關的 Azure 訂用帳戶。
   - 如果您選取的 Azure 訂用帳戶與沒有任何 Azure 儲存體帳戶的 Azure-SSIS IR 相同，請選取 [ **建立] Azure 儲存體** 按鈕。 我們會在您 Azure-SSIS IR 的相同位置中，自動建立一個新的名稱，其命名方式是將您的 Azure-SSIS IR 名稱和其建立日期的前置詞結合在一起。 建立之後，您可以返回此頁面來選取新的 Azure 儲存體帳戶。
   - 如果您選取沒有任何 Azure 儲存體帳戶的不同 Azure 訂用帳戶，請選取 [ **建立 Azure 儲存體** ] 按鈕，在 Azure 入口網站上建立新的訂用帳戶。 建立之後，您可以返回此頁面來選取新的 Azure 儲存體帳戶。

   ![選取 [Azure 儲存體]](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. 選取 [連線 **]** 按鈕，完成將專案連接到 Azure-SSIS IR 的工作。 我們將會在 SSDT 的 [方案總管] 視窗中，于 [ **連結的 Azure 資源** ] 節點下，顯示您選取的 Azure-SSIS IR 和 Azure 儲存體帳戶。 我們也會定期重新整理並顯示 Azure-SSIS IR 的狀態。 您可以用滑鼠右鍵按一下其節點來開啟功能表，然後選取將您帶到 ADF 入口網站的 **Start\Stop\Manage** 專案，來管理您的 Azure-SSIS IR。

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>評定 SSIS project\packages 以在 Azure 中執行

### <a name="assessing-single-or-multiple-packages"></a>評定單一或多個封裝

在 Azure 中執行您的封裝之前，您可以進行評估，以呈現任何可能的雲端相容性問題。 其中包括遷移封鎖程式和您應該注意的其他資訊。 
-  您可以選擇在專案下，同時評估單一套件或所有套件。

   ![評估套件](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![評定專案](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  在 SSDT 的 [ **評定報告** ] 視窗中，您可以找到所有出現的潛在雲端相容性問題，各有各自的詳細描述和建議。 您也可以將評量報告匯出成 CSV 檔案，該檔案可以與任何應減輕這些問題的人共用。 

   ![評估報告](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>隱藏評量規則

一旦您確定某些可能的雲端相容性問題不適用，或您的套件中已適當地緩和，您就可以隱藏相關的評定規則。 這會減少後續評量報告中的雜訊。
-  在 SSDT 的 [**評量報告**] 視窗中，選取 [**設定評定規則隱藏**] 連結，以顯示 [**評量規則隱藏設定**] 視窗，您可以在其中選取要隱藏的評量規則。

   ![評量規則隱藏設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  或者，在 SSDT 的方案總管視窗中，以滑鼠右鍵按一下您的專案節點，以顯示功能表。 在 Azure Data Factory 子功能表中，選取 [ **SSIS 中****啟用 Azure 的設定** 專案]，以顯示包含專案屬性頁的視窗。 在 [**啟用 Azure 的設定**] 區段中，選取 [**隱藏的評定規則識別碼**] 屬性。 最後，選取其省略號 (**...**) 按鈕以顯示 [ **評量規則隱藏設定** ] 視窗，您可以在其中選取要隱藏的評量規則。

   ![啟用 Azure 的設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![透過啟用 Azure 的設定進行評定規則隱藏設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>在 Azure 中執行 SSIS 套件

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> 設定啟用 Azure 的設定

在 Azure 中執行您的封裝之前，您可以為其設定 Azure 啟用的設定。 例如，您可以依照下列步驟，在您的 Azure-SSIS IR 上啟用 Windows 驗證，以存取內部部署/雲端資料存放區：

1. 在 SSDT 的 [方案總管] 視窗中，以滑鼠右鍵按一下您的專案節點，以顯示功能表。 接下來，**在 Azure Data Factory** 子功能表中，選取 [SSIS 中 **啟用 Azure 的設定** 專案]，以顯示包含專案屬性頁的視窗。

   ![啟用 Azure 的設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. 在 [啟用 **Azure 的設定**] 區段中，選取 [**啟用 Windows 驗證**] 屬性，然後在其子功能表中選取 [ **True** ]。 接下來，選取 [ **Windows 驗證認證** ] 屬性，然後選取其省略號 (**...**) 按鈕以顯示 [ **windows 驗證認證** ] 視窗。

   ![啟用 Windows 驗證](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. 輸入您的 Windows 驗證認證。 例如，若要存取 Azure 檔案儲存體，您可以 `Azure` `YourStorageAccountName` `YourStorageAccountKey` 分別針對 **網域**、使用者 **名稱** 和 **密碼** 分別輸入、和。

   ![Windows 驗證認證](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>啟動封裝執行

將已啟用 Azure 的專案連接至 ADF 中的 SSIS、評估其雲端相容性，並減輕潛在問題之後，您可以在 Azure-SSIS IR 上執行/測試您的套件。
-  選取 [SSDT] 工具列中的 [ **開始** ] 按鈕，以下拉式功能表。 接下來，選取 [ **在 Azure 中執行** ] 專案。

   ![在 Azure 中執行](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  或者，在 SSDT 的 [方案總管] 視窗中，以滑鼠右鍵按一下您的套件節點，以顯示功能表。 接下來，選取 [ **在 Azure 中執行封裝** ] 專案。

   ![在 Azure 中執行套件](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> 若要在 Azure 中執行您的封裝，您必須有執行中的 Azure-SSIS IR，因此，如果您的 Azure-SSIS IR 已停止，則會顯示對話方塊視窗來啟動它。 排除任何自訂的安裝時間，此程式應該在5分鐘內完成，但可能需要大約 20-30 分鐘的時間，Azure-SSIS IR 加入虛擬網路。 在 Azure 中執行您的封裝之後，您可以在 SSDT 的 [方案總管] 視窗中，以滑鼠右鍵按一下節點，然後選取會帶您前往 ADF 入口網站的 **Start\Stop\Manage** 專案，來停止您的 Azure-SSIS IR 來管理其執行成本。

### <a name="using-execute-package-task"></a>使用執行封裝工作

如果您的套件包含的「執行封裝」工作參考儲存在本機檔案系統上的子封裝，請遵循下列其他步驟：

1. 將子封裝上傳至與您的專案連線之相同 Azure 儲存體帳戶下的 Azure 檔案儲存體，並將其新的通用命名慣例 (UNC) 路徑，例如 `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. 以新的 UNC 路徑取代執行封裝工作的檔案連線管理員中這些子封裝的檔案路徑
   - 如果執行 SSDT 的本機電腦無法存取新的 UNC 路徑，您可以在 [檔案] 的 [屬性] 面板上輸入它連線管理員
   - 或者，您可以使用檔案路徑的變數，在執行時間指派正確的值

如果您的封裝包含在相同專案中參考子封裝的「執行封裝」工作，則不需要額外的步驟。

### <a name="switching-package-protection-level"></a>切換封裝保護層級

在 Azure 中執行 SSIS 套件不支援 **>encryptsensitivewithuserkey** / **>encryptallwithuserkey** 保護層級。 因此，如果您的套件設定為使用這些套件，我們會暫時將它們分別轉換成使用 **EncryptSensitiveWithPassword** / **>encryptallwithpassword** 保護層級。 我們也會在將套件上傳到 Azure 檔案儲存體以供 Azure-SSIS IR 執行時，隨機產生加密密碼。

> [!NOTE]
> 如果您的套件包含的「執行封裝」工作參考的子封裝設定為使用 **>encryptsensitivewithuserkey** / **>encryptallwithuserkey** 保護層級，則您必須手動將這些子封裝重新設定為使用 **EncryptSensitiveWithPassword** / **>encryptallwithpassword** 保護層級，再執行您的套件。

如果您的套件已設定為使用 **EncryptSensitiveWithPassword** / **>encryptallwithpassword** 保護層級，我們會將它們保持不變。 當我們將套件上傳到 Azure 檔案儲存體以便在您的 Azure-SSIS IR 上執行時，我們仍然會隨機產生加密密碼。

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> 切換封裝執行環境

如果您在專案部署模型中將專案/套件參數化，您可以建立多個 VS 設定來切換封裝執行環境。 如此一來，您就可以在執行時間將環境特有的值指派給專案/封裝參數。 建議您在本機和雲端環境中至少有兩個不同的 VS 設定來執行套件，以便 Azure 針對雲端設定啟用您的專案。 以下是在本機電腦與 Azure 之間切換封裝執行環境的逐步範例：

1. 假設您的套件包含設定檔案屬性的檔案系統工作。 當您在本機電腦上執行它時，它會設定儲存在本機檔案系統上之檔案的屬性。 當您在 Azure-SSIS IR 上執行時，您會想要讓它設定 Azure 檔案儲存體中所儲存之檔案的屬性。 首先，建立字串類型的封裝參數，並將它命名為 **FilePath** 以保存目標檔案路徑的值。

   ![建立封裝參數](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. 接下來，在 [**檔案系統工作編輯器**] 視窗的 [**一般**] 頁面上，使用 **FilePath** 封裝參數參數化 [**來源連接**] 區段中的 **[sourcevariable** 屬性。 

   ![參數化來源連接](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. 根據預設，您在名為 **開發** 的本機環境中有現有的 VS 設定，可供封裝執行。 在名為 **Azure** 的雲端環境中建立套件執行的新 vs 設定，請參閱 [建立新的 vs](/visualstudio/ide/how-to-create-and-edit-configurations)設定（如果您尚未這樣做）。

4. 當您查看套件的參數時，請選取 [ **將參數新增至** 設定] 按鈕，以開啟封裝的 [ **管理參數值** ] 視窗。 接下來，將目標檔案路徑的不同值指派給 **開發** 和 **Azure** 設定下的 **FilePath** 套件參數。

   ![指派參數值](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure-針對雲端設定啟用您的專案，請參閱 [Azure-啟用現有的 SSIS 專案](#azureenableproject)（如果您尚未這樣做）。 接下來，設定啟用 Azure 的設定，以啟用 Azure-SSIS IR 存取 Azure 檔案儲存體的 Windows 驗證，請參閱 [設定啟用 azure 的設定](#azureenabledsettings)（如果您尚未這樣做）。

6. 在 Azure 中執行您的套件。 您可以選取 **開發** 設定，將封裝執行環境切換回本機電腦。

   ![切換 Visual Studio 設定](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>使用套件設定檔

如果您在封裝部署模型中使用封裝設定檔，您可以在執行時間將環境特有的值指派給封裝屬性。 我們會使用您的套件自動將這些檔案上傳至 Azure 檔案儲存體，以便在您的 Azure-SSIS IR 上執行。

### <a name="checking-package-execution-logs"></a>檢查封裝執行記錄

開始執行封裝之後，我們會將其記錄格式化並顯示在 SSDT 的 [ **進度** ] 視窗中。 針對長時間執行的套件，我們會定期更新其記錄檔（分鐘）。 您可以藉由選取 [SSDT] 工具列中的 [ **停止** ] 按鈕，立即取消封裝執行。 您也可以在下列 UNC 路徑中暫時找出其記錄的原始資料： `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` ，但我們會在一天后將其清除。

## <a name="current-limitations"></a>目前的限制

-  啟用 Azure 的 SSDT 僅支援商業/全球雲端區域，目前不支援政府/國家雲端區域。

## <a name="next-steps"></a>後續步驟

當您滿意從 SSDT 在 Azure 中執行套件之後，您就可以在 ADF 管線中將其部署並執行為執行 SSIS 套件活動，請參閱 [在 adf 管線中執行 ssis 套件作為執行 Ssis 套件活動](./how-to-invoke-ssis-package-ssis-activity.md)。