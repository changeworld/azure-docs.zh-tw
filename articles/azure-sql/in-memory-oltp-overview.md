---
title: 記憶體內部技術
description: 記憶體內部技術可大幅提升 Azure SQL Database 和 Azure SQL 受控執行個體中交易與分析工作負載的效能。
services: sql-database
ms.service: sql-db-mi
ms.subservice: ''
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/19/2019
ms.openlocfilehash: 48b74a5507eb4a1d48b7bf70133e476a30fe8169
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779946"
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-azure-sql-database-and-azure-sql-managed-instance"></a>使用 Azure SQL Database 和 Azure SQL 受控執行個體中的記憶體內部技術將效能優化
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

記憶體內部技術可讓您改善應用程式的效能，而且可能會降低資料庫的成本。

## <a name="when-to-use-in-memory-technologies"></a>使用記憶體內部技術的時機

藉由使用記憶體內部技術，您可以透過各種工作負載達到效能改進：

- **交易式** (線上交易處理 (OLTP))，其中大部分要求會讀取或更新較小的資料集 (例如 CRUD 作業)。
- **分析** (線上分析處理 (OLAP))，其中大多數查詢具有用於報告的複雜計算，且有一定數量的查詢將載入資料並將其附加至現有資料表 (所謂的大量載入)，或刪除資料表中的資料。
- **混合** (混合式交易/分析處理 (HTAP))，其中 OLTP 與 OLAP 查詢都在相同的資料集上執行。

記憶體內部技術可以改善這些工作負載的效能，方法是使用基礎硬體上可用的查詢原生編譯或進階處理 (例如，批次處理和 SIMD 指令)，將應該處理的資料保留到記憶體中。

## <a name="overview"></a>概觀

Azure SQL Database 和 Azure SQL 受控執行個體具有下列記憶體內部技術：

-  可增加每秒交易數量並減少交易處理的延遲。 受益於記憶體內部 OLTP 的案例包括︰高輸送量的交易處理 (例如股票交易和網路遊戲)、從事件或 IoT 裝置擷取資料、快取、資料載入，以及暫存資料表和資料表變數等案例。
- 「叢集資料行存放區索引」  可減少儲存體使用量 (最多 10 倍)，並提升報告和分析查詢的效能。 您可以將它用於資料超市中的事實資料表，在資料庫中容納更多資料並提升效能。 另外，您還可以將它用於操作資料庫中的歷史資料，則可封存並查詢多達 10 倍以上的資料。
- 「非叢集資料行存放區索引」  (適用於 HTAP) 可讓您透過直接查詢操作資料庫，即時深入了解您的業務，而不必執行昂貴的擷取、轉換和載入 (ETL) 程序並等候資料倉儲填入資料。 非叢集資料行存放區索引可快速地對 OLTP 資料庫執行分析查詢，同時降低對操作工作負載的影響。
- 適用於 HTAP 的「記憶體最佳化叢集資料行存放區索引」  可讓您執行快速的交易處理，以及快速地「同時」  針對相同的資料執行分析查詢。

資料行存放區和記憶體內部 OLTP 已分別於 2012 年和 2014 年起納入成為 SQL Server 產品的一部分。 Azure SQL Database、Azure SQL 受控執行個體和 SQL Server 共用相同的記憶體內部技術執行。

## <a name="benefits-of-in-memory-technology"></a>記憶體內部技術的優點

由於查詢和交易處理更有效率，記憶體內部技術也可協助您降低成本。 您通常不需要升級資料庫的定價層，就能提升效能。 在某些情況下，您甚至可以降低定價層，但仍會看到記憶體內部技術的效能改進。

以下是記憶體內部 OLTP 如何幫助大幅提升效能的兩個範例︰

- 由於使用記憶體內部 OLTP，[Quorum Business Solutions 能使其工作負載倍增，同時將 DTU 提高 70%](https://resources.quorumsoftware.com/case-studies/quorum-doubles-key-database-s-workload-while-lowering-dtu)。
- 下列影片示範使用範例工作負載（ [記憶體內部 OLTP 影片）](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB)大幅改善資源耗用量。 如需詳細資訊，請參閱 blog 文章： [記憶體內部 OLTP](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

> [!NOTE]  
> 記憶體內部技術適用于 Premium 和業務關鍵層。

下列影片說明記憶體內部技術的潛在效能提升。 請記住，能提升多少效能永遠取決於許多因素，包括工作負載和資料的性質、資料庫的存取模式等等。

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>

本文說明 Azure SQL Database 和 Azure SQL 受控執行個體專屬 In-Memory OLTP 和資料行存放區索引的各個層面，也包含範例：

- 您將了解這些技術對儲存體和資料大小限制的影響。
- 您將了解如何管理在不同定價層之間移動採用這些技術的資料庫。
- 您會看到兩個範例，說明如何使用 In-Memory OLTP 以及資料行存放區索引。

如需 SQL Server 中記憶體的詳細資訊，請參閱：

- [記憶體內部 OLTP 概觀和使用案例](/sql/relational-databases/in-memory-oltp/overview-and-usage-scenarios) (包括客戶案例研究參考和入門資訊)
- [記憶體內部 OLTP 的文件](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [資料行存放區索引指南](/sql/relational-databases/indexes/columnstore-indexes-overview)
- 混合式交易/分析處理 (HTAP)，也稱為[即時作業分析](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)

## <a name="in-memory-oltp"></a>記憶體內部 OLTP

In-Memory OLTP 技術可將所有資料保留在記憶體中，以提供極快速的資料存取作業。 它還會使用特殊索引、查詢的原生編譯及無閂鎖的資料存取來提升 OLTP 工作負載的效能。 有兩種方式可用來組織您的記憶體內部 OLTP 資料：

- **記憶體最佳化資料列存放區** 格式，其中每個資料列為不同的記憶體物件。 這是針對高效能 OLTP 工作負載進行最佳化的傳統記憶體內部 OLTP 格式。 有兩種類型的記憶體最佳化資料表，可用於記憶體最佳化資料列存放區格式：

  - 「持久性資料表」  (SCHEMA_AND_DATA)，其中置於記憶體內的資料列會在伺服器重新啟動後予以保留。 這類型的資料表行為類似於傳統資料列存放區資料表，但具有記憶體內部最佳化的額外好處。
  - *非持久性資料表* (SCHEMA_ONLY) 在重新開機後不會保留資料列。 這種類型的資料表專為下列項目而設計：暫存資料 (例如，取代暫存資料表)，或是您需要快速載入資料，再將它移至某個永續性資料表的資料表 (所謂的暫存資料表)。

- **記憶體最佳化資料行存放區** 格式，其中的資料會組織成單欄式格式。 此結構設計用於 HTAP 案例，在此案例中，您需要在執行 OLTP 工作負載的相同資料結構上執行分析查詢。

> [!Note]
> 記憶體內部 OLTP 技術則設計用於可完全位於記憶體中的資料結構。 因為記憶體內部資料不能卸載到磁碟，所以請確定您使用的資料庫具有足夠記憶體。 如需詳細資料，請參閱[記憶體內部 OLTP 的資料大小和儲存體上限](#data-size-and-storage-cap-for-in-memory-oltp)。

In-Memory OLTP 的快速入門：快速 [入門1： In-Memory OLTP 技術，以加快 T-sql 效能](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp) (另一篇文章來協助您開始使用) 

技術的相關深入介紹影片︰

- [記憶體內部 OLTP](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (，其中包含效能優點的示範，以及自行重現這些結果的步驟) 
- [記憶體內部 OLTP 影片︰其功能、使用時機和使用方式](/archive/blogs/sqlserverstorageengine/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it)

您可以透過程式設計的方式，來了解給定資料庫是否支援記憶體內部 OLTP。 您可以執行下列 Transact-SQL 查詢︰

```sql
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

如果查詢傳回 **1** ，則此資料庫支援記憶體內部 OLTP。 下列查詢會識別在資料庫降級為一般用途、標準或基本之前需要移除的所有物件：

```sql
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>記憶體內部 OLTP 的資料大小和儲存體上限

記憶體內部 OLTP 包含記憶體最佳化資料表，以用來儲存使用者資料。 這些資料表必須可容納於記憶體。 由於您是直接在 SQL Database 中管理記憶體，因此我們有使用者資料配額的概念。 這個概念稱為「記憶體內部 OLAP 儲存體」  。

每個受支援的單一資料庫定價層以及每個彈性集區定價層都包含一定數量的記憶體內部 OLTP 儲存體。

- [以 DTU 為基礎的資源限制-單一資料庫](database/resource-limits-dtu-single-databases.md)
- [以 DTU 為基礎的資源限制-彈性集區](database/resource-limits-dtu-elastic-pools.md)
- [以虛擬核心為基礎的資源限制 - 單一資料庫](database/resource-limits-vcore-single-databases.md)
- [以 vCore 為基礎的資源限制-彈性集區](database/resource-limits-vcore-elastic-pools.md)
- [以 vCore 為基礎的資源限制-受控實例](managed-instance/resource-limits.md)

下列項目計入記憶體內部 OLTP 儲存體容量上限︰

- 記憶體最佳化資料表和資料表變數中的作用中使用者資料列。 請注意，舊資料列版本不計入上限。
- 記憶體最佳化資料表上的索引。
- ALTER TABLE 作業的作業負荷。

如果您達到上限，則會收到超出配額錯誤，並再也無法插入或更新資料。 為避免此錯誤，您可以刪除資料或增加資料庫或集區的定價層。

如需有關監視 In-Memory OLTP 儲存使用量和設定警示的詳細資訊（當您幾乎達到上限時），請參閱 [監視記憶體內部儲存體](in-memory-oltp-monitor-space.md)。

#### <a name="about-elastic-pools"></a>關於彈性集區

使用彈性集區時，集區中的所有資料庫會共用記憶體內部 OLTP 儲存體。 因此，一個資料庫中的使用量可能會影響其他資料庫。 對此，您可以採取以下兩個對策︰

- 將資料庫的 `Max-eDTU` 或 `MaxvCore` 設定為低於整個集區的 eDTU 或虛擬核心計數。 此最大值會將集區中任何資料庫的記憶體內部 OLTP 儲存體使用量上限，限制為對應到 eDTU 計數的大小。
- 設定大於 0 的 `Min-eDTU` 或 `MinvCore`。 此最小值可確保集區中的每個資料庫，都能擁有與所設定 `Min-eDTU` 或 `vCore` 相對應的可用記憶體內部 OLTP 儲存體數量。

### <a name="changing-service-tiers-of-databases-that-use-in-memory-oltp-technologies"></a>變更使用記憶體內部 OLTP 技術之資料庫的服務層級

您一律可將資料庫或執行個體升級至較高的層，例如從一般用途升級至業務關鍵 (或從標準升級至進階)。 可用的功能和資源只會增加。

但是將層降級可能會對資料庫有負面影響。 當您的資料庫包含記憶體內部 OLTP 物件時，從業務關鍵降級到一般用途 (或從進階降級到標準或基本) 會有特別顯著的影響。 降級之後將無法使用記憶體最佳化資料表 (即使它們依然可見)。 當您降低彈性集區的定價層，或將具有記憶體內部技術的資料庫移至一般用途、標準或基本彈性集區時，相同的考慮也適用。

> [!Important]
> 一般用途層、標準層或基本層不支援記憶體內部 OLTP。 因此，您無法將具有任何 In-Memory OLTP 物件的資料庫移至其中一個層級。

在您將資料庫降級為一般用途、標準或基本之前，請移除所有記憶體優化資料表和資料表類型，以及所有原生編譯的 T-sql 模組。

相應 *減少商務關鍵性層中的資源* ：記憶體優化資料表中的資料必須符合與資料庫或受控實例層相關聯之 In-Memory OLTP 儲存體內的資料，或可在彈性集區中使用的資料。 如果您嘗試相應減少層，或將資料庫移入沒有足夠之可用記憶體內部 OLTP 儲存體的集區內，則作業會失敗。

## <a name="in-memory-columnstore"></a>記憶體內部資料行存放區

記憶體內部資料行存放區技術可讓您在資料表中儲存及查詢大量資料。 資料行存放區技術會使用以資料行為基礎的資料儲存格式和批次查詢處理，比起傳統的資料列導向儲存體，OLAP 工作負載的查詢效能提升了 10 倍。 相較於未壓縮的資料大小，您也可以將資料壓縮提升高達 10 倍。
有兩種類型的資料行存放區模型，可供您用來組織資料：

- **叢集資料行存放區** ，其中資料表中的所有資料都會組織成單欄式格式。 在此模型中，資料表中的所有資料列都會以高度壓縮資料，並可讓您對資料表執行快速分析查詢和報告的單欄式格式進行放置。 根據資料的本質，您的資料大小可能會降低 10 倍到 100 倍。 叢集資料行存放區模型也可以快速擷取大量資料 (大量載入)，因為超過 100K 資料列的大型批次資料會在儲存於磁碟之前先進行壓縮。 對於傳統資料倉儲案例來說，此模型是不錯的選擇。
- **非叢集資料行存放區** ，其中資料會儲存在傳統的資料列存放區資料表中，且有一個資料行存放區格式的索引用於分析查詢。 此模型會啟用混合式交易分析處理 (HTAP)：在交易式工作負載上執行效能即時分析的能力。 OLTP 查詢是在資料列存放區資料表上執行，該資料表已經過最佳化可存取較小的資料列，而 OLAP 查詢則是在資料行存放區索引上執行，這對掃描和分析來說是比較好的選擇。 查詢最佳化工具會根據查詢動態選擇 rowstore 或資料行存放區格式。 非叢集資料行存放區索引不會降低資料的大小，因為原始資料集會保留在原始的資料列存放區資料表中，而沒有任何變更。 不過，其他資料行存放區索引的大小應該比對等 B 型樹狀結構索引要來得小。

> [!Note]
> 記憶體內部資料行存放區技術只會保留在記憶體中進行處理所需的資料，而無法放入記憶體的資料則會儲存在磁碟上。 因此，記憶體內部資料行存放區結構中的資料量可能會超過可用記憶體的數量。

技術的相關深入介紹影片：

- [資料行存放區索引： Ignite 2016 的記憶體中分析影片](/archive/blogs/sqlserverstorageengine/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016)

### <a name="data-size-and-storage-for-columnstore-indexes"></a>資料行存放區索引的資料大小和儲存體

資料行存放區索引不需要納入記憶體中。 因此，索引大小的唯一上限是整體資料庫大小上限，相關說明請參閱[以 DTU 為基礎的購買模型](database/service-tiers-dtu.md)和[以虛擬核心為基礎的購買模型](database/service-tiers-vcore.md) 一文。

當您使用叢集資料行存放區索引時，基底表格儲存體會使用單資料行式壓縮。 壓縮可大幅降低使用者資料的儲存體使用量，這表示您可以在資料庫中容納更多資料。 若要再進一步壓縮，您可以使用[單資料行式封存壓縮](/sql/relational-databases/data-compression/data-compression#using-columnstore-and-columnstore-archive-compression)。 能達到多大壓縮量取決於資料性質，但 10 倍的壓縮並不罕見。

例如，如果您的資料庫大小上限是 1 TB，而且您使用資料行存放區達到 10 倍壓縮，您總共可以在資料庫中容納 10 TB 的使用者資料。

當您使用非叢集資料行存放區索引時，基底資料表仍然會以傳統資料列存放區格式儲存。 因此，節省的儲存空間與叢集資料行存放區索引並沒有那麼重要。 不過，如果您以單一資料行存放區索引來取代部分的傳統非叢集索引，整體來說仍可節省資料表的儲存體使用量。

### <a name="changing-service-tiers-of-databases-containing-columnstore-indexes"></a>變更包含資料行存放區索引之資料庫的服務層級

如果您的目標層低於 S3，可能無法將 *單一資料庫降級為基本或標準* 。 只有在業務關鍵層/進階定價層和標準層、S3 及更高的層才支援資料行存放區索引，基本層則不支援。 當您將資料庫降級至不支援的層級時，資料行存放區索引將變成無法使用。 系統會維持您的資料行存放區索引，但它不會再利用索引。 如果您之後再升級為支援的層級，系統會立即重新利用您的資料行存放區索引。

如果您有「叢集」  資料行存放區索引，則降級之後整個資料表會變成無法使用。 因此我們建議您在將資料庫降級至不支援的層級之前，先捨棄所有「叢集」  資料行存放區索引。

> [!Note]
> SQL 受控執行個體支援所有層級的資料行存放區索引。

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

## <a name="next-steps"></a>後續步驟

- [快速入門 1：快速入門 1：可讓 Transact-SQL 擁有更快效能的記憶體內部 OLTP 技術](/sql/relational-databases/in-memory-oltp/survey-of-initial-areas-in-in-memory-oltp)
- [在現有的 Azure SQL 應用程式中使用記憶體內部 OLTP](in-memory-oltp-configure.md)
- 針對記憶體內部 OLAP [監視記憶體內部 OLTP 儲存體](in-memory-oltp-monitor-space.md)
- [試用記憶體內部功能](in-memory-sample.md)

## <a name="additional-resources"></a>其他資源

### <a name="deeper-information"></a>更深入的資訊

- [了解仲裁如何透過 SQL Database 中的記憶體內部 OLTP，讓重要資料庫的工作負載加倍，同時降低 70% 的 DTU](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)
- [記憶體內部 OLTP 的 Blog 文章](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)
- [了解記憶體內部 OLTP](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [了解資料行存放區索引](/sql/relational-databases/indexes/columnstore-indexes-overview)
- [了解即時作業分析](/sql/relational-databases/indexes/get-started-with-columnstore-for-real-time-operational-analytics)
- 請參閱[一般工作負載模式和移轉考量](/previous-versions/dn673538(v=msdn.10)) (其中描述記憶體內部 OLTP 經常提供顯著效能改善的工作負載模式)

### <a name="application-design"></a>應用程式設計

- [記憶體內部 OLTP (記憶體內部優化) ](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization)
- [在現有的 Azure SQL 應用程式中使用記憶體內部 OLTP](in-memory-oltp-configure.md)

### <a name="tools"></a>工具

- [Azure 入口網站](https://portal.azure.com/)
- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)
- [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt)