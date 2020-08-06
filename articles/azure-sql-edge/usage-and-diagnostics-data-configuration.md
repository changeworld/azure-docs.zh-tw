---
title: Azure SQL Edge 使用量和診斷資料設定
description: 瞭解如何在 Azure SQL Edge 中設定使用方式和診斷資料。
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 8547c07214e94176babe4909504b9292d45c06f9
ms.sourcegitcommit: 5a37753456bc2e152c3cb765b90dc7815c27a0a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87759609"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Azure SQL Edge 使用量和診斷資料設定

根據預設，Azure SQL Edge 會收集其客戶如何使用應用程式的相關資訊。 具體而言，Azure SQL Edge 會收集有關部署體驗、使用方式和效能的資訊。 這些資訊可協助 Microsoft 改善產品，以進一步滿足客戶需求。 例如，Microsoft 會收集有關客戶所遇到之錯誤碼類型的資訊，以便修正相關的錯誤、改善如何使用 Azure SQL Edge 的檔，以及判斷是否應將功能新增至產品，以提供更好的服務給客戶。

具體來說，Microsoft 不會透過此機制傳送下列類型的資訊：

- 使用者資料表內的任何值。
- 任何登入認證或其他驗證資訊。
- 任何個人或客戶資料。

下列範例案例包含可協助改善產品的功能使用方式資訊。

以下提供用於使用方式和診斷資料收集之查詢的範例查詢。 查詢會識別 Azure SQL Edge 中所使用之不同串流資料來源的計數和類型。 這項資料可協助 Microsoft 找出常用的串流資料來源，讓 Microsoft 能夠改善與這些資料來源相關聯的效能和使用者經驗。 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type  
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job  
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es              
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id             
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js                
             on js.stream_id = es.object_id 
    ) ds            
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds        
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>停用使用方式和診斷資料收集

您可以使用下列其中一種方法來停用 Azure SQL Edge 上的使用方式和診斷資料收集。

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>使用環境變數停用使用方式和診斷

若要在 Azure SQL Edge 上停用使用方式和診斷資料收集，請新增下列環境變數，並將其值設定為 `*False*` 。 如需使用環境變數來設定 Azure SQL Edge 的詳細資訊，請參閱[使用環境變數進行設定](configure.md#configure-by-using-environment-variables)。

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE-啟用收集使用方式和診斷資料。 這是預設組態。
- FALSE-停用使用方式和診斷資料的收集

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>使用 mssql. 檢測檔案停用使用方式和診斷

若要停用 Azure SQL Edge 上的使用量和診斷資料收集，請在持續儲存磁片磁碟機（對應至 SQL Edge 模組中的/var/opt/mssql/資料夾）的 mssql 檔案中，新增下列檔案。 如需有關使用 mssql 檔案來設定 Azure SQL Edge 的詳細資訊，請參閱[使用 mssql](configure.md#configure-by-using-an-mssqlconf-file)檔案進行設定。

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>使用方式和診斷資料收集的本機審核

Azure SQL Edge 使用量和診斷資料收集的本機 Audit 元件可以將服務所收集的資料寫入指定的資料夾，代表將傳送給 Microsoft 的資料 (記錄) 。 本機稽核的目的是要讓客戶看到 Microsoft 以此功能收集的所有資料，以用於相容性、法規或隱私權驗證的理由。

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>啟用使用方式和診斷資料的本機審核

啟用 Azure SQL Edge 上的本機審核使用方式和診斷資料

1. 為新的本機 Audit 記錄儲存體建立目標目錄。 此目標目錄可以在主機上或在容器內。 在下列範例中，會在與 SQL Edge 上對應至/var/opt/mssql/路徑的相同裝入磁片區中建立目標目錄。

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. 使用環境變數或 mssql. 檢測檔案來設定使用狀況和診斷資料的審核。

   - 使用環境變數-將下列環境變數新增至您的 SQL Edge 部署，並指定 audit 檔案的目標目錄。
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - 使用 mssql 檔案-在 mssql 檔案中新增下列幾行，並指定 audit 檔案的目標目錄。
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>後續步驟

- [連線到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 建立端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)
