---
title: 資源限制-受控實例
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80365383"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL Database 受控實例資源限制的總覽

本文概述 Azure SQL Database 受控實例的技術特性和資源限制，並提供如何要求增加至這些限制的相關資訊。

> [!NOTE]
> 若想了解支援的功能和 T-SQL 陳述式的差異，請參閱[功能差異](sql-database-features.md)和 [T-SQL 陳述式支援](sql-database-managed-instance-transact-sql-information.md)。 如需單一資料庫和受控實例中服務層級之間的一般 differencess，請參閱[服務層級比較](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)。

## <a name="hardware-generation-characteristics"></a>硬體世代特性

受控實例具有相依于基礎結構和架構的特性和資源限制。 Azure SQL Database 受控實例可以部署在兩個硬體層代上：第4代和第5代。 硬體世代具有不同的特性，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 硬體 | Intel E5 2673 v3 (Haswell) 2.4-GHz 處理器，附加 SSD 虛擬核心 = 1 PP (實體核心) | Intel E5-2673 v4 （Broadwell） 2.3-GHz 和 Intel SP-8160 （Skylake）處理器，fast NVMe SSD，vCore = 1 LP （超執行緒） |
| 虛擬核心數目 | 8 個、16 個、24 個虛擬核心 | 4、8、16、24、32、40、64、80虛擬核心 |
| 最大記憶體（記憶體/核心比率） | 每個虛擬核心 7GB<br/>新增更多虛擬核心以取得更多記憶體。 | 每個虛擬核心 5.1 GB<br/>新增更多虛擬核心以取得更多記憶體。 |
| 記憶體內部 OLTP 記憶體上限 | 實例限制： 1-每個 vCore 1.5 GB| 實例限制： 0.8-每個 vCore 1.65 GB |
| 實例保留的最大儲存體 |  一般用途： 8 TB<br/>業務關鍵： 1 TB | 一般用途： 8 TB<br/> 商務關鍵性 1 TB、2 TB 或 4 TB，視核心數目而定 |

> [!IMPORTANT]
> - 第4代硬體即將推出，而不再適用于新的部署。 所有新的受控實例都必須部署在第5代硬體上。
> - 請考慮將[您的受控實例移至 Gen 5](sql-database-service-tiers-vcore.md)硬體，以體驗更廣泛的 vCore 和存放裝置擴充性、加速網路、最佳 IO 效能，以及最少的延遲。

### <a name="in-memory-oltp-available-space"></a>記憶體內部 OLTP 可用空間 

[商務關鍵](sql-database-service-tier-business-critical.md)服務層級中的記憶體內部 OLTP 空間數量，取決於虛擬核心和硬體產生的數目。 下表列出可用於記憶體內部 OLTP 物件的記憶體限制。

| 記憶體內部 OLTP 空間  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 個 V 核心  | 3.14 GB | |   
| 8 個 V 核心  | 6.28 GB | 8 GB |
| 16虛擬核心 | 15.77 GB | 20 GB |
| 24虛擬核心 | 25.25 GB | 36 GB |
| 32虛擬核心 | 37.94 GB | |
| 40虛擬核心 | 52.23 GB | |
| 64虛擬核心 | 99.9 GB    | |
| 80虛擬核心 | 131.68 GB| |

## <a name="service-tier-characteristics"></a>服務層的特性

受控實例有兩個服務層級：[一般用途](sql-database-service-tier-general-purpose.md)與[業務關鍵](sql-database-service-tier-business-critical.md)。 這些層級提供[不同的功能](sql-database-service-tiers-general-purpose-business-critical.md)，如下表所述。

> [!Important]
> 商務關鍵服務層級提供額外的內建實例（次要複本）複本，可用於唯讀工作負載。 如果您可以分隔讀寫查詢和唯讀/分析/報告查詢，就會得到兩個虛擬核心和記憶體的相同價格。 次要複本可能會在主要實例後方的幾秒鐘後延遲，因此它的設計是要卸載不需要資料的確切目前狀態的報告/分析工作負載。 在下表中，**唯讀查詢**是在次要複本上執行的查詢。

| **功能** | **一般用途** | **業務關鍵** |
| --- | --- | --- |
| 虛擬核心數目\* | Gen4：8、16、24<br/>第5代：4、8、16、24、32、40、64、80 | Gen4：8、16、24 <br/> 第5代：4、8、16、24、32、40、64、80 <br/>\*相同數目的虛擬核心專門用於唯讀查詢。 |
| 最大記憶體 | 第4代： 56 GB-168 GB （7GB/vCore）<br/>第5代： 20.4 GB-408 GB （5.1 GB/vCore）<br/>新增更多虛擬核心以取得更多記憶體。 | 第4代： 56 GB-168 GB （7GB/vCore）<br/>第5代： 20.4 GB-408 GB （5.1 GB/vCore），適用于讀寫查詢<br/>+ 額外 20.4 GB-408 GB （5.1 GB/vCore）用於唯讀查詢。<br/>新增更多虛擬核心以取得更多記憶體。 |
| 最大實例儲存體大小（保留） | -2 TB 適用于4虛擬核心（僅限第5代）<br/>-適用于其他大小的 8 TB | 第4代： 1 TB <br/> 第 5 代： <br/>-1 TB 適用于4、8、16虛擬核心<br/>- 2 TB (適用於 24 個虛擬核心)<br/>- 4 TB (適用於 32、40、64、80 個虛擬核心) |
| 資料庫大小上限 | 目前可用的實例大小上限（最多 2 TB-8 TB，視虛擬核心的數目而定）。 | 目前可用的實例大小上限（最多 1 TB-4 TB，視虛擬核心的數目而定）。 |
| 最大 tempDB 大小 | 限制為 24 GB/vCore （96-1920 GB）和目前可用的實例儲存體大小。<br/>新增更多虛擬核心以取得更多 TempDB 空間。<br/> 記錄檔大小限制為 120 GB。| 最高可達目前可用的實例儲存體大小。 |
| 每個執行個體的資料庫數目上限 | 100，除非已達到實例儲存體大小限制。 | 100，除非已達到實例儲存體大小限制。 |
| 每個實例的資料庫檔案數目上限 | 最多280，除非已達到實例儲存體大小或[Azure Premium 磁片儲存體配置空間](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files)的限制。 | 32767每個資料庫的檔案，除非已達到實例儲存體大小限制。 |
| 資料檔案大小上限 | 受限於目前可用的實例儲存體大小（最多 2 TB-8 TB）和[Azure Premium 磁片儲存體配置空間](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files)。 | 受限於目前可用的實例儲存體大小（最多 1 TB-4 TB）。 |
| 記錄檔大小上限 | 受限於 2 TB 和目前可用的實例儲存體大小。 | 受限於 2 TB 和目前可用的實例儲存體大小。 |
| 資料/記錄 IOPS (大約) | 每個實例最多 30-40 K IOPS *，500-每個檔案7500<br/>\*[增加檔案大小以取得更多 IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K （2500 IOPS/vCore）<br/>新增更多虛擬核心，以取得更佳的 IO 效能。 |
| 記錄寫入輸送量限制（每個實例） | 每個虛擬核心 3 MB/秒<br/>最大值 22 MB/秒 | 每個 vCore 4 MB/秒<br/>最大 48 MB/秒 |
| 資料輸送量 (大約) | 每個檔案 100 - 250 MB/秒<br/>\*[增加檔案大小以取得更佳的 IO 效能](#file-io-characteristics-in-general-purpose-tier) | 不受限制。 |
| 儲存 IO 延遲（近似） | 5-10 毫秒 | 1-2 毫秒 |
| 記憶體內部 OLTP | 不支援 | 可用，[大小取決於 vCore 數目](#in-memory-oltp-available-space) |
| 會話數上限 | 30000 | 30000 |
| [唯讀複本](sql-database-read-scale-out.md) | 0 | 1（包含在價格中） |

> [!NOTE]
> - **目前可用的實例儲存體大小**是保留實例大小與已使用的儲存空間之間的差異。
> - 使用者和系統資料庫中的資料和記錄檔大小都會計入執行個體儲存體大小，並與儲存體大小上限相比較。 使用 <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 系統檢視來判斷資料庫所使用的總空間。 錯誤記錄不會持續留存，也不計入大小。 備份並未計入儲存體大小。
> - 一般用途層的輸送量和 IOPS 也取決於受控實例未明確限制的檔案[大小](#file-io-characteristics-in-general-purpose-tier)。
> - 您可以使用自動容錯移轉群組，在不同的 Azure 區域中建立另一個可讀取的複本。
> - 最大實例 IOPS 取決於工作負載的檔案配置和散發。 例如，如果您建立 7 x TB 的檔案，其中每個都有最大的「大小上限」 IOPS 和每個7個小型檔案（小於 128 GB），而且每個都有 500 IOPS，如果您的工作負載可以使用所有檔案，您可以取得每個實例 38500 IOPS （7x5000 + 7x500 請注意，某些 IOPS 數量也會用於自動備份。

> [!NOTE]
> [在本文中，尋找受控實例集區中資源限制](sql-database-instance-pools.md#instance-pools-resource-limitations)的詳細資訊。

### <a name="file-io-characteristics-in-general-purpose-tier"></a>一般用途層中的檔案 IO 特性

在一般用途服務層級中，每個資料庫檔案都會取得相依于檔案大小的專用 IOPS 和輸送量。 較大的檔案會取得更多的 IOPS 和輸送量。 下表顯示資料庫檔案的 IO 特性：

| 檔案大小 | >= 0，<= 128 GiB | >128 和 <= 256 GiB | >256 和 <= 512 GiB | >0.5 和 <= 1 TiB    | >1 和 <= 2 TiB    | >2 和 <= 4 TiB | >4 和 <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 每個檔案的 IOPS       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12,500   |
| 每個檔案的輸送量 | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 MiB/秒 | 

如果您注意到某個資料庫檔案上有高 IO 延遲，或發現 IOPS/輸送量達到此限制，您可以藉由[增加檔案大小](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)來改善效能。

此外，也有實例層級的限制，例如最大記錄寫入輸送量 22 MB/s，因此您可能無法在整個記錄檔上連線到檔案，因為您達到實例輸送量限制。

## <a name="supported-regions"></a>支援區域

受控實例只能在[支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)中建立。 若要在目前不支援的區域中建立受控實例，您可以透過[Azure 入口網站傳送支援要求](quota-increase-request.md)。

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

受控實例目前僅支援下列訂閱類型的部署：

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [雲端服務提供者（CSP）](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 訂閱者的每月 Azure 點數訂用帳戶](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>區域資源限制

支援的訂用帳戶類型可包含有限的每一區域資源數目。 受控實例有兩個每個 Azure 區域的預設限制（根據訂用帳戶類型的類型，您可以視需要在[Azure 入口網站中建立特殊支援要求](quota-increase-request.md)來增加）：

- **子網路限制**：在單一區域中部署受控執行個體的子網路數目上限。
- **vCore 單位限制**：可以在單一區域中的所有實例之間部署的 vCore 單位數目上限。 一個 GP vCore 使用一個 vCore 單位，而一個 BC vCore 採用4個 vCore 單位。 實例總數不會受到限制，只要它在 vCore 單位限制內即可。

> [!Note]
> 這些限制是預設設定，而不是技術限制。 如果您在目前的區域中需要更多受控實例，您可以在 Azure 入口網站中建立特殊的[支援要求，以](quota-increase-request.md)增加視需要的限制。 或者，您可以在另一個 Azure 區域中建立新的受控實例，而不需要傳送支援要求。

下表顯示支援的訂用帳戶類型的**預設地區限制**（可以使用以下所述的支援要求來擴充預設限制）：

|訂用帳戶類型| 受控實例子網的最大數目 | VCore 單位數目上限 * |
| :---| :--- | :--- |
|隨用隨付|3|320|
|CSP |8（在某些區域中為 15 * *）|960（在某些區域中為 1440 * *）|
|隨用隨付開發/測試|3|320|
|Enterprise 開發/測試|3|320|
|EA|8（在某些區域中為 15 * *）|960（在某些區域中為 1440 * *）|
|Visual Studio 企業版|2 |64|
|Visual Studio Professional 和 MSDN 平臺|2|32|

\*在規劃部署中，請考慮商務關鍵性（BC）服務層級需要四（4）倍以上的 vCore 容量，而不是一般用途（GP）服務層級。 例如：1個 GP vCore = 1 個 vCore unit 和1個 BC vCore = 4 個 vCore 單位。 若要簡化您對預設限制的耗用量分析，請摘要說明部署受控實例之區域中所有子網的 vCore 單位，並將結果與訂用帳戶類型的實例單位限制進行比較。 **VCore 單位限制的最大數目**適用于區域中的每個訂用帳戶。 每個個別子網沒有限制，不同之處在于跨多個子網部署的所有虛擬核心總和必須小於或等於**vCore 單位的最大數目**。

\*\*較大的子網和 vCore 限制適用于下欄區域：澳大利亞東部、美國東部、美國東部2、北歐、美國中南部、東南亞、英國南部、西歐、美國西部2。

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>要求 SQL 受控實例的配額增加

如果您在目前的區域中需要更多受控實例，請使用 Azure 入口網站來傳送支援要求以擴充配額。 如需詳細資訊，請參閱[Azure SQL Database 的要求配額增加](quota-increase-request.md)。

## <a name="next-steps"></a>後續步驟

- 如需受控實例的詳細資訊，請參閱[什麼是受控實例？](sql-database-managed-instance.md)。
- 如需定價資訊，請參閱[SQL Database 受控實例定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要瞭解如何建立您的第一個受控實例，請參閱[快速入門手冊](sql-database-managed-instance-get-started.md)。
