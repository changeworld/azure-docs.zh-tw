---
title: 連接源資料庫的問題
titleSuffix: Azure Database Migration Service
description: 深入瞭解如何針對與將 Azure 資料庫移轉服務連線到源資料庫相關聯的已知問題/錯誤進行疑難排解。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 4421ad4260ff8eada87cdebc031d09922860324f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84196321"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>針對連線到來源資料庫時發生的 DMS 錯誤進行疑難排解

下列文章提供如何解決將 Azure 資料庫移轉服務（DMS）連接至源資料庫時可能會遇到之潛在問題的詳細資料。 以下每一節都與特定類型的源資料庫相關，其中列出您可能會遇到的錯誤以及有關如何疑難排解連線的詳細資訊連結。

## <a name="sql-server"></a>SQL Server

下表提供與連接到來源 SQL Server 資料庫相關的潛在問題，以及如何處理它們。

| 錯誤         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| SQL 連接失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 請確認實例名稱是否正確，以及 SQL Server 是否設定為允許遠端連線。<br> | 如果服務找不到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱[當使用動態通訊埠或命名實例時，連接到來源 SQL Server](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)一文。 |
| **錯誤 53** -SQL 連接失敗。 （也適用于錯誤碼1、2、5、53、233、258、1225、11001）<br><br> | 如果服務無法連接到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱下列資源，然後再試一次。 <br><br>  [針對連線問題進行疑難排解的互動式使用者指南](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [將 SQL Server 移轉至 Azure SQL Database 的先決條件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [將 SQL Server 遷移至 Azure SQL 受控執行個體的必要條件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **錯誤 18456** -登入失敗。<br> | 如果服務無法使用所提供的 T-sql 認證連接到源資料庫，就會發生這個錯誤。 若要解決此問題，請確認輸入的認證。 您也可以參考[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或此表格下方的附注中所列的疑難排解檔，然後再試一次。 |
| 提供的 AccountName 值 ' ' 格式不正確 {0} 。 AccountName 的預期格式為 DomainName\UserName<br> | 如果使用者選取 Windows 驗證，但以無效格式提供使用者名稱，就會發生這個錯誤。 若要解決此問題，請以正確格式提供 Windows 驗證的使用者名稱，或選取 **[SQL 驗證**]。 |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

下表提供與連接到來源 AWS RDS MySQL 資料庫相關的潛在問題，以及如何解決這些問題。

| 錯誤         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| **錯誤 [2003]**[HY000]-連接失敗。 錯誤 [HY000] [MySQL] [ODBC x. x （w）驅動程式] 無法連接到 ' {server} ' 上的 MySQL 伺服器（10060） | 如果 MySQL ODBC 驅動程式無法連接到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱此表格下方的附注中所列的疑難排解檔，然後再試一次。<br> |
| **錯誤 [2005]**[HY000]-連接失敗。 錯誤 [HY000] [MySQL] [ODBC x. x （w）驅動程式] 不明的 MySQL server 主機 ' {server} ' | 如果服務在 RDS 上找不到來源主機，就會發生這個錯誤。 問題可能是因為列出的來源不存在，或是 RDS 基礎結構發生問題。 若要解決此問題，請參閱此表格下方的附注中所列的疑難排解檔，然後再試一次。<br> |
| **錯誤 [1045]**[HY000]-連接失敗。 錯誤 [HY000] [MySQL] [ODBC x. x （w）驅動程式] 拒絕存取使用者 ' {user} ' @ ' {server} ' （使用密碼：是） | 如果 MySQL ODBC 驅動程式因為認證無效而無法連接到來源伺服器，就會發生此錯誤。 確認您所輸入的認證。 如果問題持續發生，請確認來源電腦具有正確的認證。 您可能需要在主控台中重設密碼。 如果您仍然遇到此問題，請參閱此表格下方的附注中所列的疑難排解檔，然後再試一次。<br> |
| **錯誤 [9002]**[HY000]-連接失敗。 錯誤 [HY000] [MySQL] [ODBC x. x （w）驅動程式] 連接字串可能不正確。 流覽入口網站以取得參考。| 如果連接因為連接字串發生問題而失敗，就會發生這個錯誤。 請確認提供的連接字串有效。 若要解決此問題，請參閱此表格下方的附注中所列的疑難排解檔，然後再試一次。<br> |
| **二進位檔記錄時發生錯誤。變數 binlog_format 具有值 ' {value} '。請將它變更為「資料列」。** | 如果二進位記錄中有錯誤，就會發生此錯誤;變數 binlog_format 的值錯誤。 若要解決此問題，請將參數群組中的 binlog_format 變更為 ' ROW '，然後重新開機實例。 如需詳細資訊，請參閱[二進位記錄選項和變數](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)或[AWS RDS MySQL 資料庫記錄檔檔](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)。<br> |

> [!NOTE]
> 如需有關疑難排解與來源 AWS RDS MySQL 資料庫相關之問題的詳細資訊，請參閱下列資源：
> * [Amazon RDS 連接問題的疑難排解](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何? 解決連接到我的 Amazon RDS 資料庫實例時發生的問題嗎？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS 于 postgresql

下表提供與連接到來源 AWS RDS 于 postgresql 資料庫相關的潛在問題，以及如何解決這些問題。

| 錯誤         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| **錯誤 [101]**[08001]-連接失敗。 錯誤 [08001] timeout 已過期。 | 如果 Postgres 驅動程式無法連接到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱此表格下方的附注中所列的疑難排解檔，然後再試一次。 |
| **錯誤：參數 wal_level 具有值 ' {value} '。請將它變更為 [邏輯] 以允許複寫。** | 如果參數 wal_level 的值錯誤，就會發生這個錯誤。 若要解決此問題，請將參數群組中的 rds. logical_replication 變更為1，然後重新開機實例。 如需詳細資訊，請參閱[在 AMAZON RDS 上](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)[使用 DMS 或于 postgresql 遷移至 Azure 于 postgresql 的先決條件](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites)。 |

> [!NOTE]
> 如需有關疑難排解與來源 AWS RDS 于 postgresql 資料庫相關之問題的詳細資訊，請參閱下列資源：
> * [Amazon RDS 連接問題的疑難排解](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何? 解決連接到我的 Amazon RDS 資料庫實例時發生的問題嗎？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

下表提供與連接到來源 AWS RDS SQL Server 資料庫以及如何解決這些問題相關的潛在問題。

| 錯誤         | 原因和疑難排解詳細資料 |
| ------------- | ------------- |
| **錯誤 53** -SQL 連接失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 請確認實例名稱是否正確，以及 SQL Server 是否設定為允許遠端連線。 （提供者：具名管道提供者，錯誤： 40-無法開啟與 SQL Server 的連接 | 如果服務無法連接到來源伺服器，就會發生此錯誤。 若要解決此問題，請參閱此表格下方的附注中所列的疑難排解檔，然後再試一次。 |
| **錯誤 18456** -登入失敗。 使用者 ' {user} ' 的登入失敗 | 如果服務無法使用提供的 T-sql 認證連接到源資料庫，就會發生此錯誤。 若要解決此問題，請確認輸入的認證。 您也可以參考[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或此表格下方的附注中所列的疑難排解檔，然後再試一次。 |
| **錯誤 87** -連接字串無效。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 請確認實例名稱是否正確，以及 SQL Server 是否設定為允許遠端連線。 （提供者： SQL 網路介面，錯誤： 25-連接字串無效） | 如果服務因為連接字串無效而無法連接到來源伺服器，就會發生這個錯誤。 若要解決此問題，請確認所提供的連接字串。 如果問題持續發生，請參閱此表格下方的附注中所列的疑難排解檔，然後再試一次。 |
| **錯誤-伺服器憑證不受信任。** 已順利建立與伺服器的連接，但隨後在登入過程中發生錯誤。 （提供者： SSL 提供者，錯誤： 0-憑證鏈是由不受信任的授權單位所簽發）。 | 如果使用的憑證不受信任，就會發生此錯誤。 若要解決此問題，您需要尋找可信任的憑證，然後在伺服器上加以啟用。 或者，您也可以在連接時選取 [信任憑證] 選項。 只有在您熟悉使用的憑證且信任它時，才採取此動作。 <br> 使用自我簽署憑證加密的 TLS 連線不會提供強大的安全性，它們很容易受到攔截式攻擊。 請勿在生產環境中使用自我簽署憑證，或在連線到網際網路的伺服器上依賴 TLS。 <br> 如需詳細資訊，請參閱搭配使用[SSL 與 MICROSOFT SQL SERVER DB 實例](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)或[教學課程：使用 DMS 將 RDS SQL Server 遷移至 Azure](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)。 |
| **錯誤 300** -使用者沒有必要的許可權。 物件 ' {SERVER} '，資料庫 ' {database} ' 的 VIEW SERVER STATE 許可權被拒絕 | 如果使用者沒有執行遷移的許可權，就會發生此錯誤。 若要解決此問題，請參閱[授與伺服器許可權-transact-sql](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017)或[教學課程：使用 DMS 將 RDS SQL Server 遷移至 Azure](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) ，以取得詳細資料。 |

> [!NOTE]
> 如需有關疑難排解連線到來源 AWS RDS SQL Server 相關問題的詳細資訊，請參閱下列資源：
>
> * [解決 SQL Server 的連線錯誤](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [如何? 解決連接到我的 Amazon RDS 資料庫實例時發生的問題嗎？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>已知問題

* [線上遷移到 Azure SQL Database 的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [線上遷移到適用於 MySQL 的 Azure 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [線上遷移到適用於 PostgreSQL 的 Azure 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>後續步驟

* 請參閱[Azure 資料庫移轉服務 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)文章。
* 請參閱[如何使用 Azure 入口網站在適用於 MySQL 的 Azure 資料庫中設定伺服器參數](https://docs.microsoft.com/azure/mysql/howto-server-parameters)文章。
* 若[要瞭解使用 Azure 資料庫移轉服務的必要條件](https://docs.microsoft.com/azure/dms/pre-reqs)，請參閱此文章。
* 請參閱[有關使用 Azure 資料庫移轉服務的常見問題](https://docs.microsoft.com/azure/dms/faq)。
