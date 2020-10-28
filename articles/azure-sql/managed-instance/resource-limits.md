---
title: 資源限制
titleSuffix: Azure SQL Managed Instance
description: 本文概要說明 Azure SQL 受控執行個體的資源限制。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: bonova
ms.author: bonova
ms.reviewer: sstein, jovanpop, sachinp
ms.date: 09/14/2020
ms.openlocfilehash: 34f71dfeb0b4e5f94d953137fd45777bf14baa4e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790758"
---
# <a name="overview-of-azure-sql-managed-instance-resource-limits"></a>Azure SQL 受控執行個體資源限制概觀
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本文概要說明 Azure SQL 受控執行個體的技術特性和資源限制，並提供如何要求增加這些限制的相關資訊。

> [!NOTE]
> 若想了解支援的功能和 T-SQL 陳述式的差異，請參閱[功能差異](../database/features-comparison.md)和 [T-SQL 陳述式支援](transact-sql-tsql-differences-sql-server.md)。 如需 Azure SQL Database 和 SQL 受控執行個體服務層級之間的一般差異，請參閱 [服務層級比較](../database/service-tiers-general-purpose-business-critical.md#service-tier-comparison)。

## <a name="hardware-generation-characteristics"></a>硬體世代特性

SQL 受控執行個體具有相依于基礎結構和架構的特性和資源限制。 SQL 受控執行個體可以部署在兩個硬體世代上：第4代和第5代。 硬體世代具有不同的特性，如下表所述：

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| **硬體** | Intel® E5-2673 v3 (Haswell) 2.4 GHz 處理器，附加的 SSD vCore = 1 PP (實體核心)  | Intel® E5-2673 v4 (Broadwell) 2.3 GHz、Intel® SP-8160 (Skylake) 和 Intel® 8272CL (Cascade Lake) 2.5 GHz 處理器、快速 NVMe SSD、vCore = 1 LP (超執行緒)  |
| **虛擬核心數目** | 8 個、16 個、24 個虛擬核心 | 4、8、16、24、32、40、64、80虛擬核心 |
| **記憶體 (記憶體/核心比率上限)** | 每個虛擬核心 7GB<br/>新增更多虛擬核心以取得更多記憶體。 | 每個虛擬核心 5.1 GB<br/>新增更多虛擬核心以取得更多記憶體。 |
| **最大 In-Memory OLTP 記憶體** | 實例限制：每個 vCore 1-1.5 GB| 實例限制：每個 vCore 0.8-1.65 GB |
| **實例保留的最大儲存空間** |  一般用途： 8 TB<br/>商務關鍵性： 1 TB | 一般用途： 8 TB<br/> 商務關鍵 1 TB、2 TB 或 4 TB，視核心數目而定 |

> [!IMPORTANT]
> - 第4代硬體即將推出，無法再供新的部署使用。 所有 SQL 受控執行個體的新實例都必須部署在第5代硬體上。
> - 請考慮將 [SQL 受控執行個體的實例移至 Gen 5](../database/service-tiers-vcore.md) 硬體，以體驗更廣泛的 vCore 和儲存體擴充性、加速網路、最佳 IO 效能和最短延遲。

### <a name="in-memory-oltp-available-space"></a>記憶體內部 OLTP 可用空間 

[商務關鍵](../database/service-tier-business-critical.md)服務層級中的記憶體內部 OLTP 空間數量，取決於虛擬核心和硬體產生的數目。 下表列出可用於記憶體內部 OLTP 物件的記憶體限制。

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

SQL 受控執行個體有兩個服務層級： [一般用途](../database/service-tier-general-purpose.md) 和 [業務關鍵](../database/service-tier-business-critical.md)。 這些層會提供 [不同的功能](../database/service-tiers-general-purpose-business-critical.md)，如下表所述。

> [!Important]
> 商務關鍵性服務層級提供額外的 SQL 受控執行個體內建複本 (次要複本) ，可用於唯讀工作負載。 如果您可以分隔讀寫查詢和唯讀/分析/報告查詢，則會取得相同價格的兩倍虛擬核心和記憶體。 次要複本可能會在主要實例後方延遲幾秒鐘，因此它的設計目的是要卸載不需要實際資料狀態的報表/分析工作負載。 在下表中， **唯讀查詢** 是在次要複本上執行的查詢。

| **功能** | **一般用途** | **業務關鍵** |
| --- | --- | --- |
| 虛擬核心數目\* | Gen4：8、16、24<br/>第5代：4、8、16、24、32、40、64、80 | Gen4：8、16、24 <br/> 第5代：4、8、16、24、32、40、64、80 <br/>\*相同數目的虛擬核心專用於唯讀查詢。 |
| 最大記憶體 | 第4代： 56 GB-168 GB (7GB/vCore) <br/>第5代： 20.4 GB-408 GB (5.1 GB/vCore) <br/>新增更多虛擬核心以取得更多記憶體。 | 第4代： 56 GB-168 GB (7GB/vCore) <br/>第5代： 20.4 GB-408 GB (5.1 GB/vCore) 用於讀寫查詢<br/>+ 額外 20.4 GB-408 GB (5.1 GB/vCore) 用於唯讀查詢。<br/>新增更多虛擬核心以取得更多記憶體。 |
|  (保留) 的實例儲存體大小上限 | -2 TB 適用于4虛擬核心 (僅第5代) <br/>-8 TB 適用于其他大小 | 第4代： 1 TB <br/> 第 5 代： <br/>-1 TB （4、8、16虛擬核心）<br/>- 2 TB (適用於 24 個虛擬核心)<br/>- 4 TB (適用於 32、40、64、80 個虛擬核心) |
| 資料庫大小上限 | 根據虛擬核心) 數目，最高可達目前可用的實例大小 (最大 2 TB-8 TB。 | 根據虛擬核心) 數目，最高可達目前可用的實例大小 (最大 1 TB-4 TB。 |
| 最大 tempDB 大小 | 受限於 24 GB/vCore (96-1920 GB) 和目前可用的實例儲存體大小。<br/>加入更多虛擬核心以取得更多 TempDB 空間。<br/> 記錄檔大小限制為 120 GB。| 目前可用的實例儲存體大小上限。 |
| 每個執行個體的資料庫數目上限 | 100，除非已達到實例儲存體大小限制。 | 100，除非已達到實例儲存體大小限制。 |
| 每個實例的資料庫檔案數目上限 | 最高280，除非已達到實例儲存體大小或 [Azure Premium 磁片儲存體配置空間](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files) 的限制。 | 32767每個資料庫的檔案，除非已達到實例儲存體大小限制。 |
| 資料檔案大小上限 | 受限於目前可用的實例儲存體大小 (最大值為 2 TB-8 TB) 和 [Azure Premium 磁片儲存體配置空間](../database/doc-changes-updates-release-notes.md#exceeding-storage-space-with-small-database-files)。 | 受限於目前可用的實例儲存體大小 (高達 1 TB-4 TB) 。 |
| 記錄檔大小上限 | 受限於 2 TB 和目前可用的實例儲存體大小。 | 受限於 2 TB 和目前可用的實例儲存體大小。 |
| 資料/記錄 IOPS (大約) | 每個實例最多 30-40 K IOPS *、500-7500/每個檔案<br/>\*[增加檔案大小以取得更多 IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 k-200 K (4000 IOPS/vCore) <br/>新增更多虛擬核心，以取得更佳的 IO 效能。 |
| 每個實例的記錄寫入輸送量限制 ()  | 每個虛擬核心 3 MB/秒<br/>每個實例最大 120 MB/秒<br/>每個資料庫 22-65 MB/秒<br/>\*[增加檔案大小以取得更好的 IO 效能](#file-io-characteristics-in-general-purpose-tier) | 每個 vCore 4 MB/秒<br/>最大 96 MB/秒 |
| 資料輸送量 (大約) | 每個檔案 100 - 250 MB/秒<br/>\*[增加檔案大小以取得更好的 IO 效能](#file-io-characteristics-in-general-purpose-tier) | 不受限制。 |
| 儲存體 IO 延遲 (大約)  | 5-10 毫秒 | 1-2 毫秒 |
| 記憶體內部 OLTP | 不支援 | 可用， [大小取決於 vCore 數目](#in-memory-oltp-available-space) |
| 會話數上限 | 30000 | 30000 |
| 並行背景工作 (要求) 數上限 | 第 4 代：210 * 虛擬核心數目 + 800<br>第 5 代：105 * 虛擬核心數目 + 800 | 第 4 代：210 * 虛擬核心計數 + 800<br>第 5 代：105 * 虛擬核心計數 + 800 |
| [唯讀複本](../database/read-scale-out.md) | 0 | 價格) 包含 1 ( |
| 計算隔離 | 第 5 代：<br/>-支援80虛擬核心<br/>-不支援其他大小<br/><br/>因為已淘汰，所以不支援第4代|第 5 代：<br/>-支援60、64、80虛擬核心<br/>-不支援其他大小<br/><br/>因為已淘汰，所以不支援第4代|


另外還有幾個考慮： 

- **目前可用的實例儲存體大小** 是保留實例大小與使用的儲存空間之間的差異。
- 使用者和系統資料庫中的資料和記錄檔大小都會包含在實例儲存體大小，並與儲存體大小上限相比較。 您可以使用 [sys.master_files](/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql) 系統檢視，判斷資料庫所使用的總空間。 錯誤記錄不會持續留存，也不計入大小。 備份並未計入儲存體大小。
- 一般用途層中的輸送量和 IOPS 也取決於 SQL 受控執行個體未明確限制的檔案 [大小](#file-io-characteristics-in-general-purpose-tier) 。
  您可以使用[自動容錯移轉群組](../database/auto-failover-group-configure.md)，在不同的 Azure 區域中建立另一個可讀取的複本
- 最大實例 IOPS 取決於設定檔配置和工作負載的散發。 舉例來說，如果您建立 7 x 1TB 檔案，且每個檔案的大小上限為 1 TB，且每7個小型檔案 (小於 128 GB) 每個 500 IOPS，則可以取得每個實例 38500 IOPS (7x5000 + 7x500) 如果您的工作負載可以使用所有檔案。 請注意，某些 IOPS 也會用於自動備份。

請 [在本文中找到 SQL 受控執行個體](instance-pools-overview.md#resource-limitations)集區中資源限制的詳細資訊。

### <a name="file-io-characteristics-in-general-purpose-tier"></a>一般用途層中的檔案 IO 特性

在一般用途服務層級中，每個資料庫檔案都會取得相依于檔案大小的專用 IOPS 和輸送量。 較大的檔案會獲得更多的 IOPS 和輸送量。 下表顯示資料庫檔案的 IO 特性：

| 檔案大小 | >= 0 且 <= 128 GiB | >128 和 <= 512 GiB | >0.5 和 <= 1 TiB    | >1 和 <= 2 TiB    | >2 和 <= 4 TiB | >4 和 <= 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| 每個檔案的 IOPS       | 500   | 2300              | 5000              | 7500              | 7500              | 12,500   |
| 每個檔案的輸送量 | 100 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒 | 480 MiB/秒 | 

如果您注意到某個資料庫檔案的 IO 延遲很高，或您看到 IOPS/輸送量達到限制，您可能會增加檔案 [大小](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)來改善效能。

此外，也有實例層級限制的最大記錄寫入輸送量 (這是 22 MB/s) ，因此您可能無法在整個記錄檔上到達最大檔案，因為您達到實例輸送量限制。

## <a name="supported-regions"></a>支援區域

SQL 受控執行個體只能在 [支援的區域](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)中建立。 若要在目前不支援的區域中建立 SQL 受控執行個體，您可以透過 [Azure 入口網站傳送支援要求](../database/quota-increase-request.md)。

## <a name="supported-subscription-types"></a>支援的訂用帳戶類型

SQL 受控執行個體目前僅支援在下列類型的訂閱上進行部署：

- [Enterprise 合約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [隨用隨付](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [雲端服務提供者 (CSP)](/partner-center/csp-documents-and-learning-resources)
- [Enterprise 開發/測試](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [隨用隨付開發/測試](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Visual Studio 訂閱者的每月 Azure 點數訂用帳戶](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>區域資源限制

> [!Note]
> 如需訂用帳戶區域可用性的最新資訊，請先核取 [ [選取區域](https://aka.ms/sqlcapacity)]。

支援的訂用帳戶類型可包含有限的每一區域資源數目。 SQL 受控執行個體的每個 Azure 區域都有兩個預設限制 (可以根據訂用帳戶類型的類型， [在 Azure 入口網站中](../database/quota-increase-request.md) 建立特殊的支援要求，視需要增加：

- **子網限制** ：在單一區域中部署 SQL 受控執行個體實例的子網數目上限。
- **vCore unit limit** ：可在單一區域中的所有實例上部署的最大 vCore 單位數目。 一個 GP vCore 使用一個 vCore 單位，而一個 BC vCore 採用4個 vCore 單位。 只要實例的總數在 vCore 單位限制內，就不會受到限制。

> [!Note]
> 這些限制是預設設定，而不是技術限制。 如果您在目前的區域中需要更多實例，可以在 Azure 入口網站中建立特殊的 [支援要求，以](../database/quota-increase-request.md) 視需要增加限制。 或者，您可以在另一個 Azure 區域中建立 SQL 受控執行個體的新實例，而不需要傳送支援要求。

下表顯示支援的訂用帳戶類型的 **預設區域限制** (預設限制可以使用以下所述的支援要求進行擴充) ：

|訂用帳戶類型| SQL 受控執行個體子網數目上限 | VCore 單位數目上限 * |
| :---| :--- | :--- |
|隨用隨付|3|320|
|CSP |某些區域有 8 (15 * * ) |某些區域中的 960 (1440 * * ) |
|隨用隨付開發/測試|3|320|
|Enterprise 開發/測試|3|320|
|EA|某些區域有 8 (15 * * ) |某些區域中的 960 (1440 * * ) |
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional 和 MSDN 平臺|2|32|

\* 在規劃部署中，請考慮到商務關鍵性 (BC) 服務層級所需的 vCore 容量比一般用途 (GP) 服務層級的四 (4) 倍多。 例如： 1 GP vCore = 1 vCore unit 和 1 BC vCore = 4 vCore 單位。 若要針對預設限制簡化耗用量分析，請在部署 SQL 受控執行個體的區域中匯總所有子網的 vCore 單位，並將結果與訂用帳戶類型的實例單位限制進行比較。 **VCore 單位限制的最大數目** 會套用至區域中的每個訂用帳戶。 每個個別子網都沒有限制，不同之處在于跨多個子網部署的所有虛擬核心總和必須小於或等於 **vCore 單位的最大數目** 。

\*\* 較大的子網和 vCore 限制可在下欄區域使用：澳大利亞東部、美國東部、美國東部2、北歐、美國中南部、東南亞、英國南部、西歐、美國西部2。

> [!IMPORTANT]
> 如果您的 vCore 和子網限制為0，則表示您的訂用帳戶類型的預設區域限制未設定。 您也可以在相同的程式之後，使用配額增加要求來取得特定區域中的訂用帳戶存取權，並提供必要的 vCore 和子網值。

## <a name="request-a-quota-increase"></a>要求增加配額

如果您在目前的區域中需要更多實例，請使用 Azure 入口網站傳送支援要求以延伸配額。 如需詳細資訊，請參閱 [Azure SQL Database 的要求配額增加](../database/quota-increase-request.md)。

## <a name="next-steps"></a>後續步驟

- 如需 SQL 受控執行個體的詳細資訊，請參閱 [何謂 sql 受控執行個體？](sql-managed-instance-paas-overview.md)。
- 如需定價資訊，請參閱 [SQL 受控執行個體定價](https://azure.microsoft.com/pricing/details/sql-database/managed/)。
- 若要瞭解如何建立您的第一個 SQL 受控執行個體，請參閱 [快速入門手冊](instance-create-quickstart.md)。