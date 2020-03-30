---
title: 設計 Azure Cosmos DB 表，用於縮放和性能
description: Azure 表存儲設計指南：Azure Cosmos DB 和 Azure 表存儲中的可擴展和執行表
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 05/21/2019
author: sakash279
ms.author: akshanka
ms.custom: seodec18
ms.openlocfilehash: 166076d366cbbf7bef24648772beaba9b3a88253
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246470"
---
# <a name="azure-table-storage-table-design-guide-scalable-and-performant-tables"></a>Azure 表存儲表設計指南：可擴展和執行表

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

若要設計可擴充且高效能的資料表，您必須考慮各種因素，包括成本。 如果您先前已為關聯式資料庫設計架構，那麼您應該很熟悉這些考量。 但是，雖然 Azure 資料表儲存體與關聯式模型之間有一些相似之處，但也有許多重要的差異。 這些差異常會導致不同的設計，看起來可能違反直覺性，或讓熟悉關聯式資料庫的人覺得不對勁，但如果您設計的是 NoSQL 索引鍵/值存放區 (例如資料表儲存體)，這就顯得合理了。

表存儲旨在支援雲規模應用程式，這些應用程式可以包含數十億個實體（關係資料庫術語中的"行"），或者支援高事務量的資料集。 因此，您需要以不同的方式考慮如何存儲資料，並瞭解表存儲的工作原理。 設計良好的 NoSQL 資料存儲可以使解決方案比使用關係資料庫的解決方案更擴展（而且成本更低）。 本指南針對這些主題為您提供協助。  

## <a name="about-azure-table-storage"></a>關於 Azure 表存儲
本節重點介紹表存儲的一些關鍵功能，這些功能與性能和可擴充性設計特別相關。 如果您是 Azure 存儲和表存儲的新功能，請參閱[Microsoft Azure 存儲簡介](../storage/common/storage-introduction.md)，並在閱讀本文的其餘部分之前[使用 .NET 開始使用 Azure 表存儲](table-storage-how-to-use-dotnet.md)。 儘管本指南的重點是表存儲，但它確實包括一些關於 Azure 佇列存儲和 Azure Blob 存儲的討論，以及如何在解決方案中將它們與表存儲一起使用。  

表存儲使用表格格式來存儲資料。 在標準術語中，資料表的每個資料列都代表一個實體，而資料行儲存該實體的各種屬性。 每個實體都有一對鍵來唯一地標識它，並且表存儲用於跟蹤實體上次更新的時間戳記列。 時間戳記欄位將自動添加，您無法手動用任意值覆蓋時間戳記。 表存儲使用此上次修改的時間戳記 （LMT） 來管理樂觀併發性。  

> [!NOTE]
> 表存儲 REST API 操作`ETag`還返回從 LMT 派生的值。 在本文中，術語 ETag 和 LMT 可互換使用，因為它們引用相同的基礎資料。  
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


到目前為止，此設計類似于關係資料庫中的表。 關鍵區別是必需列和將多個實體類型存儲在同一表中的能力。 此外，每個使用者定義的屬性（如**NameName**或**Age）** 都有一個資料類型，如整數或字串，就像關係資料庫中的列一樣。 但是，與關係資料庫不同，表存儲的無架構性質意味著屬性不必在每個實體上具有相同的資料類型。 若要將複雜資料類型儲存在單一屬性中，您必須使用序列化格式，例如 JSON 或 XML。 有關詳細資訊，請參閱[瞭解表存儲資料模型](https://msdn.microsoft.com/library/azure/dd179338.aspx)。

您的選擇`PartitionKey`和對`RowKey`良好的表設計至關重要。 存儲在表中的每個實體必須具有`PartitionKey`和`RowKey`的唯一組合。 與關係資料庫表中的鍵一樣，`PartitionKey`對 和`RowKey`值進行索引，以創建支援快速查找的群集索引。 但是，表存儲不會創建任何輔助索引，因此這些索引屬性是唯一的兩個索引屬性（稍後描述的一些模式顯示了如何解決這種明顯的限制）。  

表由一個或多個分區組成，您所做的許多設計決策將圍繞選擇合適的`PartitionKey`分區並`RowKey`優化解決方案。 解決方案可以僅包含一個表，其中包含組織到分區中的所有實體，但通常解決方案具有多個表。 表可説明您以邏輯方式組織實體，並説明您使用存取控制清單管理對資料的訪問。 您可以使用單個存儲操作刪除整個表。  

### <a name="table-partitions"></a>資料表的資料分割
帳戶名稱、表名稱以及`PartitionKey`一起標識表存儲存儲存儲實體的存儲服務中的分區。 分區除了作為實體定址方案的一部分外，還定義了事務的範圍（請參閱本文後面的"[實體組事務](#entity-group-transactions)"部分），並形成表存儲如何擴展的基礎。 有關表分區的詳細資訊，請參閱[表存儲的性能和可伸縮性檢查表](../storage/tables/storage-performance-checklist.md)。  

在表存儲中，單個節點為一個或多個完整分區提供服務，服務通過跨節點的動態負載平衡分區進行擴展。 如果節點處於負載下，表存儲可以將該節點服務的分區範圍拆分到不同的節點上。 當流量消退時，表存儲可以將分區範圍從安靜的節點合併到單個節點上。  

有關表存儲的內部詳細資訊的詳細資訊，特別是它管理分區的方式，請參閱[Microsoft Azure 存儲：具有強一致性的高可用性雲存儲服務](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)。  

### <a name="entity-group-transactions"></a>實體組交易記錄
在表存儲中，實體組事務 （EGT） 是跨多個實體執行原子更新的唯一內置機制。 EGT 也稱為*批次處理事務*。 EGT 只能對存儲在同一分區中的實體（在特定表中共用同一分區鍵）進行操作，因此，每當需要跨多個實體進行原子事務行為時，請確保這些實體位於同一分區中。 這通常是將多個實體類型保留在同一表（和分區）中的原因，並且不為不同的實體類型使用多個表。 單一 EGT 最多可以操作 100 個實體。  如果提交多個併發的 EgT 進行處理，請務必確保這些 EgT 不在通用處理器中常見的實體上運行。 否則，您可能會延遲處理。

EGT 也會帶來需在您設計中評估的潛在取捨。 使用更多分區可提高應用程式的可伸縮性，因為 Azure 具有更多跨節點進行負載平衡請求的機會。 但是，這可能會限制應用程式執行原子事務和保持資料強一致性的能力。 此外，還有些在分割層級上的特定延展性目標，可能會限制單一節點的預期交易輸送量。

有關 Azure 存儲帳戶的可伸縮性目標的詳細資訊，請參閱[標準存儲帳戶的可伸縮性目標](../storage/common/scalability-targets-standard-account.md)。 有關表存儲的可伸縮性目標的詳細資訊，請參閱[表存儲的可伸縮性和性能目標](../storage/tables/scalability-targets.md)。 本指南後續幾節將討論各種設計策略，以協助您管理這類的權衡，並討論如何根據您用戶端應用程式的特定需求，選擇您的資料分割索引鍵。  

### <a name="capacity-considerations"></a>容量考量
下表包括設計表存儲解決方案時需要注意的一些鍵值：  

| Azure 儲存體帳戶的容量總計 | 500 TB |
| --- | --- |
| Azure 儲存體帳戶中的資料表數目 |僅受存儲帳戶容量的限制。 |
| 資料表中的資料分割數目 |僅受存儲帳戶容量的限制。 |
| 資料分割中的實體數目 |僅受存儲帳戶容量的限制。 |
| 個別實體的大小 |最多 1 MB，最多 255 個屬性（包括`PartitionKey` `RowKey`、`Timestamp`和 ）。 |
| 的大小`PartitionKey` |大小高達 1 KB 的字串。 |
| 的大小`RowKey` |大小高達 1 KB 的字串。 |
| 實體組交易記錄的大小 |事務最多隻能包含 100 個實體，並且有效負載的大小必須小於 4 MB。 一個 EGT 只能更新實體一次。 |

有關詳細資訊，請參閱[瞭解表服務資料模型](https://msdn.microsoft.com/library/azure/dd179338.aspx)。  

### <a name="cost-considerations"></a>成本考量
表存儲相對便宜，但作為評估使用表存儲的任何解決方案的一部分，應包括容量使用方式和事務數量的成本估算值。 但是，在許多情況下，存儲非正常化或重復資料以提高解決方案的性能或可擴充性是一種有效的方法。 如需定價的詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。  

## <a name="guidelines-for-table-design"></a>資料表設計指導方針
這些清單總結了在設計表時應牢記的一些關鍵準則。 本指南稍後將更詳細地介紹所有這些問題。 這些準則與您通常遵循的關係資料庫設計準則不同。  

將表存儲設計為可高效*讀取*：

* **設計大量讀取應用程式中的查詢。** 設計表時，在考慮如何更新實體之前，先考慮要運行的查詢（尤其是對延遲敏感的查詢）。 透過這樣的方式，通常可造就一個有效率且高效能的解決方案。  
* **在查詢`PartitionKey`中`RowKey`指定和。** 點*查詢*（如這些）是最有效的表存儲查詢。  
* **考慮儲存重複的實體副本。** 表存儲很便宜，因此請考慮多次存儲同一個實體（使用不同的金鑰），以實現更高效的查詢。  
* **考慮將資料反正規化。** 表存儲很便宜，因此請考慮使資料非正常化。 例如儲存摘要實體，可讓彙總資料的查詢只需存取單一實體。  
* **使用複合索引鍵值。** 您唯一的金鑰是`PartitionKey``RowKey`和 。 例如，使用複合索引鍵值啟用替代的實體索引鍵式存取路徑。  
* **使用查詢預測。**  使用僅選取您所需欄位的查詢，即可減少透過網路傳輸的資料量。  

將表存儲設計為高效*寫入*：  

* **不要創建熱分區。**  選擇可讓您在任何時間點，將要求分散到多個分割上的索引鍵。  
* **避免流量尖峰。** 在合理的時間段內分配流量，避免流量高峰。
* **不一定為每種類型的實體創建單獨的表。**  需要跨實體類型執行不可部分完成的交易時，您可以在相同資料表的相同分割中儲存多個實體類型。
* **考慮必須達到的最大輸送量。** 您必須瞭解表存儲的可伸縮性目標，並確保您的設計不會導致超出這些目標。  

在本指南的後面部分，您將看到將所有這些原則付諸實踐的示例。  

## <a name="design-for-querying"></a>查詢的設計
表存儲可以是讀取密集型、寫入密集型，也可以混合兩者。 本節考慮設計以有效地支援讀取操作。 一般而言，支援有效讀取作業的設計，也可兼顧寫入作業的效率。 但是，在設計以支援寫入操作時還有其他注意事項。 下文將討論這些部分，[設計資料修改](#design-for-data-modification)。

使您能夠有效地讀取資料的一個良好起點是詢問"我的應用程式需要運行哪些查詢才能檢索所需的資料？  

> [!NOTE]
> 使用表存儲，提前糾正設計非常重要，因為以後更改它既困難又昂貴。 例如，在關係資料庫中，通常只需向現有資料庫添加索引即可解決性能問題。 這不是表存儲的選項。  

### <a name="how-your-choice-of-partitionkey-and-rowkey-affects-query-performance"></a>您選擇的`PartitionKey`和`RowKey`如何影響查詢性能
以下示例假定表存儲使用以下結構存儲員工實體（大多數示例省略屬性`Timestamp`以清楚起見）：  

| 資料行名稱 | 資料類型 |
| --- | --- |
| `PartitionKey`（部門名稱） |String |
| `RowKey`（員工 ID） |String |
| `FirstName` |String |
| `LastName` |String |
| `Age` |整數  |
| `EmailAddress` |String |

以下是設計表存儲查詢的一些一般準則。 以下示例中使用的篩選器語法來自表存儲 REST API。 有關詳細資訊，請參閱[查詢實體](https://msdn.microsoft.com/library/azure/dd179421.aspx)。  

* *點查詢*是要使用的最有效的查找，建議用於需要最低延遲的大容量查找或查找。 此類查詢可以使用索引通過指定 和`PartitionKey``RowKey`值來有效地定位單個實體。 例如：`$filter=(PartitionKey eq 'Sales') and (RowKey eq '2')`。  
* 第二最好的是*範圍查詢*。 它使用`PartitionKey`值範圍`RowKey`的 和 篩選器來返回多個實體。 該`PartitionKey`值標識特定分區，`RowKey`並且值標識該分區中的實體子集。 例如：`$filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'`。  
* 第三最好的是*分區掃描*。 它使用`PartitionKey`另一個非鍵屬性上的 和 篩選器，並可能返回多個實體。 該`PartitionKey`值標識特定分區，屬性值為該分區中的實體子集選擇。 例如：`$filter=PartitionKey eq 'Sales' and LastName eq 'Smith'`。  
* *資料表掃描*不包括`PartitionKey`，並且效率低下，因為它會搜索構成表的所有分區，以搜索任何匹配的實體。 無論篩選器是否使用，`RowKey`它都會執行資料表掃描。 例如：`$filter=LastName eq 'Jones'`。  
* Azure 表存儲查詢返回多個實體按`PartitionKey`順序`RowKey`對它們進行排序。 為了避免在用戶端中採用實體，請選擇定義最常見排序`RowKey`順序的 。 Azure Cosmos DB 中的 Azure 表 API 返回的查詢結果不按分區鍵或行鍵排序。 如需詳細的功能差異清單，請參閱 [Azure Cosmos DB 和 Azure 資料表儲存體中資料表 API 之間的差異](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)。

使用"**或**"指定基於`RowKey`值的篩選器會導致分區掃描，並且不被視為範圍查詢。 因此，請避免使用篩選器（如： `$filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')`） 的查詢。  

有關使用存儲用戶端庫運行高效查詢的用戶端代碼的示例，請參閱：  

* [使用存儲用戶端庫運行點查詢](#run-a-point-query-by-using-the-storage-client-library)
* [使用 LINQ 檢索多個實體](#retrieve-multiple-entities-by-using-linq)
* [伺服器端預測](#server-side-projection)  

如需可對儲存在相同資料表中的多個實體類型進行處理的用戶端程式碼範例，請參閱：  

* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  

### <a name="choose-an-appropriate-partitionkey"></a>選擇合適的`PartitionKey`
您選擇的`PartitionKey`應平衡啟用 EGT（確保一致性）的需要與跨多個分區（確保可擴展的解決方案）分配實體的要求。  

在一個極端情況下，您可以將所有實體存儲在單個分區中。 但這可能會限制解決方案的可伸縮性，並阻止表存儲能夠載入平衡請求。 另一個極端，您可以存儲每個分區一個實體。 這是高度可擴展的，使表存儲能夠載入平衡請求，但會阻止您使用實體組事務。  

理想的`PartitionKey`功能使您能夠使用高效的查詢，並有足夠的分區來確保解決方案是可擴展的。 通常，您會發現您的實體將具有一個合適的屬性，該屬性將實體分佈在足夠的分區中。

> [!NOTE]
> 例如，在存儲使用者或員工資訊的系統中，`UserID`可以是一個很好的`PartitionKey`。 您可能有多個實體使用特定作為`UserID`分區鍵。 存儲有關使用者資料的每個實體都分組到單個分區中。 這些實體可通過 EgT 訪問，但仍具有高度可擴充性。
> 
> 

在選擇`PartitionKey`與插入、更新和刪除實體的方式相關的其他注意事項中。 有關詳細資訊，請參閱本文後面的["設計"中的資料修改](#design-for-data-modification)。  

### <a name="optimize-queries-for-table-storage"></a>優化表存儲查詢
表存儲通過使用單個群集索引中的`PartitionKey`和`RowKey`值自動對實體編制索引。 這就是點查詢使用效率最高的原因。 但是，除了`PartitionKey`和`RowKey`上的聚類索引上的索引之外，沒有其他索引。

許多設計必須符合需求，才能讓您根據多個準則查閱實體。 例如，基於電子郵件、員工 ID 或姓氏查找員工實體。 "[表"部分中的](#table-design-patterns)以下模式解決了這些類型的要求。 這些模式還描述瞭解決表存儲不提供輔助索引這一事實的方法。  

* [分區內輔助索引模式](#intra-partition-secondary-index-pattern)：使用不同的`RowKey`值（在同一分區中）存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。  
* [分區間輔助索引模式](#inter-partition-secondary-index-pattern)：在單獨的分區或單獨的表中使用不同的`RowKey`值來存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。  
* [索引實體模式](#index-entities-pattern)：維護索引實體，以啟用返回實體清單的有效搜索。  

### <a name="sort-data-in-table-storage"></a>對表存儲中的資料進行排序

表存儲返回按昇冪排序的查詢結果，基於`PartitionKey`，然後返回`RowKey`。

> [!NOTE]
> Azure Cosmos DB 中的 Azure 表 API 返回的查詢結果不按分區鍵或行鍵排序。 如需詳細的功能差異清單，請參閱 [Azure Cosmos DB 和 Azure 資料表儲存體中資料表 API 之間的差異](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)。

表存儲中的鍵是字串值。 為了確保數值排序正確，應將它們轉換為固定長度，並將其填充為零。 例如，如果您用作 的員工 ID 值`RowKey`是整數值，則應將員工 ID **123**轉換為**00000123**。 

許多應用程式都需要使用以不同順序排序的資料：例如，依名稱或加入日期為員工排序。 ["表"部分中的](#table-design-patterns)以下模式涉及如何為實體交替排序次序：  

* [分區內輔助索引模式](#intra-partition-secondary-index-pattern)：使用不同的`RowKey`值（在同一分區中）存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。  
* [分區間輔助索引模式](#inter-partition-secondary-index-pattern)：在單獨的表中使用不同的分區中使用不同的`RowKey`值來存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。
* [日誌尾模式](#log-tail-pattern)：使用按*n*相反日期和時間順序排序`RowKey`的值，檢索最近添加到分區的 n 個實體。  

## <a name="design-for-data-modification"></a>資料修改的設計
本節著重於最佳化插入、更新和刪除的設計考量。 在某些情況下，您需要評估優化用於針對優化資料修改的設計進行查詢的設計之間的權衡。 此評估類似于您在關係資料庫設計中所做的（儘管管理設計權衡的技術在關係資料庫中是不同的）。 [表設計模式](#table-design-patterns)部分介紹了表存儲的一些詳細設計模式，並重點介紹了其中一些權衡。 實際上，您會發現許多針對查詢實體優化的設計也非常適合修改實體。  

### <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>最佳化插入、更新和刪除作業的效能
要更新或刪除實體，您必須能夠使用`PartitionKey`和`RowKey`值來標識它。 在這方面，您選擇的`PartitionKey`實體和`RowKey`修改實體應遵循與您選擇的類似條件來支援點查詢。 您希望盡可能高效地標識實體。 您不希望使用低效的分區或資料表掃描來查找實體，以便發現更新或刪除實體所需的`PartitionKey`和`RowKey`值。  

["表"部分中的](#table-design-patterns)以下模式涉及優化插入、更新和刪除操作的性能：  

* [大容量刪除模式](#high-volume-delete-pattern)：通過將所有實體存儲在自己的單獨表中同時刪除，從而啟用刪除大量實體。 通過刪除表來刪除實體。  
* [資料數列模式](#data-series-pattern)：將完整的資料數列存儲在單個實體中，以儘量減少發出的請求數。  
* [寬實體模式](#wide-entities-pattern)：使用多個物理實體存儲具有 252 個以上屬性的邏輯實體。  
* [大型實體模式](#large-entities-pattern)：使用 blob 存儲存儲大型屬性值。  

### <a name="ensure-consistency-in-your-stored-entities"></a>確保預存實體中的一致性
另一個會在您選擇最佳化資料修改的索引鍵時造成影響的主要因素，是如何使用不可部分完成的交易確保一致性。 只有對於儲存在相同資料分割中的實體，才能使用 EGT 來運作。  

"[表"部分中的](#table-design-patterns)以下模式解決了管理一致性的問題：  

* [分區內輔助索引模式](#intra-partition-secondary-index-pattern)：使用不同的`RowKey`值（在同一分區中）存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。  
* [分區間輔助索引模式](#inter-partition-secondary-index-pattern)：在單獨的分區或單獨的表中使用不同的`RowKey`值來存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。  
* [最終一致的事務模式](#eventually-consistent-transactions-pattern)：通過使用 Azure 佇列，在分區邊界或存儲系統邊界之間啟用最終一致的行為。
* [索引實體模式](#index-entities-pattern)：維護索引實體，以啟用返回實體清單的有效搜索。  
* [非正常化模式](#denormalization-pattern)：將相關資料合併到單個實體中，使您能夠通過單個點查詢檢索所需的所有資料。  
* [資料數列模式](#data-series-pattern)：將完整的資料數列存儲在單個實體中，以儘量減少發出的請求數。  

有關詳細資訊，請參閱本文後面的[實體組事務](#entity-group-transactions)。  

### <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>確保您針對有效率的修改所做的設計有助於提升查詢效率
在許多情況下，效率查詢的設計都可造就有效的修改，但是您務必要評估這對您的特定案例是否適用。 [表設計模式](#table-design-patterns)中的一些模式顯式評估查詢和修改實體之間的權衡，並且應始終考慮每種操作類型的數量。  

["表"](#table-design-patterns)部分中的以下模式解決了高效查詢設計和設計進行高效資料修改之間的權衡：  

* [複合鍵模式](#compound-key-pattern)：使用`RowKey`複合值使用戶端能夠使用單個點查詢查找相關資料。  
* [日誌尾模式](#log-tail-pattern)：使用按*n*相反日期和時間順序排序`RowKey`的值，檢索最近添加到分區的 n 個實體。  

## <a name="encrypt-table-data"></a>加密資料表資料
.NET Azure 存儲用戶端庫支援用於插入和替換操作的字串實體屬性的加密。 加密字串作為二進位屬性存儲在服務上，並在解密後轉換回字串。    

針對資料表，除了加密原則之外，使用者必須指定要加密的屬性。 指定屬性`EncryptProperty`（對於派生自`TableEntity`的 POCO 實體），或在請求選項中指定加密解析器。 加密解析器是一個委託，它採用分區金鑰、行鍵和屬性名稱，並返回一個布林，指示是否應加密該屬性。 在加密期間，用戶端庫使用此資訊來確定在寫入導線時是否應加密屬性。 委派也提供關於屬性如何加密的可能邏輯。 （例如，如果 X，則加密屬性 A;否則加密屬性 A 和 B。讀取或查詢實體時不必提供此資訊。

當前不支援合併。 由於以前可能使用不同的金鑰組屬性子集進行了加密，因此只需合併新屬性並更新中繼資料即可導致資料丟失。 合併要麼需要進行額外的服務調用才能從服務中讀取預先存在的實體，要麼使用每個屬性的新金鑰。 這兩者都不適合性能原因。     

有關加密表資料的資訊，請參閱[用戶端加密和用於 Microsoft Azure 存儲的 Azure 金鑰保存庫](../storage/common/storage-client-side-encryption.md)。  

## <a name="model-relationships"></a>模型關係
建置網域模型是設計複雜系統時的關鍵步驟。 通常，您可以使用建模過程來標識實體及其之間的關係，作為瞭解業務域和通知系統設計的方法。 本節重點介紹如何將域模型中的一些常見關聯類型轉換為表存儲的設計。 從邏輯資料模型映射到基於 NoSQL 的物理資料模型的過程與設計關係資料庫時使用的過程不同。 關係資料庫設計通常假定資料正常化過程經過優化，以儘量減少冗余。 這種設計還假定一種聲明性查詢功能，它抽象了資料庫工作原理的實現。  

### <a name="one-to-many-relationships"></a>一對多關聯性
商業網域物件之間常會產生一對多關聯性：例如，一個部門有許多員工。 在表存儲中實現一對多關聯性的方法有多種，每種關係都有可能與特定方案相關的優缺點。  

以擁有數萬個部門和員工實體的大型跨國公司為例。 每個部門都有許多員工，每個員工都與一個特定部門相關聯。 一種方法是存儲單獨的部門和員工實體，例如：  

![顯示部門實體和員工實體的圖形][1]

此示例顯示類型之間基於值的`PartitionKey`隱式一對多關聯性。 每個部門可以有許多員工。  

此範例也說明相同資料分割中的部門實體及其相關聯的員工實體。 您可以選擇使用不同的分區、表，甚至存儲帳戶用於不同的實體類型。  

另一種方法是使資料非正常化，並且僅存儲具有非正常化部門資料的員工實體，如以下示例所示。 在此特定方案中，如果您要求能夠更改部門經理的詳細資訊，則此非正常化方法可能不是最佳方法。 為此，您需要更新部門中的每個員工。  

![員工實體的圖形][2]

如需詳細資訊，請參閱本指南下半部的 [去正規化模式](#denormalization-pattern) 。  

下表總結了存儲具有一對多關聯性的員工和部門實體的每個方法的優缺點。 您還應考慮預期執行各種操作的頻率。 如果該操作很少發生，則包含昂貴操作的設計可能是可以接受的。  

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
<li>您可能需要檢索某些用戶端活動的員工和部門實體。</li>
<li>儲存作業會在相同資料分割中執行。 在高交易量下，這可能導致熱點。</li>
<li>不能使用 EGT 將員工轉移到新部門。</li>
</ul>
</td>
</tr>
<tr>
<td>個別的實體類型、不同的資料分割或資料表或儲存體帳戶</td>
<td>
<ul>
<li>您可以使用單一作業更新部門實體或員工實體。</li>
<li>在高事務量下，這可以説明將負載分散到多個分區中。</li>
</ul>
</td>
<td>
<ul>
<li>您可能需要檢索某些用戶端活動的員工和部門實體。</li>
<li>在更新/插入/刪除員工並更新部門時，不能使用 ET 來保持一致性。 例如，更新某個部門實體中的員工計數。</li>
<li>不能使用 EGT 將員工轉移到新部門。</li>
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
<li>如果需要更新部門資訊（這將要求您更新部門中的所有員工），則保持一致性的成本可能很高。</li>
</ul>
</td>
</tr>
</table>

如何從這些選項中進行選擇，以及哪些優缺點最重要，取決於您的特定應用程式方案。 例如，您修改部門實體的頻率如何？ 您的所有員工查詢是否需要其他部門資訊？ 您離分區或存儲帳戶的可伸縮性限制有多近？  

### <a name="one-to-one-relationships"></a>一對一關聯性
域模型可以包括實體之間的一對一關聯性。 如果需要在表存儲中實現一對一關聯性，還必須選擇在需要檢索兩個相關實體時如何連結它們。 此連結可以是隱式的，基於鍵值中的約定，也可以是顯式連結，通過在每個實體中以`PartitionKey`和`RowKey`值的形式存儲到其相關實體的連結。 如需是否應將相關實體儲存於相同資料分割中的討論，請參閱 [一對多關聯性](#one-to-many-relationships)一節。  

還有一些實現注意事項可能導致您在表存儲中實現一對一關聯性：  

* 處理大型實體（有關詳細資訊，請參閱[大型實體模式](#large-entities-pattern)）。  
* 實現存取控制（有關詳細資訊，請參閱[使用共用訪問簽名的控制項訪問](#control-access-with-shared-access-signatures)）。  

### <a name="join-in-the-client"></a>用戶端中的聯結
儘管有方法對表存儲中的關係進行建模，但不要忘記使用表存儲的兩個主要原因是可伸縮性和性能。 如果您發現正在建模許多關係，這些關係會危及解決方案的性能和可擴充性，則應問自己是否需要將所有資料關係構建到表設計中。 如果允許用戶端應用程式執行任何必要的聯接，則可以簡化設計並提高解決方案的可擴充性和性能。  

例如，如果您有包含不經常更改的資料的小表，則可以檢索此資料一次，並將其緩存在用戶端上。 這可以避免重複擷取相同資料的往返。 在本指南中查看的示例中，小型組織中的部門集可能很小且變化很少。 這使得它非常適合用戶端應用程式可以下載一次並緩存為查找資料的資料。  

### <a name="inheritance-relationships"></a>繼承關聯性
如果用戶端應用程式使用一組構成繼承關係的類來表示業務實體，則可以輕鬆地將這些實體保留在表存儲中。 例如，您可能在用戶端應用程式中定義了以下一組類，其中`Person`是抽象類別。

![繼承關係圖][3]

可以使用單個`Person`表在表存儲中保留兩個具體類的實例。 使用如下所示的實體：  

![顯示客戶實體和員工實體的圖形][4]

有關在用戶端代碼中同一表中使用多個實體類型的詳細資訊，請參閱本指南後面的["處理異構實體類型](#work-with-heterogeneous-entity-types)"。 其中提供如何在用戶端程式碼中辨識實體類型的範例。  

## <a name="table-design-patterns"></a>資料表設計模式
在前面的章節中，您瞭解了如何使用查詢以及插入、更新和刪除實體資料的表設計來優化表設計。 本節介紹一些適合與表存儲一起使用的模式。 此外，您將瞭解如何實際解決本指南中之前提出的一些問題和權衡。 下圖總結了不同模式之間的關係：  

![表格設計模式圖][5]

模式圖突出顯示本指南中記錄的模式（藍色）和反模式（橙色）之間的一些關係。 當然還有許多其他模式也值得考量。 例如，表存儲的關鍵方案之一是從[命令查詢責任分離](https://msdn.microsoft.com/library/azure/jj554200.aspx)模式中使用[具體化視圖](https://msdn.microsoft.com/library/azure/dn589782.aspx)模式。  

### <a name="intra-partition-secondary-index-pattern"></a>內部資料分割次要索引模式
使用不同的`RowKey`值（在同一分區中）存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。 通過使用 EgT，副本之間的更新可以保持一致。  

#### <a name="context-and-problem"></a>內容和問題
表存儲通過使用`PartitionKey`和`RowKey`值自動對實體編制索引。 這使用戶端應用程式能夠使用這些值有效地檢索實體。 例如，使用以下表結構，用戶端應用程式可以使用點查詢使用部門名稱和員工 ID（和`PartitionKey``RowKey`值）檢索單個員工實體。 用戶端也可以擷取每個部門內以員工識別碼排序的實體。

![員工實體的圖形][6]

如果還希望根據另一個屬性（如電子郵件地址）的值查找員工實體，則必須使用效率較低的分區掃描來查找匹配項。 這是因為表存儲不提供輔助索引。 此外，沒有選項請求按順序`RowKey`排序的員工清單。  

#### <a name="solution"></a>解決方法
要解決缺少輔助索引的問題，可以存儲每個實體的多個副本，每個副本使用不同的`RowKey`值。 如果使用以下結構存儲實體，則可以根據電子郵件地址或員工 ID 高效地檢索員工實體。 的首碼值`RowKey`，`empid_``email_`使您能夠通過使用一系列電子郵件地址或員工 ID 查詢單個員工或一系列員工。  

![顯示具有不同 RowKey 值的員工實體的圖形][7]

以下兩個篩選準則（一個按員工 ID 查找，一個按電子郵件地址查找）都指定點查詢：  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'email_jonesj@contoso.com')  

如果查詢一系列員工實體，則可以指定按員工 ID 順序排序的範圍，或按電子郵件地址順序排序的範圍。 查詢在 中具有相應首碼的`RowKey`實體。  

* 要查找銷售部門中員工 ID 在 000100 到 000199 範圍內的所有員工，請使用：$filter*（分區鍵 eq 'Sales'）和（RowKey ge'empid_000100'）和（RowKey le'empid_000199'）  
* 要查找銷售部門中所有具有以字母"a"開頭的電子郵件地址的員工，請使用：$filter*（分區鍵 eq 'Sales'）和（RowKey ge'email_a'）和（RowKey lt'email_b'）  
  
前面示例中使用的篩選器語法來自表存儲 REST API。 有關詳細資訊，請參閱[查詢實體](https://msdn.microsoft.com/library/azure/dd179421.aspx)。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 表存儲的使用相對便宜，因此存儲重復資料的成本開銷不應是主要問題。 但是，應始終根據預期的存儲要求評估設計成本，並且僅添加重複的實體來支援用戶端應用程式將運行的查詢。  
* 由於輔助索引實體與原始實體存儲在同一分區中，因此請確保不會超出單個分區的可伸縮性目標。  
* 您可以將重複實體彼此保持一致，方法是使用 EGT 自動更新實體的兩個複本。 這表示您應該將實體的所有複本儲存在相同的資料分割中。 有關詳細資訊，請參閱[使用實體組交易記錄](#entity-group-transactions)。  
* 用於 的值`RowKey`對於每個實體必須是唯一的。 請考慮使用複合索引鍵值。  
* 在`RowKey`中填充數值（例如，員工 ID 000223）可基於上限和下限進行正確的排序和篩選。  
* 不一定需要複製實體的所有屬性。 例如，如果使用 中的電子郵件地址查找實體的查詢`RowKey`永遠不需要員工的年齡，則這些實體可以具有以下結構：

  ![員工實體的圖形][8]

* 通常，最好存儲重復資料，並確保可以通過單個查詢檢索所需的所有資料，而不是使用一個查詢查找實體，而另一個查詢查找所需的資料。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
使用此模式的時機包括：

- 用戶端應用程式需要使用各種不同的金鑰檢索實體。
- 用戶端需要檢索不同排序訂單中的實體。
- 您可以使用各種唯一值標識每個實體。

但是，請確保在使用不同的`RowKey`值執行實體查找時不超過分區可伸縮性限制。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [間資料分割次要索引模式](#inter-partition-secondary-index-pattern)
* [複合索引鍵模式](#compound-key-pattern)
* [實體組交易記錄](#entity-group-transactions)
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>間資料分割次要索引模式
通過在單獨的分區或單獨的表中使用不同的`RowKey`值來存儲每個實體的多個副本。 這允許快速高效的查找，並使用不同的`RowKey`值交替排序次序。  

#### <a name="context-and-problem"></a>內容和問題
表存儲通過使用`PartitionKey`和`RowKey`值自動對實體編制索引。 這使用戶端應用程式能夠使用這些值有效地檢索實體。 例如，使用以下表結構，用戶端應用程式可以使用點查詢使用部門名稱和員工 ID（和`PartitionKey``RowKey`值）檢索單個員工實體。 用戶端也可以擷取每個部門內以員工識別碼排序的實體。  

![員工實體的圖形][9]

如果您也想能夠根據其他屬性 (例如電子郵件地址) 的值尋找員工實體，您必須使用效率較低的資料分割掃描來尋找相符項目。 這是因為表存儲不提供輔助索引。 此外，沒有選項請求按順序`RowKey`排序的員工清單。  

您預計針對這些實體的事務量會很高，並且希望將表存儲速率限制用戶端的風險降至最低。  

#### <a name="solution"></a>解決方法
要解決缺少輔助索引的問題，可以存儲每個實體的多個副本，每個副本使用不同的`PartitionKey`和`RowKey`值。 如果使用以下結構存儲實體，則可以根據電子郵件地址或員工 ID 高效地檢索員工實體。 的首碼值`PartitionKey`，`empid_`並`email_`使您能夠標識要用於查詢的索引。  

![顯示具有主索引的員工實體和具有輔助索引的員工實體的圖形][10]

以下兩個篩選準則（一個按員工 ID 查找，一個按電子郵件地址查找）都指定點查詢：  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') and (RowKey eq 'jonesj@contoso.com')  

如果查詢一系列員工實體，則可以指定按員工 ID 順序排序的範圍，或按電子郵件地址順序排序的範圍。 查詢在 中具有相應首碼的`RowKey`實體。  

* 要查找銷售部門中員工 ID 在**000100**到**000199**範圍內的所有員工，按員工 ID 順序排序，請使用：$filter*（分區鍵 eq'empid_Sales'）和（RowKey ge '000100'） 和 （RowKey le'000199'）  
* 要查找銷售部門中所有以"a"開頭的電子郵件地址，按電子郵件地址順序排序，請使用：$filter*（分區鍵 eq'email_Sales'）和（RowKey ge'a'）和（RowKey lt'b'）  

請注意，前面示例中使用的篩選器語法來自表存儲 REST API。 有關詳細資訊，請參閱[查詢實體](https://msdn.microsoft.com/library/azure/dd179421.aspx)。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您可以讓重複實體彼此之間始終保持一致，方法是使用 [最終一致的交易模式](#eventually-consistent-transactions-pattern) 來維護主要和次要索引實體。  
* 表存儲的使用相對便宜，因此存儲重復資料的成本開銷不應是主要問題。 但是，始終根據預期的存儲要求評估設計成本，並且僅添加重複的實體來支援用戶端應用程式將運行的查詢。  
* 用於 的值`RowKey`對於每個實體必須是唯一的。 請考慮使用複合索引鍵值。  
* 在`RowKey`中填充數值（例如，員工 ID 000223）可基於上限和下限進行正確的排序和篩選。  
* 不一定需要複製實體的所有屬性。 例如，如果使用 中的電子郵件地址查找實體的查詢`RowKey`永遠不需要員工的年齡，則這些實體可以具有以下結構：
  
  ![顯示具有輔助索引的員工實體的圖形][11]
* 通常，最好存儲重復資料，並確保可以使用單個查詢檢索所需的所有資料，而不是使用輔助索引使用一個查詢來查找實體，而另一個查詢則用於查找主索引中所需的資料。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
使用此模式的時機包括：

- 用戶端應用程式需要使用各種不同的金鑰檢索實體。
- 用戶端需要檢索不同排序訂單中的實體。
- 您可以使用各種唯一值標識每個實體。

如果要避免使用不同的`RowKey`值執行實體查找時，請避免超出分區可伸縮性限制時，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  
* [內部資料分割次要索引模式](#intra-partition-secondary-index-pattern)  
* [複合索引鍵模式](#compound-key-pattern)  
* [實體組交易記錄](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>最終一致的交易模式
使用 Azure 佇列，跨資料分割界限或儲存體系統界限啟用最終一致的行為。  

#### <a name="context-and-problem"></a>內容和問題
EGT 可讓您在共用相的資料分割索引鍵的多個實體之間執行不可部分完成的交易。 出於性能和可伸縮性的原因，您可以決定在單獨的分區或單獨的存儲系統中存儲具有一致性要求的實體。 在這種情況下，不能使用 EgT 來保持一致性。 例如，您可能必須在下列項目間維護最終一致性：  

* 儲存在相同資料表的兩個不同分割區中、不同的資料表中或不同儲存體帳戶中的實體。  
* 存儲在表存儲中的實體和存儲在 Blob 存儲中的 Blob。  
* 存儲在表存儲中的實體和檔案系統中的檔。  
* 存儲在表存儲中的實體，但使用 Azure 認知搜索編制索引。  

#### <a name="solution"></a>解決方法
藉由使用 Azure 佇列，您可以實作解決方案，提供跨兩個或多個資料分割或儲存系統的最終一致性。

為了說明此方法，假設您需要能夠存檔以前的員工實體。 前員工實體很少受到質疑，應排除在任何與當前員工打交道的活動之外。 要實現此要求，您需要將活動員工存儲在 **"當前"** 表中，並將前員工存儲在 **"存檔"** 表中。 存檔員工要求您從 **"當前"** 表中刪除實體，並將實體添加到 **"存檔"** 表。

但是，不能使用 EGT 來執行這兩個操作。 若要避免因失敗而導致實體同時出現或未出現在這兩個資料表中，封存作業必須最終一致。 下列順序圖說明此作業的步驟。  

![最終一致性的解決方案圖表][12]

用戶端通過在 Azure 佇列中放置消息來啟動存檔操作（在此示例中，用於存檔員工#456）。 背景工作角色會輪詢佇列中的新訊息；若找到新訊息，它會讀取訊息，並將隱藏的複本保留在佇列上。 背景工作角色接著會擷取來自**目前**資料表的實體複本、將複本插入**封存**資料表中，然後從**目前**資料表中刪除原始複本。 最後，如果前述步驟沒有任何錯誤，背景工作角色會從佇列中刪除隱藏的訊息。  

在此示例中，關係圖中的步驟 4 將員工插入到 **"存檔"** 表中。 它可以將員工添加到 Blob 存儲中的 Blob 或檔案系統中的檔。  

#### <a name="recover-from-failures"></a>從故障中恢復
請務必在關係圖中的步驟 4-5 中的操作是*冪等的*，以防工作人員角色需要重新開機存檔操作。 如果使用表存儲，則對於步驟 4，應使用"插入或替換"操作;對於步驟 4，應使用"插入或替換"操作。對於步驟 5，應在正在使用的用戶端庫中使用"如果存在"操作。 如果您使用的是其他存儲系統，則必須使用適當的冪等操作。  

如果輔助角色從未完成關係圖中的步驟 6，則超時後，消息重新出現在佇列中，以便工作人員角色嘗試重新處理它。 輔助角色可以檢查佇列中的消息已讀取的次數，並在必要時將其標記為"有害"消息，以便將其發送到單獨的佇列進行調查。 有關讀取佇列消息和檢查取消佇列計數的詳細資訊，請參閱[獲取消息](https://msdn.microsoft.com/library/azure/dd179474.aspx)。  

表存儲和佇列存儲中的一些錯誤是暫時性錯誤，用戶端應用程式應包含適當的重試邏輯來處理它們。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 此解決方案不提供事務隔離。 例如，當輔助角色位於關係圖中的步驟 4-5 之間時，用戶端可能會讀取 **"當前**"和 **"存檔"** 表，並看到資料不一致的視圖。 資料最終將會一致。  
* 您必須確保步驟 4-5 是冪等的，以確保最終的一致性。  
* 您可以使用多個佇列和背景工作角色執行個體來調整方案。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您想要確保存在於不同磁碟分割或資料表中的實體之間保有最終一致性，請使用此模式。 可以擴展此模式，以確保跨表存儲和 Blob 存儲以及其他非 Azure 存儲資料來源（如資料庫或檔案系統）的操作的最終一致性。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體組交易記錄](#entity-group-transactions)  
* [合併或取代](#merge-or-replace)  

> [!NOTE]
> 如果事務隔離對解決方案很重要，請考慮重新設計表以使您能夠使用 EGT。  
> 
> 

### <a name="index-entities-pattern"></a>索引實體模式
維護索引項目，啟用有效的搜尋以傳回實體清單。  

#### <a name="context-and-problem"></a>內容和問題
表存儲通過使用`PartitionKey`和`RowKey`值自動對實體編制索引。 這使用戶端應用程式能夠使用點查詢有效地檢索實體。 例如，使用以下表結構，用戶端應用程式可以使用部門名稱和員工 ID （和`PartitionKey``RowKey`） 有效地檢索單個員工實體。  

![員工實體的圖形][13]

如果還希望能夠根據另一個非唯一屬性（如姓氏）的值檢索員工實體的清單，則必須使用效率較低的分區掃描。 此掃描查找匹配項，而不是使用索引直接查找它們。 這是因為表存儲不提供輔助索引。  

#### <a name="solution"></a>解決方法
要啟用具有上述實體結構的姓氏查找，必須維護員工 ID 的清單。 如果要檢索具有特定姓氏的員工實體（如 Jones），必須首先查找以 Jones 為姓氏的員工的員工 ID 清單，然後檢索這些員工實體。 存儲員工 ID 清單有三個主要選項：  

* 使用 Blob 存儲。  
* 在與員工實體相同的磁碟分割中建立索引實體。  
* 在個別的資料分割或資料表中建立索引實體。  

選項 1：使用 Blob 存儲  

為每個唯一姓氏創建一個 Blob，並在每個 Blob 中存儲具有`PartitionKey`該姓氏的員工的`RowKey`（部門）和（員工 ID）值的清單。 添加或刪除員工時，請確保相關 Blob 的內容最終與員工實體一致。  

選項 2：在同一分區中創建索引實體  

使用存儲以下資料的索引實體：  

![顯示員工實體的圖形，字串包含具有相同姓氏的員工 ID 清單][14]

屬性`EmployeeIDs`包含員工 ID 的清單，其中包含存儲在 中的姓氏的員工`RowKey`ID。  

以下步驟概述了添加新員工時應遵循的流程。 在此示例中，我們將在銷售部門中添加 ID 000152 和姓氏 Jones 的員工：  

1. 檢索具有`PartitionKey`值"銷售"和值"Jones"的`RowKey`索引實體。 儲存此實體的 ETag　以在步驟 2 中使用。  
2. 創建一`PartitionKey`個實體組交易記錄（即批次處理工序），該交易記錄插入新的員工實體（值"銷售"和`RowKey`值"000152"），並更新索引實體（`PartitionKey`值"銷售"和`RowKey`值"Jones"）。 EGT 通過將新員工 ID 添加到"員工 ID"欄位中的清單來進行此項調查。 有關 EgT 的詳細資訊，請參閱[實體組事務](#entity-group-transactions)。  
3. 如果 EGT 由於樂觀的併發錯誤（即其他人修改了索引實體）而失敗，則需要在步驟 1 重新開始。  

如果您使用第二個選項，則可以使用類似的方法來刪除員工。 更改員工的姓氏稍微複雜一些，因為您需要運行更新三個實體的 EGT：員工實體、舊姓氏的索引實體和新姓氏的索引實體。 在進行任何更改之前，必須檢索每個實體，以便檢索 ETag 值，然後可以使用樂觀併發來執行更新。  

以下步驟概述了當您需要查找部門中具有特定姓氏的所有員工時應遵循的流程。 在此示例中，我們在銷售部門查找所有姓氏為 Jones 的員工：  

1. 檢索具有`PartitionKey`值"銷售"和值"Jones"的`RowKey`索引實體。  
2. 分析`EmployeeIDs`欄位中的員工 ID 清單。  
3. 如果您需要有關每個員工的其他資訊（如他們的電子郵件地址），請使用`PartitionKey`值"銷售"檢索每個員工實體，並從`RowKey`步驟 2 中獲得的員工清單中檢索值。  

選項 3：在單獨的分區或表中創建索引實體  

對於此選項，請使用存儲以下資料的索引實體：  

![顯示員工實體的圖形，字串包含具有相同姓氏的員工 ID 清單][15]

屬性`EmployeeIDs`包含員工 ID 的清單，其中包含存儲在 中的姓氏的員工`RowKey`ID。  

不能使用 ET 來保持一致性，因為索引實體位於與員工實體不同的分區中。 確保索引實體最終與員工實體一致。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 此解決方案至少需要兩個查詢來檢索匹配的實體：一個查詢索引實體以獲取`RowKey`值清單，然後查詢以檢索清單中的每個實體。  
* 由於單個實體的最大大小為 1 MB，因此解決方案中的選項 2 和選項 3 假定任何特定姓氏的員工 ID 清單永遠不會超過 1 MB。 如果員工 ID 清單的大小可能超過 1 MB，請使用選項 1 並將索引資料存儲在 Blob 存儲中。  
* 如果使用選項 2（使用 ET 來處理添加和刪除員工，以及更改員工的姓氏），則必須評估事務量是否接近特定分區中的可伸縮性限制。 如果是這種情況，應考慮最終一致的解決方案（選項 1 或選項 3）。 這些佇列使用佇列來處理更新要求，並使您能夠將索引實體存儲在與員工實體不同的分區中。  
* 此解決方案中的選項 2 假定您希望在部門內按姓氏查找。 例如，您希望檢索銷售部門中姓氏為 Jones 的員工清單。 如果您希望能夠查找整個組織中所有姓氏為 Jones 的員工，請使用選項 1 或選項 3。
* 您可以實現基於佇列的解決方案，提供最終的一致性。 有關詳細資訊，請參閱[最終一致的事務模式](#eventually-consistent-transactions-pattern)。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果要查找一組共用公共屬性值的實體（如姓氏為 Jones 的所有員工）時，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [複合索引鍵模式](#compound-key-pattern)  
* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  
* [實體組交易記錄](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>反正規化模式
將相關資料結合在單一實體中，讓您透過單點查詢擷取所有您所需的資料。  

#### <a name="context-and-problem"></a>內容和問題
在關係資料庫中，通常對資料進行正常化以刪除查詢從多個表檢索資料時發生的重複。 如果您將 Azure 資料表中的資料標準化，您必須從用戶端到伺服器往返多次才能擷取相關的資料。 例如，使用下表結構，需要兩次往返來檢索部門的詳細資訊。 一次行程獲取包含經理 ID 的部門實體，第二次行程在員工實體中獲取經理的詳細資訊。  

![部門實體和員工實體的圖形][16]

#### <a name="solution"></a>解決方法
不要將資料儲存在兩個不同的實體中，而是將資料反正規化，並將管理員詳細資料的複本保存在部門實體中。 例如：  

![非正常化和合併部門實體的圖形][17]

使用這些屬性存儲的部門實體後，現在可以使用點查詢檢索部門所需的所有詳細資訊。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 儲存資料兩次還有一些相關的成本負擔。 對表存儲的請求減少所帶來的性能優勢通常大於存儲成本的邊際增長。 此外，通過減少獲取部門詳細資訊所需的事務數，可以部分抵消此成本。  
* 您必須讓儲存管理員相關資訊的兩個實體保有一致性。 可以使用 EGT 在單個原子事務中更新多個實體來處理一致性問題。 在這種情況下，部門經理的部門實體和員工實體將存儲在同一分區中。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您經常需要查閱相關資訊，請使用此模式。 此模式可減少用戶端在擷取其所需資料時必須執行的查詢數目。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [複合索引鍵模式](#compound-key-pattern)  
* [實體組交易記錄](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>複合索引鍵模式
使用複合`RowKey`值使用戶端能夠使用單個點查詢查找相關資料。  

#### <a name="context-and-problem"></a>內容和問題
在關係資料庫中，在單個查詢中使用聯接將相關資料段返回給用戶端是很自然的。 例如，您可以使用員工識別碼來查閱包含該員工之績效和考核資料的相關實體清單。  

假設您使用以下結構將員工實體存儲在表存儲中：  

![員工實體的圖形][18]

您還需要存儲與員工為您的組織工作的每一年的評論和績效相關的歷史資料，並且您需要能夠按年訪問此資訊。 建立另一個資料表來儲存具有下列結構的實體，是可行選項之一：  

![員工審核實體的圖形][19]

使用此方法，您可以決定在新實體中複製一些資訊（如名字和姓氏），以便使用單個請求檢索資料。 但是，您無法保持強一致性，因為不能使用 EGT 以原子方式更新這兩個實體。  

#### <a name="solution"></a>解決方法
通過使用具有以下結構的實體，在原始表中存儲新的實體類型：  

![使用複合鍵的員工實體圖形][20]

請注意，`RowKey`現在如何成為複合金鑰，由員工 ID 和審核資料年份組成。 這使您能夠檢索員工的績效，並查看資料，只需單個實體的請求。  

下列範例說明如何擷取特定員工的所有考核資料 (例如銷售部門的員工 000123)：  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您應該使用適當的分隔符號，以便輕鬆分析`RowKey`值：例如 **，000123_2012**。  
* 您還將此實體存儲在同一分區中，這些實體包含同一員工的相關資料。 這意味著您可以使用 EgT 保持強一致性。
* 應考慮查詢資料的頻率，以確定此模式是否合適。 例如，如果您不經常訪問審核資料，並且經常訪問主要員工資料，則應將它們保留為單獨的實體。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您需要儲存一或多個您經常查詢的相關實體，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體組交易記錄](#entity-group-transactions)  
* [使用異質性實體類型](#work-with-heterogeneous-entity-types)  
* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>記錄結尾模式
使用按*n*相反日期和時間順序排序`RowKey`的值檢索最近添加到分區的 n 實體。  

> [!NOTE]
> Azure Cosmos DB 中的 Azure 表 API 返回的查詢結果不按分區鍵或行鍵排序。 因此，雖然此模式適用于表存儲，但它不適合 Azure Cosmos DB。 有關要素差異的詳細清單，請參閱[Azure Cosmos DB 中的表 API 和 Azure 表存儲 中的表 API 之間的差異](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior)。

#### <a name="context-and-problem"></a>內容和問題
常見的需求是要能夠取出最近建立的實體，例如員工提交的最近 10 筆費用請款。 表查詢支援`$top`查詢操作，以便從集返回第一個*n*實體。 沒有等效的查詢操作來返回集中的最後一個*n*個實體。  

#### <a name="solution"></a>解決方法
使用以相反日期/時間`RowKey`順序自然排序的 存儲實體，因此最近的條目始終是表中的第一個條目。  

比方說，若要能夠擷取某員工最近提交的十筆費用請款，您可以使用衍生自目前日期/時間的反向刻度值。 以下 C# 代碼示例顯示了為從最新到最舊排序的一個`RowKey`類型創建合適的"倒點數"值的一種方法：  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

您可以使用以下代碼返回日期/時間值：  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

資料表查詢如下所示：  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您必須將反向刻度值填充前置字元為零，以確保字串值按預期排序。  
* 您必須知道資料分割層級的延展性目標。 小心不要創建熱點分區。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
當您需要以相反的日期/時間循序存取實體時，或者當您需要訪問最近添加的實體時，使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [在前面加上/附加反向模式](#prepend-append-anti-pattern)  
* [檢索實體](#retrieve-entities)  

### <a name="high-volume-delete-pattern"></a>大量刪除模式
通過將所有實體存儲在自己的單獨表中，以便同時刪除大量實體，從而啟用刪除大量實體。 通過刪除表來刪除實體。  

#### <a name="context-and-problem"></a>內容和問題
許多應用程式刪除用戶端應用程式已無需使用的舊資料，或應用程式已封存至其他儲存媒體的舊資料。 您通常按日期標識此類資料。 例如，您需要刪除所有超過 60 天的登錄請求的記錄。  

一種可能的設計是在 中使用登錄請求的日期和時間`RowKey`：  

![登錄嘗試實體的圖形][21]

此方法可避免分區熱點，因為應用程式可以在單獨的分區中插入和刪除每個使用者的登錄實體。 但是，如果您擁有大量實體，此方法可能成本高昂且耗時。 首先，您需要執行資料表掃描以標識要刪除的所有實體，然後必須刪除每個舊實體。 您可以藉由將多個刪除要求批次處理到 EGT 中，以減少刪除舊實體所需的伺服器往返次數。  

#### <a name="solution"></a>解決方法
為每天的登入嘗試使用個別的資料表。 您可以使用前面的實體設計來避免插入實體時的熱點。 刪除舊實體現在只是每天刪除一個表（單個存儲操作）的問題，而不是每天查找和刪除數百個單獨的登錄實體。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 您的設計是否支援應用程式使用資料的其他方式，例如查找特定實體、與其他資料連結或生成聚合資訊？  
* 您的設計在插入新實體時是否可避免產生熱點？  
* 如果您在刪除某個資料表名稱後想要加以重複使用，預期應會有延遲。 最好是一律使用唯一的資料表名稱。  
* 首次使用新表時，預計會有一些速率限制，而表存儲將瞭解訪問模式並在節點之間分發分區。 您應考量您需要建立新資料表的頻率。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您有大量的實體必須同時刪除，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體組交易記錄](#entity-group-transactions)
* [修改實體](#modify-entities)  

### <a name="data-series-pattern"></a>資料序列模式
將完整資料序列儲存在單一實體中，以盡可能減少您提出的要求數。  

#### <a name="context-and-problem"></a>內容和問題
常見的案例是應用程式需一次儲存必須經常擷取的資料序列。 比方說，您的應用程式可能會記錄每一位員工每小時傳送多少 IM 訊息，然後使用這項資訊來繪製每個使用者在過去 24 小時內傳送的訊息數。 一個設計可為每個員工儲存 24 個實體：  

![消息統計資訊實體的圖形][22]

採用這種設計，您可以輕鬆地找出並更新實體，以在應用程式需要更新訊息計數值時更新每個員工。 不過，若要擷取資訊以繪製過去 24 小時內的活動圖，您必須擷取 24 個實體。  

#### <a name="solution"></a>解決方法
使用以下設計，使用單獨的屬性來存儲每小時的消息計數：  

![顯示具有分隔屬性的消息統計資訊實體的圖形][23]

透過這項設計，您可以使用合併作業來更新員工在特定時段內的訊息計數。 現在，您可以使用單個實體的請求檢索繪製圖表所需的所有資訊。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 如果完整的資料數列不適合單個實體（實體最多隻能具有 252 個屬性），請使用替代資料存儲（如 Blob）。  
* 如果有多個用戶端同時更新實體，請使用**ETag**實現樂觀併發。 如果您有許多用戶端，則可能會遇到高爭。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
如果您需要更新和擷取與個別實體相關聯的資料序列，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [大型實體模式](#large-entities-pattern)  
* [合併或取代](#merge-or-replace)  
* [最終一致的事務模式](#eventually-consistent-transactions-pattern)（如果要將資料數列存儲在 Blob 中）  

### <a name="wide-entities-pattern"></a>寬型實體模式
使用多個實際的實體來儲存具有超過 252 個屬性的邏輯實體。  

#### <a name="context-and-problem"></a>內容和問題
單個實體不能超過 252 個屬性（不包括強制系統屬性），並且不能總共存儲超過 1 MB 的資料。 在關係資料庫中，通常可以通過添加新表並強制執行行之間的 1 對 1 關係來解決行大小的任何限制。  

#### <a name="solution"></a>解決方法
通過使用表存儲，可以存儲多個實體以表示具有 252 個以上屬性的單個大型業務物件。 例如，如果要存儲每個員工在過去 365 天內發送的 IM 消息數的計數，則可以使用以下設計，該設計使用具有不同架構的兩個實體：  

![圖形顯示消息統計資訊實體與羅基 01 和消息統計資訊實體與 Rowkey 02][24]

如果您需要進行必須同時更新兩個實體的變更，讓它們彼此保持同步，您可以使用 EGT。 否則，您可以使用合併作業來更新某天的訊息計數。 若要檢索單個員工的所有資料，必須檢索這兩個實體。 可以使用兩個同時使用 值`PartitionKey`和`RowKey`值的有效請求執行此操作。  

#### <a name="issues-and-considerations"></a>問題和考量
在決定如何實現此模式時，請考慮以下幾點：  

* 擷取完成邏輯實體牽涉到至少兩個儲存體交易：一個用來擷取每個實際的實體。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
當您需要存儲其大小或屬性數超過表存儲中單個實體限制的實體時，使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [實體組交易記錄](#entity-group-transactions)
* [合併或取代](#merge-or-replace)

### <a name="large-entities-pattern"></a>大型實體模式
使用 Blob 存儲存儲大型屬性值。  

#### <a name="context-and-problem"></a>內容和問題
單個實體總共不能存儲超過 1 MB 的資料。 如果一個或多個屬性存儲的值會導致實體的總大小超過此值，則無法將整個實體存儲在表存儲中。  

#### <a name="solution"></a>解決方法
如果實體的大小超過 1 MB，因為一個或多個屬性包含大量資料，則可以將資料存儲在 Blob 存儲中，然後將 Blob 的位址存儲在實體中的屬性中。 例如，您可以將員工的照片存儲在 Blob 存儲中，並將指向照片的連結存儲在員工實體`Photo`的屬性中：  

![顯示員工實體的圖形，用於指向 Blob 存儲的照片字串][25]

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 要保持表存儲中的實體與 Blob 存儲中的資料之間的最終一致性，請使用[最終一致的事務模式](#eventually-consistent-transactions-pattern)來維護實體。
* 擷取完整實體牽涉到至少兩個儲存體交易：一個用來擷取實體，另一個擷取 Blob 資料。  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
當您需要存儲其大小超過表存儲中單個實體限制的實體時，請使用此模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [最終一致的交易模式](#eventually-consistent-transactions-pattern)  
* [寬型實體模式](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>在前面加上/附加反向模式
當插入量大時，通過將插入點跨多個分區展開來提高可伸縮性。  

#### <a name="context-and-problem"></a>內容和問題
在前面加上或附加實體至您已儲存的實體，通常會導致應用程式將新實體新增至資料分割序列的第一個或最後一個資料分割。 在這種情況下，任何特定時間的所有插入都發生在同一分區中，從而創建一個熱點。 這可以防止表存儲跨多個節點進行負載平衡插入，並可能導致應用程式達到分區的可伸縮性目標。 例如，考慮記錄員工訪問網路和資源的應用程式的情況。 如果事務量達到單個分區的可伸縮性目標，則以下實體結構可能會導致當前小時的分區成為熱點：  

![員工實體的圖形][26]

#### <a name="solution"></a>解決方法
當應用程式記錄事件時，以下替代實體結構可避免任何特定分區上的熱點：  

![顯示具有 RowKey 的員工實體的圖形，將年份、月份、日、小時和事件 ID 複用][27]

使用此示例請注意 和`PartitionKey``RowKey`如何都是複合鍵。 使用`PartitionKey`部門和員工 ID 跨多個分區分發日誌記錄。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何實作此模式時，請考慮下列幾點：  

* 可避免在插入時產生熱點資料分割的替代索引鍵結構，是否可有效支援用戶端應用程式發出的查詢？  
* 預期事務量是否意味著您可能達到單個分區的可伸縮性目標，並且被表存儲限制？  

#### <a name="when-to-use-this-pattern"></a>使用此模式的時機
當您的事務量在您訪問熱分區時，可能會導致按表存儲限制速率時，請避免預提交/追加反模式。  

#### <a name="related-patterns-and-guidance"></a>相關的模式和指導方針
實作此模式時，下列模式和指導方針可能也相關：  

* [複合索引鍵模式](#compound-key-pattern)  
* [記錄檔結尾模式](#log-tail-pattern)  
* [修改實體](#modify-entities)  

### <a name="log-data-anti-pattern"></a>記錄資料反向模式
通常，應使用 Blob 存儲而不是表存儲來存儲日誌資料。  

#### <a name="context-and-problem"></a>內容和問題
日誌資料的一個常見用例是檢索特定日期/時間範圍的日誌條目選擇。 例如，您希望查找應用程式在特定日期 15：04 和 15：06 之間記錄的所有錯誤和關鍵消息。 您不希望使用日誌消息的日期和時間來確定將日誌實體保存到的分區。 這將導致熱分區，因為在任何特定時間，所有日誌實體將共用相同的`PartitionKey`值（請參閱[Prepend/附加反模式](#prepend-append-anti-pattern)）。 例如，日誌消息的以下實體架構會導致熱分區，因為應用程式將當前日期和時間的所有日誌消息寫入分區：  

![日誌消息實體的圖形][28]

在此示例中，`RowKey`包括日誌消息的日期和時間，以確保日誌消息按日期/時間順序排序。 還包括`RowKey`消息 ID，以防多個日誌消息共用相同的日期和時間。  

另一種方法是使用`PartitionKey`確保應用程式跨一系列分區寫入消息的 。 例如，如果日誌消息的來源提供了一種跨多個分區分發消息的方法，則可以使用以下實體架構：  

![日誌消息實體的圖形][29]

但是，此架構的問題是，要檢索特定時間跨度的所有日誌消息，必須搜索表中的每個分區。

#### <a name="solution"></a>解決方法
上一節強調了嘗試使用表存儲存儲日誌條目的問題，並提出了兩種不令人滿意的設計。 一個解決方案導致熱分區，存在編寫日誌消息的性能不佳的風險。 另一個解決方案導致查詢性能不佳，因為需要掃描表中的每個分區以檢索特定時間跨度的日誌消息。 Blob 存儲為此類方案提供了更好的解決方案，這就是 Azure 存儲分析存儲其收集的日誌資料的方式。  

本節概述了存儲分析如何在 Blob 存儲中存儲日誌資料，作為此存儲通常按範圍查詢的資料的方法的一個示例。  

存儲分析以分隔格式存儲多個 Blob 中的日誌消息。 分隔格式可方便用戶端應用程式剖析記錄訊息中的資料。  

存儲分析使用 Blob 的命名約定，使您能夠查找包含要搜索的日誌消息的 Blob（或 blob）。 例如，名為"queue/2014/07/31/1800/000001.log"的 blob 包含與 2014 年 7 月 31 日 18：00 開始的小時的佇列服務相關的日誌消息。 "000001"表示這是這段期間的第一個記錄檔。 存儲分析還會記錄存儲在檔中的第一個和最後一個日誌消息的時間戳記，作為 Blob 中繼資料的一部分。 Blob 存儲的 API 使您能夠根據名稱首碼在容器中查找 Blob。 要查找從 18：00 開始的每小時包含佇列日誌資料的所有 blob，可以使用首碼"佇列/2014/07/31/1800"。  

存儲分析會在內部緩衝日誌消息，然後定期更新相應的 Blob 或使用最新一批日誌條目創建新的 Blob。 這減少了它必須對 Blob 存儲執行的寫入數。  

如果要在您自己的應用程式中實現類似的解決方案，請考慮如何管理可靠性、成本和可擴充性之間的權衡。 換句話說，與應用程式中的緩衝更新和分批寫入 Blob 存儲相比，評估將每個日誌條目寫入 Blob 存儲的效果。  

#### <a name="issues-and-considerations"></a>問題和考量
當您決定如何儲存記錄資料時，請考慮下列幾點：  

* 如果創建避免潛在熱分區的表設計，您可能會發現無法有效地訪問日誌資料。  
* 在處理記錄資料時，用戶端常需要載入多筆記錄。  
* 儘管日誌資料通常是結構化的，但 Blob 存儲可能是一個更好的解決方案。  

### <a name="implementation-considerations"></a>實作考量
本節討論您在實作前面幾節說明的模式時應謹記在心的注意事項。 本節中的範例大多是以 C# 撰寫，並使用儲存體用戶端程式庫 (撰寫本文時為 4.3.0 版)。  

### <a name="retrieve-entities"></a>檢索實體
如 [查詢的設計](#design-for-querying)一節中所述，最有效率的查詢是點查詢。 但是，在某些情況下，您可能需要檢索多個實體。 本節介紹使用存儲用戶端庫檢索實體的一些常見方法。  

#### <a name="run-a-point-query-by-using-the-storage-client-library"></a>使用存儲用戶端庫運行點查詢
運行點查詢的最簡單方法是使用**檢索**表操作。 如以下 C# 程式碼片段所示，此操作將檢索具有`PartitionKey`值"Sales"和值"212"`RowKey`的實體：  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

請注意此示例如何期望它檢索的實體的類型`EmployeeEntity`。  

#### <a name="retrieve-multiple-entities-by-using-linq"></a>使用 LINQ 檢索多個實體
通過將 LINQ 與存儲用戶端庫一起使用，以及使用**where**子句指定查詢，可以檢索多個實體。 為了避免資料表掃描，應始終在 where 子句中`PartitionKey`包括該值，如果可能，則該`RowKey`值將避免表和分區掃描。 表存儲支援一組有限的比較運算子（大於、大於或等於、小於、小於或等於、等於和等於）以在 where 子句中使用。 以下 C# 程式碼片段查找其姓氏以銷售部門中的"B"（假設`RowKey`存儲姓氏）開頭的所有員工（假設`PartitionKey`商店為部門名稱）：  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

請注意查詢如何指定 和`RowKey`a`PartitionKey`以確保更好的性能。  

以下代碼示例使用流暢的 API 顯示等效功能（有關流暢 API 的詳細資訊，請參閱[設計流暢 API 的最佳做法](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)）：  

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
> 該示例嵌套多`CombineFilters`種方法以包括三個篩選器條件。  
> 
> 

#### <a name="retrieve-large-numbers-of-entities-from-a-query"></a>從查詢中檢索大量實體
最佳查詢返回基於`PartitionKey`值和`RowKey`值的單個實體。 但是，在某些情況下，您可能需要從同一分區，甚至從多個分區返回許多實體。 在此類情況下，您務必要完整測試應用程式的效能。  

針對表存儲的查詢一次最多可以返回 1，000 個實體，並且最多運行 5 秒。 表存儲返回延續權杖，使用戶端應用程式能夠請求下一組實體（如果以下任一實體為 true）：

- 結果集包含 1，000 多個實體。
- 查詢在五秒內未完成。
- 查詢跨越分區邊界。 

有關延續權杖的工作原理的詳細資訊，請參閱[查詢超時和暫停](https://msdn.microsoft.com/library/azure/dd135718.aspx)。  

如果使用存儲用戶端庫，它可以自動處理從表存儲返回實體時為您處理延續權杖。 例如，如果表存儲在回應中返回延續權杖，以下 C# 代碼示例將自動處理延續權杖：  

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

藉由明確使用接續權杖，您將可控制應用程式何時會擷取下一個資料區段。 例如，如果用戶端應用程式允許使用者在表中存儲的實體進行頁面流覽，則使用者可能會決定不翻頁查詢檢索的所有實體。 僅當使用者完成通過當前段中的所有實體進行分頁時，應用程式才會使用延續權杖檢索下一個段。 這種方法有幾項優點：  

* 您可以限制從表存儲檢索的資料量，以及通過網路移動的資料量。  
* 您可以在 .NET 中執行非同步 I/O。  
* 您可以將延續權杖序列化為持久存儲，以便在應用程式崩潰時繼續。  

> [!NOTE]
> 延續權杖通常返回包含 1，000 個實體的段，儘管它可以包含較少的實體。 如果通過使用**Take**返回與查找條件匹配的前 n 個實體來限制查詢返回的條目數，則也會出現這種情況。 表存儲可能會返回包含少於 n 個實體的段，以及一個延續權杖，以便您檢索剩餘的實體。  
> 
> 

下列 C# 程式碼說明如何修改區段內傳回的實體數目：  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>伺服器端預測
單一實體最多可以有 255 個屬性，且大小上限為 1 MB。 查詢表並檢索實體時，可能不需要所有屬性，並且可以避免不必要地傳輸資料（以説明降低延遲和成本）。 您可以使用伺服器端預測，僅傳送您需要的屬性。 下面的示例僅從查詢選擇的實體`Email`中檢索屬性（`PartitionKey`以及`RowKey` `Timestamp`、 `ETag`、 和 ）。  

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

請注意該`RowKey`值的可用方式，即使它未包含在要檢索的屬性清單中也是如此。  

### <a name="modify-entities"></a>修改實體
存儲用戶端庫使您能夠通過插入、刪除和更新實體來修改存儲在表存儲中的實體。 您可以使用 EgT 將多個插入批次處理、更新和刪除操作一起批次處理，以減少所需的往返次數並提高解決方案的性能。  

存儲用戶端庫運行 EGT 時引發的異常通常包括導致批次處理失敗的實體的索引。 當您偵錯使用 EGT 的程式碼時，這會很有幫助。  

您也應該考量您的設計會如何影響用戶端應用程式處理並行存取和更新作業的方式。  

#### <a name="managing-concurrency"></a>管理並行存取
預設情況下，表存儲在單個實體級別實現用於插入、合併和刪除操作的樂觀併發檢查，儘管用戶端可以強制表存儲繞過這些檢查。 有關詳細資訊，請參閱在[Microsoft Azure 存儲中管理併發](../storage/common/storage-concurrency.md)。  

#### <a name="merge-or-replace"></a>合併或取代
`TableOperation`類`Replace`的方法始終替換表存儲中的完整實體。 如果在存儲的實體中存在該屬性時未在請求中包含屬性，則請求將從存儲的實體中刪除該屬性。 除非您想要明確地從已儲存的實體中移除屬性，否則即必須在要求中包含每個屬性。  

可以使用`Merge``TableOperation`類的方法來減少在更新實體時發送到表存儲的資料量。 該方法`Merge`將存儲實體中的任何屬性替換為請求中包含的實體的屬性值。 此方法保留請求中未包括的存儲實體中的任何屬性的完整。 如果具有大型實體，並且只需要更新要求中的少量屬性，則此功能非常有用。  

> [!NOTE]
> 如果`*Replace`實體`Merge`不存在，和 方法將失敗。 作為替代方法，如果新實體不存在，`InsertOrReplace`則可以使用`InsertOrMerge`和 方法創建新實體。  
> 
> 

### <a name="work-with-heterogeneous-entity-types"></a>使用異質性實體類型
表存儲是*無架構的*表存儲。 這意味著單個表可以存儲多種類型的實體，從而在設計中提供極大的靈活性。 下列範例說明用以儲存員工和部門實體的資料表：  

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

每個實體必須仍具有`PartitionKey``RowKey`和`Timestamp`值，但可以具有任何屬性集。 此外，除非您選擇將該資訊存儲在某處，否則沒有任何指示實體的類型。 有兩個選項可用來識別實體類型：  

* 將實體類型預置到`RowKey`（或 可能`PartitionKey`為 。 例如，`EMPLOYEE_000123`或`DEPARTMENT_SALES`作為`RowKey`值。  
* 使用單獨的屬性記錄實體類型，如下表所示。  

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

如果兩個不同類型的實體可能具有相同的鍵值，則`RowKey`第一個選項（將實體類型預置為 ）非常有用。 它也會將屬於相同類型的實體分組在資料分割中。  

本節中討論的技術與有關[繼承關係](#inheritance-relationships)的討論特別相關。  

> [!NOTE]
> 請考慮在實體類型值中包括版本號，以使用戶端應用程式能夠發展 POCO 物件並使用不同的版本。  
> 
> 

本節的其餘部分說明儲存體用戶端程式庫中一些有助於在相同資料表中使用多個實體類型的功能。  

#### <a name="retrieve-heterogeneous-entity-types"></a>檢索異構實體類型
如果使用存儲用戶端庫，則有三個選項可用於處理多個實體類型。  

如果您知道使用特定`RowKey`值和`PartitionKey`值存儲的實體的類型，則可以在檢索實體時指定實體類型。 在前面兩個檢索類型`EmployeeEntity`實體的示例中，您看到了這一點：[使用存儲用戶端庫運行點查詢](#run-a-point-query-by-using-the-storage-client-library)，並使用[LINQ 檢索多個實體](#retrieve-multiple-entities-by-using-linq)。  

第二個選項是使用`DynamicTableEntity`類型（屬性包），而不是具體的 POCO 實體類型。 此選項還可以提高性能，因為無需序列化實體並將其序列化為 .NET 類型。 以下 C# 代碼可能會從表中檢索多個不同類型的實體，但將所有實體作為`DynamicTableEntity`實例返回。 然後，它使用`EntityType`屬性來確定每個實體的類型：  

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

若要檢索其他屬性，必須在`TryGetValue``Properties``DynamicTableEntity`類的屬性上使用 方法。  

第三個選項是使用`DynamicTableEntity`類型和實例進行`EntityResolver`組合。 這可讓您解析為相同查詢中的多個 POCO 類型。 在此示例中，`EntityResolver`委託使用 屬性`EntityType`來區分查詢返回的兩種類型的實體。 該方法`Resolve`使用`resolver`委託將實例解析`DynamicTableEntity`為`TableEntity`實例。  

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

#### <a name="modify-heterogeneous-entity-types"></a>修改異構實體類型
您不需要知道要刪除它的實體的類型，並且在插入實體時始終知道實體的類型。 但是，您可以使用 類型`DynamicTableEntity`來更新實體，而無需知道其類型，並且不使用 POCO 實體類。 以下代碼示例檢索單個實體，並在更新`EmployeeCount`該實體之前檢查該屬性是否存在。  

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

### <a name="control-access-with-shared-access-signatures"></a>使用共用訪問簽名控制訪問
您可以使用共用訪問簽名 （SAS） 權杖使用戶端應用程式能夠直接修改（和查詢）表實體，而無需直接使用表存儲進行身份驗證。 一般而言，在您的應用程式中使用 SAS 有三大優點：  

* 您無需將存儲帳戶金鑰分發到不安全的平臺（如行動裝置），以便該設備訪問和修改表存儲中的實體。  
* 您可以卸載 Web 角色和輔助角色在管理實體時執行的一些工作。 您可以將負載卸載到用戶端設備（如最終使用者電腦和行動裝置）。  
* 您可以將受約束且有時間限制的許可權集分配給用戶端（例如允許對特定資源的唯讀訪問）。  

有關將 SAS 權杖與表存儲一起使用的詳細資訊，請參閱[使用共用訪問簽名 （SAS）。](../storage/common/storage-dotnet-shared-access-signature-part-1.md)  

但是，您仍必須生成將用戶端應用程式授予表存儲中的實體的 SAS 權杖。 在具有對存儲帳戶金鑰的安全存取權限的環境中執行此操作。 一般而言，您可以使用 Web 或背景工作角色來產生 SAS 權杖，並將其傳送至需要存取您的實體的用戶端應用程式。 由於產生 SAS 權杖並將其傳遞至用戶端仍會產生額外負荷，因此您應考量怎樣最能降低此負荷，尤其是在大量的案例中。  

可以生成 SAS 權杖，該權杖授予對表中實體子集的存取權限。 預設情況下，為整個表創建 SAS 權杖。 但是，也可以指定 SAS 權杖授予對一系列`PartitionKey`值或一個`PartitionKey`值的範圍和`RowKey`值的存取權限。 您可以選擇為系統的單個使用者生成 SAS 權杖，以便每個使用者的 SAS 權杖只允許他們訪問表存儲中的自己的實體。  

### <a name="asynchronous-and-parallel-operations"></a>非同步和平行作業
假設您要跨多個資料分割分散您的要求，您可以使用非同步或平行查詢來改善輸送量和用戶端的回應性。
例如，您可以用兩個或更多背景工作角色執行個體，以平行方式存取您的資料表。 您可以讓負責特定分區集的各個輔助角色，或者只是具有多個輔助角色實例，每個角色實例都可以訪問表中的所有分區。  

在用戶端實例中，可以通過非同步運行存儲操作來提高輸送量。 儲存體用戶端程式庫可讓您輕鬆撰寫非同步查詢並修改。 例如，您可能從檢索分區中的所有實體的同步方法開始，如以下 C# 代碼所示：  

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

您可以輕鬆地修改此代碼，以便查詢以非同步方式運行，如下所示：  

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

* 方法簽名現在包括`async`修飾符，並返回實例。 `Task`  
* 該方法現在調用`ExecuteSegmented``ExecuteSegmentedAsync`該方法，而不是調用 方法以檢索結果。 該方法使用`await`修改器非同步檢索結果。  

用戶端應用程式可以多次調用此方法，`department`對參數使用不同的值。 每個查詢在單獨的執行緒上運行。  

類中沒有`Execute`方法的非同步版本，因為`IEnumerable`介面不支援非同步枚舉。 `TableQuery`  

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

您可以輕鬆修改此代碼，以便更新以非同步方式運行，如下所示：  

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

* 方法簽名現在包括`async`修飾符，並返回實例。 `Task`  
* 該方法現在調用`Execute``ExecuteAsync`該方法，而不是調用 方法以更新實體。 該方法使用`await`修改器非同步檢索結果。  

用戶端應用程式可以調用多個非同步方法，如下所示，並且每個方法調用都運行在單獨的執行緒上。  


[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

