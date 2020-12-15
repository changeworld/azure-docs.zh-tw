---
title: 使用 Azure Data Factory 從 SAP Hana 移動資料
description: 了解如何使用 Azure Data Factory 從 SAP Hana 移動資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c08aeca8e7aaa71e5a2a80cbdece762b989e28c2
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510136"
---
# <a name="move-data-from-sap-hana-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP Hana 移動資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](data-factory-sap-hana-connector.md)
> * [第 2 版 (目前的版本)](../connector-sap-hana.md)

> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱[第 2 版中的 SAP HANA 連接器](../connector-sap-business-warehouse.md)。

本文說明如何使用 Azure Data Factory 中的「複製活動」，從內部部署的 SAP HANA 移動資料。 它是以「 [資料移動活動](data-factory-data-movement-activities.md) 」一文為基礎，提供使用複製活動來移動資料的一般總覽。

您可以將資料從內部部署的 SAP HANA 資料存放區複製到任何支援的接收資料存放區。 如需複製活動所支援作為接收器的資料存放區清單，請參閱 [支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 資料表。 Data Factory 目前只支援將資料從 SAP HANA 移到其他資料存放區，而不支援將資料從其他資料存放區移到 SAP HANA。

## <a name="supported-versions-and-installation"></a>支援的版本和安裝
此連接器支援任何版本的 SAP HANA 資料庫。 它支援從 HANA 資訊模型 (例如分析和計算檢視) 及使用 SQL 查詢資料列/資料行資料表複製資料。

若要啟用 SAP HANA 執行個體的連線，請安裝下列元件︰
- **資料管理閘道器**：資料處理站服務支援使用稱為資料管理閘道器的元件連接到內部部署資料存放區 (包括 SAP HANA)。 若要了解資料管理閘道和設定閘道的逐步指示，請參閱[在內部部署資料存放區和雲端資料存放區之間移動資料](data-factory-move-data-between-onprem-and-cloud.md)一文。 即使 SAP Hana 裝載於 Azure IaaS 虛擬機器 (VM) 中，也必須要有閘道。 您可以將閘道安裝在與資料存放區相同或相異的 VM 上，只要閘道可以連線到資料庫即可。
- 閘道器電腦上的 **SAP HANA ODBC 驅動程式**。 您可以從 [SAP 軟體下載中心](https://support.sap.com/swdc)下載 SAP Hana ODBC 驅動程式。 使用關鍵字 **SAP HANA CLIENT for Windows** 搜尋。 

## <a name="getting-started"></a>開始使用
您可以藉由使用不同的工具/API，建立內含複製活動的管線，以從內部部署的 SAP HANA 資料存放區移動資料。 

- 若要建立管線，最簡單的方式就是使用「 **複製嚮導**」。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。 
- 您也可以使用下列工具來建立管線： **Visual Studio**、 **Azure PowerShell**、 **Azure Resource Manager 範本**、 **.net API** 和 **REST API**。 請參閱「 [複製活動」教學](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 課程，以取得使用複製活動建立管線的逐步指示。 

不論您是使用工具還是 API，都需執行下列步驟來建立將資料從來源資料存放區移到接收資料存放區的管線：

1. 建立 **連結服務**，將輸入和輸出資料存放區連結到資料處理站。
2. 建立 **資料集** 以代表複製作業的輸入和輸出資料。 
3. 建立具有複製活動的 **管線** ，該活動會採用資料集做為輸入，並使用資料集做為輸出。 

使用精靈時，精靈會自動為您建立這些 Data Factory 實體 (已連結的服務、資料集及管線) 的 JSON 定義。 使用工具/API (.NET API 除外) 時，您需使用 JSON 格式來定義這些 Data Factory 實體。  如需相關範例，其中含有用來從內部部署 SAP HANA 複製資料之 Data Factory 實體的 JSON 定義，請參閱本文的 [JSON 範例：將資料從 SAP HANA 複製到 Azure Blob](#json-example-copy-data-from-sap-hana-to-azure-blob) 一節。 

下列各節提供 JSON 屬性的相關詳細資料，這些屬性是用來定義 SAP HANA 資料存放區特定的 Data Factory 實體：

## <a name="linked-service-properties"></a>連結服務屬性
下表提供 SAP Hana 連結服務專屬 JSON 元素的描述。

屬性 | 描述 | 允許的值 | 必要
-------- | ----------- | -------------- | --------
伺服器 | SAP Hana 執行個體所在之伺服器的名稱。 如果您的伺服器使用自訂連接埠，指定 `server:port`。 | 字串 | 是
authenticationType | 驗證類型。 | 字串。 "Basic" 或 "Windows" | 是 
username | 具有 SAP 伺服器存取權之使用者的名稱 | 字串 | 是
密碼 | 使用者的密碼。 | 字串 | 是
gatewayName | Data Factory 服務應該用來連接到內部部署 SAP Hana 執行個體的閘道器名稱。 | 字串 | 是
encryptedCredential | 加密的認證字串。 | 字串 | No

## <a name="dataset-properties"></a>資料集屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 **>typeproperties** 區段都不同，並提供資料存放區中資料位置的相關資訊。 沒有類型特定的屬性支援 **RelationalTable** 的 SAP HANA 資料集類型。 


## <a name="copy-activity-properties"></a>複製活動屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

而活動的 **typeProperties** 區段中，可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

當複製活動中的來源類型為 **RelationalSource** (包括 SAP Hana) 時，typeProperties 區段中可使用下列屬性：

| 屬性 | 描述 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| 查詢 | 指定 SQL 查詢從 SAP HANA 執行個體讀取資料。 | SQL 查詢。 | 是 |

## <a name="json-example-copy-data-from-sap-hana-to-azure-blob"></a>JSON 範例：將資料從 SAP HANA 複製到 Azure Blob
下列範例提供您可以使用 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)來建立管線的範例 JSON 定義。 此範例示範如何將資料從內部部署 SAP Hana 複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動， **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所列的任何接收器。  

> [!IMPORTANT]
> 此範例提供 JSON 程式碼片段。 其中並不包含建立 Data Factory 的逐步指示。 如需逐步指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文。

此範例具有下列 Data Factory 實體：

1. [SapHana](#linked-service-properties) 類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
3. [RelationalTable](#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有複製活動的管線，該 [管線](data-factory-create-pipelines.md) 會使用 [>relationalsource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

範例會每隔一小時就把 SAP Hana 執行個體的資料複製到 Azure Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

第一步是設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

### <a name="sap-hana-linked-service"></a>SAP HANA 連結服務
此連結服務會將您的 SAP HANA 執行個體連結至資料處理站。 Type 屬性設為 **SapHana**。 TypeProperties 區段提供 SAP HANA 執行個體的連接資訊。

```json
{
    "name": "SapHanaLinkedService",
    "properties":
    {
        "type": "SapHana",
        "typeProperties":
        {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```

### <a name="azure-storage-linked-service"></a>Azure 儲存體連結服務
此連結服務會將您的 Azure 儲存體帳戶連結至資料處理站。 Type 屬性設為 **AzureStorage**。 typeProperties 區段提供 Azure 儲存體帳戶的連線資訊。

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="sap-hana-input-dataset"></a>SAP HANA 輸入資料集

此資料集會定義 SAP HANA 資料集。 將資料處理站資料集的類型設定為 **RelationalTable**。 目前，您不指定任何特定類型的 SAP HANA 資料集屬性。 複製活動定義中的查詢會指定要從 SAP Hana 執行個體讀取的資料。 

將 external 屬性設定為 true 會通知 Data Factory 服務，指出資料表是在資料處理站外部，不是由資料處理站中的活動所產生。

頻率和間隔屬性會定義排程。 在此情況下，每小時會從 SAP Hana 執行個體讀取資料。 

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

### <a name="azure-blob-output-dataset"></a>Azure Blob 輸出資料集
此資料集會定義輸出 Azure Blob 資料集。 Type 屬性設為 AzureBlob。 TypeProperties 區段提供從 SAP HANA 執行個體所儲存之複製資料的位置。 資料會每小時寫入新的 blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/saphana/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="pipeline-with-copy-activity"></a>具有複製活動的管線

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，已將 **source** 類型設為 **RelationalSource** (適用於 SAP HANA 來源)，並將 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<SQL Query for HANA>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapHanaDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapHanaToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```


### <a name="type-mapping-for-sap-hana"></a>SAP Hana 的類型對應
如同 [資料移動活動](data-factory-data-movement-activities.md) 文章中所述，複製活動會使用下列兩個步驟的方法，執行從來源類型轉換成接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 SAP Hana 移動資料時，下列對應會用於從 SAP Hana 類型對應至 .NET 類型。

SAP Hana 類型 | 以 .Net 為基礎的類型
------------- | ---------------
TINYINT | Byte
SMALLINT | Int16
INT | Int32
bigint | Int64
REAL | Single
DOUBLE | Single
DECIMAL | Decimal
BOOLEAN | Byte
VARCHAR | String
NVARCHAR | String
CLOB | Byte[]
ALPHANUM | String
BLOB | Byte[]
日期 | Datetime
TIME | TimeSpan
timestamp | Datetime
SECONDDATE | Datetime

## <a name="known-limitations"></a>已知限制
從 SAP HANA 複製資料時，有幾個已知的限制︰

- NVARCHAR 字串會截斷成 4000 個 Unicode 字元的最大長度
- 不支援 SMALLDECIMAL
- 不支援 VARBINARY
- 有效日期為 1899/12/30 到 9999/12/31 之間

## <a name="map-source-to-sink-columns"></a>將來源對應到接收資料行
若要了解如何將來源資料集內的資料行與接收資料集內的資料行對應，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>從關聯式來源進行可重複的讀取
從關聯式資料存放區複製資料時，請將可重複性謹記在心，以避免產生非預期的結果。 在 Azure Data Factory 中，您可以手動重新執行配量。 您也可以為資料集設定重試原則，使得在發生失敗時，重新執行配量。 以上述任一方式重新執行配量時，您必須確保不論將配量執行多少次，都會讀取相同的資料。 請參閱[從關聯式來源進行可重複的讀取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。
