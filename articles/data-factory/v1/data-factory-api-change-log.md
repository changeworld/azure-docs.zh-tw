---
title: Data Factory-.NET API 變更記錄檔
description: 描述 Azure Data Factory 的特定 .NET API 版本中的重大變更、新增功能、bug 修正等等。
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 70df35409d1c84efb996bb40f4e39bde6ad7d5a8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496495"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory - .NET API 變更記錄
> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 

本文章提供 Azure Data Factory SDK 在特定版本中有何變更的相關資訊。 您可以在 [這裡](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>版本 4.11.0
新增功能︰

* 已加入下列連結服務類型：
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* 已加入下列資料集類型：
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* 已加入下列複製來源類型：
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>版本 4.10.0
* 下列選擇性屬性已新增至 TextFormat：
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [FirstRowAsHeader](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* 已加入下列連結服務類型：
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* 已加入下列資料集類型：
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* 已加入下列複製來源類型：
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* 新增 [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) 屬性至 AzureMLBatchExecutionActivity
  * 可將多個 Web 服務輸入傳遞到 Azure Machine Learning 實驗

## <a name="version-491"></a>版本 4.9.1
### <a name="bug-fix"></a>錯誤 (bug) 修正
* 取代 [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)的 WebApi 型驗證。

## <a name="version-490"></a>版本 4.9.0
### <a name="feature-additions"></a>新增功能
* 新增 CopyActivity 的 [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) 和 [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) 屬性。 如需功能的詳細資訊，請參閱 [分段複製](data-factory-copy-activity-performance.md#staged-copy) 。

### <a name="bug-fix"></a>錯誤 (bug) 修正
* 導入 [ActivityWindowOperationExtensions.List](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) 方法的多載，它會採用 [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) 執行個體。
* 將 [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) 和 [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) 標示為 CopySink 中的選擇性項目。

## <a name="version-480"></a>版本 4.8.0
### <a name="feature-additions"></a>新增功能
* 複製活動類型中已新增下列選擇性屬性，以啟用複製效能的微調︰
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>4.7.0 版
### <a name="feature-additions"></a>新增功能
* 加入了新的 StorageFormat 類型 [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) 類型，可用來複製最佳化資料列單欄式 (ORC) 格式的檔案。
* 為 SqlDWSink 新增 [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) 和 PolyBaseSettings 屬性。
  * 可讓您使用 PolyBase 將資料複製到 Azure Synapse Analytics。

## <a name="version-461"></a>4.6.1 版
### <a name="bug-fixes"></a>Bug 修正
* 修正用於列出活動時段的 HTTP 要求。
  * 從要求承載移除資源群組名稱和 Data Factory 名稱。

## <a name="version-460"></a>4.6.0 版
### <a name="feature-additions"></a>新增功能
* 下列屬性已新增至 [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)：
  * [PipelineMode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [資料集](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* 下列屬性已新增至 [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)：
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* 加入了新的 [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) 類型 [JsonFormat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) 類型，可用來定義資料採用 JSON 格式的資料集。

## <a name="version-450"></a>4.5.0 版
### <a name="feature-additions"></a>新增功能
* 加入了 [活動時段的清單作業](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions)。
  * 已根據實體類型 (即資料處理站、資料集、管線和活動)，加入了擷取有篩選器活動視窗的方法。
* 已加入下列連結服務類型：
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice)、[WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* 已加入下列資料集類型：
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset)、[WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* 已加入下列複製來源類型：     
  * [WebSource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>4.4.0 版
### <a name="feature-additions"></a>新增功能
* 已新增下列連結服務類型來做為複製活動的資料來源和接收器：
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice)。 如需概念性資訊和範例，請參閱 [Azure 儲存體 SAS 連結服務](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 。

## <a name="version-430"></a>4.3.0 版
### <a name="feature-additions"></a>新增功能
* 已新增下列連結服務類型來做為複製活動的資料來源：
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice)。 如需概念性資訊和範例，請參閱 [使用 Data Factory 從 HDFS 移動資料](data-factory-hdfs-connector.md) 。
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice)。 如需概念性資訊和範例，請參閱 [使用 Azure Data Factory 從 ODBC 資料存放區移動資料](data-factory-odbc-connector.md) 。

## <a name="version-420"></a>4.2.0 版
### <a name="feature-additions"></a>新增功能
* 新增下列新的活動類型： [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity)。 如需有關活動的詳細資訊，請參閱[使用更新資源活動更新 Azure ML 模型](data-factory-azure-ml-batch-execution-activity.md)。
* 新的選擇性屬性 [updateResourceEndpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) 已加入至 [AzureMLLinkedService 類別](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)。
* [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 和 [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 屬性已加入至 [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) 類別。
* 允許設定用戶端呼叫 Data Factory 服務的逾時值。

## <a name="version-410"></a>4.1.0 版
### <a name="feature-additions"></a>新增功能
* 已加入下列連結服務類型：
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* 已加入下列活動類型：
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* 已加入下列資料集類型：
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* 已加入複製活動的下列來源和接收器類型：
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>4.0.1 版
### <a name="breaking-changes"></a>重大變更
下列類別已重新命名。 新的名稱是 4.0.0 版之前的原始類別名稱。

| 4.0.0 中的名稱 | 4.0.1 中的名稱 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>4.0.0 版
### <a name="breaking-changes"></a>重大變更
* 下列類別/介面已重新命名。

| 舊名稱 | 新名稱 |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Table |[資料集](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* **List** 方法現在會傳回分頁式結果。 如果回應包含非空白的 **NextLink** 屬性，用戶端應用程式需要繼續提取下一個頁面，直到傳回了所有頁面為止。  範例如下：

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** 管線 API 只會傳回管線的摘要，而不是完整的詳細資料。 例如，管線摘要中的活動只包含名稱和類型。

### <a name="feature-additions"></a>新增功能
* [>sqldwsink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink)類別支援兩個新的屬性（ **SliceIdentifierColumnName** 和 **>sqlwritercleanupscript**），以支援對 Azure Azure Synapse Analytics 的等冪複製。 如需這些屬性的詳細資訊，請參閱 [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) 文章。
* 我們現在支援針對 Azure SQL Database 和 Azure Synapse Analytics 來源執行預存程式，做為複製活動的一部分。 [SqlSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) 和 [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) 類別具有下列屬性：**SqlReaderStoredProcedureName** 和 **StoredProcedureParameters**。 如需這些屬性的詳細資訊，請參閱 Azure.com 上的 [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) 和 [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) 文章。