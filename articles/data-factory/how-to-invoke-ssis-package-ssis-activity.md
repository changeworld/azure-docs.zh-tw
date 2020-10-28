---
title: 使用執行 SSIS 套件活動執行 SSIS 套件
description: 本文說明如何使用「執行 SSIS 套件」活動，在 Azure Data Factory 管線中執行 SQL Server Integration Services 的 (SSIS) 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 07/20/2020
ms.openlocfilehash: 1bd983f7faeff456b04d4b2958236193a827a2cc
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635009"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure Data Factory 中使用 Execute SSIS 套件活動執行 SSIS 套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明如何使用「執行 SSIS 套件」活動，在 Azure Data Factory 管線中執行 SQL Server Integration Services 的 (SSIS) 套件。 

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

依照 [教學課程：](./tutorial-deploy-ssis-packages-azure.md)布建 Azure-SSIS IR 中的逐步指示，建立 (IR) 的 Azure SSIS 整合執行時間（如果您還沒有的話）。

## <a name="run-a-package-in-the-azure-portal"></a>在 Azure 入口網站中執行套件
在本節中，您會使用 Data Factory 使用者介面 (UI) 或應用程式，以執行 SSIS 套件的執行 SSIS 套件活動來建立 Data Factory 管線。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線
在此步驟中，您會使用 Data Factory UI 或應用程式來建立管線。 您要將 Execute SSIS 套件活動新增至管線，並設定它來執行 SSIS 套件。 

1. 在 Azure 入口網站的 Data Factory 總覽或首頁上，選取 [ **作者 & 監視器** ] 磚，以在個別的索引標籤中啟動 Data Factory UI 或應用程式。 

   ![Data Factory 首頁](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   在 [現在就開始吧] 頁面中，選取 [建立管線]。 

   ![開始使用頁面](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. 在 [ **活動** 工具箱] 中，展開 **[一般** ]。 然後將 [ **執行 SSIS 封裝** ] 活動拖曳至管線設計工具介面。 

   ![將執行 SSIS 套件活動拖曳至設計工具介面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   選取 [執行 SSIS 封裝] 活動物件來設定其 **[一般** ]、[ **設定** ]、[ **SSIS 參數** ]、[ **連線管理員** ] 和 [ **屬性覆寫** ]

#### <a name="general-tab"></a>一般索引標籤

在 [執行 SSIS 套件活動] 的 [ **一般** ] 索引標籤上，完成下列步驟。

![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. 針對 [ **名稱** ]，輸入您的「執行 SSIS 套件」活動的名稱。

   1. 在 [ **描述** ] 中，輸入「執行 SSIS 套件」活動的描述。

   1. 針對 [ **超時** ]，輸入執行 SSIS 封裝活動可以執行的最長時間量。 預設值為7天，格式為 d. HH： MM： SS。

   1. 如果是 [ **重試** ]，請輸入執行 SSIS 套件活動的重試嘗試次數上限。

   1. 在 [ **重試間隔** ] 中，輸入您的「執行 SSIS 套件」活動每次重試之間的秒數。 預設值為30秒。

   1. 選取 [ **安全輸出** ] 核取方塊，選擇您是否要從記錄中排除執行 SSIS 套件活動的輸出。

   1. 選取 [ **安全輸入** ] 核取方塊，選擇您是否要從記錄中排除 [執行 SSIS 封裝] 活動的輸入。

#### <a name="settings-tab"></a>[設定] 索引標籤

在 [執行 SSIS 套件活動] 的 [ **設定** ] 索引標籤上，完成下列步驟。

![在 [設定] 索引標籤上設定屬性 - 自動化](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. 針對 **Azure-SSIS IR** ，請選取指定的 Azure-SSIS IR 來執行「執行 SSIS 套件」活動。

   1. 在 [ **描述** ] 中，輸入「執行 SSIS 套件」活動的描述。

   1. 選取 [ **Windows 驗證** ] 核取方塊，選擇您是否要使用 Windows 驗證來存取資料存放區，例如 SQL server/內部部署或 Azure 檔案儲存體的檔案共用。
   
      如果您選取此核取方塊，請在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入套件執行認證的值。 例如，若要存取 Azure 檔案儲存體，網域為 `Azure` ，使用者名稱為 `<storage account name>` ，密碼為 `<storage account key>` 。

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這樣做，請選取旁邊的 [ **AZURE 金鑰保存庫** ] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您值的秘密名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的金鑰保存庫。 如果您尚未這麼做，請務必將 Data Factory 受控識別存取權授與您的金鑰保存庫。 您也可以用下列格式直接輸入秘密： `<key vault linked service name>/<secret name>/<secret version>` 。
      
   1. 選取 [ **32 位運行** 時間] 核取方塊，以選擇您的套件是否需要32位執行時間來執行。

   1. 針對 [ **封裝位置** ]，選取 [ **SSISDB** ]、[ **檔案系統] (封裝)** 、[ **檔案系統 (專案)** 、 **內嵌封裝** 或 **封裝存放區** 。 

##### <a name="package-location-ssisdb"></a>封裝位置： SSISDB

如果您的 Azure-SSIS IR 是以 Azure SQL Database server/受控執行個體所裝載 (SSISDB) 的 SSIS 目錄布建，則會自動選取 [ **ssisdb** ] 作為套件位置，或者您可以自行選取。 如果已選取，請完成下列步驟。

   1. 如果您的 Azure-SSIS IR 正在執行，且已清除 [ **手動專案** ] 核取方塊，請流覽並選取 SSISDB 的現有資料夾、專案、封裝和環境。 選取 **[** 重新整理] 以從 SSISDB 提取新加入的資料夾、專案、封裝或環境，讓它們可供流覽及選取。 若要流覽並選取您的封裝執行環境，您必須事先設定您的專案，以將這些環境新增為 SSISDB 下相同資料夾的參考。 如需詳細資訊，請參閱 [建立和對應 SSIS 環境](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)。

   1. 針對 [記錄層級]  ，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [ **自訂** ] 核取方塊。 

   1. 如果您的 Azure-SSIS IR 未執行或已選取 [ **手動專案** ] 核取方塊，請直接以下列格式輸入您的封裝和來自 SSISDB 的環境路徑： `<folder name>/<project name>/<package name>.dtsx` 和 `<folder name>/<environment name>` 。

      ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>封裝位置：檔案系統 (套件) 

**檔案系統 (套件)** 因為如果您的 Azure-SSIS IR 是在沒有 SSISDB 的情況下布建，則會自動選取您的套件位置，或您可以自行選取。 如果已選取，請完成下列步驟。

![在 [設定] 索引標籤上設定屬性-檔案系統 (套件) ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. 將通用命名慣例 (UNC) 路徑提供給封裝檔案，以指定要執行的封裝， (`.dtsx` 在 [ **封裝路徑** ] 方塊中) 。 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的套件。 例如，如果您將套件儲存在 Azure 檔案儲存體中，則其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` 。 
   
   1. 如果您在不同的檔案中設定套件，您也需要提供設定檔的 UNC 路徑 (與 [設定路徑] 方塊 `.dtsConfig` 中 **Configuration path** 的) 。 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的設定。 例如，如果您將設定儲存在 Azure 檔案儲存體中，則其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` 。

   1. 指定存取套件和設定檔的認證。 如果您先前已針對 **Windows 驗證** ) 輸入封裝執行認證 (的值，您可以選取 [ **與封裝執行認證相同** ] 核取方塊來重複使用它們。 否則，請在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入套件存取認證的值。 例如，如果您將封裝和設定儲存在 Azure 檔案儲存體中，則網域為 `Azure` ，使用者名稱為 `<storage account name>` ，密碼為 `<storage account key>` 。 

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這樣做，請選取旁邊的 [ **AZURE 金鑰保存庫** ] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您值的秘密名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的金鑰保存庫。 如果您尚未這麼做，請務必將 Data Factory 受控識別存取權授與您的金鑰保存庫。 您也可以用下列格式直接輸入秘密： `<key vault linked service name>/<secret name>/<secret version>` 。 

      這些認證也會用來存取「執行封裝」工作中的子封裝，該工作是由自己的路徑和套件中指定的其他設定所參考。 

   1. 如果您在透過 SQL Server Data Tools (SSDT) 建立封裝時使用 **>encryptallwithpassword** 或 **EncryptSensitiveWithPassword** 保護層級，請在 [ **加密密碼** ] 方塊中輸入您的密碼值。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
      
      如果您使用 **>encryptsensitivewithuserkey** 保護層級，請在設定檔或 **SSIS 參數** 、 **連線管理員** 或 **屬性覆寫** 索引標籤中重新輸入您的機密值， (查看以下) 。
      
      如果您使用 **>encryptallwithuserkey** 保護層級，則不受支援。 您必須透過 SSDT 或 `dtutil` 命令列公用程式，重新設定您的套件以使用另一個保護層級。 

   1. 針對 [記錄層級]  ，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [ **自訂** ] 核取方塊。 
   
   1. 如果您想要使用可在封裝中指定的標準記錄提供者來記錄套件執行，請在 [ **記錄路徑** ] 方塊中提供其 UNC 路徑，以指定您的記錄檔資料夾。 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則記錄路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個執行的個別封裝（以執行 SSIS 套件活動執行識別碼命名）建立子資料夾，而且每隔五分鐘就會產生記錄檔。 
   
   1. 指定要存取記錄檔資料夾的認證。 如果您先前已輸入封裝存取認證的值 (請參閱上述) ，您可以選取 [ **與封裝存取認證相同** ] 核取方塊來重複使用它們。 否則，請在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入記錄存取認證的值。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域為 `Azure` ，使用者名稱是 `<storage account name>` ，密碼為 `<storage account key>` 。 或者，您可以使用 Azure Key Vault 中儲存的秘密作為其值， (見上述) 。
   
對於先前提及的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

##### <a name="package-location-file-system-project"></a>封裝位置：檔案系統 (專案) 

如果您選取 [ **檔案系統] (專案)** 作為套件位置，請完成下列步驟。

![在 [設定] 索引標籤上設定屬性-檔案系統 (專案) ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. 在 [專案路徑] 方塊中提供)  (專案檔的 UNC 路徑 `.ispac` ，並使用 **Project path** `.dtsx` [ **封裝名稱** ] 方塊中專案的)  (封裝檔案，以指定要執行的封裝。 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的專案。 例如，如果您將專案儲存在 Azure 檔案儲存體中，則其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` 。

   1. 指定用來存取專案和封裝檔案的認證。 如果您先前已針對 **Windows 驗證** ) 輸入封裝執行認證 (的值，您可以選取 [ **與封裝執行認證相同** ] 核取方塊來重複使用它們。 否則，請在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入套件存取認證的值。 例如，如果您將專案和封裝儲存在 Azure 檔案儲存體中，則網域為 `Azure` ，使用者名稱是 `<storage account name>` ，密碼為 `<storage account key>` 。 

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這樣做，請選取旁邊的 [ **AZURE 金鑰保存庫** ] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您值的秘密名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的金鑰保存庫。 如果您尚未這麼做，請務必將 Data Factory 受控識別存取權授與您的金鑰保存庫。 您也可以用下列格式直接輸入秘密： `<key vault linked service name>/<secret name>/<secret version>` 。 

      這些認證也會用來存取在相同專案中參考的「執行封裝」工作中的子封裝。 

   1. 如果您在透過 SSDT 建立封裝時使用 **>encryptallwithpassword** 或 **EncryptSensitiveWithPassword** 保護等級，請在 [ **加密密碼** ] 方塊中輸入您的密碼值。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
      
      如果您使用 **>encryptsensitivewithuserkey** 保護層級，請在 **SSIS 參數** 、 **連線管理員** 或 **屬性覆寫** 索引標籤上重新輸入您的機密值， (查看以下) 。
      
      如果您使用 **>encryptallwithuserkey** 保護層級，則不受支援。 您必須透過 SSDT 或 `dtutil` 命令列公用程式，重新設定您的套件以使用另一個保護層級。 

   1. 針對 [記錄層級]  ，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [ **自訂** ] 核取方塊。 
   
   1. 如果您想要使用可在封裝中指定的標準記錄提供者來記錄套件執行，請在 [ **記錄路徑** ] 方塊中提供其 UNC 路徑，以指定您的記錄檔資料夾。 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則記錄路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個執行的個別封裝（以執行 SSIS 套件活動執行識別碼命名）建立子資料夾，而且每隔五分鐘就會產生記錄檔。 
   
   1. 指定要存取記錄檔資料夾的認證。 如果您先前已輸入封裝存取認證的值 (請參閱上述) ，您可以選取 [ **與封裝存取認證相同** ] 核取方塊來重複使用它們。 否則，請在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入記錄存取認證的值。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域為 `Azure` ，使用者名稱是 `<storage account name>` ，密碼為 `<storage account key>` 。 或者，您可以使用 Azure Key Vault 中儲存的秘密作為其值， (見上述) 。
   
對於先前提及的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

##### <a name="package-location-embedded-package"></a>封裝位置：內嵌封裝

如果您選取 [ **內嵌封裝** ] 作為套件位置，請完成下列步驟。

![在 [設定] 索引標籤上設定屬性-內嵌封裝](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. 使用) 將套件檔案拖放 (， `.dtsx` 或將它從檔案資料夾 **上傳** 至提供的方塊。 您的套件將會自動壓縮並內嵌在活動裝載中。 內嵌之後，您可以稍後 **下載** 套件進行編輯。 您也可以將內嵌封裝 **的參數化** ，方法是將它指派給可用於多個活動的管線參數，進而優化管線承載的大小。 `.ispac`目前不支援使用)  (內嵌專案檔，因此您無法在內嵌封裝中使用 SSIS 參數/連線管理員與專案層級範圍。
   
   1. 如果您的內嵌封裝未全部加密，而且我們偵測到使用「執行封裝」工作 (EPT) ，則會自動選取 [ **執行封裝** 工作] 核取方塊，並且會自動加入其檔案系統路徑所參考的子封裝，因此您也可以將其內嵌。
   
      如果無法偵測到使用 EPT，您必須手動選取 [ **執行封裝** 工作] 核取方塊，並將其檔案系統路徑所參考的子封裝逐一加入，以便您也可以內嵌它們。 如果您的子封裝儲存在 SQL Server 資料庫 (MSDB) 中，您就無法將其內嵌，因此您必須確定您的 Azure-SSIS IR 可以存取 MSDB，以使用其 SQL Server 參考來提取它們。 目前不支援使用)  (內嵌專案檔 `.ispac` ，因此您無法使用子封裝的專案型參考。
   
   1. 如果您在透過 SSDT 建立封裝時使用 **>encryptallwithpassword** 或 **EncryptSensitiveWithPassword** 保護等級，請在 [ **加密密碼** ] 方塊中輸入您的密碼值。 
   
      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這樣做，請選取旁邊的 [ **AZURE 金鑰保存庫** ] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您值的秘密名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的金鑰保存庫。 如果您尚未這麼做，請務必將 Data Factory 受控識別存取權授與您的金鑰保存庫。 您也可以用下列格式直接輸入秘密： `<key vault linked service name>/<secret name>/<secret version>` 。
      
      如果您使用 **>encryptsensitivewithuserkey** 保護層級，請在設定檔或 **SSIS 參數** 、 **連線管理員** 或 **屬性覆寫** 索引標籤中重新輸入您的機密值， (查看以下) 。
      
      如果您使用 **>encryptallwithuserkey** 保護層級，則不受支援。 您必須透過 SSDT 或 `dtutil` 命令列公用程式，重新設定您的套件以使用另一個保護層級。

   1. 針對 [記錄層級]  ，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [ **自訂** ] 核取方塊。 
   
   1. 如果您想要使用可在封裝中指定的標準記錄提供者來記錄套件執行，請在 [ **記錄路徑** ] 方塊中提供其 UNC 路徑，以指定您的記錄檔資料夾。 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則記錄路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個執行的個別封裝（以執行 SSIS 套件活動執行識別碼命名）建立子資料夾，而且每隔五分鐘就會產生記錄檔。 
   
   1. 在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入使用者的值，以指定要存取記錄檔資料夾的認證。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域為 `Azure` ，使用者名稱是 `<storage account name>` ，密碼為 `<storage account key>` 。 或者，您可以使用 Azure Key Vault 中儲存的秘密作為其值， (見上述) 。
   
對於先前提及的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

##### <a name="package-location-package-store"></a>封裝位置：套件存放區

如果您選取 [ **封裝存放區** ] 作為套件位置，請完成下列步驟。

![在 [設定] 索引標籤上設定屬性-套件存放區](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. 針對 [ **封裝存放區名稱** ]，選取附加至您 Azure-SSIS IR 的現有封裝存放區。

   1. 藉由在 `.dtsx` [ **封裝路徑** ] 方塊中，從選取的封裝存放區提供路徑)  (，以指定要執行的封裝。 如果選取的封裝存放區位於檔案系統/Azure 檔案儲存體頂端，您可以選取 **[流覽檔案儲存** ] 來流覽並選取封裝，否則您可以使用的格式輸入其路徑 `<folder name>\<package name>` 。 您也可以透過類似 [舊版 SSIS 封裝存放區](/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)的 SQL SERVER MANAGEMENT STUDIO (SSMS) ，將新的封裝匯入選取的封裝存放區。 如需詳細資訊，請參閱[使用 Azure-SSIS IR 套件存放區管理 SSIS 套件](./azure-ssis-integration-runtime-package-store.md) \(英文\)。

   1. 如果您在不同的檔案中設定您的套件，您必須在 [設定路徑] 方塊中，提供您的設定檔 (的 UNC 路徑 `.dtsConfig`) 。 **Configuration path** 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的設定。 例如，如果您將設定儲存在 Azure 檔案儲存體中，則其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` 。

   1. 選取 [設定 **存取認證** ] 核取方塊，以選擇是否要指定認證來個別存取設定檔。 當選取的封裝存放區位於您的 Azure SQL 受控執行個體所裝載的 SQL Server 資料庫 (MSDB) 之上，或也不會儲存您的設定檔時，就需要此選項。
   
      如果您先前已針對 **Windows 驗證** ) 輸入封裝執行認證 (的值，您可以選取 [ **與封裝執行認證相同** ] 核取方塊來重複使用它們。 否則，請在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入設定存取認證的值。 例如，如果您將設定儲存在 Azure 檔案儲存體中，則網域為 `Azure` ，使用者名稱為 `<storage account name>` ，密碼為 `<storage account key>` 。 

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這樣做，請選取旁邊的 [ **AZURE 金鑰保存庫** ] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您值的秘密名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的金鑰保存庫。 如果您尚未這麼做，請務必將 Data Factory 受控識別存取權授與您的金鑰保存庫。 您也可以用下列格式直接輸入秘密： `<key vault linked service name>/<secret name>/<secret version>` 。

   1. 如果您在透過 SSDT 建立封裝時使用 **>encryptallwithpassword** 或 **EncryptSensitiveWithPassword** 保護等級，請在 [ **加密密碼** ] 方塊中輸入您的密碼值。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
      
      如果您使用 **>encryptsensitivewithuserkey** 保護層級，請在設定檔或 **SSIS 參數** 、 **連線管理員** 或 **屬性覆寫** 索引標籤中重新輸入您的機密值， (查看以下) 。
      
      如果您使用 **>encryptallwithuserkey** 保護層級，則不受支援。 您必須透過 SSDT 或 `dtutil` 命令列公用程式，重新設定您的套件以使用另一個保護層級。 

   1. 針對 [記錄層級]  ，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [ **自訂** ] 核取方塊。 
   
   1. 如果您想要使用可在封裝中指定的標準記錄提供者來記錄套件執行，請在 [ **記錄路徑** ] 方塊中提供其 UNC 路徑，以指定您的記錄檔資料夾。 您可以選取 **[流覽檔案儲存體** ] 或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則記錄路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個執行的個別封裝（以執行 SSIS 套件活動執行識別碼命名）建立子資料夾，而且每隔五分鐘就會產生記錄檔。 
   
   1. 在 [ **網域** ]、[使用者 **名稱** ] 和 [ **密碼** ] 方塊中輸入使用者的值，以指定要存取記錄檔資料夾的認證。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域為 `Azure` ，使用者名稱是 `<storage account name>` ，密碼為 `<storage account key>` 。 或者，您可以使用 Azure Key Vault 中儲存的秘密作為其值， (見上述) 。
   
對於先前提及的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

#### <a name="ssis-parameters-tab"></a>SSIS 參數索引標籤

在 [執行 SSIS 套件活動] 的 [ **SSIS 參數** ] 索引標籤上，完成下列步驟。

![在 [SSIS 參數] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. 如果您的 Azure-SSIS IR 正在執行，則會選取 **SSISDB** 作為您的封裝位置，並清除 [ **設定** ] 索引標籤上的 [ **手動專案** ] 核取方塊，並顯示您所選取專案中的現有 SSIS 參數和 SSISDB 的封裝，以指派值給它們。 否則，您可以逐一輸入它們，以手動方式指派值給它們。 請確定它們存在且已正確輸入，您的套件執行才會成功。 
   
   1. 如果您在透過 SSDT 和 **檔案系統 () 套件** 建立封裝時使用 **>encryptsensitivewithuserkey** 保護等級，則會選取 [ **檔案系統 (專案)** ]、[ **內嵌封裝** ] 或 [ **套件存放區** ] 作為套件位置，您也必須重新輸入您的機密參數，以在此索引標籤上指派值給它們。 
   
當您將值指派給您的參數時，您可以使用運算式、函式、Data Factory 系統變數，以及 Data Factory 管線參數或變數，來新增動態內容。

或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這樣做，請選取旁邊的 [ **AZURE 金鑰保存庫** ] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您值的秘密名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的金鑰保存庫。 如果您尚未這麼做，請務必將 Data Factory 受控識別存取權授與您的金鑰保存庫。 您也可以用下列格式直接輸入秘密： `<key vault linked service name>/<secret name>/<secret version>` 。 

#### <a name="connection-managers-tab"></a>[連線管理員] 索引標籤

在 [執行 SSIS 套件活動] 的 [ **連接管理員** ] 索引標籤上，完成下列步驟。

![在 [連線管理員] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. 如果您的 Azure-SSIS IR 正在執行，則會選取 **SSISDB** 作為您的封裝位置，並清除 [ **設定** ] 索引標籤上的 [ **手動專案** ] 核取方塊，並顯示您所選取專案中的現有連線管理員和 SSISDB 的封裝，以將值指派給它們的屬性。 否則，您可以逐一輸入它們，以手動方式將值指派給它們的屬性。 請確定它們存在且已正確輸入，您的套件執行才會成功。 
   
      您可以在 SSDT 上開啟包含該連接管理員的封裝，以取得該連接管理員的正確 **範圍** 、 **名稱** 和 **屬性** 名稱。 開啟封裝之後，請選取相關的連線管理員，以在 SSDT 的 [ **屬性** ] 視窗中顯示其所有屬性的名稱和值。 利用這項資訊，您可以在執行時間覆寫任何連接管理員屬性的值。 

      ![從 SSDT 取得連線管理員屬性](media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png)

      例如，不在 SSDT 上修改原始封裝，您可以在執行時間覆寫現有連線管理員中的 **ConnectByProxy** 、 **ConnectionString** 和 **>connectusingmanagedidentity** 屬性值，將在 SQL Server 上執行的內部部署對內部部署資料流程轉換成在 ADF 上執行的內部部署到雲端資料流程。
      
      這些執行時間覆寫可以在存取內部部署資料時，啟用 Self-Hosted IR (SHIR) 作為 SSIS IR 的 proxy，請參閱將 [SHIR 設定為 SSIS ir 的 proxy](./self-hosted-integration-runtime-proxy-ssis.md)，以及使用最新 msoledbsql.h 驅動程式的 Azure SQL Database/受控執行個體連線，進而啟用使用 adf 受控識別的 [adf 受控識別來進行 aad 驗證](/sql/integration-services/connection-manager/ole-db-connection-manager?view=sql-server-ver15#managed-identities-for-azure-resources-authentication)。

      ![在 [連接管理員] 索引標籤上設定 SSDT 的屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png)
   
   1. 如果您在透過 SSDT 和 **檔案系統 () 套件** 建立封裝時使用 **>encryptsensitivewithuserkey** 保護等級，則會選取 [ **檔案系統 (專案)** ]、[ **內嵌封裝** ] 或 [ **套件存放區** ] 作為套件位置，您也必須重新輸入您的機密連接管理員屬性，以便在此索引標籤上指派值給它們。 

當您將值指派給您的連線管理員屬性時，您可以使用運算式、函式、Data Factory 系統變數，以及 Data Factory 管線參數或變數，來新增動態內容。 

或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這樣做，請選取旁邊的 [ **AZURE 金鑰保存庫** ] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您值的秘密名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的金鑰保存庫。 如果您尚未這麼做，請務必將 Data Factory 受控識別存取權授與您的金鑰保存庫。 您也可以用下列格式直接輸入秘密： `<key vault linked service name>/<secret name>/<secret version>` 。 

#### <a name="property-overrides-tab"></a>屬性覆寫索引標籤

在 [執行 SSIS 套件活動] 的 [ **屬性覆寫** ] 索引標籤上，完成下列步驟。

![在 [屬性覆寫] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. 輸入您所選取封裝中現有屬性的路徑，逐一指派值給它們。 請確定它們存在且已正確輸入，您的套件執行才會成功。 例如，若要覆寫您的使用者變數值，請以下列格式輸入其路徑： `\Package.Variables[User::<variable name>].Value` 。 

      您可以開啟包含 SSDT 的封裝，以取得任何封裝屬性的正確 **屬性路徑** 。 開啟封裝之後，請在 SSDT 的 [ **屬性** ] 視窗中選取其 [控制 **流程和設定** ] 屬性。 接下來，選取 [設定 **] 屬性旁** 的省略號 ( **...** ) 按鈕，以開啟通常用來 [在封裝部署模型中建立封裝](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations)設定的 [ **套件設定召集人** ]。 

      ![從 SSDT 取得套件屬性-設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png)

      在 [ **套件設定召集人** ] 上，選取 [ **啟用封裝** 設定] 核取方塊和 [ **新增 ...** ] 按鈕，以開啟 [ **套件設定]** 。 
      
      在 [ **套件設定向導]** 的 [設定 **類型** ] 下拉式功能表中，選取 [ **XML 設定檔** ] 專案，並選取 [ **直接指定設定** ] 按鈕、輸入您的設定檔名稱，然後選取 [ **下一步] >** 按鈕。 

      ![從 SSDT 取得套件屬性-設定召集人](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png)

      最後，選取您要的路徑和 **下一個 >** 按鈕的封裝屬性。  您現在可以看到，複製 & 貼上您想要的套件屬性路徑，並將它們儲存在設定檔中。 利用這項資訊，您可以在執行時間覆寫任何封裝屬性的值。 

      ![從 SSDT 取得套件屬性-設定 wizard](media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png)
   
   1. 如果您在透過 SSDT 和 **檔案系統 () 套件** 建立封裝時使用 **>encryptsensitivewithuserkey** 保護等級，則會選取 [ **檔案系統 (專案)** ]、[ **內嵌封裝** ] 或 [ **套件存放區** ] 作為套件位置，您也必須重新輸入您的機密套件屬性，以便在此索引標籤上指派值給它們。 
   
當您將值指派給封裝屬性時，您可以使用運算式、函式、Data Factory 系統變數，以及 Data Factory 管線參數或變數，來新增動態內容。

您可以使用 **連線管理員** 或 **屬性覆寫** 索引標籤，覆寫 [設定檔] 和 [ **SSIS 參數** ] 索引標籤上指派的值。 您也可以使用 [ **屬性覆寫** ] 索引標籤來覆寫 [ **連接管理員** ] 索引標籤上指派的值。

若要驗證管線設定，請選取工具列上的 [ **驗證** ]。 若要關閉 [ **管線驗證] 報表** ，請選取 [] **>>** 。

若要將管線發佈至 Data Factory，請選取 [ **全部發佈** ]。 

### <a name="run-the-pipeline"></a>執行管線
在此步驟中，您會觸發管線執行。 

1. 若要觸發管線執行，請選取工具列上的 [ **觸發** 程式]，然後選取 [ **立即觸發** ]。 

   ![立即觸發](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在 [管線執行] 視窗中，選取 [完成]。 

### <a name="monitor-the-pipeline"></a>監視管線

1. 切換至左側的 [監視] 索引標籤。 您會看到管線執行及其狀態以及其他資訊，例如 **執行時間開始** 時間。 若要重新整理檢視，請選取 [重新整理]  。

   ![管線執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. 選取 [動作]  資料行中的 [檢視活動執行]  連結。 您只會看到一個活動執行，因為該管線只有一個活動。 這是「執行 SSIS 套件」活動。

   ![活動執行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 針對 SQL server 中的 SSISDB 資料庫執行下列查詢，以確認封裝已執行。 

   ```sql
   select * from catalog.executions
   ```

   ![確認封裝執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 您也可以從管線活動執行的輸出取得 SSISDB 執行識別碼，並使用此識別碼在 SQL Server Management Studio 中檢查更完整的執行記錄和錯誤訊息。

   ![取得執行識別碼。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線

您也可以建立管線的排程觸發程式，以便管線依排程執行，例如每小時或每日。 如需範例，請參閱[建立資料處理站 - 資料處理站 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="run-a-package-with-powershell"></a>使用 PowerShell 執行套件
在本節中，您會使用 Azure PowerShell，利用執行 SSIS 套件的執行 SSIS 套件活動來建立 Data Factory 管線。 

依照[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)中的逐步指示，安裝最新的 Azure PowerShell 模組。

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>使用 Azure-SSIS IR 建立 data factory
您可以使用已 Azure-SSIS IR 布建的現有 data factory，或使用 Azure-SSIS IR 建立新的資料處理站。 遵循 [教學課程：透過 PowerShell 將 SSIS 套件部署至 Azure](./tutorial-deploy-ssis-packages-azure-powershell.md)中的逐步指示。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線 
在此步驟中，您要使用執行 SSIS 套件活動建立管線。 此活動會執行您的 SSIS 套件。 

1. `RunSSISPackagePipeline.json`使用與下列範例類似的內容，在資料夾中建立名為的 JSON 檔案 `C:\ADF\RunSSISPackage` 。

   > [!IMPORTANT]
   > 儲存檔案之前，請先取代物件名稱、描述和路徑、屬性或參數值、密碼和其他變數值。 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   若要執行儲存在檔案系統/Azure 檔案儲存體中的封裝，請輸入您的封裝和記錄檔位置屬性的值，如下所示：

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   若要在儲存于檔案系統/Azure 檔案儲存體的專案內執行封裝，請輸入套件位置屬性的值，如下所示：

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   若要執行內嵌封裝，請輸入套件位置屬性的值，如下所示：

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   若要執行儲存在封裝存放區中的封裝，請輸入您的封裝和設定位置屬性的值，如下所示：

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. 在 Azure PowerShell 中，切換至 `C:\ADF\RunSSISPackage` 資料夾。

3. 若要建立管線 **RunSSISPackagePipeline** ，請執行 **>invoke-azdatafactoryv2pipeline** Cmdlet。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   以下是範例輸出：

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>執行管線
使用 **>invoke-azdatafactoryv2pipeline** Cmdlet 來執行管線。 Cmdlet 會傳回管線執行識別碼，方便後續監視。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>監視管線

執行下列 PowerShell 程式碼以持續檢查管線執行狀態，直到完成複製資料為止。 在 PowerShell 視窗中複製或貼上下列腳本，然後選取 Enter。 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

您也可以使用 Azure 入口網站來監視管線。 如需逐步指示，請參閱[監視管線](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)：

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線
在上一個步驟中，您已依需求執行管線。 您也可以建立排程觸發程式，依排程執行管線，例如每小時或每日。

1. `MyTrigger.json`使用下列內容，在資料夾中建立名為的 JSON 檔案 `C:\ADF\RunSSISPackage` ： 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. 在 Azure PowerShell 中，切換至 `C:\ADF\RunSSISPackage` 資料夾。
1. 執行 >start-azdatafactoryv2trigger Cmdlet，以建立觸發 **程式** 。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 觸發程序預設處於已停止狀態。 執行 **>start-azdatafactoryv2trigger** Cmdlet 來啟動觸發程式。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. 藉由執行 **>start-azdatafactoryv2trigger** Cmdlet 來確認觸發程式是否已啟動。 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. 在下一個小時後，執行下列命令。 例如，如果目前的時間是 UTC 下午 3:25，請在 UTC 下午 4:00 執行命令。 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   針對 SQL server 中的 SSISDB 資料庫執行下列查詢，以確認封裝已執行。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>後續步驟
請參閱下列部落格文章：
- [使用 Azure Data Factory 管線中的 SSIS 活動，將您的 ETL/ELT 工作流程現代化及擴充](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)