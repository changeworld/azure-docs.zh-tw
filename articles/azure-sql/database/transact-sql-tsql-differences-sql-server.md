---
title: 解決 T-sql 差異-遷移
description: Azure SQL Database 中小於完整支援的 transact-sql 語句。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: 02b589eebb716f5a69b4db9f00faf12401b8de7f
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619000"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>解決移轉至 SQL Database 期間的 Transact-SQL 差異

將 [您的資料庫](migrate-to-database-from-sql-server.md) 從 SQL Server 遷移至 Azure SQL Database 時，您可能會發現您的 SQL Server 資料庫需要進行一些重新設計，才能進行遷移。 本文章提供一些指引，以協助您執行此再造作業，以及了解為何必須執行再造的基礎原因。 若要偵測不相容性，請使用 [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595)。

## <a name="overview"></a>概觀

Microsoft SQL Server 和 Azure SQL Database 都支援應用程式使用的大部分 Transact-SQL 功能。 例如，資料類型、運算子、字串、算術、邏輯和資料指標函式等核心 SQL 元件在 SQL Server 和 SQL Database 中的運作方式相同。 不過，DDL (資料定義語言) 和 DML (資料操作語言) 元素中有幾個 T-SQL 差異導致對 T-SQL 陳述式和查詢僅提供部分支援 (將在本文章中稍後探討)。

此外，有一些功能和語法都不受支援，因為 Azure SQL Database 是為了隔離 master 資料庫與作業系統相依性的功能而設計的。 因此，大多數伺服器層級活動都不適用於 SQL Database。 如果設定伺服器層級選項、作業系統元件或指定檔案系統設定，則無法使用 t-sql 語句和選項。 當需要這類功能時，通常會從 SQL Database 或從另一個 Azure 功能或服務以其他方式提供適合的替代方案。

例如，高可用性會使用類似 [Always On 可用性群組](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)的技術來建置到 Azure SQL Database 中。 SQL Database 不支援與可用性群組相關的 T-SQL 陳述式，也不支援與「Always On 可用性群組」相關的動態管理檢視。

如需 SQL Database 所支援和不支援的功能清單，請參閱 [Azure SQL Database 功能比較](features-comparison.md)。 此頁面上的清單可補充該指引和功能文章，並將焦點放在 Transact-SQL 陳述式。

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>具有部分差異的 Transact-SQL 語法陳述式

核心 DDL (資料定義語言) 陳述式可供使用，但有些 DDL 陳述式具有與磁碟放置相關的擴充功能和不支援的功能。

- CREATE 和 ALTER DATABASE 陳述式有超過三十多個選項。 這些陳述式包括僅適用於 SQL Server 的檔案放置、FILESTREAM 及服務訊息代理程式選項。 如果您在遷移之前建立資料庫，但您要遷移 T-sql 程式碼來建立資料庫，您應該比較 create database [ (Azure SQL Database) ](https://msdn.microsoft.com/library/dn268335.aspx) 與 [create Database (SQL Server transact-sql) ](https://msdn.microsoft.com/library/ms176061.aspx) 的 SQL Server 語法，以確定您使用的所有選項都受到支援。 Azure SQL Database 的 CREATE DATABASE 也有僅適用於 SQL Database 的服務目標和靈活擴充能力選項。
- CREATE 和 ALTER TABLE 語句具有無法在 SQL Database 上使用的 FileTable 選項，因為不支援 FILESTREAM。
- 支援 CREATE 和 ALTER login 語句，但 SQL Database 不提供所有選項。 為了讓您的資料庫更具可攜性，SQL Database 建議您儘可能使用自主資料庫使用者，而不要使用登入。 如需詳細資訊，請參閱 [建立/變更登](https://docs.microsoft.com/sql/t-sql/statements/alter-login-transact-sql) 入和管理登入 [和使用者](logins-create-manage.md)。

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>在 Azure SQL Database 中不支援的 Transact-SQL 語法

除了與 [Azure SQL Database 功能比較](features-comparison.md)中所述的不支援功能相關的 transact-sql 語句之外，也不支援下列語句和語句群組。 因此，如果要移轉的資料庫使用下列任何功能，請再造您的 T-SQL 以排除這些 T-SQL 功能和陳述式。

- 系統物件的定序
- 相關連接：端點陳述式。 SQL Database 不支援 Windows 驗證，但支援類似的 Azure Active Directory 驗證。 某些驗證類型需要最新的 SSMS 版本。 如需詳細資訊，請參閱 [使用 Azure Active Directory 驗證，連接到 SQL Database 或 Azure Azure Synapse Analytics (先前的 SQL 資料倉儲) ](authentication-aad-overview.md)。
- 使用三個或四個組件名稱跨資料庫查詢。 (使用[彈性資料庫查詢](elastic-query-overview.md)支援跨資料庫唯讀查詢。)
- 跨資料庫擁有權鏈結，`TRUSTWORTHY` 設定
- `EXECUTE AS LOGIN` 請改用 'EXECUTE AS USER'。
- 除了可延伸金鑰管理之外還支援加密
- 事件服務：事件、事件通知、查詢通知
- 檔案放置：與資料庫檔案放置、大小及資料庫檔案 (由 Microsoft Azure 自動管理) 相關的語法。
- 高可用性：與透過 Microsoft Azure 帳戶管理的高可用性相關的語法。 這包括備份、還原、永遠開啟、資料庫鏡像、記錄傳送、修復模式的語法。
- 記錄讀取器：依賴記錄讀取器的語法，在 SQL Database 上無法使用：推送複寫、變更資料捕獲。 SQL Database 可以是推送複寫文章的訂閱者。
- 函式：`fn_get_sql`、`fn_virtualfilestats`、`fn_virtualservernodes`
- 硬體：與硬體相關伺服器設定相關的語法：例如記憶體、背景工作執行緒、CPU 親和性、追蹤旗標。 改為使用服務層級和計算大小。
- `KILL STATS JOB`
- `OPENQUERY`、`OPENROWSET`、`OPENDATASOURCE` 和四部分的名稱
- .NET Framework：與 SQL Server 整合的 CLR
- 語意搜尋
- 伺服器認證：請改用[資料庫範圍認證](https://msdn.microsoft.com/library/mt270260.aspx)。
- 伺服器層級項目：伺服器角色，`sys.login_token`。 `GRANT``REVOKE`和 `DENY` 伺服器層級許可權的、和都無法使用，但有些會由資料庫層級許可權取代。 一些有用的伺服器層級 DMV 有相同的資料庫層級 DMV。
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` 選項和 `RECONFIGURE`。 有些選項可透過 [變更資料庫範圍組態](https://msdn.microsoft.com/library/mt629158.aspx)來使用。
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent：依賴 SQL Server Agent 或 MSDB 資料庫的語法︰警示、運算子、中央管理伺服器。 改用指令碼，例如 Azure PowerShell。
- SQL Server Audit：請改用 SQL Database 稽核。
- SQL Server 追蹤
- 追蹤旗標：有些追蹤旗標項目已移至相容性模式。
- Transact-SQL 偵錯
- 觸發程序：伺服器範圍或登入觸發程序
- `USE` 陳述式：若要將資料庫內容變更為不同的資料庫，您必須建立與新資料庫的連接。

## <a name="full-transact-sql-reference"></a>完整 Transact-SQL 參考

如需有關 Transact-sql 文法、使用方式和範例的詳細資訊，請參閱 SQL Server 線上叢書中的 [Transact-sql 參考 (資料庫引擎) ](https://msdn.microsoft.com/library/bb510741.aspx)   。

### <a name="about-the-applies-to-tags"></a>關於「適用於」標記

Transact-SQL 參考包括從 SQL Server 版本 2008 到目前版本的相關文章。 文章標題底下有一個圖示列，列出四個 SQL Server 的平臺，並指出適用性。 例如，可用性群組是在 SQL Server 2012 中導入。 「 [建立可用性群組](https://msdn.microsoft.com/library/ff878399.aspx)」一   文表示語句會套用至**SQL Server (從 2012) 開始**。 本聲明不適用於 SQL Server 2008、SQL Server 2008 R2、Azure SQL Database、Azure Azure Synapse Analytics (先前的 SQL 資料倉儲) 或平行處理資料倉儲。

在某些情況下，文章的一般主旨可用於產品中，但產品之間會有些許差異。 依適當情況會在文章的中間點指出差異。 在某些情況下，文章的一般主旨可用於產品中，但產品之間會有些許差異。 依適當情況會在文章的中間點指出差異。 例如，SQL Database 中提供建立觸發程式文章。 但是伺服器層級觸發程式的 [ **所有伺服器** ] 選項，表示伺服器層級的觸發程式無法在 SQL Database 中使用。 請改用資料庫層級的觸發程序。

## <a name="next-steps"></a>後續步驟

如需 SQL Database 所支援和不支援的功能清單，請參閱 [Azure SQL Database 功能比較](features-comparison.md)。 此頁面上的清單可補充該指引和功能文章，並將焦點放在 Transact-SQL 陳述式。
