---
title: 從 SAP 表複製資料
description: 瞭解如何使用 Azure 資料工廠管道中的複製活動將資料從 SAP 表複製到受支援的接收器資料存儲。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: e98ff7fd914bb86cae256bb1bf6c19086758d463
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371553"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>使用 Azure 資料工廠從 SAP 表複製資料

本文概述了如何使用 Azure 資料工廠中的複製活動從 SAP 表複製資料。 有關詳細資訊，請參閱[複製活動概述](copy-activity-overview.md)。

>[!TIP]
>要瞭解 ADF 對 SAP 資料整合方案的總體支援，請參閱[使用 Azure 資料工廠白皮書進行 SAP 資料整合](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf)，並詳細說明介紹、比較和指導。

## <a name="supported-capabilities"></a>支援的功能

此 SAP 表連接器支援以下活動：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從 SAP 表複製到任何受支援的接收器資料存儲。 有關複製活動支援為源或接收器的資料存儲的清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)表。

具體而言，此 SAP 表連接器支援：

- 從 SAP 表中複製資料：

  - SAP ERP 中央元件 （SAP ECC） 版本 7.01 或更高版本（在 2015 年以後發佈的 SAP 支援包堆疊中）。
  - SAP 業務倉庫 （SAP BW） 版本 7.01 或更高版本（在 2015 年以後發佈的最近 SAP 支援包堆疊中）。
  - SAP S/4HANA。
  - SAP 業務套件版本 7.01 或更高版本的其他產品（在 2015 年以後發佈的最近 SAP 支援包堆疊中）。

- 從 SAP 透明表、池表、群集表和視圖複製資料。
- 如果使用基本驗證或安全網路通信 （SNC） 複製資料（如果配置了 SNC）。
- 連接到 SAP 應用程式伺服器或 SAP 消息伺服器。

## <a name="prerequisites"></a>Prerequisites

要使用此 SAP 表連接器，您需要：

- 設置自託管集成運行時（版本 3.17 或更高版本）。 有關詳細資訊，請參閱[創建和配置自託管的集成運行時](create-self-hosted-integration-runtime.md)。

- 從 SAP 網站下載[Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html)的 64 位 SAP 連接器，並將其安裝在自託管的集成運行時電腦上。 在安裝過程中，請確保在 **"可選設置步驟**"視窗中選擇"**將元件安裝到 GAC"** 選項。

  ![為 .NET 安裝 SAP 連接器](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- 在資料工廠 SAP 表連接器中使用的 SAP 使用者必須具有以下許可權：

  - 使用遠端函式呼叫 （RFC） 目標的授權。
  - S_SDSAUTH授權物件的執行活動的許可權。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 SAP 表連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

SAP BW 開放中心連結服務支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設為 `SapTable`。 | 是 |
| `server` | SAP 實例所在的伺服器的名稱。<br/>用於連接到 SAP 應用程式伺服器。 | 否 |
| `systemNumber` | SAP 系統的系統編號。<br/>用於連接到 SAP 應用程式伺服器。<br/>允許值：表示為字串的兩位小數。 | 否 |
| `messageServer` | SAP 消息伺服器的主機名稱。<br/>用於連接到 SAP 消息伺服器。 | 否 |
| `messageServerService` | 消息伺服器的服務名稱或埠號。<br/>用於連接到 SAP 消息伺服器。 | 否 |
| `systemId` | 表所在的 SAP 系統的 ID。<br/>用於連接到 SAP 消息伺服器。 | 否 |
| `logonGroup` | SAP 系統的登錄組。<br/>用於連接到 SAP 消息伺服器。 | 否 |
| `clientId` | SAP 系統中用戶端的 ID。<br/>允許值：表示為字串的三位小數。 | 是 |
| `language` | SAP 系統使用的語言。<br/>預設值為 `EN`。| 否 |
| `userName` | 有權訪問 SAP 伺服器的使用者的名稱。 | 是 |
| `password` | 使用者的密碼。 使用`SecureString`類型標記此欄位以將其安全地存儲在資料工廠中，或[引用存儲在 Azure 金鑰保存庫中的機密](store-credentials-in-key-vault.md)。 | 是 |
| `sncMode` | 用於訪問表所在的 SAP 伺服器的 SNC 啟動指示器。<br/>如果要使用 SNC 連接到 SAP 伺服器，請使用。<br/>允許的值是`0`（關閉、預設）或`1`（開）。 | 否 |
| `sncMyName` | 開始器的 SNC 名稱，用於訪問表所在的 SAP 伺服器。<br/>應用時`sncMode`打開。 | 否 |
| `sncPartnerName` | 通信合作夥伴的 SNC 名稱，用於訪問表所在的 SAP 伺服器。<br/>應用時`sncMode`打開。 | 否 |
| `sncLibraryPath` | 用於訪問表所在的 SAP 伺服器的外部安全產品庫。<br/>應用時`sncMode`打開。 | 否 |
| `sncQop` | 要應用的 SNC 保護品質級別。<br/>應用時`sncMode`處於打開。 <br/>允許的值是`1``2`（身份驗證）、（完整性）、（`3`隱私）、（`8`預設）、（`9`最大值）。 | 否 |
| `connectVia` | 用於連接到資料存儲的[集成運行時](concepts-integration-runtime.md)。 需要自託管的集成運行時，如[先決條件](#prerequisites)前面所述。 |是 |

**示例 1：連接到 SAP 應用程式伺服器**

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

### <a name="example-2-connect-to-an-sap-message-server"></a>示例 2：連接到 SAP 消息伺服器

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

### <a name="example-3-connect-by-using-snc"></a>示例 3：使用 SNC 連接

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

有關用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。 以下部分提供 SAP 表資料集支援的屬性的清單。

要將資料從 SAP BW 開放集線器連結服務複製，請支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| `type` | `type` 屬性必須設為 `SapTableResource`。 | 是 |
| `tableName` | 要從中複製資料的 SAP 表的名稱。 | 是 |

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

有關用於定義活動的節和屬性的完整清單，請參閱[管道](concepts-pipelines-activities.md)。 以下部分提供 SAP 表源支援的屬性的清單。

### <a name="sap-table-as-source"></a>SAP 表作為源

要從 SAP 表複製資料，請支援以下屬性：

| 屬性                         | 描述                                                  | 必要 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | `type` 屬性必須設為 `SapTableSource`。         | 是      |
| `rowCount`                         | 要檢索的行數。                              | 否       |
| `rfcTableFields`                   | 要從 SAP 表複製的欄位（列）。 例如： `column0, column1` 。 | 否       |
| `rfcTableOptions`                  | 篩選 SAP 表中的行的選項。 例如： `COLUMN0 EQ 'SOMEVALUE'` 。 請參閱本文後面的 SAP 查詢運算子表。 | 否       |
| `customRfcReadTableFunctionModule` | 可用於從 SAP 表中讀取資料的自訂 RFC 函數模組。<br>您可以使用自訂 RFC 函數模組來定義如何從 SAP 系統檢索資料並將其返回到資料工廠。 自訂函數模組必須具有與 的介面類別似的`/SAPDS/RFC_READ_TABLE2`介面（導入、匯出、表），該介面是資料工廠使用的預設介面。 | 否       |
| `partitionOption`                  | 要從 SAP 表讀取的分區機制。 支援的選項包括： <ul><li>`None`</li><li>`PartitionOnInt`（法線整數或整數值，左側為零填充，如`0000012345`。</li><li>`PartitionOnCalendarYear`（4 位數位，格式為"YYYY"）</li><li>`PartitionOnCalendarMonth`（6 位數位，格式為"YYYYMM"）</li><li>`PartitionOnCalendarDate`（8 位數位，格式為"YYYYMMD"）</li></ul> | 否       |
| `partitionColumnName`              | 用於對資料進行分區的列的名稱。                | 否       |
| `partitionUpperBound`              | 中`partitionColumnName`指定的列的最大值將用於繼續分區。 | 否       |
| `partitionLowerBound`              | 中`partitionColumnName`指定的列的最小值將用於繼續分區。 （注意：`partitionLowerBound`當分區選項為`PartitionOnInt`時，不能為"0" | 否       |
| `maxPartitionsNumber`              | 要將資料拆分為的最大分區數。     | 否       |

>[!TIP]
>如果您的 SAP 表具有大量資料（如數十億行），請使用`partitionOption`並將`partitionSetting`資料拆分為較小的分區。 在這種情況下，資料按分區讀取，並且每個資料分區通過單個 RFC 調用從 SAP 伺服器檢索。<br/>
<br/>
>`partitionOption`以`partitionOnInt`此為例，每個分區中的行數使用此公式計算：（介於 和`partitionUpperBound``partitionLowerBound`）之間的總行數。`maxPartitionsNumber`<br/>
<br/>
>要並行載入資料分區以加快複製速度，並行度由複製活動上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)設置控制。 例如，如果將設置為`parallelCopies`4，則資料工廠會根據指定的分區選項和設置同時生成和運行四個查詢，並且每個查詢從 SAP 表中檢索一部分資料。 我們強烈建議將`parallelCopies`屬性`maxPartitionsNumber`的值的倍數。 將資料複製到基於檔的資料存儲中時，還會重新命令以多個檔形式寫入資料夾（僅指定資料夾名稱），在這種情況下，性能優於寫入單個檔。

在`rfcTableOptions`中，可以使用以下常見的 SAP 查詢運算子來篩選行：

| 運算子 | 描述 |
| :------- | :------- |
| `EQ` | 等於 |
| `NE` | 不等於 |
| `LT` | 小於 |
| `LE` | 小於或等於 |
| `GT` | 大於 |
| `GE` | 大於或等於 |
| `LIKE` | 如`LIKE 'Emma%'` |

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

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 表的資料類型映射

從 SAP 表複製資料時，將使用以下映射從 SAP 表資料類型到 Azure 資料工廠臨時資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| SAP ABAP 類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| `C`（弦） | `String` |
| `I`（整數） | `Int32` |
| `F`（浮動） | `Double` |
| `D`（日期） | `String` |
| `T`（時間） | `String` |
| `P`（BCD 包裝， 貨幣， 十進位， 數量） | `Decimal` |
| `N`（數位） | `String` |
| `X`（二進位和原始） | `String` |

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟

有關 Azure 資料工廠中複製活動支援為源和接收器的資料存儲的清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)。
