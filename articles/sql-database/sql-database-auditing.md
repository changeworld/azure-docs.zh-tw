---
title: Azure SQL 稽核
description: 使用 Azure SQL 資料庫稽核來將資料庫事件追蹤至稽核記錄。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.custom: azure-synapse
ms.openlocfilehash: 57c4b22dfe6ef6cf44be64a4b5c042403f64ccf2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82096651"
---
# <a name="azure-sql-auditing"></a>Azure SQL 稽核

Azure [SQL Database](sql-database-technical-overview.md)和[azure Synapse 分析](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)的審核會追蹤資料庫事件，並將其寫入您 Azure 儲存體帳戶、log Analytics 工作區或事件中樞中的 audit 記錄。 

稽核也具備下列功能：

- 協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。

- 啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準合規性之 Azure 程式的詳細資訊，請參閱[Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在其中找到最新的 SQL Database 合規性認證清單。

> [!NOTE] 
> 本主題適用于 Azure SQL Database 和 Azure Synapse Analytics 資料庫。 為了簡單起見，在同時參考 Azure SQL Database 和 Azure Synapse 分析時，會使用 SQL Database。

## <a name="overview"></a><a id="overview"></a>概觀

您可以使用 SQL 資料庫稽核完成下列工作：

- **保留** 所選事件的稽核記錄。 您可以定義要稽核的資料庫動作類別。
- **報告** 資料庫活動。 您可以使用預先設定的報告和儀表板，快速地開始使用活動和事件報告。
- **分析** 報告。 您可以尋找可疑事件、異常活動及趨勢。

> [!IMPORTANT]
> - Azure SQL Database 的審核已針對可用性 & 效能進行優化。 在非常高的活動期間，Azure SQL Database 允許作業繼續執行，而不會記錄某些已審核的事件。

### <a name="auditing-limitations"></a>審核限制

- **不支援****進階儲存體**。
- 目前**不支援** **Azure Data Lake Storage Gen2 儲存體帳戶**的**階層命名空間**。
- 不支援在已暫停的**Azure SQL 資料倉儲**上啟用審核。 若要啟用審核，請繼續資料倉儲。

#### <a name="define-server-level-vs-database-level-auditing-policy"></a><a id="server-vs-database-level"></a>定義伺服器層級與資料庫層級的稽核原則

您可以針對特定資料庫定義稽核原則，或將稽核原則定義為預設伺服器原則：

- 伺服器原則會套用至伺服器上所有現有和新建立的資料庫。

- 如果*已啟用伺服器審核*，它*一律會套用到資料庫*。 不論資料庫稽核資料是什麼，都會稽核資料庫。

- 除了在伺服器上啟用資料庫或資料倉儲的審核功能之外，也*不*會覆寫或變更伺服器審核的任何設定。 這兩種稽核將會並存。 換句話說，系統將會對資料庫進行兩次相同的稽核；一次是由伺服器原則，一次是由資料庫原則。

   > [!NOTE]
   > 您應該避免同時啟用伺服器審核和資料庫 blob 審核，除非：
    > - 您想要為特定資料庫使用不同的*儲存體帳戶*、*保留週期*或*Log Analytics 工作區*。
    > - 您想要針對伺服器上不同於其餘資料庫的特定資料庫，稽核其事件類型或類別。 例如，您可能只需要針對特定資料庫稽核資料表插入。
   >
   > 否則，我們建議您只啟用伺服器層級的審核，並讓所有資料庫的資料庫層級審核保持停用狀態。

## <a name="set-up-auditing-for-your-server"></a><a id="setup-auditing"></a>設定伺服器的稽核

預設稽核原則包含所有動作和下列一組動作群組，這會稽核對資料庫執行的所有查詢和預存程序，以及成功和失敗的登入：
  
  - BATCH_COMPLETED_GROUP
  - SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP
  - FAILED_DATABASE_AUTHENTICATION_GROUP
  
您可以使用 PowerShell 設定不同動作和動作群組類型的稽核，如[使用 Azure PowerShell 管理 SQL 資料庫稽核](#manage-auditing)一節中所述。

Azure SQL Database 稽核會在稽核記錄中的字元欄位儲存 4000 個字元的資料。 當**陳述式**或從可稽核的動作傳回的 **data_sensitivity_information** 值包含超過 4000 個字元，超過前 4000 個字元的任何資料將會**截斷且不會稽核**。
下節描述使用 Azure 入口網站進行稽核的設定。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 瀏覽至 SQL 資料庫/伺服器窗格中 [安全性] 標題下的 [稽核]****。
3. 如果您想要設定伺服器稽核原則，可以選取資料庫稽核頁面上的 [檢視伺服器設定]**** 連結。 然後，您可以檢視或修改伺服器稽核設定。 伺服器稽核原則會套用至此伺服器上所有現有和新建立的資料庫。

    ![瀏覽窗格](./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png)

4. 如果您偏向在資料庫層級啟用稽核，請將 [稽核]**** 切換到 [開啟]****。 如果已啟用伺服器稽核，資料庫設定的稽核將會與伺服器稽核並存。

5. 您有多個選項可用來設定要寫入 audit 記錄的位置。 您可以使用事件中樞（預覽），將記錄寫入至 Azure 儲存體帳戶、Log Analytics 工作區，以供取用 Azure 監視器記錄（預覽）或事件中樞取用。 您可以設定這些選項的任何組合，並將稽核記錄寫入至每個組合。
  
   ![儲存體選項](./media/sql-database-auditing-get-started/auditing-select-destination.png)
   
### <a name="audit-to-storage-destination"></a><a id="audit-storage-destination"></a>對儲存體目的地的審核

若要設定將稽核記錄寫入至儲存體帳戶，請選取 [儲存體]****，然後開啟 [儲存體詳細資料]****。 選取將儲存記錄的 Azure 儲存體帳戶，然後選取保留期間。 然後按一下 [ **確定**]。 早于保留期限的記錄會遭到刪除。

- [保留週期] 的預設值為0（無限制保留）。 您可以變更此值，方法是在設定儲存體帳戶以進行審核時，將 [**保留（天數）** ] 滑杆移至 [**儲存體設定**]。
  - 如果您將保留期間從0（無限制的保留）變更為任何其他值，請注意保留期只會套用至保留值變更之後所寫入的記錄（保留設定為無限制的期間所寫入的記錄，即使已啟用保留期）。

  ![storage account](./media/sql-database-auditing-get-started/auditing_select_storage.png)

#### <a name="remarks"></a>備註

- Audit 記錄會寫入 Azure 訂用帳戶中 Azure Blob 儲存體的**附加 blob**
- 若要針對伺服器或資料庫層級的 audit 事件設定不可變的記錄存放區，請遵循[Azure 儲存體提供的指示](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes)（請確定您在設定不可變的 blob 儲存體時，已選取 [**允許其他附加**]）。
- 您可以將 audit 記錄寫入 VNet 或防火牆後方的 Azure 儲存體帳戶。 如需特定指示，請參閱將[Audit 寫入 VNet 和防火牆後方的儲存體帳戶](create-auditing-storage-account-vnet-firewall.md)。
- 設定您的稽核設定之後，您可以開啟新的威脅偵測功能，並設定電子郵件以接收安全性警示。 使用威脅偵測時，您會接收與指示潛在安全性威脅的異常資料庫活動相關的主動式警示。 如需詳細資訊，請參閱[開始使用威脅偵測](sql-database-threat-detection-get-started.md)。
- 如需有關記錄格式、儲存體資料夾階層和命名慣例的詳細資訊，請參閱 [Blob 稽核記錄格式參考](https://go.microsoft.com/fwlink/?linkid=829599)。
- 使用 AAD 驗證時，失敗的登入記錄「不會」** 顯示在 SQL 稽核記錄中。 若要檢視失敗的登入稽核記錄，您需要瀏覽 [Azure Active Directory 入口網站]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)，其中會記錄這些事件的詳細資料。
- [唯讀複本](sql-database-read-scale-out.md)上的審核功能會自動啟用。 如需有關儲存體資料夾階層、命名慣例和記錄格式的進一步詳細資訊，請參閱[SQL Database Audit 記錄檔格式](sql-database-audit-log-format.md)。 

### <a name="audit-to-log-analytics-destination"></a><a id="audit-log-analytics-destination"></a>對 Log Analytics 目的地的審核
  
若要設定將稽核記錄寫入至 Log Analytics 工作區，請選取 [Log Analytics (預覽)]****，然後開啟 [Log Analytics 詳細資料]****。 選取或建立將寫入記錄的 Log Analytics 工作區，然後按一下 [確定]****。
   
   ![LogAnalyticsworkspace](./media/sql-database-auditing-get-started/auditing_select_oms.png)

### <a name="audit-to-event-hub-destination"></a><a id="audit-event-hub-destination"></a>審核至事件中樞目的地

> [!WARNING]
> 在具有 SQL 集區的伺服器上啟用審核，會**導致 SQL 集區再次繼續，並再次重新暫停，** 這可能會產生計費費用。
> 無法在已暫停的 SQL 集區上啟用審核。 若要啟用它，請取消暫停 SQL 集區。

若要設定將稽核記錄寫入至事件中樞，請選取 [事件中樞 (預覽)]****，然後開啟 [事件中樞詳細資料]****。 選取要寫入記錄的事件中樞，然後按一下 [確定]****。 請確定事件中樞與您的資料庫和伺服器位於相同的區域。

   ![Eventhub](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a name="analyze-audit-logs-and-reports"></a><a id="subheading-3"></a>分析稽核記錄和報告

如果您選擇將 audit 記錄寫入 Azure 監視器記錄：

- 使用[Azure 入口網站](https://portal.azure.com)。  開啟相關的資料庫。 在資料庫的 [稽核]**** 頁面頂端，按一下 [檢視稽核記錄]****。

    ![檢視稽核記錄](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- 然後，您有兩種方式可查看記錄：
    
    按一下 [ **Audit 記錄**] 頁面頂端的 [ **log analytics** ] 將會開啟 log analytics 工作區中的 [記錄] 視圖，您可以在其中自訂時間範圍和搜尋查詢。
    
    ![在 Log Analytics 工作區中開啟](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    按一下 [ **audit 記錄**] 頁面頂端的 [ **View 儀表板**] 將會開啟儀表板，其中會顯示 [審核記錄] 資訊，您可以在其中向下切入到安全性見解、存取機密資料等等。 此儀表板的設計目的是協助您取得資料的安全性見解。
    您也可以自訂時間範圍和搜尋查詢。 
    ![查看 Log Analytics 儀表板](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Log Analytics 儀表板](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics 安全性深入解析](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- 或者，您也可以從 Log Analytics 刀鋒視窗存取稽核記錄。 開啟 Log Analytics 工作區，然後在 [一般]**** 區段下，按一下 [記錄]****。 您可以從簡單的查詢開始，例如：「搜尋 "SQLSecurityAuditEvents"」** 以檢視稽核記錄。
    從這裡，您也可以使用[Azure 監視器記錄](../log-analytics/log-analytics-log-search.md)，對您的審核記錄資料執行先進的搜尋。 Azure 監視器記錄可讓您使用整合式搜尋和自訂儀表板，在您的所有工作負載和伺服器上輕鬆分析數百萬筆記錄，以提供您即時的 operational insights。 如需 Azure 監視器記錄搜尋語言和命令的其他實用資訊，請參閱[Azure 監視器記錄搜尋參考](../log-analytics/log-analytics-log-search.md)。

如果您選擇將稽核記錄寫入至事件中樞：

- 若要取用來自事件中樞的稽核記錄資料，您必須設定資料流取用事件並將其寫入至目標。 如需詳細資訊，請參閱 [Azure 事件中樞文件](../event-hubs/index.yml)。
- 系統會在 [Apache Avro](https://avro.apache.org/) \(英文\) 事件的主體中擷取事件中樞的稽核記錄，並使用以 UTF-8 編碼方式格式化的 JSON 來儲存。 若要讀取稽核記錄，您可以使用 [Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) 或類似工具來處理這種格式。

如果您選擇將稽核記錄寫入至 Azure 儲存體帳戶，您可使用數種方法來檢視記錄：

- 稽核記錄會在您於設定期間選擇的帳戶中彙總。 您可以使用[Azure 儲存體總管](https://storageexplorer.com/)之類的工具來探索 audit 記錄。 在 Azure 儲存體中，稽核記錄是以 Blob 檔案集合的方式儲存在名為 **sqldbauditlogs** 的容器內。 如需有關儲存體資料夾階層、命名慣例和記錄格式的進一步詳細資訊，請參閱[SQL Database Audit 記錄檔格式](https://go.microsoft.com/fwlink/?linkid=829599)。

- 使用[Azure 入口網站](https://portal.azure.com)。  開啟相關的資料庫。 在資料庫的 [稽核]**** 頁面頂端，按一下 [檢視稽核記錄]****。

    ![瀏覽窗格](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

    隨即開啟 [稽核記錄]****，您可以在其中檢視記錄。

  - 您可以按一下 [稽核記錄]**** 頁面頂端的 [篩選]**** 來檢視特定日期。
  - 切換 [稽核來源]****，即可在由「伺服器稽核原則」** 和「資料庫稽核原則」** 建立的稽核記錄之間切換。
  - 如果勾選 [只顯示 SQL 插入的稽核記錄]**** 核取方塊，只可以檢視 SQL 插入相關的稽核記錄。

       ![瀏覽窗格]( ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png)

- 使用系統函數 **sys.fn_get_audit_file** (T-SQL) 以表格格式傳回稽核記錄資料。 如需使用此函式的詳細資訊，請參閱 [sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql)。

- 使用 SQL Server Management Studio (SSMS 17 或更新版本) 中的 [合併稽核檔案]****：
    1. 從 SSMS 功能表中 **，選取** > [檔案] [**開啟** > ] [合併] [**Audit Files**]。

        ![瀏覽窗格](./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png)
    2. 隨即開啟 [新增稽核檔案]**** 對話方塊。 選取其中一個 [新增]**** 選項以選擇是否要從本機磁碟合併稽核檔案，或從 Azure 儲存體匯入稽核檔案。 您將需要提供您的 Azure 儲存體詳細資料和帳戶金鑰。

    3. 已新增要合併的所有檔案之後，請按一下 [確定]**** 以完成合併作業。

    4. 合併的檔案會在 SSMS 中開啟，您可以在其中檢視和分析該檔案，以及將其匯出至 XEL 或 CSV 檔案，或是匯出至資料表。

- 使用 Power BI。 您可以在 Power BI 中檢視和分析稽核記錄資料。 如需詳細資訊，以及若要存取可下載的範本，請參閱 [Analyzie audit log data in Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/) (在 Power BI 中分析稽核記錄資料)。
- 透過入口網站或使用如[Azure 儲存體總管](https://storageexplorer.com/)之類的工具，從您的 Azure 儲存體 blob 容器下載記錄檔。
  - 在您將記錄下載到本機之後，按兩下檔案，以在 SSMS 中開啟、檢視及分析記錄。
  - 您也可以透過 Azure 儲存體總管同時下載多個檔案。 若要執行這項作業，請以滑鼠右鍵按一下特定子資料夾，然後選取 [另存新檔]**** 儲存在本機資料夾。

- 其他方法：

  - 下載多個檔案或包含記錄檔的子資料夾後，可以在本機合併這些檔案，如先前所述的 SSMS 合併稽核檔案指示中所述。
  - 以程式設計方式檢視 Blob 稽核記錄：

    - 使用 PowerShell [查詢擴充事件檔案](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/)。

## <a name="production-practices"></a><a id="production-practices"></a>實際作法

<!--The description in this section refers to preceding screen captures.-->

### <a name="auditing-geo-replicated-databases"></a>稽核異地複寫資料庫

使用異地複寫資料庫，當您在主要資料庫啟用稽核，次要資料庫會有相同的稽核原則。 也可以在**次要伺服器**上啟用稽核，設定次要資料庫稽核，和主要資料庫分開。

- 伺服器層級 (**建議**)：啟動**主要伺服器**和**次要伺服器**上的稽核 - 將根據其個別的伺服器層級原則對主要和次要資料庫分開進行稽核。
- 資料庫層級：只能從主要資料庫稽核設定來設定次要資料庫的資料庫層級稽核。
  - 必須在「主要資料庫本身」** (而不是在伺服器上) 啟用稽核。
  - 在主要資料庫上啟用稽核之後，它也會在次要資料庫上變成啟用狀態。

    >[!IMPORTANT]
    >使用資料庫層級稽核時，次要資料庫的儲存體設定將會和主要資料庫上的設定完全相同，這會導致跨地區流量。 建議您只啟用伺服器層級稽核，並讓所有資料庫的資料庫層級稽核保留在停用狀態。

### <a name="storage-key-regeneration"></a>儲存體金鑰重新產生

在生產中，您可能會定期重新整理儲存體金鑰。 當您將稽核記錄寫入至 Azure 儲存體時，您需要在重新整理金鑰期間重新儲存稽核原則。 此程序如下：

1. 開啟 [儲存體詳細資料]****。 在 [儲存體存取金鑰]**** 方塊中，選取 [次要]****，然後按一下 [確定]****。 然後按一下稽核設定頁面頂端的 [儲存]****。

    ![瀏覽窗格](./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png)
2. 移至儲存體設定頁面，並重新產生主要存取金鑰。

    ![瀏覽窗格](./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png)
3. 返回稽核設定頁面，將儲存體存取金鑰從次要切換成主要，然後按一下 [確定]****。 然後按一下稽核設定頁面頂端的 [儲存]****。
4. 返回儲存體設定頁面，並重新產生次要存取金鑰 (為下一個金鑰重新整理週期做準備)。

## <a name="manage-azure-sql-server-and-database-auditing"></a><a id="manage-auditing"></a>管理 Azure SQL Server 和資料庫的審核

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

**PowerShell Cmdlet (包含其他篩選的 WHERE 子句支援)**：

- [建立或更新資料庫稽核原則（設定-AzSqlDatabaseAudit）](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [建立或補救伺服器稽核原則（設定-AzSqlServerAudit）](/powershell/module/az.sql/set-azsqlserveraudit)
- [取得資料庫稽核原則（AzSqlDatabaseAudit）](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [取得伺服器稽核原則（AzSqlServerAudit）](/powershell/module/az.sql/get-azsqlserveraudit)
- [移除資料庫稽核原則（移除-AzSqlDatabaseAudit）](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [移除伺服器稽核原則（移除-AzSqlServerAudit）](/powershell/module/az.sql/remove-azsqlserveraudit)

如需指令碼範例，請參閱[使用 PowerShell 設定稽核與威脅偵測](scripts/sql-database-auditing-and-threat-detection-powershell.md)。

### <a name="using-rest-api"></a>使用 REST API

**REST API**：

- [建立或更新資料庫稽核原則](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [建立或更新伺服器稽核原則](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [取得資料庫稽核原則](/rest/api/sql/database%20auditing%20settings/get)
- [取得伺服器稽核原則](/rest/api/sql/server%20auditing%20settings/get)

具有 WHERE 子句而可支援其他篩選的擴充原則：

- [建立或更新資料庫*延伸*的稽核原則](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [建立或補救伺服器*擴充*的稽核原則](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [取得資料庫*延伸*的稽核原則](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [取得伺服器*擴充*的稽核原則](/rest/api/sql/server%20auditing%20settings/get)

### <a name="using-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本

您可以使用 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 範本來管 Azure SQL 資料庫，如下列範例所示：

- [部署 Azure SQL Server 並啟用審核功能，以將 audit 記錄寫入至 Azure Blob 儲存體帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [部署啟用稽核的 Azure SQL Server 以將稽核記錄寫入 Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [部署啟用稽核的 Azure SQL Server 以將稽核記錄寫入事件中樞](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> 連結的範例位於外部公用存放庫，並以「原樣」提供，不含擔保，而且在任何 Microsoft 支援方案/服務下不受支援。
