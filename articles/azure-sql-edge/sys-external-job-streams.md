---
title: sys.external_job_streams (Transact-SQL) - Azure SQL Edge (預覽)
description: 了解如何在 Azure SQL Edge (預覽) 中使用 sys.external_job_streams
keywords: sys.external_job_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 2634128f4d431e4283f59032c6474a71f2af364d
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84233090"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

針對對應至外部串流作業的輸入或輸出外部串流物件各傳回一個資料列。

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| 串流作業物件的物件識別碼。 此資料行會對應至 sys.external_streaming_jobs 的 object_id 資料行。|
|**stream_id**|**int**| 串流物件的物件識別碼。 此資料行會對應至 sys.external_streams 的 object_id 資料行。 |
|**is_input**|**bit**| 如果使用串流物件作為串流作業的輸入則為 1，否則為 0。|
|**is_output**|**bit**| 如果使用串流物件作為串流作業的輸出則為 1，否則為 0。|

## <a name="example"></a>範例

此目錄檢視會與 `sys.external_streams` 和 `sys.external_streaming_jobs` 目錄檢視一起使用。 範例查詢如下所示

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>權限

目錄檢視內中繼資料的可見性會限制在使用者所擁有的安全性實體，或已授與使用者某些權限的安全性實體。 如需相關資訊，請參閱 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)。

## <a name="see-also"></a>另請參閱

- [目錄檢視 (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [系統檢視 (Transact-SQL)](/sql/t-sql/language-reference/)
