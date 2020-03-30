---
title: 連接源資料庫的問題
titleSuffix: Azure Database Migration Service
description: 瞭解如何解決與將 Azure 資料庫移轉服務連接到源資料庫相關的已知問題/錯誤。
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
ms.openlocfilehash: 3eef7476a729c7b83290b9d8d86ba06524bed72b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297088"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>針對連線到來源資料庫時發生的 DMS 錯誤進行疑難排解

以下文章詳細介紹了如何解決將 Azure 資料庫移轉服務 （DMS） 連接到源資料庫時可能遇到的潛在問題。 以下每個部分都與特定類型的源資料庫相關，列出您可能遇到的錯誤以及有關如何排除連接故障的詳細資訊和連結。

## <a name="sql-server"></a>SQL Server

下表提供了與連接到源 SQL Server 資料庫以及如何解決這些問題相關的潛在問題。

| 錯誤         | 原因和故障排除詳細資訊 |
| ------------- | ------------- |
| SQL 連接失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 驗證實例名稱是否正確，以及 SQL Server 配置為允許遠端連線。<br> | 如果服務找不到源伺服器，則會發生此錯誤。 要解決此問題，請參閱使用[動態埠或命名實例 時連接到源 SQL Server](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)的文章。 |
| **錯誤 53** - SQL 連接失敗。 （此外，對於錯誤代碼 1、2、5、53、233、258、1225、11001）<br><br> | 如果服務無法連接到源伺服器，則會發生此錯誤。 要解決此問題，請參閱以下資源，然後重試。 <br><br>  [互動式使用者指南，用於解決連接問題](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [將 SQL Server 移轉至 Azure SQL Database 的先決條件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [將 SQL 伺服器遷移到 Azure SQL 資料庫託管實例的先決條件](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **錯誤 18456** - 登錄失敗。<br> | 如果服務無法使用提供的 T-SQL 憑據連接到源資料庫，則會發生此錯誤。 要解決此問題，請驗證輸入的憑據。 您還可以參考[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或下表下的說明中列出的故障排除文檔，然後重試。 |
| 提供的帳戶名稱值"{0}" 格式錯誤。 帳戶名稱的預期格式為功能變數名稱\使用者名<br> | 如果使用者選擇 Windows 身份驗證，但以無效格式提供使用者名，則會發生此錯誤。 要解決此問題，請以正確的格式提供 Windows 身份驗證的使用者名，或者選擇**SQL 身份驗證**。 |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

下表提供了與連接到源 AWS RDS MySQL 資料庫以及如何解決這些問題相關的潛在問題。

| 錯誤         | 原因和故障排除詳細資訊 |
| ------------- | ------------- |
| **錯誤 [2003]**[HY000] - 連接失敗。 錯誤 [HY000] [MySQL][ODBC x.x（w） 驅動程式] 無法在"[伺服器]"（10060） 上連接到 MySQL 伺服器 | 如果 MySQL ODBC 驅動程式無法連接到源伺服器，則會發生此錯誤。 要解決此問題，請參閱下表下面的注釋中列出的故障排除文檔，然後重試。<br> |
| **錯誤 [2005]**[HY000] - 連接失敗。 錯誤 [HY000] [MySQL][ODBC x.x（w） 驅動程式] 未知 MySQL 伺服器主機 "[伺服器]" | 如果服務在 RDS 上找不到源主機，則會發生此錯誤。 問題可能是因為列出的源不存在，或者 RDS 基礎結構有問題。 要解決此問題，請參閱下表下面的注釋中列出的故障排除文檔，然後重試。<br> |
| **錯誤 [1045]**[HY000] - 連接失敗。 錯誤 [HY000] [MySQL][ODBC x.x（w） 驅動程式] 使用者"[使用者][\'\'\"伺服器{伺服器}"（使用密碼：是） | 如果 MySQL ODBC 驅動程式由於憑據無效而無法連接到源伺服器，則會發生此錯誤。 驗證您輸入的憑據。 如果問題仍然存在，請驗證源電腦是否具有正確的憑據。 您可能需要在主控台中重置密碼。 如果仍然遇到此問題，請參閱下表下面的注釋中列出的故障排除文檔，然後重試。<br> |
| **錯誤 [9002]**[HY000] - 連接失敗。 錯誤 [HY000] [MySQL][ODBC x.x（w） 驅動程式] 連接字串可能不對。 訪問門戶以獲得參考。| 如果連接由於連接字串問題而失敗，則會發生此錯誤。 驗證提供的連接字串是否有效。 要解決此問題，請參閱下表下面的注釋中列出的故障排除文檔，然後重試。<br> |
| **二進位日誌記錄中的錯誤。變數binlog_format具有值"[值]"。請將其更改為"行"。** | 如果二進位日誌記錄中出現錯誤，則會發生此錯誤;變數binlog_format的值不正確。 要解決此問題，請將參數組中binlog_format更改為"ROW"，然後重新開機實例。 有關詳細資訊，請參閱[二進位日誌記錄選項和變數](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html)或 AWS [RDS MySQL 資料庫日誌檔文檔](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)。<br> |

> [!NOTE]
> 有關與連接到源 AWS RDS MySQL 資料庫相關的故障排除問題的詳細資訊，請參閱以下資源：
> * [針對 Amazon RDS 連接問題的故障排除](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解決連接到 Amazon RDS 資料庫實例的問題？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS 後

下表提供了與連接到源 AWS RDS PostgreSQL 資料庫以及如何解決這些問題相關的潛在問題。

| 錯誤         | 原因和故障排除詳細資訊 |
| ------------- | ------------- |
| **錯誤 [101]**[08001] - 連接失敗。 錯誤 [08001] 超時已過期。 | 如果 Postgres 驅動程式無法連接到源伺服器，則會發生此錯誤。 要解決此問題，請參閱下表下面的注釋中列出的故障排除文檔，然後重試。 |
| **錯誤：參數wal_level的值"[值]"。請將其更改為"邏輯"以允許複製。** | 如果參數wal_level值錯誤，則發生此錯誤。 要解決此問題，請將參數組中的 rds.logical_replication 更改為 1，然後重新開機實例。 有關詳細資訊，請參閱使用 DMS 或[PostgreSQL 在 Amazon RDS 上](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)[遷移到 Azure PostgreSQL](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites)的先決條件。 |

> [!NOTE]
> 有關與連接到源 AWS RDS PostgreSQL 資料庫相關的故障排除問題的詳細資訊，請參閱以下資源：
> * [針對 Amazon RDS 連接問題的故障排除](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [如何解決連接到 Amazon RDS 資料庫實例的問題？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL 伺服器

下表提供了與連接到源 AWS RDS SQL Server 資料庫以及如何解決這些問題相關的潛在問題。

| 錯誤         | 原因和故障排除詳細資訊 |
| ------------- | ------------- |
| **錯誤 53** - SQL 連接失敗。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到伺服器或無法訪問伺服器。 驗證實例名稱是否正確，以及 SQL Server 配置為允許遠端連線。 （提供程式：具名管道提供程式，錯誤： 40 - 無法打開到 SQL Server 的連接 | 如果服務無法連接到源伺服器，則會發生此錯誤。 要解決此問題，請參閱下表下面的注釋中列出的故障排除文檔，然後重試。 |
| **錯誤 18456** - 登錄失敗。 使用者登錄失敗 [使用者] | 如果服務無法使用提供的 T-SQL 憑據連接到源資料庫，則會發生此錯誤。 要解決此問題，請驗證輸入的憑據。 您還可以參考[MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017)或下表下的說明中列出的故障排除文檔，然後重試。 |
| **錯誤 87** - 連接字串無效。 和 SQL Server 建立連線時，發生與網路相關或執行個體特定的錯誤。 找不到或無法存取伺服器。 驗證實例名稱是否正確，以及 SQL Server 配置為允許遠端連線。 （提供程式：SQL 網路介面，錯誤： 25 - 連接字串無效） | 如果服務由於連接字串無效而無法連接到源伺服器，則會發生此錯誤。 要解決此問題，請驗證提供的連接字串。 如果問題仍然存在，請參閱下表下面的注釋中列出的故障排除文檔，然後重試。 |
| **錯誤 - 伺服器憑證不受信任的。** 已順利建立與伺服器的連接，但隨後在登入過程中發生錯誤。 （提供程式：SSL 提供程式，錯誤：0 - 憑證連結由不受信任的頒發機構頒發。 | 如果使用的證書不受信任的，則會發生此錯誤。 要解決此問題，您需要查找可信任的證書，然後在伺服器上啟用它。 或者，您可以在連接時選擇"信任證書"選項。 僅當熟悉使用的證書並信任它時，才執行此操作。 <br> 使用自簽章憑證加密的 TLS 連接不提供強大的安全性-它們容易受到中間人攻擊。 請勿在生產環境或連接到 Internet 的伺服器上使用自簽章憑證依賴 TLS。 <br> 有關詳細資訊，請參閱將 SSL 與 Microsoft SQL Server DB 實例或教程[一起使用](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html)[：使用 DMS 將 RDS SQL 伺服器遷移到 Azure。](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) |
| **錯誤 300** - 使用者沒有必需的許可權。 在物件"[伺服器]"，資料庫"[資料庫]"上，視圖伺服器狀態許可權被拒絕 | 如果使用者沒有執行遷移的許可權，則會發生此錯誤。 要解決此問題，請參閱 GRANT[伺服器許可權 - 轉用 SQL](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017)或[教程：使用 DMS 將 RDS SQL 伺服器遷移到 Azure](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)以瞭解更多詳細資訊。 |

> [!NOTE]
> 有關與連接到源 AWS RDS SQL 伺服器相關的故障排除問題的詳細資訊，請參閱以下資源：
>
> * [解決 SQL Server 的連線錯誤](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)
> * [如何解決連接到 Amazon RDS 資料庫實例的問題？](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>已知問題

* [連線遷移到 Azure SQL 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [MySQL 的 Azure 資料庫連線遷移的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [連線遷移到 Azure 資料庫的已知問題/遷移限制](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>後續步驟

* 查看文章[Azure 資料庫移轉服務 PowerShell](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)。
* 查看文章["如何使用 Azure 門戶在 Azure 資料庫中為 MySQL 佈建服務器參數](https://docs.microsoft.com/azure/mysql/howto-server-parameters)"。
* 查看文章[概述使用 Azure 資料庫移轉服務的先決條件](https://docs.microsoft.com/azure/dms/pre-reqs)。
* 請參閱[有關使用 Azure 資料庫移轉服務的常見問題解答](https://docs.microsoft.com/azure/dms/faq)。
