---
title: 複製活動效能優化功能
description: 瞭解可協助您在 Azure Data Factory。中優化複製活動效能的主要功能
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
ms.date: 08/05/2020
ms.openlocfilehash: 45cecccd88b0b84b478bc6fc7346cb9ef9c2f454
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87846338"
---
# <a name="copy-activity-performance-optimization-features"></a>複製活動效能優化功能

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述可在 Azure Data Factory 中運用的複製活動效能優化功能。

## <a name="data-integration-units"></a>資料整合單位

資料整合單位是一種量值，代表 power (Azure Data Factory 中單一單位的 CPU、記憶體和網路資源分配) 組合。 資料整合單位僅適用于[Azure 整合運行](concepts-integration-runtime.md#azure-integration-runtime)時間，但不會套用至[自我裝載整合運行](concepts-integration-runtime.md#self-hosted-integration-runtime)時間。

允許複製活動執行的 Diu，是**介於2到256之間**。 如果未指定或在 UI 上選擇 [自動]，Data Factory 會根據您的來源接收配對和資料模式，動態套用最佳 DIU 設定。 下表列出不同複製案例中支援的 DIU 範圍和預設行為：

| 複製案例 | 支援的 DIU 範圍 | 服務決定的預設 DIU |
|:--- |:--- |---- |
| 在檔案存放區之間 |- **從或複製到單一**檔案：2-4 <br>- **從和複製到多個**檔案：2-256，視檔案的數目和大小而定 <br><br>例如，如果您從含有4個大型檔案的資料夾複製資料，並選擇保留階層，則最大有效 DIU 為 16;當您選擇合併檔案時，最大有效 DIU 為4。 |根據檔案的數目和大小，介於4到32之間 |
| 從檔案存放區到非檔案存放區 |- **從單一檔案複製**：2-4 <br/>- **從多個檔案複製**：2-256，視檔案的數目和大小而定 <br/><br/>例如，如果您從含有4個大型檔案的資料夾複製資料，最大有效 DIU 就是16。 |- **複製到 Azure SQL Database 或 Azure Cosmos DB**：介於4到16之間，視接收層 (dtu/ru) 和來源檔案模式而定<br>- 使用 PolyBase 或 COPY 語句來**複製到 Azure Synapse 分析**：2<br>-其他案例：4 |
| 從非檔案存放區到檔案存放區 |- **從已啟用分割選項的資料存放區複製** (包括[AZURE SQL DATABASE](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [Azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [azure Synapse 分析](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和[Teradata](connector-teradata.md#teradata-as-source)) ：2-256 寫入資料夾時，以及寫入至一個單一檔案時2-4。 請注意，每個來源資料分割最多可使用4個 Diu。<br>- **其他案例**：2-4 |- **從 REST 複製或 HTTP**：1<br/>- 使用 UNLOAD**從 Amazon Redshift 複製**：2<br>- **其他案例**：4 |
| 在非檔案存放區之間 |- **從已啟用分割選項的資料存放區複製** (包括[AZURE SQL DATABASE](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [Azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [azure Synapse 分析](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和[Teradata](connector-teradata.md#teradata-as-source)) ：2-256 寫入資料夾時，以及寫入至一個單一檔案時2-4。 請注意，每個來源資料分割最多可使用4個 Diu。<br/>- **其他案例**：2-4 |- **從 REST 複製或 HTTP**：1<br>- **其他案例**：4 |

您可以在 [複製活動監視] 視圖或 [活動輸出] 中看到用於每個複製執行的 Diu。 如需詳細資訊，請參閱[複製活動監視](copy-activity-monitoring.md)。 若要覆寫此預設值，請如下所示指定屬性的值 `dataIntegrationUnits` 。 根據您的資料模式，複製作業會在執行階段使用的 *實際 DIU 數目* 等於或小於所設定的值。

您將以 **# 個已使用的 diu \* 複製持續時間 \* 單位價格/DIU 小時**計費。 請在[這裡](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)查看目前的價格。 每個訂用帳戶類型可能會套用當地貨幣和個別折扣。

**範例︰**

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

如果您想要達到更高的輸送量，您可以擴充或相應放大自我裝載的 IR：

- 如果自我裝載 IR 節點上的 CPU 和可用記憶體未完全使用，但並行作業的執行達到限制，您應該增加可在節點上執行的並行作業數目來相應增加。  如需相關指示，請參閱[這裡](create-self-hosted-integration-runtime.md#scale-up)。
- 另一方面，如果自我裝載 IR 節點上的 CPU 很高，或可用記憶體不足，您可以加入新的節點，以協助相應放大多個節點上的負載。  如需相關指示，請參閱[這裡](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

請注意，在下列案例中，單一複製活動執行可以利用多個自我裝載的 IR 節點：

- 根據檔案的數目和大小，從以檔案為基礎的存放區複製資料。
- 從分割區啟用選項的資料存放區複製資料 ([包括 Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [azure Synapse 分析](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [SAP 開放式中樞](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、 [SAP 資料表](connector-sap-table.md#sap-table-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和[Teradata](connector-teradata.md#teradata-as-source)) ，視資料分割的數目而定。

## <a name="parallel-copy"></a>平行複製

您可以 `parallelCopies` 在複製活動) 設定平行複製 (屬性，以指出您想要複製活動使用的平行處理原則。 您可以將此屬性視為複製活動內的最大執行緒數目，這會從來源讀取或以平行方式寫入接收資料存放區。

平行複製正交于[資料整合單位](#data-integration-units)或[自我裝載 IR 節點](#self-hosted-integration-runtime-scalability)。 它會在所有 Diu 或自我裝載的 IR 節點上計算。

根據預設，針對每個複製活動執行，Azure Data Factory 會根據您的來源接收配對和資料模式，動態套用最佳的平行複製設定。 

> [!TIP]
> 平行複製的預設行為通常會提供最佳的輸送量，這是根據您的來源接收組、資料模式和 Diu 數目，或自我裝載 IR 的 CPU/記憶體/節點計數，由 ADF 自動決定。 請參閱針對何時微調平行複製[進行複製活動效能疑難排解](copy-activity-performance-troubleshooting.md)。

下表列出平行複製行為：

| 複製案例 | 平行複製行為 |
| --- | --- |
| 在檔案存放區之間 | `parallelCopies`決定檔案**層級**的平行處理原則。 每個檔案內的區塊處理會自動且透明地出現在下方。 其設計目的是針對指定的資料存放區類型，使用最適合的區塊大小，以平行方式載入資料。 <br/><br/>在執行時間，實際使用的平行複製活動數目不會超過您擁有的檔案數目。 如果複製行為**mergeFile**到檔案接收，複製活動就無法利用檔案層級的平行處理原則。 |
| 從檔案存放區到非檔案存放區 | -將資料複製到 Azure SQL Database 或 Azure Cosmos DB 時，預設的平行複製也取決於接收層 (Dtu/ru) 的數目。<br>-將資料複製到 Azure 資料表時，預設的平行複製會是4。 |
| 從非檔案存放區到檔案存放區 | -從分割區啟用選項的資料存放 (區（包括[Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [azure Synapse 分析](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [SAP 開放式中樞](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、 [sap 資料表](connector-sap-table.md#sap-table-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和[Teradata](connector-teradata.md#teradata-as-source) ）複製資料時，預設平行複製為4。 在執行時間，實際使用的平行複製活動數目不會超過您擁有的資料分割數目。 當您使用自我裝載的 Integration Runtime 並複製到 Azure Blob/ADLS Gen2 時，請注意每個 IR 節點的最大有效平行複本是4或5。<br>-針對其他案例，平行複製不會生效。 即使已指定平行處理原則，也不會套用。 |
| 在非檔案存放區之間 | -將資料複製到 Azure SQL Database 或 Azure Cosmos DB 時，預設的平行複製也取決於接收層 (Dtu/ru) 的數目。<br/>-從分割區啟用選項的資料存放 (區（包括[Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、 [azure SQL 受控執行個體](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、 [azure Synapse 分析](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [SAP 開放式中樞](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、 [sap 資料表](connector-sap-table.md#sap-table-as-source)、 [SQL Server](connector-sql-server.md#sql-server-as-a-source)和[Teradata](connector-teradata.md#teradata-as-source) ）複製資料時，預設平行複製為4。<br>-將資料複製到 Azure 資料表時，預設的平行複製會是4。 |

若要控制裝載資料存放區之電腦上的負載，或調整複製效能，您可以覆寫預設值並指定屬性的值 `parallelCopies` 。 值必須是大於或等於 1 的整數。 在執行時間，為了達到最佳效能，複製活動會使用小於或等於您所設定之值的值。

當您指定屬性的值時 `parallelCopies` ，會將來源和接收資料存放區的負載增加納入考慮。 也請考慮將負載增加至自我裝載整合執行時間（如果複製活動由它所擁有）。 當您對相同的資料存放區執行相同活動的多個活動或並存執行時，就會發生這種負載增加的情況。 如果您注意到資料存放區或自我裝載整合執行時間已負擔負載，請減少此 `parallelCopies` 值以減輕負載。

**範例︰**

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

從來源資料存放區將資料複製到接收資料存放區時，您可以選擇使用 Blob 儲存體做為過渡暫存存放區。 暫存在下列情況下特別有用︰

- **您想要將資料從各種資料存放區內嵌到 Azure Synapse 分析， (先前已透過 PolyBase SQL 資料倉儲) 。** Azure Synapse 分析會使用 PolyBase 做為高輸送量的機制，將大量資料載入 Azure Synapse 分析。 來源資料必須位於 Blob 儲存體或 Azure Data Lake 存放區中，而且必須符合其他準則。 當您從 Blob 儲存體或 Azure Data Lake Store 以外的資料存放區載入資料時，您可以啟用透過過渡暫存 Blob 儲存體的資料複製。 在此情況下，Azure Data Factory 會執行必要的資料轉換，以確保它符合 PolyBase 的需求。 然後，它會使用 PolyBase 有效率地將資料載入 Azure Synapse 分析。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有時候會花一些時間執行混合式資料移動 (也就是從內部部署資料存放區複製到雲端資料存放區，) 透過低速網路連線。** 若要改善效能，您可以使用分段複製來壓縮內部部署資料，以便將資料移至雲端中的暫存資料存放區所需的時間較短。 接著，您可以在載入至目的地資料存放區之前，先解壓縮臨時存儲區中的資料。
- **您不想要在防火牆中開啟埠80和埠443以外的通訊埠，因為公司的 IT 原則。** 例如，當您將資料從內部部署資料存放區複製到 Azure SQL Database 接收或 Azure Synapse 分析接收時，您必須在 Windows 防火牆和公司防火牆的埠1433上啟用輸出 TCP 通訊。 在此案例中，分段複製可以利用自我裝載整合執行時間，先透過 HTTP 或 HTTPS 在埠443上將資料複製到 Blob 儲存體暫存實例。 然後，它可以將資料從 Blob 儲存體暫存載入 SQL Database 或 Azure Synapse 分析。 在此流程中，您不需要啟用連接埠 1433。

### <a name="how-staged-copy-works"></a>分段複製的運作方式

當您啟用暫存功能時，會先從來源資料存放區複製資料到暫存 Blob 儲存體 (自備)。 接著再從暫存資料存放區複製資料到接收資料存放區。 Azure Data Factory 會自動為您管理兩階段流程。 在資料移動完成後，Azure Data Factory 也會清除暫存儲存體中的臨時資料。

![分段複製](media/copy-activity-performance/staged-copy.png)

當您使用暫存存放區啟用資料移動時，可以指定是否要在將資料從來源資料存放區移至過渡或暫存資料存放區之前先壓縮資料，然後再將資料從過渡或暫存資料存放區移到接收資料存放區，然後再進行解壓縮。

目前，您無法在透過不同自我裝載的 IRs 連線的兩個數據存放區之間複製資料，不論是否有分段複製也不會。 針對這種情況，您可以設定兩個明確連結的複製活動，從來源複製到預備環境，然後從暫存到接收。

### <a name="configuration"></a>組態

設定 [複製] 活動中的 [ **enableStaging** ] 設定，指定在將資料載入目的地資料存放區之前，是否要在 Blob 儲存體中暫存資料。 當您將**enableStaging**設定為時 `TRUE` ，請指定下表所列的其他屬性。 您也需要建立 Azure 儲存體或儲存體共用存取簽章連結服務，以供暫存（如果您沒有的話）。

| 屬性 | 描述 | 預設值 | 必要 |
| --- | --- | --- | --- |
| enableStaging |指定您是否要透過過渡暫存存放區複製資料。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 連結服務的名稱，以代表您用來做為過渡暫存存放區的儲存體執行個體。 <br/><br/> 您無法使用具有共用存取簽章的儲存體，透過 PolyBase 將資料載入至 Azure Synapse 分析。 您可以將它用於其他所有案例。 |N/A |是，當 **enableStaging** 設為 TRUE |
| path |指定要包含分段資料的 Blob 儲存體路徑。 如果您未提供路徑，服務會建立容器來儲存暫存資料。 <br/><br/> 只有在使用具有共用存取簽章的儲存體時，或需要讓暫存資料位於特定位置時，才指定路徑。 |N/A |否 |
| enableCompression |指定是否應該先壓縮資料，再將它複製到目的地。 此設定可減少傳輸的資料量。 |False |否 |

>[!NOTE]
> 如果您在啟用壓縮的情況下使用分段複製，則不支援暫存 blob 連結服務的服務主體或 MSI 驗證。

以下是複製活動的範例定義，其中包含上表中所述的屬性：

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>分段複製的計費影響

您會根據兩個步驟向您收費：複製持續時間和複製類型。

* 當您在雲端複製期間使用暫存時，如果將資料從雲端資料存放區複製到另一個雲端資料存放區（這兩個階段都是由 Azure integration runtime 所授權），您就會向您收取 [步驟1和步驟2的複製持續時間總和] x [雲端複製單位價格]。
* 當您在混合式複製期間使用暫存時，會將資料從內部部署資料存放區複製到雲端資料存放區，由自我裝載整合執行時間所授權的一個階段，您需支付 [混合式複製持續時間] x [混合式複製單位價格] + [雲端複製持續時間] x [雲端複製單位價格]。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動效能和擴充性指南](copy-activity-performance.md)
- [針對複製活動效能進行疑難排解](copy-activity-performance-troubleshooting.md)
- [使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)