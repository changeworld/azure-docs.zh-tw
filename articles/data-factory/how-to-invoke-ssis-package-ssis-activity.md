---
title: 使用執行 SSIS 套件活動執行 SSIS 套件
description: 本文介紹如何使用執行 SSIS 包活動在 Azure 數據工廠管道中運行 SQL 伺服器整合服務 (SSIS) 包。
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
ms.date: 11/14/2019
ms.openlocfilehash: f505313b37d5289a5af10c40ede7f376eab4841d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605949"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>在 Azure Data Factory 中使用 Execute SSIS 套件活動執行 SSIS 套件

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介紹如何使用執行 SSIS 包活動在 Azure 數據工廠管道中運行 SQL 伺服器整合服務 (SSIS) 包。 

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

如果尚未按照教學中的分步說明建立 Azure-SSIS 整合執行時[(IR)。設定 Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md)。

## <a name="run-a-package-in-the-azure-portal"></a>在 Azure 入口網站中執行套件
在本節中,您可以使用數據工廠使用者介面 (UI) 或應用使用運行 SSIS 包的執行 SSIS 包活動創建數據工廠管道。

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線
在此步驟中,您可以使用數據工廠 UI 或應用創建管道。 您要將 Execute SSIS 套件活動新增至管線，並設定它來執行 SSIS 套件。 

1. 在 Azure 入口中的資料工廠概述或主頁上,選擇 **「作者&監視器」** 磁貼以在單獨的選項卡中啟動數據工廠 UI 或應用。 

   ![Data Factory 首頁](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   在 [現在就開始吧]**** 頁面中，選取 [建立管線]****。 

   ![開始使用頁面](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. 在 **"活動"** 工具箱中,展開 **"常規**"。 然後,將**執行 SSIS 包**活動拖動到管道設計器表面。 

   ![將執行 SSIS 套件活動拖曳至設計工具介面](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. 在"執行 SSIS 套件"活動的 **「一般**」選項卡上,提供活動的名稱和說明。 設置可選**的超時**和**重試**值。

   ![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. 在"執行 SSIS 套件"活動的 **"設定'** 選項卡上,選擇要執行套件的 Azure-SSIS IR。 如果包使用 Windows 身份驗證存取資料儲存(例如,本地 SQL 伺服器或檔案共享或 Azure 檔),請選擇**Windows 身份驗證**複選方塊。 在 **「網域**、**使用者名稱**和**密碼**」框中輸入包執行認證的值。 

    或者,您可以使用存儲在 Azure 密鑰保管庫中的秘密作為其值。 為此,請選擇相關憑據旁邊的**AZURE 密鑰 VAULT**複選框。 選擇或編輯現有金鑰保管庫連結服務或創建新密鑰保管庫連結服務。 然後選擇憑據值的機密名稱或版本。

    創建或編輯金鑰保管庫連結服務時,可以選擇或編輯現有密鑰保管庫或創建新密鑰保管庫。 如果尚未授予數據工廠託管對密鑰保管庫的標識訪問許可權,請確保授予數據工廠託管身份訪問許可權。 您也可以直接以以下格式輸入機密: `<Key vault linked service name>/<secret name>/<secret version>`. 如果包需要 32 位元執行時才能運行,請選擇**32 位元執行時**複選框。

   在**套件位置**,請選擇**SSISDB,****檔案系統 (套件,****檔案系統 )** 或**嵌入式套件**。 如果選擇**SSISDB**作為套件位置,如果 Azure-SSIS IR 預配了 Azure SQL 資料庫伺服器或託管實例託管的 SSIS 目錄 (SSISDB),則指定要運行到 SSISDB 中的套件。 

    如果 Azure-SSIS IR 正在執行,並且「**手動項目」** 複選框已清除,請瀏覽並從 SSISDB 中選擇現有資料夾、專案、包或環境。 選擇 **「刷新」** 可從 SSISDB 取得新添加的資料夾、專案、包或環境,以便它們可用於瀏覽和選擇。 要流覽或選擇包執行的環境,必須事先配置專案,以將這些環境添加為 SSISDB 下同一資料夾中的引用。 有關詳細資訊,請參閱[建立並映射 SSIS 環境](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)。

   針對 [記錄層級]****，針對您的套件執行選取預先定義的記錄範圍。 如果要改為輸入自訂日誌記錄名稱,請選擇 **「自訂**」複選框。 

   ![在 [設定] 索引標籤上設定屬性 - 自動化](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   如果 Azure-SSIS IR 未執行或選擇了 **「手動項目」** 複選框,請直接以以下格式從 SSISDB`<folder name>/<project name>/<package name>.dtsx`輸入`<folder name>/<environment name>`套件和環境路徑: 與 。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   如果選擇**檔案系統(包)** 作為套件位置(如果 Azure-SSIS IR 預配而不提供 SSISDB),則透過在`.dtsx`**套件路徑**框中提供套件檔 () 的通用命名約定 (UNC) 路徑來指定要運行的包。 例如,如果將套件儲存在 Azure 檔中,則其包`\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`路徑為 。 
   
   如果在單獨的檔中配置包,則還需要在`.dtsConfig`**「設定」路徑**框中提供設定檔 () 中的 UNC 路徑。 例如,如果將設定儲存在 Azure 檔中,則其設定`\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`路徑為 。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   如果選擇**檔案系統(Project)** 作為套件位置,請在 **「專案路徑**」 框中提供專案檔`.dtsx``.ispac`的 UNC 路徑 ( ) 和**套件名稱**框中的專案套件檔 ( ) 來指定要執行的套件。 例如,如果將專案儲存在 Azure 檔中,則路徑`\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`為 。

   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   接下來,指定訪問專案、包或配置檔的憑據。 如果以前輸入了包執行憑據的值(請參閱上一個),則可以通過選中"**與包執行憑據相同**「複選框來重用它們。 否則,請在 **「網域**、**使用者名稱**」和 **「密碼」** 框中輸入包存取認證的值。 例如,如果在 Azure 檔中儲存專案、包或設定,則`Azure`網域為`<storage account name>`,使用者名為`<storage account key>`,並且密碼為 。 

   或者,您可以使用存儲在密鑰保管庫中的機密作為其值(請參閱上一頁)。 這些憑據用於訪問執行包任務中的包和子包,所有這些包都來自它們自己的路徑或同一專案,以及包括包中指定的配置。 

   如果選擇 **「嵌入」包**作為包位置,請拖放套件以運行,或將其從資料夾**上傳**到提供的框中。 您的包將自動壓縮並嵌入到活動負載中。 嵌入後,您可以稍後**下載**包進行編輯。 還可以通過將嵌入式包分配給可用於多個活動的管道參數來**參數化**,從而優化管道負載的大小。 如果您的嵌入式包未全部加密,並且我們檢測到其中使用執行包任務,將自動選中**執行包任務**複選框,並將自動添加包含其文件系統引用的相關子包,以便您也嵌入它們。 如果無法檢測到執行套件工作的使用方式,您必須手動選擇 **「執行包任務**」複選框,並添加相關子包及其檔案系統引用,以便您也嵌入它們。 如果子包使用 SQL Server 引用,請確保 Azure-SSIS IR 可以存取 SQL 伺服器。  當前不支援對子包使用專案引用。
   
   ![在 [設定] 索引標籤上設定屬性 - 手動](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   如果您在透過 SQL 伺服器資料工具建立套件時使用了**EncryptAll WithPassword**或**Encrypt敏感密碼**保護等級,請在 **「加密密碼」** 框中輸入密碼的值。 或者,您可以使用存儲在密鑰保管庫中的機密作為其值(請參閱上一頁)。 如果使用**加密敏感使用者金鑰**保護等級,請在設定檔或**SSIS 參數**、**連接管理器**或**屬性覆蓋**選項卡上重新輸入您的敏感值(請參閱稍後)。 

   如果您使用**加密AllWithUserKey**保護級別,則不支援該保護級別。 您需要透過 SQL Server`dtutil`資料工具或命令列實用程式重新配置套件以使用其他保護等級。 
   
   針對 [記錄層級]****，針對您的套件執行選取預先定義的記錄範圍。 如果要改為輸入自訂日誌記錄名稱,請選擇 **「自訂**」複選框。 如果要在使用可在包中指定的標準日誌提供程式之外記錄包執行,請在 **「日誌記錄」路徑**框中提供其 UNC 路徑來指定日誌資料夾。 例如,如果將紀錄儲存在 Azure 檔中,則紀錄記錄路徑為`\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`。 在此路徑中為每個運行的各個包創建一個子資料夾,並命名為執行 SSIS 包活動運行 ID,其中每五分鐘生成一次日誌檔。 
   
   最後,指定訪問日誌資料夾的憑據。 如果以前輸入了包訪問憑據的值(請參閱上一個),則可以通過選中"**與包訪問憑據相同**「複選框來重用它們。 否則,請在 **「網域**」、「**使用者名**」和 **「密碼」** 框中輸入日誌記錄訪問認證的值。 例如,如果將紀錄儲存在 Azure 檔中,則網`Azure`域為,`<storage account name>`使用者名為`<storage account key>`, 密碼為 。 

    或者,您可以使用存儲在密鑰保管庫中的機密作為其值(請參閱上一頁)。 這些憑據用於存儲日誌。 
   
   對於前面提到的所有 UNC 路徑,完全限定的檔名必須小於 260 個字元。 目錄名稱必須小於 248 個字元。

1. 在「執行 SSIS 套件」活動的**SSIS 參數**選項卡上,如果 Azure-SSIS IR 正在運行,則選擇**SSISDB**作為套件位置,並清除 **「設定」** 選項卡上的 **「手動條目**」複選框,將顯示所選專案或 SSISDB 套件中的現有 SSIS 參數,以便您為其分配值。 否則,您可以逐個輸入它們,以手動為其分配值。 請確保它們存在並正確輸入,以便包執行成功。 
   
   如果在透過 SQL 伺服器資料工具和**檔案系統(包)** 或**檔案系統(專案)** 建立套件時使用**Encrypt 敏感與 UserKey**保護等級,則還需要重新輸入敏感參數,以在設定檔或此選項卡上為其分配值。 
   
   為參數分配值時,可以使用運算式、函數、資料工廠系統變數和資料工廠管道參數或變數添加動態內容。 或者,您可以使用存儲在密鑰保管庫中的機密作為其值(請參閱上一頁)。

   ![在 [SSIS 參數] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. 在「執行 SSIS 套件」活動的 **「連接管理器**」選項卡上,如果 Azure-SSIS IR 正在運行,則選擇**SSISDB**作為套件位置,並清除**設定「** 選項卡上的 **」手動條目**「複選框」,將顯示所選專案或 SSISDB 套件中的現有連接管理器,以便您為其屬性分配值。 否則,您可以逐個輸入它們,以手動為其屬性分配值。 請確保它們存在並正確輸入,以便包執行成功。 
   
   如果在透過 SQL 伺服器資料工具和**檔案系統(包)** 或**檔案系統(專案)** 建立套件時使用**加密敏感與 UserKey**保護等級,則還需要重新輸入敏感連線管理員屬性,以在設定檔或此選項卡上為其分配值。 
   
   將值分配給連接管理器屬性時,可以使用運算式、函數、資料工廠系統變數和資料工廠管道參數或變數添加動態內容。 或者,您可以使用存儲在密鑰保管庫中的機密作為其值(請參閱上一頁)。

   ![在 [連線管理員] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. 在"執行 SSIS 包"活動的屬性**覆蓋**選項卡上,逐個輸入所選包中現有屬性的路徑,以便手動分配值。 請確保它們存在並正確輸入,以便包執行成功。 例如,要覆蓋使用者變數的值,請輸入其路徑,其格式為: `\Package.Variables[User::<variable name>].Value`。 
   
   如果在透過 SQL 伺服器資料工具和**檔案系統(包)** 或**檔案系統(專案)** 建立套件時使用**加密敏感與 UserKey**保護等級,則還需要重新輸入敏感屬性,以在設定檔或此選項卡上為其分配值。 
   
   將值分配給屬性時,可以使用運算式、函數、資料工廠系統變數和資料工廠管道參數或變數添加動態內容。

   ![在 [屬性覆寫] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   通過使用**連接管理器**或**屬性覆蓋**選項卡,可以覆蓋配置檔和**SSIS 參數**選項卡上分配的值。 通過使用 **「屬性覆蓋」** 選項卡,也可以覆蓋連接**管理器**選項卡上分配的值。

1. 要驗證管道配置,請在工具列上選擇 **「驗證**」。 要關閉**管管檢查報告****>>**,請選擇 。

1. 要將管道發佈到數據工廠,請選擇 **「全部發佈**」。。 

### <a name="run-the-pipeline"></a>執行管道
在此步驟中，您會觸發管線執行。 

1. 要觸發管道運行,請在工具列上選擇 **「觸發器**」,然後選擇 **「立即觸發**」。。 

   ![立即觸發](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. 在 [管線執行]**** 視窗中，選取 [完成]****。 

### <a name="monitor-the-pipeline"></a>監視管線

1. 切換至左側的 [監視]**** 索引標籤。 您將看到管道運行及其狀態以及其他資訊,如**運行開始**時間。 若要重新整理檢視，請選取 [重新整理]****。

   ![管線執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. 選取 [動作]**** 資料行中的 [檢視活動執行]**** 連結。 您只看到一個活動在運行,因為管道只有一個活動。 這是執行 SSIS 包活動。

   ![活動執行](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. 針對 SQL 伺服器中的 SSISDB 資料庫運行以下查詢,以驗證包是否執行。 

   ```sql
   select * from catalog.executions
   ```

   ![確認封裝執行](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 您還可以從執行的管道活動的輸出中獲取 SSISDB 執行 ID,並使用 ID 在 SQL Server 管理工作室中檢查更全面的執行日誌和錯誤訊息。

   ![取得執行識別碼。](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線

您還可以為管道創建計畫觸發器,以便管道按計劃運行,例如每小時或每天。 如需範例，請參閱[建立資料處理站 - 資料處理站 UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)。

## <a name="run-a-package-with-powershell"></a>使用 PowerShell 執行套件
在本節中,使用 Azure PowerShell 創建具有運行 SSIS 包的「執行 SSIS 包」活動的資料工廠管道。 

依照[如何安裝和設定 Azure PowerShell](/powershell/azure/install-az-ps)中的逐步指示，安裝最新的 Azure PowerShell 模組。

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>使用 Azure SSIS IR 建立資料工廠
您可以使用已預配 Azure SSIS IR 的現有資料工廠,也可以使用 Azure-SSIS IR 創建新的資料工廠。 按照本教學中的分步說明[:透過 PowerShell 將 SSIS 包部署到 Azure。](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>使用 Execute SSIS 套件活動建立管線 
在此步驟中，您要使用執行 SSIS 套件活動建立管線。 此活動會執行您的 SSIS 套件。 

1. 在*C:_ADF_RunSSIS 包*資料夾中創建名為*RunSSISPackagePipeline.json*的 JSON 檔,內容類似於以下範例。

   > [!IMPORTANT]
   > 在保存檔之前,替換物件名稱、描述和路徑、屬性或參數值、密碼和其他變數值。 
    
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
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
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

   要執行儲存在檔案系統、檔案分享或 Azure 檔案中的套件,請輸入套件的值和紀錄位置屬性,如下所示:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
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
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
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

   要在儲存在檔案系統、檔案共享或 Azure 檔案中的項目執行套件,請輸入套件位置屬性的值,如下所示:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
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

   要執行嵌入套件,請輸入包位置屬性的值,如下所示:

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

2. 在 Azure PowerShell 中,切換到*C:\ADF_RunSSIS 包*資料夾。

3. 要建立管道**RunSSIS 套件管道**,請運行**集-AzDataFactoryV2管道**cmdlet。

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   下面是範例輸出:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>執行管道
使用**Invoke-AzDataFactoryV2Pipeline** cmdlet 執行管道。 Cmdlet 會傳回管線執行識別碼，方便後續監視。

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>監視管線

執行下列 PowerShell 程式碼以持續檢查管線執行狀態，直到完成複製資料為止。 在 PowerShell 視窗中複製或貼上以下文稿,然後選擇「輸入」。 

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

您還可以使用 Azure 門戶監視管道。 如需逐步指示，請參閱[監視管線](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)：

### <a name="schedule-the-pipeline-with-a-trigger"></a>使用觸發程序排程管線
在上一步中,您可以按需運行管道。 您還可以創建計畫觸發器以按計劃運行管道,例如每小時或每天。

1. 在*C:_ADF_RunSSIS 套件*資料夾中創建名為*MyTrigger.json*的 JSON 檔,內容如下: 
        
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
    
1. 在 Azure PowerShell 中,切換到*C:\ADF_RunSSIS 包*資料夾。
1. 運行**集-阿茲達工廠V2Trigger** cmdlet,這將創建觸發器。 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 觸發程序預設處於已停止狀態。 通過運行**啟動-AzDataFactoryV2Trigger** cmdlet 啟動觸發器。 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. 通過運行**Get-AzDataFactoryV2Trigger** cmdlet 確認觸發器啟動。 

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

   針對 SQL 伺服器中的 SSISDB 資料庫運行以下查詢,以驗證包是否執行。 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>後續步驟
請參閱下列部落格文章：
- [透過 Azure 資料工廠管道中的 SSIS 活動實現 ETL/ELT 工作流的現代化和擴展](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
