---
title: SQL 受控執行個體稽核
description: 瞭解如何使用 T-sql 開始使用 Azure SQL 受控執行個體的審核
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 05/26/2020
ms.openlocfilehash: 213a4fdb0e064e1c36a04f7190f14fab80cb4daa
ms.sourcegitcommit: 0820c743038459a218c40ecfb6f60d12cbf538b3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87117345"
---
# <a name="get-started-with-azure-sql-managed-instance-auditing"></a>開始使用 Azure SQL 受控執行個體的審核
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

[AZURE SQL 受控執行個體](sql-managed-instance-paas-overview.md)審核會追蹤資料庫事件，並將它們寫入您 Azure 儲存體帳戶中的 audit 記錄。 稽核也會：

- 協助您維護合規性、了解資料庫活動，以及獲取可能指出業務疑慮或可疑安全性違規之不一致及異常的見解。
- 啟用及推動遵循法規標準，但不保證符合法規。 如需有關支援標準合規性之 Azure 程式的詳細資訊，請參閱[Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在其中找到最新的合規性認證清單。

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>設定將伺服器的稽核儲存至 Azure 儲存體

下節描述在您受控執行個體上進行稽核的設定。

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 建立用來儲存稽核記錄的 Azure 儲存體**容器**。

   1. 流覽至您想要儲存 audit 記錄的 Azure 儲存體帳戶。

      > [!IMPORTANT]
      > - 請在與受控執行個體相同的區域中使用儲存體帳戶，以避免跨區域讀取/寫入。 
      > - 如果您的儲存體帳戶位於虛擬網路或防火牆後方，請參閱[授與從虛擬網路存取權](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)。
      > - 如果您將保留期間從 0 (無限制的保留) 變更為任何其他值，請注意保留期只會套用至保留值變更之後所寫入的記錄 (設定為無限制的期間所寫入的記錄會予以保留，即使已啟用保留期)。

   1. 在儲存體帳戶中，移至 [概觀]****，然後按一下 [Blob]****。

      ![Azure Blob widget](./media/auditing-configure/1_blobs_widget.png)

   1. 在上方功能表中，按一下 [+ 容器]**** 以建立新的容器。

      ![建立 Blob 容器圖示](./media/auditing-configure/2_create_container_button.png)

   1. 提供容器**名稱**、將 [**公用存取層級**] 設定為 [**私人**]，然後按一下 **[確定]**。

      ![建立 Blob 容器設定](./media/auditing-configure/3_create_container_config.png)

    > [!IMPORTANT]
    > 若客戶想要為其伺服器或資料庫層級的 audit 事件設定不可變的記錄存放區，則應遵循[Azure 儲存體提供的指示](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage#enabling-allow-protected-append-blobs-writes)。 （請確定您在設定不可變的 blob 儲存體時，已選取 [**允許其他附加**]）。
  
3. 建立 audit 記錄檔的容器之後，有兩種方式可以將它設定為 audit 記錄的目標：[使用 t-sql](#blobtsql)或[使用 SQL Server Management Studio （SSMS） UI](#blobssms)：

   - <a id="blobtsql"></a>使用 T-sql 為 audit 記錄設定 blob 儲存體：

     1. 在容器清單中，按一下新建立的容器，然後按一下 [容器屬性]****。

        ![Blob 容器屬性按鈕](./media/auditing-configure/4_container_properties_button.png)

     1. 按一下複製圖示來複製容器 URL，然後儲存 URL (例如，儲存在 [記事本] 中) 供之後使用。 容器 URL 的格式應為 `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![Blob 容器複製 URL](./media/auditing-configure/5_container_copy_name.png)

     1. 產生 Azure 儲存體**SAS 權杖**，以授與受控實例對儲存體帳戶的審核存取權限：

        - 流覽至您在上一個步驟中建立容器所在的 Azure 儲存體帳戶。

        - 按一下 [**儲存體設定**] 功能表中的 [**共用存取**簽章]。

          ![[儲存體設定] 功能表中的 [共用存取簽章] 圖示](./media/auditing-configure/6_storage_settings_menu.png)

        - 請依照下列方式設定 SAS：

          - **允許的服務**：Blob

          - **開始日期**：若要避免時區相關問題，請使用昨天的日期

          - **結束日期**：選擇此 SAS 權杖到期的日期

            > [!NOTE]
            > 在過期時更新權杖，以避免稽核失敗。

          - 按一下 [產生 SAS]****。

            ![SAS 設定](./media/auditing-configure/7_sas_configure.png)

        - SAS 權杖會出現在底部。 按一下複製圖示來複製權杖，然後儲存權杖 (例如，儲存在 [記事本] 中) 供之後使用。

          ![複製 SAS 權杖](./media/auditing-configure/8_sas_copy.png)

          > [!IMPORTANT]
          > 將權杖開頭的問號 (“?”) 移除。

     1. 透過 SQL Server Management Studio 或任何其他支援的工具連接到您的受控實例。

     1. 執行下列 T-sql 語句，使用您在先前步驟中建立的容器 URL 和 SAS 權杖來**建立新的認證**：

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. 執行下列 T-sql 語句來建立新的伺服器 audit （選擇您自己的 audit name，然後使用您在先前步驟中建立的容器 URL）。 如果未指定， `RETENTION_DAYS` 預設值為0（無限制的保留）：

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' , RETENTION_DAYS =  integer )
        GO
        ```

        繼續[建立伺服器 audit 規格或資料庫 audit 規格](#createspec)。

   - <a id="blobssms"></a>使用 SQL Server Management Studio 18 （預覽）為 audit 記錄設定 blob 儲存體：

     1. 使用 SQL Server Management Studio UI 連接到受控實例。

     1. 展開物件總管的根附注。

     1. 展開 [**安全性**] 節點，以滑鼠右鍵按一下 [**審核**] 節點，然後按一下 [新增] [ **Audit**]：

        ![展開 [安全性] 和 [稽核] 節點](./media/auditing-configure/10_mi_SSMS_new_audit.png)

     1. 請確定已在 [ **Audit 目的地**] 中選取 [ **URL** ]，然後按一下 **[流覽]**：

        ![瀏覽 Azure 儲存體](./media/auditing-configure/11_mi_SSMS_audit_browse.png)

     1. (選擇性) 登入您的 Azure 帳戶：

        ![登入 Azure](./media/auditing-configure/12_mi_SSMS_sign_in_to_azure.png)

     1. 從下拉式清單中選取 [訂用帳戶]、[儲存體帳戶] 和 [blob 容器]，或按一下 [**建立**] 來建立您自己的容器。 完成後，請按一下 **[確定]**：

        ![選取 Azure 訂用帳戶、儲存體帳戶和 blob 容器](./media/auditing-configure/13_mi_SSMS_select_subscription_account_container.png)

     1. 在 [**建立 Audit** ] 對話方塊中按一下 **[確定]** 。

4. <a id="createspec"></a>在您將 blob 容器設定為 audit 記錄檔的目標之後，請建立並啟用伺服器 audit 規格或資料庫 audit 規格，如同 SQL Server：

   - [建立伺服器 audit 規格 T-sql 指南](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [建立資料庫 audit 規格 T-sql 指南](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

5. 啟用您在步驟3中建立的伺服器 audit：

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

如需其他資訊：

- [SQL Server 中的 Azure SQL 受控執行個體與資料庫之間的差異](#auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server)
- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hubs-or-azure-monitor-logs"></a>設定伺服器的審核，以事件中樞或 Azure 監視器記錄

來自受控實例的 Audit 記錄可以傳送至 Azure 事件中樞或 Azure 監視器記錄。 本節說明如何進行此設定：

1. 在[Azure 入口網站](https://portal.azure.com/)中流覽至受控實例。

2. 按一下 [診斷設定]****。

3. 按一下 [開啟診斷]****。 如果已啟用診斷，則會改為顯示 [ **+ 新增診斷設定**]。

4. 從記錄清單中選取 [SQLSecurityAuditEvents]****。

5. 選取 audit 事件的目的地： [事件中樞]、[Azure 監視器記錄] 或 [兩者]。 為每個目標設定必要的參數 (例如 Log Analytics 工作區)。

6. 按一下 [檔案] 。

    ![設定診斷設定](./media/auditing-configure/9_mi_configure_diagnostics.png)

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

- 您可以使用[Azure 儲存體總管](https://azure.microsoft.com/features/storage-explorer/)之類的工具來探索 audit 記錄。 在 Azure 儲存體中，會以 blob 檔案集合的形式，將審核記錄儲存在已定義用來儲存 audit 記錄的容器內。 如需有關儲存體資料夾階層、命名慣例、記錄格式的進一步詳細資訊，請參閱 [Blob 稽核記錄格式參考](https://go.microsoft.com/fwlink/?linkid=829599)。

- 如需完整的審查記錄耗用量方法清單，請參閱[開始使用 Azure SQL Database 的審核](../../azure-sql/database/auditing-overview.md)。

### <a name="consume-logs-stored-in-event-hubs"></a>使用儲存在事件中樞中的記錄

若要從事件中樞取用 audit 記錄資料，您必須設定串流來取用事件，並將它們寫入目標。 如需詳細資訊，請參閱 Azure 事件中樞文件。

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>取用和分析儲存在 Azure 監視器記錄檔中的記錄

如果將 audit 記錄寫入 Azure 監視器記錄檔，它們會出現在 Log Analytics 工作區中，您可以在其中執行 audit 資料的 advanced 搜尋。 作為起點，流覽至 Log Analytics 工作區。 在 [**一般**] 區段中，按一下 [**記錄**]，然後輸入簡單查詢，例如： `search "SQLSecurityAuditEvents"` 以查看 audit 記錄。  

Azure 監視器記錄可讓您使用整合式搜尋和自訂儀表板，在您的所有工作負載和伺服器上輕鬆分析數百萬筆記錄，以提供您即時的 operational insights。 如需 Azure 監視器記錄搜尋語言和命令的其他實用資訊，請參閱[Azure 監視器記錄搜尋參考](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-managed-instance-and-databases-in-sql-server"></a>SQL Server 中的 Azure SQL 受控執行個體和資料庫中的資料庫之間的差異

Azure SQL 受控執行個體中的資料庫和 SQL Server 中的資料庫之間的主要差異如下：

- 有了 Azure SQL 受控執行個體，審核功能會在伺服器層級運作，並將記錄檔儲存 `.xel` 在 Azure Blob 儲存體中。
- 在 SQL Server 中，audit 會在伺服器層級運作，但會將事件儲存在檔案系統/windows 事件記錄檔上。

受控實例中的 XEvent 審核支援 Azure Blob 儲存體目標。 **不支援**檔案與 windows 記錄。

向 Azure Blob 儲存體進行稽核的 `CREATE AUDIT` 語法有一個主要差異：

- `TO URL`系統會提供新的語法，並可讓您指定放置檔案之 Azure Blob 儲存體容器的 URL `.xel` 。
- 提供新的語法 `TO EXTERNAL MONITOR` 來啟用事件中樞和 Azure 監視器記錄檔目標。
- `TO FILE`因為 AZURE SQL 受控執行個體無法存取 Windows 檔案共用，所以**不支援**此語法。
- **不支援**關機選項。
- **不支援**使用 0 的 `queue_delay`。

## <a name="next-steps"></a>後續步驟

- 如需完整的審查記錄耗用量方法清單，請參閱[開始使用 Azure SQL Database 的審核](../../azure-sql/database/auditing-overview.md)。
- 如需有關支援標準合規性之 Azure 程式的詳細資訊，請參閱[Azure 信任中心](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)，您可以在其中找到最新的合規性認證清單。

<!--Image references-->
