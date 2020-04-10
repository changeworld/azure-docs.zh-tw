---
title: 使用 Azure 資料工廠將資料複製到 Oracle 和從 Oracle
description: 瞭解如何使用資料工廠將資料從受支援的源存儲複製到 Oracle 資料庫或從 Oracle 複製到支援的接收體記憶體儲存。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: jingwang
ms.openlocfilehash: 534e5c913685eeac92022f6694ea31b24816da5d
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011645"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>使用 Azure Data Factory 從 Oracle 複製資料及將資料複製到該處
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [版本 1](v1/data-factory-onprem-oracle-connector.md)
> * [目前版本](connector-oracle.md)

本文概述了如何使用 Azure 資料工廠中的複製活動將數據從 Oracle 資料庫複製到 Oracle 資料庫。 複製[活動概述](copy-activity-overview.md)。

## <a name="supported-capabilities"></a>支援的功能

此 Oracle 連線器支援用於以下活動:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)

您可以將資料從 Oracle 資料庫複製到任何支援的接收資料存放區。 您也可以從任何支援的來源資料存放區將資料複製到 Oracle 資料庫。 有關複製活動支援為源或接收體的清單,請參閱[支援資料儲存](copy-activity-overview.md#supported-data-stores-and-formats)表。

具體而言,此 Oracle 連接器支援:

- 以下版本的 Oracle 資料庫:
    - 甲骨文 19c R1 (19.1) 及以上
    - 甲骨文 18c R1 (18.1) 及以上
    - 甲骨文 12c R1 (12.1) 及以上
    - 甲骨文 11g R1 (11.1) 及以上
    - 甲骨文 10g R1 (10.1) 及以上
    - 甲骨文 9i R2 (9.2) 及以上
    - 甲骨文 8i R3 (8.1.7) 及以上
    - Oracle 資料庫雲 Exadata 服務
- 從 Oracle 源並行複製。 有關詳細資訊,請參閱 Oracle 部分[的行副本](#parallel-copy-from-oracle)。

> [!Note]
> 不支援 Oracle Proxy 伺服器。

## <a name="prerequisites"></a>Prerequisites

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

整合執行階段提供內建的 Oracle 驅動程式。 因此，當您從 Oracle 複製資料或將資料複製到該處時，不需要手動安裝驅動程式。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Oracle 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

Oracle 連結服務支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為**Oracle**。 | 是 |
| connectionString | 指定連線到 Oracle 資料庫執行個體所需的資訊。 <br/>您還可以在 Azure 密鑰保管庫中放置密碼,然後`password`從連接 字串中提取配置。 有關詳細資訊,請參閱以下範例並在[Azure 金鑰保管庫中儲存認證](store-credentials-in-key-vault.md)。 <br><br>**支援的連線類型**：您可以使用 [Oracle SID]**** 或 [Oracle 服務名稱]**** 來識別您的資料庫：<br>- 如果您使用 SID：`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 如果您使用服務名稱：`Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>對於進階 Oracle 本機連線選項,您可以選擇在 TNSNAMES 中新增項目[。Oracle](http://www.orafaq.com/wiki/Tnsnames.ora)伺服器上的 ORA 檔案以及 ADF Oracle 連結服務中,選擇使用 Oracle 服務名稱連接類型並配置相應的服務名稱。 | 是 |
| connectVia | 連線到資料儲存的[整合時](concepts-integration-runtime.md)。 從[先決條件](#prerequisites)部分瞭解更多資訊。 如果未指定，則會使用預設的 Azure Integration Runtime。 |否 |

>[!TIP]
>如果出現錯誤,"ORA-01025:UPI參數範圍外",並且您的 Oracle 版本為 8i,`WireProtocolMode=1`請添加到 連接字串中。 然後再試一次。

每個案例在連接字串中可以設定的更多連線屬性:

| 屬性 | 描述 | 允許的值 |
|:--- |:--- |:--- |
| ArraySize |連接器可以在單個網路往返行程中獲取的位元組數。 例如, `ArraySize=‭10485760‬`.<br/><br/>較大的值通過減少通過網路獲取數據的次數來提高輸送量。 值越小會增加回應時間,因為等待伺服器傳輸數據的延遲更少。 | 從 1 到 4294967296 (4 GB) 的整數。 預設值為 `60000`。 值 1 不定義位元組數,但指示為一行數據分配空間。 |

若要啟用 Oracle 連線加密，您有兩個選項：

-   要在 Oracle 伺服器端使用**三重 DES 加密 (3DES) 和進階加密標準 (AES),** 請轉到 Oracle 進階安全 (OAS) 並設定加密設定。 有關詳細資訊,請參閱此[Oracle 文件](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)。 Oracle 應用程式開發框架 (ADF) 連接器自動協商加密方法,以在建立與 Oracle 的連線時使用您在 OAS 中設定的加密方法。

-   要使用**TLS**:

    1.  獲取 TLS/SSL 憑證資訊。 取得 TLS/SSL 憑證的可分辨編碼規則 (DER) 編碼的憑證資訊,並儲存輸出(-----開始憑證... End Certificate -----) 儲存為文字檔。

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **範例:** 從 DERcert.cer 中提取憑證資訊,然後將輸出保存到 cert.txt。

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
    
    2.  產生`keystore`或`truststore`。 以下命令以 PKCS-12 格式創建帶密碼或`truststore`沒有密碼的檔。

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **範例:** 使用密碼創建名為 MyTrustStoreFile 的 PKCS12`truststore`檔。

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  將`truststore`檔案放在自承載的 IR 電腦上。 例如,將檔放在 C:\MyTrustStoreFile。
    4.  在 Azure 資料`EncryptionMethod=1`工廠中 ,`TrustStore`/`TrustStorePassword`使用和設定 Oracle 連接字串。 例如： `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>` 。

**範例:**

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

本節提供 Oracle 資料集所支援的屬性清單。 有關可用於定義資料集的節和屬性的完整清單,請參閱[資料集](concepts-datasets-linked-services.md)。 

要將資料從 Oracle 複製到 Oracle,請將資料集的類型`OracleTable`屬性設定為 。 以下是支援的屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為`OracleTable`。 | 是 |
| 結構描述 | 結構描述的名稱。 |否 (來源)；是 (接收)  |
| 資料表 | 表/視圖的名稱。 |否 (來源)；是 (接收)  |
| tableName | 具有架構的表/視圖的名稱。 此屬性支援向後相容性。 對新工作負載,請使用`schema``table`與 。 | 否 (來源)；是 (接收) |

**範例:**

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
>要透過使用資料分割區有效地從 Oracle 載入資料,[請從 Oracle 的並行副本](#parallel-copy-from-oracle)中學習詳細資訊。

要從 Oracle 複製資料,請將複製活動中的來源類型`OracleSource`設定為 。 複製活動的 [來源]**** 區段支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動來源的類型屬性設定為`OracleSource`。 | 是 |
| oracleReaderQuery | 使用自訂 SQL 查詢來讀取資料。 例如 `"SELECT * FROM MyTable"`。<br>啟用分區載入時,需要在查詢中掛鉤任何相應的內置分區參數。 有關範例,請參閱[Oracle 部分的並行副本](#parallel-copy-from-oracle)。 | 否 |
| 分割區選項 | 指定用於從 Oracle 載入資料的資料分區選項。 <br>允許的值是:**沒有**(預設,**物理分割區表**與**動態範圍**。<br>啟用分區選項(即不是`None`),從 Oracle 資料庫同時載入資料的並行性程度由[`parallelCopies`](copy-activity-performance-features.md#parallel-copy)複製活動上的 設置控制。 | 否 |
| 分割區設定 | 指定資料分割區設定的群組。 <br>當分區選項不是`None`時應用。 | 否 |
| 區名稱 | 需要複製的物理分區的清單。 <br>當分區選項為`PhysicalPartitionsOfTable`時應用。 如果使用查詢檢索源數據,請挂`?AdfTabularPartitionName`鉤 WHERE 子句。 例如,請參閱 Oracle 部份[的行複本](#parallel-copy-from-oracle)。 | 否 |
| 分割區列名稱 | **以整數類型**指定源列的名稱,該類型將由並行複製的範圍分區使用。 如果未指定,則表的主鍵將自動檢測並用作分區列。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索源數據,請挂`?AdfRangePartitionColumnName`鉤 WHERE 子句。 例如,請參閱 Oracle 部份[的行複本](#parallel-copy-from-oracle)。 | 否 |
| 分割區上邊界 | 要將資料複製出去的分區列的最大值。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索源數據,請挂`?AdfRangePartitionUpbound`鉤 WHERE 子句。 例如,請參閱 Oracle 部份[的行複本](#parallel-copy-from-oracle)。 | 否 |
| 分割區下限 | 要將資料複製出去的分區列的最小值。 <br>當分區選項為`DynamicRange`時應用。 如果使用查詢檢索源數據,請挂`?AdfRangePartitionLowbound`鉤 WHERE 子句。 例如,請參閱 Oracle 部份[的行複本](#parallel-copy-from-oracle)。 | 否 |

**範例:使用沒有分割區的基本查詢複製資料**

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

要複製到 Oracle,請將複製活動中的接收器類型設定為`OracleSink`。 複製活動的 [接收]**** 區段支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動接收器的類型屬性設定為`OracleSink`。 | 是 |
| writeBatchSize | 當緩衝區大小達到`writeBatchSize`時,將數據插入到 SQL 表中。<br/>允許的值為整數 (資料列數目)。 |否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br/>允許的值為時間範圍。 範例是 00:30:00 (30 分鐘)。 | 否 |
| preCopyScript | 為每次運行將資料寫入 Oracle 之前要執行的複製活動指定 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |

**範例:**

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

## <a name="parallel-copy-from-oracle"></a>來自 Oracle 的行複本

數據工廠 Oracle 連接器提供內建資料分割區,用於並行複製來自 Oracle 的數據。 您可以在複製活動的 **「源」** 選項卡上找到資料分區選項。

![分割區選項的螢幕擷取](./media/connector-oracle/connector-oracle-partition-options.png)

啟用分區副本時,數據工廠會針對 Oracle 源運行並行查詢,以便按分區載入資料。 並行度由複製活動上的[`parallelCopies`](copy-activity-performance-features.md#parallel-copy)設置控制。 例如,如果將設置為`parallelCopies`4,則數據工廠會根據指定的分區選項和設置同時生成和運行四個查詢,並且每個查詢從 Oracle 資料庫中檢索一部分數據。

建議使用數據分區啟用並行複製,尤其是在從 Oracle 資料庫載入大量資料時。 以下是針對不同方案的建議配置。 將資料複製到基於檔案的資料儲存中時,將重新命令以多個檔案形式寫入資料夾(僅指定資料夾名稱),在這種情況下,性能優於寫入單個檔。

| 狀況                                                     | 建議的設定                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 來自大表的滿載,帶物理分區。          | **分區選項**:表的物理分區。 <br><br/>在執行期間,數據工廠會自動檢測物理分區,並按分區複製數據。 |
| 從大表滿載,沒有物理分區,同時帶有用於數據分區的整數列。 | **分區選項**:動態範圍分區。<br>**分割區列**:指定用於對資料進行分割區的欄位。 如果未指定,則使用主鍵列。 |
| 使用具有物理分區的自定義查詢載入大量數據。 | **分區選項**:表的物理分區。<br>**查詢**`SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`: .<br>**分割區名稱**:指定要從中複製資料的分割區名稱。 如果未指定,資料工廠將自動檢測您在 Oracle 資料集中指定的表上的物理分割區。<br><br>在執行期間,資料工廠`?AdfTabularPartitionName`將替換為實際分區名稱,併發送到 Oracle。 |
| 使用自定義查詢載入大量資料,無需物理分區,同時使用用於數據分區的整數列。 | **分區選項**:動態範圍分區。<br>**查詢**`SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`: .<br>**分割區列**:指定用於對資料進行分割區的欄位。 您可以使用整數資料類型對列進行分區。<br>**分區上限**和**分區下限**:指定是否要針對分區列進行篩選,以便僅在下部和上部範圍之間檢索數據。<br><br>在執行期間,資料工廠`?AdfRangePartitionColumnName``?AdfRangePartitionUpbound``?AdfRangePartitionLowbound`將替換和替換為每個分區的實際列名稱和值範圍,然後發送到 Oracle。 <br>例如,如果分區列"ID"設置為下限為1,上限設置為80,並行副本設置為4,則數據工廠將數據按4個分區檢索數據。 他們的年號分別在[1,20]、[21、40]、[41、60]和[61、80]之間。 |

**範例:具有物理分割區的查詢**

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

**範例:具有動態範圍分割區的查詢**

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

當您從 Oracle 複製數據並將其複製到 Oracle 時,將應用以下映射。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

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

## <a name="lookup-activity-properties"></a>尋找活動屬性

要瞭解有關屬性的詳細資訊,請檢查[。](control-flow-lookup-activity.md)

## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
