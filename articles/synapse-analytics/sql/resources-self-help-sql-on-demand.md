---
title: 無伺服器 SQL 集區自助服務
description: 本節所包含的資訊可協助您針對無伺服器 SQL 集區的問題進行疑難排解。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 12ff369cb931eb36014b7c9598b036afdc158750
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457191"
---
# <a name="self-help-for-serverless-sql-pool"></a>無伺服器 SQL 集區的自助服務

關於如何針對 Azure Synapse Analytics 中的無伺服器 SQL 集區最常見問題進行疑難排解，本文會有相關資訊。

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Synapse Studio 中的無伺服器 SQL 集區呈現灰色

如果 Synapse Studio 無法與無伺服器 SQL 集區連線，您將會發現無伺服器 SQL 集區呈現灰色，或顯示狀態為「離線」。 這個問題通常會在有下列其中一種情況時發生：

1) 網路阻止與 Azure Synapse 後端通訊。 最常見的情況是連接埠 1443 已遭到封鎖。 若要讓無伺服器 SQL 集區正常執行，請將此連接埠解除封鎖。 其他問題也可能會讓無伺服器 SQL 集區無法運作，[請瀏覽完整的疑難排解指南以取得詳細資訊](../troubleshoot/troubleshoot-synapse-studio.md)。
2) 您沒有可供登入無伺服器 SQL 集區的權限。 若要取得存取權，請讓其中一個 Azure Synapse 工作區系統管理員將您新增至工作區系統管理員或 SQL 系統管理員角色。 [請瀏覽關於存取控制的完整指南以取得詳細資訊](access-control.md)。

## <a name="query-fails-because-file-cannot-be-opened"></a>查詢因為無法開啟檔案而失敗

如果您的查詢失敗，並出現「無法開啟檔案，因為檔案不存在或其他程序正在使用該檔案」錯誤，而且您確定檔案既存在，也沒有其他程序正在使用，則表示無伺服器 SQL 集區無法存取該檔案。 此問題通常發生在 Azure Active Directory 身分識別沒有可供存取檔案的權限時。 根據預設，無伺服器 SQL 集區會嘗試使用 Azure Active Directory 身分識別來存取檔案。 若要解決此問題，您必須具有可存取該檔案的適當權限。 最簡單的方式是在您嘗試查詢的儲存體帳戶上對自己授與「儲存體 Blob 資料參與者」角色。 [請瀏覽關於儲存體的 Azure Active Directory 存取控制完整指南以取得詳細資訊](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。 

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>查詢失敗，因為目前的資源條件約束導致其無法執行 

如果您的查詢失敗，並出現錯誤訊息「此查詢因為目前的資源條件約束而無法執行」，則表示無伺服器 SQL 集區目前因為資源條件約束而無法執行查詢： 

- 確定您已使用適當大小的資料類型。 此外，請針對字串資料行指定 Parquet 檔案的結構描述，因為其預設會是 VARCHAR (8000)。 

- 如果您的查詢以 CSV 檔案作為目標，請考慮[建立統計資料](develop-tables-statistics.md#statistics-in-serverless-sql-pool)。 

- 請瀏覽[無伺服器 SQL 集區的效能最佳做法](best-practices-sql-on-demand.md)以將查詢最佳化。  

## <a name="create-statement-is-not-supported-in-master-database"></a>master 資料庫不支援 CREATE 'STATEMENT'

如果您的查詢失敗，並出現錯誤訊息：

> 「無法執行查詢。 錯誤：master 資料庫不支援 CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT。」 

這表示無伺服器 SQL 集區中的 master 資料庫不支援建立下列項目：
  - 外部資料表
  - 外部資料來源
  - 資料庫範圍認證
  - 外部檔案格式

解決方案：

  1. 建立使用者資料庫：

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. 在 <DATABASE_NAME> 的內容中執行建立陳述式，先前在 master 資料庫這麼做會失敗。 
  
  建立外部檔案格式的範例：
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>後續步驟

請檢閱下列文章以深入了解如何使用無伺服器 SQL 集區：

- [查詢單一 CSV 檔案](query-single-csv-file.md)

- [查詢資料夾和多個 CSV 檔案](query-folders-multiple-csv-files.md)

- [查詢特定檔案](query-specific-files.md)

- [查詢 Parquet 檔案](query-parquet-files.md)

- [查詢 Parquet 巢狀型別](query-parquet-nested-types.md)

- [查詢 JSON 檔案](query-json-files.md)

- [建立和使用檢視](create-use-views.md)

- [建立和使用外部資料表](create-use-external-tables.md)

- [將查詢結果儲存到儲存體](create-external-table-as-select.md)
