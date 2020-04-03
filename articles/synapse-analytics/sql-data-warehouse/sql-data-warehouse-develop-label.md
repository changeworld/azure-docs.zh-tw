---
title: 使用分頁偵測查詢
description: 使用標籤在 Synapse SQL 池中檢測查詢以開發解決方案的提示。
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
ms.openlocfilehash: c1a4ffcab3d10f1dc91ce036e995ae0026a0d718
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619020"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>使用標籤在 Synapse SQL 池中偵測查詢
本文包括使用標籤在 SQL 池中檢測查詢開發解決方案的提示。


## <a name="what-are-labels"></a>什麼是標籤？
SQL 池支援一個稱為查詢標籤的概念。 繼續進行之前，讓我們看看一個範例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最後一行將字串 'My Query Label' 標記為查詢。 此標記很有用,因為標籤可通過 DMV 進行查詢。 

查詢標籤提供可找出問題查詢的機制，並可協助透過 ELT 執行識別進度。

良好的命名慣例非常有幫助。 例如,使用 PROJECT、程式、聲明或註釋啟動標籤可唯一標識原始程式碼管理中的所有代碼之間的查詢。

以下查詢使用動態管理檢視按分頁進行搜尋:

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
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。


