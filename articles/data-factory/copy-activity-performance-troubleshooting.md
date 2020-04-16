---
title: 排除複製活動效能的故障
description: 瞭解如何在 Azure 數據工廠中排除複製活動性能的疑難排解。
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
ms.date: 03/11/2020
ms.openlocfilehash: 6df1903e828c0c4cafa6589d4a85f4016bed893e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414132"
---
# <a name="troubleshoot-copy-activity-performance"></a>排除複製活動效能的故障

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述了如何在 Azure 數據工廠中解決複製活動性能問題。 

運行複製活動後,可以在[複製活動監視](copy-activity-monitoring.md)檢視中收集運行結果和性能統計資訊。 以下是一個範例。

![監視複製活動執行詳細資訊](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>效能微調秘訣

在某些情況下,在資料工廠中執行複製活動時,您將在頂部看到 **「效能調優提示」,** 如上例所示。 這些提示告訴您 ADF 為此特定副本運行確定的瓶頸,以及如何提高複製輸送量的建議。 嘗試進行重新命令的更改,然後再次運行副本。

作為參考,當前性能調優提示為以下情況提供建議:

| 類別              | 效能微調秘訣                                      |
| --------------------- | ------------------------------------------------------------ |
| 資料儲存特定   | 將數據載入**Azure Synpase 分析(以前 SQL DW)** 中:建議在不使用 PolyBase 或 COPY 語句時使用它。 |
| &nbsp;                | 將數據從/複製到**Azure SQL 資料庫**:當 DTU 處於高利用率時,建議升級到更高層。 |
| &nbsp;                | 將資料從/複製到**Azure Cosmos DB:** 當 RU 被高度利用時,建議升級到更大的 RU。 |
| &nbsp;                | 從**亞馬遜紅移**引入數據:建議使用UNLOAD,如果它不使用。 |
| 資料儲存限制 | 如果在複製過程中數據存儲限制了多個讀/寫操作,建議檢查和提高數據存儲的允許請求速率,或減少併發工作負載。 |
| 整合執行時  | 如果使用**自託管整合時 (IR)** 並在佇列中等待很長時間,直到 IR 具有可用資源執行,建議向外 / 上擴展 IR。 |
| &nbsp;                | 如果使用的**Azure 整合執行時**位於不理想區域中,導致讀取/寫入速度慢,建議配置以在另一個區域中使用 IR。 |
| 容錯       | 如果配置容錯和跳過不相容行會導致性能降低,建議確保源和接收器數據相容。 |
| 分段複製           | 如果已配置暫存副本,但對源接收器對沒有幫助,建議將其刪除。 |
| 繼續                | 當複製活動從上次失敗點恢復,但您碰巧在原始運行後更改 DIU 設置時,請注意新的 DIU 設置不起作用。 |

## <a name="understand-copy-activity-execution-details"></a>瞭解複製活動執行詳細資訊

複製活動監視檢視底部的執行詳細資訊和持續時間描述了複製活動經歷的關鍵階段(請參閱本文開頭的示例),這對於解決複製性能的疑難問題特別有用。 複製運行的瓶頸是持續時間最長的瓶頸。 請參考每個階段定義的下表,瞭解如何[對 Azure IR 上的複製活動進行故障排除](#troubleshoot-copy-activity-on-azure-ir),以及如何使用此類資訊[對自託管 IR 中的複製活動進行故障排除](#troubleshoot-copy-activity-on-self-hosted-ir)。

| 階段           | 描述                                                  |
| --------------- | ------------------------------------------------------------ |
| 佇列           | 在整合式執行時實際啟動複製活動之前已過的時間。 |
| 預拷貝腳本 | 從 IR 開始開始複製活動到在接收器資料儲存中完成執行預複製文本的複製活動之間的運行時間。 在為資料庫接收器配置預複製腳本時應用,例如將數據寫入 Azure SQL 資料庫時,在複製新數據之前請清理。 |
| 傳送        | 上一步驟結束和紅外從源傳輸到接收器之間的運行時間。 "傳輸"下的子步驟並行運行。<br><br>- **第一個字節的時間:** 從上一步驟的結束到 IR 從來源資料儲存接收第一個字節的時間之間的時間。 應用於非基於檔的源。<br>- **清單來源:** 枚舉源文件或數據分區所花費的時間量。 當您為資料庫源配置分區選項時,例如從 Oracle/SAP HANA/Teradata/Netezza/等資料庫複製數據時,則適用後者。<br/>-**從來源讀取:** 從源數據存儲檢索數據所花費的時間量。<br/>- **寫入接收者:** 用於將數據寫入接收體資料儲存所花費的時間。 |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>在 Azure IR 上排除複製活動故障

按照[性能調優步驟](copy-activity-performance.md#performance-tuning-steps)為方案規劃和執行性能測試。 

當複製活動性能不符合預期時,要對 Azure 整合執行時上運行的單個複製活動進行故障排除,如果看到複製監視檢視中顯示[的性能調優提示](#performance-tuning-tips),請應用建議,然後重試。 否則,[瞭解複製活動執行詳細資訊](#understand-copy-activity-execution-details),檢查哪個階段持續時間**最長**,並應用以下指南以提高複製性能:

- **"預複製腳本"經歷了長時間的運行:** 這意味著在接收器資料庫上運行的預複製腳本需要很長時間才能完成。 調整指定的預複製腳本邏輯以提高性能。 如果您需要改進文稿的進一步説明,請與資料庫團隊聯繫。

- **"傳輸 - 第一位元節的時間"經歷了很長的工作持續時間**:這意味著源查詢返回任何資料需要很長時間。 檢查並優化查詢或伺服器。 如果您需要進一步説明,請聯絡數據儲存團隊。

- **"傳輸 - 清單源"經歷了長時間的工作**:這意味著枚舉源檔或源資料庫資料分區很慢。

  - 從基於檔案的源複製資料時,如果在資料夾路徑或檔名`wildcardFolderPath``wildcardFileName`( 或 ) 上使用**通配符篩選器**,或使用`modifiedDatetimeStart`**檔案上次修改的時間篩選器**( 或`modifiedDatetimeEnd`),請注意,此類篩選器將導致將指定資料夾下的所有檔案清單的副本活動添加到用戶端,然後應用篩選器。 此類檔枚舉可能成為瓶頸,尤其是當只有少量檔滿足篩選器規則時。

    - 檢查是否可以[從日期時間分區的檔案路徑或名稱複製檔案](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 這種方式不會給上市來源帶來負擔。

    - 檢查是否可以使用資料儲存的本機篩選器,特別是 Amazon S3 和 Azure Blob 的「**前置字**串」 。。 首碼篩選器是資料儲存伺服器端篩選器,性能會好得多。

    - 請考慮將單個大型數據集拆分為多個較小的數據集,並讓這些複製作業同時運行,每個數據處理部分。 您可以使用搜尋/獲取中繼資料 = 每個內容 = 複製來執行此操作。 請參閱[從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)或[將數據從 Amazon S3 遷移到 ADLS Gen2](solution-template-migration-s3-azure.md)解決方案範本作為一般範例。

  - 檢查 ADF 是否報告來源上有任何限制錯誤,或者資料儲存是否處於高利用率狀態。 如果是這樣,請減少數據存儲上的工作負載,或者嘗試聯絡數據存儲管理員以增加限制限制或可用資源。

  - 在來源資料儲存區域的相同或附近使用 Azure IR。

- **"從源轉移閱讀"經歷了長時間的工作:** 

  - 如果適用,採用特定於連接器的數據載入最佳做法。 例如,從[Amazon Redshift](connector-amazon-redshift.md)複製數據時,配置為使用紅移 UNLOAD。

  - 檢查 ADF 是否報告來源上有任何限制錯誤,或者您的資料儲存是否被高利用率。 如果是這樣,請減少數據存儲上的工作負載,或者嘗試聯絡數據存儲管理員以增加限制限制或可用資源。

  - 檢查複製來源與接收器模式: 

    - 如果您的複製模式支援大於 4 個資料集成單元 (DIUs) - 請參閱[本節](copy-activity-performance-features.md#data-integration-units)的詳細資訊,通常您可以嘗試增加 DIA 以獲得更好的性能。 

    - 否則,請考慮將單個大型數據集拆分為多個較小的數據集,並讓這些複製作業同時運行,每個數據處理部分。 您可以使用搜尋/獲取中繼資料 = 每個內容 = 複製來執行此操作。 請參閱[從多個容器複製檔](solution-template-copy-files-multiple-containers.md)、[將資料從 Amazon S3 移至 ADLS Gen2,](solution-template-migration-s3-azure.md)或[使用控制表](solution-template-bulk-copy-with-control-table.md)解決方案範本進行大量複製作為一般範例。

  - 在來源資料儲存區域的相同或附近使用 Azure IR。

- **"轉移-寫作到水槽"經歷了漫長的工作時間**:

  - 如果適用,採用特定於連接器的數據載入最佳做法。 例如,將數據複製到[Azure 突觸分析](connector-azure-sql-data-warehouse.md)(以前的 SQL DW)時,請使用 PolyBase 或 COPY 語句。 

  - 檢查 ADF 是否報告接收器上有任何限制錯誤,或者您的資料儲存是否被高利用率。 如果是這樣,請減少數據存儲上的工作負載,或者嘗試聯絡數據存儲管理員以增加限制限制或可用資源。

  - 檢查複製來源與接收器模式: 

    - 如果您的複製模式支援大於 4 個資料集成單元 (DIUs) - 請參閱[本節](copy-activity-performance-features.md#data-integration-units)的詳細資訊,通常您可以嘗試增加 DIA 以獲得更好的性能。 

    - 否則,逐漸調整[並行副本](copy-activity-performance-features.md),注意太多並行副本甚至可能損害性能。

  - 在同一或靠近接收體資料儲存區域時使用 Azure IR。

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>在自託管 IR 上排除複製活動故障

按照[性能調優步驟](copy-activity-performance.md#performance-tuning-steps)為方案規劃和執行性能測試。 

當複製性能不符合預期時,要對 Azure 整合執行時上運行的單個複製活動進行故障排除,如果看到複製監視檢視中顯示[的性能調優提示](#performance-tuning-tips),請應用建議,然後重試。 否則,[瞭解複製活動執行詳細資訊](#understand-copy-activity-execution-details),檢查哪個階段持續時間**最長**,並應用以下指南以提高複製性能:

- **"佇列"經歷了長時間的持續時間:** 這意味著複製活動在佇列中等待很長時間,直到您的自託管 IR 具有要執行的資源。 檢查紅外容量和使用方式,並根據工作負載[向上或擴展](create-self-hosted-integration-runtime.md#high-availability-and-scalability)。

- **"傳輸 - 第一位元節的時間"經歷了很長的工作持續時間**:這意味著源查詢返回任何資料需要很長時間。 檢查並優化查詢或伺服器。 如果您需要進一步説明,請聯絡數據儲存團隊。

- **"傳輸 - 清單源"經歷了長時間的工作**:這意味著枚舉源檔或源資料庫資料分區很慢。

  - 檢查自承載的 IR 計算機是否具有連接到來源資料儲存的低延遲。 如果源位於 Azure 中,則可以使用[此工具](http://www.azurespeed.com/Azure/Latency)檢查從自託管 IR 電腦到 Azure 區域的延遲,越少越好。

  - 從基於檔案的源複製資料時,如果在資料夾路徑或檔名`wildcardFolderPath``wildcardFileName`( 或 ) 上使用**通配符篩選器**,或使用`modifiedDatetimeStart`**檔案上次修改的時間篩選器**( 或`modifiedDatetimeEnd`),請注意,此類篩選器將導致將指定資料夾下的所有檔案清單的副本活動添加到用戶端,然後應用篩選器。 此類檔枚舉可能成為瓶頸,尤其是當只有少量檔滿足篩選器規則時。

    - 檢查是否可以[從日期時間分區的檔案路徑或名稱複製檔案](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)。 這種方式不會給上市來源帶來負擔。

    - 檢查是否可以使用資料儲存的本機篩選器,特別是 Amazon S3 和 Azure Blob 的「**前置字**串」 。。 首碼篩選器是資料儲存伺服器端篩選器,性能會好得多。

    - 請考慮將單個大型數據集拆分為多個較小的數據集,並讓這些複製作業同時運行,每個數據處理部分。 您可以使用搜尋/獲取中繼資料 = 每個內容 = 複製來執行此操作。 請參閱[從多個容器複製檔案](solution-template-copy-files-multiple-containers.md)或[將數據從 Amazon S3 遷移到 ADLS Gen2](solution-template-migration-s3-azure.md)解決方案範本作為一般範例。

  - 檢查 ADF 是否報告來源上有任何限制錯誤,或者資料儲存是否處於高利用率狀態。 如果是這樣,請減少數據存儲上的工作負載,或者嘗試聯絡數據存儲管理員以增加限制限制或可用資源。

- **"從源轉移閱讀"經歷了長時間的工作:** 

  - 檢查自承載的 IR 計算機是否具有連接到來源資料儲存的低延遲。 如果源位於 Azure 中,則可以使用[此工具](http://www.azurespeed.com/Azure/Latency)檢查從自託管 IR 電腦到 Azure 區域的延遲,越少越好。

  - 檢查自承載的 IR 電腦是否有足夠的入站頻寬來高效地讀取和傳輸數據。 如果來源資料儲存位於 Azure 中,則可以使用[此工具](https://www.azurespeed.com/Azure/Download)檢查下載速度。

  - 在 Azure 門戶中檢查自託管 IR 的 CPU 和記憶體使用趨勢 ->数据工厂 ->概述页。 如果 CPU 使用率高或可用記憶體不足,請考慮[向上 /縮小 IR。](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

  - 如果適用,採用特定於連接器的數據載入最佳做法。 例如：

    - 從[Oracle](connector-oracle.md#oracle-as-source)Oracle、Netezza、Teradata、SAP [Netezza](connector-netezza.md#netezza-as-source) [HANA、SAP](connector-sap-hana.md#sap-hana-as-source)[表](connector-sap-table.md#sap-table-as-source)和[SAP 開放集線器](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)複製數據時,啟用資料分區選項[Teradata](connector-teradata.md#teradata-as-source)以並行複製數據。

    - 從[HDFS](connector-hdfs.md)複製資料時 ,配置為使用 DistCp。

    - 從[亞馬遜紅移](connector-amazon-redshift.md)複製數據時,配置為使用紅移卸載。

  - 檢查 ADF 是否報告來源上有任何限制錯誤,或者您的資料儲存是否被高利用率。 如果是這樣,請減少數據存儲上的工作負載,或者嘗試聯絡數據存儲管理員以增加限制限制或可用資源。

  - 檢查複製來源與接收器模式: 

    - 如果從支援分區選項的數據存儲複製數據,請考慮逐步調整[並行副本](copy-activity-performance-features.md),請注意,太多的並行副本甚至可能損害性能。

    - 否則,請考慮將單個大型數據集拆分為多個較小的數據集,並讓這些複製作業同時運行,每個數據處理部分。 您可以使用搜尋/獲取中繼資料 = 每個內容 = 複製來執行此操作。 請參閱[從多個容器複製檔](solution-template-copy-files-multiple-containers.md)、[將資料從 Amazon S3 移至 ADLS Gen2,](solution-template-migration-s3-azure.md)或[使用控制表](solution-template-bulk-copy-with-control-table.md)解決方案範本進行大量複製作為一般範例。

- **"轉移-寫作到水槽"經歷了漫長的工作時間**:

  - 如果適用,採用特定於連接器的數據載入最佳做法。 例如,將數據複製到[Azure 突觸分析](connector-azure-sql-data-warehouse.md)(以前的 SQL DW)時,請使用 PolyBase 或 COPY 語句。 

  - 檢查自承載的 IR 計算機是否具有連接到接收器數據儲存的低延遲。 如果接收器位於 Azure 中,則可以使用[此工具](http://www.azurespeed.com/Azure/Latency)檢查從自承載的 IR 電腦到 Azure 區域的延遲,越少越好。

  - 檢查自承載的 IR 電腦是否有足夠的出站頻寬來高效地傳輸和寫入數據。 如果接收器數據存儲在 Azure 中,則可以使用[此工具](https://www.azurespeed.com/Azure/UploadLargeFile)檢查上載速度。

  - 檢查 Azure 門戶中的自託管 IR 的 CPU 和記憶體使用趨勢 ->数据工厂 -> 概覽頁。 如果 CPU 使用率高或可用記憶體不足,請考慮[向上 /縮小 IR。](create-self-hosted-integration-runtime.md#high-availability-and-scalability)

  - 檢查 ADF 是否報告接收器上有任何限制錯誤,或者您的資料儲存是否被高利用率。 如果是這樣,請減少數據存儲上的工作負載,或者嘗試聯絡數據存儲管理員以增加限制限制或可用資源。

  - 考慮逐漸調整[並行副本](copy-activity-performance-features.md),請注意,太多的並行副本甚至可能損害性能。

## <a name="other-references"></a>其他參考資料

以下是幾個支援的資料存放區所適用的效能監視及調整參考：

* Azure Blob 儲存[:Blob 儲存的可伸縮性和效能目標](../storage/blobs/scalability-targets.md),[以及 Blob 儲存的效能以及可伸縮性檢查表](../storage/blobs/storage-performance-checklist.md)。
* Azure 表儲存:[儲存的可伸縮性和效能目標](../storage/tables/scalability-targets.md)表[儲存的效能以及可伸縮性檢查表](../storage/tables/storage-performance-checklist.md)。
* Azure SQL 資料庫:您可以[監視性能](../sql-database/sql-database-single-database-monitor.md)並檢查資料庫事務單元 (DTU) 百分比。
* Azure SQL 資料倉庫:其功能以數據倉庫單位 (DWU) 度量。 請參考[Azure SQL 資料主目錄(概述)中管理運算能力](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)。
* Azure 宇宙[DB:Azure 宇宙 DB 中的效能等級](../cosmos-db/performance-levels.md)。
* 本地 SQL 伺服器:[監控與調整效能](https://msdn.microsoft.com/library/ms189081.aspx)。
* 本地端檔案伺服器:[檔案伺服器的效能調優](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>後續步驟
請參考其他複製活動文章:

- [複製活動概述](copy-activity-overview.md)
- [複製活動效能和可伸縮性指南](copy-activity-performance.md)
- [複製活動效能最佳化功能](copy-activity-performance-features.md)
- [使用 Azure 資料工廠將資料從資料湖或資料倉儲移到 Azure](data-migration-guidance-overview.md)
- [將資料從 Amazon S3 遷移到 Azure 儲存體](data-migration-guidance-s3-azure-storage.md)
