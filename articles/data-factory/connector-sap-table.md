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
ms.date: 09/01/2020
ms.openlocfilehash: 4505deaa4cc11c00c7283ef686827d6893c2742a
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93280418"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 SAP 資料表複製資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 SAP 資料表複製資料。 如需詳細資訊，請參閱[複製活動概觀](copy-activity-overview.md)。

>[!TIP]
>若要瞭解 ADF 對 SAP 資料整合案例的整體支援，請參閱 [使用 Azure Data Factory 白皮書的 sap 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) ，其中包含每個 sap 連接器、比較和指引的詳細簡介。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 SAP 資料表連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 SAP 資料表複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats) 資料表。

具體而言，此 SAP 資料表連接器支援：

- 從下列來源的 SAP 資料表複製資料：

  - Sap ERP 中央元件 (SAP ECC) 7.01 版或更新版本 (在 2015) 之後發行的最新 SAP 支援封裝堆疊中。
  - SAP Business 倉儲 (SAP BW) 7.01 版或更新版本， (在 2015) 之後發行的最新 SAP 支援封裝堆疊中。
  - SAP S/4HANA。
  - SAP Business Suite 7.01 版或更新版本中的其他產品 (在 2015) 之後發行的最新 SAP 支援封裝堆疊中。

- 從 SAP 透明資料表、集區資料表、叢集資料表和視圖複製資料。
- 如果已設定 SNC，請使用基本驗證或安全網路通訊來複製資料 (SNC) 。
- 連接至 SAP 應用程式伺服器或 SAP 訊息伺服器。
- 透過預設或自訂的 RFC 來抓取資料。

7.01 版或更新版本指的是 SAP NetWeaver 版本，而不是 SAP ECC 版本。 例如，SAP ECC 6.0 EHP 7 一般都有 NetWeaver 版本 >= 7.4。 如果您不確定您的環境，以下是從您的 SAP 系統確認版本的步驟：

1. 使用 SAP GUI 連接到 SAP 系統。 
2. 移至 [ **系統**  ->  **狀態** ]。 
3. 檢查 SAP_BASIS 的版本，確定它等於或大於701。  
      ![檢查 SAP_BASIS](./media/connector-sap-table/sap-basis.png)

## <a name="prerequisites"></a>Prerequisites

若要使用此 SAP 資料表連接器，您需要：

- 設定自我裝載整合執行時間 (3.17 版或更新版本的) 。 如需詳細資訊，請參閱 [建立和設定自我裝載整合運行](create-self-hosted-integration-runtime.md)時間。

- 從 SAP 的網站下載64位 [Sap Connector For Microsoft .net 3.0](https://support.sap.com/en/product/connectors/msnet.html) ，並將其安裝在自我裝載整合執行時間電腦上。 在安裝期間，請確定您已在 [ **選用設定步驟** ] 視窗中選取 [將 **元件安裝到 GAC** ] 選項。

  ![安裝適用于 .NET 的 SAP Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory SAP 資料表連接器中使用的 SAP 使用者必須具有下列許可權：

  - 使用遠端函數呼叫 (RFC) 目的地的授權。
  - S_SDSAUTH 授權物件的 Execute 活動許可權。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 SAP 資料表連接器特定的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 SAP BW Open Hub 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設定為 `SapTable`。 | 是 |
| `server` | SAP 實例所在的伺服器名稱。<br/>使用連接至 SAP 應用程式伺服器。 | 否 |
| `systemNumber` | SAP 系統的系統編號。<br/>使用連接至 SAP 應用程式伺服器。<br/>允許的值：以字串表示的二位數十進位數。 | 否 |
| `messageServer` | SAP 訊息伺服器的主機名稱。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| `messageServerService` | 訊息伺服器的服務名稱或埠號碼。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| `systemId` | 資料表所在 SAP 系統的識別碼。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| `logonGroup` | SAP 系統的登入群組。<br/>用來連接到 SAP 訊息伺服器。 | 否 |
| `clientId` | SAP 系統中的用戶端識別碼。<br/>允許的值：以字串表示的三位數十進位數。 | 是 |
| `language` | SAP 系統使用的語言。<br/>預設值為 `EN`。| 否 |
| `userName` | 具有 SAP 伺服器存取權之使用者的名稱。 | 是 |
| `password` | 使用者的密碼。 將此欄位標示 `SecureString` 為安全地儲存在 Data Factory 中，或 [參考儲存在 Azure Key Vault 中的秘密](store-credentials-in-key-vault.md)。 | 是 |
| `sncMode` | 用來存取資料表所在之 SAP 伺服器的 SNC 啟用指標。<br/>如果您想要使用 SNC 來連接到 SAP 伺服器，請使用。<br/>允許的值 `0` (off、預設) 或 `1`)  (。 | 否 |
| `sncMyName` | 用來存取資料表所在之 SAP 伺服器的啟動器 SNC 名稱。<br/>當 `sncMode` 為 on 時套用。 | 否 |
| `sncPartnerName` | 用來存取資料表所在之 SAP 伺服器的通訊夥伴 SNC 名稱。<br/>當 `sncMode` 為 on 時套用。 | 否 |
| `sncLibraryPath` | 外部安全性產品的程式庫，用來存取資料表所在的 SAP 伺服器。<br/>當 `sncMode` 為 on 時套用。 | 否 |
| `sncQop` | 要套用的 SNC 保護層級品質。<br/>當 `sncMode` 為 On 時套用。 <br/>允許的值為 `1` (Authentication) 、 `2` (完整性) 、 `3` (隱私權) 、 `8` (預設) 、 (`9` 最大) 。 | 否 |
| `connectVia` | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如先前的 [必要條件](#prerequisites)所述，需要自我裝載的整合執行時間。 |是 |

**範例1：連接至 SAP 應用程式伺服器**

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

如需定義資料集之區段和屬性的完整清單，請參閱 [資料集](concepts-datasets-linked-services.md)。 下列小節提供 SAP 資料表資料集所支援的屬性清單。

若要從 SAP BW 開啟中樞連結服務複製資料及將資料複製到其中，支援下列屬性：

| 屬性 | 描述 | 必要 |
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

如需定義活動之區段和屬性的完整清單，請參閱 [管線](concepts-pipelines-activities.md)。 下一節提供 SAP 資料表來源所支援的屬性清單。

### <a name="sap-table-as-source"></a>SAP 資料表作為來源

若要從 SAP 資料表複製資料，支援下列屬性：

| 屬性                         | 描述                                                  | 必要 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` 屬性必須設定為 `SapTableSource`。         | 是      |
| `rowCount`                         | 要取出的資料列數目。                              | 否       |
| `rfcTableFields`                 | 欄位 (資料行) 要從 SAP 資料表複製。 例如： `column0, column1` 。 | 否       |
| `rfcTableOptions`                | 用來篩選 SAP 資料表中之資料列的選項。 例如： `COLUMN0 EQ 'SOMEVALUE'` 。 另請參閱本文稍後的 SAP 查詢操作員資料表。 | 否       |
| `customRfcReadTableFunctionModule` | 可以用來從 SAP 資料表讀取資料的自訂 RFC 函數模組。<br>您可以使用自訂 RFC 函式模組來定義如何從 SAP 系統取出資料，並將其傳回 Data Factory。 自訂函式模組必須有實 (匯入、匯出、資料表) 的介面，其類似于 `/SAPDS/RFC_READ_TABLE2` Data Factory 所使用的預設介面。<br>Data Factory | 否       |
| `partitionOption`                  | 從 SAP 資料表讀取的資料分割機制。 支援的選項包括： <ul><li>`None`</li><li>`PartitionOnInt` (左填補的一般整數或整數值，例如 `0000012345`) </li><li>`PartitionOnCalendarYear` (4 位數，格式為 "YYYY" ) </li><li>`PartitionOnCalendarMonth` (6 位數的格式為 "YYYYMM" ) </li><li>`PartitionOnCalendarDate` (8 位數，格式為 "YYYYMMDD" ) </li><li>`PartitionOntime` (6 位數的格式為 "HHMMSS"，例如 `235959`) </li></ul> | 否       |
| `partitionColumnName`              | 用來分割資料的資料行名稱。                | 否       |
| `partitionUpperBound`              | 中指定之資料行的最大值 `partitionColumnName` 將用來繼續進行資料分割。 | 否       |
| `partitionLowerBound`              | 在中指定之資料行的最小值 `partitionColumnName` ，將用來繼續進行資料分割。  (附注： `partitionLowerBound` 當分割區選項為) 時，不可以是 "0" `PartitionOnInt` | 否       |
| `maxPartitionsNumber`              | 要分割資料的資料分割數目上限。     | 否       |
| `sapDataColumnDelimiter` | 用來做為分隔符號的單一字元，可傳遞至 SAP RFC 以分割輸出資料。 | 否 |

>[!TIP]
>如果您的 SAP 資料表有大量資料（例如數億個數據列），請使用 `partitionOption` 和 `partitionSetting` 將資料分割成較小的資料分割。 在此情況下，資料會讀取每個分割區，而且每個資料磁碟分割都是透過單一 RFC 呼叫從您的 SAP 伺服器中取出。<br/>
<br/>
>作為 `partitionOption` `partitionOnInt` 範例，每個資料分割中的資料列數目都是使用下列公式來計算： (在 `partitionUpperBound` 和) /之間的總數據列數 `partitionLowerBound` `maxPartitionsNumber` 。<br/>
<br/>
>若要以平行方式載入資料分割以加速複製，平行程度是由 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 複製活動上的設定所控制。 例如，如果您設定 `parallelCopies` 為四，Data Factory 會同時產生並執行四個以指定的資料分割選項和設定為基礎的查詢，而每個查詢都會從您的 SAP 資料表中取出部分資料。 強烈建議您將 `maxPartitionsNumber` 屬性的值設為多個值 `parallelCopies` 。 將資料複製到以檔案為基礎的資料存放區時，也會建議以多個檔案的形式寫入資料夾， (只指定資料夾名稱) ，在這種情況下，效能優於寫入至單一檔案。

在中 `rfcTableOptions` ，您可以使用下列一般 SAP 查詢運算子來篩選資料列：

| 運算子 | 描述 |
| :------- | :------- |
| `EQ` | 等於 |
| `NE` | 不等於 |
| `LT` | 小於 |
| `LE` | 小於或等於 |
| `GT` | 大於 |
| `GE` | 大於或等於 |
| `IN` | 如下所示 `TABCLASS IN ('TRANSP', 'INTTAB')` |
| `LIKE` | 如下所示 `LIKE 'Emma%'` |

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
| `C` (字串)  | `String` |
| `I` (整數)  | `Int32` |
| `F` (Float)  | `Double` |
| `D` (日期)  | `String` |
| `T` (時間)  | `String` |
| `P` (BCD 壓縮、貨幣、十進位、數量)  | `Decimal` |
| `N` (數值)  | `String` |
| `X` (二進位和原始)  | `String` |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
