---
title: 在 SynapsE SQL 中使用查詢分頁
description: 本文中包括在 Synapse SQL 中使用查詢標籤的基本提示。
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430028"
---
# <a name="use-query-labels-in-synapse-sql"></a>在 SynapsE SQL 中使用查詢分頁
本文中包括在 Synapse SQL 中使用查詢標籤的基本提示。

> [!NOTE]
> SQL 按需(預覽)不支援標記查詢。

## <a name="what-are-query-labels"></a>什麼是查詢分頁
SQL 池支援一個稱為查詢標籤的概念。 繼續進行之前，讓我們看看一個範例：

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

最後一行將字串 'My Query Label' 標記為查詢。 此標籤特別有幫助，因為標籤可透過 DMV 查詢。 對標籤的查詢提供了一種查找問題查詢的機制,並有助於通過 ELT 運行辨識進度。

良好的命名約定最有説明。 例如,使用 PROJECT、程式、聲明或註釋啟動標籤可唯一標識原始程式碼管理中的所有代碼之間的查詢。

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
如需更多開發秘訣，請參閱[開發概觀](develop-overview.md)。


