---
title: 在 Synapse SQL 中使用查詢標籤
description: 本文中所包含的重要秘訣，是在 Synapse SQL 中使用查詢標籤。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81430028"
---
# <a name="use-query-labels-in-synapse-sql"></a>在 Synapse SQL 中使用查詢標籤
本文中所包含的重要秘訣，是在 Synapse SQL 中使用查詢標籤。

> [!NOTE]
> SQL 隨選（預覽）不支援標示查詢。

## <a name="what-are-query-labels"></a>什麼是查詢標籤
SQL 集區支援稱為「查詢標籤」的概念。 繼續進行之前，讓我們看看一個範例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最後一行將字串 'My Query Label' 標記為查詢。 此標籤特別有幫助，因為標籤可透過 DMV 查詢。 查詢標籤會提供一個機制來尋找問題查詢，並協助您透過 ELT 執行識別進度。

良好的命名慣例最有用。 例如，以專案、程式、語句或批註開頭的標籤，可唯一識別原始檔控制中所有程式碼之間的查詢。

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


