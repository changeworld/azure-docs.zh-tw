---
title: 已知問題：線上遷移至 SQL Database
titleSuffix: Azure Database Migration Service
description: 瞭解使用 Azure 資料庫移轉服務來 Azure SQL Database 線上遷移至的已知問題/遷移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 6648a20e03facad4b791cacba8513f9f1aa7d2f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291906"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>線上遷移至 Azure SQL Database 的已知問題/遷移限制

從 SQL Server 線上移轉到 Azure SQL Database 的相關聯已知問題和限制，如下所述。

> [!IMPORTANT]
> 將 SQL Server 的線上遷移至 Azure SQL Database 時，不支援 SQL_variant 資料類型的遷移。

### <a name="migration-of-temporal-tables-not-supported"></a>不支援移轉時態表

**徵兆**

如果您的來源資料庫包含一或多個時態表，則「完整資料載入」作業期間，資料庫移轉會失敗，您可能會看到下列訊息：

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![時態表錯誤範例](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**因應措施**

使用下列步驟。

1. 請使用以下的查詢，在您的來源結構描述中尋找時態表。

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. 從 [進行移轉設定]**** 刀鋒視窗 (您在其中指定要移轉的資料表) 中，排除這些資料表。

3. 重新執行移轉活動。

**資源**

如需詳細資訊，請參閱[時態表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)一文。

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>移轉資料表包含具有 hierarchyid 資料類型的一或多個資料行

**徵兆**

您可能會看到 SQL 例外狀況，表示在「完整資料載入」期間「ntext 與 hierarchyid 不相容」：

![hierarchyid 錯誤範例](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**因應措施**

使用下列步驟。

1. 請使用以下查詢，尋找使用者資料表，該資料表包含具有 hierarchyid 資料類型的資料行。

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. 從 [進行移轉設定]**** 刀鋒視窗 (您在其中指定要移轉的資料表) 中，排除這些資料表。

3. 重新執行移轉活動。

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>移轉失敗，具有「完整資料載入」或「增量資料同步處理」期間，結構描述中使用中觸發程序的各種完整性違規

**因應措施**

使用下列步驟。

1. 請使用以下查詢，尋找來源資料庫中目前使用中的觸發程序：

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. 使用 [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) 一文中提供的步驟，停用來源資料庫中的觸發程序。

3. 重新執行移轉活動。

### <a name="support-for-lob-data-types"></a>支援 LOB 資料類型

**徵兆**

如果大型物件 (LOB) 資料行的長度大於 32 KB，目標的資料可能會遭到截斷。 您可以使用以下查詢，檢查 LOB 資料行的長度：

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**因應措施**

如果您有大於 32 KB 的 LOB 資料行，請洽詢工程團隊， [詢問 Azure 資料庫的遷移](mailto:AskAzureDatabaseMigrations@service.microsoft.com)。

### <a name="issues-with-timestamp-columns"></a>時間戳記資料行的問題

**徵兆**

Azure 資料庫移轉服務不會遷移來源時間戳記值;相反地，Azure 資料庫移轉服務會在目標資料表中產生新的時間戳記值。

**因應措施**

如果您需要 Azure 資料庫移轉服務來遷移儲存在來源資料表中的確切時間戳記值，請洽詢工程團隊， [要求 Azure 資料庫移轉](mailto:AskAzureDatabaseMigrations@service.microsoft.com)。

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>資料移轉錯誤未提供資料庫詳細狀態分頁的其他詳細資料

**徵兆**

當您在 [資料庫詳細資料] 狀態視圖中遇到不同的遷移失敗時，選取頂端功能區上的 [ **資料移轉錯誤** ] 連結可能無法提供有關遷移失敗的其他詳細資料。

![資料移轉錯誤沒有詳細資料範例](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**因應措施**

若要取得特定失敗的詳細資料，請使用下列步驟。

1. 關閉資料庫詳細狀態刀鋒視窗，以顯示移轉活動畫面。

     ![移轉活動畫面](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. 選取 [查看錯誤詳細資料]****，以檢視特定錯誤訊息，協助您針對移轉錯誤進行疑難排解。

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>SQLDB online 遷移不支援 Geography 資料類型

**徵兆**

遷移失敗，並出現包含下列文字的錯誤訊息：

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**因應措施**

雖然 Azure 資料庫移轉服務支援將地理位置資料類型用於離線遷移至 Azure SQL Database，但若要進行線上遷移，則不支援地理資料類型。 嘗試使用 Azure 資料庫移轉服務來線上遷移此資料庫之前，請先嘗試使用替代方法，將來源的資料類型變更為支援的類型。

### <a name="supported-editions"></a>支援的版本

**徵兆**

遷移失敗，並出現包含下列文字的錯誤訊息：

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**因應措施**

使用 Azure 資料庫移轉服務來 Azure SQL Database 的線上遷移支援僅延伸至 Enterprise、Standard 和 Developer 版本。 開始進行遷移程式之前，請確定您使用的是支援的版本。
