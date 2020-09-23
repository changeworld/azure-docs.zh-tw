---
title: sys.sp_cleanup_data_retention (Transact-sql) -Azure SQL Edge
description: '瞭解如何在 Azure SQL Edge 中使用 sys.sp_cleanup_data_retention (Transact-sql) '
keywords: sys.sp_cleanup_data_retention (Transact-sql) 、SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 9c0a6700a476d4f7f875af5373e3c99bc4e25af2
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934132"
---
# <a name="syssp_cleanup_data_retention-transact-sql"></a>sys.sp_cleanup_data_retention (Transact-SQL)

**適用于：** Azure SQL Edge

從已啟用資料保留原則的資料表中清除過時記錄。 如需詳細資訊，請參閱 [資料保留](data-retention-overview.md)。 

## <a name="syntax"></a>語法 

```sql
sys.sp_cleanup_data_retention   
    { [@schema_name = ] 'schema_name' },  
    { [@table_name = ] 'table_name' },   
    [ [@rowcount =] rowcount OUTPUT ]    

```

## <a name="arguments"></a>引數  
`[ @schema_name = ] schema_name`    
 這是需要執行清除的資料表之擁有架構的名稱。 *schema_name* 是 **sysname**類型的必要參數。
  
`[ @table_name = ] 'table_name'`    
 這是需要執行清除作業的資料表名稱。 *table_name* 是 **sysname**類型的必要參數。

## <a name="output-parameter"></a>輸出參數  

`[ @rowcount = ] rowcount OUTPUT`   
 rowcount 是選擇性的輸出參數，代表從資料表清除的記錄數目。 *rowcount* 是 int。
  
## <a name="permissions"></a>權限    
 需要 db_owner 許可權。

## <a name="next-steps"></a>下一步
- [資料保留和自動資料清除](data-retention-overview.md)
- [使用保留原則管理歷程記錄資料](data-retention-cleanup.md) 
- [啟用及停用資料保留](data-retention-enable-disable.md)
