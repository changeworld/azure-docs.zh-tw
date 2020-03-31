---
title: Lightingest 是一個命令列實用程式，用於引入 Azure 資料資源管理器。
description: 瞭解 LightIngest，這是一個用於將臨時資料引入 Azure 資料資源管理器的命令列實用程式。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 99517e45892cd7a6167ae83ff3058edae1377b10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109559"
---
# <a name="install-and-use-lightingest"></a>安裝和使用最亮燈

Lightingest 是一個命令列實用程式，用於將臨時資料引入 Azure 資料資源管理器。
該實用程式可以從本地資料夾或 Azure Blob 存儲容器中提取來源資料。

## <a name="prerequisites"></a>Prerequisites

* 最輕 - 作為[Microsoft.Azure.Kusto.Tools NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)的一部分下載它
* WinRAR - 從[www.win-rar.com/download.html](http://www.win-rar.com/download.html)下載

## <a name="install-lightingest"></a>安裝最亮燈

1. 導航到您下載的"燈光"電腦上的位置。 
1. 使用 WinRAR，將*工具*目錄提取到您的電腦。

## <a name="run-lightingest"></a>運行照明

1. 導航到電腦上的提取*工具*目錄。
1. 從位置欄中刪除現有位置資訊。
    
      ![刪除位置資訊](media/lightingest/lightingest-location-bar.png)

1. 輸入`cmd`並按**Enter**。
1. 在命令提示符下，`LightIngest.exe`輸入後跟相關的命令列參數。

    > [!Tip]
    > 有關受支援的命令列參數的清單，請輸入`LightIngest.exe /help`。
    >
    >![命令列説明](media/lightingest/lightingest-cmd-line-help.png)

1. 輸入`LightIngest`後跟將管理引入的 Azure 資料資源管理器群集的連接字串。
    將連接字串括在雙引號中，並遵循[Kusto 連接字串規範](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto)。

    例如：
    ```
    LightIngest "Data Source=https://{Cluster name and region}.kusto.windows.net;AAD Federated Security=True"  -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 建議的方法用於`LightIngest`使用 中的`https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`引入終結點。 這樣，Azure 資料資源管理器服務可以管理引入負載，並且可以輕鬆地從瞬態錯誤中恢復。 但是，您還可以配置為`LightIngest`直接使用引擎終結點 （）。`https://{yourClusterNameAndRegion}.kusto.windows.net`
* 為了獲得最佳的攝入性能，LightIngest 必須瞭解原始資料大小，因此`LightIngest`可以估計本地檔的未壓縮大小。 但是，`LightIngest`如果不首先下載壓縮 blob，可能無法正確估計壓縮 blob 的原始大小。 因此，在引入壓縮的 Blob 時，將`rawSizeBytes`blob 中繼資料上的屬性設置為以位元組為單位的未壓縮資料大小。

## <a name="general-command-line-arguments"></a>一般命令列參數

|引數名稱         |簡短名稱   |類型    |強制性 |描述                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |字串  |強制性 |[Azure 資料資源管理器連接字串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto)，指定將處理引入的 Kusto 終結點。 應以雙引號括起來 |
|-資料庫             |-db          |字串  |選用  |目標 Azure 資料資源管理器資料庫名稱 |
|-表                |             |字串  |強制性 |目標 Azure 資料資源管理器表名稱 |
|-sourcePath           |-source      |字串  |強制性 |blob 容器的原始檔案或根 URI 的路徑。 如果資料在 Blob 中，則必須包含存儲帳戶金鑰或 SAS。 建議以雙引號括起來 |
|-首碼               |             |字串  |選用  |當要引入的來源資料駐留在 Blob 存儲上時，此 URL 首碼由所有 Blob 共用，不包括容器名稱。 <br>例如，如果資料在 中`MyContainer/Dir1/Dir2`，則首碼應為`Dir1/Dir2`。 建議用雙引號括起來 |
|-模式              |             |字串  |選用  |選取原始檔案/blob 的模式。 支援萬用字元。 例如： `"*.csv"` 。 建議以雙引號括起來 |
|-zipPattern           |             |字串  |選用  |在選擇要引入的 ZIP 存檔中的檔時使用的正則運算式。<br>存檔中的所有其他檔將被忽略。例如， `"*.csv"`. 建議用雙引號括起來 |
|-格式               |-f           |字串  |選用  |源資料格式。 必須是[受支援的格式](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#supported-data-formats)之一 |
|-引入映射路徑 |-映射路徑 |字串  |選用  |引入列映射檔的路徑（Json 和 Avro 格式的必需）。 查看[資料對應](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-引入映射參考  |-映射參考  |字串  |選用  |預先創建的引入列映射的名稱（Json 和 Avro 格式的必需）。 查看[資料對應](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-創建時間模式  |             |字串  |選用  |設置後，用於從檔或 Blob 路徑中提取創建時間屬性。 請參閱[使用創建時間模式參數](#using-creationtimepattern-argument) |
|-忽略第一行       |-忽略第一 |bool    |選用  |如果設置，將忽略每個檔/blob 的第一個記錄（例如，如果來源資料具有標頭） |
|-標籤                  |             |字串  |選用  |要與引入的資料關聯的[標記](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging)。 允許多次發生 |
|-不要等待             |             |bool    |選用  |如果設置為"true"，則不等待攝入完成。 攝入大量檔/blob 時非常有用 |

### <a name="using-creationtimepattern-argument"></a>使用創建時間模式參數

參數`-creationTimePattern`從檔或 blob 路徑中提取創建時間屬性。 模式不需要反映整個專案路徑，只需反映不包含要使用的時間戳記的部分。
參數的值必須包含三個部分：
* 時間戳記前面的常量測試，以單引號括起來
* 時間戳記格式，標準為[.NET 日期時程表示法](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* 時間戳記之後的常量文本 例如，如果 blob 名稱以"歷史值 19840101.parquet"結尾（時間戳記為年份的四位數位、該月的兩位數位和月份當天的兩位數位），`-creationTimePattern`則參數的相應值為"歷史值"yyymMdd'.parquet"。

### <a name="command-line-arguments-for-advanced-scenarios"></a>高級方案的命令列參數

|引數名稱         |簡短名稱   |類型    |強制性 |描述                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-壓縮          |-cr          |double  |選用  |壓縮比提示。 引入壓縮檔/blob 以説明 Azure 資料資源管理器評估原始資料大小時非常有用。 按壓縮大小除以原始大小 |
|-限制                |-l           |integer |選用  |如果設置，則將引入限制為前 N 個檔 |
|-僅清單             |-list        |bool    |選用  |如果設置，則僅顯示已選擇要攝入的專案| 
|-引入超時        |             |integer |選用  |所有引入操作完成以分鐘表示超時。 預設為 `60`|
|-力同步            |             |bool    |選用  |如果設置，則強制同步攝入。 預設為 `false` |
|-資料批量大小        |             |integer |選用  |設置每個引入操作的總大小限制（MB、未壓縮） |
|-檔在巴奇         |             |integer |選用  |設置每個引入操作的檔/Blob 計數限制 |
|-dev跟蹤           |-跟蹤       |字串  |選用  |如果設置，診斷日誌將寫入本地目錄（預設情況下，`RollingLogs`在目前的目錄中，或者可以通過設置開關值進行修改） |

## <a name="blob-metadata-properties"></a>Blob 中繼資料屬性
當與 Azure Blob`LightIngest`一起使用時，將使用某些 blob 中繼資料屬性來增強引入過程。

|中繼資料屬性                            | 使用量                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 如果設置，將解釋為未壓縮的資料大小                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | 解釋為 UTC 時間戳記。 如果設置，將用於覆蓋 Kusto 中的創建時間。 適用于回填方案 |

## <a name="usage-examples"></a>使用範例

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>使用存儲帳戶金鑰或 SAS 權杖引入 Blob

* 在指定的存儲帳戶`ACCOUNT`下、在資料夾中`DIR`、容器`CONT`下引入 10 個 blob，並匹配模式`*.csv.gz`
* 目標是資料庫`DB`、表`TABLE`，在目標上預先創建引入`MAPPING`映射
* 該工具將等待，直到引入操作完成
* 請注意指定目標資料庫和存儲帳戶金鑰與 SAS 權杖的不同選項

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>引入容器中的所有 Blob，不包括標頭行

* 在指定的存儲帳戶`ACCOUNT`下、資料夾中`DIR1/DIR2`、容器`CONT`下和匹配模式下引入所有 Blob`*.csv.gz`
* 目標是資料庫`DB`、表`TABLE`，在目標上預先創建引入`MAPPING`映射
* 源 Blob 包含標題列，因此指示工具刪除每個 Blob 的第一條記錄
* 該工具將發佈用於攝取的資料，並且不會等待引入操作完成

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>從路徑中引入所有 JSON 檔

* 引入路徑`PATH`下的所有檔，與模式匹配`*.json`
* 目標是資料庫`DB`、`TABLE`表 ，引入映射在本地檔中定義`MAPPING_FILE_PATH`
* 該工具將發佈用於攝取的資料，並且不會等待引入操作完成

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>引入檔和寫入診斷追蹤檔案

* 引入路徑`PATH`下的所有檔，與模式匹配`*.json`
* 目標是資料庫`DB`、`TABLE`表 ，引入映射在本地檔中定義`MAPPING_FILE_PATH`
* 該工具將發佈用於攝取的資料，並且不會等待引入操作完成
* 診斷追蹤檔案將在資料夾下本地寫入`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>變更記錄
|版本        |變更                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>已新增 `-zipPattern` 引數 </li><li>已新增 `-listOnly` 引數 </li><li>在開始運行之前顯示參數摘要</li><li>根據`-creationTimePattern`參數，從 Blob 中繼資料屬性或 blob 或檔案名讀取創建時間</li></ul>|
