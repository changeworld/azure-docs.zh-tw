---
title: 針對複製活動效能進行疑難排解
description: 瞭解如何在 Azure Data Factory 中針對複製活動效能進行疑難排解。
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
ms.date: 01/07/2021
ms.openlocfilehash: ee6105376f5e8dc884f13e04db51126c039328e9
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97968886"
---
# <a name="troubleshoot-copy-activity-performance"></a>針對複製活動效能進行疑難排解

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何針對 Azure Data Factory 中的複製活動效能問題進行疑難排解。 

執行複製活動之後，您可以在 [複製活動監視](copy-activity-monitoring.md) 視圖中收集執行結果和效能統計資料。 以下是一個範例。

![監視複製活動執行詳細資料](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>效能微調秘訣

在某些情況下，當您在 Data Factory 中執行複製活動時，您會在頂端看到「 **效能微調秘訣** 」，如上述範例所示。 提示會告訴您 ADF 針對這個特定的複製執行所識別的瓶頸，以及如何提高複製輸送量的建議。 請嘗試進行建議變更，然後再次執行複製。

作為參考，目前的效能微調秘訣提供下列情況的建議：

| 類別              | 效能微調秘訣                                      |
| --------------------- | ------------------------------------------------------------ |
| 特定的資料存放區   | 將資料載入 **Azure Synapse Analytics**：建議使用 PolyBase 或 COPY 語句（如果未使用）。 |
| &nbsp;                | 從 **Azure SQL Database** 複製資料/將資料複製到該處：當 DTU 使用率過高時，建議升級至更高的層級。 |
| &nbsp;                | 從 **Azure Cosmos DB** 複製資料/將資料複製到該處：當 RU 使用率過高時，建議升級為較大的 ru。 |
|                       | 從 **SAP 資料表** 複製資料：複製大量資料時，建議利用 SAP 連接器的分割區選項，以啟用平行載入並增加最大分割區數目。 |
| &nbsp;                | 從 **Amazon Redshift** 擷取資料：如果未使用，建議使用 UNLOAD。 |
| 資料存放區節流 | 在複製期間，如果資料存放區已節流許多讀取/寫入作業，建議檢查並增加資料存放區允許的要求率，或減少並行工作負載。 |
| 整合執行時間  | 如果您使用自我裝載的 **Integration Runtime (ir)** 和複製活動等待時間長度超過佇列，直到 ir 具有可用資源才能執行為止，建議您相應放大/相應放大 ir。 |
| &nbsp;                | 如果您使用非最佳區域中的 **Azure Integration Runtime** ，而導致讀取/寫入變慢，建議您將設定為在另一個區域中使用 IR。 |
| 容錯       | 如果您設定容錯並跳過不相容的資料列，會導致效能變慢，建議確保來源和接收資料相容。 |
| 分段複製           | 如果已設定分段複製，但對於您的來源接收配對而言並不實用，建議您將它移除。 |
| 繼續                | 從上次失敗點繼續複製活動，但在原始執行之後變更 DIU 設定時，請注意新的 DIU 設定不會生效。 |

## <a name="understand-copy-activity-execution-details"></a>瞭解複製活動的執行詳細資料

[複製活動監視] 視圖底部的 [執行詳細資料] 和 [持續時間] 會說明複製活動所經歷的主要階段 (請參閱本文開頭的範例) ，這特別適用于疑難排解複製效能。 複製執行的瓶頸是具有最長持續時間的瓶頸。 請參閱下表中每個階段的定義，並瞭解如何在 [Azure IR 上疑難排解複製活動](#troubleshoot-copy-activity-on-azure-ir) ，以及如何使用這類資訊針對 [自我裝載 IR 上的複製活動進行疑難排解](#troubleshoot-copy-activity-on-self-hosted-ir) 。

| 階段           | 描述                                                  |
| --------------- | ------------------------------------------------------------ |
| 佇列           | 在「複製活動」實際于整合執行時間開始之前所經過的時間。 |
| 複製前腳本 | 從 IR 和複製活動開始，在接收資料存放區中執行預先複製腳本的複製活動之間經過的時間。 當您為資料庫接收設定複製前腳本時套用，例如將資料寫入 Azure SQL Database 時，請先清除，然後再複製新的資料。 |
| 傳送        | 上一個步驟結束和 IR 將所有資料從來源傳輸到接收的經過時間。 <br/>請注意，傳輸中的子步驟會以平行方式執行，而某些作業現在不會顯示，例如剖析/產生檔案格式。<br><br/>- 距 **第一位元組時間：** 上一個步驟結束和 IR 從來源資料存放區收到第一個位元組的時間之間經過的時間。 適用于非檔案型來源。<br>- **列出來源：** 列舉原始程式檔或資料分割所花費的時間量。 當您設定資料庫來源的資料分割選項（例如，從 Oracle/SAP Hana/Teradata/Netezza/等資料庫複製資料時），就會套用後者。<br/>-**從來源讀取：** 從來源資料存放區中取出資料所花費的時間量。<br/>- **寫入至接收：** 將資料寫入至接收資料存放區所花費的時間量。 請注意，某些連接器目前沒有此計量，包括 Azure 認知搜尋、Azure 資料總管、Azure 資料表儲存體、Oracle、SQL Server、Common Data Service、Dynamics 365、Dynamics CRM、Salesforce/Salesforce 服務雲端。 |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>針對 Azure IR 上的複製活動進行疑難排解

遵循 [效能微調步驟](copy-activity-performance.md#performance-tuning-steps) 來規劃和執行您案例的效能測試。 

當複製活動的效能不符合您的預期時，若要疑難排解在 Azure Integration Runtime 上執行的單一複製活動，如果您看到 [複製監視] 視圖顯示的 [效能微調提示](#performance-tuning-tips) ，請套用建議，然後再試一次。 否則，請 [瞭解複製活動執行詳細資料](#understand-copy-activity-execution-details)、檢查哪個階段有 **最長** 的持續時間，並套用下列指引以提升複製效能：

- 「**預先複製腳本」有很長的持續時間：** 這表示在接收資料庫上執行的複製前腳本需要很長的時間才能完成。 調整指定的複製前腳本邏輯以增強效能。 如果您需要進一步協助改進腳本，請洽詢您的資料庫團隊。

- 「**傳輸時到第一個位元組」有很長的工作持續時間**：這表示您的來源查詢需要很長的時間才會傳回任何資料。 檢查查詢或伺服器並將其優化。 如果您需要進一步的協助，請洽詢您的資料存放區小組。

- 「**傳輸-列出來源」有很長的工作持續時間**：這表示列舉原始程式檔或源資料庫資料分割的速度很慢。
  - 從檔案型來源複製資料時，如果您在資料夾路徑或檔案名 (或) 上使用 **萬用字元篩選** `wildcardFolderPath` `wildcardFileName` ，或使用檔案 **上次修改時間篩選** (`modifiedDatetimeStart` 或 `modifiedDatetimeEnd`) ，請注意這種篩選準則會導致複製活動將指定資料夾下的所有檔案列示給用戶端，然後套用篩選。 這類檔案列舉可能會成為瓶頸，尤其是當只有一小部分的檔案符合篩選規則時。

    - 檢查您是否可以 [根據日期時間分割的檔案路徑或名稱來複製](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)檔案。 這種方式不會在清單來源端帶來負擔。

    - 檢查您是否可以改為使用資料存放區的原生篩選，特別是 Amazon S3/Azure Blob/Azure 檔案儲存體的「**前置** 詞」和 ADLS Gen1 的 "**listAfter/listBefore**"。 這些篩選器是資料存放區的伺服器端篩選，而且效能較佳。

    - 請考慮將單一大型資料集分割成數個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理的資料部分。 您可以使用 Lookup/GetMetadata + ForEach + 複製來完成這項作業。 如一般範例，請參閱 [從多個容器複製](solution-template-copy-files-multiple-containers.md) 檔案，或將 [資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md) 解決方案範本。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否處於高使用率狀態。 如果是，請減少資料存放區上的工作負載，或嘗試聯繫資料存放區系統管理員，以增加節流限制或可用的資源。

  - 使用相同或接近來源資料存放區區域中的 Azure IR。

- 「**從來源傳輸-讀取」有很長的工作持續時間**： 

  - 如果適用，請採用連接器特有的資料載入最佳作法。 例如，從 [Amazon Redshift](connector-amazon-redshift.md)複製資料時，請將設定為使用 Redshift UNLOAD。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否處於高使用率。 如果是，請減少資料存放區上的工作負載，或嘗試聯繫資料存放區系統管理員，以增加節流限制或可用的資源。

  - 檢查您的複製來源和接收模式： 

    - 如果您的複製模式支援大於4個數據整合單位 (Diu) -如需詳細資料，請參閱 [這一節](copy-activity-performance-features.md#data-integration-units) ，通常您可以嘗試增加 diu，以獲得更好的效能。 

    - 否則，請考慮將單一大型資料集分割成數個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理的資料部分。 您可以使用 Lookup/GetMetadata + ForEach + 複製來完成這項作業。 如需一般範例，請參閱 [從多個容器複製](solution-template-copy-files-multiple-containers.md)檔案、將 [資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)，或 [使用控制資料表](solution-template-bulk-copy-with-control-table.md) 方案範本進行大量複製。

  - 使用相同或接近來源資料存放區區域中的 Azure IR。

- 「**傳輸-寫入至接收」有很長的工作持續時間**：

  - 如果適用，請採用連接器特有的資料載入最佳作法。 例如，將資料複製到 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)時，請使用 POLYBASE 或 COPY 語句。 

  - 檢查 ADF 是否在接收時報告任何節流錯誤，或您的資料存放區是否處於高使用率。 如果是，請減少資料存放區上的工作負載，或嘗試聯繫資料存放區系統管理員，以增加節流限制或可用的資源。

  - 檢查您的複製來源和接收模式： 

    - 如果您的複製模式支援大於4個數據整合單位 (Diu) -如需詳細資料，請參閱 [這一節](copy-activity-performance-features.md#data-integration-units) ，通常您可以嘗試增加 diu，以獲得更好的效能。 

    - 否則，會逐漸調整 [平行複製](copy-activity-performance-features.md)，請注意，太多平行複製甚至可能會影響效能。

  - 使用相同或接近您接收資料存放區區域的 Azure IR。

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>針對自我裝載 IR 上的複製活動進行疑難排解

遵循 [效能微調步驟](copy-activity-performance.md#performance-tuning-steps) 來規劃和執行您案例的效能測試。 

當複製效能不符合您的預期時，若要疑難排解在 Azure Integration Runtime 上執行的單一複製活動，如果您看到 [複製監視] 查看中顯示的 [效能微調提示](#performance-tuning-tips) ，請套用建議，然後再試一次。 否則，請 [瞭解複製活動執行詳細資料](#understand-copy-activity-execution-details)、檢查哪個階段有 **最長** 的持續時間，並套用下列指引以提升複製效能：

- 「**佇列」花費很長的時間：** 這表示在您的自我裝載 IR 具有要執行的資源之前，複製活動會在佇列中等待很長的時間。 檢查 IR 容量和使用量，然後根據您的工作負載 [擴大或縮小](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 。

- 「**傳輸時到第一個位元組」有很長的工作持續時間**：這表示您的來源查詢需要很長的時間才會傳回任何資料。 檢查查詢或伺服器並將其優化。 如果您需要進一步的協助，請洽詢您的資料存放區小組。

- 「**傳輸-列出來源」有很長的工作持續時間**：這表示列舉原始程式檔或源資料庫資料分割的速度很慢。

  - 檢查自我裝載 IR 機器是否有低延遲的連線至來源資料存放區。 如果您的來源是在 Azure 中，您可以使用 [此工具](http://www.azurespeed.com/Azure/Latency) 來檢查從自我裝載 IR 電腦到 azure 區域的延遲，效能愈低。

  - 從檔案型來源複製資料時，如果您在資料夾路徑或檔案名 (或) 上使用 **萬用字元篩選** `wildcardFolderPath` `wildcardFileName` ，或使用檔案 **上次修改時間篩選** (`modifiedDatetimeStart` 或 `modifiedDatetimeEnd`) ，請注意這種篩選準則會導致複製活動將指定資料夾下的所有檔案列示給用戶端，然後套用篩選。 這類檔案列舉可能會成為瓶頸，尤其是當只有一小部分的檔案符合篩選規則時。

    - 檢查您是否可以 [根據日期時間分割的檔案路徑或名稱來複製](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)檔案。 這種方式不會在清單來源端帶來負擔。

    - 檢查您是否可以改為使用資料存放區的原生篩選，特別是 Amazon S3/Azure Blob/Azure 檔案儲存體的「**前置** 詞」和 ADLS Gen1 的 "**listAfter/listBefore**"。 這些篩選器是資料存放區的伺服器端篩選，而且效能較佳。

    - 請考慮將單一大型資料集分割成數個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理的資料部分。 您可以使用 Lookup/GetMetadata + ForEach + 複製來完成這項作業。 如一般範例，請參閱 [從多個容器複製](solution-template-copy-files-multiple-containers.md) 檔案，或將 [資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md) 解決方案範本。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否處於高使用率狀態。 如果是，請減少資料存放區上的工作負載，或嘗試聯繫資料存放區系統管理員，以增加節流限制或可用的資源。

- 「**從來源傳輸-讀取」有很長的工作持續時間**： 

  - 檢查自我裝載 IR 機器是否有低延遲的連線至來源資料存放區。 如果您的來源是在 Azure 中，您可以使用 [此工具](http://www.azurespeed.com/Azure/Latency) 來檢查從自我裝載 IR 電腦到 azure 區域的延遲，效能愈低。

  - 檢查自我裝載 IR 機器是否有足夠的輸入頻寬，以有效率地讀取和傳輸資料。 如果您的來源資料存放區位於 Azure 中，您可以使用 [此工具](https://www.azurespeed.com/Azure/Download) 來檢查下載速度。

  - 在 Azure 入口網站 > 您的資料處理站-> 總覽頁面中，檢查自我裝載 IR 的 CPU 和記憶體使用量趨勢。 如果 CPU 使用量很高或可用記憶體不足，請考慮將 [IR 擴大/](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 放大。

  - 如果適用，請採用連接器特有的資料載入最佳作法。 例如：

    - 從 [Oracle](connector-oracle.md#oracle-as-source)、 [Netezza](connector-netezza.md#netezza-as-source)、 [Teradata](connector-teradata.md#teradata-as-source)、 [SAP Hana](connector-sap-hana.md#sap-hana-as-source)、 [sap 資料表](connector-sap-table.md#sap-table-as-source)和 [sap Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)) 複製資料時，請啟用資料分割選項以平行複製資料。

    - 從 [HDFS](connector-hdfs.md)複製資料時，請將設定為使用 DistCp。

    - 從 [Amazon Redshift](connector-amazon-redshift.md)複製資料時，請將設定為使用 Redshift UNLOAD。

  - 檢查 ADF 是否報告來源上的任何節流錯誤，或您的資料存放區是否處於高使用率。 如果是，請減少資料存放區上的工作負載，或嘗試聯繫資料存放區系統管理員，以增加節流限制或可用的資源。

  - 檢查您的複製來源和接收模式： 

    - 如果您從分割區選項的資料存放區複製資料，請考慮逐漸調整 [平行複製](copy-activity-performance-features.md)，請注意，太多平行複製可能甚至會影響效能。

    - 否則，請考慮將單一大型資料集分割成數個較小的資料集，並讓這些複製作業同時執行每個 esposito 著手處理的資料部分。 您可以使用 Lookup/GetMetadata + ForEach + 複製來完成這項作業。 如需一般範例，請參閱 [從多個容器複製](solution-template-copy-files-multiple-containers.md)檔案、將 [資料從 Amazon S3 遷移至 ADLS Gen2](solution-template-migration-s3-azure.md)，或 [使用控制資料表](solution-template-bulk-copy-with-control-table.md) 方案範本進行大量複製。

- 「**傳輸-寫入至接收」有很長的工作持續時間**：

  - 如果適用，請採用連接器特有的資料載入最佳作法。 例如，將資料複製到 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)時，請使用 POLYBASE 或 COPY 語句。 

  - 檢查自我裝載 IR 機器是否有低延遲連線至接收資料存放區。 如果您的接收是在 Azure 中，您可以使用 [此工具](http://www.azurespeed.com/Azure/Latency) 來檢查從自我裝載 IR 機器到 azure 區域的延遲，越好。

  - 檢查自我裝載 IR 機器是否有足夠的輸出頻寬可有效率地傳輸和寫入資料。 如果您的接收資料存放區位於 Azure 中，您可以使用 [此工具](https://www.azurespeed.com/Azure/UploadLargeFile) 來檢查上傳速度。

  - 檢查 Azure 入口網站 > 您的資料處理站-> 總覽頁面中的自我裝載 IR 的 CPU 和記憶體使用量趨勢。 如果 CPU 使用量很高或可用記憶體不足，請考慮將 [IR 擴大/](create-self-hosted-integration-runtime.md#high-availability-and-scalability) 放大。

  - 檢查 ADF 是否在接收時報告任何節流錯誤，或您的資料存放區是否處於高使用率。 如果是，請減少資料存放區上的工作負載，或嘗試聯繫資料存放區系統管理員，以增加節流限制或可用的資源。

  - 請考慮逐漸調整 [平行複製](copy-activity-performance-features.md)，請注意，太多平行複製甚至可能會影響效能。


## <a name="connector-and-ir-performance"></a>連接器和 IR 效能

本節將探討特定連接器類型或整合執行時間的一些效能疑難排解指南。

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>使用 Azure IR 與 Azure VNet IR 的活動執行時間會有所不同

當資料集是根據不同的 Integration Runtime 時，活動執行時間會有所不同。

- **徵兆**：只要切換資料集中的 [連結服務] 下拉式清單，就會執行相同的管線活動，但會有截然不同的執行時間。 當資料集是根據受管理的虛擬網路 Integration Runtime 時，平均需要2分鐘以上的時間才能完成執行，但根據預設 Integration Runtime，大約需要20秒的時間才能完成。

- **原因**：檢查管線執行的詳細資料，您可以看到緩慢的管線是在受管理的 VNet (虛擬網路) IR，而在 Azure IR 上執行正常的虛擬網路。 根據設計，受控 VNet IR 的佇列時間會比 Azure IR 更長，因為我們不會為每個資料處理站保留一個計算節點，因此每個複製活動都有大約2分鐘的時間來啟動，而這主要是發生在 VNet 聯結而非 Azure IR。

    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>將資料載入 Azure SQL Database 時的效能低

- **徵兆**：將中的資料複製到 Azure SQL Database 會變得很慢。

- **原因**：問題的根本原因大部分是由 Azure SQL Database 端的瓶頸所觸發。 以下是一些可能的原因：

    - Azure SQL Database 層不夠高。

    - Azure SQL Database DTU 使用量接近100%。 您可以 [監視效能](https://docs.microsoft.com/azure/azure-sql/database/monitor-tune-overview) ，並考慮升級 Azure SQL Database 層。

    - 未正確設定索引。 在載入資料之前先移除所有索引，並在載入完成後重新建立索引。

    - WriteBatchSize 不夠大，無法容納架構資料列大小。 請嘗試放大問題的屬性。

    - 使用預存程式，而不是使用大量插入，這應該會有較差的效能。 

- **解決** 方式：請參閱 [複製活動效能疑難排解](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)。

### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>剖析大型 Excel 檔案時的效能超時或效能變慢

- **徵兆**：

    - 當您從連接/存放區、預覽資料、清單或重新整理工作表建立 Excel 資料集和匯入架構時，如果 Excel 檔案大小很大，您可能會遇到逾時錯誤。

    - 當您使用複製活動將資料從大型 Excel 檔案複製 ( # B0 = 100 MB) 至其他資料存放區時，可能會遇到效能變慢或 OOM 問題。

- **原因**： 

    - 針對匯入架構、預覽資料，以及列出 excel 資料集上的工作表等作業，timeout 為 100 s 和靜態。 針對大型 Excel 檔案，這些作業可能無法在 timeout 值中完成。

    - ADF 複製活動會將整個 Excel 檔案讀取到記憶體中，然後找出指定的工作表和資料格以讀取資料。 這是因為基礎 SDK ADF 使用的行為。

- **解決方案**： 

    - 若要匯入架構，您可以產生較小的範例檔案，也就是原始檔案的子集，然後選擇 [從範例檔案匯入架構]，而不是 [從連接/存放區匯入架構]。

    - 針對清單工作表，您可以按一下 [工作表] 下拉式清單中的 [編輯]，改為輸入工作表名稱/索引。

    - 若要將大型 excel 檔案 ( # B0 100 MB) 複製到其他存放區，您可以使用資料流程 Excel 來源，讓串流讀取和執行效能更好。
    
## <a name="other-references"></a>其他參考資料

以下是幾個支援的資料存放區所適用的效能監視及調整參考：

* Azure Blob 儲存體： [適用于 blob 儲存體的擴充性和效能目標](../storage/blobs/scalability-targets.md) ，以及 [Blob 儲存體的效能和擴充性檢查清單](../storage/blobs/storage-performance-checklist.md)。
* Azure 資料表儲存體：表格儲存體 [的擴充性和效能目標](../storage/tables/scalability-targets.md) ，以及 [資料表儲存體的效能和擴充性檢查清單](../storage/tables/storage-performance-checklist.md)。
* Azure SQL Database：您可以 [監視效能](../azure-sql/database/monitor-tune-overview.md) ，並檢查資料庫交易單位 (DTU) 百分比。
* Azure Synapse Analytics：其功能會以資料倉儲單位 (Dwu) 來測量。 請參閱 [Azure Synapse Analytics 中的管理計算能力 (總覽) ](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure Cosmos DB： [Azure Cosmos DB 中的效能層級](../cosmos-db/performance-levels.md)。
* SQL Server： [監視和調整效能](/sql/relational-databases/performance/monitor-and-tune-for-performance)。
* 內部部署檔案伺服器： [檔案伺服器的效能微調](/previous-versions//dn567661(v=vs.85))。

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概觀](copy-activity-overview.md)
- [複製活動效能和可擴縮性指南](copy-activity-performance.md)
- [複製活動效能優化功能](copy-activity-performance-features.md)
- [使用 Azure Data Factory 將資料從 data lake 或資料倉儲遷移至 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)