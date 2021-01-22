---
title: 使用動態 SQL
description: 針對 Azure Synapse Analytics 中的專用 SQL 集區使用動態 SQL 的開發解決方案的秘訣。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 18dcdbf5a4840fda3c2689cc3c8c003470bf1c7f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679596"
---
# <a name="dynamic-sql-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中專用 SQL 集區的動態 SQL

本文提供的秘訣適用于在專用 SQL 集區中使用動態 SQL 的開發解決方案。

## <a name="dynamic-sql-example"></a>動態 SQL 範例

開發專用 SQL 集區的應用程式程式碼時，您可能需要使用動態 SQL，以協助提供有彈性、泛型和模組化的解決方案。 專用的 SQL 集區目前不支援 blob 資料類型。

不支援 Blob 資料類型可能會限制字串大小，因為 Blob 資料類型包括 varchar(max) 與 nvarchar(max) 類型。

如果您在應用程式程式碼中使用這些類型建立大型字串，則必須將程式碼分割成區塊，並改用 EXEC 語句。

以下是簡單的範例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。

> [!NOTE]
> 以動態 SQL 的形式執行的語句仍會受限於所有的 T-sql 驗證規則。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
