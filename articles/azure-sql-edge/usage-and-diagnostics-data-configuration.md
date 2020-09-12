---
title: Azure SQL Edge 使用量和診斷資料設定
description: 瞭解如何在 Azure SQL Edge 中設定使用量和診斷資料。
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: af4353e5b0991f7ce2f6fe8ff940e916717b0579
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650488"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Azure SQL Edge 使用量和診斷資料設定

根據預設，Azure SQL Edge 會收集其客戶如何使用應用程式的相關資訊。 具體而言，Azure SQL Edge 會收集部署體驗、使用方式和效能的相關資訊。 這些資訊可協助 Microsoft 改善產品，以進一步滿足客戶需求。 例如，Microsoft 會收集客戶所遇到之錯誤碼類型的相關資訊，讓我們能夠修正相關的錯誤、改善有關如何使用 Azure SQL Edge 的檔，並判斷是否應該將功能新增至產品，以提供更好的服務給客戶。

具體來說，Microsoft 不會透過此機制傳送下列類型的資訊：

- 來自使用者資料表內的任何值。
- 任何登入認證或其他驗證資訊。
- 任何個人或客戶資料。

下列範例案例包含可協助改善產品的功能使用方式資訊。

以下提供用於使用方式和診斷資料收集的查詢查詢範例。 此查詢會識別 Azure SQL Edge 中所使用之不同串流資料來源的計數和類型。 這項資料可協助 Microsoft 找出目前使用的串流資料來源，讓 Microsoft 可以改善與這些資料來源相關聯的效能和使用者體驗。 

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

若要在 Azure SQL Edge 上停用使用量和診斷資料收集，請新增下列環境變數，並將其值設定為 `*False*` 。 如需使用環境變數來設定 Azure SQL Edge 的詳細資訊，請參閱 [使用環境變數進行設定](configure.md#configure-by-using-environment-variables)。

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE-啟用收集使用方式和診斷資料。 這是預設組態。
- FALSE-停用收集使用方式和診斷資料

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>使用 mssql 檔案停用使用方式和診斷

若要在 Azure SQL Edge 上停用使用量和診斷資料收集，請在與 SQL Edge 模組中/var/opt/mssql/資料夾對應的持續性存放磁片磁碟機上，于 mssql 檔案中新增下列幾行。 如需使用 mssql 檔案設定 Azure SQL Edge 的詳細資訊，請參閱 [使用 mssql](configure.md#configure-by-using-an-mssqlconf-file)檔案進行設定。

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>使用方式和診斷資料收集的本機審核

Azure SQL Edge 使用量和診斷資料收集的本機 Audit 元件可以將服務收集的資料寫入指定的資料夾，代表將傳送給 Microsoft 的資料 (記錄) 。 本機稽核的目的是要讓客戶看到 Microsoft 以此功能收集的所有資料，以用於相容性、法規或隱私權驗證的理由。

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>啟用使用方式和診斷資料的本機審核

啟用 Azure SQL Edge 上的本機 Audit 使用量和診斷資料

1. 針對新的本機 Audit log storage 建立目標目錄。 這個目標目錄可以位於主機或容器內。 在下列範例中，會在對應至 SQL Edge 上/var/opt/mssql/路徑的相同裝入磁片區中建立目標目錄。

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. 使用環境變數或 mssql 檔案，設定使用環境變數或 mssql 檔案來審核使用狀況和診斷資料。

   - 使用環境變數-將下列環境變數新增至您的 SQL Edge 部署，並指定該審核檔案的目標目錄。
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - 使用 mssql 檔案-在 mssql 檔案中新增下列幾行，並指定審核檔案的目標目錄。
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>接下來的步驟

- [連線到 Azure SQL Edge](connect.md)
- [使用 SQL Edge 建立端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)
