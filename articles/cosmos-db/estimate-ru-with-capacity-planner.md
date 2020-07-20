---
title: 使用 Azure Cosmos DB 容量規劃來預估成本
description: Azure Cosmos DB 容量規劃工具可讓您預估所需的輸送量（RU/秒）和工作負載的成本。 本文說明如何使用新版本的容量規劃工具來估計所需的輸送量和成本。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 6a30535ac3aaa90dc3553f6901a83ab300546fb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85261846"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>使用 Azure Cosmos DB 容量規劃來估計 RU/秒

將您的 Azure Cosmos 資料庫和容器設定為適當數量的布建輸送量，或針對您的工作負載設定[要求單位（RU/秒）](request-units.md)，對於將成本和效能優化是不可或缺的。 本文說明如何使用 Azure Cosmos DB[容量規劃](https://cosmos.azure.com/capacitycalculator/)工具來估計所需的 RU/秒，以及工作負載的成本。 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>如何使用 Azure Cosmos DB 容量規劃來預估輸送量和成本

容量規劃可用於兩種模式。

|**Mode**  |**描述**  |
|---------|---------|
|基本|提供快速、高階的 RU/秒和成本預估。 此模式會假設索引編制原則、一致性和其他參數的預設 Azure Cosmos DB 設定。 <br/><br/>當您正在評估可能要在 Azure Cosmos DB 上執行的工作負載時，請使用基本模式來進行快速的高階評估。|
|進階|提供更詳細的 RU/秒和成本預估，並能夠微調其他設定—編制索引原則、一致性層級，以及影響成本和輸送量的其他參數。 <br/><br/>當您針對新專案估計 RU/秒，或想要更詳細的評估時，請使用 [advanced] 模式。 |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>使用基本模式估計布建的輸送量和成本
若要使用基本模式取得工作負載的快速估計，請流覽至[容量規劃](https://cosmos.azure.com/capacitycalculator/)工具。 根據您的工作負載，輸入下列參數： 

|**輸入**  |**描述**  |
|---------|---------|
|區域數目|所有 Azure 區域皆可使用 Azure Cosmos DB。 選取您的工作負載所需的區域數目。 您可以將任意數目的區域與您的 Cosmos 帳戶建立關聯。 如需詳細資訊，請參閱 Azure Cosmos DB 中的[全域散發](distribute-data-globally.md)。|
|多重區域寫入|如果您啟用[多區域寫入](distribute-data-globally.md#key-benefits-of-global-distribution)，您的應用程式可以讀取和寫入任何 Azure 區域。 如果您停用多區域寫入，您的應用程式可以將資料寫入至單一區域。 <br/><br/> 如果預期在不同區域中需要低延遲寫入的主動-主動工作負載，請啟用多重區域寫入。 例如，IOT 工作負載會將資料寫入位於不同區域中大量磁片區的資料庫。 <br/><br/> 多重區域寫入可保證99.999% 的讀取和寫入可用性。 相較于單一寫入區域，多重區域寫入會需要更多的輸送量。 若要深入瞭解，請參閱[單一和多個寫入區域的 ru 有何不同一](optimize-cost-regions.md)文。|
|儲存的資料總計（每個地區）|單一區域中以 GB 儲存的預估資料總數。|
|項目大小|資料項目（例如檔）的估計大小，範圍從 1 KB 到 2 MB。 |
|每個區域的讀取/秒|每秒預期的讀取次數。 |
|每個區域的寫入數/秒|每秒預期的寫入數目。 |

填入所需的詳細資料之後，選取 [**計算**]。 [**成本預估**] 索引標籤會顯示儲存體和布建輸送量的總成本。 您可以展開此索引標籤中的 [**顯示詳細資料**] 連結，以取得讀取和寫入要求所需的輸送量明細。 每次您變更任何欄位的值時，請選取 [**計算**] 以重新計算預估成本。 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="容量規劃基本模式":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>使用 advanced 模式估計布建的輸送量和成本

[Advanced] 模式可讓您提供更多會影響 RU/秒估計值的設定。 若要使用此選項，請流覽至 [[容量規劃](https://cosmos.azure.com/capacitycalculator/)]，並使用您用於 Azure 的帳戶來登入工具。 [登入] 選項位於右下角。 

登入之後，您可以看到與 [基本] 模式中的欄位相較之下的其他欄位。 根據您的工作負載輸入其他參數。 

|**輸入**  |**描述**  |
|---------|---------|
|API|Azure Cosmos DB 是多模型和多 API 服務。 針對新的工作負載，請選取 [SQL （核心） API]。 |
|區域數目|所有 Azure 區域皆可使用 Azure Cosmos DB。 選取您的工作負載所需的區域數目。 您可以將任意數目的區域與您的 Cosmos 帳戶建立關聯。 如需詳細資訊，請參閱 Azure Cosmos DB 中的[全域散發](distribute-data-globally.md)。|
|多重區域寫入|如果您啟用[多區域寫入](distribute-data-globally.md#key-benefits-of-global-distribution)，您的應用程式可以讀取和寫入任何 Azure 區域。 如果您停用多區域寫入，您的應用程式可以將資料寫入至單一區域。 <br/><br/> 如果預期在不同區域中需要低延遲寫入的主動-主動工作負載，請啟用多重區域寫入。 例如，IOT 工作負載會將資料寫入位於不同區域中大量磁片區的資料庫。 <br/><br/> 多重區域寫入可保證99.999% 的讀取和寫入可用性。 相較于單一寫入區域，多重區域寫入會需要更多的輸送量。 若要深入瞭解，請參閱[單一和多個寫入區域的 ru 有何不同一](optimize-cost-regions.md)文。|
|預設一致性|Azure Cosmos DB 支援5個一致性層級，可讓開發人員在一致性、可用性和延遲取捨之間平衡取捨。 若要深入瞭解，請參閱[一致性層級](consistency-levels.md)一文。 <br/><br/> 根據預設，Azure Cosmos DB 會使用會話一致性，以確保能夠在會話中讀取您自己的寫入。 <br/><br/> 相較于會話、一致前置詞和最終一致性，選擇 [強式] 或 [限定過期] 將需要兩個必要的 RU/秒來進行讀取。 不支援多重區域寫入的強式一致性，而且會自動預設為具有強式一致性的單一區域寫入。 |
|編製索引原則|根據預設，Azure Cosmos DB 會為所有專案中的[所有屬性編制索引](index-policy.md)，以提供彈性且有效率的查詢（對應至**自動**索引編制原則）。 <br/><br/> 如果您選擇 [**關閉**]，則不會為任何屬性編制索引。 這會導致寫入的最低 RU 費用。 如果您預期只會執行[點讀取](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet)（索引鍵值查閱）和/或寫入，而且沒有查詢，請選取 [**關閉**原則]。 <br/><br/> 自訂索引編制原則可讓您在索引中包含或排除特定屬性，以提供較低的寫入輸送量和儲存體。 若要深入瞭解，請參閱[編制索引原則](index-overview.md)和[範例索引編制原則](how-to-manage-indexing-policy.md#indexing-policy-examples)文章。|
|儲存的資料總計（每個地區）|單一區域中以 GB 儲存的預估資料總數。|
|工作負載模式|如果您的工作負載磁片區是固定的，請選取 [**穩定**]。 <br/><br/> 如果您的工作負載磁片區隨著時間變更，請選取 [**變數**]。  例如，在特定一天或一個月。 <br/><br/> 如果您選擇 [變數工作負載] 選項，可以使用下列設定：<ul><li>尖峰時間的百分比：您的工作負載需要尖峰（最高）輸送量的一個月中的時間百分比。 <br/><br/> 例如，如果您的工作負載在上午9點到下午6點的上班時間內有高活動，則尖峰時間的百分比為：45小時，尖峰/730 小時/月 = ~ 6%。<br/><br/></li><li>尖峰時每個區域的讀取/秒（尖峰時預期為每秒讀取次數）。</li><li>尖峰時每個區域的寫入/秒–尖峰時每秒預期的寫入數目。</li><li>每個區域的讀取/秒關閉尖峰–在離峰期間每秒預期的讀取次數。</li><li>尖峰的每個區域的寫入/秒數-在離峰期間每秒預期的寫入次數。</li></ul>透過尖峰和離峰間隔，您可以透過程式設計方式相應增加或減少布[建的輸送量，以](set-throughput.md#update-throughput-on-a-database-or-a-container)將成本優化。|
|項目大小|資料項目（例如檔）的大小，範圍從 1 KB 到 2 MB。 <br/><br/>您也可以**上傳範例（JSON）** 檔，以取得更精確的估計。<br/><br/>如果您的工作負載在相同容器中有多個類型的專案（具有不同的 JSON 內容），您可以上傳多個 JSON 檔並取得預估。 使用 [**加入新專案**] 按鈕來新增多個範例 JSON 檔。|

您也可以使用 [**儲存估價**] 按鈕來下載包含目前估計值的 CSV 檔案。 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="容量規劃工具先進模式":::

Azure Cosmos DB 容量規劃工具中所顯示的價格是根據輸送量和儲存體的公開價格費率來預估。 所有價格均以美元顯示。 請參閱[Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)，以查看依區域的所有費率。  

## <a name="estimating-throughput-for-queries"></a>估計查詢的輸送量

Azure Cosmos 容量計算機會假設點讀取（依識別碼和資料分割索引鍵值的單一專案讀取，例如檔）和寫入工作負載。 若要估計查詢所需的輸送量，請在 Cosmos 容器中的代表性資料集上執行查詢，並[取得 RU 費用](find-request-unit-charge.md)。 將 RU 費用乘以您預期每秒執行的查詢數目，以取得所需的 RU/秒總數。 

例如，如果您的工作負載需要查詢（ ``SELECT * FROM c WHERE c.id = 'Alice'`` 每秒執行100次），而查詢的 RU 費用是 10 ru，則您將需要 100 query/sec * 10 RU/查詢 = 1000 RU/秒，才能處理這些要求。 將這些 RU/秒新增至工作負載中發生的任何讀取或寫入所需的 RU/秒。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure Cosmos DB 的定價模式](how-pricing-works.md)。
* 建立新的[Cosmos 帳戶、資料庫和容器](create-cosmosdb-resources-portal.md)。
* 瞭解如何將布[建的輸送量成本優化](optimize-cost-throughput.md)。
* 瞭解如何[使用保留容量將成本優化](cosmos-db-reserved-capacity.md)。

