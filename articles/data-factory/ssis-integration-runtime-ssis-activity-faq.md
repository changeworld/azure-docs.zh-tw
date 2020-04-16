---
title: 在 SSIS 整合時排除套件執行故障
description: 本文為 SSIS 整合執行時的 SSIS 套件執行提供了故障排除指南
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 8c85a652cde840336c51e1a5b5459f9dc591e0be
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414678"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>在 SSIS 整合時排除套件執行故障

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文包括在 SSIS 整合執行時執行 SQL 伺服器整合服務 (SSIS) 包時可能發現的最常見錯誤。 它描述了解決錯誤的潛在原因和操作。

## <a name="where-to-find-logs-for-troubleshooting"></a>在哪裡可以找到用於故障排除的紀錄

使用 Azure 數據工廠門戶檢查 SSIS 套件執行活動的輸出。 輸出包括執行結果、錯誤訊息和操作 ID。 有關詳細資訊,請參閱[監察管](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)。

使用 SSIS 目錄 (SSISDB) 檢查執行的詳細資訊紀錄。 有關詳細資訊,請參閱[監察正在執行的套件和其他操作](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)。

## <a name="common-errors-causes-and-solutions"></a>常見錯誤、原因和解決方案

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>錯誤消息:「連接超時已過期」或「服務在處理您的請求時遇到錯誤。 請再試一次」。

以下是潛在原因和建議的操作:
* 數據源或目標已過載。 檢查數據源或目標的負載,看看它是否有足夠的容量。 例如,如果使用 Azure SQL 資料庫,請考慮在資料庫可能超時時向上擴展。
* SSIS 整合式執行時與資料源或目標之間的網路不穩定,尤其是在連接是跨區域或本地和 Azure 之間時。 依以下步驟在 SSIS 套件中應用重試模式:
  * 確保您的 SSIS 包可以在失敗時重新運行,而不會造成副作用(例如,資料丟失或數據重複)。
  * 在 **「一般**![」 選項卡上設定執行**SSIS 套件**活動的**重試**,**並重新試 。**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * 對於ADO.NET和OLE DB源或目標元件,在SSIS包或 SSIS 活動中將連接管理員中的**ConnectRetryCount**和**連接重試間隔**設定。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>錯誤消息:"ADO NET 源未能獲取連接"..." 與"建立與 SQL Server 的連接時發生的與網路相關或特定於實例的錯誤。 找不到或無法存取伺服器。」

此問題通常表示資料來源或目的地無法從 SSIS 整合執行階段中存取。 原因可能有所不同。 請嘗試下列動作：
* 確保正確傳遞資料源或目標名稱/IP。
* 確保防火牆設置正確。
* 如果您的資料來源或目標位於本地,請確保虛擬網路設定正確:
  * 通過在同一虛擬網路中預配 Azure VM,可以驗證問題是否來自虛擬網路配置。 然後檢查是否可以從 Azure VM 訪問數據源或目標。
  * 您可以在[加入 Azure-SSIS 整合時到虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)中尋找有關將虛擬網路與 SSIS 整合執行時使用的詳細資訊。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>錯誤消息:"ADO NET 源未能獲取連接"..." 與"無法創建託管連接管理器"。

潛在原因是包中使用的ADO.NET提供程式未安裝在 SSIS 整合執行時中。 您可以使用自定義設置安裝提供程式。 您可以在[Azure-SSIS 整合時的自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)中找到有關自訂設置的更多詳細資訊。

### <a name="error-message-the-connection--is-not-found"></a>錯誤訊息:"連接'...'找不到"

舊版 SQL Server Management Studio (SSMS) 中的已知問題可能會造成此錯誤。 如果使用 SSMS 進行部署的機器上未安裝套件中包含的自訂元件 (例如：SSIS Azure Feature Pack 或合作夥伴元件)，則 SSMS 會移除該元件並造成錯誤。 將[SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)升級到已修復問題的最新版本。

### <a name="error-messagessis-executor-exit-code--1073741819"></a>錯誤消息:「SSIS 執行器退出代碼:-1073741819。

* 可能的原因和建議的動作：
  * 此錯誤可能是由於在多線程中並行執行多個 Excel 源或目標時,Excel 源和目標的限制。 您可以通過更改 Excel 元件以按順序執行來解決此限制,或者將它們分成不同的包,並透過「執行包任務」觸發,將 ExecuteOutOutOutProcess 屬性設定為 True。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>錯誤消息:"磁碟上沒有足夠的空間"

此錯誤表示本地磁碟在 SSIS 整合時節點中已用完。 檢查包或自訂設定是否佔用了大量磁碟空間:
* 如果磁碟被包使用,它將在包執行完成後釋放。
* 如果自定義設置使用磁碟,則需要停止 SSIS 整合執行時、修改文稿並再次啟動整合式執行時。 為自訂設置指定的整個 Azure Blob 容器將複製到 SSIS 整合執行時節點,因此請檢查該容器下是否有任何不必要的內容。

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>錯誤消息:「無法從主伺服器檢索資源。 微軟.SqlServer.整合服務.Scale.Scaleout合同.通用.主回應失敗例外:代碼:300004。 說明:載入檔"

* 可能的原因和建議的動作：
  * 如果 SSIS 活動正在從檔案系統(包檔案或專案檔)執行套件,則如果無法使用您在 SSIS 活動中提供的套件存取認證專案、包或設定檔,則會發生此錯誤
    * 如果使用 Azure 檔案:
      * 檔案路徑應從\\\\\<儲存帳戶名稱\>.file.core.windows.net\\\<檔案共享路徑開始\>
      * 域應為"Azure"
      * 使用者名稱應為\<儲存帳戶名稱\>
      * 密碼應儲存\<金鑰\>
    * 如果使用本地檔案,請檢查 VNet、包存取認證和權限是否設定正確,以便 Azure-SSIS 整合執行時可以存取本地檔案分享

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>錯誤訊息:"檔名 '...'在連接中指定無效"

* 可能的原因和建議的動作：
  * 指定不合法的檔案名稱
  * 確保您使用的是 FQDN(完全合格網域名稱),而不是在連接管理器中使用時間短

### <a name="error-message-cannot-open-file-"></a>錯誤訊息:"無法打開檔案'...'

當包執行在 SSIS 整合時的本地磁碟中找不到檔時,將發生此錯誤。 請嘗試下列動作：
* 不要使用在 SSIS 整合執行時執行的套件中的絕對路徑。 使用目前執行工作目錄 (.) 或暫存資料夾 (%TEMP%)相反。
* 如果需要在 SSIS 整合時節點上保留某些檔,請按照[自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)中所述準備這些檔。 執行完成後,將清理工作目錄中的所有檔。
* 使用 Azure 檔案而不是將檔案儲存在 SSIS 整合執行時節點中。 有關詳細資訊,請參閱[使用 Azure 檔案共享](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>錯誤消息:"数据庫'SSISDB'已達到其大小配額"

可能原因是在 Azure SQL 資料庫中建立的 SSISDB 資料庫，或您建立 SSIS 整合執行階段時的受控執行個體已達到其配額。 請嘗試下列動作：
* 請考慮增加資料庫的 DTU。 您可以在 [Azure SQL Database 伺服器的 SQL Database 資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)中找到詳細資料。
* 檢查您的套件是否會產生許多記錄。 若是如此，您可以設定彈性作業來清除這些記錄。 如需詳細資料，請參閱[使用 Azure 彈性資料庫工作清除 SSISDB 記錄](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>錯誤訊息:「資料庫的要求限制是 ...並已達到。

如果許多包在 SSIS 整合時並行運行,則由於 SSISDB 已達到其請求限制,可能會發生此錯誤。 請考慮增加 SSISDB 的 DTC 以解決此問題。 您可以在 [Azure SQL Database 伺服器的 SQL Database 資源限制](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)中找到詳細資料。

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>錯誤消息:"SSIS 操作失敗,操作狀態意外:..."

錯誤主要是由暫時性問題引起的,因此請嘗試重新運行包執行。 依以下步驟在 SSIS 套件中應用重試模式:

* 確保您的 SSIS 包可以在失敗時重新運行,而不會造成副作用(例如,資料丟失或數據重複)。
* 在 **「一般**![」 選項卡上設定執行**SSIS 套件**活動的**重試**,**並重新試 。**](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* 對於ADO.NET和OLE DB源或目標元件,在SSIS包或 SSIS 活動中將連接管理員中的**ConnectRetryCount**和**連接重試間隔**設定。

### <a name="error-message-there-is-no-active-worker"></a>錯誤消息:「沒有活動工作人員。

此錯誤通常意味著 SSIS 整合執行時處於不正常狀態。 檢查 Azure 門戶的狀態和詳細錯誤。 有關詳細資訊,請參閱[Azure-SSIS 整合執行時](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)。

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>錯誤消息:「無法升級集成運行時,並最終停止工作,因為我們無法訪問為自定義設置提供的 Azure Blob 容器。

當 SSIS 整合時無法存取為自訂設定設定的儲存時,將發生此錯誤。 檢查您提供的共享訪問簽名 (SAS) URI 是否有效且尚未過期。

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>錯誤消息:「微軟 OLE DB 分析服務提供者。 'Hresult: 0x80004005 描述:' COM 錯誤: COM 錯誤: mscorlib;調用的目標已引發異常"

一個可能的原因是啟用了 Azure 多重身份驗證的使用者名或密碼配置為 Azure 分析服務身份驗證。 SSIS 整合式執行時不支援此身份驗證。 嘗試將服務主體用於 Azure 分析服務身份驗證:

1. 使用[服務主體](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)準備服務主體,如自動化中所述。
2. 在連接管理器中,配置**使用特定的使用者名稱和密碼**:將**AppID**設置為使用者名,**用戶端密碼**為密碼。

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>錯誤消息:"ADONET 源未能獲取連接 [GUID],並顯示以下錯誤消息:使用者使用託管標識時登錄失敗"NT AUTHORITY_ANONYMOUS LOGON"

確保當參數*ConnectUsing 託管識別***為 true**時,不要將連線管理員的驗證方法設定為**動作目錄密碼認證**。 您可以將其設定為**SQL 身份驗證**,如果設定了*ConnectUsing 託管標識*,則忽略該身份驗證。

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>錯誤消息:"0xC020801F 在 ..., OData 源 [...] : 無法從運行時連接管理器獲取託管連接"

一個潛在的原因是傳輸層安全 (TLS) 未在 OData 源所需的 SSIS 整合式執行時啟用。 您可以使用「自訂」設定在 SSIS 整合時啟用 TLS。 更多詳細資訊可在[無法從 SSIS 連接專案連線 Odata](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis)和[Azure-SSIS 整合式執行時的自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)中找到。

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>錯誤訊息:"請求具有操作 guid 的暫存任務...由於錯誤而失敗:無法使用錯誤消息調度暫存操作:Microsoft.SqlServer.整合服務.AisAgentCore.AisAgentException:無法載入資料代理。

確保 Azure-SSIS 整合執行時設定了自託管整合時。 更多細節可在[ADF 中配置自託管 IR 作為 Azure-SSIS IR 的代理](self-hosted-integration-runtime-proxy-ssis.md)中找到。

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>錯誤消息:「暫存任務狀態:失敗。 暫存任務錯誤:錯誤代碼:2010,錯誤訊息:自託管整合執行時...處於脫機狀態"

確保已安裝並啟動自託管集成運行時。 更多詳細資訊可在[建立及設定自託管整合時](create-self-hosted-integration-runtime.md), 找到

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>錯誤訊息:「暫存任務錯誤:錯誤代碼:2906,錯誤訊息:包執行失敗.,輸出:\"操作錯誤訊息":"錯誤:請求的 OLE 資料庫提供者...未註冊。 如果未安裝 64 位元驅動程式,請以 32 位模式運行包..."

確保包中的 OLE DB 連接器使用的相應提供程式正確安裝在自託管整合執行時電腦上。 更多詳細資訊可在[ADF 中設定自託管 IR 作為 Azure-SSIS IR 的代理中找到](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>錯誤消息:"暫存任務錯誤:錯誤代碼:2906,錯誤消息:包執行失敗.,輸出:{"操作錯誤消息":"錯誤:系統.IO.fileLoadException:無法載入文件或程式集'Microsoft.WindowsAzure.存儲,版本=...,區域性=中性,PublicKeyToken_31bf385ad364e35'或其依賴項之一。 定位程式集的清單定義與程式集引用不匹配。..."

一個潛在的原因是您的自託管集成運行時安裝不正確或升級。 建議下載並重新安裝最新的自託管集成運行時。 更多詳細資訊可在[建立及設定自託管整合時](create-self-hosted-integration-runtime.md#installation-best-practices), 找到

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>錯誤訊息:「請求元數據時需要連接。 如果脫機工作,請在 SSIS 功能表上取消選中"離線工作"以啟用連接"

* 可能的原因和建議的動作：
  * 如果執行日誌中還出現警告消息「元件不支援使用連接管理器,連接管理器具有連接ByProxy值設定為 true」,則這意味著連接管理器在尚未支援「ConnectByProxy」的元件上使用。 支援元件可在[ADF 中作為 Azure-SSIS IR 的代理設定自託管 IR 中找不到](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy)
  * 執行紀錄可以在[SSMS 報告](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports)或您在 SSIS 套件執行活動中指定的紀錄資料夾中找到。
  * vNet 還可用於訪問本地數據作為替代方法。 更多詳細資訊可在[Azure-SSIS 整合執行時連接到虛擬網路中找到](join-azure-ssis-integration-runtime-virtual-network.md)

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>錯誤消息:「暫存任務狀態:失敗。 暫存任務錯誤:錯誤代碼:2906,錯誤消息:包執行失敗.,輸出:\"操作錯誤消息":"SSIS 執行器退出代碼:-1.\n","Loglocation":"...\\SSIS遙測\\執行日\\誌 ...","有效集成運行時":"...","執行持續時間":","持續時間在佇列":[集成運行時佇列]:

確保可視化C++運行時安裝在自託管集成運行時計算機上。 更多詳細資訊可在[ADF 中設定自託管 IR 作為 Azure-SSIS IR 的代理中找到](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>意外觸發多個套件執行

* 可能的原因和建議的動作：
  * ADF 儲存過程活動或查找活動用於觸發 SSIS 包執行。 t-sql 命令可能會遇到暫時性問題並觸發重新運行,這將導致多個包執行。
  * 改用 ExecuteSSIS 套件活動,這可確保包執行不會重新運行,除非使用者在活動中設置重試計數。 詳細資訊可在[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * 通過檢查是否已觸發執行,優化 t-sql 命令以重新執行

### <a name="package-execution-takes-too-long"></a>套件執行時間過長

以下是潛在原因和建議的操作:

* 在 SSIS 整合執行時計畫執行的包太多。 所有這些處決都將在佇列中等待輪到他們。
  * 使用此公式確定最大值:

    每個 IR 的最大並行執行計數 = 節點計數 = 每個節點的最大並行執行數
  * 要瞭解如何設定節點計數和每個節點的最大並行執行,請參閱[在 Azure 資料工廠建立 Azure-SSIS 整合執行時](create-azure-ssis-integration-runtime.md)。
* SSIS 整合式執行時已停止或處於不正常狀態。 要瞭解如何檢查 SSIS 整合時狀態與錯誤,請參閱[Azure-SSIS 整合時](monitor-integration-runtime.md#azure-ssis-integration-runtime)。

我們還建議您在 **「一般**」選項卡上設置![超時: 在「](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)一般」選項卡 上設置屬性。

### <a name="poor-performance-in-package-execution"></a>套件執行效能差

請嘗試下列動作：

* 確保 SSIS 整合執行時與數據源和目標位於同一區域。

* 將套件執行的紀錄紀錄等級設定為 **「效能」** 以收集執行中每個元件的持續時間資訊。 有關詳細資訊,請參閱[整合服務 (SSIS) 紀錄紀錄](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)。

* 檢查 Azure 門戶中的 IR 節點效能:
  * 有關如何監視 SSIS 整合時的資訊,請參閱[Azure-SSIS 整合時](monitor-integration-runtime.md#azure-ssis-integration-runtime)。
  * 通過在 Azure 門戶中查看數據工廠的指標,可以找到 SSIS 整合時的 CPU/記憶體歷史記錄。
    ![監視 SSIS 整合時的指標](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
