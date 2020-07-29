---
title: ALTER EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge (預覽)
description: 了解 Azure SQL Edge 中的 ALTER EXTERNAL STREAM 陳述式 (預覽)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84235204"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER EXTERNAL STREAM (Transact-SQL)

修改外部串流的定義。 不允許修改處於*執行中*狀態的串流作業所使用的外部串流。 



## <a name="syntax"></a>語法

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>引數

如需 Alter External Stream 命令引數的詳細資訊，請參閱 [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md)。

## <a name="return-code-values"></a>傳回碼值

如果成功，ALTER EXTERNAL STREAM 會傳回 0。 傳回值不是 0 就表示失敗。


## <a name="see-also"></a>另請參閱

- [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 
