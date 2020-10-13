---
title: 使用標籤來檢測查詢
description: 使用標籤來檢測 Synapse SQL 集區中的查詢以開發解決方案的秘訣。
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
ms.openlocfilehash: 91b6dac5fba4bb8dfd8cf4a3bb4e5952f8388bb5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89459142"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>使用標籤來檢測 Synapse SQL 集區中的查詢

本文提供的秘訣，可讓您使用標籤來檢測 SQL 集區中的查詢，以開發解決方案。

使用標籤來檢測 Azure Synapse Analytics 中的查詢以開發解決方案的秘訣。

## <a name="what-are-labels"></a>什麼是標籤？

SQL 集區支援稱為查詢標籤的概念。 繼續進行之前，讓我們看看一個範例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最後一行將字串 'My Query Label' 標記為查詢。 此標記很有説明，因為標籤可透過 Dmv 進行查詢。

查詢標籤提供可找出問題查詢的機制，並可協助透過 ELT 執行識別進度。

良好的命名慣例非常有幫助。 例如，以 PROJECT、PROCEDURE、語句或批註開機磁碟區標，可在原始檔控制中的所有程式碼之間唯一識別查詢。

下列查詢會使用動態管理檢視依標籤進行搜尋：

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> 查詢時，請務必將方括弧或雙引號放在文字標籤兩側。 標籤是一個保留字，不以符號分隔時會導致發生錯誤。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
