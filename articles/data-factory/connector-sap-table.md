---
title: 從 SAP 資料表複製資料
description: 瞭解如何使用 Azure Data Factory 管線中的複製活動，將資料從 SAP 資料表複製到支援的接收資料存放區。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/03/2020
ms.openlocfilehash: a6eaa5519607d5d5e9a49851e1c55f9b60b554ea
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529716"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP 資料表複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SAP 資料表複製資料。 如需詳細資訊，請參閱[複製活動概觀](copy-activity-overview.md)。

>[!TIP]
>若要瞭解 ADF 對於 SAP 資料整合案例的整體支援，請參閱[使用 Azure Data Factory 白皮書的 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，以及每個 sap 連接器、comparsion 和指引的詳細介紹。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 SAP 資料表連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 SAP 資料表複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 SAP 資料表連接器支援：

- 從中的 SAP 資料表複製資料：

  - SAP ERP Central Component （SAP ECC）7.01 版或更新版本（在2015之後發行的最新 SAP 支援封裝堆疊中）。
  - SAP Business 倉儲（SAP BW）7.01 版或更新版本（在2015之後發行的最新 SAP 支援封裝堆疊中）。
  - SAP S/4HANA。
  - SAP Business Suite 7.01 版或更新版本中的其他產品（在2015之後發行的最新 SAP 支援封裝堆疊中）。

- 從 SAP 透明資料表、集區資料表、叢集資料表和視圖複製資料。
- 若已設定 SNC，則使用基本驗證或安全網路通訊（SNC）來複製資料。
- 連接到 SAP 應用程式伺服器或 SAP 訊息伺服器。
- 透過預設或自訂 RFC 來抓取資料。

## <a name="prerequisites"></a>先決條件

若要使用此 SAP 資料表連接器，您需要：

- 設定自我裝載整合執行時間（3.17 版或更新版本）。 如需詳細資訊，請參閱[建立及設定自我裝載整合運行](create-self-hosted-integration-runtime.md)時間。

- 從 SAP 的網站下載64位[Sap Connector For Microsoft .net 3.0](https://support.sap.com/en/product/connectors/msnet.html) ，並將它安裝在自我裝載整合執行時間電腦上。 在安裝期間，請務必在 [**選擇性設定步驟**] 視窗中選取 [將**元件安裝到 GAC** ] 選項。

  ![安裝適用于 .NET 的 SAP Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在 Data Factory SAP 資料表連接器中使用的 SAP 使用者必須具備下列許可權：

  - 使用遠端函式呼叫（RFC）目的地的授權。
  - S_SDSAUTH 授權物件的執行活動許可權。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP 資料表連接器特定的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SAP BW 開放式中樞連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設定為 `SapTable`。 | 是 |
| `server` | SAP 實例所在的伺服器名稱。<br/>用來連接到 SAP 應用程式伺服器。 | 否 |
| `systemNumber` | SAP 系統的系統編號。<br/>用來連接到 SAP 應用程式伺服器。<br/>允許的值：以字串表示的二位數十進位數。 | 否 |
| `messageServer` | SAP 訊息伺服器的主機名稱。<br/>使用連接到 SAP 訊息伺服器。 | 否 |
| `messageServerService` | 訊息伺服器的服務名稱或埠號碼。<br/>使用連接到 SAP 訊息伺服器。 | 否 |
| `systemId` | 資料表所在的 SAP 系統識別碼。<br/>使用連接到 SAP 訊息伺服器。 | 否 |
| `logonGroup` | SAP 系統的登入群組。<br/>使用連接到 SAP 訊息伺服器。 | 否 |
| `clientId` | SAP 系統中用戶端的識別碼。<br/>允許的值：以字串表示的三位數十進位數。 | 是 |
| `language` | SAP 系統使用的語言。<br/>預設值為 `EN`。| 否 |
| `userName` | 可存取 SAP 伺服器的使用者名稱。 | 是 |
| `password` | 使用者的密碼。 將此欄位標記 `SecureString` 為類型，以安全地將它儲存在 Data Factory 中，或[參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | 是 |
| `sncMode` | 用來存取資料表所在之 SAP 伺服器的 SNC 啟用指示器。<br/>如果您想要使用 SNC 連接到 SAP 伺服器，請使用。<br/>允許的值為 `0` （關閉、預設值）或 `1` （開啟）。 | 否 |
| `sncMyName` | 要存取資料表所在之 SAP 伺服器的啟動器 SNC 名稱。<br/>當 `sncMode` 為 on 時套用。 | 否 |
| `sncPartnerName` | 通訊夥伴的 SNC 名稱，用以存取資料表所在的 SAP 伺服器。<br/>當 `sncMode` 為 on 時套用。 | 否 |
| `sncLibraryPath` | 外部安全性產品的程式庫，用來存取資料表所在的 SAP 伺服器。<br/>當 `sncMode` 為 on 時套用。 | 否 |
| `sncQop` | 要套用的 SNC 品質保護等級。<br/>當 `sncMode` 為 On 時套用。 <br/>允許的值為 `1` （驗證）、 `2` （完整性）、 `3` （隱私權）、（ `8` `9` 最大值）。 | 否 |
| `connectVia` | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如先前的[必要條件](#prerequisites)中所述，需要自我裝載整合執行時間。 |是 |

**範例1：連接到 SAP 應用程式伺服器**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>範例2：連接到 SAP 訊息伺服器

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>範例3：使用 SNC 進行連接

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。 下列小節提供 SAP 資料表資料集所支援的屬性清單。

若要將資料從和複製到 SAP BW 開放式中樞連結服務，支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設定為 `SapTableResource`。 | 是 |
| `tableName` | 要從中複製資料的 SAP 資料表名稱。 | 是 |

### <a name="example"></a>範例

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 下一節提供 SAP 資料表來源所支援的屬性清單。

### <a name="sap-table-as-source"></a>作為來源的 SAP 資料表

若要從 SAP 資料表複製資料，支援下列屬性：

| 屬性                         | 說明                                                  | 必要 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` 屬性必須設定為 `SapTableSource`。         | 是      |
| `rowCount`                         | 要抓取的資料列數目。                              | 否       |
| `rfcTableFields`                 | 要從 SAP 資料表複製的欄位（資料行）。 例如： `column0, column1` 。 | 否       |
| `rfcTableOptions`                | 用來篩選 SAP 資料表中之資料列的選項。 例如： `COLUMN0 EQ 'SOMEVALUE'` 。 另請參閱本文稍後的 SAP 查詢運算子資料表。 | 否       |
| `customRfcReadTableFunctionModule` | 自訂 RFC 函數模組，可用來從 SAP 資料表讀取資料。<br>您可以使用自訂 RFC 函式模組來定義從 SAP 系統抓取資料並傳回 Data Factory 的方式。 自訂函式模組必須具有類似于的介面（匯入、匯出、資料表） `/SAPDS/RFC_READ_TABLE2` ，這是 Data Factory 所使用的預設介面。<br>Data Factory | 否       |
| `partitionOption`                  | 要從 SAP 資料表讀取的資料分割機制。 支援的選項包括： <ul><li>`None`</li><li>`PartitionOnInt`（左邊有零填補的一般整數或整數值，例如 `0000012345` ）</li><li>`PartitionOnCalendarYear`（4個格式為 "YYYY" 的數位）</li><li>`PartitionOnCalendarMonth`（6位數的格式為 "YYYYMM"）</li><li>`PartitionOnCalendarDate`（格式為 "YYYYMMDD" 的8位數）</li></ul> | 否       |
| `partitionColumnName`              | 用來分割資料的資料行名稱。                | 否       |
| `partitionUpperBound`              | 中所指定之資料行的最大值 `partitionColumnName` ，將用來繼續進行資料分割。 | 否       |
| `partitionLowerBound`              | 中所指定之資料行的最小值 `partitionColumnName` ，將用來繼續進行資料分割。 （注意： `partitionLowerBound` 當分割區選項為時，不可為 "0" `PartitionOnInt` ） | 否       |
| `maxPartitionsNumber`              | 資料分割的最大數目。     | 否       |
| `sapDataColumnDelimiter` | 用來當做分隔符號的單一字元，會傳遞給 SAP RFC 以分割輸出資料。 | 否 |

>[!TIP]
>如果您的 SAP 資料表有大量資料（例如，數億個數據列），請使用 `partitionOption` 和 `partitionSetting` 將資料分割成較小的磁碟分割。 在此情況下，每個分割區都會讀取資料，而且每個資料分割都會透過單一 RFC 呼叫從您的 SAP 伺服器抓取。<br/>
<br/>
>做 `partitionOption` 為 `partitionOnInt` 範例，每個資料分割中的資料列數目會使用下列公式來計算：（和之間的總數據列數 `partitionUpperBound` `partitionLowerBound` ）/ `maxPartitionsNumber` 。<br/>
<br/>
>若要平行載入資料分割以加速複製，平行程度是由 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 複製活動上的設定所控制。 例如，如果您將設定 `parallelCopies` 為四，Data Factory 會同時根據您指定的資料分割選項和設定，產生並執行四個查詢，而且每個查詢都會從您的 SAP 資料表中抓取部分資料。 我們強烈建議您建立 `maxPartitionsNumber` 屬性值的倍數 `parallelCopies` 。 將資料複製到以檔案為基礎的資料存放區時，也會建議寫入資料夾做為多個檔案（僅指定資料夾名稱），在此情況下，效能會比寫入單一檔案更好。

在中 `rfcTableOptions` ，您可以使用下列常用的 SAP 查詢運算子來篩選資料列：

| 運算子 | 說明 |
| :------- | :------- |
| `EQ` | 等於 |
| `NE` | 不等於 |
| `LT` | 小於 |
| `LE` | 小於或等於 |
| `GT` | 大於 |
| `GE` | 大於或等於 |
| `IN` | 如同中的`TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | 如同中的`LIKE 'Emma%'` |

### <a name="example"></a>範例

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 資料表的資料類型對應

當您從 SAP 資料表複製資料時，會使用下列從 SAP 資料表資料類型對應到 Azure Data Factory 過渡期資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| `C`字串 | `String` |
| `I`介於 | `Int32` |
| `F`Float | `Double` |
| `D`日期 | `String` |
| `T`階段 | `String` |
| `P`（BCD 已封裝，貨幣，十進位，數量） | `Decimal` |
| `N`數值 | `String` |
| `X`（二進位和原始） | `String` |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
