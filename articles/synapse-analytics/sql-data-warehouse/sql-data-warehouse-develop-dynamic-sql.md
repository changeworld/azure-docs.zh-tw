---
title: 使用動態 SQL
description: 在 Synapse SQL 集區中使用動態 SQL 之開發解決方案的秘訣。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bce79b8e18b3ec6f1fd139af280086281bbdda98
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213460"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Synapse SQL 集區中的動態 SQL

本文中所包含的秘訣，是在 SQL 集區中使用動態 SQL 的開發解決方案。

## <a name="dynamic-sql-example"></a>動態 SQL 範例

開發 SQL 集區的應用程式程式碼時，您可能需要使用動態 SQL，以協助提供彈性、一般和模組化的解決方案。 SQL 集區目前不支援 blob 資料類型。

不支援 Blob 資料類型可能會限制字串大小，因為 Blob 資料類型包括 varchar(max) 與 nvarchar(max) 類型。

如果您已在應用程式代碼中使用這些型別來建立大型字串，則必須將程式碼分割成區塊，並改為使用 EXEC 語句。

以下是簡單的範例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

> [!NOTE]
> 當做動態 SQL 執行的語句仍然會受到所有 T-sql 驗證規則的規範。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
