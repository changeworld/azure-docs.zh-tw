---
title: 使用執行 SSIS 套件活動執行 SSIS 套件
description: 本文說明如何使用執行 SSIS 套件活動，在 Azure Data Factory 管線中執行 SQL Server Integration Services (SSIS) 套件。
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
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: 172a2ae863714b54e052819df93f872385b9c77a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86183336"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure Data Factory 中使用 Execute SSIS 套件活動執行 SSIS 套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文說明如何使用執行 SSIS 套件活動，在 Azure Data Factory 管線中執行 SQL Server Integration Services (SSIS) 套件。 

## <a name="prerequisites"></a>必要條件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果您還沒有遵循[教學課程：布建 AZURE SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)中的逐步指示，請 (IR) 建立 azure SSIS 整合執行時間。

## <a name="run-a-package-in-the-azure-portal"></a>在 Azure 入口網站中執行套件
在本節中，您會使用 Data Factory 使用者介面 (UI) 或應用程式，以執行 SSIS 套件的 Execute SSIS 套件活動來建立 Data Factory 管線。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線
在此步驟中，您會使用 Data Factory UI 或應用程式來建立管線。 您要將 Execute SSIS 套件活動新增至管線，並設定它來執行 SSIS 套件。 

1. 在 Azure 入口網站的 Data Factory 總覽或首頁上，選取 [**作者 & 監視器**] 圖格，以在另一個索引標籤中啟動 Data Factory UI 或應用程式。 

   ![Data Factory 首頁](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   在 [現在就開始吧] 頁面中，選取 [建立管線]。 

   ![開始使用頁面](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. 在 [**活動**] 工具箱中，展開 **[一般**]。 然後將 [**執行 SSIS 套件**] 活動拖曳至管線設計工具介面。 

   ![將執行 SSIS 套件活動拖曳至設計工具介面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

   選取 [執行 SSIS 套件] 活動物件，以設定其**一般**、**設定**、 **SSIS 參數**、**連線管理員**和 [**屬性覆寫**] 索引標籤。

#### <a name="general-tab"></a>一般索引標籤

在 [執行 SSIS 套件活動] 的 [**一般**] 索引標籤上，完成下列步驟。

   ![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

   1. 針對 [**名稱**]，輸入執行 SSIS 套件活動的名稱。

   1. 針對 [**描述**]，輸入「執行 SSIS 套件」活動的描述。

   1. 針對 [**超時**]，輸入執行 SSIS 套件活動可以執行的最大時間量。 預設值為7天，格式為 d. HH： MM： SS。

   1. 針對 [**重試**]，輸入執行 SSIS 套件活動的重試次數上限。

   1. 在 [**重試間隔**] 中，輸入執行 SSIS 套件活動每次重試之間的秒數。 預設值為30秒。

   1. 選取 [**安全輸出**] 核取方塊，選擇是否要從記錄中排除 [執行 SSIS 套件] 活動的輸出。

   1. 選取 [**安全輸入**] 核取方塊，以選擇是否要從記錄中排除 [執行 SSIS 套件] 活動的輸入。

#### <a name="settings-tab"></a>[設定] 索引標籤

在 [執行 SSIS 套件活動] 的 [**設定**] 索引標籤上，完成下列步驟。

   ![在 [設定] 索引標籤上設定屬性 - 自動化](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   1. 針對**AZURE SSIS ir**，請選取指定的 AZURE SSIS ir 來執行執行 SSIS 套件活動。

   1. 針對 [**描述**]，輸入「執行 SSIS 套件」活動的描述。

   1. 選取 [ **windows 驗證**] 核取方塊，選擇是否要使用 windows 驗證來存取資料存放區，例如內部部署或 AZURE 檔案儲存體的 SQL server/檔案共用。
   
      如果您選取此核取方塊，請在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入封裝執行認證的值。 例如，若要存取 Azure 檔案儲存體，網域是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這麼做，請選取其旁邊的 [ **AZURE 金鑰保存庫**] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您的值的密碼名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的。 如果您尚未這麼做，請務必將您的金鑰保存庫存取權授與 Data Factory 受控識別。 您也可以直接以下列格式輸入您的密碼： `<key vault linked service name>/<secret name>/<secret version>` 。
      
   1. 選取 [ **32 位運行**時間] 核取方塊，選擇您的套件是否需要執行32位執行時間。

   1. 針對 [**封裝位置**]，選取 [ **SSISDB**]、[**檔案系統] (封裝) **、**檔案系統 (專案) **、**內嵌封裝**或**封裝存放區**。 

##### <a name="package-location-ssisdb"></a>套件位置： SSISDB

如果您的 Azure SSIS IR 是使用 SSIS 目錄布建 (SSISDB) 裝載 Azure SQL Database server/受控執行個體，或者您可以自行選取，則會自動選取**ssisdb**作為套件位置。 如果已選取，請完成下列步驟。

   1. 如果您的 Azure SSIS IR 正在執行，且已清除 [**手動輸入**] 核取方塊，請從 SSISDB 流覽並選取現有的資料夾、專案、封裝和環境。 選取 [重新整理 **]，從**SSISDB 提取新加入的資料夾、專案、套件或環境，讓它們可供流覽和選取。 若要流覽並選取封裝執行的環境，您必須事先設定專案，將這些環境新增為 SSISDB 底下相同資料夾中的參考。 如需詳細資訊，請參閱[建立和對應 SSIS 環境](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)。

   1. 針對 [記錄層級]****，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [**自訂**] 核取方塊。 

   1. 如果您的 Azure SSIS IR 未執行或已選取 [**手動專案**] 核取方塊，請直接以下列格式輸入 SSISDB 的套件和環境路徑： `<folder name>/<project name>/<package name>.dtsx` 和 `<folder name>/<environment name>` 。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

##### <a name="package-location-file-system-package"></a>封裝位置：檔案系統 (封裝) 

如果您的 Azure SSIS IR 已布建但沒有 SSISDB，則會自動選取**檔案系統 (套件) ** ，而您可以自行加以選取。 如果已選取，請完成下列步驟。

   ![在 [設定] 索引標籤上設定屬性-[檔案系統] (封裝) ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)
   
   1. 藉由提供通用命名慣例 (UNC) 路徑來指定要執行的封裝， (使用 `.dtsx` [**封裝路徑**] 方塊中的) 。 您可以藉由選取 **[流覽檔案] [儲存體]** 或 [手動輸入路徑]，來流覽並選取您的套件。 例如，如果您將封裝儲存在 Azure 檔案儲存體中，其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` 。 
   
   1. 如果您在不同的檔案中設定封裝，您也必須在 [設定路徑] 方塊中使用) ，提供設定檔的 UNC 路徑 (`.dtsConfig` 。 **Configuration path** 您可以流覽並選取您的設定，方法是選取 **[流覽檔案] [儲存體]** ，或手動輸入其路徑。 例如，如果您將設定儲存在 Azure 檔案儲存體中，其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` 。

   1. 指定用來存取封裝和設定檔的認證。 如果您先前已輸入 (**Windows 驗證**) 的封裝執行認證值，您可以選取 [與**封裝執行認證相同**] 核取方塊來重複使用它們。 否則，請在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入套件存取認證的值。 例如，如果您將封裝和設定儲存在 Azure 檔案儲存體中，則網域是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。 

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這麼做，請選取其旁邊的 [ **AZURE 金鑰保存庫**] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您的值的密碼名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的。 如果您尚未這麼做，請務必將您的金鑰保存庫存取權授與 Data Factory 受控識別。 您也可以直接以下列格式輸入您的密碼： `<key vault linked service name>/<secret name>/<secret version>` 。 

      這些認證也會用來存取「執行封裝」工作中的子封裝，這些是由自己的路徑和封裝中指定的其他設定所參考。 

   1. 如果您在透過 SQL Server Data Tools (SSDT) 建立套件時使用了**EncryptAllWithPassword**或**EncryptSensitiveWithPassword**保護層級，請在 [**加密密碼**] 方塊中輸入密碼的值。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
      
      如果您使用**EncryptSensitiveWithUserKey**保護層級，請在設定檔中重新輸入敏感性值，或在 [ **SSIS 參數**]、[**連接管理員**] 或 [**屬性覆寫**] 索引標籤上， (看到下列) 。
      
      如果您使用**EncryptAllWithUserKey**保護層級，則不受支援。 您需要重新設定您的套件，以透過 SSDT 或 `dtutil` 命令列公用程式使用另一個保護層級。 

   1. 針對 [記錄層級]****，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [**自訂**] 核取方塊。 
   
   1. 如果您想要使用可在套件中指定的標準記錄提供者來記錄封裝執行，請在 [**記錄路徑**] 方塊中提供其 UNC 路徑來指定記錄檔資料夾。 您可以選取 **[流覽檔案] [儲存體**]，或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，您的記錄路徑會是 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個個別的封裝執行建立子資料夾，並在執行 SSIS 套件活動執行識別碼之後，以及每五分鐘產生一次記錄檔。 
   
   1. 指定認證以存取您的記錄檔資料夾。 如果您先前已輸入套件存取認證的值 (參閱上述) ，您可以選取 [與**封裝存取認證相同**] 核取方塊來重複使用它們。 否則，請在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入記錄存取認證的值。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
   
   針對先前提到的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

##### <a name="package-location-file-system-project"></a>封裝位置：檔案系統 (專案) 

如果您選取 [**檔案系統] (專案) **做為封裝位置，請完成下列步驟。

   ![在 [設定] 索引標籤上設定屬性-[檔案系統] (專案) ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   1. 指定要執行的封裝，方法是提供專案檔的 UNC 路徑 (`.ispac` 並在 [**專案路徑**] 方塊中使用) ，並在 [ `.dtsx` **封裝名稱**] 方塊中，使用您專案的)  (封裝檔案。 您可以流覽並選取您的專案，方法是選取 **[流覽檔案] [儲存體]** ，或手動輸入其路徑。 例如，如果您將專案儲存在 Azure 檔案儲存體中，其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` 。

   1. 指定用來存取專案和封裝檔案的認證。 如果您先前已輸入 (**Windows 驗證**) 的封裝執行認證值，您可以選取 [與**封裝執行認證相同**] 核取方塊來重複使用它們。 否則，請在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入套件存取認證的值。 例如，如果您將專案和封裝儲存在 Azure 檔案儲存體中，則網域會是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。 

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這麼做，請選取其旁邊的 [ **AZURE 金鑰保存庫**] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您的值的密碼名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的。 如果您尚未這麼做，請務必將您的金鑰保存庫存取權授與 Data Factory 受控識別。 您也可以直接以下列格式輸入您的密碼： `<key vault linked service name>/<secret name>/<secret version>` 。 

      這些認證也會用來存取相同專案所參考之「執行封裝」工作中的子封裝。 

   1. 如果您在透過 SSDT 建立套件時使用了**EncryptAllWithPassword**或**EncryptSensitiveWithPassword**保護層級，請在 [**加密密碼**] 方塊中輸入密碼的值。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
      
      如果您使用**EncryptSensitiveWithUserKey**保護層級，請在 [ **SSIS 參數**]、[**連線管理員**] 或 [**屬性覆寫**] 索引標籤上重新輸入您的機密值 () 下方查看。
      
      如果您使用**EncryptAllWithUserKey**保護層級，則不受支援。 您需要重新設定您的套件，以透過 SSDT 或 `dtutil` 命令列公用程式使用另一個保護層級。 

   1. 針對 [記錄層級]****，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [**自訂**] 核取方塊。 
   
   1. 如果您想要使用可在套件中指定的標準記錄提供者來記錄封裝執行，請在 [**記錄路徑**] 方塊中提供其 UNC 路徑來指定記錄檔資料夾。 您可以選取 **[流覽檔案] [儲存體**]，或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，您的記錄路徑會是 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個個別的封裝執行建立子資料夾，並在執行 SSIS 套件活動執行識別碼之後，以及每五分鐘產生一次記錄檔。 
   
   1. 指定認證以存取您的記錄檔資料夾。 如果您先前已輸入套件存取認證的值 (參閱上述) ，您可以選取 [與**封裝存取認證相同**] 核取方塊來重複使用它們。 否則，請在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入記錄存取認證的值。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
   
   針對先前提到的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

##### <a name="package-location-embedded-package"></a>封裝位置：內嵌封裝

如果您選取 [**內嵌套件**] 做為封裝位置，請完成下列步驟。

   ![設定 [設定] 索引標籤上的屬性-內嵌套件](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)

   1. 拖放您的封裝，從檔案資料夾執行或**上傳**至提供的方塊。 您的套件會自動壓縮並內嵌在活動裝載中。 內嵌之後，您可以稍後**下載**您的套件以進行編輯。 您也可以將內嵌套件**參數**化，方法是將它指派給可用於多個活動的管線參數，進而優化管線裝載的大小。 
   
   1. 如果您的內嵌封裝並非全部加密，而且我們偵測到在其中使用「執行封裝」工作，則會自動選取 [**執行封裝**工作] 核取方塊，並自動新增相關的子封裝及其檔案系統參考，讓您也可以將其內嵌。 
   
      如果無法偵測到使用「執行封裝」工作，您必須手動選取 [**執行封裝**工作] 核取方塊，並加入相關的子封裝及其檔案系統參考，以供您同時內嵌。 如果子封裝使用 SQL Server 的參考，請確定您的 Azure SSIS IR 可以存取 SQL Server。  目前不支援使用子封裝的專案參考。
   
   1. 如果您在透過 SSDT 建立套件時使用了**EncryptAllWithPassword**或**EncryptSensitiveWithPassword**保護層級，請在 [**加密密碼**] 方塊中輸入密碼的值。 
   
      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這麼做，請選取旁邊的 [ **AZURE 金鑰保存庫**] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您的值的密碼名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的。 如果您尚未這麼做，請務必將您的金鑰保存庫存取權授與 Data Factory 受控識別。 您也可以直接以下列格式輸入您的密碼： `<key vault linked service name>/<secret name>/<secret version>` 。
      
      如果您使用**EncryptSensitiveWithUserKey**保護層級，請在設定檔中重新輸入敏感性值，或在 [ **SSIS 參數**]、[**連接管理員**] 或 [**屬性覆寫**] 索引標籤上， (看到下列) 。
      
      如果您使用**EncryptAllWithUserKey**保護層級，則不受支援。 您需要重新設定您的套件，以透過 SSDT 或 `dtutil` 命令列公用程式使用另一個保護層級。

   1. 針對 [記錄層級]****，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [**自訂**] 核取方塊。 
   
   1. 如果您想要使用可在套件中指定的標準記錄提供者來記錄封裝執行，請在 [**記錄路徑**] 方塊中提供其 UNC 路徑來指定記錄檔資料夾。 您可以選取 **[流覽檔案] [儲存體**]，或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，您的記錄路徑會是 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個個別的封裝執行建立子資料夾，並在執行 SSIS 套件活動執行識別碼之後，以及每五分鐘產生一次記錄檔。 
   
   1. 在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入其值，以指定要存取記錄檔資料夾的認證。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
   
   針對先前提到的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

##### <a name="package-location-package-store"></a>封裝位置：封裝存放區

如果您選取 [**封裝存放區**] 做為封裝位置，請完成下列步驟。

   ![在 [設定] 索引標籤上設定屬性-封裝存放區](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png)
   
   1. 針對 [**套件存放區名稱**]，選取附加至您的 AZURE SSIS IR 的現有套件存放區。

   1. 藉由在 `.dtsx` [**封裝路徑**] 方塊中，從選取的封裝存放區提供路徑 (，指定要執行的封裝，而不) 。 如果選取的封裝存放區位於檔案系統/Azure 檔案儲存體的頂端，您可以藉由選取 **[流覽檔案儲存體**] 來流覽並選取您的套件，否則您可以使用的格式輸入其路徑 `<folder name>\<package name>` 。 您也可以透過 SQL Server Management Studio (SSMS) 類似[舊版 SSIS 封裝存放區](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)，將新封裝匯入到選取的封裝存放區。 如需詳細資訊，請參閱[使用 Azure-SSIS IR 套件存放區管理 SSIS 套件](https://docs.microsoft.com/azure/data-factory/azure-ssis-integration-runtime-package-store) \(英文\)。

   1. 如果您在不同的檔案中設定您的套件，您需要在 [設定路徑] 方塊中，提供設定檔的 UNC 路徑 (與 `.dtsConfig`) 。 **Configuration path** 您可以流覽並選取您的設定，方法是選取 **[流覽檔案] [儲存體]** ，或手動輸入其路徑。 例如，如果您將設定儲存在 Azure 檔案儲存體中，其路徑為 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` 。

   1. 選取 [設定**存取認證**] 核取方塊，選擇是否要分別指定要存取設定檔的認證。 當選取的封裝存放區位於 Azure SQL 受控執行個體所裝載的 SQL Server 資料庫 (MSDB) 上，或者也不會儲存您的設定檔時，就需要進行這項操作。
   
      如果您先前已輸入 (**Windows 驗證**) 的封裝執行認證值，您可以選取 [與**封裝執行認證相同**] 核取方塊來重複使用它們。 否則，請在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入設定存取認證的值。 例如，如果您將設定儲存在 Azure 檔案儲存體中，則網域是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。 

      或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這麼做，請選取其旁邊的 [ **AZURE 金鑰保存庫**] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您的值的密碼名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的。 如果您尚未這麼做，請務必將您的金鑰保存庫存取權授與 Data Factory 受控識別。 您也可以直接以下列格式輸入您的密碼： `<key vault linked service name>/<secret name>/<secret version>` 。

   1. 如果您在透過 SSDT 建立套件時使用了**EncryptAllWithPassword**或**EncryptSensitiveWithPassword**保護層級，請在 [**加密密碼**] 方塊中輸入密碼的值。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
      
      如果您使用**EncryptSensitiveWithUserKey**保護層級，請在設定檔中重新輸入敏感性值，或在 [ **SSIS 參數**]、[**連接管理員**] 或 [**屬性覆寫**] 索引標籤上， (看到下列) 。
      
      如果您使用**EncryptAllWithUserKey**保護層級，則不受支援。 您需要重新設定您的套件，以透過 SSDT 或 `dtutil` 命令列公用程式使用另一個保護層級。 

   1. 針對 [記錄層級]****，針對您的套件執行選取預先定義的記錄範圍。 如果您想要改為輸入自訂的記錄名稱，請選取 [**自訂**] 核取方塊。 
   
   1. 如果您想要使用可在套件中指定的標準記錄提供者來記錄封裝執行，請在 [**記錄路徑**] 方塊中提供其 UNC 路徑來指定記錄檔資料夾。 您可以選取 **[流覽檔案] [儲存體**]，或手動輸入其路徑，以流覽並選取您的記錄檔資料夾。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，您的記錄路徑會是 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` 。 在此路徑中，會為每個個別的封裝執行建立子資料夾，並在執行 SSIS 套件活動執行識別碼之後，以及每五分鐘產生一次記錄檔。 
   
   1. 在 [**網域**]、[使用者**名稱**] 和 [**密碼**] 方塊中輸入其值，以指定要存取記錄檔資料夾的認證。 例如，如果您將記錄儲存在 Azure 檔案儲存體中，則網域是 `Azure` 、使用者名稱是 `<storage account name>` ，而密碼是 `<storage account key>` 。 或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值 (查看上述) 。
   
   針對先前提到的所有 UNC 路徑，完整檔案名必須少於260個字元。 目錄名稱必須少於248個字元。

#### <a name="ssis-parameters-tab"></a>[SSIS 參數] 索引標籤

在 [執行 SSIS 套件活動] 的 [ **SSIS 參數**] 索引標籤上，完成下列步驟。

   ![在 [SSIS 參數] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

   1. 如果您的 Azure SSIS IR 正在執行，則會選取**SSISDB**作為您的封裝位置，並清除 [**設定**] 索引標籤上的 [**手動專案**] 核取方塊，並顯示您所選取專案中的現有 SSIS 參數和 SSISDB 中的封裝，讓您為其指派值。 否則，您可以逐一輸入它們，以手動為其指派值。 請確定它們存在且已正確輸入，您的套件執行才會成功。 
   
   1. 如果您在透過 SSDT 和檔案系統建立封裝時使用了**EncryptSensitiveWithUserKey**保護層級** (封裝) **、**檔案系統 (專案) **、**內嵌封裝**或**封裝存放區**已選取為封裝位置，則您也需要重新輸入您的敏感性參數，以便在此索引標籤上指派值給它們。 
   
   當您將值指派給參數時，您可以使用運算式、函式、Data Factory 系統變數，以及 Data Factory 管線參數或變數來加入動態內容。

   或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這麼做，請選取其旁邊的 [ **AZURE 金鑰保存庫**] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您的值的密碼名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的。 如果您尚未這麼做，請務必將您的金鑰保存庫存取權授與 Data Factory 受控識別。 您也可以直接以下列格式輸入您的密碼： `<key vault linked service name>/<secret name>/<secret version>` 。 

#### <a name="connection-managers-tab"></a>[連線管理員] 索引標籤

在 [執行 SSIS 套件活動] 的 [**連線管理員**] 索引標籤上，完成下列步驟。

   ![在 [連線管理員] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

   1. 如果您的 Azure SSIS IR 正在執行，則會在 [設定] 索引標籤上選取 [ **SSISDB** ] 做為您的套件位置，而 [**設定**] 索引標籤上的 [**手動專案**] 核取方塊則會顯示，讓您可以將值指派給其屬性。 否則，您可以逐一輸入它們，以手動將值指派給其屬性。 請確定它們存在且已正確輸入，您的套件執行才會成功。 
   
   1. 如果您在透過 SSDT 和檔案系統建立封裝時使用了**EncryptSensitiveWithUserKey**保護層級** (封裝) **、**檔案系統 (專案) **、**內嵌封裝**或**封裝存放區**已選取為封裝位置，則您也需要重新輸入您的敏感性連線管理員屬性，以便在此索引標籤上指派值給它們。 
   
   當您將值指派給連接管理員屬性時，您可以使用運算式、函式、Data Factory 系統變數，以及 Data Factory 管線參數或變數來加入動態內容。 

   或者，您可以使用儲存在 Azure Key Vault 中的秘密作為其值。 若要這麼做，請選取其旁邊的 [ **AZURE 金鑰保存庫**] 核取方塊。 選取或編輯現有的金鑰保存庫連結服務，或建立一個新的。 然後選取您的值的密碼名稱和版本。 當您建立或編輯金鑰保存庫連結服務時，您可以選取或編輯現有的金鑰保存庫，或建立一個新的。 如果您尚未這麼做，請務必將您的金鑰保存庫存取權授與 Data Factory 受控識別。 您也可以直接以下列格式輸入您的密碼： `<key vault linked service name>/<secret name>/<secret version>` 。 

#### <a name="property-overrides-tab"></a>屬性覆寫索引標籤

在 [執行 SSIS 套件活動] 的 [**屬性覆寫**] 索引標籤上，完成下列步驟。

   ![在 [屬性覆寫] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   1. 逐一輸入所選封裝中現有屬性的路徑，以手動為其指派值。 請確定它們存在且已正確輸入，您的套件執行才會成功。 例如，若要覆寫使用者變數的值，請以下列格式輸入其路徑： `\Package.Variables[User::<variable name>].Value` 。 
   
   1. 如果您在透過 SSDT 和檔案系統建立封裝時使用了**EncryptSensitiveWithUserKey**保護層級** (封裝) **、**檔案系統 (專案) **、**內嵌封裝**或**封裝存放區**已選取為封裝位置，則您也需要重新輸入您的敏感性封裝內容，以便在此索引標籤上指派值給這些屬性。 
   
   當您將值指派給封裝屬性時，您可以使用運算式、函式、Data Factory 系統變數，以及 Data Factory 管線參數或變數來加入動態內容。

   在 [設定檔] 和 [ **SSIS 參數**] 索引標籤中指派的值，可以使用 [**連線管理員**] 或 [**屬性覆寫**] 索引標籤來 在 [**連接管理員**] 索引標籤上指派的值也可以使用 [**屬性覆寫**] 索引標籤加以覆寫。

若要驗證管線設定，請選取工具列上的 [**驗證**]。 若要關閉 [**管線驗證報告**]，請選取 [] **>>** 。

若要將管線發佈到 Data Factory，請選取 [**全部發佈**]。 

### <a name="run-the-pipeline"></a>執行管道
在此步驟中，您會觸發管線執行。 

1. 若要觸發管線執行，請選取工具列上的 [**觸發**程式]，然後選取 [**立即觸發**]。 

   ![立即觸發](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在 [管線執行] 視窗中，選取 [完成]。 

### <a name="monitor-the-pipeline"></a>監視管線

1. 切換至左側的 [監視] 索引標籤。 您會看到管線執行及其狀態以及其他資訊，例如**執行開始**時間。 若要重新整理檢視，請選取 [重新整理]。

   ![管線執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. 選取 [動作]**** 資料行中的 [檢視活動執行]**** 連結。 因為管線只有一個活動，所以您只會看到一個活動執行。 這是「執行 SSIS 套件」活動。

   ![活動執行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 針對 SQL server 中的 SSISDB 資料庫執行下列查詢，以確認已執行封裝。 

   ```sql
   select * from catalog.executions
   ```

   ![確認封裝執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 您也可以從管線活動執行的輸出取得 SSISDB 執行識別碼，並使用此識別碼來檢查 SQL Server Management Studio 中更完整的執行記錄和錯誤訊息。

   ![取得執行識別碼。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線

您也可以建立管線的排程觸發程式，以便管線依排程執行，例如每小時或每日。 如需範例，請參閱[建立資料處理站 - 資料處理站 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="run-a-package-with-powershell"></a>使用 PowerShell 執行套件
在本節中，您會使用 Azure PowerShell 來建立 Data Factory 管線，其中包含執行 SSIS 套件的 Execute SSIS 套件活動。 

依照[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)中的逐步指示，安裝最新的 Azure PowerShell 模組。

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>使用 Azure SSIS IR 建立 data factory
您可以使用已布建 Azure SSIS IR 的現有資料處理站，或使用 Azure SSIS IR 建立新的 data factory。 遵循[教學課程：透過 PowerShell 將 SSIS 套件部署至 Azure](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)中的逐步指示。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線 
在此步驟中，您要使用執行 SSIS 套件活動建立管線。 此活動會執行您的 SSIS 套件。 

1. `RunSSISPackagePipeline.json`使用類似下列範例的內容，在資料夾中建立名為的 JSON 檔案 `C:\ADF\RunSSISPackage` 。

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

   若要執行儲存在檔案系統/Azure 檔案儲存體中的封裝，請輸入您的封裝和記錄位置屬性的值，如下所示：

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

   若要在檔案系統/Azure 檔案儲存體中儲存的專案內執行封裝，請輸入套件位置屬性的值，如下所示：

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

3. 若要建立管線**RunSSISPackagePipeline**，請執行**set-azdatafactoryv2pipeline** Cmdlet。

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

### <a name="run-the-pipeline"></a>執行管道
使用**set-azdatafactoryv2pipeline** Cmdlet 來執行管線。 Cmdlet 會傳回管線執行識別碼，方便後續監視。

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
在上一個步驟中，您已依需求執行管線。 您也可以建立排程觸發程式，依排程（例如每小時或每日）執行管線。

1. `MyTrigger.json`在資料夾中， `C:\ADF\RunSSISPackage` 使用下列內容建立名為的 JSON 檔案： 
        
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
1. 執行**start-azdatafactoryv2trigger 指令程式**，以建立觸發程式。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 觸發程序預設處於已停止狀態。 執行**start-azdatafactoryv2trigger** Cmdlet 來啟動觸發程式。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. 藉由執行**start-azdatafactoryv2trigger** Cmdlet，確認觸發程式已啟動。 

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

   針對 SQL server 中的 SSISDB 資料庫執行下列查詢，以確認已執行封裝。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>後續步驟
請參閱下列部落格文章：
- [使用 Azure Data Factory 管線中的 SSIS 活動，現代化和擴充您的 ETL/ELT 工作流程](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)