---
title: 針對 SSIS 整合執行時間中的封裝執行進行疑難排解
description: 本文提供 SSIS 整合執行時間中 SSIS 套件執行的疑難排解指導方針
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
ms.openlocfilehash: b4902e1fb7a2a181d3d5b2ce2ac6d1d458500fce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844177"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>針對 SSIS 整合執行時間中的封裝執行進行疑難排解

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文包含當您在 SSIS Integration runtime 中執行 SQL Server Integration Services (SSIS) 套件時，可能會發現最常見的錯誤。 它描述可能的原因和解決錯誤的動作。

## <a name="where-to-find-logs-for-troubleshooting"></a>哪裡可以找到要進行疑難排解的記錄

使用 Azure Data Factory 入口網站來檢查 SSIS 套件執行活動的輸出。 輸出會包含執行結果、錯誤訊息和作業識別碼。 如需詳細資訊，請參閱 [監視管線](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)。

使用 (SSISDB) 的 SSIS 目錄來檢查執行的詳細記錄。 如需詳細資訊，請參閱 [監視正在執行的封裝和其他作業](/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)。

## <a name="common-errors-causes-and-solutions"></a>常見錯誤、原因和解決方案

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>錯誤訊息：「連接逾時已過期」或「服務在處理您的要求時發生錯誤。 請再試一次」。

以下是可能的原因和建議的動作：
* 資料來源或目的地已多載。 檢查資料來源或目的地的負載，並查看它是否有足夠的容量。 例如，如果您使用 Azure SQL Database，請考慮在資料庫可能會有時間時相應增加。
* SSIS 整合執行時間與資料來源或目的地之間的網路不穩定，尤其是當連線在內部部署與 Azure 之間的連線時。 依照下列步驟，在 SSIS 套件中套用重試模式：
  * 請確定您的 SSIS 套件可在失敗時重新執行，而不會產生副作用 (例如，資料遺失或資料重複) 。
  * 在 [**一般**] 索引標籤上設定 [**執行 SSIS 套件**] 活動的 **重試** 和 **重試間隔**。 ![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * 若為 ADO.NET 和 OLE DB 來源或目的地元件，請在 SSIS 封裝或 SSIS 活動連線管理員中設定 **ConnectRetryCount** 和 **ConnectRetryInterval** 。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>錯誤訊息：「ADO NET 來源無法取得連接」 ...」 使用建立與 SQL Server 的連接時發生網路相關或實例特定的錯誤。 找不到或無法存取伺服器。」

此問題通常表示資料來源或目的地無法從 SSIS 整合執行階段中存取。 原因可能有所不同。 請嘗試下列動作：
* 請確定您已正確地傳遞資料來源或目的地名稱/ip。
* 請確定已正確設定防火牆。
* 如果您的資料來源或目的地位於內部部署環境，請確定您的虛擬網路已正確設定：
  * 您可以藉由在相同的虛擬網路中布建 Azure VM，來確認問題是否來自虛擬網路設定。 然後檢查是否可以從 Azure VM 存取資料來源或目的地。
  * 您可以在將 [AZURE ssis 整合執行時間加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)中，找到使用具有 SSIS 整合執行時間之虛擬網路的詳細資料。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>錯誤訊息：「ADO NET 來源無法取得連接」 ...」 使用「無法建立 managed 連線管理員」。

可能的原因是封裝中使用的 ADO.NET 提供者未安裝在 SSIS integration runtime 中。 您可以使用自訂安裝程式來安裝提供者。 您可以在 [AZURE SSIS integration runtime 的自訂設定](how-to-configure-azure-ssis-ir-custom-setup.md)中，找到自訂設定的詳細資料。

### <a name="error-message-the-connection--is-not-found"></a>錯誤訊息：「連接 ' ...」找不到 "

舊版 SQL Server Management Studio (SSMS) 中的已知問題可能會造成此錯誤。 如果使用 SSMS 進行部署的機器上未安裝套件中包含的自訂元件 (例如：SSIS Azure Feature Pack 或合作夥伴元件)，則 SSMS 會移除該元件並造成錯誤。 將 [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) 升級至已修正問題的最新版本。

### <a name="error-messagessis-executor-exit-code--1073741819"></a>錯誤訊息：「SSIS 執行程式結束代碼：-1073741819」。

* 可能的原因和建議的動作：
  * 此錯誤可能是因為在多執行緒中平行執行多個 Excel 來源或目的地時，Excel 來源和目的地的限制。 您可以解決這項限制，方法是變更 Excel 元件以依序執行，或將它們分成不同的封裝，並透過「執行封裝工作」觸發，並將 ExecuteOutOfProcess 屬性設定為 True。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>錯誤訊息：「磁碟空間不足」

此錯誤表示本機磁片會在 SSIS integration runtime 節點中使用。 檢查您的封裝或自訂安裝程式是否耗用大量磁碟空間：
* 如果您的套件使用了該磁片，它會在封裝執行完成後釋出。
* 如果您的自訂安裝程式使用該磁片，您必須停止 SSIS 整合執行時間、修改您的腳本，然後再次啟動整合執行時間。 您為自訂安裝程式指定的整個 Azure blob 容器將會複製到 SSIS integration runtime 節點，因此請檢查該容器下是否有任何不必要的內容。

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>錯誤訊息：「無法從 master 取得資源。 IntegrationServices. ScaleoutContract. MasterResponseFailedException： Code：300004。 描述：載入檔案 "* * *" failed "。

* 可能的原因和建議的動作：
  * 如果 SSIS 活動正在從檔案系統執行封裝 (封裝檔案或專案檔) ，如果無法使用您在 SSIS 活動中提供的封裝存取認證來存取專案、封裝或設定檔，就會發生此錯誤。
    * 如果您使用 Azure 檔案：
      * 檔案路徑的開頭應為 \\ \\ \<storage account name\> . file.core.windows.net\\\<file share path\>
      * 網域應為 "Azure"
      * 使用者名稱應該是 \<storage account name\>
      * 密碼應為 \<storage access key\>
    * 如果您使用內部部署檔案，請檢查是否已正確設定 VNet、套件存取認證和許可權，讓您的 Azure SSIS 整合執行時間可以存取內部部署檔案共用

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>錯誤訊息： "file name ' ... '在連接中指定的無效」

* 可能的原因和建議的動作：
  * 指定了不正確檔案名
  * 請確定您在連線管理員中使用 FQDN (完整功能變數名稱) 而不是短時間

### <a name="error-message-cannot-open-file-"></a>錯誤訊息：「無法開啟檔案 ' ... '」

當封裝執行在 SSIS integration runtime 的本機磁片中找不到檔案時，就會發生此錯誤。 請嘗試下列動作：
* 請勿在 SSIS integration runtime 中執行的封裝中使用絕對路徑。 請改用目前的執行工作目錄 ( ) 或暫存資料夾 (% TEMP% ) 。
* 如果您需要將某些檔案保存在 SSIS integration runtime 節點上，請依照 [自訂安裝程式](how-to-configure-azure-ssis-ir-custom-setup.md)中所述的方式來準備檔案。 執行完成之後，工作目錄中的所有檔案都會清除。
* 使用 Azure 檔案儲存體而不是將檔案儲存在 SSIS integration runtime 節點中。 如需詳細資訊，請參閱 [使用 Azure 檔案共用](/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>錯誤訊息：「資料庫 ' SSISDB ' 已達到其大小配額」

可能的原因是在 Azure SQL Database 中或在 SQL 受控執行個體中建立的 SSISDB 資料庫已達到其配額。 請嘗試下列動作：
* 請考慮增加資料庫的 DTU。 您可以在 [邏輯伺服器的 SQL Database 限制](../azure-sql/database/resource-limits-logical-server.md)中找到詳細資料。
* 檢查您的套件是否會產生許多記錄。 若是如此，您可以設定彈性作業來清除這些記錄。 如需詳細資料，請參閱[使用 Azure 彈性資料庫工作清除 SSISDB 記錄](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>錯誤訊息：「資料庫的要求限制為 .。。已經到達。」

如果在 SSIS integration runtime 中平行執行許多套件，則可能會發生此錯誤，因為 SSISDB 已達到其要求限制。 請考慮增加 SSISDB 的 DTC 以解決此問題。 您可以在 [邏輯伺服器的 SQL Database 限制](../azure-sql/database/resource-limits-logical-server.md)中找到詳細資料。

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>錯誤訊息：「SSIS 作業失敗，發生非預期的操作狀態： ...」

錯誤大多是暫時性問題所致，所以請嘗試重新執行封裝執行。 依照下列步驟，在 SSIS 套件中套用重試模式：

* 請確定您的 SSIS 套件可在失敗時重新執行，而不會產生副作用 (例如，資料遺失或資料重複) 。
* 在 [**一般**] 索引標籤上設定 [**執行 SSIS 套件**] 活動的 **重試** 和 **重試間隔**。 ![在 [一般] 索引標籤上設定屬性](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* 若為 ADO.NET 和 OLE DB 來源或目的地元件，請在 SSIS 封裝或 SSIS 活動連線管理員中設定 **ConnectRetryCount** 和 **ConnectRetryInterval** 。

### <a name="error-message-there-is-no-active-worker"></a>錯誤訊息：「沒有作用中的背景工作角色」。

此錯誤通常表示 SSIS integration runtime 的狀態不良。 檢查 Azure 入口網站的狀態和詳細錯誤。 如需詳細資訊，請參閱 [AZURE SSIS 整合運行](./monitor-integration-runtime.md#azure-ssis-integration-runtime)時間。

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>錯誤訊息：「您的整合執行時間無法升級，而且最終將會停止運作，因為我們無法存取您為自訂安裝所提供的 Azure Blob 容器。」

當 SSIS 整合執行時間無法存取針對自訂安裝所設定的存放裝置時，就會發生此錯誤。 檢查您提供的共用存取簽章 (SAS) URI 是否有效且尚未過期。

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>錯誤訊息：「適用于 Analysis Services 的 Microsoft OLE DB 提供者。 ' Hresult： 0x80004005 Description： ' COM error： COM error： mscorlib;調用的目標已擲回例外狀況」

其中一個可能的原因是已針對 Azure Analysis Services 驗證設定 Azure AD Multi-Factor Authentication 啟用的使用者名稱或密碼。 SSIS 整合執行時間不支援此驗證。 嘗試使用 Azure Analysis Services authentication 的服務主體：

1. [依照自動化中的服務主體](../analysis-services/analysis-services-service-principal.md)描述來準備服務主體。
2. 在連線管理員中，設定 **使用特定的使用者名稱和密碼**：將 **AppID** 設定為使用者名稱，並 **clientSecret** 為密碼。

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>錯誤訊息：「ADONET 來源無法取得連接 {GUID}，出現下列錯誤訊息：使用受控識別時，使用者 ' NT AUTHORITY\ANONYMOUS Login ' 的登入失敗

當參數 *>connectusingmanagedidentity* 為 **True** 時，請確定您未將連線管理員的驗證方法設定為 **Active Directory 密碼驗證**。 您可以改為將它設定為 **SQL 驗證** ，如果設定了 *>connectusingmanagedidentity* ，則會忽略此項。

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>錯誤訊息：「0xC020801F at ...，OData Source [...]：無法從執行時間連接管理員取得 managed 連接」

其中一個可能的原因是，傳輸層安全性 (TLS) 不會在 OData 來源所需的 SSIS 整合執行時間中啟用。 您可以使用 [自訂安裝] 在 SSIS 整合執行時間中啟用 TLS。 在 [無法從 SSIS 連接 Project Online Odata](/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) ，以及 [自訂 Azure SSIS 整合執行時間的安裝程式](how-to-configure-azure-ssis-ir-custom-setup.md)時，可以找到更多詳細資料。

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>錯誤訊息：「使用作業 guid 要求預備工作 .。。失敗，因為發生錯誤：無法分派暫存作業，錯誤訊息： IntegrationServices. AisAgentCore. AisAgentException：無法載入資料 proxy。」

請確定您的 Azure SSIS 整合執行時間已設定 Self-Hosted 整合執行時間。 如需詳細資訊，請參閱 [設定 Self-Hosted IR 作為 ADF 中 Azure-SSIS IR 的 proxy](self-hosted-integration-runtime-proxy-ssis.md)。

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>錯誤訊息：「暫存工作狀態：失敗。 臨時工作錯誤： ErrorCode：2010，ErrorMessage：自我裝載的 Integration Runtime .。。離線」

確定已安裝並啟動您的 Self-Hosted 整合執行時間。 您可以在[建立和設定自我裝載整合運行](create-self-hosted-integration-runtime.md)時間中找到更多詳細資料

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>錯誤訊息：「暫存工作錯誤： ErrorCode：2906，ErrorMessage：封裝執行失敗。，輸出： {"OperationErrorMessages"： "錯誤：要求的 OLE DB 提供者 .。。未註冊。 如果未安裝64位驅動程式，請在32位模式下執行封裝 ...」

確定您的封裝中 OLE DB 連接器所使用的對應提供者已正確地安裝在 Self-Hosted 整合執行時間電腦上。 如需更多詳細資料，請參閱 [設定 Self-Hosted IR 作為 ADF 中 Azure-SSIS IR 的 proxy](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>錯誤訊息：「暫存工作錯誤： ErrorCode：2906，ErrorMessage：封裝執行失敗。，輸出： {"OperationErrorMessages"： "錯誤： FileLoadException：無法載入檔案或元件 ' WindowsAzure. '，版本 = ...，文化特性 = 中性，PublicKeyToken = 31bf3856ad364e35 ' 或其相依性的其中之一。 找到元件的資訊清單定義與元件參考不符。 '..."

其中一個可能原因是您的 Self-Hosted 整合執行時間未正確安裝或升級。 建議您下載並重新安裝最新的自我裝載整合執行時間。 您可以在[建立和設定自我裝載整合運行](create-self-hosted-integration-runtime.md#installation-best-practices)時間中找到更多詳細資料

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>錯誤訊息：「要求中繼資料時，需要連接。 如果您是離線工作，請取消核取 [SSIS] 功能表上的 [離線工作] 以啟用連接

* 可能的原因和建議的動作：
  * 如果在執行記錄中還有警告訊息「元件不支援使用具有 ConnectByProxy 值的連線管理員」設定 true」，這表示連接管理員會用於尚未支援「ConnectByProxy」的元件。 您可以在[設定 Self-Hosted IR 作為 ADF 中 Azure-SSIS IR 的 proxy](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy) ，找到支援的元件
  * 您可以在 [SSMS 報告](/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) 或您于 SSIS 封裝執行活動中指定的記錄資料夾中找到執行記錄。
  * vNet 也可以用來存取內部部署資料，以做為替代方案。 您可以在將[AZURE SSIS 整合執行時間加入虛擬網路](join-azure-ssis-integration-runtime-virtual-network.md)中找到更多詳細資料

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>錯誤訊息：「暫存工作狀態：失敗。 臨時工作錯誤：錯誤碼：2906，ErrorMessage：封裝執行失敗。，輸出： {"OperationErrorMessages"： "SSIS 執行程式結束代碼：-1. \ n"，"LogLocation"： "... \\SSISTelemetry \\ ExecutionLog \\ ... "，" effectiveIntegrationRuntime "：" ... "，" executionDuration "： ...，" durationInQueue "： {" integrationRuntimeQueue "： ...}}"

請確定 Self-Hosted 整合執行時間電腦上已安裝 Visual C++ 執行時間。 如需更多詳細資料，請參閱 [設定 Self-Hosted IR 作為 ADF 中 Azure-SSIS IR 的 proxy](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir)

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>多個封裝執行未預期地觸發

* 可能的原因和建議的動作：
  * ADF 預存程式活動或查閱活動可用來觸發 SSIS 封裝執行。 T-sql 命令可能會遇到暫時性問題，並觸發重新執行，這會導致多個封裝執行。
  * 使用 ExecuteSSISPackage 活動，以確保不會重新執行封裝執行，除非活動中的使用者設定重試計數。 詳細資料可在 [https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](./how-to-invoke-ssis-package-ssis-activity.md)
  * 藉由檢查是否已觸發執行，來調整您的 t-sql 命令以重新執行

### <a name="package-execution-takes-too-long"></a>封裝執行時間太長

以下是可能的原因和建議的動作：

* SSIS 整合執行時間上已排程過多的封裝執行。 所有這些執行都會在佇列中等候。
  * 使用此公式來判斷最大值：

    每個 IR 的最大平行執行計數 = 節點計數 * 每個節點的平行執行數上限
  * 若要瞭解如何設定每個節點的節點計數和平行執行上限，請參閱 [Azure Data Factory 中的建立 AZURE SSIS 整合運行](create-azure-ssis-integration-runtime.md)時間。
* SSIS 整合執行時間已停止或狀態不良。 若要瞭解如何檢查 SSIS 整合執行時間狀態和錯誤，請參閱 [AZURE ssis 整合運行](monitor-integration-runtime.md#azure-ssis-integration-runtime)時間。

我們也建議您在 [ **一般** ] 索引標籤上設定 timeout：在 [一般] 索引標籤 ![ 上設定屬性 ](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png) 。

### <a name="poor-performance-in-package-execution"></a>封裝執行效能不佳

請嘗試下列動作：

* 請確定 SSIS 整合執行時間與資料來源和目的地位於相同的區域中。

* 將封裝執行的記錄層級設定為 [ **效能** ]，以收集執行中每個元件的持續時間資訊。 如需詳細資訊，請參閱 [Integration Services (SSIS) 記錄](/sql/integration-services/performance/integration-services-ssis-logging)。

* 檢查 Azure 入口網站中的 IR 節點效能：
  * 如需有關如何監視 SSIS 整合執行時間的詳細資訊，請參閱 [AZURE ssis integration runtime](monitor-integration-runtime.md#azure-ssis-integration-runtime)。
  * 您可以藉由在 Azure 入口網站中查看資料處理站的計量，尋找 SSIS 整合執行時間的 CPU/記憶體歷程記錄。
    ![監視 SSIS 整合執行時間的計量](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)