---
title: 使用 Azure Data Factory 從 Teradata 移動資料
description: 了解 Data Factory 服務的 Teradata 連接器，其可讓您從 Teradata 資料庫移動資料
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 98eb76d8-5f3d-4667-b76e-e59ed3eea3ae
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ecde5784e759ef5259b8c67ed574cef6cae98f30
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84707305"
---
# <a name="move-data-from-teradata-using-azure-data-factory"></a>使用 Azure Data Factory 從 Teradata 移動資料
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](data-factory-onprem-teradata-connector.md)
> * [第 2 版 (目前的版本)](../connector-teradata.md)

> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱[第 2 版中的 Teradata 連接器](../connector-teradata.md)。

本文說明如何使用 Azure Data Factory 中的「複製活動」，從內部部署的 Teradata 資料庫移動資料。 它是以[資料移動活動](data-factory-data-movement-activities.md)一文為基礎，其中呈現使用複製活動移動資料的一般總覽。

您可以將資料從內部部署的 Teradata 資料存放區複製到任何支援的接收資料存放區。 如需複製活動所支援作為接收器的資料存放區清單，請參閱[支援的資料存放區](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表格。 Data Factory 目前只支援將資料從 Teradata 資料存放區移到其他資料存放區，而不支援將資料從其他資料存放區移到 Teradata 資料存放區。

## <a name="prerequisites"></a>必要條件
資料處理站支援透過資料管理閘道器連接至內部部署 Teradata 來源。 請參閱 [在內部部署位置與雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 一文來了解資料管理閘道和設定閘道的逐步指示。

即使 Teradata 裝載於 Azure IaaS VM 中，也必須要有閘道。 您可以將閘道安裝在與資料存放區相同或相異的 IaaS VM 上，只要閘道可以連線到資料庫即可。

> [!NOTE]
> 如需連接/閘道器相關問題的疑難排解秘訣，請參閱 [針對閘道問題進行疑難排解](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 。

## <a name="supported-versions-and-installation"></a>支援的版本和安裝
若要讓資料管理閘道連接至 Teradata 資料庫，您必須在與資料管理閘道相同的系統上，安裝 [Teradata 的 .NET 資料提供者](https://go.microsoft.com/fwlink/?LinkId=278886)版本 14 或以上版本。 支援 Teradata 版本 12 和以上版本。

## <a name="getting-started"></a>開始使用
您可以藉由使用不同的工具/API，建立內含複製活動的管線，以從內部部署的 Cassandra 資料存放區移動資料。

- 建立管線的最簡單方式是使用**複製嚮導**。 如需使用複製資料精靈建立管線的快速逐步解說，請參閱 [教學課程︰使用複製精靈建立管線](data-factory-copy-data-wizard-tutorial.md) 。
- 您也可以使用下列工具來建立管線： [ **Visual Studio**]、[ **Azure PowerShell**]、[ **Azure Resource Manager 範本**]、[ **.net API**] 和 [ **REST API**]。 如需建立包含複製活動之管線的逐步指示，請參閱[複製活動教學](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)課程。

不論您是使用工具還是 API，都需執行下列步驟來建立將資料從來源資料存放區移到接收資料存放區的管線：

1. 建立**連結服務**，將輸入和輸出資料存放區連結到資料處理站。
2. 建立**資料集**來代表複製作業的輸入和輸出資料。
3. 建立具有複製活動的**管線**，以將資料集作為輸入，並使用資料集做為輸出。

使用精靈時，精靈會自動為您建立這些 Data Factory 實體 (已連結的服務、資料集及管線) 的 JSON 定義。 使用工具/API (.NET API 除外) 時，您需使用 JSON 格式來定義這些 Data Factory 實體。  如需相關範例，其中含有用來從內部部署 Teradata 資料存放區複製資料之 Data Factory 實體的 JSON 定義，請參閱本文的 [JSON 範例：將資料從 Teradata 複製到 Azure Blob](#json-example-copy-data-from-teradata-to-azure-blob) 一節。

下列各節提供 JSON 屬性的相關詳細資料，這些屬性是用來定義 Teradata 資料存放區特定的 Data Factory 實體：

## <a name="linked-service-properties"></a>連結服務屬性
下表提供 Teradata 連結服務專屬 JSON 元素的描述。

| 屬性 | 說明 | 必要 |
| --- | --- | --- |
| type |類型屬性必須設為： **OnPremisesTeradata** |Yes |
| 伺服器 |Teradata 伺服器的名稱。 |是 |
| authenticationType |用來連接到 Teradata 資料庫的驗證類型。 可能的值為：匿名、基本和 Windows。 |Yes |
| username |如果您使用基本或 Windows 驗證，請指定使用者名稱。 |否 |
| 密碼 |指定您為使用者名稱所指定之使用者帳戶的密碼。 |No |
| gatewayName |Data Factory 服務應該用來連接到內部部署 Teradata 資料庫的閘道器名稱。 |Yes |

## <a name="dataset-properties"></a>資料集屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的**typeProperties**區段都不同，並提供資料存放區中資料位置的相關資訊。 目前沒有支援 Teradata 資料集的類型屬性。

## <a name="copy-activity-properties"></a>複製活動屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

而活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

當來源的類型為 **RelationalSource** (包括 Teradata) 時，**typeProperties** 區段中會有下列可用屬性：

| 屬性 | 描述 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| 查詢 |使用自訂查詢來讀取資料。 |SQL 查詢字串。 例如：select * from MyTable。 |Yes |

### <a name="json-example-copy-data-from-teradata-to-azure-blob"></a>JSON 範例：將資料從 Teradata 複製到 Azure Blob
下列範例提供可用來使用[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)或[Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)建立管線的範例 JSON 定義。 這些範例示範如何將資料從 Teradata 複製到 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動，將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。

此範例具有下列 Data Factory 實體：

1. [OnPremisesTeradata](#linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
3. [RelationalTable](#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [RelationalSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每個小時將資料從 Teradata 資料庫中的查詢結果複製到 Blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

第一步是設定資料管理閘道。 如需相關指示，請參閱 [在內部部署位置和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 。

**Teradata 連結服務：**

```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

**Azure Blob 儲存體連結服務：**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorageLinkedService",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
        }
    }
}
```

**Teradata 輸入資料集：**

此範例假設您已在 Teradata 中建立資料表 "MyTable"，其中包含時間序列資料的資料行 (名稱為 "timestamp")。

設定 “external”: true 可讓 Data Factory 服務知道資料表是在 Data Factory 外部，而不是由 Data Factory 中的活動所產生。

```json
{
    "name": "TeradataDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```json
{
    "name": "AzureBlobTeradataDataSet",
    "properties": {
        "published": false,
        "location": {
            "type": "AzureBlobLocation",
            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
            ],
            "linkedServiceName": "AzureStorageLinkedService"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
**具有複製活動的管線：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，已將 **source** 類型設為 **RelationalSource**，並將 **sink** 類型設為 **BlobSink**。 針對 **query** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "TeradataDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobTeradataDataSet"
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
                "name": "TeradataToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z",
        "isPaused": false
    }
}
```
## <a name="type-mapping-for-teradata"></a>Teradata 的類型對應
如[資料移動活動](data-factory-data-movement-activities.md)一文所述，複製活動會使用下列2個步驟的方法，執行從來源類型到接收類型的自動類型轉換：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

將資料移到 Teradata 時，下列對應會用於從 Teradata 類型到 .NET 類型。

| Teradata 資料庫類型 | .NET Framework 類型 |
| --- | --- |
| Char |String |
| Clob |String |
| Graphic |String |
| VarChar |String |
| VarGraphic |String |
| Blob |Byte[] |
| Byte |Byte[] |
| VarByte |Byte[] |
| BigInt |Int64 |
| ByteInt |Int16 |
| Decimal |Decimal |
| Double |Double |
| 整數 |Int32 |
| Number |Double |
| SmallInt |Int16 |
| 日期 |Datetime |
| Time |TimeSpan |
| 時區的時間 |String |
| 時間戳記 |Datetime |
| 時區的時間戳記 |DateTimeOffset |
| Interval Day |TimeSpan |
| 間隔日至小時 |TimeSpan |
| 間隔日至分鐘 |TimeSpan |
| 間隔日至秒鐘 |TimeSpan |
| Interval Hour |TimeSpan |
| 間隔小時至分鐘 |TimeSpan |
| 間隔小時至秒鐘 |TimeSpan |
| Interval Minute |TimeSpan |
| 間隔分鐘至秒鐘 |TimeSpan |
| Interval Second |TimeSpan |
| Interval Year |String |
| 間隔年至月 |String |
| Interval Month |String |
| Period(Date) |String |
| Period(Time) |String |
| Period(Time With Time Zone) |String |
| Period(Timestamp) |String |
| Period(Timestamp With Time Zone) |String |
| Xml |String |

## <a name="map-source-to-sink-columns"></a>將來源對應到接收資料行
若要了解如何將來源資料集內的資料行與接收資料集內的資料行對應，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="repeatable-read-from-relational-sources"></a>從關聯式來源進行可重複的讀取
從關聯式資料存放區複製資料時，請將可重複性謹記在心，以避免產生非預期的結果。 在 Azure Data Factory 中，您可以手動重新執行配量。 您也可以為資料集設定重試原則，使得在發生失敗時，重新執行配量。 以上述任一方式重新執行配量時，您必須確保不論將配量執行多少次，都會讀取相同的資料。 請參閱[從關聯式來源重複讀取](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)。

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。
