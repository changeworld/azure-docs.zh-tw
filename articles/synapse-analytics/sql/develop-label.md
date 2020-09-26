---
title: 在 Synapse SQL 中使用查詢標籤
description: 本文包含在 Synapse SQL 中使用查詢標籤的重要秘訣。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: e8f78d6031e57da42e1d69587aedca0763c9fec2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289067"
---
# <a name="use-query-labels-in-synapse-sql"></a>在 Synapse SQL 中使用查詢標籤
本文包含在 Synapse SQL 中使用查詢標籤的重要秘訣。

> [!NOTE]
> SQL 隨選 (預覽) 不支援標示查詢。

## <a name="what-are-query-labels"></a>什麼是查詢標籤
SQL 集區支援稱為查詢標籤的概念。 繼續進行之前，讓我們看看一個範例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最後一行將字串 'My Query Label' 標記為查詢。 此標籤很有説明，因為標籤可透過 Dmv 進行查詢。 查詢標籤會提供尋找問題查詢的機制，並且有助於透過 ELT 執行來識別進度。

良好的命名慣例最有説明。 例如，以 PROJECT、PROCEDURE、語句或批註開機磁碟區標，可在原始檔控制中的所有程式碼之間唯一識別查詢。

下列查詢會使用動態管理檢視依標籤進行搜尋：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查詢時，請務必將方括弧或雙引號放在文字標籤兩側。 標籤是一個保留字，不以符號分隔時會導致發生錯誤。 
> 
> 

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](develop-overview.md)。


