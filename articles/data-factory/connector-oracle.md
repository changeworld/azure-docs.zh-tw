---
title: 使用 Azure Data Factory 將資料複製到 Oracle 或從中複製資料
description: 瞭解如何使用 Data Factory，將資料從支援的來源存放區複製到 Oracle 資料庫，或從 Oracle 複製到支援的接收存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: b4d2b277eea85fb8a5c9eb733e5bfd64d66f392c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91407821"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Oracle 複製資料及將資料複製到該處

> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](v1/data-factory-onprem-oracle-connector.md)
> * [目前的版本](connector-oracle.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Oracle 資料庫複製資料及將資料複製到其中。 它是以 [複製活動總覽](copy-activity-overview.md)為基礎。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Oracle 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Oracle 資料庫複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到 Oracle 資料庫。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Oracle 連接器支援：

- 下列版本的 Oracle 資料庫：
    - Oracle 19c R1 (19.1) 和更高版本
    - Oracle 18c R1 (18.1) 和更高版本
    - Oracle 12c R1 (12.1) 和更高版本
    - Oracle 11g R1 (11.1) 和更高版本
    - Oracle 10g R1 (10.1) 和更高版本
    - Oracle 9i R2 (9.2) 和更高版本
    - Oracle 8i R3 (8.1.7) 和更高版本
    - Oracle Database Cloud Exadata Service
- 從 Oracle 來源進行平行複製。 如需詳細資料，請參閱 [從 Oracle 平行複製](#parallel-copy-from-oracle) 一節。

> [!Note]
> 不支援 Oracle Proxy 伺服器。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

整合執行階段提供內建的 Oracle 驅動程式。 因此，當您從 Oracle 複製資料或將資料複製到該處時，不需要手動安裝驅動程式。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Oracle 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Oracle 連結服務支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為 **Oracle**。 | 是 |
| connectionString | 指定連線到 Oracle 資料庫執行個體所需的資訊。 <br/>您也可以將密碼放在 Azure Key Vault 中，並 `password` 從連接字串中提取設定。 請參閱下列範例，並 [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md) ，並提供更多詳細資料。 <br><br>**支援的連線類型**：您可以使用 [Oracle SID]**** 或 [Oracle 服務名稱]**** 來識別您的資料庫：<br>- 如果您使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果您使用服務名稱：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>您可以選擇在 [Tnsnames.ora] 中新增專案 [。](http://www.orafaq.com/wiki/Tnsnames.ora) 在 oracle 伺服器上的 tnsnames.ora 檔案，以及在 ADF Oracle 連結服務中，選擇使用 Oracle 服務名稱連線類型，並設定對應的服務名稱。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

>[!TIP]
>如果您收到「TNSNAMES.ORA-01025： UPI 參數超出範圍」的錯誤，而且您的 Oracle 版本為8i，請將加入 `WireProtocolMode=1` 您的連接字串。 然後再試一次。

如果您有多個 Oracle 實例以進行容錯移轉案例，您可以建立 Oracle 連結服務，並填入主要主機、埠、使用者名稱、密碼等，然後加入新的 [**其他連接屬性**]，其屬性名稱為，而 [值] 則是 [其他連接屬性]，並將)  ([屬性名稱] `AlternateServers` 和 [值] 改為 [值] `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)` `:` 。 例如，下列替代伺服器值會定義兩個用於連接容錯移轉的替代資料庫伺服器： `(HostName=AccountingOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)` 。

您可以根據您的案例，在連接字串中設定更多的連接屬性：

| 屬性 | 描述 | 允許的值 |
|:--- |:--- |:--- |
| ArraySize |連接器可以在單一網路往返中提取的位元組數目。 例如， `ArraySize=‭10485760‬` 。<br/><br/>較大的值會藉由減少透過網路提取資料的次數來增加輸送量。 較小的值會增加回應時間，因為等待伺服器傳輸資料的延遲較少。 | 從1到 4294967296 (4 GB) 的整數。 預設值為 `60000`。 值1不會定義位元組數目，但表示只為一個資料列配置空間。 |

若要啟用 Oracle 連線加密，您有兩個選項：

-   若要使用 **三重 DES 加密 (3des) 和進階加密標準 (AES) **，請在 oracle 伺服器端，移至 Oracle Advanced SECURITY (OAS) 並設定加密設定。 如需詳細資訊，請參閱此 [Oracle 檔](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)。 Oracle 應用程式開發架構 (ADF) 連接器會自動協調加密方法，以在建立與 Oracle 的連接時使用您在 OAS 中設定的方法。

-   使用 **TLS**：

    1.  取得 TLS/SSL 憑證資訊。 取得 TLS/SSL 憑證的可辨別編碼規則 (DER) 編碼的憑證資訊，然後儲存輸出 (-----開始憑證 .。。 End Certificate -----) 儲存為文字檔。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **範例：** 從 DERcert 解壓縮 cert 資訊，然後將輸出儲存至 cert.txt。

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
    
    2.  建立 `keystore` 或 `truststore` 。 下列命令 `truststore` 會建立包含或不含密碼的檔案（採用 PKCS-12 格式）。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **範例：**`truststore`使用密碼建立名為 MyTrustStoreFile 的 PKCS12 檔案。

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  將檔案放 `truststore` 在自我裝載的 IR 機器上。 例如，將檔案放在 C:\MyTrustStoreFile。
    4.  在 Azure Data Factory 中，使用 `EncryptionMethod=1` 和對應的值來設定 Oracle 連接字串 `TrustStore` / `TrustStorePassword` 。 例如： `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>` 。

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

本節提供 Oracle 資料集所支援的屬性清單。 如需定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)。 

若要從 Oracle 複製資料或將資料複製到該處，請將資料集的類型屬性設定為 `OracleTable` 。 以下是支援的屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為 `OracleTable` 。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 資料表/檢視的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有結構描述的資料表/檢視名稱。 支援此屬性是基於回溯相容性。 對於新的工作負載，請使用 `schema` 和 `table`。 | 否 (來源)；是 (接收) |

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
>若要使用資料分割有效率地從 Oracle 載入資料，請從 [Oracle 平行複製](#parallel-copy-from-oracle)深入瞭解。

若要從 Oracle 複製資料，請將複製活動中的來源類型設定為 `OracleSource` 。 複製活動的 [來源] 區段支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為 `OracleSource` 。 | 是 |
| oracleReaderQuery | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。<br>當您啟用分割區載入時，您必須在查詢中攔截任何對應的內建資料分割參數。 如需範例，請參閱 [從 Oracle 平行複製](#parallel-copy-from-oracle) 一節。 | 否 |
| partitionOptions | 指定用來從 Oracle 載入資料的資料分割選項。 <br>允許的值為： **無** (預設值) 、 **PhysicalPartitionsOfTable**和 **DynamicRange**。<br>當您啟用分割區選項時 (也就是不 `None`) ，從 Oracle 資料庫並行載入資料的平行處理原則程度，是由 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 複製活動上的設定所控制。 | 否 |
| partitionSettings | 指定資料分割的設定群組。 <br>當資料分割選項不適用時套用 `None` 。 | 否 |
| partitionNames | 需要複製的實體分割區清單。 <br>當分割選項是 `PhysicalPartitionsOfTable` 時套用。 如果您使用查詢來取出來源資料，請在 WHERE 子句中加上 `?AdfTabularPartitionName`。 如需範例，請參閱 [從 Oracle 平行複製](#parallel-copy-from-oracle) 一節。 | 否 |
| partitionColumnName | 指定**整數類型**來源資料行的名稱，供平行複製的範圍分割使用。 如果未指定，則會自動偵測資料表的主鍵，並將其當做資料分割資料行使用。 <br>當分割選項是 `DynamicRange` 時套用。 如果您使用查詢來取出來源資料，請  `?AdfRangePartitionColumnName` 在 WHERE 子句中掛上。 如需範例，請參閱 [從 Oracle 平行複製](#parallel-copy-from-oracle) 一節。 | 否 |
| partitionUpperBound | 從分割資料行複製出資料時的最大值。 <br>當分割選項是 `DynamicRange` 時套用。 如果您使用查詢來取出來源資料，請在 WHERE 子句中加上 `?AdfRangePartitionUpbound`。 如需範例，請參閱 [從 Oracle 平行複製](#parallel-copy-from-oracle) 一節。 | 否 |
| partitionLowerBound | 從分割資料行複製出資料時的最小值。 <br>當分割選項是 `DynamicRange` 時套用。 如果您使用查詢來取出來源資料，請在 WHERE 子句中加上 `?AdfRangePartitionLowbound`。 如需範例，請參閱 [從 Oracle 平行複製](#parallel-copy-from-oracle) 一節。 | 否 |

**範例：使用沒有資料分割的基本查詢來複製資料**

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

若要將資料複製到 Oracle，請將複製活動中的接收類型設定為 `OracleSink` 。 複製活動的 [接收] 區段支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設定為 `OracleSink` 。 | 是 |
| writeBatchSize | 當緩衝區大小達到時，將資料插入 SQL 資料表中 `writeBatchSize` 。<br/>允許的值為整數 (資料列數目)。 |否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/>允許的值為時間範圍。 範例是 00:30:00 (30 分鐘)。 | 否 |
| preCopyScript | 針對每次執行時將資料寫入 Oracle 之前，指定要執行之複製活動的 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |

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

## <a name="parallel-copy-from-oracle"></a>從 Oracle 平行複製

Data Factory Oracle 連接器提供內建的資料分割，以平行方式從 Oracle 複製資料。 您可以在複製活動的 [ **來源** ] 索引標籤上找到資料分割選項。

![分割選項的螢幕擷取畫面](./media/connector-oracle/connector-oracle-partition-options.png)

當您啟用分割區複製時，Data Factory 會針對您的 Oracle 來源執行平行查詢，以依分割區載入資料。 平行程度由複製活動的 [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) 設定所控制。 例如，如果您設定 `parallelCopies` 為四，Data Factory 會同時產生並執行四個以指定的資料分割選項和設定為基礎的查詢，而每個查詢都會從您的 Oracle 資料庫中取出部分資料。

當您從 Oracle 資料庫載入大量資料時，建議您啟用資料分割的平行複製。 以下針對各種情節的建議設定。 將資料複製到以檔案為基礎的資料存放區時，建議分成多個檔案來寫入資料夾 (僅指定資料夾名稱)，這樣效能會比寫入單一檔案更好。

| 狀況                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 具有實體資料分割之大型資料表的完整載入。          | **分割區選項**：資料表的實體分割區。 <br><br/>在執行期間，Data Factory 會自動偵測實體分割區，並依分割區複製資料。 |
| 從大型資料表完整載入（沒有實體資料分割），同時使用整數資料行進行資料分割。 | **分割選項**：動態範圍分割。<br>**分割資料行**：指定用來分割資料的資料行。 如果未指定，則會使用主要索引鍵資料行。 |
| 使用自訂查詢（具有實體分割區）載入大量資料。 | **分割區選項**：資料表的實體分割區。<br>**查詢**：`SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`。<br>**磁碟分割名稱**：指定要從中複製資料的資料分割名稱 (s) 。 如果未指定，Data Factory 會自動偵測您在 Oracle 資料集中指定之資料表上的實體分割區。<br><br>在執行期間，Data Factory 會 `?AdfTabularPartitionName` 以實際的資料分割名稱取代，並傳送到 Oracle。 |
| 使用自訂查詢（沒有實體資料分割）載入大量資料，並使用資料分割的整數資料行。 | **分割選項**：動態範圍分割。<br>**查詢**：`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`。<br>**分割資料行**：指定用來分割資料的資料行。 您可以按照整數資料類型的資料行來分割。<br>**分割區上限** 和資料 **分割下限**：指定是否要針對資料分割資料行進行篩選，以便只在下限和上限之間取得資料。<br><br>在執行期間，Data Factory 會以 `?AdfRangePartitionColumnName` `?AdfRangePartitionUpbound` `?AdfRangePartitionLowbound` 每個資料分割的實際資料行名稱和值範圍取代、和，並傳送到 Oracle。 <br>例如，如果您將資料分割資料行 "ID" 的下限設為1，並將上限設為80，並將平行複本設定為4，Data Factory 會依4個數據分割抓取資料。 識別碼的範圍分別為 [1,20]、[21, 40]、[41, 60] 和 [61, 80]。 |

> [!TIP]
> 從非資料分割資料表複製資料時，您可以使用「動態範圍」資料分割選項來分割整數資料行。 如果您的來源資料沒有這類資料行類型，您可以利用來源查詢中的 [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) 函數來產生資料行，並使用它作為資料分割資料行。

**範例：使用實體分割區查詢**

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

當您從 Oracle 複製資料或將資料複製到該處時，會套用下列對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Oracle 資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(僅 Oracle 10g 及更高版本可支援) |
| CHAR |String |
| CLOB |字串 |
| DATE |Datetime |
| FLOAT |Decimal，字串 (如果精確度 > 28) |
| INTEGER |Decimal，字串 (如果精確度 > 28) |
| LONG |字串 |
| LONG RAW |Byte[] |
| NCHAR |字串 |
| NCLOB |字串 |
| NUMBER |Decimal，字串 (如果精確度 > 28) |
| NVARCHAR2 |字串 |
| RAW |Byte[] |
| ROWID |字串 |
| timestamp |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |字串 |
| TIMESTAMP WITH TIME ZONE |字串 |
| 不帶正負號的整數 |數字 |
| VARCHAR2 |字串 |
| XML |字串 |

> [!NOTE]
> 不支援 INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND 資料類型。

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
