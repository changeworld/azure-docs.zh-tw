---
title: 設計可擴充兼具效能的 Azure Cosmos DB 資料表
description: Azure 表格儲存體設計指南：Azure Cosmos DB 和 Azure 表格儲存體中可擴充的高效能資料表
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 06/19/2020
author: sakash279
ms.author: akshanka
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 812d4976a0c6afe646c329ee483be20c33416381
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943896"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Azure 表格儲存體資料表設計指南：可擴充的高效能資料表
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

若要設計可擴充且高效能的資料表，您必須考慮各種因素，包括成本。 如果您先前已為關聯式資料庫設計架構，那麼您應該很熟悉這些考量。 但是，雖然 Azure 資料表儲存體與關聯式模型之間有一些相似之處，但也有許多重要的差異。 這些差異常會導致不同的設計，看起來可能違反直覺性，或讓熟悉關聯式資料庫的人覺得不對勁，但如果您設計的是 NoSQL 索引鍵/值存放區 (例如資料表儲存體)，這就顯得合理了。

表格儲存體旨在支援雲端規模的應用程式，這種應用程式可能包含數十億個資料實體 (以關聯式資料庫術語來說，即「資料列」)，或針對必須支援大量交易的資料集而設計。 因此，您必須從不同的角度思考如何儲存資料，並了解表格儲存體的運作方式。 相較於使用關聯式資料庫的解決方案，設計良好的 NoSQL 資料存放區可讓您的解決方案更進一步縮放 (以較低的成本)。 本指南針對這些主題為您提供協助。  

## <a name="about-azure-table-storage"></a>關於 Azure 表格儲存體
本節著重於表格儲存體的一些主要特性，與效能和可擴縮性的設計特別有關。 如果您不熟悉 Azure 儲存體和表格儲存體，請先參閱 [Microsoft Azure 儲存體簡介](../storage/common/storage-introduction.md)和[透過 .NET 開始使用 Azure 表格儲存體](./tutorial-develop-table-dotnet.md)，再閱讀本文的其餘部分。 雖然本指南著重於表格儲存體，但仍論及 Azure 佇列儲存體和 Azure Blob 儲存體，以及如何與表格儲存體一起運用在解決方案中。  

表格儲存體以表格形式來儲存資料。 在標準術語中，資料表的每個資料列都代表一個實體，而資料行儲存該實體的各種屬性。 每個實體都有一組索引鍵當作唯一識別，還有時間戳記資料行供表格儲存體追蹤實體上次更新的時間。 系統會自動新增時間戳記，您無法以任意值來手動覆寫時間戳記。 表格儲存體使用這個上次修改時間戳記 (LMT) 來管理開放式同步存取。  

> [!NOTE]
> 表格儲存體 REST API 作業也會傳回從 LMT 衍生的 `ETag` 值。 ETag 和 LMT 兩字在本文中交互使用，因為都是指相同的基礎資料。  
> 
> 

下列範例說明簡單的資料表設計，用以儲存員工和部門的實體。 本指南稍後說明的許多範例皆以這個簡單的設計為基礎。  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>時間戳記</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td>六月</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>department</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Sales</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


到目前為止，這項設計看起來類似關聯式資料庫中的資料表。 主要差異在於必要的資料行，以及能夠在相同資料表中儲存多個實體類型。 此外，每個使用者定義屬性 (例如 **FirstName** 或 **Age**) 都具有資料類型，例如整數或字串，就像關聯式資料庫中的資料行一樣。 然而，不同於關聯式資料庫，表格儲存體的無結構描述本質意味著，一個屬性在每個實體上不一定是相同的資料類型。 若要將複雜資料類型儲存在單一屬性中，您必須使用序列化格式，例如 JSON 或 XML。 如需詳細資訊，請參閱[了解表格儲存體資料模型](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)。

您選擇的 `PartitionKey` 和 `RowKey` 極為重要，攸關資料表設計是否良好。 儲存在資料表中的每個實體，都必須有獨一無二的 `PartitionKey` 和 `RowKey` 組合。 如同關聯式資料庫資料表中的索引鍵，`PartitionKey` 和 `RowKey` 值會進行索引編製，以建立可啟用快速查閱的叢集索引。 不過，表格儲存體不會建立任何次要索引，因此這些是僅有的兩個索引屬性 (稍後描述的一些模式將說明如何解決這種明顯的限制)。  

資料表由一或多個分割區組成，您的設計決策大多圍遶在選擇適合的 `PartitionKey` 和 `RowKey`，以最佳化解決方案。 解決方案可以只由單一資料表組成，其中包含您的所有實體 (組成分割區)，但解決方案通常有多個資料表。 資料表協助您以邏輯方式組織實體，並協助您使用存取控制清單來管理資料的存取權。 您可以使用單一儲存體作業來卸除整個資料表。  

### <a name="table-partitions"></a>資料表的資料分割
帳戶名稱、資料表名稱和 `PartitionKey` 一起識別儲存體服務內的分割區，供表格儲存體在其中儲存實體。 分割區不但是實體定址配置的一部分，而且定義交易的範圍 (請參閱本節稍後的[實體群組交易](#entity-group-transactions)一節)，也是表格儲存體如何縮放的基礎。 如需資料表分割區的詳細資訊，請參閱[表格儲存體的效能和可擴縮性檢查清單](../storage/tables/storage-performance-checklist.md)。  

在表格儲存體中，個別節點提供一或多個完整分割區，此服務的縮放作法是在節點之間動態平衡分割區的負載。 如果節點承受負載，表格儲存體可以將該節點使用的分割區範圍，分割給不同的節點。 當流量消退時，表格儲存體可以將靜止節點的分割區範圍，合併回到單一節點。  

如需表格儲存體內部細節的詳細資訊，特別是其管理分割區的方式，請參閱 [Microsoft Azure 儲存體：一致性很強的高可用性雲端儲存體服務](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)。  

### <a name="entity-group-transactions"></a>實體群組交易
在表格儲存體中，需要跨多個實體執行不可部分完成的更新時，實體群組交易 (EGT) 是唯一的內建機制。 EGT 也稱為「批次交易」。 EGT 只能處理儲存在相同分割區中的實體 (在特定資料表中共用相同分割區索引鍵)，每當您在多個實體之間需要有不可部分完成的交易行為時，請確定這些實體位於相同的分割區。 通常就是因為這樣，才需要將多個實體類型放在相同的資料表 (和分割區)，而不是針對不同的實體類型使用多個資料表。 單一 EGT 最多可以操作 100 個實體。  如果您送出多個同時的 EGT 來處理，務必確保這些 EGT 不影響跨 EGT 通用的實體。 否則會招致延遲處理的風險。

EGT 也會帶來需在您設計中評估的潛在取捨。 使用越多分割區可提高應用程式的可擴縮性，因為 Azure 更有機會在節點之間平衡要求的負載。 但這可能造成應用程式難以執行不可部分完成的交易，也不易維護資料的強大一致性。 此外，還有些在分割層級上的特定延展性目標，可能會限制單一節點的預期交易輸送量。

關於 Azure 儲存體帳戶的可擴縮性目標，如需詳細資訊，請參閱[標準儲存體帳戶的可擴縮性目標](../storage/common/scalability-targets-standard-account.md)。 關於表格儲存體的可擴縮性目標，如需詳細資訊，請參閱[表格儲存體的可擴縮性和效能目標](../storage/tables/scalability-targets.md)。 本指南後續幾節將討論各種設計策略，以協助您管理這類的權衡，並討論如何根據您用戶端應用程式的特定需求，選擇您的資料分割索引鍵。  

### <a name="capacity-considerations"></a>容量考量
下表包含設計表格儲存體解決方案時，需要注意的一些重要值：  

| Azure 儲存體帳戶的容量總計 | 500 TB |
| --- | --- |
| Azure 儲存體帳戶中的資料表數目 |僅受限於儲存體帳戶的容量。 |
| 資料表中的資料分割數目 |僅受限於儲存體帳戶的容量。 |
| 資料分割中的實體數目 |僅受限於儲存體帳戶的容量。 |
| 個別實體的大小 |最多 1 MB 和 255 個屬性 (包括 `PartitionKey`、`RowKey` 和 `Timestamp`)。 |
| `PartitionKey` 的大小 |最大 1 KB 的字串。 |
| `RowKey` 的大小 |最大 1 KB 的字串。 |
| 實體群組交易的大小 |交易最多可包含 100 個實體，承載大小必須小於 4 MB。 一個 EGT 只能更新實體一次。 |

如需詳細資訊，請參閱[了解表格服務資料模型](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model)。  

### <a name="cost-considerations"></a>成本考量
雖然表格儲存體相對便宜，但您在評估任何使用表格儲存體的解決方案時，應該將容量使用量和交易數量的成本預估納入考量。 不過，在許多情況下，為了改善解決方案的效能或可擴縮性，儲存反正規化或重複的資料是可行的作法。 如需定價的詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。  

## <a name="guidelines-for-table-design"></a>資料表設計指導方針
這些清單概述您在設計資料表時，應該牢記的一些重要指導方針。 本指南稍後會更詳細地完整探討。 這些指導方針不同於關聯式資料庫設計通常遵循的方針。  

將表格儲存體設計為高效率「讀取」：

* **設計大量讀取應用程式中的查詢。** 設計資料表時，請先考慮您將執行的查詢 (尤其是易受延遲影響的查詢)，再考慮如何更新實體。 透過這樣的方式，通常可造就一個有效率且高效能的解決方案。  
* **在查詢中同時指定 `PartitionKey` 和 `RowKey`。** 這一類的「點查詢」是最有效率的表格儲存體查詢。  
* **考慮儲存重複的實體副本。** 表格儲存體便宜，請考慮將相同的實體儲存多次 (使用不同索引鍵)，讓查詢更有效率。  
* **考慮將資料反正規化。** 表格儲存體便宜，請考慮將資料反正規化。 例如儲存摘要實體，可讓彙總資料的查詢只需存取單一實體。  
* **使用複合索引鍵值。** 您只有 `PartitionKey` 和 `RowKey` 索引鍵。 例如，使用複合索引鍵值啟用替代的實體索引鍵式存取路徑。  
* **使用查詢預測。** 使用僅選取您所需欄位的查詢，即可減少透過網路傳輸的資料量。  

將表格儲存體設計為高效率「寫入」：  

* **不要產生熱點分割區。** 選擇可讓您在任何時間點，將要求分散到多個分割上的索引鍵。  
* **避免流量尖峰。** 將流量分散在一段合理的時間內，避免流量尖峰。
* **不一定要為每個實體的類型建立個別的資料表。** 需要跨實體類型執行不可部分完成的交易時，您可以在相同資料表的相同分割中儲存多個實體類型。
* **考慮必須達到的最大輸送量。** 您必須留意表格儲存體的可擴縮性目標，並確保您的設計不會讓您逾越目標。  

本指南稍後，您會看到這些準則全部實施的範例。  

## <a name="design-for-querying"></a>查詢的設計
表格儲存體可以是讀取密集、寫入密集或兩者混合。 本節探討如何設計為有效率支援讀取作業。 一般而言，支援有效讀取作業的設計，也可兼顧寫入作業的效率。 但在設計為支援寫入作業時，還有其他考量。 這些放在下一節[資料修改的設計](#design-for-data-modification)中討論。

為了有效率地讀取資料，首先應該想到「我的應用程式需要執行哪些查詢，才能取得所需的資料？」  

> [!NOTE]
> 使用表格儲存體時，事先必須正確設計，因為後來很難變更，且代價頗高。 例如，在關聯式資料庫中，只要將索引新增至現有的資料庫，通常就能解決效能問題。 這對於表格儲存體來說不可行。  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>您選擇的 `PartitionKey` 和 `RowKey` 如何影響查詢效能
下列範例假設表格儲存體中儲存員工實體，結構如下 (為求簡潔，大部分範例都省略 `Timestamp` 屬性)：  

| 資料行名稱 | 資料類型 |
| --- | --- |
| `PartitionKey` (部門名稱) |String |
| `RowKey` (員工識別碼) |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |整數 |
| `EmailAddress` |String |

以下是設計表格儲存體查詢時的幾個一般指導方針。 下列範例中使用的篩選語法來自於表格儲存體 REST API。 如需詳細資訊，請參閱[查詢實體](/rest/api/storageservices/Query-Entities)。  

* 「點查詢」是最有效率的查閱，建議用於大量查閱，或需要最低延遲的查閱。 這類查詢可同時指定 `PartitionKey` 和 `RowKey` 值，以利用索引有效率地尋找個別實體。 例如： `$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')` 。  
* 「範圍查詢」次佳。 採用 `PartitionKey`，並依 `RowKey` 值範圍來篩選，以傳回一個以上的實體。 `PartitionKey` 值識別特定的分割區，`RowKey` 值識別該分割區中的實體子集。 例如： `$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'` 。  
* 第三名是「分割區掃描」。 採用 `PartitionKey`，並依另一個非索引鍵屬性來篩選，可能傳回一個以上的實體。 `PartitionKey` 值識別特定的分割區，屬性值選取該分割區中的實體子集。 例如： `$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'` 。  
* 「資料表掃描」不包含 `PartitionKey`，而且效率不佳，因為會在所有組成資料表的分割區中搜尋任何相符實體。 無論篩選條件是否使用 `RowKey`，都會執行資料表掃描。 例如： `$filter=LastName eq 'Jones'` 。  
* 傳回多個實體的 Azure 表格儲存體查詢，依 `PartitionKey` 和 `RowKey` 順序來排序實體。 為了避免在用戶端重新排序實體，選擇的 `RowKey` 應該定義最常用的排序次序。 Azure Cosmos DB 中以 Azure 資料表 API 傳回的查詢結果，不會依分割區索引鍵或資料列索引鍵排序。 如需詳細的功能差異清單，請參閱 [Azure Cosmos DB 和 Azure 資料表儲存體中資料表 API 之間的差異](table-api-faq.md#table-api-vs-table-storage)。

如果使用 "**or**" 來根據 `RowKey` 值指定篩選條件，將會導致分割區掃描，且不視為範圍查詢。 因此，請避免查詢使用像這樣的篩選條件：`$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`。  

關於如何使用儲存體用戶端程式庫來執行有效率的查詢，如需用戶端程式碼範例，請參閱：  

* [使用儲存體用戶端程式庫執行點查詢](#run-a-point-query-by-using-the-storage-client-library)
* [使用 LINQ 取出多個實體](#retrieve-multiple-entities-by-using-linq)
* [伺服器端預測](#server-side-projection)  

如需可對儲存在相同資料表中的多個實體類型進行處理的用戶端程式碼範例，請參閱：  

* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>選擇適當的 `PartitionKey`
如果需要啟用 EGT (以確保一致性)，又需要將實體分散到多個分割區 (以確保可調整的解決方案)，您選擇的 `PartitionKey` 應該兩者兼顧。  

一種極端作法是將所有實體儲存在單一分割區。 但這可能限制解決方案的可擴縮性，還會導致表格儲存體無法平衡要求的負載。 另一種極端是每個分割區儲存一個實體。 這很容易調整，也可讓表格儲存體平衡要求的負載，但會禁止您使用實體群組交易。  

理想的 `PartitionKey` 可讓您使用有效率的查詢，而且有足夠的分割區來確保可調整解決方案。 一般而言，您會發現實體有適當的屬性可將實體分散到足夠的分割區。

> [!NOTE]
> 例如，在儲存使用者或員工相關資訊的系統中，`UserID` 可能就是適合的 `PartitionKey`。 您可能有數個實體使用特定的 `UserID` 作為分割區索引鍵。 儲存使用者資料的每個實體都歸屬於單一分割區。 這些實體可以透過 EGT 存取，但可擴縮性仍然很高。
> 
> 

選擇 `PartitionKey` 時還有其他考量，與您如何插入、更新和刪除實體有關。 如需詳細資訊，請參閱本文稍後的[資料修改的設計](#design-for-data-modification)。  

### <a name="optimize-queries-for-table-storage"></a>最佳化表格儲存體的查詢
表格儲存體會在單一叢集索引中使用 `PartitionKey` 和 `RowKey` 值，自動為實體編製索引。 這是點查詢最有效可行的原因。 不過，除了在叢集索引上，`PartitionKey` 和 `RowKey` 上沒有其他任何索引。

許多設計必須符合需求，才能讓您根據多個準則查閱實體。 例如，根據電子郵件、員工識別碼或姓氏來尋找員工實體。 在[資料表設計模式](#table-design-patterns)一節中，下列模式滿足這幾種需求。 這些模式也說明如何解決表格儲存體不提供次要索引的情況。  

* [分割區內部次要索引模式](#intra-partition-secondary-index-pattern)：使用不同的 `RowKey` 值 (在相同的分割區) 儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。  
* [分割區之間次要索引模式](#inter-partition-secondary-index-pattern)：在個別的分割區或個別的資料表中，使用不同的 `RowKey` 值來儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。  
* [索引實體模式](#index-entities-pattern)：維護索引項目，啟用有效的搜尋以傳回實體清單。  

### <a name="sort-data-in-table-storage"></a>排序表格儲存體中的資料

表格儲存體傳回的查詢結果會先依 `PartitionKey`，再依 `RowKey`，以遞增順序排序。

> [!NOTE]
> Azure Cosmos DB 中以 Azure 資料表 API 傳回的查詢結果，不會依分割區索引鍵或資料列索引鍵排序。 如需詳細的功能差異清單，請參閱 [Azure Cosmos DB 和 Azure 資料表儲存體中資料表 API 之間的差異](table-api-faq.md#table-api-vs-table-storage)。

表格儲存體中的索引鍵是字串值。 為確保數值正確排序，您應該將數值轉換成固定長度，並以零填補。 例如，如果作為 `RowKey` 的員工識別碼值是整數值，您應該將員工識別碼 **123** 轉換為 **00000123**。 

許多應用程式都需要使用以不同順序排序的資料：例如，依名稱或加入日期為員工排序。 在[資料表設計模式](#table-design-patterns)一節中，下列模式指出如何替換實體的排序次序：  

* [分割區內部次要索引模式](#intra-partition-secondary-index-pattern)：使用不同的 `RowKey` 值 (在相同的分割區) 儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。  
* [分割區之間次要索引模式](#inter-partition-secondary-index-pattern)：在個別的分割區或個別的資料表中，使用不同的 `RowKey` 值來儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。
* [記錄結尾模式](#log-tail-pattern)：使用以日期和時間反向順序排序的 `RowKey` 值，取出最近加入分割區的 *n* 個實體。  

## <a name="design-for-data-modification"></a>資料修改的設計
本節著重於最佳化插入、更新和刪除的設計考量。 在某些情況下，您必須評估查詢最佳化設計和資料修改設計之間的利弊得失。 這項評估類似於評估關聯式資料庫的設計 (儘管關聯式資料庫中處理設計利弊得失的技巧不同)。 [資料表設計模式](#table-design-patterns)一節說明表格儲存體的一些詳細設計模式，並強調其中一些利弊得失。 在實務上，您會發現許多針對查詢實體而最佳化的設計，也適用於修改實體。  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>最佳化插入、更新和刪除作業的效能
若要更新或刪除實體，您必須能夠使用 `PartitionKey` 和 `RowKey` 值來識別該實體。 在這方面，選擇 `PartitionKey` 和 `RowKey` 來修改實體時，請遵循您選擇支援點查詢時的類似準則。 您希望以最有效率的方法來識別實體。 請不要為了探索您更新或刪除實體時需要的 `PartitionKey` 和 `RowKey` 值，而使用效率不佳的分割區或資料表掃描來尋找實體。  

在[資料表設計模式](#table-design-patterns)一節中，下列模式可將插入、更新和刪除作業的效能最佳化：  

* [大量刪除模式](#high-volume-delete-pattern)：將您想要同時刪除的所有實體，儲存在其自己的個別資料表中，以支援刪除大量實體。 只要刪除資料表，就會刪除實體。  
* [資料序列模式](#data-series-pattern)：將完整資料序列儲存在單一實體中，以盡可能減少您提出的要求數。  
* [寬型實體模式](#wide-entities-pattern)：使用多個實際的實體來儲存具有超過 252 個屬性的邏輯實體。  
* [大型實體模式](#large-entities-pattern)：使用 Blob 儲存體來儲存大型屬性值。  

### <a name="ensure-consistency-in-your-stored-entities"></a>確保預存實體中的一致性
另一個會在您選擇最佳化資料修改的索引鍵時造成影響的主要因素，是如何使用不可部分完成的交易確保一致性。 只有對於儲存在相同資料分割中的實體，才能使用 EGT 來運作。  

在[資料表設計模式](#table-design-patterns)一節中，下列模式可管理一致性：  

* [分割區內部次要索引模式](#intra-partition-secondary-index-pattern)：使用不同的 `RowKey` 值 (在相同的分割區) 儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。  
* [分割區之間次要索引模式](#inter-partition-secondary-index-pattern)：在個別的分割區或個別的資料表中，使用不同的 `RowKey` 值來儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。  
* [最終一致的交易模式](#eventually-consistent-transactions-pattern)：使用 Azure 佇列，跨資料分割界限或儲存體系統界限啟用最終一致的行為。
* [索引實體模式](#index-entities-pattern)：維護索引項目，啟用有效的搜尋以傳回實體清單。  
* [反正規化模式](#denormalization-pattern)：將相關資料結合成單一實體，讓您透過單點查詢取出您需要的所有資料。  
* [資料序列模式](#data-series-pattern)：將完整資料序列儲存在單一實體中，以盡可能減少您提出的要求數。  

如需詳細資訊，請參閱本文稍後的[實體群組交易](#entity-group-transactions)。  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>確保您針對有效率的修改所做的設計有助於提升查詢效率
在許多情況下，效率查詢的設計都可造就有效的修改，但是您務必要評估這對您的特定案例是否適用。 在[資料表設計模式](#table-design-patterns) 一節中，某些模式會在查詢和修改實體之間明確評估利弊得失，您一定要將各種作業的數量納入考量。  

在[資料表設計模式](#table-design-patterns)一節中，下列模式可解決有效率查詢設計和有效率資料修改設計之間的利弊得失：  

* [複合索引鍵模式](#compound-key-pattern)：使用複合 `RowKey` 值，讓用戶端透過單點查詢來查閱相關資料。  
* [記錄結尾模式](#log-tail-pattern)：使用以日期和時間反向順序排序的 `RowKey` 值，取出最近加入分割區的 *n* 個實體。  

## <a name="encrypt-table-data"></a>加密資料表資料
.NET Azure 儲存體用戶端程式庫支援在插入和取代作業中加密字串實體的屬性。 加密的字串在服務上儲存為二進位屬性，解密後會轉換回字串。    

針對資料表，除了加密原則之外，使用者必須指定要加密的屬性。 請指定 `EncryptProperty` 屬性 (針對衍生自 `TableEntity` 的 POCO 實體)，或在要求選項中指定加密解析程式。 加密解析程式是委派，接受分割區索引鍵、資料列索引鍵和屬性名稱，並傳回布林值來指出是否應該加密該屬性。 在加密期間，用戶端程式庫會根據此資訊，決定將屬性寫到網路時是否應該加密。 委派也提供關於屬性如何加密的可能邏輯。 (例如，如果 X，則加密屬性 A，否則加密屬性 A 和 B。)讀取或查詢實體時不需要提供此資訊。

目前不支援合併。 因為先前可能使用不同的金鑰來加密屬性子集，直接合併新的屬性和更新中繼資料會導致資料遺失。 合併時可能需要發出額外的服務呼叫，以從服務讀取預先存在的實體，也可能需要在每個屬性上使用新的金鑰。 兩者都不利於效能。     

如需加密資料表資料的相關資訊，請參閱 [Microsoft Azure 儲存體的用戶端加密和 Azure Key Vault](../storage/common/storage-client-side-encryption.md)。  

## <a name="model-relationships"></a>模型關聯性
建置網域模型是設計複雜系統時的關鍵步驟。 一般而言，您會使用模型化程序來識別實體及其之間的關聯性，以了解商務領域，並報告系統的設計。 本節著重於如何將網域模型中一些常見的關聯性類型，轉化為表格儲存體的設計。 從邏輯資料模型對應至實體 NoSQL 架構資料模型的程序，與設計關聯式資料庫時使用的程序不同。 關聯式資料庫設計通常會採用資料正規化程序，以盡可能避免冗餘。 這種設計也採用宣告式查詢功能，將資料庫的運作方式抽象化。  

### <a name="one-to-many-relationships"></a>一對多關聯性
商業網域物件之間常會產生一對多關聯性：例如，一個部門有許多員工。 有幾種方式可在表格儲存體中實作一對多關聯性，但在特定情況下，各有優缺點。  

以一家擁有數萬個部門和員工實體的大型跨國公司為例。 每個部門有許多員工，而每個員工都與一個特定部門相關聯。 有一種方法是儲存個別部門和員工實體，如下所示：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE01.png" alt-text="此圖顯示部門實體和員工實體":::

此範例根據 `PartitionKey` 值，顯示類型之間隱含的一對多關聯性。 每個部門可以有許多員工。  

此範例也說明相同資料分割中的部門實體及其相關聯的員工實體。 針對各種實體類型，您可以選擇使用不同的分割區、資料表，甚至不同的儲存體帳戶。  

另一種方式是將資料反正規化，只儲存具有反正規化部門資料的員工實體，如下列範例所示。 在此特定案例中，如果您需要變更部門經理的詳細資料，則這種反正規化方法可能不是最佳選擇。 因為，您必須更新部門的每個員工。  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE02.png" alt-text="員工實體的圖形，顯示如何反正規化您的資料，並只儲存具有正規化部門資料的員工實體。":::

如需詳細資訊，請參閱本指南下半部的 [去正規化模式](#denormalization-pattern) 。  

下表概述儲存具有一對多關聯性的員工和部門實體時，各種方法的優缺點。 您也應該考量預期執行各種作業的頻率。 即使設計包含成本較高的作業，只要該作業很少發生，或許尚可接受。  

<table>
<tr>
<th>方法</th>
<th>優點</th>
<th>缺點</th>
</tr>
<tr>
<td>個別的實體類型、相同的資料分割、相同的資料表</td>
<td>
<ul>
<li>您可以使用單一作業更新部門實體。</li>
<li>如果您需要在每次更新/插入/刪除員工實體時修改部門實體，您可以使用 EGT 來維持一致性。 例如，假設您維護每個部門的部門員工計數。</li>
</ul>
</td>
<td>
<ul>
<li>在某些用戶端活動中，您可能需要同時取出員工和部門實體。</li>
<li>儲存作業會在相同資料分割中執行。 在交易量很高時，這可能會形成熱點。</li>
<li>您無法使用 EGT 將員工移至新部門。</li>
</ul>
</td>
</tr>
<tr>
<td>個別的實體類型、不同的資料分割或資料表或儲存體帳戶</td>
<td>
<ul>
<li>您可以使用單一作業更新部門實體或員工實體。</li>
<li>在交易量很高時，這有助於將負載分散到更多分割區。</li>
</ul>
</td>
<td>
<ul>
<li>在某些用戶端活動中，您可能需要同時取出員工和部門實體。</li>
<li>當您更新/插入/刪除員工並更新部門時，您無法使用 EGT 來維持一致性。 例如，更新某個部門實體中的員工計數。</li>
<li>您無法使用 EGT 將員工移至新部門。</li>
</ul>
</td>
</tr>
<tr>
<td>去正規化為單一實體類型</td>
<td>
<ul>
<li>您可以透過單一要求擷取您所需的所有資訊。</li>
</ul>
</td>
<td>
<ul>
<li>如果您需要更新部門資訊，則維持一致性的代價很高 (因為您需要更新部門的所有員工)。</li>
</ul>
</td>
</tr>
</table>

如何選擇這些選項及哪些優缺點最值得注意，取決於您的應用程式案例。 例如，您修改部門實體的頻率為何？ 所有員工查詢需要額外的部門資訊嗎？ 分割區或儲存體帳戶有多接近可擴縮性限制？  

### <a name="one-to-one-relationships"></a>一對一關聯性
領域模型的實體之間可能包含一對一關聯性。 如果您需要在表格儲存體中實作一對一關聯性，則在需要同時取出兩個相關的實體時，還必須選擇如何連結兩者。 此連結可以隱含 (根據索引鍵值的慣例) 或明確 (以 `PartitionKey` 和 `RowKey` 值的形式將連結儲存在每個實體中) 指向其相關實體。 如需是否應將相關實體儲存於相同資料分割中的討論，請參閱 [一對多關聯性](#one-to-many-relationships)一節。  

您也可能因為實作上的考量，而決定在表格儲存體中實作一對一關聯性：  

* 處理大型實體 (如需詳細資訊，請參閱[大型實體模式](#large-entities-pattern))。  
* 實作存取控制 (如需詳細資訊，請參閱[使用共用存取簽章來控制存取](#control-access-with-shared-access-signatures))。  

### <a name="join-in-the-client"></a>用戶端中的聯結
雖然有辦法在表格儲存體中模仿關聯性，但別忘了，可擴縮性和效能才是使用表格儲存體的兩大理由。 如果發覺您在模型化許多關聯性，而損害解決方案的效能和可擴縮性，請捫心自問，是否需要在資料表設計中建立所有資料關聯性。 如果讓用戶端應用程式執行任何必要的聯結，或許可以簡化設計，並改善解決方案的效能和可擴縮性。  

例如，如果您有小型資料表，其中包含不常變更的資料，則您可以擷取此資料一次，並放在用戶端快取。 這可以避免重複擷取相同資料的往返。 在本指南所討論的範例中，小型組織中的部門集合可能很小，而且不常變動。 用戶端應用程式可下載一次並快取的資料，就很適合成為查閱資料。  

### <a name="inheritance-relationships"></a>繼承關聯性
如果用戶端應用程式使用一組類別 (形成一部分繼承關聯性) 來代表商務實體，則您可以輕鬆地將這些實體保存在表格儲存體中。 例如，您在用戶端應用程式中可能定義下列這組類別，其中 `Person` 是抽象類別。

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE03.png" alt-text="繼承關聯性的圖表":::

在表格儲存體中，您可以使用單一 `Person` 資料表來保存兩個具體類別的執行個體。 使用如下所示的實體：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE04.png" alt-text="此圖顯示客戶實體和員工實體":::

關於在用戶端程式碼中如何在相同資料表中使用多個實體類型，如需詳細資訊，請參閱本指南稍後的[使用異質實體類型](#work-with-heterogeneous-entity-types)。 其中提供如何在用戶端程式碼中辨識實體類型的範例。  

## <a name="table-design-patterns"></a>資料表設計模式
在前幾節中，您已了解如何將資料表設計最佳化，以利於使用查詢來取出實體資料，以及插入、更新和刪除實體資料。 本節說明一些適用於表格儲存體的模式。 此外，您會看到如何實際解決本指南前述的一些問題和利弊得失。 下圖總結不同模式之間的關聯性：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE05.png" alt-text="資料表設計模式的圖表":::

此模式圖突顯本指南提及的模式 (藍色) 與反模式 (橘色) 之間的一些關聯性。 當然還有許多其他模式也值得考量。 例如，表格儲存體的主要案例之一是使用[命令查詢責任分離](/previous-versions/msp-n-p/jj554200(v=pandp.10))模式中的[具體化檢視模式](/previous-versions/msp-n-p/dn589782(v=pandp.10))。  

### <a name="intra-partition-secondary-index-pattern"></a>內部資料分割次要索引模式
使用不同的 `RowKey` 值 (在相同的分割區) 儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。 使用 EGT 可讓複本之間的更新保持一致。  

#### <a name="context-and-problem"></a>內容和問題
表格儲存體會使用 `PartitionKey` 和 `RowKey` 值，自動為實體編製索引。 這可讓用戶端應用程式使用這些值，有效率地取出實體。 例如，就下列資料表結構來說，用戶端應用程式可以使用點查詢，依門名稱和員工識別碼 (`PartitionKey` 和 `RowKey` 值) 來取出個別員工實體。 用戶端也可以擷取每個部門內以員工識別碼排序的實體。

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE06.png" alt-text="員工實體的圖形，其中用戶端應用程式可以使用「部門名稱」和「員工識別碼」 (PartitionKey 和 RowKey 值) ，藉此使用點查詢來取出個別員工實體。":::

如果您還想根據其他屬性 (例如電子郵件地址) 的值來尋找員工實體，則必須使用效率較低的分割區掃描，才能找出相符項目。 這是因為表格儲存體不提供次要索引。 此外，無法要求以 `RowKey` 順序以外的順序來排序員工清單。  

#### <a name="solution"></a>解決方法
若要解決缺少次要索引的問題，您可以為每個實體儲存多個複本，且每個複本使用不同的 `RowKey` 值。 如果您儲存的實體具有下列結構，則您可以根據電子郵件地址或員工識別碼，有效率地取出員工實體。 `RowKey`、`empid_`和 `email_` 前置值可讓您使用一組電子郵件地址或員工識別碼，以查詢單一員工或一群員工。  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE07.png" alt-text="此圖顯示 RowKey 值不固定的員工實體":::

下列兩個篩選準則 (一個依員工識別碼查閱，另一個依電子郵件地址查閱) 都指定點查詢：  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'email_jonesj@contoso.com')  

如果您要查詢一組員工實體，您可以指定依員工識別碼順序排序的範圍，或依電子郵件地址順序排序的範圍。 使用 `RowKey` 中適當的前置詞來查詢實體。  

* 若要在 Sales 部門中，找出員工識別碼範圍從 000100 至 000199 的所有員工，請使用：$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000100') and (RowKey le 'empid_000199')  
* 若要在 Sales 部門中，找出電子郵件地址開頭為字母 'a' 的所有員工，請使用：$filter=(PartitionKey eq 'Sales') and (RowKey ge 'email_a') and (RowKey lt 'email_b')  
  
前述範例中使用的篩選語法來自於表格儲存體 REST API。 如需詳細資訊，請參閱[查詢實體](/rest/api/storageservices/Query-Entities)。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 表格儲存體相對便宜，因此，不必在意儲存重複資料帶來的額外成本。 不過，您一定要根據預期的儲存體需求來評估設計成本，而且只為了支援用戶端應用程式將執行的查詢，才新增重複實體。  
* 因為次要索引實體和原始實體儲存在相同的分割區，請確定您不會超過個別分割區的可擴縮性目標。  
* 您可以將重複實體彼此保持一致，方法是使用 EGT 自動更新實體的兩個複本。 這表示您應該將實體的所有複本儲存在相同的資料分割中。 如需詳細資訊，請參閱[使用實體群組交易](#entity-group-transactions)。  
* 針對每個實體，`RowKey` 使用的值必須是唯一的。 請考慮使用複合索引鍵值。  
* 在 `RowKey` 中填補數值 (例如，員工識別碼 000223)，有助於根據上限和下限來正確排序和篩選。  
* 您不一定需要複製實體的所有屬性。 例如，如果查詢在 `RowKey` 中使用電子郵件地址來查閱實體，而且決不需要員工的年齡，則這些實體可能具有下列結構：

  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE08.png" alt-text="員工實體的圖形":::

* 一般來說，最好是儲存重複的資料，並確定您可以使用單一查詢來取出所有需要的資料，而不要使用一個查詢來尋找實體，然後又使用另一個查詢來查閱所需的資料。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
使用此模式的時機包括：

- 用戶端應用程式需要使用各種不同的索引鍵來取出實體。
- 用戶端需要以不同的排序次序來取出實體。
- 您可以使用各種唯一值來識別每個實體。

不過，在使用不同的 `RowKey` 值執行實體查閱時，請確定您不會超過分割區可擴縮性限制。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [間分割次要索引模式](#inter-partition-secondary-index-pattern)
* [複合索引鍵模式](#compound-key-pattern)
* [實體群組交易](#entity-group-transactions)
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>間資料分割次要索引模式
在個別的分割區或個別的資料表中，使用不同的 `RowKey` 值來儲存每個實體的多個複本。 這有助於快速有效率地查閱，還可使用不同的 `RowKey` 值來替換排序次序。  

#### <a name="context-and-problem"></a>內容和問題
表格儲存體會使用 `PartitionKey` 和 `RowKey` 值，自動為實體編製索引。 這可讓用戶端應用程式使用這些值，有效率地取出實體。 例如，就下列資料表結構來說，用戶端應用程式可以使用點查詢，依門名稱和員工識別碼 (`PartitionKey` 和 `RowKey` 值) 來取出個別員工實體。 用戶端也可以擷取每個部門內以員工識別碼排序的實體。  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE09.png" alt-text="員工實體結構的圖形，在使用時，用戶端應用程式可以使用「部門名稱」和「員工識別碼」 (PartitionKey 和 RowKey 值) ，藉此使用點查詢來取出個別的員工實體。":::形

如果您也想能夠根據其他屬性 (例如電子郵件地址) 的值尋找員工實體，您必須使用效率較低的資料分割掃描來尋找相符項目。 這是因為表格儲存體不提供次要索引。 此外，無法要求以 `RowKey` 順序以外的順序來排序員工清單。  

您預期這些實體會有大量的交易，而想要盡可能避免用戶端受制於表格儲存體速率。  

#### <a name="solution"></a>解決方法
若要解決缺少次要索引的問題，您可以為每個實體儲存多個複本，且每個複本使用不同的 `PartitionKey` 和 `RowKey` 值。 如果您儲存的實體具有下列結構，則您可以根據電子郵件地址或員工識別碼，有效率地取出員工實體。 `PartitionKey`、`empid_` 和 `email_` 前置值可讓您識別要用於查詢的索引。  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE10.png" alt-text="此圖顯示具有主要索引的員工實體和具有次要索引的員工實體":::

下列兩個篩選準則 (一個依員工識別碼查閱，另一個依電子郵件地址查閱) 都指定點查詢：  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') and (RowKey eq 'jonesj@contoso.com')  

如果您要查詢一組員工實體，您可以指定依員工識別碼順序排序的範圍，或依電子郵件地址順序排序的範圍。 使用 `RowKey` 中適當的前置詞來查詢實體。  

* 若要在 Sales 部門中，找出員工識別碼範圍從 **000100** 至 **000199** 的所有員工，並依員工識別碼順序排序，請使用：$filter=(PartitionKey eq 'empid_Sales') and (RowKey ge '000100') and (RowKey le '000199')  
* 若要在 Sales 部門中，找出電子郵件地址以 'a' 開頭的所有員工，並依電子郵件地址順序排序，請使用：$filter=(PartitionKey eq 'email_Sales') and (RowKey ge 'a') and (RowKey lt 'b')  

請注意，前述範例中使用的篩選語法來自於表格儲存體 REST API。 如需詳細資訊，請參閱[查詢實體](/rest/api/storageservices/Query-Entities)。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您可以讓重複實體彼此之間始終保持一致，方法是使用 [最終一致的交易模式](#eventually-consistent-transactions-pattern) 來維護主要和次要索引實體。  
* 表格儲存體相對便宜，因此，不必在意儲存重複資料帶來的額外成本。 不過，請務必根據預期的儲存體需求來評估設計成本，而且只為了支援用戶端應用程式將執行的查詢，才新增重複實體。  
* 針對每個實體，`RowKey` 使用的值必須是唯一的。 請考慮使用複合索引鍵值。  
* 在 `RowKey` 中填補數值 (例如，員工識別碼 000223)，有助於根據上限和下限來正確排序和篩選。  
* 您不一定需要複製實體的所有屬性。 例如，如果查詢在 `RowKey` 中使用電子郵件地址來查閱實體，而且決不需要員工的年齡，則這些實體可能具有下列結構：
  
  :::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE11.png" alt-text="此圖顯示具有次要索引的員工實體":::

* 一般來說，最好是儲存重複的資料，並確定您可以使用單一查詢來取出所有需要的資料，而不要使用一個查詢來透過次要索引尋找實體，然後又使用另一個查詢在主要索引中查閱所需的資料。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
使用此模式的時機包括：

- 用戶端應用程式需要使用各種不同的索引鍵來取出實體。
- 用戶端需要以不同的排序次序來取出實體。
- 您可以使用各種唯一值來識別每個實體。

當您使用不同的 `RowKey` 值來執行實體查閱時，如果想要避免超過分割區可擴縮性限制，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  
* [內部資料分割次要索引模式](#intra-partition-secondary-index-pattern)  
* [複合索引鍵模式](#compound-key-pattern)  
* [實體群組交易](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>最終一致的交易模式
使用 Azure 佇列，跨資料分割界限或儲存體系統界限啟用最終一致的行為。  

#### <a name="context-and-problem"></a>內容和問題
EGT 可讓您在共用相的資料分割索引鍵的多個實體之間執行不可部分完成的交易。 基於效能和可擴縮性考量，您可能決定將需要一致性的實體，儲存在個別的分割區或個別的儲存體系統中。 在這種情況下，您無法使用 EGT 來維持一致性。 例如，您可能必須在下列項目間維護最終一致性：  

* 儲存在相同資料表的兩個不同分割區中、不同的資料表中或不同儲存體帳戶中的實體。  
* 儲存在表格儲存體中的實體，以及儲存在 Blob 儲存體中的 blob。  
* 儲存在表格儲存體中的實體，以及檔案系統中的檔案。  
* 儲存在表格儲存體中的實體，而且還使用 Azure 認知搜尋來編製索引。  

#### <a name="solution"></a>解決方法
藉由使用 Azure 佇列，您可以實作解決方案，提供跨兩個或多個資料分割或儲存系統的最終一致性。

為了說明這種方法，我們假設您需要封存離職員工實體。 離職員工實體很少查詢，應該從涉及現有員工的任何活動中排除。 為了滿足這項需求，您將現有員工儲存在 **Current** 資料表中，並將離職員工儲存在 **Archive** 資料表中。 若要封存員工，您必須從 **Current** 資料表中刪除實體，並將實體新增至 **Archive** 資料表。

但是，您無法使用 EGT 來執行這兩項作業。 若要避免因失敗而導致實體同時出現或未出現在這兩個資料表中，封存作業必須最終一致。 下列順序圖說明此作業的步驟。  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE12.png" alt-text="最終一致性的解決方案圖表":::

用戶端會將訊息放入 Azure 佇列中，以起始封存作業 (在此範例中，封存員工 #456)。 背景工作角色會輪詢佇列中的新訊息；若找到新訊息，它會讀取訊息，並將隱藏的複本保留在佇列上。 背景工作角色接著會擷取來自 **目前** 資料表的實體複本、將複本插入 **封存** 資料表中，然後從 **目前** 資料表中刪除原始複本。 最後，如果前述步驟沒有任何錯誤，背景工作角色會從佇列中刪除隱藏的訊息。  

在此範例中，圖表中的步驟 4 將員工插入 **Archive** 資料表中。 員工可新增至 Blob 儲存體中的 blob，或檔案系統中的檔案。  

#### <a name="recover-from-failures"></a>從失敗中復原
在圖表中，步驟 4-5 中的作業必須是「等冪」，以防萬一背景工作角色需要重新啟動封存作業。 如果您使用表格儲存體，請在步驟 4 中使用「插入或取代」作業 (在您使用的用戶端程式庫中)；在步驟 5 中，請使用「如果存在即刪除」作業。 如果您使用其他儲存體系統，則必須使用適當的等冪作業。  

如果背景工作角色從未完成圖表中的步驟 6，則在逾時之後，訊息會重新出現在佇列上，讓背景工作角色嘗試重新處理。 背景工作角色可以檢查佇列上某個訊息的讀取次數，如有必要，就將訊息標示為「有害」訊息，以傳送至另一個佇列進行調查。 如需讀取佇列訊息及檢查清除佇列計數的詳細資訊，請參閱[取得訊息](/rest/api/storageservices/Get-Messages)。  

表格儲存體和佇列儲存體的某些錯誤是暫時性，用戶端應用程式要有適當的重試邏輯來處理這些錯誤。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 此解決方案不支援交易隔離。 例如，當背景工作角色介於圖表中的步驟 4 和 5 之間時，用戶端可能讀取 **Current** 和 **Archive** 資料表，並且看到不一致的資料檢視。 資料最終將會一致。  
* 您必須確定步驟 4-5 是等冪，才能確保最終一致性。  
* 您可以使用多個佇列和背景工作角色執行個體來調整方案。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您想要確保存在於不同磁碟分割或資料表中的實體之間保有最終一致性，請使用此模式。 您可以擴充此模式，以確保在表格儲存體、Blob 儲存體及其他非 Azure 儲存體資料來源 (例如資料庫或檔案系統) 之間，作業會達到最終一致性。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體群組交易](#entity-group-transactions)  
* [合併或取代](#merge-or-replace)  

> [!NOTE]
> 如果交易隔離對解決方案而言很重要，請考慮重新設計資料表，讓您能夠使用 EGT。  
> 
> 

### <a name="index-entities-pattern"></a>索引實體模式
維護索引項目，啟用有效的搜尋以傳回實體清單。  

#### <a name="context-and-problem"></a>內容和問題
表格儲存體會使用 `PartitionKey` 和 `RowKey` 值，自動為實體編製索引。 這可讓用戶端應用程式使用點查詢，有效率地取出實體。 例如，就下列資料表結構來說，用戶端應用程式可以使用門名稱和員工識別碼 (`PartitionKey` 和 `RowKey` 值)，有效率地取出個別員工實體。  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE13.png" alt-text="員工實體結構的圖形，用戶端應用程式可以使用部門名稱和員工識別碼 (PartitionKey 和 RowKey) ，有效率地取得個別員工實體。":::

如果您還想要根據另一個非唯一屬性 (例如姓氏) 的值，以取出員工實體清單，則必須使用效率較低的分割區掃描。 此掃描會尋找相符項目，而不是使用索引直接查閱。 這是因為表格儲存體不提供次要索引。  

#### <a name="solution"></a>解決方法
就先前的實體結構而言，若要依姓氏來查閱，您必須維護員工識別碼清單。 如果您想要取出具有特定姓氏 (例如 Jones) 的員工實體，則必須先針對姓氏為 Jones 的員工，找出員工識別碼清單，然後取出這些員工實體。 有三個主要的選項可儲存員工識別碼清單：  

* 使用 Blob 儲存體。  
* 在與員工實體相同的磁碟分割中建立索引實體。  
* 在個別的資料分割或資料表中建立索引實體。  

選項 1：使用 Blob 儲存體  

為每個唯一姓氏建立 blob，並針對該姓氏的員工，在每個 blob 中儲存 `PartitionKey` (部門) 和 `RowKey` (員工識別碼) 值的清單。 當您新增或刪除員工時，請確保相關 blob 的內容與員工實體最終一致。  

選項 2：在相同的資料分割中建立索引實體  

使用儲存下列資料的索引實體：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE14.png" alt-text="此圖顯示的員工實體，具有字串包含姓氏相同的員工識別碼清單":::

對於姓氏儲存在 `RowKey` 中的員工，`EmployeeIDs` 屬性包含員工識別碼清單。  

下列步驟概述您新增員工時應該遵循的程序。 在此範例中，我們要在 Sales 部門中新增識別碼為 000152 且姓氏為 Jones 的員工：  

1. 取出具有 `PartitionKey` 值 "Sales" 和 `RowKey` 值 "Jones" 的索引實體。 儲存此實體的 ETag　以在步驟 2 中使用。  
2. 建立實體群組交易 (也就是批次作業)，以插入新的員工實體 (`PartitionKey` 值 "Sales" 和 `RowKey` 值 "000152") 和更新索引實體 (`PartitionKey` 值 "Sales" 和 `RowKey` 值 "Jones")。 EGT 的作法是將新的員工識別碼加入 EmployeeIDs 欄位中的清單。 如需 EGT 的詳細資訊，請參閱[實體群組交易](#entity-group-transactions)。  
3. 如果 EGT 因為開放式同步存取錯誤而失敗 (也就是其他人已修改索引實體)，則您必須從步驟 1 重新開始。  

如果您使用第二個選項，則可以採用類似的方法來刪除員工。 變更員工的姓氏稍微複雜一些，因為您需要執行 GET 來更新三個實體：員工實體、舊姓氏的索引實體，以及新姓氏的索引實體。 進行任何變更之前，您必須先取出每個實體，以便取出 ETag 值，讓您之後利用開放式同步存取來執行更新。  

當您需要查閱部門中具有特定姓氏的所有員工時，下列步驟概述您應該遵循的程序。 在此範例中，我們要在 Sales 部門中查閱姓氏為 Jones 的所有員工：  

1. 取出具有 `PartitionKey` 值 "Sales" 和 `RowKey` 值 "Jones" 的索引實體。  
2. 剖析 `EmployeeIDs` 欄位中的員工識別碼清單。  
3. 如果您需要其中每個員工的其他資訊 (例如電子郵件地址)，請使用 `PartitionKey` 值 "Sales" 和 `RowKey` 值，從您在步驟 2 中取得的員工清單取出每個員工實體。  

選項 3：在個別的資料分割或資料表中建立索引實體  

使用此選項時，請使用儲存下列資料的索引實體：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE15.png" alt-text="顯示員工索引實體的螢幕擷取畫面，其中包含 RowKey 和 PartitionKey 中儲存姓氏的員工的員工識別碼清單。":::

`EmployeeIDs`屬性包含員工識別碼清單，其中包含儲存在和中的姓氏 `RowKey` `PartitionKey` 。  

您無法使用 EGY 來維持一致性，因為索引實體和員工實體位於不同的分割區。 請確定索引實體與員工實體最終一致。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 此解決方案至少需要兩個查詢來取出相符實體：一個用來查詢索引實體以取得 `RowKey` 值清單，其他查詢用來取出清單中的每個實體。  
* 因為單一實體的大小上限為 1 MB，解決方案中的選項 2 和選項 3 假設任何特定姓氏的員工識別碼清單決不超過 1 MB。 如果員工識別碼清單的大小可能超過 1 MB，請使用選項 1，並將索引資料儲存在 Blob 儲存體中。  
* 如果您使用選項 2 (使用 EGT 處理新增和刪除員工，以及變更員工的姓氏)，則必須評估特定分割區的交易量是否將接近可擴縮性限制。 如果是這種情況，您應該考慮最終一致的解決方案 (選項 1 或選項 3)。 這些都使用佇列來處理更新要求，還可讓您將索引實體儲存在不同於員工實體的分割區。  
* 此解決方案中的選項 2 假設您想要在部門內依姓氏查閱。 例如，您想要在 Sales 部門中取出姓氏為 Jones 的員工清單。 如果您想要查閱整個組織中姓氏為 Jones 的所有員工，請使用選項 1 或選項 3。
* 您可以執行以佇列為基礎的解決方案，以達到最終一致性。 如需詳細資訊，請參閱[最終一致的交易模式](#eventually-consistent-transactions-pattern)。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您想要查閱的一組實體全部都有一個共同屬性值 (例如，姓氏為 Jones 的所有員工)，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [複合索引鍵模式](#compound-key-pattern)  
* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  
* [實體群組交易](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>反正規化模式
將相關資料結合在單一實體中，讓您透過單點查詢擷取所有您所需的資料。  

#### <a name="context-and-problem"></a>內容和問題
在關係資料庫中，您通常會將資料正規化，以移除當查詢從多個資料表取出資料時出現的重複資料。 如果您將 Azure 資料表中的資料標準化，您必須從用戶端到伺服器往返多次才能擷取相關的資料。 例如，就下列資料表結構而言，則您需要兩次來回行程，才能取出部門的詳細資料。 其中，一個行程擷取含有經理識別碼的部門實體，第二個行程在員工實體中擷取經理的詳細資料。  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE16.png" alt-text="部門實體和員工實體的圖形":::

#### <a name="solution"></a>解決方法
不要將資料儲存在兩個不同的實體中，而是將資料反正規化，並將管理員詳細資料的複本保存在部門實體中。 例如：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE17.png" alt-text="反正規化且合併的部門實體圖形":::

由於部門實體中儲存這些屬性，您現在可以使用點查詢，取出部門相關的所有詳細資料。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 儲存資料兩次還有一些相關的成本負擔。 減少對表格儲存體的要求可提升效能，即使儲存體成本略有增加，通常也顯示得微不足道。 此外，由於擷取部門詳細資料所需的交易數目減少，也局部抵銷這項成本。  
* 您必須讓儲存管理員相關資訊的兩個實體保有一致性。 您可以使用 EGT 在單一不可部分完成交易中更新多個實體，以處理一致性問題。 在此情況下，部門經理的部門實體和員工實體儲存在相同的分割區。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您經常需要查閱相關資訊，請使用此模式。 此模式可減少用戶端在擷取其所需資料時必須執行的查詢數目。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [複合索引鍵模式](#compound-key-pattern)  
* [實體群組交易](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>複合索引鍵模式
使用複合 `RowKey` 值，讓用戶端透過單點查詢來查閱相關資料。  

#### <a name="context-and-problem"></a>內容和問題
在關聯式資料庫中，很自然會在查詢中使用聯結，以透過單一查詢將相關資料片段傳回至用戶端。 例如，您可以使用員工識別碼來查閱包含該員工之績效和考核資料的相關實體清單。  

假設您在表格儲存體中使用下列結構來儲存員工實體：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE18.png" alt-text="您應該用來將員工實體儲存在資料表儲存體中的員工實體結構圖形。":::

您也需要儲存員工在組織上班各年度的考核和績效歷史資料，而且您需要依年份存取這項資訊。 建立另一個資料表來儲存具有下列結構的實體，是可行選項之一：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE19.png" alt-text="員工考核實體的圖形":::

使用此方法時，您可能決定在新的實體中重複某些資訊 (例如名字和姓氏)，讓您透過單一要求來取出資料。 不過，因為您無法使用 EGT 以不可部分完成的方式更新兩個實體，所以無法維持強大的一致性。  

#### <a name="solution"></a>解決方法
使用具有下列結構的實體，在原始資料表中儲存新的實體類型：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE20.png" alt-text="具有複合索引鍵的員工實體圖片":::

請注意，`RowKey` 現在是複合索引鍵，由員工識別碼和考核資料年份所組成。 這可讓您對單一實體使用單一要求，以取出員工的績效和考核資料。  

下列範例說明如何擷取特定員工的所有考核資料 (例如銷售部門的員工 000123)：  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您應該使用適當的分隔符號字元，以輕鬆剖析 `RowKey` 值：例如，**000123_2012**。  
* 您也將此實體與包含同一員工相關資料的其他實體，一起儲存在相同的分割區。 這表示您可以使用 EGT 來維持強大的一致性。
* 您應該考量查詢資料的頻率，以判斷此模式是否合適。 例如，如果您不常存取考核資料，但經常存取主要員工資料，則應該將這兩項資料保持為分開的實體。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您需要儲存一或多個您經常查詢的相關實體，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體群組交易](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  
* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>記錄結尾模式
使用以日期和時間反向順序排序的 `RowKey` 值，取出最近加入分割區的 *n* 個實體。  

> [!NOTE]
> Azure Cosmos DB 中以 Azure 資料表 API 傳回的查詢結果，不會依分割區索引鍵或資料列索引鍵排序。 因此，雖然此模式適用於表格儲存體，但不適用於 Azure Cosmos DB。 如需詳細的功能差異清單，請參閱 [Azure Cosmos DB 和 Azure 資料表儲存體中資料表 API 之間的差異](table-api-faq.md#table-api-vs-table-storage)。

#### <a name="context-and-problem"></a>內容和問題
常見的需求是要能夠取出最近建立的實體，例如員工提交的最近 10 筆費用請款。 資料表查詢支援 `$top` 查詢作業，以從集合傳回前 *n* 個實體。 沒有對等的查詢作業可傳回集合的最後 *n* 個實體。  

#### <a name="solution"></a>解決方法
使用自然以日期/時間反向順序排序的 `RowKey` 來儲存實體，讓最新的項目一律排在資料表的首位。  

比方說，若要能夠擷取某員工最近提交的十筆費用請款，您可以使用衍生自目前日期/時間的反向刻度值。 下列 C# 程式碼範例說明如何針對從最新排序到最舊的 `RowKey`，建立適當的「反向刻度」值：  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

您可以使用下列程式碼來回復為日期/時間值：  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

資料表查詢如下所示：  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您必須為反向刻度值填補前置零，以確保字串值如預期排序。  
* 您必須知道資料分割層級的延展性目標。 請小心不要產生作用點分割區。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
當您需要以日期/時間反向順序來存取實體時，或需要存取最近新增的實體時，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [在前面加上/附加反向模式](#prepend-append-anti-pattern)  
* [取出實體](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>大量刪除模式
將您想要同時刪除的所有實體，儲存在其自己的個別資料表中，以支援刪除大量實體。 只要刪除資料表，就會刪除實體。  

#### <a name="context-and-problem"></a>內容和問題
許多應用程式刪除用戶端應用程式已無需使用的舊資料，或應用程式已封存至其他儲存媒體的舊資料。 您通常會依日期來識別這類資料。 例如，您需要刪除 60 天以前所有登入要求的記錄。  

一種可能的設計是在 `RowKey` 中使用登入要求的日期和時間：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE21.png" alt-text="登入嘗試實體的圖形":::

這種方法可避免分割區熱點，因為應用程式可以在個別的分割區，插入和刪除每個使用者的登入實體。 不過，如果您有大量實體，則這種方法可能耗費成本又耗時。 首先，您需要執行資料表掃描，以識別要刪除的所有實體，然後您必須刪除每個舊的實體。 您可以藉由將多個刪除要求批次處理到 EGT 中，以減少刪除舊實體所需的伺服器往返次數。  

#### <a name="solution"></a>解決方法
為每天的登入嘗試使用個別的資料表。 當您要插入實體時，您可以使用上述實體設計，以避免產生作用點。 現在，刪除舊實體只不過是每天刪除一個資料表 (單一儲存體作業)，不需要每天尋找和刪除成百上千個單一登入實體。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您的設計是否支援應用程式以其他方式使用資料，例如查閱特定實體、連結其他資料或產生彙總資訊？  
* 您的設計在插入新實體時是否可避免產生熱點？  
* 如果您在刪除某個資料表名稱後想要加以重複使用，預期應會有延遲。 最好是一律使用唯一的資料表名稱。  
* 第一次使用新的資料表時，預期會受到些許速率限制，但表格儲存體會逐漸了解存取模式，將分割區分散到各節點。 您應考量您需要建立新資料表的頻率。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您有大量的實體必須同時刪除，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體群組交易](#entity-group-transactions)
* [修改實體](#modify-entities)  

### <a name="data-series-pattern"></a>資料序列模式
將完整資料序列儲存在單一實體中，以盡可能減少您提出的要求數。  

#### <a name="context-and-problem"></a>內容和問題
常見的案例是應用程式需一次儲存必須經常擷取的資料序列。 比方說，您的應用程式可能會記錄每一位員工每小時傳送多少 IM 訊息，然後使用這項資訊來繪製每個使用者在過去 24 小時內傳送的訊息數。 一個設計可為每個員工儲存 24 個實體：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE22.png" alt-text="訊息統計資料實體的圖形":::

採用這種設計，您可以輕鬆地找出並更新實體，以在應用程式需要更新訊息計數值時更新每個員工。 不過，若要擷取資訊以繪製過去 24 小時內的活動圖，您必須擷取 24 個實體。  

#### <a name="solution"></a>解決方法
使用下列設計，以個別屬性來儲存每小時的訊息計數：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE23.png" alt-text="此圖顯示具有各個屬性的訊息統計資料實體":::

透過這項設計，您可以使用合併作業來更新員工在特定時段內的訊息計數。 現在，您可以對單一實體使用要求，以取出您繪製圖表所需的全部資訊。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 如果您的完整資料序列無法放入單一實體中 (一個實體最多可以有 252 個屬性)，請使用替代資料存放區，例如 blob。  
* 如果您有多個用戶端同時更新實體，請使用 **ETag** 來實作開放式同步存取。 如果您有許多用戶端，可能會遇到激烈競爭的情況。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您需要更新和擷取與個別實體相關聯的資料序列，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [大型實體模式](#large-entities-pattern)  
* [合併或取代](#merge-or-replace)  
* [最終一致的交易模式](#eventually-consistent-transactions-pattern) (如果您要將資料序列儲存在 blob 中)  

### <a name="wide-entities-pattern"></a>寬型實體模式
使用多個實際的實體來儲存具有超過 252 個屬性的邏輯實體。  

#### <a name="context-and-problem"></a>內容和問題
單一實體不能有超過 252 個屬性 (不包括必要的系統屬性)，也無法儲存總計超過 1 MB 的資料。 在關聯式資料庫中，您通常會新增資料表，並在實體之間施行一對一關聯性，以避開資料列大小的任何限制。  

#### <a name="solution"></a>解決方法
表格儲存體可讓您儲存多個實體，以代表單一大型商務物件，而此物件有 252 個以上的屬性。 例如，如果您想要儲存每個員工在過去 365 天傳送的 IM 訊息計數，您可以採用下列設計，使用兩個具有不同結構描述的實體：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE24.png" alt-text="此圖顯示具有 Rowkey 01 的訊息統計資料實體和具有 Rowkey 02 的訊息統計資料實體":::

如果您需要進行必須同時更新兩個實體的變更，讓它們彼此保持同步，您可以使用 EGT。 否則，您可以使用合併作業來更新某天的訊息計數。 若要取得個別員工的所有資料，您必須同時取出這兩個實體。 作法是透過兩個有效率的要求 (使用 `PartitionKey` 和 `RowKey` 值)。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列重點：  

* 擷取完成邏輯實體牽涉到至少兩個儲存體交易：一個用來擷取每個實際的實體。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您需要儲存實體，但其大小或屬性數目超過表格儲存體中單一實體的限制，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體群組交易](#entity-group-transactions)
* [合併或取代](#merge-or-replace)

### <a name="large-entities-pattern"></a>大型實體模式
使用 Blob 儲存體來儲存大型屬性值。  

#### <a name="context-and-problem"></a>內容和問題
單一實體無法儲存總計超過 1 MB 的資料。 如果其中一個或多個屬性儲存的值導致實體的總大小超過此值，您就無法將整個實體儲存在表格儲存體中。  

#### <a name="solution"></a>解決方法
如果因為一或多個屬性包含大量資料，導致實體的大小超過 1 MB，您可以將資料儲存在 Blob 儲存體中，然後將 blob 的位址儲存在實體的屬性中。 例如，您可以將員工的相片儲存在 Blob 儲存體中，並將相片的連結儲存在員工實體的 `Photo` 屬性中：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE25.png" alt-text="此圖顯示的員工實體，具有 Photo 字串指向 Blob 儲存體":::

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 若要讓表格儲存體中的實體與 Blob 儲存體中的資料之間維持最終一致性，請使用[最終一致的交易模式](#eventually-consistent-transactions-pattern)來維護實體。
* 擷取完整實體牽涉到至少兩個儲存體交易：一個用來擷取實體，另一個擷取 Blob 資料。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
當您需要儲存的實體大小超過表格儲存體中單一實體的限制，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  
* [寬型實體模式](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>在前面加上/附加反向模式
當您有大量插入作業時，請將插入作業分散到多個分割區，以增加可擴縮性。  

#### <a name="context-and-problem"></a>內容和問題
在前面加上或附加實體至您已儲存的實體，通常會導致應用程式將新實體新增至資料分割序列的第一個或最後一個資料分割。 在此情況下，任何特定時刻的所有插入作業，都發生在相同的分割區，因而產生熱點。 這會導致表格儲存體無法在多個節點之間平衡插入的負載，還可能導致應用程式觸及分割區的可擴縮性目標。 例如，假設有一個應用程式會記錄員工的網路和資源存取。 就如下的實體結構而言，如果交易量達到個別分割區的可擴縮性目標，可能會導致當前這一小時的分割區成為熱點：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE26.png" alt-text="實體結構的圖形，如果交易量達到個別資料分割的擴充性目標，可能會導致目前小時的資料分割變成作用點。":::

#### <a name="solution"></a>解決方法
下列替代實體結構在應用程式記錄事件時，可避免任何特定分割區產生熱點：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE27.png" alt-text="此圖顯示的員工實體，具有年、月、日、時及事件識別碼的複合 RowKey":::

請注意，在此範例中，`PartitionKey` 和 `RowKey` 都是複合索引鍵。 `PartitionKey` 同時使用部門和員工識別碼，將記錄分散到多個分割區。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 可避免在插入時產生熱點資料分割的替代索引鍵結構，是否可有效支援用戶端應用程式發出的查詢？  
* 您預期的交易量是否表示很可能達到個別分割區的可擴縮性目標，而受到表格儲存體節流管制？  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
在您存取熱點分割區時，如果交易量很可能導致表格儲存體限制速率，請避免前置/附加反模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [複合索引鍵模式](#compound-key-pattern)  
* [記錄檔結尾模式](#log-tail-pattern)  
* [修改實體](#modify-entities)  

### <a name="log-data-anti-pattern"></a>記錄資料反向模式
一般而言，您應該使用 Blob 儲存體來儲存記錄資料，而不要使用表格儲存體。  

#### <a name="context-and-problem"></a>內容和問題
記錄資料有一個常見的使用案例，就是取出特定日期/時間範圍的一組記錄項目。 例如，您想要尋找應用程式在特定日期的 15:04 和 15:06 之間記錄的所有錯誤和重大訊息。 您不想要使用記錄訊息的日期和時間，以決定將記錄實體的儲存到哪個分割區。 這會產生熱點分割區，因為在任何特定時間，所有記錄實體都共用相同的 `PartitionKey` 值 (請參閱[前置/附加反模式](#prepend-append-anti-pattern))。 例如，記錄訊息的下列實體結構描述會產生熱點分割區，因為應用程式會將當前日期和小時的所有記錄訊息，全部寫入分割區：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE28.png" alt-text="顯示記錄訊息之實體架構的圖形會產生熱資料分割。":::

在此範例中，`RowKey` 包含記錄訊息的日期和時間，以確保記錄訊息依日期/時間順序排序。 `RowKey` 也包含訊息識別碼，以防萬一多個記錄訊息共用相同的日期和時間。  

另一種方法是使用 `PartitionKey`，以確保應用程式將訊息寫入一系列的分割區。 例如，如果記錄訊息的來源可讓您將訊息分散到許多分割區，則您可以採用下列實體結構描述：  

:::image type="content" source="./media/storage-table-design-guide/storage-table-design-IMAGE29.png" alt-text="記錄訊息實體的圖形":::

不過，此結構描述的問題在於，若要取出特定時間範圍內的所有記錄訊息，您必須搜尋資料表中的每個分割區。

#### <a name="solution"></a>解決方法
上一節指出嘗試使用表格儲存體來儲存記錄項目時的問題，並提出兩個不夠理想的設計。 其中一個解決方案會導致產生熱點分割區，導致寫入記錄訊息時效能不佳。 另一個方案會導致查詢效能不佳，因為必須掃描資料表中的每個分割區，才能取出特定時間範圍內的記錄訊息。 Blob 儲存體為這種情況提供更好的解決方案，而 Azure 儲存體分析就是以此來儲存所收集的記錄資料。  

本節概述儲存體分析如何將記錄資料儲存在 Blob 儲存體中，說明以此方式儲存您通常依範圍查詢的資料。  

儲存體分析會以分隔格式，將記錄訊息儲存在多個 blob 中。 分隔格式可方便用戶端應用程式剖析記錄訊息中的資料。  

儲存體分析對 blob 採用命名慣例，可讓您找出一或多個 blob，其中含有您要搜尋的記錄訊息。 例如，名為 "queue/2014/07/31/1800/000001.log" 的 blob 包含 2014 年 7 月 31 日 18:00 起一小時內，與佇列服務有關的記錄訊息。 "000001"表示這是這段期間的第一個記錄檔。 對於檔案中儲存的第一個和最後一個記錄訊息，儲存體分析也會將時間戳記當成 blob 的中繼資料記錄下來。 Blob 儲存體的 API 可讓您根據名稱前置詞，找出容器中的 blob。 若要尋找在 18:00 起一小時內有佇列記錄資料的所有 blob，您可以使用前置詞 "queue/2014/07/31/1800"。  

Storage Analytics 會在內部緩衝處理記錄訊息，然後定期更新適當的 blob，或以最新一批的記錄項目建立新的 blob。 這樣可減少必須寫入 Blob 儲存體的次數。  

如果想要在您自己的應用程式中實作類似的解決方案，請考慮如何處理可靠性、成本和可擴縮性之間的利弊得失。 換句話說，是否將每個記錄項目在發生當下就寫入 Blob 儲存體，還是先在應用程式中緩衝處理更新，再分批寫入 Blob 儲存體中，請評估兩者的效果。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何儲存記錄資料時，請考慮下列幾點：  

* 如果您建立的資料表設計可避免潛在的熱點分割區，您可能會發現無法有效率地存取記錄資料。  
* 在處理記錄資料時，用戶端常需要載入多筆記錄。  
* 雖然記錄資料通常是結構化，但 Blob 儲存體可能是較佳的解決方案。  

### <a name="implementation-considerations"></a>實作考量
本節討論您在實作前面幾節說明的模式時應謹記在心的注意事項。 本節中的範例大多是以 C# 撰寫，並使用儲存體用戶端程式庫 (撰寫本文時為 4.3.0 版)。  

### <a name="retrieve-entities"></a>取出實體
如 [查詢的設計](#design-for-querying)一節中所述，最有效率的查詢是點查詢。 但在某些情況下，您可能需要取出多個實體。 本節說明使用儲存體用戶端程式庫來取出實體的一些常見方法。  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>使用儲存體用戶端程式庫執行點查詢
執行點查詢最簡單的方式是使用 **Retrieve** 資料表作業。 如下列 C# 程式碼片段所示，這項作業會取出 `PartitionKey` 值為 "Sales" 且 `RowKey` 值為 "212" 的實體：  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

請注意，此範例預期取出的實體是 `EmployeeEntity` 類型。  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>使用 LINQ 取出多個實體
您可以搭配使用 LINQ 與儲存體用戶端程式庫，並指定具有 **where** 子句的查詢，以取出多個實體。 若要避免資料表掃描，請一律在 where 子句中加入 `PartitionKey` 值，可能的話，也加入 `RowKey` 值，以避免資料表和分割區掃描。 表格儲存體支援在 where 子句中使用一組有限的比較運算子 (大於、大於或等於、小於、小於或等於、等於、不等於)。 下列 C# 程式碼片段在 Sales 部門中 (假設 `PartitionKey` 儲存部門名稱)，尋找姓氏開頭為 "B" 的所有員工 (假設 `RowKey` 儲存姓氏)：  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

請注意，查詢同時指定 `RowKey` 和 `PartitionKey`，以確保更好的效能。  

下列程式碼範例說明使用 Fluent API 的對等功能 (如需 Fluent API 完整的詳細資訊，請參閱[設計 Fluent API 的最佳做法](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx))：  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> 此範例巢狀多個 `CombineFilters` 方法來包含三個篩選條件。  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>透過查詢取出大量實體
最佳查詢會根據 `PartitionKey` 值和 `RowKey` 值來傳回個別實體。 但在某些情況下，您可能需要從相同的分割區 (甚至從許多分割區) 傳回許多實體。 在此類情況下，您務必要完整測試應用程式的效能。  

查詢表格儲存體一次最多可傳回 1000 個實體，最長可執行五秒。 表格儲存體會傳回接續 Token，可讓用戶端應用程式在下列任何情況下，要求下一組實體：

- 結果集包含超過 1000 個實體。
- 查詢未於五秒內完成。
- 查詢跨越分割區界限。 

如需接續 Token 如何運作的詳細資訊，請參閱[查詢逾時和分頁](/rest/api/storageservices/Query-Timeout-and-Pagination)。  

如果您使用儲存體用戶端程式庫，則從表格儲存體傳回實體時，將自動為您處理接續 Token。 例如，下列程式 C# 程式碼範例會自動處理表格儲存體在回應中傳回的接續 Token：  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

下列 C# 程式碼會明確處理接續權杖：  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

藉由明確使用接續權杖，您將可控制應用程式何時會擷取下一個資料區段。 例如，如果用戶端應用程式可讓使用者逐頁瀏覽儲存在資料表中的實體，使用者可能決定不要逐頁瀏覽查詢所取出的所有實體。 只有當使用者逐頁瀏覽目前區段中的所有實體之後，您的應用程式才會使用接續 Token 來取出下一個區段。 這種方法有幾項優點：  

* 您可以限制從表格儲存體取出的資料量，以及您在網路上移動的資料量。  
* 您可以在 .NET 中執行非同步 I/O。  
* 您可以將接續 Token 序列化到持續性儲存體，萬一應用程式損毀，您還是可以繼續進行。  

> [!NOTE]
> 接續 Token 通常會傳回包含 1000 個實體的區段 (也可能更少)。 如果您使用 **Take** 傳回符合查閱準則的前 n 個實體，以限制查詢所傳回的項目數，情況也是一樣。 表格儲存體傳回的區段可能包含不到 n 個實體，還會傳回接續 Token，讓您取出其餘的實體。  
> 
> 

下列 C# 程式碼說明如何修改區段內傳回的實體數目：  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>伺服器端預測
單一實體最多可以有 255 個屬性，且大小上限為 1 MB。 當您查詢資料表和取出實體時，您可能不需要所有屬性，以避免不必要地傳輸資料 (有助於降低延遲和成本)。 您可以使用伺服器端預測，僅傳送您需要的屬性。 下列範例從查詢所選取的實體中，只取出 `Email` 屬性 (連同 `PartitionKey`、`RowKey`、`Timestamp` 和 `ETag`)。  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

請注意，`RowKey` 值即使未納入要取出的屬性清單中，也可供使用。  

### <a name="modify-entities"></a>修改實體
儲存體用戶端程式庫可讓您修改表格儲存體中儲存的實體，包括插入、刪除和更新實體。 您可以使用 EGT 來整批處理多個插入、更新和刪除作業，以減少所需的來回行程次數，提升解決方案的效能。  

儲存體用戶端程式庫執行 EGT 時所擲回的例外狀況，通常起因於實體的索引造成批次失敗。 當您偵錯使用 EGT 的程式碼時，這會很有幫助。  

您也應該考量您的設計會如何影響用戶端應用程式處理並行存取和更新作業的方式。  

#### <a name="managing-concurrency"></a>管理並行存取
根據預設，表格儲存體會在個別實體的層級上，針對插入、合併和刪除作業，實作開放式同步存取檢查，但用戶端可以強制表格儲存體略過這些檢查。 如需詳細資訊，請參閱[在 Microsoft Azure 儲存體中管理並行](../storage/blobs/concurrency-manage.md)。  

#### <a name="merge-or-replace"></a>合併或取代
`TableOperation` 類別的 `Replace` 方法一律會取代表格儲存體中的完整實體。 如果您在要求中未包含某個屬性，但該屬性存在於已儲存的實體中，則要求會從已儲存的實體中移除該屬性。 除非您想要明確地從已儲存的實體中移除屬性，否則即必須在要求中包含每個屬性。  

您可以使用 `TableOperation` 類別的 `Merge` 方法，以減少您更新實體時傳送至表格儲存體的資料量。 `Merge` 方法會以要求所包含實體的屬性值，取代已儲存實體中的任何屬性。 如有任何屬性存在於已儲存的實體中，但未包含在要求中，這個方法會保持這些屬性原封不動。 如果您有大型實體，但只需要更新要求中的少數屬性，這就非常有用。  

> [!NOTE]
> 如果實體不存在，`*Replace` 和 `Merge` 方法會失敗。 或者，您可以使用 `InsertOrReplace` 和 `InsertOrMerge` 方法，在實體不存在時建立新實體。  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>使用異質實體類型
表格儲存體是「無結構描述」的資料表存放區。 這表示單一資料表可以儲存多種類型的實體，讓您的設計更有彈性。 下列範例說明用以儲存員工和部門實體的資料表：  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>時間戳記</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

每個實體仍然必須有 `PartitionKey`、`RowKey` 和 `Timestamp` 值，但可以有任何一組屬性。 此外，除非您選擇將實體的類型儲存在某處，否則無法指出這項資訊。 有兩個選項可用來識別實體類型：  

* 在 `RowKey` (或是 `PartitionKey`) 前面加上實體類型。 例如，`EMPLOYEE_000123` 或 `DEPARTMENT_SALES` 作為 `RowKey` 值。  
* 使用個別屬性來記錄實體類型，如下表所示。  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>時間戳記</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td>員工</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td>員工</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>名字</th>
<th>姓氏</th>
<th>Age</th>
<th>電子郵件</th>
</tr>
<tr>
<td>員工</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

如果兩個不同類型的實體可能有相同的索引鍵值，則第一個選項 (在 `RowKey` 前面加上實體類型) 很有用。 它也會將屬於相同類型的實體分組在資料分割中。  

本節所討論的技巧與[繼承關聯性](#inheritance-relationships)的討論特別有關。  

> [!NOTE]
> 請考慮在實體類型值中包含版本號碼，讓用戶端應用程式能夠演化 POCO 物件，並與不同的版本搭配使用。  
> 
> 

本節的其餘部分說明儲存體用戶端程式庫中一些有助於在相同資料表中使用多個實體類型的功能。  

#### <a name="retrieve-heterogeneous-entity-types"></a>取出異質實體類型
如果您使用儲存體用戶端程式庫，有三個選項可讓您使用多個實體類型。  

如果您知道以特定 `RowKey` 和 `PartitionKey` 值儲存的實體類型，則可以在取出實體時指定實體類型。 此選項已運用在先前的兩個範例中 (取出 `EmployeeEntity` 類型的實體)：[使用儲存體用戶端程式庫執行點查詢](#run-a-point-query-by-using-the-storage-client-library)和[使用 LINQ 取出多個實體](#retrieve-multiple-entities-by-using-linq)。  

第二個選項是使用 `DynamicTableEntity` 類型 (屬性包)，而不是具象 POCO 實體類型。 此選項也可能改善效能，因為不需要將實體序列化和還原序列化成 .NET 類型。 下列程式 C# 程式碼可能從資料表中取出不同類型的多個實體，但會以 `DynamicTableEntity` 執行個體的形式傳回所有實體。 然後使用 `EntityType` 屬性來判斷每個實體的類型：  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

若要取出其他屬性，您必須在 `DynamicTableEntity` 類別的 `Properties` 屬性上使用 `TryGetValue` 方法。  

第三個選項是使用 `DynamicTableEntity` 類型和 `EntityResolver` 執行個體來合併。 這可讓您解析為相同查詢中的多個 POCO 類型。 在此範例中，`EntityResolver` 委派使用 `EntityType` 屬性來區別查詢傳回的兩種實體。 `Resolve` 方法使用 `resolver` 委派，將 `DynamicTableEntity` 執行個體解析為 `TableEntity` 執行個體。  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modify-heterogeneous-entity-types"></a>修改異質實體類型
您不需要知道實體的類型就可以刪除實體，而您插入實體時一定會知道實體的類型。 不過，您可以使用 `DynamicTableEntity` 類型來更新實體，而不需要知道其類型，也不需要使用 POCO 實體類別。 下列程式碼範例取出單一實體，而且在更新 `EmployeeCount` 屬性之前會檢查屬性是否存在。  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="control-access-with-shared-access-signatures"></a>使用共用存取簽章來控制存取
您可以使用共用存取簽章 (SAS) 權杖，讓用戶端應用程式直接修改 (和查詢) 資料表實體，而不需要直接向表格儲存體驗證。 一般而言，在您的應用程式中使用 SAS 有三大優點：  

* 您不需要為了讓裝置存取和修改表格儲存體中的實體，而將儲存體帳戶金鑰散發至不安全的平台 (例如行動裝置)。  
* 您可以卸載 web 角色和背景工作角色在管理實體時所執行的一些工作。 您可以卸載至用戶端裝置，例如終端使用者電腦和行動裝置。  
* 您可以將一組受條件約束和限時的權限指派給用戶端 (例如允許唯讀存取特定的資源)。  

如需對表格儲存體使用 SAS 權杖的詳細資訊，請參閱[使用共用存取簽章 (SAS)](../storage/common/storage-sas-overview.md)。  

不過，您仍然必須產生 SAS 權杖，以授權用戶端應用程式存取表格儲存體中的實體。 請在可安全存取儲存體帳戶金鑰的環境中這樣做。 一般而言，您可以使用 Web 或背景工作角色來產生 SAS 權杖，並將其傳送至需要存取您的實體的用戶端應用程式。 由於產生 SAS 權杖並將其傳遞至用戶端仍會產生額外負荷，因此您應考量怎樣最能降低此負荷，尤其是在大量的案例中。  

您可以產生 SAS 權杖，以授與存取資料表中的實體子集。 根據預設，您會為整個資料表建立 SAS 權杖。 但也可以指定 SAS 權杖授與存取一系列的 `PartitionKey` 值，或一系列的 `PartitionKey` 和 `RowKey` 值。 您可以選擇為系統的個別使用者產生 SAS 權杖，使每位使用者的 SAS 權杖只允許他們在表格儲存體中存取自己的實體。  

### <a name="asynchronous-and-parallel-operations"></a>非同步和平行作業
假設您要跨多個資料分割分散您的要求，您可以使用非同步或平行查詢來改善輸送量和用戶端的回應性。
例如，您可以用兩個或更多背景工作角色執行個體，以平行方式存取您的資料表。 您可以有個別的背景工作角色來負責特定的分割區集，或單純有多個背景工作角色執行個體，而每個都能夠存取資料表中的所有分割區。  

在用戶端執行個體內，您可以非同步執行儲存作業，以提高輸送量。 儲存體用戶端程式庫可讓您輕鬆撰寫非同步查詢並修改。 例如，您一開始可能使用同步方法來取出分割區中的所有實體，如下列 C# 程式碼所示：  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

您可以輕鬆地修改此程式碼，讓查詢以非同步方式執行，如下所示：  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

在此非同步範例中，您可以看到下列與同步版本不同的變更：  

* 方法特徵標記現在包含 `async` 修飾元，並且傳回 `Task` 執行個體。  
* 此方法現在會呼叫 `ExecuteSegmentedAsync` 方法，而不是呼叫 `ExecuteSegmented` 方法來取出結果。 此方法使用 `await` 修飾元，以非同步方式取出結果。  

用戶端應用程式可以呼叫此方法許多次，每次 `department` 參數的值都不同。 每個查詢在個別的執行緒上執行。  

`TableQuery` 類別中的 `Execute` 方法沒有非同步版本，因為 `IEnumerable` 介面不支援非同步列舉。  

您也可以透過非同步方式插入、更新和刪除實體。 下列 C# 範例說明如何以簡單的同步方法來插入或取代員工實體：  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

您可以輕鬆地修改此程式碼，讓更新以非同步方式執行，如下所示：  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

在此非同步範例中，您可以看到下列與同步版本不同的變更：  

* 方法特徵標記現在包含 `async` 修飾元，並且傳回 `Task` 執行個體。  
* 此方法現在會呼叫 `ExecuteAsync` 方法，而不是呼叫 `Execute` 方法來更新實體。 此方法使用 `await` 修飾元，以非同步方式取出結果。  

用戶端應用程式可以呼叫多個像這樣的非同步方法，而每個方法引動過程都在個別的執行緒上執行。