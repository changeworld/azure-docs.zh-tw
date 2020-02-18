---
title: 使用 Azure Data Factory 在 Oracle 之間複製資料
description: 瞭解如何使用 Data Factory，將資料從支援的來源存放區複製到 Oracle 資料庫，或從 Oracle 複製到支援的接收存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 874c685491774e2a318ae0a8b7394945a51b2f7f
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423805"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Oracle 複製資料及將資料複製到該處
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-onprem-oracle-connector.md)
> * [目前的版本](connector-oracle.md)

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料從 Oracle 資料庫複製到其中。 它是以[複製活動總覽](copy-activity-overview.md)為基礎。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Oracle 連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Oracle 資料庫複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到 Oracle 資料庫。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Oracle 連接器支援：

- 下列 Oracle 資料庫版本：
    - Oracle 18c R1 （18.1）和更高版本
    - Oracle 12c R1 （12.1）和更高版本
    - Oracle 11g R1 （11.1）和更高版本
    - Oracle 10g R1 （10.1）和更高版本
    - Oracle 9i R2 （9.2）和更高版本
    - Oracle 8i R3 （8.1.7）和更高版本
    - Oracle Database Cloud Exadata 服務
- 從 Oracle 來源平行複製。 如需詳細資訊，請參閱[從 Oracle 平行複製](#parallel-copy-from-oracle)一節。

> [!Note]
> 不支援 Oracle Proxy 伺服器。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

整合執行階段提供內建的 Oracle 驅動程式。 因此，當您從 Oracle 複製資料或將資料複製到該處時，不需要手動安裝驅動程式。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Oracle 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Oracle 連結服務支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設定為 **Oracle**。 | 是 |
| connectionString | 指定連線到 Oracle 資料庫執行個體所需的資訊。 <br/>您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `password` 設定。 請參閱下列範例，並[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)，並提供更多詳細資料。 <br><br>**支援的連線類型**：您可以使用 [Oracle SID] 或 [Oracle 服務名稱] 來識別您的資料庫：<br>- 如果您使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果您使用服務名稱：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>針對 [先進的 Oracle 原生連接選項]，您可以選擇在 Tnsnames.ora 中新增專案[。TNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora)檔案在 oracle 伺服器和 ADF oracle 連結服務中，選擇使用 [Oracle 服務名稱] 連線類型，並設定對應的服務名稱。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 深入瞭解[必要條件](#prerequisites)一節。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

>[!TIP]
>如果您收到錯誤「TNSNAMES.ORA-01025： UPI 參數超出範圍」，而您的 Oracle 版本為8i，請將 `WireProtocolMode=1` 新增至您的連接字串。 然後再試一次。

您可以在連接字串中，根據您的案例設定更多的連接屬性：

| 屬性 | 描述 | 允許的值 |
|:--- |:--- |:--- |
| ArraySize |連接器可以在單一網路來回行程中提取的位元組數目。 例如，`ArraySize=‭10485760‬`。<br/><br/>較大的值會藉由減少透過網路提取資料的次數來增加輸送量。 較小的值會增加回應時間，因為等待伺服器傳輸資料的延遲較少。 | 從1到4294967296（4 GB）的整數。 預設值為 `60000`。 值1不會定義位元組數目，而是表示只為一個資料列配置空間。 |

若要啟用 Oracle 連線加密，您有兩個選項：

-   若要使用**三重 DES 加密（3des）和進階加密標準（AES）** ，請在 oracle 伺服器端，移至 Oracle Advanced SECURITY （OAS）並設定加密設定。 如需詳細資訊，請參閱此[Oracle 檔](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)集。 Oracle 應用程式開發架構（ADF）連接器會在建立與 Oracle 的連線時，自動協商加密方法，以使用您在 OAS 中設定的方法。

-   若要使用**SSL**：

    1.  取得 SSL 憑證資訊。 取得您 SSL 憑證的可辨別編碼規則（DER）編碼憑證資訊，並儲存輸出（-----開始憑證 。 End Certificate -----) 儲存為文字檔。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **範例：** 從 DERcert 解壓縮 cert 資訊，然後將輸出儲存至 cert。

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  建立 `keystore` 或 `truststore`。 下列命令會建立包含或不含密碼的 `truststore` 檔案（採用 PKCS-12 格式）。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **範例：** 使用密碼建立名為 MyTrustStoreFile 的 PKCS12 `truststore` 檔案。

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  將 `truststore` 檔案放在自我裝載的 IR 機器上。 例如，將檔案放在 C:\MyTrustStoreFile。
    4.  在 Azure Data Factory 中，使用 `EncryptionMethod=1` 和對應的 `TrustStore`/`TrustStorePassword`值來設定 Oracle 連接字串。 例如： `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>` 。

**範例︰**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
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

本節提供 Oracle 資料集所支援的屬性清單。 如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。 

若要將資料從和複製到 Oracle，請將資料集的 type 屬性設定為 `OracleTable`。 以下是支援的屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為 `OracleTable`。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 資料表/視圖的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有架構的資料表/視圖名稱。 此屬性支援回溯相容性。 針對新的工作負載，請使用 `schema` 和 `table`。 | 否 (來源)；是 (接收) |

**範例︰**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Oracle 來源和接收所支援的屬性清單。 如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。 

### <a name="oracle-as-source"></a>Oracle 作為來源

>[!TIP]
>若要使用資料分割有效率地從 Oracle 載入資料，請從[oracle 的平行複製](#parallel-copy-from-oracle)深入瞭解。

若要從 Oracle 複製資料，請將複製活動中的來源類型設定為 `OracleSource`。 複製活動的 [來源] 區段支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為 `OracleSource`。 | 是 |
| oracleReaderQuery | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。<br>當您啟用資料分割載入時，您必須在查詢中攔截任何對應的內建資料分割參數。 如需範例，請參閱[從 Oracle 平行複製](#parallel-copy-from-oracle)一節。 | 否 |
| partitionOptions | 指定用來從 Oracle 載入資料的資料分割選項。 <br>允許的值為： **None** （預設值）、 **PhysicalPartitionsOfTable**和**DynamicRange**。<br>當分割區選項已啟用（也就是不 `None`）時，從 Oracle 資料庫並行載入資料的平行處理原則程度，是由複製活動上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)設定所控制。 | 否 |
| partitionSettings | 指定資料分割的設定群組。 <br>當分割區選項不 `None`時套用。 | 否 |
| partitionNames | 需要複製的實體分割區清單。 <br>當分割區選項 `PhysicalPartitionsOfTable`時套用。 如果您使用查詢來抓取來源資料，請在 WHERE 子句中攔截 `?AdfTabularPartitionName`。 如需範例，請參閱[從 Oracle 平行複製](#parallel-copy-from-oracle)一節。 | 否 |
| partitionColumnName | **以整數類型**指定來源資料行的名稱，以供範圍分割用於平行複製。 如果未指定，則會自動偵測資料表的主鍵，並使用該索引鍵做為資料分割資料行。 <br>當分割區選項 `DynamicRange`時套用。 如果您使用查詢來抓取來源資料，請在 WHERE 子句中攔截 `?AdfRangePartitionColumnName`。 如需範例，請參閱[從 Oracle 平行複製](#parallel-copy-from-oracle)一節。 | 否 |
| partitionUpperBound | 用來複製資料的分割區資料行的最大值。 <br>當分割區選項 `DynamicRange`時套用。 如果您使用查詢來抓取來源資料，請在 WHERE 子句中攔截 `?AdfRangePartitionUpbound`。 如需範例，請參閱[從 Oracle 平行複製](#parallel-copy-from-oracle)一節。 | 否 |
| partitionLowerBound | 用來複製資料的分割區資料行的最小值。 <br>當分割區選項 `DynamicRange`時套用。 如果您使用查詢來抓取來源資料，請在 WHERE 子句中攔截 `?AdfRangePartitionLowbound`。 如需範例，請參閱[從 Oracle 平行複製](#parallel-copy-from-oracle)一節。 | 否 |

**範例：使用沒有分割區的基本查詢來複製資料**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle 作為接收器

若要將資料複製到 Oracle，請將複製活動中的接收類型設定為 `OracleSink`。 複製活動的 [接收] 區段支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的 type 屬性必須設定為 `OracleSink`。 | 是 |
| writeBatchSize | 當緩衝區大小達到 `writeBatchSize`時，將資料插入 SQL 資料表中。<br/>允許的值為整數 (資料列數目)。 |否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/>允許的值為時間範圍。 範例是 00:30:00 (30 分鐘)。 | 否 |
| preCopyScript | 指定在每次執行時將資料寫入 Oracle 之前，要執行之複製活動的 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>從 Oracle 進行平行複製

Data Factory Oracle 連接器會提供內建的資料分割，以平行方式從 Oracle 複製資料。 您可以在複製活動的 [**來源**] 索引標籤上找到資料分割選項。

![資料分割選項的螢幕擷取畫面](./media/connector-oracle/connector-oracle-partition-options.png)

當您啟用資料分割複本時，Data Factory 會針對您的 Oracle 來源執行平行查詢，以依分割區載入資料。 平行程度是由複製活動上的[`parallelCopies`](copy-activity-performance.md#parallel-copy)設定所控制。 例如，如果您將 `parallelCopies` 設定為四，Data Factory 會同時根據您指定的資料分割選項和設定，產生並執行四個查詢，而且每個查詢都會從您的 Oracle 資料庫中取得部分資料。

建議您啟用具有資料分割的平行複製，特別是當您從 Oracle 資料庫載入大量資料時。 以下是適用于不同案例的建議設定。 將資料複製到以檔案為基礎的資料存放區時，會建議寫入資料夾做為多個檔案（僅指定資料夾名稱），在此情況下，效能會比寫入單一檔案更好。

| 狀況                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 具有實體分割區的大型資料表完整載入。          | 資料**分割選項**：資料表的實體分割區。 <br><br/>在執行期間，Data Factory 會自動偵測實體分割區，並依分割區複製資料。 |
| 從大型資料表（不含實體分割區）進行完整載入，同時使用資料磁碟分割的整數資料行。 | 資料**分割選項**：動態範圍分割區。<br>**分割**區資料行：指定用來分割資料的資料行。 如果未指定，則會使用主鍵資料行。 |
| 使用包含實體分割區的自訂查詢來載入大量資料。 | 資料**分割選項**：資料表的實體分割區。<br>**查詢**： `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`。<br>**分割區名稱**：指定要從中複製資料的分割區名稱。 如果未指定，Data Factory 會自動偵測您在 Oracle 資料集中指定之資料表上的實體分割區。<br><br>在執行期間，Data Factory 會將 `?AdfTabularPartitionName` 取代為實際的分割區名稱，並傳送至 Oracle。 |
| 使用自訂查詢（不含實體分割區）載入大量資料，而使用資料磁碟分割的整數資料行。 | 資料**分割選項**：動態範圍分割區。<br>**查詢**： `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分割**區資料行：指定用來分割資料的資料行。 您可以針對具有整數資料類型的資料行進行分割。<br>**分割區上限**和資料**分割下限**：指定您是否要針對資料分割資料行進行篩選，以便只在範圍下限和上限之間取得資料。<br><br>在執行期間，Data Factory 會將 `?AdfRangePartitionColumnName`、`?AdfRangePartitionUpbound`和 `?AdfRangePartitionLowbound` 取代為每個資料分割的實際資料行名稱和值範圍，並傳送至 Oracle。 <br>例如，如果您的資料分割資料行 "ID" 已設定為下限為1，而上限為80，且 parallel copy 設為4，則 Data Factory 會依4個分割區來抓取資料。 其識別碼分別介於 [1，20]、[21，40]、[41、60] 和 [61，80] 之間。 |

**範例：使用實體資料分割進行查詢**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**範例：使用動態範圍分割進行查詢**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Oracle 的資料類型對應

當您將資料從和複製到 Oracle 時，會套用下列對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Oracle 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(僅 Oracle 10g 及更高版本可支援) |
| CHAR |String |
| CLOB |String |
| 日期 |Datetime |
| FLOAT |Decimal，字串 (如果精確度 > 28) |
| INTEGER |Decimal，字串 (如果精確度 > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal，字串 (如果精確度 > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| 不帶正負號的整數 |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 不支援 INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND 資料類型。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要瞭解屬性的詳細資料，請檢查[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
