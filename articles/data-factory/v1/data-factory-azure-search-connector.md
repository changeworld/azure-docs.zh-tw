---
title: 使用 Data Factory 將資料推送至搜尋索引
description: 深入瞭解如何使用 Azure Data Factory 將資料推送至 Azure 認知搜尋索引。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f8d46e1e-5c37-4408-80fb-c54be532a4ab
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f6521efe024ba0ea29ae427aeaf06ca0e5fa8dd7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194925"
---
# <a name="push-data-to-an-azure-cognitive-search-index-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料推送至 Azure 認知搜尋索引
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](data-factory-azure-search-connector.md)
> * [第 2 版 (目前的版本)](../connector-azure-search.md)

> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱第[2 版中的 Azure 認知搜尋連接器](../connector-azure-search.md)。

本文說明如何使用複製活動，將資料從支援的來源資料存放區推送至 Azure 認知搜尋索引。 支援的來源資料存放區會列於[支援的來源與接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表格的 [來源] 欄中。 本文是以[資料移動活動](data-factory-data-movement-activities.md)一文為基礎，其中呈現使用複製活動移動資料的一般總覽，以及支援的資料存放區組合。

## <a name="enabling-connectivity"></a>啟用連線
若要讓 Data Factory 服務連接到內部部署資料存放區，您要在內部部署環境中安裝資料管理閘道。 您可以在裝載來源資料存放區的同一部電腦上或個別電腦上安裝閘道，以避免與資料存放區競用資源。

資料管理閘道會透過安全且可管理的方式，將內部部署資料來源連接到雲端服務。 如需資料管理閘道的詳細資訊，請參閱 [在內部部署和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文。

## <a name="getting-started"></a>開始使用
您可以建立具有複製活動的管線，使用不同的工具/Api 將資料從來源資料存放區推送至搜尋索引。

建立管線的最簡單方式是使用**複製嚮導**。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。

您也可以使用下列工具來建立管線： [ **Visual Studio**]、[ **Azure PowerShell**]、[ **Azure Resource Manager 範本**]、[ **.net API**] 和 [ **REST API**]。 如需建立包含複製活動之管線的逐步指示，請參閱[複製活動教學](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)課程。

不論您是使用工具還是 API，都需執行下列步驟來建立將資料從來源資料存放區移到接收資料存放區的管線：

1. 建立**連結服務**，將輸入和輸出資料存放區連結到資料處理站。
2. 建立**資料集**來代表複製作業的輸入和輸出資料。
3. 建立具有複製活動的**管線**，以將資料集作為輸入，並使用資料集做為輸出。

使用精靈時，精靈會自動為您建立這些 Data Factory 實體 (已連結的服務、資料集及管線) 的 JSON 定義。 使用工具/API (.NET API 除外) 時，您需使用 JSON 格式來定義這些 Data Factory 實體。  如需相關範例，其中含有用來將資料複製到搜尋索引之 Data Factory 實體的 JSON 定義，請參閱本文的[json 範例：將資料從 SQL Server 複製到 Azure 認知搜尋索引](#json-example-copy-data-from-sql-server-to-azure-cognitive-search-index)一節。

下列各節提供 JSON 屬性的相關詳細資料，這些屬性是用來定義搜尋索引特定的 Data Factory 實體：

## <a name="linked-service-properties"></a>連結服務屬性

下表提供 Azure 認知搜尋連結服務特有的 JSON 元素說明。

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| type | Type 屬性必須設定為： **AzureSearch**。 | 是 |
| url | 搜尋服務的 URL。 | Yes |
| 索引鍵 | 搜尋服務的管理金鑰。 | Yes |

## <a name="dataset-properties"></a>資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱 [建立資料集](data-factory-create-datasets.md) 一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型。 不同類型資料集的 **typeProperties** 區段不同。 **AzureSearchIndex** 類型資料集的 typeProperties 區段有下列屬性：

| 屬性 | 說明 | 必要 |
| -------- | ----------- | -------- |
| type | type 屬性必須設為 **AzureSearchIndex**。| Yes |
| IndexName | 搜尋索引的名稱。 Data Factory 不會建立索引。 索引必須存在於 Azure 認知搜尋中。 | Yes |


## <a name="copy-activity-properties"></a>複製活動屬性
如需定義活動的區段和屬性完整清單，請參閱 [建立管線](data-factory-create-pipelines.md) 一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及各種原則) 適用於所有類型的活動。 而 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

對於「複製活動」，當接收的類型為 **AzureSearchIndexSink** 時，typeProperties 區段中會有下列可用屬性：

| 屬性 | 描述 | 允許的值 | 必要 |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | 指定若文件已經存在於索引中，是否要合併或取代。 請參閱 [WriteBehavior 屬性](#writebehavior-property)。| 合併 (預設值)<br/>上傳| No |
| WriteBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料上傳至搜尋索引。 如需詳細資訊，請參閱 [WriteBatchSize 屬性](#writebatchsize-property)。 | 1 到 1000。 預設值為 1000。 | No |

### <a name="writebehavior-property"></a>WriteBehavior 屬性
AzureSearchSink 會在寫入資料時更新插入。 換句話說，寫入檔時，如果檔索引鍵已存在於搜尋索引中，Azure 認知搜尋就會更新現有的檔，而不會擲回衝突例外狀況。

AzureSearchSink (藉由使用 AzureSearch SDK) 提供下列兩種更新插入行為：

- **合併**︰將新文件中的所有資料行與現有的文件相結合。 對於新文件中含有 null 值的資料行，則會保留現有文件中的值。
- **上傳**：新的文件會取代現有的文件。 針對新文件中未指定的資料行，不論現有的文件中是否具有非 null 的值，都會將值設為 null。

預設行為是**合併**。

### <a name="writebatchsize-property"></a>WriteBatchSize 屬性
Azure 認知搜尋服務支援以批次方式撰寫檔。 一個批次可包含 1 到 1,000 個動作。 一個動作可指示一份文件來執行上傳/合併作業。

### <a name="data-type-support"></a>資料類型支援
下表指定是否支援 Azure 認知搜尋資料類型。

| Azure 認知搜尋資料類型 | Azure 認知搜尋接收中支援 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| Boolean | Y |
| DataTimeOffset | Y |
| 字串陣列 | N |
| GeographyPoint | N |

## <a name="json-example-copy-data-from-sql-server-to-azure-cognitive-search-index"></a>JSON 範例：將資料從 SQL Server 複製到 Azure 認知搜尋索引

下列範例顯示︰

1. [AzureSearch](#linked-service-properties) 類型的連結服務。
2. [OnPremisesSqlServer](data-factory-sqlserver-connector.md#linked-service-properties)類型的連結服務。
3. [SqlServerTable](data-factory-sqlserver-connector.md#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureSearchIndex](#dataset-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
4. 具有使用 [SqlSource](data-factory-sqlserver-connector.md#copy-activity-properties) 和 [AzureSearchIndexSink](#copy-activity-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將時間序列資料從 SQL Server 資料庫複製到搜尋索引。 範例後面的各節將會說明此範例中使用的 JSON 屬性。

第一步是在內部部署電腦上設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

**Azure 認知搜尋連結服務：**

```JSON
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

**SQL Server 連結服務**

```JSON
{
  "Name": "SqlServerLinkedService",
  "properties": {
    "type": "OnPremisesSqlServer",
    "typeProperties": {
      "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
      "gatewayName": "<gatewayname>"
    }
  }
}
```

**SQL Server 輸入資料集**

此範例假設您已在 SQL Server 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestampcolumn")。 您可以使用單一資料集來查詢相同資料庫內的多個資料表，但針對資料集的 tableName typeProperty 必須使用單一資料表。

設定 “external”: ”true” 可讓 Data Factory 服務知道資料集是在 Data Factory 外部，而不是由 Data Factory 中的活動所產生。

```JSON
{
  "name": "SqlServerDataset",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlServerLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure 認知搜尋輸出資料集：**

此範例會將資料複製到名為**products**的 Azure 認知搜尋索引。 Data Factory 不會建立索引。 若要測試範例，請以此名稱建立索引。 使用與輸入資料集相同的資料行數目來建立搜尋索引。 每小時會將新的專案新增至搜尋索引。

```JSON
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties" : {
            "indexName": "products",
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

**具有 SQL 來源和 Azure 認知搜尋索引接收器之管線中的複製活動：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，將 **source** 類型設為 **SqlSource**，並且將 **sink** 類型設為 **AzureSearchIndexSink**。 針對 **SqlReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "SqlServertoAzureSearchIndex",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": " SqlServerInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSearchIndexDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "AzureSearchIndexSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

如果您要將資料從雲端資料存放區複製到「Azure 認知搜尋」， `executionLocation` 則需要屬性。 下列 JSON 程式碼片段顯示必須在「複製活動」的 `typeProperties` 下進行的變更。 參閱[在雲端資料存放區之間複製資料](data-factory-data-movement-activities.md#global)一節以取得支援的值和更多詳細資料。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```


## <a name="copy-from-a-cloud-source"></a>從雲端來源複製
如果您要將資料從雲端資料存放區複製到「Azure 認知搜尋」， `executionLocation` 則需要屬性。 下列 JSON 程式碼片段顯示必須在「複製活動」的 `typeProperties` 下進行的變更。 參閱[在雲端資料存放區之間複製資料](data-factory-data-movement-activities.md#global)一節以取得支援的值和更多詳細資料。

```JSON
"typeProperties": {
  "source": {
    "type": "BlobSource"
  },
  "sink": {
    "type": "AzureSearchIndexSink"
  },
  "executionLocation": "West US"
}
```

您也可以在複製活動定義中，將來自來源資料集的資料行與來自接收資料集的資料行對應。 如需詳細資料，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>效能和微調
若要了解影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法，請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)。

## <a name="next-steps"></a>後續步驟
查看下列文章：

* [複製活動教學課程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ，以取得使用「複製活動」來建立管線的逐步指示。
