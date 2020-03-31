---
title: 資源限制 - 託管實例
description: 本文將概略說明 Azure SQL Database 的受控執行個體有哪些資源限制。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365383"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>概述 Azure SQL 資料庫託管實例資源限制

本文概述了 Azure SQL 資料庫託管實例的技術特性和資源限制，並提供有關如何請求增加對這些限制的資訊。

> [!NOTE]
> 若想了解支援的功能和 T-SQL 陳述式的差異，請參閱[功能差異](sql-database-features.md)和 [T-SQL 陳述式支援](sql-database-managed-instance-transact-sql-information.md)。 有關單個資料庫中的服務層和託管實例中的一般差異，請參閱[服務層比較](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)。

## <a name="hardware-generation-characteristics"></a>硬體世代特性

託管實例具有依賴于底層基礎結構和體系結構的特徵和資源限制。 Azure SQL 資料庫託管實例可以部署在兩個硬體代：第 4 代和第 5 代。 硬體生成具有不同的特性，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬體 | Intel E5 2673 v3 (Haswell) 2.4-GHz 處理器，附加 SSD 虛擬核心 = 1 PP (實體核心) | 英特爾 E5-2673 v4 （Broadwell） 2.3-GHz 和英特爾 SP-8160 （天湖） 處理器， 快速 NVMe 固態， vCore=1 LP （超執行緒） |
| 虛擬核心數目 | 8 個、16 個、24 個虛擬核心 | 4， 8， 16， 24， 32， 40， 64， 80 vCores |
| 最大記憶體（記憶體/核心比率） | 每個虛擬核心 7GB<br/>添加更多 vCore 以獲得更多的記憶體。 | 每個虛擬核心 5.1 GB<br/>添加更多 vCore 以獲得更多的記憶體。 |
| 最大記憶體內 OLTP 記憶體 | 實例限制：每個 vCore 1-1.5 GB| 實例限制：每個 vCore 0.8 - 1.65 GB |
| 最大實例預留存儲 |  一般用途： 8 TB<br/>業務關鍵：1 TB | 一般用途： 8 TB<br/> 業務關鍵型 1 TB、2 TB 或 4 TB，具體取決於內核數 |

> [!IMPORTANT]
> - Gen4 硬體正在逐步淘汰，不再可用於新部署。 所有新的託管實例都必須部署在 Gen5 硬體上。
> - 請考慮[將託管實例移動到第 5 代](sql-database-service-tiers-vcore.md)硬體，以體驗更廣泛的 vCore 和存儲可擴充性、加速網路、最佳 IO 性能和最小延遲。

### <a name="in-memory-oltp-available-space"></a>記憶體中 OLTP 可用空間 

[業務關鍵型](sql-database-service-tier-business-critical.md)服務層中的記憶體中 OLTP 空間量取決於 vCore 的數量和硬體生成。 下表列出了可用於記憶體中 OLTP 物件的記憶體限制。

| 記憶體中 OLTP 空間  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 個 V 核心  | 3.14 GB | |   
| 8 個 V 核心  | 6.28 GB | 8 GB |
| 16 vCore | 15.77 GB | 20 GB |
| 24 vCore | 25.25 GB | 36 GB |
| 32 vCore | 37.94 GB | |
| 40 vCore | 52.23 GB | |
| 64 vCore | 99.9 GB    | |
| 80 vCore | 131.68 GB| |

## <a name="service-tier-characteristics"></a>服務層的特性

託管實例有兩個服務層：[通用和](sql-database-service-tier-general-purpose.md)[業務關鍵](sql-database-service-tier-business-critical.md)。 這些層提供[不同的功能](sql-database-service-tiers-general-purpose-business-critical.md)，如下表所述。

> [!Important]
> 業務關鍵型服務層提供了可用於唯讀工作負載的其他實例（輔助副本）的內置副本。 如果可以分離讀寫查詢和唯讀/分析/報告查詢，則以相同的價格獲得兩次 vCore 和記憶體。 輔助副本可能滯後于主實例幾秒鐘，因此旨在卸載不需要確切當前資料狀態的報告/分析工作負載。 在下表中，**唯讀查詢**是在輔助副本上執行的查詢。

| **特徵** | **通用用途** | **商務關鍵性** |
| --- | --- | --- |
| 虛擬核心數目\* | Gen4：8、16、24<br/>第5代： 4， 8， 16， 24， 32， 40， 64， 80 | Gen4：8、16、24 <br/> 第5代： 4， 8， 16， 24， 32， 40， 64， 80 <br/>\*相同數量的 vCore 專用於唯讀查詢。 |
| 最大記憶體 | 第4代： 56 GB - 168 GB （7GB/vCore）<br/>第5代： 20.4 GB - 408 GB （5.1GB/vCore）<br/>添加更多 vCore 以獲得更多的記憶體。 | 第4代： 56 GB - 168 GB （7GB/vCore）<br/>第5代： 20.4 GB - 408 GB （5.1GB/vCore）， 用於讀寫查詢<br/>• 額外的 20.4 GB - 408 GB（5.1GB/vCore），用於唯讀查詢。<br/>添加更多 vCore 以獲得更多的記憶體。 |
| 最大實例存儲大小（保留） | - 2 TB，用於 4 個 vCore（僅限第 5 代）<br/>- 8 TB 用於其他尺寸 | 第4代： 1 TB <br/> 第 5 代： <br/>- 1 TB 用於 4、8、16 vCore<br/>- 2 TB (適用於 24 個虛擬核心)<br/>- 4 TB (適用於 32、40、64、80 個虛擬核心) |
| 資料庫大小上限 | 當前可用的實例大小（最多 2 TB - 8 TB，具體取決於 vCore 的數量）。 | 當前可用的實例大小（最多 1 TB - 4 TB，具體取決於 vCore 的數量）。 |
| 最大 tempDB 大小 | 僅限 24 GB/vCore （96 - 1，920 GB）和當前可用的實例存儲大小。<br/>添加更多 vCore 以獲得更多的 TempDB 空間。<br/> 日誌檔案大小限制為 120 GB。| 當前可用的實例存儲大小。 |
| 每個執行個體的資料庫數目上限 | 100，除非已達到實例存儲大小限制。 | 100，除非已達到實例存儲大小限制。 |
| 每個實例的資料庫檔案的最大數量 | 最多 280 個，除非已達到實例存儲大小或[Azure 高級磁片存儲分配空間](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files)限制。 | 每個資料庫有 32，767 個檔，除非已達到實例存儲大小限制。 |
| 最大資料檔案大小 | 限制為當前可用的實例存儲大小（最大 2 TB - 8 TB）和[Azure 高級磁片存儲分配空間](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files)。 | 限制為當前可用的實例存儲大小（最多 1 TB - 4 TB）。 |
| 最大日誌檔案大小 | 限制為 2 TB，當前可用的實例存儲大小。 | 限制為 2 TB，當前可用的實例存儲大小。 |
| 資料/記錄 IOPS (大約) | 每個實例最多 30-40 K IOPS*，每個檔 500 - 7500<br/>\*[增加檔案大小以獲得更多的 IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K （2500 IOPS/vCore）<br/>添加更多 vCore 以獲得更好的 IO 性能。 |
| 日誌寫入輸送量限制（每個實例） | 每個虛擬核心 3 MB/秒<br/>最大 22 MB/s | 每個 vCore 4 MB/s<br/>最大 48 MB/秒 |
| 資料輸送量 (大約) | 每個檔案 100 - 250 MB/秒<br/>\*[增加檔案大小以獲得更好的 IO 性能](#file-io-characteristics-in-general-purpose-tier) | 不受限制。 |
| 存儲 IO 延遲（近似） | 5-10 毫秒 | 1-2 毫秒 |
| 記憶體內部 OLTP | 不支援 | 可用，[大小取決於 vCore 的數量](#in-memory-oltp-available-space) |
| 最大會話數 | 30000 | 30000 |
| [唯讀副本](sql-database-read-scale-out.md) | 0 | 1 （包含在價格中） |

> [!NOTE]
> - **當前可用的實例存儲大小**是預留實例大小和已使用的存儲空間之間的差異。
> - 使用者和系統資料庫中的資料和記錄檔大小都會計入執行個體儲存體大小，並與儲存體大小上限相比較。 使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系統檢視來判斷資料庫所使用的總空間。 錯誤記錄不會持續留存，也不計入大小。 備份並未計入儲存體大小。
> - 通用層上的輸送量和 IOPS 還取決於未受託管實例顯式限制[的檔案大小](#file-io-characteristics-in-general-purpose-tier)。
> - 您可以使用自動容錯移轉組在不同的 Azure 區域中創建另一個可讀副本。
> - 最大實例 IOPS 取決於檔佈局和工作負載分佈。 例如，如果創建 7 x 1TB 檔，每個檔最多 5K IOPS，7 個小檔（小於 128 GB），每個實例 500 個 IOPS，則如果工作負荷可以使用所有檔，則可以為每個實例獲得 38500 IOPS（7x5000+7x500）。 請注意，一定數量的 IOPS 也用於自動備份。

> [!NOTE]
> 在本文中，查找有關[託管實例池中資源限制的詳細資訊](sql-database-instance-pools.md#instance-pools-resource-limitations)。

### <a name="file-io-characteristics-in-general-purpose-tier"></a>通用層中的檔 IO 特徵

在泛型服務層中，每個資料庫檔案都獲取了專用的 IOPS 和輸送量，具體取決於檔案大小。 更大的檔正在獲得更多的IOPS和輸送量。 資料庫檔案的 IO 特徵顯示在下表中：

| 檔案大小 | >±0 和 <=128 GiB | >128 和 <+256 GiB | >256 和 <= 512 GiB | >0.5 和 <±1 TiB    | >1 和 <±2 TiB    | >2 和 <#4 TiB | >4 和 <+8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 每個檔的 IOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500   |
| 每個檔的輸送量 | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 米B/s | 

如果您注意到某些資料庫檔案上的 IO 延遲很高，或者發現 IOPS/輸送量正在達到限制，則可以通過增加[檔案大小](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)來提高性能。

還有實例級限制，如最大日誌寫入輸送量 22 MB/s，因此可能無法在整個日誌檔上訪問檔，因為您正在達到實例輸送量限制。

## <a name="supported-regions"></a>支援區域

託管實例只能在[受支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)創建。 要在當前不支援的區域中創建託管實例，可以通過[Azure 門戶發送支援請求](quota-increase-request.md)。

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

託管實例當前僅支援以下類型的訂閱部署：

- [企業協定](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [雲服務提供者 （CSP）](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [視覺化工作室訂閱者每月使用 Azure 積分的訂閱](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>區域資源限制

支援的訂用帳戶類型可包含有限的每一區域資源數目。 託管實例每個 Azure 區域有兩個預設限制（根據訂閱類型，可以通過[在 Azure 門戶中創建特殊支援請求](quota-increase-request.md)按需增加限制：

- **子網路限制**：在單一區域中部署受控執行個體的子網路數目上限。
- **vCore 單元限制**：可在單個區域中所有實例中部署的最大 vCore 單元數。 一個 GP vCore 使用一個 vCore 單元，一個 BC vCore 需要 4 個 vCore 單元。 只要實例在 vCore 單元限制範圍內，實例總數不受限制。

> [!Note]
> 這些限制是預設設置，而不是技術限制。 如果需要目前範圍中的更多託管實例，則可以[通過在 Azure 門戶中創建特殊支援請求](quota-increase-request.md)來按需增加限制。 作為替代方法，您可以在另一個 Azure 區域中創建新的託管實例，而無需發送支援請求。

下表顯示了支援的訂閱類型的**預設區域限制**（可以使用下面描述的支援請求擴展預設限制）：

|訂用帳戶類型| 託管實例子網的最大數量 | vCore 單元的最大數量* |
| :---| :--- | :--- |
|隨用隨付|3|320|
|CSP |8 （某些地區為 15*）|960 （某些地區為 1440）*|
|隨用隨付開發/測試|3|320|
|Enterprise 開發/測試|3|320|
|EA|8 （某些地區為 15*）|960 （某些地區為 1440）*|
|Visual Studio Enterprise|2 |64|
|視覺工作室專業和MSDN平臺|2|32|

\*在規劃部署時，請考慮業務關鍵型 （BC） 服務層需要的 vCore 容量比通用 （GP） 服務層多四 （4） 倍。 例如：1 個 GP vCore = 1 個 vCore 單元和 1 個 BC vCore = 4 個 vCore 單元。 要簡化流量分析（針對預設限制），請匯總部署託管實例的區域中所有子網的 vCore 單元，並將結果與訂閱類型的實例單元限制進行比較。 **vCore 單位限制的最大數量**適用于區域中的每個訂閱。 每個子網沒有限制，只是跨多個子網部署的所有 vCore 的總和必須低於或等於**vCore 單元的最大數量**。

\*\*較大的子網和 vCore 限制可用於以下區域：澳大利亞東部、美國東部、美國東部 2、北歐、美國中南部、東南亞、英國南部、西歐、美國西部 2。

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>請求增加 SQL 託管實例的配額

如果目前範圍中需要更多託管實例，請發送支援請求以使用 Azure 門戶擴展配額。 有關詳細資訊，請參閱請求[Azure SQL 資料庫的配額增加](quota-increase-request.md)。

## <a name="next-steps"></a>後續步驟

- 有關託管實例的詳細資訊，請參閱[什麼是託管實例？](sql-database-managed-instance.md)
- 有關定價資訊，請參閱[SQL 資料庫託管實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 要瞭解如何創建第一個託管實例[，請參閱快速入門手冊](sql-database-managed-instance-get-started.md)。
