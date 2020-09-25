---
title: 複製活動效能優化功能
description: 深入瞭解可協助您在 Azure Data Factory。中優化複製活動效能的主要功能
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/24/2020
ms.openlocfilehash: 8e46e9b323657b747fd73bad3b25ed66390f3aa9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324326"
---
# <a name="copy-activity-performance-optimization-features"></a>複製活動效能優化功能

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述您可在 Azure Data Factory 中運用的複製活動效能優化功能。

## <a name="data-integration-units"></a>資料整合單位

資料整合單位是一種量值，表示 power (Azure Data Factory 中單一單位的 CPU、記憶體和網路資源分配) 的組合。 資料整合單位只適用于 [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime)，但不適用 [自我裝載的整合運行](concepts-integration-runtime.md#self-hosted-integration-runtime)時間。

允許複製活動執行的 Diu， **介於2到256之間**。 如果未指定，或您在 UI 上選擇 [自動]，Data Factory 會根據您的來源接收配對和資料模式，動態套用最佳的 DIU 設定。 下表列出不同複製案例中支援的 DIU 範圍和預設行為：

| 複製案例 | 支援的 DIU 範圍 | 服務決定的預設 DIU |
|:--- |:--- |---- |
| 檔案存放區之間 |- **從單一檔案複製或複製到單一**檔案：2-4 <br>- **從或複製到多個**檔案：2-256，視檔案的數目和大小而定 <br><br>例如，如果您從含有4個大型檔案的資料夾複製資料，並選擇保留階層，則最大有效 DIU 為 16;當您選擇合併檔案時，最大有效 DIU 為4。 |介於4到32之間，視檔案的數目和大小而定 |
| 從檔案存放區至非檔案存放區 |- **從單一檔案複製**：2-4 <br/>- **從多個檔案複製**：2-256，視檔案的數目和大小而定 <br/><br/>例如，如果您從含有4個大型檔案的資料夾複製資料，則最大有效 DIU 為16。 |- **複製到 Azure SQL Database 或 Azure Cosmos DB**：在4和16之間，視接收層 (dtu/ru) 和來源檔案模式而定<br>- 使用 PolyBase 或 COPY 語句**複製到 Azure Synapse Analytics** ：2<br>-其他案例：4 |
| 從非檔案存放區到檔案存放區 |- **從分割區啟用選項的資料存放區進行複製** (包括 [AZURE SQL DATABASE](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [Azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和 [Teradata](connector-teradata.md#teradata-as-source)) ：2-256 寫入至資料夾，以及在寫入至一個單一檔案時使用2-4。 請注意，每個來源資料分割可以使用最多4個 Diu。<br>- **其他案例**：2-4 |- **從 REST 複製或 HTTP**：1<br/>- 使用 UNLOAD**從 Amazon Redshift 複製**：2<br>- **其他案例**：4 |
| 在非檔案存放區之間 |- **從分割區啟用選項的資料存放區進行複製** (包括 [AZURE SQL DATABASE](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [Azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和 [Teradata](connector-teradata.md#teradata-as-source)) ：2-256 寫入至資料夾，以及在寫入至一個單一檔案時使用2-4。 請注意，每個來源資料分割可以使用最多4個 Diu。<br/>- **其他案例**：2-4 |- **從 REST 複製或 HTTP**：1<br>- **其他案例**：4 |

您可以在「複製活動監視」或「活動」輸出中，看到每個複製執行所使用的 Diu。 如需詳細資訊，請參閱 [複製活動監視](copy-activity-monitoring.md)。 若要覆寫此預設值，請指定屬性的值，如下所 `dataIntegrationUnits` 示。 根據您的資料模式，複製作業會在執行階段使用的 *實際 DIU 數目* 等於或小於所設定的值。

您將會被收取 **# 個已使用的 diu \* 複製持續時間 \* 單位價格/DIU 小時**。 請參閱 [此處](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)的目前價格。 每一訂用帳戶類型可能會套用本地貨幣和個別折扣。

**範例：**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>自我裝載整合執行時間的擴充性

如果您想要達到更高的輸送量，您可以擴大或相應放大自我裝載的 IR：

- 如果未充分利用自我裝載 IR 節點上的 CPU 和可用記憶體，但是並行作業的執行達到限制，您應該增加節點上可執行檔並行作業數目來擴大規模。  請參閱 [這裡](create-self-hosted-integration-runtime.md#scale-up) 以取得相關指示。
- 另一方面，在自我裝載 IR 節點或可用記憶體偏低的情況下，您可以新增節點，以協助在多個節點之間擴充負載。  請參閱 [這裡](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 以取得相關指示。

請注意，在下列案例中，單一複製活動執行可以利用多個自我裝載的 IR 節點：

- 從檔案型存放區複製資料，視檔案的數目和大小而定。
- 從分割區選項的資料存放區複製資料， (包括 [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [Azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、 [sap Table](connector-sap-table.md#sap-table-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和 [Teradata](connector-teradata.md#teradata-as-source)) （視資料分割數目而定）。

## <a name="parallel-copy"></a>平行複製

您可以設定複製活動) 的 [平行複製] (`parallelCopies` 屬性，以指出您希望複製活動使用的平行處理原則。 您可以將此屬性視為複製活動中從來源讀取或平行寫入至接收資料存放區的執行緒數目上限。

平行複製會與 [資料整合單位](#data-integration-units) 或自我裝載 [IR 節點](#self-hosted-integration-runtime-scalability)並存。 它會在所有 Diu 或自我裝載的 IR 節點上進行計算。

針對每個複製活動執行，根據預設 Azure Data Factory 會根據您的來源接收配對和資料模式，動態套用最佳的平行複製設定。 

> [!TIP]
> 平行複製的預設行為通常會為您提供最佳的輸送量，這會根據您的來源接收配對、資料模式和 Diu 數目或自我裝載 IR 的 CPU/記憶體/節點計數，自動由 ADF 決定。 請參閱 [疑難排解複製活動效能](copy-activity-performance-troubleshooting.md) 的時機，以微調平行複製。

下表列出平行複製行為：

| 複製案例 | 平行複製行為 |
| --- | --- |
| 檔案存放區之間 | `parallelCopies` 決定檔案 **層級**的平行處理原則。 每個檔案內的區塊處理會自動且明確地出現在下方。 其設計目的是針對指定的資料存放區類型使用最適合的區塊大小，以平行方式載入資料。 <br/><br/>在執行時間，複製活動所使用的實際平行複製數目不會超過您擁有的檔案數目。 如果將複製行為 **>mergefile** 至檔案接收，複製活動就無法利用檔案層級的平行處理原則。 |
| 從檔案存放區至非檔案存放區 | -將資料複製到 Azure SQL Database 或 Azure Cosmos DB 時，預設的平行複製也取決於接收層 (Dtu/ru) 數目。<br>-將資料複製到 Azure 資料表時，預設的平行複製為4。 |
| 從非檔案存放區到檔案存放區 | -從分割區啟用選項的資料存放區複製資料時 (包括 [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [Azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、 [sap Table](connector-sap-table.md#sap-table-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和 [Teradata](connector-teradata.md#teradata-as-source)) ，預設平行複製為4。 在執行時間，複製活動所使用的實際平行複製數目不會超過您擁有的資料分割數目。 使用自我裝載的 Integration Runtime 並複製到 Azure Blob/ADLS Gen2 時，請注意每個 IR 節點的最大有效平行複本是4或5。<br>-在其他案例中，平行複製不會生效。 即使已指定平行處理原則，也不會套用。 |
| 在非檔案存放區之間 | -將資料複製到 Azure SQL Database 或 Azure Cosmos DB 時，預設的平行複製也取決於接收層 (Dtu/ru) 數目。<br/>-從分割區啟用選項的資料存放區複製資料時 (包括 [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [Azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、 [sap Table](connector-sap-table.md#sap-table-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和 [Teradata](connector-teradata.md#teradata-as-source)) ，預設平行複製為4。<br>-將資料複製到 Azure 資料表時，預設的平行複製為4。 |

若要控制裝載資料存放區之電腦上的負載，或調整複製效能，您可以覆寫預設值並指定屬性的值 `parallelCopies` 。 值必須是大於或等於 1 的整數。 在執行時間，為了獲得最佳效能，複製活動會使用小於或等於您設定之值的值。

當您指定屬性的值時 `parallelCopies` ，請將來源和接收資料存放區的負載增加到帳戶。 此外，如果複製活動是由自我裝載整合執行時間所提升，也請考慮將其提升至自我裝載的整合執行時間。 當您對相同的資料存放區執行相同活動的多個活動或並存執行時，就會發生這種負載增加。 如果您注意到資料存放區或自我裝載整合執行時間在負載上感到不知所措，請減少此 `parallelCopies` 值以減輕負載。

**範例：**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>分段複製

當您將資料從來源資料存放區複製到接收資料存放區時，您可以選擇使用 Azure Blob 儲存體或 Azure Data Lake Storage Gen2 作為過渡暫存存放區。 暫存在下列情況下特別有用︰

- **您想要從各種資料存放區將資料內嵌至 Azure Synapse Analytics (先前的 SQL 資料倉儲) via PolyBase、從 gen2 複製資料，或從 Amazon Redshift/HDFS 內嵌資料。** 深入瞭解詳細資料：
  - [使用 PolyBase 將資料載入 Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics)。
  - [雪花連接器](connector-snowflake.md)
  - [Amazon Redshift 連接器](connector-amazon-redshift.md)
  - [HDFS 連接器](connector-hdfs.md)
- **您不想要在防火牆中開啟埠80和埠443以外的埠，因為公司的 IT 原則。** 例如，當您從內部部署資料存放區將資料複製到 Azure SQL Database 或 Azure Synapse Analytics 時，您需要針對 Windows 防火牆和公司防火牆啟用埠1433上的輸出 TCP 通訊。 在此案例中，分段複製可利用自我裝載整合執行時間，先透過 HTTP 或 HTTPS 在埠443上將資料複製到暫存儲存體，然後將資料從暫存載入 SQL Database 或 Azure Synapse Analytics。 在此流程中，您不需要啟用連接埠 1433。
- **有時候，執行混合式資料移動需要一些時間， (也就是從內部部署資料存放區複製到雲端資料存放區，) 透過慢速網路連接。** 若要改善效能，您可以使用分段複製來壓縮內部部署資料，以便將資料移至雲端中的暫存資料存放區所需的時間較短。 然後，您可以在載入至目的地資料存放區之前，先解壓縮暫存存放區中的資料。

### <a name="how-staged-copy-works"></a>分段複製的運作方式

當您啟用暫存功能時，首先會將資料從來源資料存放區複製到暫存儲存體， (攜帶您自己的 Azure Blob 或 Azure Data Lake Storage Gen2) 。 接下來，會將資料從暫存複製到接收資料存放區。 Azure Data Factory 複製活動會自動為您管理兩階段流程，而且在資料移動完成之後，也會清除暫存儲存體中的暫存資料。

![分段複製](media/copy-activity-performance/staged-copy.png)

當您使用暫存存放區來啟動資料移動時，您可以指定是否要壓縮資料，然後再將資料從來源資料存放區移至暫存存放區，然後在將資料從過渡或暫存資料存放區移到接收資料存放區之前解壓縮。

目前，您無法在透過不同自我裝載的 IRs 連接的兩個數據存放區之間複製資料，兩者都不具有分段複製。 在這種情況下，您可以設定兩個明確連結的複製活動，從來源複製到預備環境，然後從預備環境複製到接收。

### <a name="configuration"></a>設定

在複製活動中設定 **>enablestaging** 設定，以指定是否要在將資料載入至目的地資料存放區之前，先在儲存體中暫存資料。 當您將 **>enablestaging** 設定為時 `TRUE` ，請指定下表所列的其他屬性。 

| 屬性 | 描述 | 預設值 | 必要 |
| --- | --- | --- | --- |
| enableStaging |指定您是否要透過過渡暫存存放區複製資料。 |False |否 |
| linkedServiceName |指定 [Azure Blob 儲存體](connector-azure-blob-storage.md#linked-service-properties) 或 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) 連結服務的名稱，這是指您用來做為過渡暫存存放區的儲存體實例。 |N/A |是，當 **enableStaging** 設為 TRUE |
| path |指定您想要包含暫存資料的路徑。 如果您未提供路徑，服務會建立一個容器來儲存暫存資料。 |N/A |否 |
| enableCompression |指定是否應該先壓縮資料，再將資料複製到目的地。 此設定可減少傳輸的資料量。 |False |否 |

>[!NOTE]
> 如果您使用已啟用壓縮的分段複製，則不支援暫存 blob 連結服務的服務主體或 MSI 驗證。

以下是複製活動的範例定義，其中包含上表所述的屬性：

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>分段複製的計費影響

您會根據兩個步驟來收費：複製持續時間和複製類型。

* 當您在雲端複製期間使用暫存時（這會將資料從雲端資料存放區複製到另一個雲端資料存放區），Azure integration runtime 所提供的兩個階段都會向您收取 [步驟1和步驟2的複製持續時間總和] x [雲端複製單位價格]。
* 當您在混合式複製期間使用暫存時（從內部部署資料存放區將資料複製到雲端資料存放區），由自我裝載整合執行時間所提供的一個階段，您需支付 [混合式複製持續時間] x [混合式複製單位價格] + [雲端複製持續時間] x [雲端複製單位價格]。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動效能和擴充性指南](copy-activity-performance.md)
- [針對複製活動效能進行疑難排解](copy-activity-performance-troubleshooting.md)
- [使用 Azure Data Factory 將資料從 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)