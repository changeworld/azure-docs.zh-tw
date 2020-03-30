---
title: 使用 Azure 宇宙資料庫容量規劃器估計成本
description: Azure Cosmos DB 容量規劃器允許您估計工作負荷所需的輸送量 （RU/s） 和成本。 本文介紹如何使用新版本的容量規劃器來估計所需的輸送量和成本。
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707626"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>使用 Azure 宇宙資料庫容量規劃器估計 RU/s

為工作負荷配置 Azure Cosmos 資料庫和容器，其預配輸送量或[請求單位 （RU/s）](request-units.md)對於優化成本和性能至關重要。 本文介紹如何使用 Azure Cosmos DB[容量規劃器](https://cosmos.azure.com/capacitycalculator/)來估算所需的 RU/s 和工作負荷成本。 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>如何使用 Azure Cosmos DB 容量規劃器估計輸送量和成本

容量規劃器可在兩種模式下使用。

|**Mode**  |**描述**  |
|---------|---------|
|基本|提供快速、高級 RU/s 和成本估算。 此模式假定預設的 Azure Cosmos DB 設置用於索引策略、一致性和其他參數。 <br/><br/>在評估要在 Azure Cosmos DB 上運行的潛在工作負荷時，使用基本模式進行快速的高級估計。|
|進階|提供更詳細的 RU/s 和成本估算，並能夠調整其他設置 — 索引策略、一致性級別以及影響成本和輸送量的其他參數。 <br/><br/>在估計新專案的 RU/s 或想要更詳細的估計時，請使用高級模式。 |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>使用基本模式估計預配輸送量和成本
要使用基本模式快速估計工作負荷，請導航到[容量規劃器](https://cosmos.azure.com/capacitycalculator/)。 根據工作負荷輸入以下參數： 

|**輸入**  |**描述**  |
|---------|---------|
|區域數|所有 Azure 區域皆可使用 Azure Cosmos DB。 選擇工作負荷所需的區域數。 您可以將任意數量的區域與 Cosmos 帳戶相關聯。 有關詳細資訊，請參閱 Azure Cosmos DB 中的[全域分發](distribute-data-globally.md)。|
|多重區域寫入|如果啟用[多區域寫入](distribute-data-globally.md#key-benefits-of-global-distribution)，則應用程式可以讀取和寫入任何 Azure 區域。 如果禁用多區域寫入，應用程式可以將資料寫入單個區域。 <br/><br/> 如果希望具有活動-活動工作負載，需要不同區域的低延遲寫入，則啟用多區域寫入。 例如，IOT 工作負載，用於在不同區域以高容量將資料寫入資料庫。 <br/><br/> 多區域寫入保證 99.999% 的讀寫可用性。 與單個寫入區域相比，多區域寫入需要更多的輸送量。 要瞭解更多資訊，請參閱[單行和多寫區域文章的 RUS 是如何不同的](optimize-cost-regions.md)。|
|存儲的資料總數（每個區域）|以 GB 存儲在單個區域中的估計資料總數。|
|項目大小|資料項目的估計大小（例如文檔），範圍從 1 KB 到 2 MB。 |
|每個區域的讀取/秒|每秒預期讀取數。 |
|每個區域的寫入/秒|每秒預期寫入數。 |

填寫所需詳細資訊後，選擇 **"計算**"。 "**成本估算**"選項卡顯示存儲和預配輸送量的總成本。 您可以展開此選項卡中的 **"顯示詳細資訊"** 連結，以獲取有關讀取和寫入請求所需的輸送量的細分。 每次更改任何欄位的值時，選擇 **"計算"** 以重新計算估計成本。 

![容量規劃器基本模式](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>使用高級模式估計預配輸送量和成本

高級模式允許您提供更多影響 RU/s 估計的設置。 要使用此選項，請導航到[容量規劃器](https://cosmos.azure.com/capacitycalculator/)並使用用於 Azure 的帳戶登錄到該工具。 登錄選項在右側角可用。 

登錄後，您可以看到與基本模式下的欄位相比的其他欄位。 根據工作負荷輸入其他參數。 

|**輸入**  |**描述**  |
|---------|---------|
|API|Azure Cosmos DB 是一種多模型和多 API 服務。 對於新的工作負載，請選擇 SQL（核心）API。 |
|區域數|所有 Azure 區域皆可使用 Azure Cosmos DB。 選擇工作負荷所需的區域數。 您可以將任意數量的區域與 Cosmos 帳戶相關聯。 有關詳細資訊，請參閱 Azure Cosmos DB 中的[全域分發](distribute-data-globally.md)。|
|多重區域寫入|如果啟用[多區域寫入](distribute-data-globally.md#key-benefits-of-global-distribution)，則應用程式可以讀取和寫入任何 Azure 區域。 如果禁用多區域寫入，應用程式可以將資料寫入單個區域。 <br/><br/> 如果希望具有活動-活動工作負載，需要不同區域的低延遲寫入，則啟用多區域寫入。 例如，IOT 工作負載，用於在不同區域以高容量將資料寫入資料庫。 <br/><br/> 多區域寫入保證 99.999% 的讀寫可用性。 與單個寫入區域相比，多區域寫入需要更多的輸送量。 要瞭解更多資訊，請參閱[單行和多寫區域文章的 RUS 是如何不同的](optimize-cost-regions.md)。|
|預設一致性|Azure Cosmos DB 支援 5 個一致性級別，允許開發人員平衡一致性、可用性和延遲權衡之間的權衡。 要瞭解更多資訊，請參閱[一致性級別](consistency-levels.md)一文。 <br/><br/> 預設情況下，Azure Cosmos DB 使用會話一致性，這保證了在會話中讀取自己的寫入功能。 <br/><br/> 與會話、一致首碼和最終一致性相比，選擇強或有限過時需要讀取所需的 RU/s 的兩倍。 不支援與多區域寫入的強一致性，並且將自動預設為具有強一致性的單區域寫入。 |
|編製索引原則|預設情況下，Azure Cosmos DB[為所有項中的所有屬性編制索引](index-policy.md)，以便進行靈活有效的查詢（映射到**自動**索引策略）。 <br/><br/> 如果選擇**關閉**，則不會對任何屬性進行索引。 這將導致寫入的 RU 費用最低。 如果希望只執行[點讀取](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)（鍵值查找）和/或寫入，並且沒有查詢，請選擇 **"關閉**策略"。 <br/><br/> 自訂索引策略允許您從索引中包括或排除特定屬性，以降低寫入輸送量和存儲。 要瞭解更多資訊，請參閱[索引策略](index-overview.md)和[索引策略](how-to-manage-indexing-policy.md#indexing-policy-examples)示例文章。|
|存儲的資料總數（每個區域）|以 GB 存儲在單個區域中的估計資料總數。|
|工作負載模式|如果工作負荷量保持不變，請選擇 **"穩定**"。 <br/><br/> 如果工作負荷量隨時間而變化，請選擇 **"變數**"。  例如，在特定一天或一個月內。 <br/><br/> 如果選擇可變工作負載選項，可以使用以下設置：<ul><li>峰值時間百分比：工作負荷需要峰值（最高）輸送量的月份時間百分比。 <br/><br/> 例如，如果您的工作負載在上午 9 點 - 下午 6 點工作時間內活動頻繁，則尖峰時間的百分比為：尖峰時間為 45 小時/730 小時/月 = +6%。<br/><br/></li><li>峰值時每個區域的讀取/秒 - 峰值時預計每秒讀取數。</li><li>峰值時每個區域的寫入/秒 = 峰值時預計每秒寫入數。</li><li>每個區域的讀取/秒關閉峰值 = 在非峰值期間預計每秒讀取數。</li><li>每個區域的寫入/秒關閉峰值 = 在非峰值期間預期每秒寫入數。</li></ul>使用高峰和非高峰間隔，您可以通過[以程式設計方式相應地向上和向下擴展預配輸送量](set-throughput.md#update-throughput-on-a-database-or-a-container)來優化成本。|
|項目大小|資料項目的大小（例如文檔），範圍從 1 KB 到 2 MB。 <br/><br/>您還可以**上傳示例 （JSON）** 文檔，以便進行更準確的估算。<br/><br/>如果工作負荷在同一容器中有多個類型的專案（具有不同的 JSON 內容），則可以上載多個 JSON 文檔並獲取估計值。 使用 **"添加新項"** 按鈕添加多個示例 JSON 文檔。|

您還可以使用 **"保存估計"** 按鈕下載包含當前估計值的 CSV 檔。 

![容量規劃器高級模式](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Azure Cosmos DB 容量規劃器中顯示的價格是根據輸送量和存儲的公共定價率估算的。 所有價格均以美元顯示。 請參閱[Azure Cosmos DB 定價頁](https://azure.microsoft.com/pricing/details/cosmos-db/)，查看按區域查看所有費率。  

## <a name="estimating-throughput-for-queries"></a>估計查詢的輸送量

Azure Cosmos 容量計算機假定點讀取（單個項（例如文檔、按 ID 和分區鍵值的讀取）和工作負載的寫入。 要估計查詢所需的輸送量，請對 Cosmos 容器中的代表性資料集執行查詢[，並獲取 RU 費用](find-request-unit-charge.md)。 將 RU 費用乘以您預計每秒運行的查詢數，以獲得所需的總 RU/秒。 

例如，如果工作負荷需要查詢，``SELECT * FROM c WHERE c.id = 'Alice'``即每秒運行 100 次，並且查詢的 RU 費用為 10 個 RU，則需要 100 個查詢 / 秒 = 10 RU / 查詢 = 1000 RU/s，以總共為這些請求提供服務。 將這些 RU/s 添加到工作負載中發生的任何讀取或寫入所需的 RU/s。

## <a name="next-steps"></a>後續步驟

* 瞭解有關[Azure 宇宙 DB 定價模型](how-pricing-works.md)的更多詳細資訊。
* 創建新的[Cosmos 帳戶、資料庫和容器](create-cosmosdb-resources-portal.md)。
* 瞭解如何[優化預配的輸送量成本](optimize-cost-throughput.md)。
* 瞭解如何[使用預留容量優化成本](cosmos-db-reserved-capacity.md)。

