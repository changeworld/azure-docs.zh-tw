---
title: 使用動態 SQL
description: 在 Synapse SQL 池中使用動態 SQL 的開發解決方案的提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5a285c273a0bc590a9f5b4ade782f2195a361cd6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619041"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Synapse SQL 池中的動態 SQL
本文包括使用 SQL 池中動態 SQL 的開發解決方案的提示。

## <a name="dynamic-sql-example"></a>動態 SQL 範例

在為 SQL 池開發應用程式代碼時,您可能需要使用動態 SQL 來説明提供靈活的通用和模組化解決方案。 SQL 池目前不支援 blob 資料類型。 

不支援 Blob 資料類型可能會限制字串大小，因為 Blob 資料類型包括 varchar(max) 與 nvarchar(max) 類型。 

如果在應用程式代碼中使用了這些類型的生成大字串,則需要將代碼分解為塊,改用 EXEC 語句。

以下是簡單的範例：

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

如果字串簡短，您可以像平常一樣使用 [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql)。

> [!NOTE]
> 作為動態 SQL 執行的語句仍將受所有 T-SQL 驗證規則的約束。
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。

