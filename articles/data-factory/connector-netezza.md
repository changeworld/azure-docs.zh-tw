---
title: 使用 Azure Data Factory 從 Netezza 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Netezza 將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: c51469997af23be7a5e1b88677ecadb37e10ac64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244533"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Netezza 複製資料

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Netezza 複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

>[!TIP]
>對於從 Netezza 到 Azure 的資料移轉方案，從[使用 Azure 資料工廠將資料從本地 Netezza 伺服器遷移到 Azure](data-migration-guidance-netezza-azure-sqldw.md)中學習詳細資訊。

## <a name="supported-capabilities"></a>支援的功能

此 Netezza 連接器支援以下活動：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)


您可以將資料從 Netezza 複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

Netezza 連接器支援從源並行複製。 有關詳細資訊，請參閱[Netezza 部分的並行副本](#parallel-copy-from-netezza)。

Azure Data Factory 會提供內建的驅動程式來啟用連線。 您不需要為了使用此連接器而需手動安裝驅動程式。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>開始使用

您可以使用 .NET SDK、Python SDK、Azure PowerShell、REST API 或 Azure Resource Manager 範本來建立使用複製活動的管線。 如需逐步指示來了解如何建立內含複製活動的管線，請參閱[複製活動教學課程](quickstart-create-data-factory-dot-net.md)。

下列各節提供屬性的相關詳細資料，您可使用這些屬性來定義 Netezza 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Netezza 連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設為 **Netezza**。 | 是 |
| connectionString | 連線到 Netezza 的 ODBC 連接字串。 <br/>您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `pwd` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`。 下表描述更多您可設定的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| SecurityLevel | 驅動程式用來連線到資料存放區的安全性 (SSL/TLS) 層級。 範例： `SecurityLevel=preferredSecured`. 支援的值為：<br/>- **僅限未受保護連線** (**onlyUnSecured**)：驅動程式不會使用 SSL。<br/>- **偏好未受保護連線 (preferredUnSecured) (預設值)**：如果伺服器提供選擇，則驅動程式不使用 SSL。 <br/>- **首選安全（首選安全）：** 如果伺服器提供選擇，驅動程式將使用 SSL。 <br/>- **僅限受保護連線 (onlySecured)**：除非有 SSL 連線，否則驅動程式不會連線。 | 否 |
| CaCertFile | 伺服器所用 SSL 憑證的完整路徑。 範例： `CaCertFile=<cert path>;`| 是，如果已啟用 SSL |

**範例**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Azure Key Vault 中儲存密碼**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

本節提供 Netezza 資料集所支援的屬性清單。

如需定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。

若要從 Netezza 複製資料，請將資料集的 **type** 屬性設定為 **NetezzaTable**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設置為 **：NetezzaTable** | 是 |
| 結構描述 | 結構描述的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| 資料表 | 資料表的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| tableName | 具有架構的表的名稱。 此屬性支援向後相容性。 用於`schema`新`table`工作負載。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Netezza 來源所支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。

### <a name="netezza-as-source"></a>Netezza 作為來源

>[!TIP]
>要使用資料分區有效地載入來自 Netezza 的資料，[請從 Netezza](#parallel-copy-from-netezza)部分的並行副本中學習詳細資訊。

若要從 Netezza 複製資料，請將複製活動中的**來源**類型設定為 **NetezzaSource**。 複製活動**源**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 **NetezzaSource**。 | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 範例： `"SELECT * FROM MyTable"` | 否 (如果已指定資料集中的 "tableName") |
| 分區選項 | 指定用於從 Netezza 載入資料的資料分區選項。 <br>允許值為：**無**（預設）、**資料切片**和**動態範圍**。<br>啟用分區選項（即不是`None`），通過[`parallelCopies`](copy-activity-performance.md#parallel-copy)設置複製活動來控制從 Netezza 資料庫同時載入資料的並行性程度。 | 否 |
| 分區設置 | 指定資料分區設置的組。 <br>當分區選項未時`None`應用。 | 否 |
| 分區列名稱 | **以整數類型**指定源列的名稱，該類型將由並行複製的定界分割使用。 如果未指定，則自動檢測表的主鍵並用作分區列。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索來源資料，請掛鉤`?AdfRangePartitionColumnName`WHERE 子句。 請參閱[Netezza](#parallel-copy-from-netezza)部分的並行副本中的示例。 | 否 |
| 分區 上邊界 | 要將資料複製出去的分區列的最大值。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索來源資料，請掛鉤`?AdfRangePartitionUpbound`WHERE 子句。 例如，請參閱[Netezza 部分的並行副本](#parallel-copy-from-netezza)。 | 否 |
| 分區下限 | 要將資料複製出去的分區列的最小值。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索來源資料，請掛鉤`?AdfRangePartitionLowbound`WHERE 子句。 例如，請參閱[Netezza 部分的並行副本](#parallel-copy-from-netezza)。 | 否 |

**例子：**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>來自內特紮的並行副本

資料工廠 Netezza 連接器提供內置資料分區，以並行複製來自 Netezza 的資料。 您可以在複製活動的**源**表上找到資料分區選項。

![分區選項的螢幕截圖](./media/connector-netezza/connector-netezza-partition-options.png)

啟用分區副本時，資料工廠會針對 Netezza 源運行並行查詢，以便按分區載入資料。 並行度由複製活動上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)設置控制。 例如，如果將設置為`parallelCopies`4，則資料工廠會根據指定的分區選項和設置同時生成和運行四個查詢，並且每個查詢從 Netezza 資料庫中檢索一部分資料。

建議使用資料分區啟用並行複製，尤其是在從 Netezza 資料庫中載入大量資料時。 以下是針對不同方案的建議配置。 將資料複製到基於檔的資料存儲中時，將重新命令以多個檔形式寫入資料夾（僅指定資料夾名稱），在這種情況下，性能優於寫入單個檔。

| 狀況                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 大桌子滿載。                                   | **分區選項**：資料切片。 <br><br/>在執行期間，資料工廠根據[Netezza 的內置資料切片](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)自動對資料進行分區，並按分區複製資料。 |
| 使用自訂查詢載入大量資料。                 | **分區選項**：資料切片。<br>**查詢** `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`： .<br>在執行期間，資料工廠將`?AdfPartitionCount`替換（在複製活動上設置並行拷貝編號）`?AdfDataSliceCondition`和資料切片分區邏輯，然後發送到 Netezza。 |
| 通過使用自訂查詢載入大量資料，該查詢具有具有均勻分佈值的整數列，用於定界分割。 | **分區選項**：動態範圍分區。<br>**查詢** `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`： .<br>**分區列**：指定用於對資料進行分區的列。 您可以使用整數資料類型對列進行分區。<br>**分區上限**和**分區下限**：指定是否要針對分區列進行篩選，以便僅在下部和上部範圍之間檢索資料。<br><br>在執行期間，資料工廠`?AdfRangePartitionColumnName``?AdfRangePartitionUpbound``?AdfRangePartitionLowbound`將 替換 和 替換為每個分區的實際列名稱和值範圍，併發送到 Netezza。 <br>例如，如果分區列"ID"設置下限為 1，上限設置為 80，並行副本設置為 4，則資料工廠將資料按 4 個分區檢索資料。 他們的年號分別在[1，20]、[21、40]、[41、60]和[61、80]之間。 |

**示例：使用資料切片分區進行查詢**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**示例：具有動態範圍分區的查詢**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
