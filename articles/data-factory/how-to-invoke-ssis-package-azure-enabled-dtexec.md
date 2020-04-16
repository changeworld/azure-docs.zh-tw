---
title: 使用開啟 Azure 的 dtexec 執行程式執行 SQL 伺服器整合服務 (SSIS) 套件
description: 瞭解如何使用啟用 Azure 的 dtexec 實用程式執行 SQL 伺服器整合服務 (SSIS) 套件。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: dce7fb87ee49aefdedf5653243fa5729eee34519
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414333"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>使用開啟 Azure 的 dtexec 執行程式執行 SQL 伺服器整合服務套件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介紹啟用 Azure 的 dtexec (AzureDTExec) 命令提示實用程式。 它用於在 Azure 資料工廠中的 Azure-SSIS 整合執行時 (IR) 上執行 SQL 伺服器整合服務 (SSIS) 套件。

傳統的 dtexec 實用程式附帶 SQL Server。 有關詳細資訊,請參閱[dtexec 實用程式](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 它通常由第三方協調程式或計劃程式(如 ActiveBatch 和控制-M)調用,用於在本地運行 SSIS 包。 

現代 AzureDTExec 實用程式附帶了 SQL 伺服器管理工作室 (SSMS) 工具。 第三方協調器或計劃程式也可以調用它,以在 Azure 中運行 SSIS 包。 它有助於將 SSIS 包提升、移動或遷移到雲。 遷移后,如果要在日常操作中繼續使用第三方協調器或計劃程式,他們現在可以調用 AzureDTExec 而不是 dtexec。

AzureDTExec 在資料工廠管道中作為執行 SSIS 包活動運行包。 有關詳細資訊,請參閱將[SSIS 包作為 Azure 數據工廠活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)運行。 

AzureDTExec 可透過 SSMS 設定為使用 Azure 活動目錄 (Azure AD) 應用程式,該應用程式在資料工廠中生成管道。 還可以將其設定為存取儲存套件的檔案系統、檔案共用或 Azure 檔。 根據為其調用選項提供的值,AzureDTExec會生成並運行具有"執行 SSIS 包"活動的唯一資料工廠管道。 為其選項使用相同的值調用 AzureDTExec 會重新執行現有管道。

## <a name="prerequisites"></a>Prerequisites
要使用 AzureDTExec,請下載並安裝最新版本的 SSMS,即版本 18.3 或更高版本。 請從[此網站](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)下載。

## <a name="configure-the-azuredtexec-utility"></a>設定 AzureDTExec 實用程式
在本地電腦上安裝 SSMS 還會安裝 AzureDTExec。 要設定其設定,請使用 **「以管理員身份執行**」選項啟動 SSMS。 然後選擇 **「** > **工具移移到 Azure** > **設定啟用 Azure 的 DTExec」。。**

![設定啟用 Azure 的 dtexec 選單](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

此操作將打開**AzureDTExecConfig**視窗,該視窗需要打開具有管理許可權才能寫入*AzureDTExec.設定*檔。 如果您尚未以管理員身份運行 SSMS,則將打開使用者帳戶控制 (UAC) 視窗。 輸入管理員密碼以提升您的許可權。

![設定啟用 Azure 的 dtexec 設定](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

在**AzureDTExecConfig**視窗中,輸入設定設定,如下所示:

- **應用程式 Id**:輸入使用在資料工廠中產生管道的正確權限創建的 Azure AD 應用的唯一識別碼。 有關詳細資訊,請參閱通過[Azure 門戶建立 Azure AD 應用和服務主體](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)。
- **身份驗證金鑰**:輸入 Azure AD 應用的身份驗證金鑰。
- **租戶 Id**:輸入 Azure AD 租戶的唯一識別碼,在該識別符下創建 Azure AD 應用。
- **數據工廠**:輸入資料工廠的名稱,其中根據調用 AzureDTExec 時提供的選項值生成具有執行 SSIS 包活動的唯一管道。
- **IRName**: 在資料工廠中輸入 Azure-SSIS IR 的名稱,在調用 AzureDTExec 時,在其通用命名約定 (UNC) 路徑中指定的包將在該名稱上運行。
- **管道名稱HashStrLen:** 輸入從調用 AzureDTExec 時提供的選項值生成的哈希字串的長度。 這些字串用於為在 Azure-SSIS IR 上運行包的數據工廠管道形成唯一名稱。 通常長度為 32 個字元就足夠了。
- **資源組**:輸入在其中創建資料工廠的 Azure 資源組的名稱。
- **訂閱 Id**:輸入 Azure 訂閱的唯一識別碼,根據該識別碼創建資料工廠。
- **LogAccessDomain:** 在寫入紀錄檔時輸入網域認證以存取其 UNC 路徑中的紀錄資料夾,這在指定**LogPath**且**LogLevel**不**為 null**時是必需的。
- **LogAccess密碼**:在寫入日誌檔時輸入密碼憑據以訪問其 UNC 路徑中的日誌資料夾,這在指定**LogPath**且**LogLevel**不**為 null**時是必需的。
- **LogAccessUserName:** 在寫入紀錄檔時輸入使用者名認證以存取其 UNC 路徑中的紀錄資料夾,這在指定**LogPath**且**LogLevel**不為**null**時是必需的。
- **紀錄等級**:輸入 Azure-SSIS IR 上套件執行的預定義**null、****基本**、**詳細**或**效能**選項的選定紀錄記錄範圍。
- **LogPath**: 輸入紀錄資料夾的 UNC 路徑,其中將 Azure-SSIS IR 上的套件執行中的日誌檔寫入該路徑。
- **包 AccessDomain**: 輸入網域認證以存取您在呼叫 AzureTExec 時指定的 UNC 路徑中的套件。
- **包存取密碼**:輸入密碼認證以存取呼叫 AzureDTExec 時指定的 UNC 路徑中的套件。
- **包存取使用者名稱**:輸入使用者名認證以存取您在調用 AzureDTExec 時指定的 UNC 路徑中的套件。

要將包和日誌檔案儲存在本地檔案系統或檔案共用中,請將 Azure-SSIS IR 加入連接到本地網路的虛擬網路,以便它可以獲取包並寫入日誌檔。 有關詳細資訊,請參閱將[Azure-SSIS IR 加入虛擬網路](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)。

為了避免以純文本形式顯示寫入*AzureDTExec.設定*檔的敏感值,我們將它們編碼為 Base64 編碼字串。 調用 AzureDTExec 時,所有 Base64 編碼的字串都解碼回其原始值。 您可以通過限制可以存取該檔的帳戶來進一步保護*AzureDTExec.設定*檔。

## <a name="invoke-the-azuredtexec-utility"></a>呼叫 AzureDTExec 實用程式
您可以在命令列提示符下調用 AzureDTExec,並在用例方案中為特定選項提供相關值。

實用程式安裝在`{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`。 您可以將其路徑添加到"PATH"環境變數,以便從任何位置調用它。

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

調用 AzureDTExec 提供了與調用 dtexec 類似的選項。 如需詳細資訊，請參閱 [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)。 以下是目前支援的選項:

- **/F[ile]**:載入儲存在檔案系統、檔案共用或 Azure 檔案中的套件。 作為這個選項的值,您可以在檔案系統、檔案共用或 Azure 檔及其 .dtsx 擴展名中為包檔指定 UNC 路徑。 如果指定的 UNC 路徑包含任何空格,則在整個路徑周圍放置引號。
- **/Conf_igFile]**: 指定要從中提取值的設定檔。 使用此選項,可以為包設置與設計時指定的配置不同的運行時配置。 您可以將不同的設定存儲在 XML 設定檔中,然後在包執行之前載入它們。 有關詳細資訊,請參閱[SSIS 套件設定](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)。 要指定這個選項的值,請使用檔案系統、檔案共用或 Azure 檔中的設定檔的 UNC 路徑及其 dtsConfig 副檔名。 如果指定的 UNC 路徑包含任何空格,則在整個路徑周圍放置引號。
- **/Conn_ection}**:為包中的現有連接管理員指定連接字串。 使用此選項,可以為套件中的現有連接管理員設置與設計時指定的連接管理器不同的運行時連接字串。 指定這個選項的值,如下所示: `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`。
- **/Set**: 覆蓋套件中的參數、變數、屬性、容器、日誌提供者、Foreach 枚舉器或連接的配置。 此選項可以指定多次。 指定這個選項的值,如下所示: `property_path;value`。 例如,`\package.variables[counter].Value;1`將變數的值`counter`覆蓋為 1。 可以使用 **「包配置**」嚮導查找、複製和貼上要覆蓋其值`property_path`的套件 中的項目的值。 有關詳細資訊,請參閱[套件設定精靈](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)。
- **/De[密碼]**:為配置了**加密密碼**/**加密**敏感密碼保護級別的包設置解密密碼。

> [!NOTE]
> 調用 AzureDTExec 具有其選項的新值將生成一個新管道,但選項 **/de_cript}** 除外。

## <a name="next-steps"></a>後續步驟

在調用 AzureDTExec 時生成並運行具有其中執行 SSIS 包活動的唯一管道後,可以在數據工廠門戶上監視它們。 如果要使用數據工廠協調/計劃數據工廠觸發器,也可以為其分配數據工廠觸發器。 有關詳細資訊,請參閱將[SSIS 包作為數據工廠活動](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)運行。

> [!WARNING]
> 生成的管道應僅由 AzureDTExec 使用。 其屬性或參數將來可能會更改,因此不要出於任何其他目的修改或重用它們。 修改可能會中斷 AzureDTExec。 如果發生這種情況,請刪除管道。 AzureDTExec會在下次調用管道時生成新管道。