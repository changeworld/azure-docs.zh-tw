---
title: 如何將來自不同來源的資料摘要新增至計量建議程式
titleSuffix: Azure Cognitive Services
description: 將不同的資料摘要新增至計量 Advisor
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: c4d1d23da5fd9678cc5b9477ddeed0daf4f5ac36
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348614"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>將來自不同資料來源的資料摘要新增至計量 Advisor

您可以使用此文章來尋找將不同類型的資料來源連接到計量建議程式的設定和需求。 請務必閱讀如何上 [架您的資料](how-tos/onboard-your-data.md) ，以瞭解將您的資料與計量建議程式搭配使用的重要概念。 

## <a name="supported-authentication-types"></a>支援的驗證類型

| 驗證類型 | 描述 |
| ---------------------|-------------|
|**基本** | 您必須能夠提供基本參數來存取資料來源。 例如，連接字串或索引鍵。 資料摘要管理員可以查看這些認證。 |
| **AzureManagedIdentity** | 適用于 Azure 資源的[受控](../../active-directory/managed-identities-azure-resources/overview.md)識別是 Azure Active Directory 的功能。 它會在 Azure AD 中為 Azure 服務提供自動管理的身分識別。 您可以使用此身分識別來向任何支援 Azure AD authentication 的服務進行驗證。|
| **AzureSQLConnectionString**| 將 AzureSQL 連接字串儲存為計量建議程式中的 **認證實體** ，並在上架度量資料時，直接使用它。 只有認證實體的系統管理員能夠查看這些認證，但是可讓授權的檢視器建立資料摘要，而不需要知道認證的詳細資料。 |
| **DataLakeGen2SharedKey**| 將您的 data lake 帳戶金鑰儲存為計量建議程式中的 **認證實體** ，並在上架度量資料時，直接使用它。 只有認證實體的系統管理員能夠查看這些認證，但是可讓授權的檢視器建立資料摘要，而不需要知道認證詳細資料。|
| **服務主體**| 將您的服務主體儲存為計量建議程式中的 **認證實體** ，並在上架度量資料時，直接使用它。 只有認證實體的管理員能夠查看認證，但可讓授權的檢視器建立資料摘要，而不需要知道認證詳細資料。|
| **Key vault 中的服務主體**|將您的服務主體儲存在金鑰保存庫中做為計量建議程式中的 **認證實體** ，並在上架度量資料時，直接使用它。 只有 **認證實體** 的系統管理員能夠查看認證，但也讓檢視器能夠建立資料摘要，而不需要知道詳細的認證。 |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>支援的資料來源和對應的驗證類型


| 資料來源 | 驗證類型 |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  基本 |
|[**Azure Blob 儲存體 (JSON)**](#blob) | 基本<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | 基本 |
|[**Azure 資料總管 (Kusto)**](#kusto) | 基本<br>ManagedIdentity|
|[**Azure Data Lake Storage Gen2**](#adl) | 基本<br>DataLakeGen2SharedKey<br>服務主體<br>Key vault 中的服務主體<br> |
|[**Azure SQL Database/SQL Server**](#sql) | 基本<br>ManagedIdentity<br>服務主體<br>Key vault 中的服務主體<br>AzureSQLConnectionString
|[**Azure 表格儲存體**](#table) | 基本 | 
|[**ElasticSearch**](#es) | 基本 |
|[**Http 要求**](#http) | 基本 | 
|[**InfluxDB (InfluxQL)**](#influxdb) | 基本 |
|[**MongoDB**](#mongodb) | 基本 |
|[**MySQL**](#mysql) | 基本 |
|[**PostgreSQL**](#pgsql)| 基本|

建立 **認證實體** ，並用它來驗證您的資料來源。 下列各節會指定 *基本* 身份驗證所需的參數。 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **應用程式識別碼**：使用 Application Insights API 時，這會用來識別此應用程式。 若要取得應用程式識別碼，請執行下列動作：

    1. 從您的 Application Insights 資源，按一下 [API 存取權]  。

    2. 將產生的應用程式識別碼複製到計量建議程式中的 [ **應用程式識別碼** ] 欄位。 
    
    如需詳細資訊，請參閱 [Azure Bot Service 檔](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) 。

* **Api 金鑰**：瀏覽器外的應用程式會使用 api 金鑰來存取此資源。 若要取得 API 金鑰，請執行下列動作：

    1. 從 Application Insights 資源，按一下 [API 存取權]  。

    2. 按一下 [建立 API 金鑰]  。

    3. 輸入簡短的描述，勾選 [讀取遙測] 選項，然後按一下 [產生金鑰] 按鈕。

    4. 將 API 金鑰複製到計量建議程式中的 [ **api 金鑰** ] 欄位。

* **查詢**： Azure 應用程式見解記錄建置於 Azure 資料總管，而且 Azure 監視器記錄查詢會使用相同 Kusto 查詢語言的版本。 [Kusto 查詢語言檔](/azure/data-explorer/kusto/query/)包含語言的所有詳細資料，而且應該是您用來針對 Application Insights 撰寫查詢的主要資源。 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob 儲存體 (JSON) </span>

* **連接字串**：如需有關如何取得此字串的詳細資訊，請參閱 Azure Blob 儲存體 [連接字串](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) 文章。

* **容器**：計量顧問預期會將時間序列資料儲存為 blob 檔案， (單一容器下的每個時間戳記) 一個 blob。 這是 [容器名稱] 欄位。

* **Blob 範本**：這是 blob 檔案名的範本。 例如：`/%Y/%m/X_%Y-%m-%d-%h-%M.json`。 支援下列參數：
  * `%Y` 年份的格式為 `yyyy`
  * `%m` 月份的格式為 `MM`
  * `%d` 日期格式為 `dd`
  * `%h` 為一小時的格式為 `HH`
  * `%M` 是格式化為的分鐘 `mm`

* **Json 格式版本**：定義 json 檔案中的資料架構。 計量審查程式目前支援兩種版本：
  
  * v1 (預設值) 

      只接受計量 *名稱* 和 *值* 。 例如：
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      也接受計量 *維度* 和 *時間戳記* 。 例如：
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

每個 JSON 檔案只允許一個時間戳記。 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL) </span>

* **連接字串**：存取 Azure Cosmos DB 的連接字串。 這可以在 Cosmos DB 資源的索引 **鍵** 中找到。 
* **資料庫**：要查詢的資料庫。 這可以在 [**容器**] 區段下的 **[流覽]** 頁面中找到。
* **集合識別碼**：要查詢的集合識別碼。 這可以在 [**容器**] 區段下的 **[流覽]** 頁面中找到。
* **Sql 查詢**：可取得資料並將其組成多維度時間序列資料的 SQL 查詢。 您可以 `@StartTime` `@EndTime` 在查詢中使用和變數。 其格式應為： `yyyy-MM-dd HH:mm:ss` 。

    範例查詢：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    來自2019/12/12 的資料配量的範例查詢：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure 資料總管 (Kusto) </span>

* **連接字串**：計量顧問支援使用 Azure AD 應用程式驗證來存取 Azure 資料總管 (Kusto) 。 您將需要建立並註冊 Azure AD 的應用程式，然後授權它存取 Azure 資料總管資料庫。 若要取得您的連接字串，請參閱 [Azure 資料總管](/azure/data-explorer/provision-azure-ad-app) 檔。

* **查詢**：請參閱 [Kusto 查詢語言](/azure/data-explorer/kusto/query) ，以取得資料並將其制訂成多維度時間序列資料。 您可以 `@StartTime` `@EndTime` 在查詢中使用和變數。 其格式應為： `yyyy-MM-dd HH:mm:ss` 。

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span> \(部分機器翻譯\)

* **帳戶名稱**：您 Azure Data Lake Storage Gen2 的帳戶名稱。 這可以在您的 Azure 儲存體帳戶中找到 (Azure Data Lake Storage Gen2) **存取金鑰** 中的資源。

* **帳戶金鑰**：請指定帳戶名稱以存取您的 Azure Data Lake Storage Gen2。 您可以在 [ **存取金鑰** ] 設定中的 [Azure 儲存體帳戶] (Azure Data Lake Storage Gen2) 資源] 中找到這項功能。

* **檔案系統名稱 (容器)**：計量建議程式會預期將您的時間序列資料儲存為 blob 檔案 (單一容器下每個時間戳記) 一個 blob。 這是 [容器名稱] 欄位。 這可以在您的 Azure 儲存體帳戶中找到 (Azure Data Lake Storage Gen2) 實例，然後按一下 [Blob 服務] 區段中的 [容器]。

* **目錄範本**：這是 Blob 檔案的目錄範本。 例如： */%Y/%m/%d*。 支援下列參數：
  * `%Y` 年份的格式為 `yyyy`
  * `%m` 月份的格式為 `MM`
  * `%d` 日期格式為 `dd`
  * `%h` 為一小時的格式為 `HH`
  * `%M` 是格式化為的分鐘 `mm`

* 檔案 **範本**：這是 Blob 檔案的檔案範本。 例如： *X_% Y-% m-% d-% h-% M.js開啟*。 支援下列參數：
  * `%Y` 年份的格式為 `yyyy`
  * `%m` 月份的格式為 `MM`
  * `%d` 日期格式為 `dd`
  * `%h` 為一小時的格式為 `HH`
  * `%M` 是格式化為的分鐘 `mm`

計量建議程式目前支援 JSON 檔案中的資料架構，如下所示。 例如：

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database |SQL Server</span>

* **連接字串**：計量顧問接受 sql server 資料來源的 [ADO.NET 樣式連接字串](/dotnet/framework/data/adonet/connection-string-syntax) 。

    範例連接字串︰

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **查詢**：可取得資料並將其組成多維度時間序列資料的 SQL 查詢。 您可以 `@StartTime` 在查詢中使用變數，以協助取得預期的計量值。

  * `@StartTime`： datetime 格式為 `yyyy-MM-dd HH:mm:ss`

    範例查詢：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    針對2019/12/12 資料配量執行的實際查詢：
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure 資料表儲存體</span>

* **連接字串**：如需如何從 Azure 資料表儲存體取得連接字串的詳細資訊，請參閱 [View 和 copy 連接字串](../../storage/common/storage-account-keys-manage.md?tabs=azure-portal&toc=%2fazure%2fstorage%2ftables%2ftoc.json#view-account-access-keys) 。

* **資料表名稱**：指定要查詢的資料表。 這可以在您的 Azure 儲存體帳戶實例中找到。 按一下 [**表格服務**] 區段中的 [**資料表]** 。

* **查詢** 您可以 `@StartTime` 在查詢中使用。 `@StartTime` 以 ddTHH： mm： ss 格式字串取代腳本中的。

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **主機**：指定 Elasticsearch 叢集的主要主機。
* **埠**：指定 Elasticsearch 叢集的主要端口。
* **授權標頭**：指定 Elasticsearch 叢集的授權標頭值。
* **查詢**：指定要取得資料的查詢。 @StartTime支援預留位置。 (例如，當 2020-06-21T00：00：00Z 的資料內嵌時， @StartTime = 2020-06-21T00：00： 00) 

## <a name="span-idhttphttp-requestspan"></a><span id="http">HTTP 要求</span>

* **要求 url**：可以傳回 JSON 的 HTTP URL。 支援預留位置% Y，% m，% d，% h，% M：% Y = year （格式為 yyyy，% m = month，格式為 MM，% d = day，格式為 HH，% M = hour，格式為 mm）。 例如：`http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`。
* **要求 HTTP 方法**：使用 GET 或 POST。
* **要求標頭**：可能會新增基本驗證。 
* **要求** 承載：僅支援 JSON 承載。 裝載 @StartTime 中支援預留位置。 回應應採用下列 JSON 格式： [{"timestamp"： "2018-01-01T00：00： 00Z"，"市"： "en-us"，"count"：11，"收益"： 1.23}、{"timestamp"： "2018-01-01T00：00： 00Z"、"市"： "zh-cn"、"count"：22、"收益"： 4.56}]。 (例如，當 2020-06-21T00：00：00Z 的資料是內嵌時， @StartTime = 2020-06-21T00：00： 00.0000000 + 00： 00) 

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL) </span>

* **連接** 字串：用來存取 InfluxDB 的連接字串。
* **資料庫**：要查詢的資料庫。
* **查詢**：取得資料並將其組成多維度時間序列資料以進行內嵌的查詢。
* **使用者名稱**：這是驗證的選擇性選項。 
* **密碼**：這是驗證的選擇性選項。 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **連接字串**：用來存取 MongoDB 的連接字串。
* **資料庫**：要查詢的資料庫。
* **命令**：用以取得資料並將其組成多維度時間序列資料以進行內嵌的命令。

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **連接字串**：用來存取 MySQL DB 的連接字串。
* **查詢**：取得資料並將其組成多維度時間序列資料以進行內嵌的查詢。

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **連接** 字串：用來存取于 postgresql DB 的連接字串。
* **查詢**：取得資料並將其組成多維度時間序列資料以進行內嵌的查詢。

## <a name="next-steps"></a>後續步驟

* 在等候度量資料內嵌到系統時，請閱讀 [如何管理資料](how-tos/manage-data-feeds.md)摘要設定。
* 當您的度量資料內嵌時，您可以設定 [計量，並微調偵測設定的微調](how-tos/configure-metrics.md)。