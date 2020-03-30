---
title: 複製活動性能優化功能
description: 瞭解可説明您優化 Azure 資料工廠中的複製活動性能的關鍵功能。
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
ms.date: 03/09/2020
ms.openlocfilehash: d37b4648c0a37f16fe5c9d8794bd78417c5780ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257881"
---
# <a name="copy-activity-performance-optimization-features"></a>複製活動性能優化功能

本文概述了可在 Azure 資料工廠中利用的複製活動性能優化功能。

## <a name="data-integration-units"></a>資料整合單位

資料整合單元是表示 Azure 資料工廠中單個單元的功率（CPU、記憶體和網路資源分配的組合）的度量值。 資料整合單元僅適用于[Azure 集成運行時](concepts-integration-runtime.md#azure-integration-runtime)，但不應用於[自託管的集成運行時](concepts-integration-runtime.md#self-hosted-integration-runtime)。

允許的 DIA 授權複製活動運行介於**2 和 256 之間**。 如果未指定或在 UI 上選擇"自動"，資料工廠會根據您的源接收器對和資料模式動態應用最佳 DIU 設置。 下表列出了不同複製方案中支援的 DIU 範圍和預設行為：

| 複製案例 | 支援的 DIU 系列 | 服務決定的預設 DIU |
|:--- |:--- |---- |
| 檔存儲之間 |- **從檔案複製或複製到單個檔**：2-4 <br>- **從多個檔案複製和複製到多個檔**：2-256 取決於檔的數量和大小 <br><br>例如，如果從包含 4 個大型檔的資料夾中複製資料並選擇保留層次結構，則最大有效 DIU 為 16;如果從包含 4 個大型檔的資料夾中複製資料，則最大有效 DIU 為 16;如果從具有 4 個大型檔的資料夾中複製資料，則最大有效 DIU 為 16。當您選擇合併檔時，最大有效 DIU 為 4。 |4 到 32 之間，具體取決於檔的數量和大小 |
| 從檔存儲到非檔存儲 |- **從單個檔案複製**： 2-4 <br/>- **從多個檔案複製**： 2-256 取決於檔的數量和大小 <br/><br/>例如，如果從包含 4 個大型檔的資料夾中複製資料，則最大有效 DIU 為 16。 |- **複製到 Azure SQL 資料庫或 Azure Cosmos DB：** 根據接收器層 （DTUs/RUs） 和源檔案模式，在 4 和 16 之間<br>- 使用 PolyBase 或 COPY 語句**複製到 Azure 同步分析**：2<br>- 其他方案： 4 |
| 從非檔存儲到檔存儲 |- **從支援分區選項的資料存儲**（包括[Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata）](connector-teradata.md#teradata-as-source)複製：寫入資料夾時為 2-256，寫入單個檔時為 2-4。 請注意，每個來源資料分區最多可以使用 4 個 DIDI。<br>- **其他方案**： 2-4 |- **從 REST 或 HTTP 複製**： 1<br/>- 使用 UnLOAD**從亞馬遜紅移複製**： 2<br>- **其他方案**： 4 |
| 在非檔存儲之間 |- **從支援分區選項的資料存儲**（包括[Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata）](connector-teradata.md#teradata-as-source)複製：寫入資料夾時為 2-256，寫入單個檔時為 2-4。 請注意，每個來源資料分區最多可以使用 4 個 DIDI。<br/>- **其他方案**： 2-4 |- **從 REST 或 HTTP 複製**： 1<br>- **其他方案**： 4 |

您可以在複製活動監視視圖或活動輸出中查看用於每次運行副本的 DIU。 有關詳細資訊，請參閱[複製活動監視](copy-activity-monitoring.md)。 要覆蓋此預設值，請指定`dataIntegrationUnits`屬性的值，如下所示。 根據您的資料模式，複製作業會在執行階段使用的 *實際 DIU 數目* 等於或小於所設定的值。

您將被收取**二手DIU\*複製持續時間\*單價/DIU小時**。 在此處查看[當前價格。](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 本地貨幣和單獨的折扣可能適用于每個訂閱類型。

**例子：**

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

## <a name="self-hosted-integration-runtime-scalability"></a>自託管集成運行時可擴充性

如果要實現更高的輸送量，可以向上擴展或擴展自承載的 IR：

- 如果未充分利用自承載 IR 節點上的 CPU 和可用記憶體，但併發作業的執行達到限制，則應通過增加可在節點上運行的併發作業數來向上擴展。  有關說明，請參閱[此處](create-self-hosted-integration-runtime.md#scale-up)。
- 另一方面，如果自承載的 IR 節點上的 CPU 較高或可用記憶體不足，則可以添加新節點以説明跨多個節點擴展負載。  有關說明，請參閱[此處](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

請注意，在以下方案中，單個複製活動執行可以利用多個自承載的 IR 節點：

- 根據檔的數量和大小，從檔案為儲存基礎複製資料。
- 根據資料分區的數量，從支援分區選項的資料存儲（包括[Oracle、Netezza、Teradata、SAP](connector-netezza.md#netezza-as-source) [Oracle](connector-oracle.md#oracle-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和[SAP 開放集線器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）複製資料。 [Teradata](connector-teradata.md#teradata-as-source)

## <a name="parallel-copy"></a>平行複製

您可以在複製活動上設置並行`parallelCopies`複製 （屬性），以指示您希望複製活動使用的並行性。 可以將此屬性視為複製活動中從源讀取或並行寫入接收器資料存儲的最大執行緒數。

並行副本是資料[集成單元](#data-integration-units)或[自承載的 IR 節點的](#self-hosted-integration-runtime-scalability)正交。 它跨所有 DIA 或自承載的 IR 節點計數。

對於運行的每個複製活動，預設情況下，Azure 資料工廠會基於源接收器對和資料模式動態應用最佳並行複製設置。 

> [!TIP]
> 並行複製的預設行為通常為您提供最佳輸送量，這是 ADF 根據源接收器對、資料模式和 DIA 數或自承載 IR 的 CPU/記憶體/節點計數自動確定的。 請參閱在何時調整並行[副本時對複製活動性能進行故障排除](copy-activity-performance-troubleshooting.md)。

下表列出了並行複製行為：

| 複製案例 | 並行複製行為 |
| --- | --- |
| 檔存儲之間 | `parallelCopies`確定**檔級別的**並行性。 每個檔中的分塊自動和透明地發生在下面。 它旨在對給定資料存儲類型使用最佳合適的塊大小來並行載入資料。 <br/><br/>活動在運行時使用的並行副本活動的實際數量不超過您擁有的檔數。 如果複製行為將**File合併**到檔接收器中，則複製活動無法利用檔級並行性。 |
| 從檔存儲到非檔存儲 | - 將資料複製到 Azure SQL 資料庫或 Azure Cosmos DB 時，預設並行副本還取決於接收器層（DTUs/RUs 的數量）。<br>- 將資料複製到 Azure 表時，預設並行副本為 4。 |
| 從非檔存儲到檔存儲 | - 從支援分區選項的資料存儲（包括[Oracle](connector-oracle.md#oracle-as-source)Oracle、Netezza、Teradata、SAP [Netezza](connector-netezza.md#netezza-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和 SAP[開放集線器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）複製資料時，預設並行複製為 4。 [Teradata](connector-teradata.md#teradata-as-source) 運行時使用的並行副本活動的實際數量不超過您擁有的資料分區數。 當使用自託管集成運行時並複製到 Azure Blob/ADLS Gen2 時，請注意最大有效並行副本是每個 IR 節點 4 或 5。<br>- 對於其他方案，並行副本不會生效。 即使指定了並行性，也不會應用它。 |
| 在非檔存儲之間 | - 將資料複製到 Azure SQL 資料庫或 Azure Cosmos DB 時，預設並行副本還取決於接收器層（DTUs/RUs 的數量）。<br/>- 從支援分區選項的資料存儲（包括[Oracle](connector-oracle.md#oracle-as-source)Oracle、Netezza、Teradata、SAP [Netezza](connector-netezza.md#netezza-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和 SAP[開放集線器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)）複製資料時，預設並行複製為 4。 [Teradata](connector-teradata.md#teradata-as-source)<br>- 將資料複製到 Azure 表時，預設並行副本為 4。 |

要控制託管資料存儲的電腦上負載，或調整複製性能，可以重寫預設值並為屬性`parallelCopies`指定值。 值必須是大於或等於 1 的整數。 在運行時，為了獲得最佳性能，複製活動使用的值小於或等於您設置的值。

指定`parallelCopies`屬性的值時，將源和接收器資料存儲的負載增加考慮在內。 如果複製活動受其授權，請考慮對自託管集成運行時的負載增加。 此負載增加發生，尤其是在有多個活動或同一活動對同一資料存儲上運行時。 如果您注意到資料存儲或自託管集成運行時因負載而不堪重負，請減小`parallelCopies`該值以釋放負載。

**例子：**

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

- **您希望通過 PolyBase 將資料從各種資料存儲引入 SQL 資料倉儲。** SQL 資料倉儲使用 PolyBase 做為高輸送量機制，將大量資料載入 SQL 資料倉儲。 來源資料必須位於 Blob 存儲或 Azure 資料湖存儲中，並且必須滿足其他條件。 當您從 Blob 儲存體或 Azure Data Lake Store 以外的資料存放區載入資料時，您可以啟用透過過渡暫存 Blob 儲存體的資料複製。 在這種情況下，Azure 資料工廠執行所需的資料轉換，以確保它滿足 PolyBase 的要求。 然後，它會使用 PolyBase 將資料有效率地載入到 SQL 資料倉儲。 如需詳細資訊，請參閱[使用 PolyBase 將資料載入 Azure SQL 資料倉儲](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)。
- **有時，通過緩慢的網路連接執行混合資料移動（即從本地資料存儲複製到雲資料存儲）需要一段時間。** 為了提高性能，可以使用暫存副本在本地壓縮資料，以便將資料移動到雲中的暫存資料存儲所需的時間更少。 然後，您可以在載入到目標資料存儲之前解壓縮暫存存儲中的資料。
- **由於公司 IT 策略，您不希望在防火牆中打開端口 80 和埠 443 以外的埠。** 例如，從內部部署資料存放區將資料複製到 Azure SQL Database 接收或 Azure SQL 資料倉儲接收時，您必須針對 Windows 防火牆和公司防火牆啟用連接埠 1433 上的輸出 TCP 通訊。 在這種情況下，暫存副本可以利用自託管的集成運行時，通過埠 443 上的 HTTP 或 HTTPS 將資料首先複製到 Blob 存儲暫存實例。 然後，它可以將資料從 Blob 存儲暫存載入到 SQL 資料庫或 SQL 資料倉儲中。 在此流程中，您不需要啟用連接埠 1433。

### <a name="how-staged-copy-works"></a>分段複製的運作方式

當您啟用暫存功能時，會先從來源資料存放區複製資料到暫存 Blob 儲存體 (自備)。 接著再從暫存資料存放區複製資料到接收資料存放區。 Azure 資料工廠會自動為您管理兩階段流。 Azure 資料工廠還會在資料移動完成後從暫存存儲中清理臨時資料。

![分段複製](media/copy-activity-performance/staged-copy.png)

使用暫存存儲啟動資料移動時，可以指定在將資料從來源資料存儲移動到臨時資料庫或暫存資料存儲之前是否希望壓縮資料，然後在從臨時或暫存移動資料之前進行解壓縮資料存儲到接收器資料存儲。

目前，您無法在通過不同自託管的 IR 連接的兩個數據存儲之間複製資料，這兩個數據存儲既沒有暫存副本，也不得複製。 對於此類方案，可以配置兩個顯式連結複製活動，以便從源複製到暫存，然後從暫存複製到接收器。

### <a name="configuration"></a>組態

在複製活動中配置**啟用暫存**設置，以指定是否要將資料暫存到 Blob 存儲中，然後再將其載入到目標資料存儲中。 將**啟用暫存**設置為`TRUE`時，請指定下表中列出的其他屬性。 如果沒有，還需要創建 Azure 存儲或存儲共用訪問簽名連結服務進行暫存。

| 屬性 | 描述 | 預設值 | 必要 |
| --- | --- | --- | --- |
| enableStaging |指定您是否要透過過渡暫存存放區複製資料。 |False |否 |
| linkedServiceName |指定 [AzureStorage](connector-azure-blob-storage.md#linked-service-properties) 連結服務的名稱，以代表您用來做為過渡暫存存放區的儲存體執行個體。 <br/><br/> 不能使用具有共用訪問簽名的存儲通過 PolyBase 將資料載入到 SQL 資料倉儲中。 您可以將它用於其他所有案例。 |N/A |是，當 **enableStaging** 設為 TRUE |
| path |指定要包含分段資料的 Blob 儲存體路徑。 如果不提供路徑，該服務將創建一個容器來存儲臨時資料。 <br/><br/> 只有在使用具有共用存取簽章的儲存體時，或需要讓暫存資料位於特定位置時，才指定路徑。 |N/A |否 |
| enableCompression |指定是否應在將資料複製到目標之前對其進行壓縮。 此設定可減少傳輸的資料量。 |False |否 |

>[!NOTE]
> 如果使用啟用壓縮的暫存副本，則不支援暫存 Blob 連結服務的服務主體或 MSI 身份驗證。

下面是具有上表中所述屬性的複製活動的示例定義：

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

根據兩個步驟向您收費：複製持續時間和複製類型。

* 當您在雲拷貝期間使用暫存（將資料從雲資料存儲複製到另一個雲資料存儲）時，Azure 集成運行時授權的兩個階段都會向您收取 [步驟 1 和步驟 2] x [雲拷貝單價]的複製持續時間總和。
* 當您在混合副本期間使用暫存（將資料從本地資料存儲複製到雲資料存儲）期間，由自託管集成運行時授權的一個階段，您需要收取 [混合複製持續時間] x [混合副本單價] = [雲拷貝持續時間]x [雲拷貝單價]。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概述](copy-activity-overview.md)
- [複製活動性能和可伸縮性指南](copy-activity-performance.md)
- [排除複製活動性能的故障](copy-activity-performance-troubleshooting.md)
- [使用 Azure 資料工廠將資料從資料湖或資料倉儲遷移到 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)