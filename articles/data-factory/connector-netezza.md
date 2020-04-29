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
ms.openlocfilehash: 89efa8dc9989f693964415741299042c63f93780
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418111"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Netezza 複製資料
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Netezza 複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

>[!TIP]
>如需從 Netezza 至 Azure 的資料移轉案例，請深入瞭解[使用 Azure Data Factory 將資料從內部部署 Netezza 伺服器遷移至 azure](data-migration-guidance-netezza-azure-sqldw.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Netezza 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)


您可以將資料從 Netezza 複製到任何支援的接收資料存放區。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

Netezza 連接器支援從來源進行平行複製。 如需詳細資訊，請參閱[從 Netezza 的平行複製](#parallel-copy-from-netezza)一節。

Azure Data Factory 會提供內建的驅動程式來啟用連線。 您不需要為了使用此連接器而需手動安裝驅動程式。

## <a name="prerequisites"></a>先決條件

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
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 深入瞭解[必要條件](#prerequisites)一節。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`。 下表描述更多您可設定的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| SecurityLevel | 驅動程式用來連線到資料存放區的安全性 (SSL/TLS) 層級。 範例： `SecurityLevel=preferredSecured`. 支援的值為：<br/>- **僅不安全**的（**onlyUnSecured**）：驅動程式不使用 TLS。<br/>- **慣用的不安全（preferredUnSecured）（預設值）**：如果伺服器提供選擇，則驅動程式不會使用 TLS。 <br/>- **慣用的安全（連線 preferredsecured）**：如果伺服器提供選擇，則驅動程式會使用 TLS。 <br/>- **僅受保護（連線 onlysecured）**：除非有 TLS 連線可用，否則驅動程式不會連接。 | 否 |
| CaCertFile | 伺服器所使用的 TLS/SSL 憑證的完整路徑。 範例：`CaCertFile=<cert path>;`| 是，如果已啟用 TLS |

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
| type | 資料集的類型屬性必須設定為： **NetezzaTable** | 是 |
| 結構描述 | 結構描述的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| 資料表 | 資料表的名稱。 |否 (如果已指定活動來源中的「查詢」)  |
| tableName | 具有架構之資料表的名稱。 此屬性支援回溯相容性。 針對`schema`新`table`的工作負載使用和。 | 否 (如果已指定活動來源中的「查詢」) |

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
>若要使用資料分割有效率地從 Netezza 載入資料，請從[Netezza 的平行複製](#parallel-copy-from-netezza)一節深入瞭解。

若要從 Netezza 複製資料，請將複製活動中的**來源**類型設定為 **NetezzaSource**。 複製活動的 [**來源**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 **NetezzaSource**。 | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 範例：`"SELECT * FROM MyTable"` | 否 (如果已指定資料集中的 "tableName") |
| partitionOptions | 指定用來從 Netezza 載入資料的資料分割選項。 <br>允許值為： **None** （預設值）、 **DataSlice**和**DynamicRange**。<br>當分割區選項已啟用（也就是不`None`是）時，從 Netezza 資料庫並行載入資料的平行處理原則程度，是[`parallelCopies`](copy-activity-performance-features.md#parallel-copy)藉由在複製活動上設定來控制。 | 否 |
| partitionSettings | 指定資料分割的設定群組。 <br>當資料分割選項不`None`適用時套用。 | 否 |
| partitionColumnName | **以整數類型**指定來源資料行的名稱，以供範圍分割用於平行複製。 如果未指定，則會 autodetected 資料表的主鍵，並當做資料分割資料行使用。 <br>適用于資料分割選項為`DynamicRange`時。 如果您使用查詢來抓取來源資料，請在 WHERE `?AdfRangePartitionColumnName`子句中掛上。 請參閱[Parallel copy From Netezza](#parallel-copy-from-netezza)一節中的範例。 | 否 |
| partitionUpperBound | 用來複製資料的分割區資料行的最大值。 <br>當資料分割選項為`DynamicRange`時套用。 如果您使用 query 來抓取來源資料，請`?AdfRangePartitionUpbound`在 WHERE 子句中掛上。 如需範例，請參閱[Parallel copy From Netezza](#parallel-copy-from-netezza)一節。 | 否 |
| partitionLowerBound | 用來複製資料的分割區資料行的最小值。 <br>適用于資料分割選項為`DynamicRange`時。 如果您使用查詢來抓取來源資料，請在 WHERE `?AdfRangePartitionLowbound`子句中掛上。 如需範例，請參閱[Parallel copy From Netezza](#parallel-copy-from-netezza)一節。 | 否 |

**範例：**

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

## <a name="parallel-copy-from-netezza"></a>從 Netezza 進行平行複製

Data Factory Netezza 連接器會提供內建的資料分割，以平行方式從 Netezza 複製資料。 您可以在複製活動的**源**資料表上找到資料分割選項。

![資料分割選項的螢幕擷取畫面](./media/connector-netezza/connector-netezza-partition-options.png)

當您啟用資料分割複本時，Data Factory 會針對您的 Netezza 來源執行平行查詢，以依據分割區來載入資料。 平行程度是由複製活動上[`parallelCopies`](copy-activity-performance-features.md#parallel-copy)的設定所控制。 例如，如果您將設定`parallelCopies`為四，Data Factory 會同時根據您指定的資料分割選項和設定，產生並執行四個查詢，而且每個查詢都會從您的 Netezza 資料庫中取得部分資料。

建議您啟用具有資料分割的平行複製，特別是當您從 Netezza 資料庫載入大量資料時。 以下是適用于不同案例的建議設定。 將資料複製到以檔案為基礎的資料存放區時，會建議寫入資料夾做為多個檔案（僅指定資料夾名稱），在此情況下，效能會比寫入單一檔案更好。

| 案例                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 來自大型資料表的完整載入。                                   | **分割區選項**：資料配量。 <br><br/>在執行期間，Data Factory 會根據[Netezza 的內建資料](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html)配量，自動分割資料，並依分割區複製資料。 |
| 使用自訂查詢載入大量資料。                 | **分割區選項**：資料配量。<br>**查詢**： `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`。<br>在執行期間，Data Factory `?AdfPartitionCount`會取代（在複製活動上設定的平行複製`?AdfDataSliceCondition`編號）和資料配量分割區邏輯，並將傳送至 Netezza。 |
| 使用自訂查詢載入大量資料，並具有以平均分佈值進行範圍分割的整數資料行。 | 資料**分割選項**：動態範圍分割區。<br>**查詢**： `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分割**區資料行：指定用來分割資料的資料行。 您可以針對具有整數資料類型的資料行進行分割。<br>**分割區上限**和資料**分割下限**：指定您是否要針對分割區資料行進行篩選，以便只在下限和上限之間抓取資料。<br><br>在執行期間，Data Factory `?AdfRangePartitionColumnName`會`?AdfRangePartitionUpbound`將、 `?AdfRangePartitionLowbound`和取代為實際的資料行名稱和每個資料分割的值範圍，並傳送至 Netezza。 <br>例如，如果您將資料分割資料行 "ID" 設定為下限為1，而上限為80，且 parallel copy 設為4，則 Data Factory 會依4個分割區來抓取資料。 其識別碼分別介於 [1，20]、[21，40]、[41、60] 和 [61，80] 之間。 |

**範例：使用資料配量分割區查詢**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**範例：使用動態範圍分割進行查詢**

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

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
