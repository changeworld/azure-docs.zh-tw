---
title: Azure Synapse Analytics 共用資料庫
description: Azure Synapse Analytics 會提供共用中繼資料模型，只要是在 Apache Spark 中建立的資料庫，便可從其 SQL 隨選 (預覽) 和 SQL 集區引擎來加以存取。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420212"
---
# <a name="azure-synapse-analytics-shared-database"></a>Azure Synapse Analytics 共用資料庫

Azure Synapse Analytics 可讓不同的計算工作區引擎在其 Spark 集區 (預覽)、SQL 隨選 (預覽) 引擎和 SQL 集區之間共用資料庫和資料表。

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

使用 Spark 作業所建立的資料庫將會以同樣的名稱顯示給工作區中所有目前和未來的 Spark 集區 (預覽) 以及 SQL 隨選引擎。

如果工作區中有已經啟用中繼資料同步處理的 SQL 集區，或如果您建立啟用了中繼資料同步處理的新 SQL 集區，則這些 Spark 所建資料庫便會自動對應至 SQL 集區資料庫中的特殊結構描述。 

每個結構描述都會以 Spark 資料庫名稱再加上 `$` 前置詞來命名。 Spark 所產生資料庫中的外部和受控資料表都會在對應的特殊結構描述中公開為外部資料表。

Spark 預設資料庫 (稱為 `default`) 也會在 SQL 隨選內容中顯示為稱為 `default` 的資料庫，而在任何已開啟中繼資料同步處理的 SQL 集區資料庫中則會顯示為結構描述 `$default`。

由於資料庫不會即時同步至 SQL 及 SQL 集區，因此資料表會延遲顯示。

## <a name="manage-a-spark-created-database"></a>管理 Spark 建立的資料庫

使用 Spark 來管理 Spark 建立的資料庫。 例如，透過 Spark 集區作業來刪除資料庫，以及從 Spark 建立其中的資料表。

如果您使用 SQL 隨選在 Spark 所建資料庫中建立物件，或嘗試卸除資料庫，則作業將會成功。 但是，原始的 Spark 資料庫不會有任何改變。

如果您嘗試卸除 SQL 集區中已同步的結構描述，或嘗試在其中建立資料表，Azure 會傳回錯誤。

## <a name="handling-of-name-conflicts"></a>名稱衝突的處理方式

如果 Spark 資料庫的名稱與現有 SQL 隨選資料庫的名稱衝突，則會在 SQL 隨選中為 Spark 資料庫附加尾碼。 SQL 隨選中的尾碼是 `_<workspace name>-ondemand-DefaultSparkConnector`。

例如，如果名為 `mydb` 的 Spark 資料庫是在 Azure Synapse 工作區 `myws` 中建立的，且已存在具有該名稱的 SQL 隨選資料庫，則必須使用名稱 `mydb_myws-ondemand-DefaultSparkConnector` 來參考 SQL 隨選中的 Spark 資料庫。

> [!CAUTION]
> 注意：請勿依賴此行為。

## <a name="security-model"></a>安全性模型

Spark 資料庫和資料表，以及其在 SQL 引擎中的同步代表項目，將會在基礎儲存層級受到保護。

系統會將建立資料庫的安全性主體視為該資料庫的擁有者，且擁有者會擁有資料庫及其物件的所有權限。

若要為安全性主體 (例如，使用者或安全性群組) 提供資料庫的存取權，請為 `warehouse` 目錄中的基礎資料夾和檔案提供適當的 POSIX 資料夾和檔案權限。 

例如，為了讓安全性主體能夠讀取資料庫中的資料表，`warehouse` 目錄中從資料庫資料夾開始的所有資料夾都必須將 `X` 和 `R` 權限指派給該安全性主體。 此外，檔案 (例如，資料表的基礎資料檔案) 也需要 `R` 權限。 

如果安全性主體需要能夠在資料庫中建立物件或卸除物件，則在 `warehouse` 資料夾中的資料夾和檔案便需要額外的 `W` 權限。

## <a name="examples"></a>範例

### <a name="create--connect-to-spark-database---sql-on-demand"></a>建立 Spark 資料庫並與其連線 - SQL 隨選

首先，請使用您已在工作區中建立的 Spark 叢集來建立名為 `mytestdb` 的新 Spark 資料庫。 例如，您可以使用 Spark C# Notebook 搭配下列 .NET for Spark 陳述式來達成此目的：

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

在短暫延遲過後，您就可以從 SQL 隨選看到該資料庫。 例如，從 SQL 隨選執行下列陳述式。

```sql
SELECT * FROM sys.databases;
```

確認結果中包含 `mytestdb`。

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>在 SQL 集區中公開 Spark 資料庫

在上一個範例建立了資料庫之後，現在請於工作區中建立名為 `mysqlpool` 的 SQL 集區，並啟用中繼資料同步處理。

針對 `mysqlpool` SQL 集區執行下列陳述式：

```sql
SELECT * FROM sys.schema;
```

在結果中確認所新建資料庫的結構描述。

## <a name="next-steps"></a>後續步驟

- [深入了解 Azure Synapse Analytics 的共用中繼資料](overview.md)
- [深入了解 Azure Synapse Analytics 的共用中繼資料資料表](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
