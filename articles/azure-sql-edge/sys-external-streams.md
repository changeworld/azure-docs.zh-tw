---
title: sys.external_streams (Transact-SQL) - Azure SQL Edge (預覽)
description: 了解如何在 Azure SQL Edge (預覽) 中使用 sys.external_streams
keywords: sys.external_streams, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 8200d1814537a76db357704d6baf3bf482c587e7
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235114"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

針對在資料庫範圍內建立的每個外部串流物件傳回一個資料列。

|資料行名稱|資料類型|描述|  
|-----------------|---------------|-----------------|
|**name**|**sysname**|串流的名稱。 在資料庫中，這是唯一的。|
|object_id|**int**|串流物件的物件識別碼。 在資料庫中，這是唯一的。|
|**principal_id**|**int**|擁有這個組件的主體識別碼|
|**schema_id**|**int**| 包含物件的結構描述識別碼。|
|**parent_object_id**|**id**| 此串流中父代物件的物件識別碼。 在目前的實作中，此值一律為 Null|
|**type**|**char(2)**|物件類型。 若為串流物件，類型一律為 'EJ'|
|**type_desc**|**nvarchar(60)**| 物件類型的描述。 若為串流物件，類型一律為 'EXTERNAL_STREAM'|
|**create_date**|**datetime**| 物件的建立日期。|
|**modify_date**|**datetime**| 上次利用 ALTER 陳述式來修改物件的日期。|
|**is_ms_shipped**|**bit**| 內部元件所建立的物件。|  
|**is_published**|**bit**|已發行物件。|  
|**is_schema_published**|**bit**|僅發行物件的結構描述。|
|**max_column_id_used**|**bit**| 此資料行用於內部用途，未來將會移除|  
|**uses_ansi_nulls**|**bit**| 在 SET ANSI_NULLS 資料庫選項為 ON 的情況下建立串流物件|
|**data_source_id**|**int**| 串流物件所代表外部資料來源的物件識別碼 |  
|**file_format_id**|**int**| 串流物件所使用外部檔案格式的物件識別碼。 必須使用外部檔案格式，才能指定外部串流所參考資料的實際配置| 
|**location**|**varchar(max)**| 外部串流物件的目標。 如需詳細資訊，請參閱[建立外部串流](overview.md) |
|**input_option**|**varchar(max)**| 在建立外部串流期間所使用的輸入選項。 如需詳細資訊，請參閱[建立外部串流](overview.md) |
|**output_option**|**varchar(max)**| 在建立外部串流期間所使用的輸出選項。 如需詳細資訊，請參閱[建立外部串流](overview.md) | 

## <a name="permissions"></a>權限

目錄檢視內中繼資料的可見性會限制在使用者所擁有的安全性實體，或已授與使用者某些權限的安全性實體。 如需相關資訊，請參閱 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)。

## <a name="see-also"></a>另請參閱

- [目錄檢視 (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [系統檢視 (Transact-SQL)](/sql/t-sql/language-reference/)
