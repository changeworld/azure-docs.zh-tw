---
title: 針對複製活動效能進行疑難排解
description: 深入瞭解如何針對 Azure Data Factory 中的複製活動效能進行疑難排解。
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
ms.date: 06/10/2020
ms.openlocfilehash: d339e68dcf49c74c508029fda3e7eb548ec92588
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84770946"
---
# <a name="troubleshoot-copy-activity-performance"></a>針對複製活動效能進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何針對 Azure Data Factory 中的複製活動效能問題進行疑難排解。 

執行複製活動之後，您可以在 [[複製活動監視](copy-activity-monitoring.md)] 視圖中收集執行結果和效能統計資料。 以下是一個範例。

![監視複製活動執行詳細資料](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>效能微調秘訣

在某些情況下，當您在 Data Factory 中執行複製活動時，您會在頂端看到「**效能調整秘訣**」，如上述範例所示。 提示會告訴您此特定複製回合的 ADF 所識別的瓶頸，並提供如何提高複製輸送量的建議。 請嘗試進行建議變更，然後再次執行複製。

做為參考，目前的效能微調秘訣會提供下列案例的建議：

| 類別              | 效能微調秘訣                                      |
| --------------------- | ------------------------------------------------------------ |
| 特定的資料存放區   | 將資料載入**Azure Synpase Analytics （先前為 SQL DW）**：建議使用 POLYBASE 或 COPY 語句（如果未使用）。 |
| &nbsp;                | 將資料從/複製到**Azure SQL Database**：當 DTU 處於高使用率的情況下，建議升級為較高的層級。 |
| &nbsp;                | 將資料從/複製到**Azure Cosmos DB**：當 RU 處於高使用率時，建議您升級至較大的 ru。 |
|                       | 從**SAP 資料表**複製資料：複製大量資料時，建議利用 SAP 連接器的分割區選項來啟用並行載入，並增加最大分割區數目。 |
| &nbsp;                | 從**Amazon Redshift**內嵌資料：建議使用 [卸載] （如果未使用的話）。 |
| 資料存放區節流 | 如果在複製期間，資料存放區會對一些讀取/寫入作業進行節流處理，建議檢查並增加資料存放區允許的要求率，或減少並行工作負載。 |
| 整合執行時間  | 如果您使用**自我裝載的 Integration Runtime （IR）** ，而佇列中的複製活動等待時間很長，直到 IR 有可用的資源可執行為止，建議相應放大/相應放大您的 ir。 |
| &nbsp;                | 如果您使用不是最佳區域的**Azure Integration Runtime**導致讀取/寫入速度變慢，建議您將設定為在另一個區域中使用 IR。 |
| 容錯       | 如果您設定容錯並略過不相容的資料列，會導致效能變慢，建議確保來源和接收資料相容。 |
| 分段複製           | 如果已設定分段複製但不適用於您的來源接收組，建議您將它移除。 |
| 繼續                | 當複製活動從最後一個失敗點繼續，但您在原始執行之後發生變更 DIU 設定時，請注意新的 DIU 設定不會生效。 |

## <a name="understand-copy-activity-execution-details"></a>瞭解複製活動執行詳細資料

[複製活動監視] 視圖底部的 [執行詳細資料] 和 [持續時間] 會說明複製活動所經歷的主要階段（請參閱本文開頭的範例），這對於疑難排解複製效能特別有用。 複製執行的瓶頸是持續時間最長的一個。 請參閱下表中每個階段的定義，並瞭解如何[針對 Azure IR 上的複製活動進行疑難排解](#troubleshoot-copy-activity-on-azure-ir)，以及如何在自我裝載 IR 上使用這類資訊[進行複製活動的疑難排解](#troubleshoot-copy-activity-on-self-hosted-ir)。

| 階段           | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| 佇列           | 已耗用的時間，直到「複製」活動實際開始于整合執行時間為止。 |
| 複製前腳本 | 從 IR 和複製活動開始，在接收資料存放區中執行預先複製腳本的複製活動之間經過的時間。 當您設定資料庫接收的預先複製腳本時套用，例如將資料寫入 Azure SQL Database 在複製新資料之前進行清除。 |
| 傳送        | 上一個步驟結束與 IR 將所有資料從來源傳輸到接收之間經過的時間。 <br/>請注意，[傳輸] 下的子步驟會以平行方式執行，而且某些作業現在不會顯示，例如剖析/產生檔案格式。<br><br/>- **第一個位元組時間：** 上一個步驟結束與紅外線從來源資料存放區接收第一個位元組之間經過的時間。 適用于非以檔案為基礎的來源。<br>- **列出來源：** 用來列舉來源檔案或資料分割的時間量。 當您設定資料庫來源的分割區選項時（例如，從 Oracle/SAP Hana/Teradata/Netezza 等資料庫複製資料時），會套用後者。<br/>-**從來源讀取：** 從來源資料存放區抓取資料所花費的時間量。<br/>- **寫入接收：** 將資料寫入至接收資料存放區所花費的時間量。 請注意，某些連接器目前沒有此計量，包括 Azure 認知搜尋、Azure 資料總管、Azure 資料表儲存體、Oracle、SQL Server、Common Data Service、Dynamics 365、Dynamics CRM、Salesforce/Salesforce 服務雲端。 |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>針對 Azure IR 上的複製活動進行疑難排解

請遵循[效能微調步驟](copy-activity-performance.md#performance-tuning-steps)，為您的案例規劃和執行效能測試。 

當複製活動的效能不符合您的預期時，若要針對在 Azure Integration Runtime 上執行的單一複製活動進行疑難排解，如果您看到 [複製監視] 視圖中顯示的[效能微調提示](#performance-tuning-tips)，請套用建議，然後再試一次。 否則，請[瞭解複製活動執行詳細資料](#understand-copy-activity-execution-details)、檢查哪一個階段的持續時間**最長**，並套用下列指引來提升複製效能：

- 「**預先複製腳本」有很長的持續時間：** 這表示在接收資料庫上執行的預先複製腳本需要很長的時間才能完成。 調整指定的預先複製腳本邏輯，以增強效能。 如果您需要進一步協助來改善腳本，請洽詢您的資料庫小組。

- 「**傳輸時間到第一位元組」的工作持續時間很長**：這表示您的來源查詢會花很長的時間來傳回任何資料。 檢查查詢或伺服器並加以優化。 如果您需要進一步的協助，請洽詢您的資料存放區小組。

- 「**傳輸-列出來源」有很長的工作持續時間**：這表示列舉來源檔案或源資料庫資料分割的速度很慢。
  - 從以檔案為基礎的來源複製資料時，如果您在資料夾路徑或檔案名（或）上使用**萬用字元篩選器** `wildcardFolderPath` `wildcardFileName` ，或使用 [檔案**上次修改時間] 篩選器**（ `modifiedDatetimeStart` 或），請 `modifiedDatetimeEnd` 注意這類篩選會導致複製活動將指定資料夾下的所有檔案都列出至用戶端，然後套用篩選。 這類檔案列舉可能會成為瓶頸，尤其是只有一小部分的檔案符合篩選規則時。

    - 檢查是否可以根據[日期時間分割的檔案路徑或名稱來複製](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)檔案。 這種方式不會造成列出來源端的負擔。

    - 請檢查您是否可以使用資料存放區的原生篩選，特別是 Amazon S3 和 Azure Blob 的「**前置**詞」。 前置詞篩選是一種資料存放區伺服器端篩選，而且效能較佳。

    - 請考慮將單一大型資料集分割成幾個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理部分的資料。 您可以使用 Lookup/GetMetadata + ForEach + Copy 來執行這項操作。 如一般範例，請參閱[從多個容器複製](solution-template-copy-files-multiple-containers.md)檔案或將[資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)解決方案範本。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否處於高使用率狀態。 若是如此，請減少資料存放區上的工作負載，或嘗試與您的資料存放區系統管理員聯繫，以增加節流限制或可用的資源。

  - 使用相同或接近您來源資料存放區區域的 Azure IR。

- 「**傳輸-從來源讀取」有很長的工作持續時間**： 

  - 採用連接器特定的資料載入最佳做法（如果適用）。 例如，從[Amazon Redshift](connector-amazon-redshift.md)複製資料時，請將設定為使用 Redshift UNLOAD。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否處於高使用率。 若是如此，請減少資料存放區上的工作負載，或嘗試與您的資料存放區系統管理員聯繫，以增加節流限制或可用的資源。

  - 檢查您的複製來源和接收模式： 

    - 如果您的複製模式支援大於4個數據整合單位（Diu）-請參閱[本節](copy-activity-performance-features.md#data-integration-units)的詳細資訊，通常您可以嘗試增加 diu 以取得更佳的效能。 

    - 否則，請考慮將單一大型資料集分割成幾個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理部分的資料。 您可以使用 Lookup/GetMetadata + ForEach + Copy 來執行這項操作。 請參閱[從多個容器複製](solution-template-copy-files-multiple-containers.md)檔案、將[資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)，或[使用控制資料表](solution-template-bulk-copy-with-control-table.md)解決方案範本作為一般範例進行大量複製。

  - 使用相同或接近您來源資料存放區區域的 Azure IR。

- 「**傳輸-寫入接收」有很長的工作持續時間**：

  - 採用連接器特定的資料載入最佳做法（如果適用）。 例如，將資料複製到[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) （先前為 SQL DW）時，請使用 POLYBASE 或 COPY 語句。 

  - 檢查 ADF 是否在接收時報告任何節流錯誤，或您的資料存放區是否在高使用率之下。 若是如此，請減少資料存放區上的工作負載，或嘗試與您的資料存放區系統管理員聯繫，以增加節流限制或可用的資源。

  - 檢查您的複製來源和接收模式： 

    - 如果您的複製模式支援大於4個數據整合單位（Diu）-請參閱[本節](copy-activity-performance-features.md#data-integration-units)的詳細資訊，通常您可以嘗試增加 diu 以取得更佳的效能。 

    - 否則，請逐漸微調[平行複製](copy-activity-performance-features.md)，請注意，太多平行複製可能會對效能造成負面影響。

  - 在相同或接近您的接收資料存放區區域中，使用 Azure IR。

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>針對自我裝載 IR 上的複製活動進行疑難排解

請遵循[效能微調步驟](copy-activity-performance.md#performance-tuning-steps)，為您的案例規劃和執行效能測試。 

當複製效能不符合您的預期時，若要針對在 Azure Integration Runtime 上執行的單一複製活動進行疑難排解，如果您看到 [複製監視] 視圖中顯示的[效能微調提示](#performance-tuning-tips)，請套用建議，然後再試一次。 否則，請[瞭解複製活動執行詳細資料](#understand-copy-activity-execution-details)、檢查哪一個階段的持續時間**最長**，並套用下列指引來提升複製效能：

- 「**佇列」很長的持續時間：** 這表示複製活動在佇列中等候長時間，直到自我裝載的 IR 具有資源可執行為止。 檢查 IR 容量和使用量，並根據您的工作負載相應[增加或相應](create-self-hosted-integration-runtime.md#high-availability-and-scalability)放大。

- 「**傳輸時間到第一位元組」的工作持續時間很長**：這表示您的來源查詢會花很長的時間來傳回任何資料。 檢查查詢或伺服器並加以優化。 如果您需要進一步的協助，請洽詢您的資料存放區小組。

- 「**傳輸-列出來源」有很長的工作持續時間**：這表示列舉來源檔案或源資料庫資料分割的速度很慢。

  - 檢查自我裝載 IR 機器是否有低延遲連線到來源資料存放區。 如果您的來源位於 Azure 中，您可以使用[此工具](http://www.azurespeed.com/Azure/Latency)來檢查從自我裝載 IR 機器到 azure 區域的延遲，越不會越好。

  - 從以檔案為基礎的來源複製資料時，如果您在資料夾路徑或檔案名（或）上使用**萬用字元篩選器** `wildcardFolderPath` `wildcardFileName` ，或使用 [檔案**上次修改時間] 篩選器**（ `modifiedDatetimeStart` 或），請 `modifiedDatetimeEnd` 注意這類篩選會導致複製活動將指定資料夾下的所有檔案都列出至用戶端，然後套用篩選。 這類檔案列舉可能會成為瓶頸，尤其是只有一小部分的檔案符合篩選規則時。

    - 檢查是否可以根據[日期時間分割的檔案路徑或名稱來複製](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)檔案。 這種方式不會造成列出來源端的負擔。

    - 請檢查您是否可以使用資料存放區的原生篩選，特別是 Amazon S3 和 Azure Blob 的「**前置**詞」。 前置詞篩選是一種資料存放區伺服器端篩選，而且效能較佳。

    - 請考慮將單一大型資料集分割成幾個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理部分的資料。 您可以使用 Lookup/GetMetadata + ForEach + Copy 來執行這項操作。 如一般範例，請參閱[從多個容器複製](solution-template-copy-files-multiple-containers.md)檔案或將[資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)解決方案範本。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否處於高使用率狀態。 若是如此，請減少資料存放區上的工作負載，或嘗試與您的資料存放區系統管理員聯繫，以增加節流限制或可用的資源。

- 「**傳輸-從來源讀取」有很長的工作持續時間**： 

  - 檢查自我裝載 IR 機器是否有低延遲連線到來源資料存放區。 如果您的來源位於 Azure 中，您可以使用[此工具](http://www.azurespeed.com/Azure/Latency)來檢查從自我裝載 IR 機器到 azure 區域的延遲，越不會越好。

  - 檢查自我裝載 IR 機器是否有足夠的輸入頻寬，以有效率地讀取和傳輸資料。 如果您的來源資料存放區位於 Azure 中，您可以使用[此工具](https://www.azurespeed.com/Azure/Download)來檢查下載速度。

  - 在 Azure 入口網站 > 您的資料處理站-> 總覽頁面中，檢查自我裝載 IR 的 CPU 和記憶體使用量趨勢。 如果 CPU 使用率很高或可用記憶體不足，請考慮相應[增加/相應放大 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 。

  - 採用連接器特定的資料載入最佳做法（如果適用）。 例如：

    - 從[Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [sap 資料表](connector-sap-table.md#sap-table-as-source)和[sap 開放式中樞](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)複製資料時，請啟用資料分割選項以平行方式複製資料。

    - 從[HDFS](connector-hdfs.md)複製資料時，請將設定為使用 DistCp。

    - 從[Amazon Redshift](connector-amazon-redshift.md)複製資料時，請將設定為使用 Redshift UNLOAD。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否在高使用率之下。 若是如此，請減少資料存放區上的工作負載，或嘗試與您的資料存放區系統管理員聯繫，以增加節流限制或可用的資源。

  - 檢查您的複製來源和接收模式： 

    - 如果您從已啟用分割選項的資料存放區複製資料，請考慮逐漸微調[平行複本](copy-activity-performance-features.md)，請注意，太多平行複本可能會損害效能。

    - 否則，請考慮將單一大型資料集分割成幾個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理部分的資料。 您可以使用 Lookup/GetMetadata + ForEach + Copy 來執行這項操作。 請參閱[從多個容器複製](solution-template-copy-files-multiple-containers.md)檔案、將[資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)，或[使用控制資料表](solution-template-bulk-copy-with-control-table.md)解決方案範本作為一般範例進行大量複製。

- 「**傳輸-寫入接收」有很長的工作持續時間**：

  - 採用連接器特定的資料載入最佳做法（如果適用）。 例如，將資料複製到[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) （先前為 SQL DW）時，請使用 POLYBASE 或 COPY 語句。 

  - 檢查自我裝載 IR 機器是否有低延遲連線到接收資料存放區。 如果您的接收器位於 Azure 中，您可以使用[此工具](http://www.azurespeed.com/Azure/Latency)來檢查從自我裝載 IR 機器到 azure 區域的延遲，越不會越好。

  - 檢查自我裝載 IR 機器是否有足夠的輸出頻寬，以有效率地傳輸和寫入資料。 如果您的接收資料存放區位於 Azure 中，您可以使用[此工具](https://www.azurespeed.com/Azure/UploadLargeFile)來檢查上傳速度。

  - 檢查 Azure 入口網站中自我裝載 IR 的 CPU 和記憶體使用量趨勢，> 您的資料處理站-> 總覽頁面。 如果 CPU 使用率很高或可用記憶體不足，請考慮相應[增加/相應放大 IR](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 。

  - 檢查 ADF 是否在接收時報告任何節流錯誤，或您的資料存放區是否在高使用率之下。 若是如此，請減少資料存放區上的工作負載，或嘗試與您的資料存放區系統管理員聯繫，以增加節流限制或可用的資源。

  - 請考慮逐漸微調[平行複本](copy-activity-performance-features.md)，請注意，太多平行複本可能會對效能造成負面影響。

## <a name="other-references"></a>其他參考資料

以下是幾個支援的資料存放區所適用的效能監視及調整參考：

* Azure Blob 儲存體： blob[儲存體的擴充性和效能目標](../storage/blobs/scalability-targets.md)，以及[Blob 儲存體的效能和擴充性檢查清單](../storage/blobs/storage-performance-checklist.md)。
* Azure 資料表儲存體：資料表儲存體[的擴充性和效能目標](../storage/tables/scalability-targets.md)，以及[資料表儲存體的效能和擴充性檢查清單](../storage/tables/storage-performance-checklist.md)。
* Azure SQL Database：您可以[監視效能](../sql-database/sql-database-single-database-monitor.md)，並檢查資料庫交易單位（DTU）百分比。
* Azure SQL 資料倉儲：其功能會以資料倉儲單位（Dwu）來測量。 請參閱[管理 Azure SQL 資料倉儲中的計算能力（總覽）](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB： [Azure Cosmos DB 中的效能層級](../cosmos-db/performance-levels.md)。
* SQL Server：[效能的監視和微調](https://msdn.microsoft.com/library/ms189081.aspx)。
* 內部部署檔案伺服器：[檔案伺服器的效能微調](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動效能和擴充性指南](copy-activity-performance.md)
- [複製活動效能優化功能](copy-activity-performance-features.md)
- [使用 Azure Data Factory 將資料從您的 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)
