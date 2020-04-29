---
title: 受控執行個體稽核
description: 了解如何透過 T-SQL 開始使用 Azure SQL Database 受控執行個體稽核
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/27/2020
ms.openlocfilehash: 405ac27fad3c24d3064f11476f452ad00abb9b02
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80387762"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>開始使用 Azure SQL Database 受控執行個體稽核

[受控執行個體稽核](sql-database-managed-instance.md)會追蹤資料庫事件並將事件寫入您 Azure 儲存體帳戶中的稽核記錄。 稽核也具備下列功能：

- 協助您保持法規遵循、了解資料庫活動，以及深入了解可指出商務考量或疑似安全違規的不一致和異常。
- 啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準合規性之 Azure 程式的詳細資訊，請參閱[Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在其中找到最新的 SQL Database 合規性認證清單。

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>設定將伺服器的稽核儲存至 Azure 儲存體

下節描述在您受控執行個體上進行稽核的設定。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 建立用來儲存稽核記錄的 Azure 儲存體**容器**。

   1. 巡覽至您要儲存稽核記錄的 Azure 儲存體。

      > [!IMPORTANT]
      > 請在與受控執行個體相同的區域中使用儲存體帳戶，以避免跨區域讀取/寫入。

   1. 在儲存體帳戶中，移至 [概觀]****，然後按一下 [Blob]****。

      ![Azure Blob 小工具](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. 在上方功能表中，按一下 [+ 容器]**** 以建立新的容器。

      ![建立 Blob 容器圖示](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. 提供一個容器 [名稱]****，將公用存取層級設定為 [私人]****，然後按一下 [確定]****。

      ![建立 Blob 容器設定](./media/sql-managed-instance-auditing/3_create_container_config.png)
  > [!IMPORTANT]
  > 若客戶想要為其伺服器或資料庫層級的 audit 事件設定不可變的記錄存放區，應遵循[Azure 儲存體提供的指示](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes)（請確定您已選取 [當您設定不可變的 blob 儲存體時**允許額外的附加**]）
  
3. 在為稽核記錄建立容器之後，有兩個方法可將該容器設定為稽核記錄的目標：[使用 T-SQL](#blobtsql) 或[使用 SQL Server Management Studio (SSMS) UI](#blobssms)：

   - <a id="blobtsql"></a>使用 T-SQL 來設定稽核記錄的 Blob 儲存體：

     1. 在容器清單中，按一下新建立的容器，然後按一下 [容器屬性]****。

        ![Blob 容器屬性按鈕](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. 按一下複製圖示來複製容器 URL，然後儲存 URL (例如，儲存在 [記事本] 中) 供之後使用。 容器 URL 的格式應為 `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Blob 容器複製 URL](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. 產生 Azure 儲存體 **SAS 權杖**來將受控執行個體稽核存取權限授予儲存體帳戶：

        - 巡覽至您在上一個步驟中建立容器的 Azure 儲存體帳戶。

        - 在 [儲存體設定] 功能表中按一下 [共用存取簽章]****。

          ![[儲存體設定] 功能表中的 [共用存取簽章] 圖示](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - 請依照下列方式設定 SAS：

          - **允許的服務**：Blob

          - **開始日期**：若要避免時區相關的問題，建議使用昨天的日期

          - **結束日期**：選擇此 SAS 權杖到期的日期

            > [!NOTE]
            > 在過期時更新權杖，以避免稽核失敗。

          - 按一下 [產生 SAS]****。
            
            ![SAS 設定](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - 按一下 [產生 SAS] 後，SAS 權杖就會出現在底部。 按一下複製圖示來複製權杖，然後儲存權杖 (例如，儲存在 [記事本] 中) 供之後使用。

          ![複製 SAS 權杖](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > 將權杖開頭的問號 (“?”) 移除。

     1. 透過 SQL Server Management Studio (SSMS) 或任何其他支援的工具來連線至受控執行個體。

     1. 執行下列 T-SQL 陳述式，以使用您在上一個步驟中建立的容器 URL 和 SAS 權杖來**建立新的認證**：

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. 執行下列 T-SQL 陳述式來建立新的伺服器稽核 (選擇您自己的稽核名稱，使用您在上一個步驟中建立的容器 URL)。 如果未指定，則 `RETENTION_DAYS` 預設為 0 (無限制的保留期)：

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. 藉由[建立伺服器稽核規格或資料庫稽核規格](#createspec)來繼續

   - <a id="blobssms"></a>使用 SQL Server Management Studio (SSMS) 18 (預覽) 來設定稽核記錄的 Blob 儲存體：

     1. 使用 SQL Server Management Studio (SSMS) UI 來連線至受控執行個體。

     1. 展開 [物件總管] 的根節點。

     1. 展開 [安全性]**** 節點，以滑鼠右鍵按一下 [稽核]**** 節點，然後按一下 [新增稽核]：

        ![展開 [安全性] 和 [稽核] 節點](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. 確定 [稽核目的地]**** 中已選取 [URL]，然後按一下 [瀏覽]****：

        ![瀏覽 Azure 儲存體](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (選擇性) 登入您的 Azure 帳戶：

        ![登入 Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. 從下拉式清單中選取訂用帳戶、儲存體帳戶和 Blob 容器，或者，藉由按一下 [建立]**** 來建立您自己的容器。 當您完成後，請按一下 [確定]****：

        ![選取 Azure 訂用帳戶、儲存體帳戶和 blob 容器](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. 在 [建立稽核] 對話方塊中按一下 [確定]****。

4. <a id="createspec"></a>將 Blob 容器設定為 audit 記錄檔的目標之後，請依照您的 SQL Server，建立並啟用伺服器 Audit 規格或資料庫 Audit 規格：

   - [建立伺服器稽核規格 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [建立資料庫稽核規格 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. 啟用您在步驟3中建立的伺服器 audit：

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

如需其他資訊：

- [SQL Server 中的 Azure SQL Database 和資料庫中的單一資料庫、彈性集區和受控實例之間的差異](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>設定伺服器對事件中樞或 Azure 監視器記錄的審核

來自受控實例的 Audit 記錄可以傳送至甚至中樞或 Azure 監視器記錄。 本節說明如何進行此設定：

1. 在 [Azure 入口網站](https://portal.azure.com/)中瀏覽至受控執行個體。

2. 按一下 [診斷設定]****。

3. 按一下 [開啟診斷]****。 如果已經啟用診斷，則會改為顯示 [+新增診斷設定]**。

4. 從記錄清單中選取 [SQLSecurityAuditEvents]****。

5. 選取 [audit 事件-事件中樞]、[Azure 監視器記錄] 或 [兩者] 的目的地。 為每個目標設定必要的參數 (例如 Log Analytics 工作區)。

6. 按一下 **[儲存]** 。

    ![設定診斷設定](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. 使用 **SQL Server Management Studio (SSMS)** 或任何其他支援的用戶端來連線至受控執行個體。

8. 執行下列 T-SQL 陳述式以建立伺服器稽核：

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. 依照您的 SQL Server，建立並啟用伺服器 audit 規格或資料庫審核規格：

   - [建立伺服器稽核規格 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [建立資料庫稽核規格 T-SQL 指南](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. 啟用在步驟8中建立的伺服器 audit：
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>取用稽核記錄

### <a name="consume-logs-stored-in-azure-storage"></a>取用儲存在 Azure 儲存體中的記錄

有幾種方法可以用於檢視 Blob 稽核記錄。

- 使用系統函式 `sys.fn_get_audit_file` (T-SQL) 以表格格式傳回稽核記錄資料。 如需有關如何使用此函數的詳細資訊，請參閱 [sys.fn_get_audit_file 文件](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql) (英文)。

- 您可以使用[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)之類的工具來探索 audit 記錄。 在 Azure 儲存體中，稽核記錄會以 Blob 檔案集合的方式儲存在定義來儲存稽核記錄的容器內。 如需有關儲存體資料夾階層、命名慣例、記錄格式的進一步詳細資訊，請參閱 [Blob 稽核記錄格式參考](https://go.microsoft.com/fwlink/?linkid=829599)。

- 如需稽核記錄耗用方法的完整清單，請參閱[開始使用 SQL 資料庫稽核](sql-database-auditing.md)。

### <a name="consume-logs-stored-in-event-hub"></a>取用儲存在事件中樞中的記錄

若要取用來自事件中樞的稽核記錄資料，您必須設定資料流取用事件並將其寫入至目標。 如需詳細資訊，請參閱＜Azure 事件中樞文件＞。

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>取用和分析儲存在 Azure 監視器記錄檔中的記錄

如果將 audit 記錄寫入 Azure 監視器記錄檔，它們會出現在 Log Analytics 工作區中，您可以在其中執行 audit 資料的 advanced 搜尋。 作為起點，流覽至 Log Analytics 工作區，並在 *[一般*] 區段下按一下 [*記錄*]，然後輸入簡單查詢`search "SQLSecurityAuditEvents"` ，例如：以查看 audit 記錄。  

Azure 監視器記錄可讓您使用整合式搜尋和自訂儀表板，在您的所有工作負載和伺服器上輕鬆分析數百萬筆記錄，以提供您即時的 operational insights。 如需 Azure 監視器記錄搜尋語言和命令的其他實用資訊，請參閱[Azure 監視器記錄搜尋參考](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Azure SQL Database 中的資料庫和 SQL Server 中的資料庫兩者之間的稽核差異

Azure SQL Database 中的資料庫和 SQL Server 中的資料庫兩者之間的主要稽核差異在於：

- 使用 Azure SQL Database 中的受控執行個體部署選項，稽核作業會在伺服器層級運作，並將 `.xel` 記錄檔儲存於 Azure Blob 儲存體。
- 在 SQL Server 內部部署 / 虛擬機器中，稽核會在伺服器層級運作，但會將事件儲存在檔案系統/windows 事件記錄。

受控執行個體中的 XEvent 稽核支援 Azure Blob 儲存體目標。 **不支援**檔案與 windows 記錄。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- 已提供新的 `TO URL` 語法，可讓您指定放置 `.xel` 檔案的 Azure Blob 儲存體容器 URL。
- 提供新的`TO EXTERNAL MONITOR`語法，以啟用甚至中樞和 Azure 監視器記錄目標。
- `TO FILE` 語法**不受支援**，因為 SQL Database 無法存取 Windows 檔案共用。
- **不支援**關機選項。
- **不支援**使用 0 的 `queue_delay`。

## <a name="next-steps"></a>後續步驟

- 如需稽核記錄耗用方法的完整清單，請參閱[開始使用 SQL 資料庫稽核](sql-database-auditing.md)。
- 如需有關支援標準合規性之 Azure 程式的詳細資訊，請參閱[Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在其中找到最新的 SQL Database 合規性認證清單。

<!--Image references-->









