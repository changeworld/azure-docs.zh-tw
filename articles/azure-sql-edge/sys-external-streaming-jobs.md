---
title: sys.external_streaming_jobs (Transact-sql) -Azure SQL Edge
description: 瞭解如何在 Azure SQL Edge 中使用 sys.external_streaming_jobs
keywords: sys.external_streaming_jobs, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 9643c58f5c9fa1db3e3eb7ec75ce6d3b41620aa3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90900341"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

針對在資料庫範圍內建立的每個外部串流工作傳回一個資料列。

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|資料流的名稱。 在資料庫中，這是唯一的。|
|object_id|**int**|資料流物件的物件識別碼。 在資料庫中，這是唯一的。|
|**principal_id**|**int**|擁有這個組件的主體識別碼|
|**schema_id**|**int**| 包含物件的結構描述識別碼。|
|**parent_object_id**|**id**| 此資料流中父代物件的物件識別碼。 在目前的實作中，此值一律為 null|
|**type**|**char(2)**|物件類型。 若為資料流物件，類型一律為 'EJ'|
|**type_desc**|**nvarchar(60)**| 物件類型的描述。 若為資料流物件，類型一律為 'EXTERNAL_STREAMING_JOB'|
|**create_date**|**datetime**| 物件的建立日期。|
|**modify_date**|**datetime**| 在目前的實作中，此值與資料流物件的 create_date 相同 |
|**is_ms_shipped**|**bit**| 內部元件所建立的物件。|  
|**is_published**|**bit**| 已發行物件。|  
|**is_schema_published**|**bit**|僅發行物件的結構描述。|
|**uses_ansi_nulls**|**bit**| 在 SET ANSI_NULLS 資料庫選項為 ON 的情況下建立資料流物件|
|**陳述式**|**varchar(max)**| 串流作業的資料流分析查詢文字。 如需詳細資訊，請參閱 [sp_create_streaming_job](overview.md) |
|**status**|**int**| 串流作業的目前狀態。 可能的值為 <br /><br /> **已建立** = 0。 已建立串流作業，但尚未啟動。 <br /><br /> **啟動中** = 1。 串流作業處於正在啟動的階段。 <br /><br /> **失敗** = 6。 串流作業失敗。 這通常表示處理期間發生嚴重錯誤。 <br /><br /> **已停止** = 4。 串流作業已停止。 <br /><br /> **閒置** = 7。 串流作業正在執行，但沒有任何要處理的輸入。 <br /><br /> **處理中** = 8。 串流作業正在執行，且正在處理輸入。 此狀態表示串流作業處於健全狀態。 <br /><br /> **已降級** = 9。 串流作業正在執行，但在處理輸入期間，發生一些非嚴重性的輸入/輸出序列化/還原序列化錯誤。 輸入作業會繼續執行，但會捨棄發生錯誤的輸入。|

## <a name="permissions"></a>權限

目錄檢視內中繼資料的可見性會限制在使用者所擁有的安全性實體，或已授與使用者某些權限的安全性實體。 如需相關資訊，請參閱 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)。

## <a name="see-also"></a>另請參閱

- [T-SQL 串流目錄檢視](overview.md)
- [目錄檢視 (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [系統檢視 (Transact-SQL)](/sql/t-sql/language-reference/)

