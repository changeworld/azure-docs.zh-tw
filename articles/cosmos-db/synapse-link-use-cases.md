---
title: 適用於 Azure Cosmos DB 的 Azure Synapse Analytics 即時分析使用案例
description: 瞭解如何在供應鏈分析、預測、報告、即時個人化、IOT 預測性維護中使用適用於 Azure Cosmos DB 的 Azure Synapse Analytics。
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.openlocfilehash: 9855a53ebdf7501907b3e1e63d59823d3a0b0209
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96463116"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>適用於 Azure Cosmos DB 的 Synapse Link：近即時分析的使用案例
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

適用於 Azure Cosmos DB 的 [Azure Synapse Analytics](synapse-link.md) 是雲端原生的混合式交易和分析處理 (HTAP) 功能，可讓您對操作資料執行近即時分析。 Synapse Link 會在 Azure Cosmos DB 與 Azure Synapse Analytics 之間建立緊密順暢的整合。

您可能想要瞭解哪些產業使用案例可以利用此雲端原生 HTAP 功能，對操作資料進行近即時分析。 以下是適用於 Azure Cosmos DB 的 Azure Synapse Link 的三個常見使用案例：

* 供應鏈分析、預測及報告
* 即時個人化
* 預測性維護，IOT 中的異常偵測案例

> [!NOTE]
> 適用於 Azure Cosmos DB 的 Azure Synapse Link 的目標案例是想要執行近即時分析的企業小組。 針對在 Azure Cosmos DB 上建立的交易應用程式所產生的操作資料執行這些分析時，不會對操作資料進行擷取、轉換和下載 (ETL) 。 當有傳統資料倉儲需求時，例如工作負載管理、高並行、跨多個資料來源的彙總持續性，這無法取代個別資料倉儲的需求。

## <a name="supply-chain-analytics-forecasting--reporting"></a>供應鏈分析、預測及報告

研究顯示，在供應鏈作業中內嵌巨量資料分析，能導致訂單週期的達成時間改善，並提升供應鍊效率。

製造商上線至雲端原生技術，以突破舊版「企業資源規劃」(ERP) 和「供應鏈管理」(SCM) 系統的限制。 利用供應鏈產生的每分鐘操作資料增加量 (訂單、運送、交易資料)，製造商需要操作資料庫。 這個操作資料庫應該縮放大小來處理資料量以及分析平台，達到即時內容相關智慧的水準，以立於領先地位。

下列架構顯示在供應鏈分析中利用 Azure Cosmos DB 做為雲端原生操作資料庫和 Synapse Link 的強大功能：

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="供應鏈分析中 Azure Cosmos DB 的 Azure Synapse 連結 " border="false":::

根據先前的架構，您可以使用 適用於 Azure Cosmos DB 的 Synapse Link 來達成下列使用案例：

* **準備和定型預測性管線：** 使用機器學習服務轉譯，針對供應鏈中的操作資料產生深入解析。 如此一來，您可以降低清查、營運成本，並減少客戶的下單至交貨時間。

  Synapse Link 可讓您分析 Azure Cosmos DB 中變更的操作資料，而不需要任何手動 ETL 程序。 其可為您省去額外的成本、延遲和操作複雜度。 Synapse Link 可讓資料工程師和資料科學家建立健全的預測性管線：

  * 藉由在 Azure Synapse Analytics 中利用與 Apache Spark 集區的原生整合，查詢 Azure Cosmos DB 分析存放區中的操作資料。 您可以在互動式筆記本或排程的遠端作業中查詢資料，不需要複雜的資料工程。

  * 使用 Spark ML 演算法和 Azure Synapse Analytics 中的 Azure ML 整合來建立機器學習 (ML) 模型。

  * 在模型推斷後將結果寫回 Azure Cosmos DB，以進行作業近即時評分。

* **作業報告：** 供應鏈小組需要有彈性的自訂報告，將即時正確的操作資料簡明呈現。 需要這些報告，才能迅速看出供應鏈的有效性、獲利率、生產力。 報告可讓資料分析師和其他重要的專案關係人持續重新評估企業，並找出要調整的部分，以降低營運成本。 

  適用於 Azure Cosmos DB 的 Synapse Link 可實現豐富的商業智慧 (BI)/報告案例：

  * 使用與無伺服器 SQL 集區的原生整合和 T-sql 語言的完整表達，查詢 Azure Cosmos DB 分析存放區中的運算元據。

  * 透過適用于熟悉 BI 工具的無伺服器 SQL 集區支援，建立模型併發布自動重新整理 BI 儀表板的 Azure Cosmos DB。 例如，Azure Analysis Services、Power BI Premium 等。

以下是一些將資料批次處理及串流至 Azure Cosmos DB 的資料整合指引：

* **批次資料整合與協調流程：** 隨著供應鏈愈來愈複雜，供應鏈資料平台必須與各種不同的資料來源和格式整合。 Azure Synapse 內建具有與 Azure Data Factory 相同的資料整合引擎和體驗。 此整合可讓資料工程師在沒有個別協調流程引擎的情況下，建立豐富的資料管線：

  * 將資料從支援的至少 85 個資料來源移至 [Azure Cosmos DB (使用 Azure Data Factory)](../data-factory/connector-azure-cosmos-db.md)。

  * 將無程式碼的 ETL 管線寫入 Azure Cosmos DB，包括[使用對應資料流程進行關聯對階層和階層對階層的對應](../data-factory/how-to-sqldb-to-cosmosdb.md)。

* **串流資料整合與處理：** 隨著產業 IoT 的成長 (感應器從「工廠到店面」追蹤資產、連線物流群等)，大量的即時資料以串流方式產生，需要與傳統的緩慢移動資料整合，以產生深入解析。 Azure 串流分析是一項建議服務，可在 Azure 上使用[各種案例](../stream-analytics/streaming-technologies.md)來串流 ETL 和處理。 Azure 串流分析支援[以 Azure Cosmos DB 做為原生資料接收器](../stream-analytics/stream-analytics-documentdb-output.md)。

## <a name="real-time-personalization"></a>即時個人化

現在的零售商會建置安全且可調整的電子商務解決方案，以滿足客戶與業務的需求。 這些電子商務解決方案需要透過自訂的產品和供應項目與客戶互動、快速且安全地處理交易，並專注於履行和客戶服務。 Azure Cosmos DB 以及適用於 Azure Cosmos DB 的最新 Synapse Link 可讓零售商即時為客戶產生個人化建議。 其使用低延遲可調整的一致性設定來取得立即的深入解析，如下列架構所示：

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="即時個人化中適用於 Azure Cosmos DB 的 Azure Synapse Link" border="false":::

適用於 Azure Cosmos DB 的 Synapse Analytics 使用案例：

* **準備和定型預測性管線：** 您可以使用 Synapse Spark 和機器學習模型，針對業務單位或客戶群體的操作資料產生深入解析。 這會轉化成個人化的交貨，以客戶群體、預測性使用者體驗和目標行銷為目標，以符合您的使用者需求。

## <a name="iot-predictive-maintenance"></a>IoT 預測性維護

產業 IoT 創新大幅降低了機械的停機時間，並提升了業界所有領域的整體效率。 其中一項創新就是雲端邊緣機械的預測性維護分析。

以下是 IoT 預測性維護中適用於 Azure Cosmos DB 的 Azure Synapse Link 的雲端原生 HTAP 功能的架構：

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="IoT 預測性維護中適用於 Azure Cosmos DB 的 Azure Synapse Link" border="false" :::

適用於 Azure Cosmos DB 的 Synapse Analytics 使用案例：

* **準備和定型預測性管線：** IoT 裝置感應器的歷史操作資料可用來定型預測性模型，例如異常偵測器。 接著再將這些異常偵測器部署回邊緣，以進行即時監視。 這種良性迴圈可讓預測模型持續重新定型。

* **作業報告：** 隨著數位對應項計畫的成長，公司會從大量的感應器收集大量操作資料，建立每部機器的數位副本。 除了即時應用最近的熱門資料以外，資料 Power BI 還需要瞭解歷史資料的趨勢。

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>範例案例：Azure Cosmos DB 的 HTAP

將近十年來，有上千名客戶使用 Azure Cosmos DB，用於需要彈性延展、全包式全域散發、多重區域寫入複寫以達到低延遲和高可用性的要徑任務應用程式，都是在其交易工作負載中讀取 & 寫入。
 
下列清單概述使用 Azure Cosmos DB 操作資料支援的各種工作負載模式：

* 即時應用程式與服務
* 事件串流處理
* BI 儀表板
* 巨量資料分析
* 機器學習服務

Azure Synapse Link 可讓 Azure Cosmos DB 不只支援交易工作負載，也能對歷史操作資料執行近即時分析工作負載。 不需要 ETL，且保證會與交易工作負載效能隔離。

下圖顯示使用 Azure Cosmos DB 的工作負載模式：:::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="適用於 Azure Cosmos DB 的 Azure Synapse Link 工作負載模式" border="false":::

我們以一家營運橫跨全球 20 個國家/地區的電子商務公司 CompanyXYZ 為範例，說明選擇 Azure Cosmos DB 作為單一即時資料庫，同時滿足庫存管理平台的交易和分析需求的優點。

* CompanyXYZ 的核心業務取決於庫存管理系統，因此可用性和可靠性是核心要件需求。 使用 Azure Cosmos DB 的優點：

  * 藉由與 Azure 基礎結構的深度整合，以及透明的多重區域寫入、全域複寫，Azure Cosmos DB 可針對區域中斷提供領先業界的 [99.999% 高可用性](high-availability.md) 。

* CompanyXYZ 的供應鏈合作夥伴可能位於不同的地理位置，但他們可能必須查看全球的產品庫存單一檢視，以支援其本地營運。 這包括需要能夠即時讀取其他供應鏈合作夥伴所做的更新。 以及能夠進行更新，而不必擔心在高輸送量時與其他合作夥伴衝突。 使用 Azure Cosmos DB 的優點：

  * 具有唯一的多重區域寫入複寫通訊協定和無閂鎖、寫入優化的交易式存放區，Azure Cosmos DB 可保證全球第99個百分位數的索引讀取和寫入小於10毫秒的延遲。

  * 交易存放區中[即時索引](index-policy.md)的批次和串流資料摘要的高輸送量內嵌。

  * 除了強式和最終一致性這兩個極端，Azure Cosmos DB 的交易存放區還多提供三個選項，來達成最接近商務需求的[可用性與效能權衡取捨](./consistency-levels.md)情況。

* CompanyXYZ 的供應鏈合作夥伴有高度變動的流量模式，從每秒數百個到數百萬個要求不等，因此庫存管理平台必須處理流量中非預期的高載。  使用 Azure Cosmos DB 的優點：

  * Azure Cosmos DB 的交易存放區使用水平資料分割，支援儲存和輸送量的彈性擴縮。 在 Autopilot 模式中設定的容器和資料庫可以根據應用程式的需求自動並立即調整佈建的輸送量，而不會影響全域工作負載的可用性、延遲、輸送量或效能。

* CompanyXYZ 需要建立一個安全的分析平台來容納整個系統的歷史庫存資料，以實現跨供應鏈合作夥伴、業務部門、功能的分析和深入解析。 分析平台需要能夠跨越系統、傳統 BI/報告使用案例、進階分析使用案例、預測性智慧型解決方案進行共同作業，處理運用營運的庫存資料。 使用適用於 Azure Cosmos DB 的 Synapse Link 的優點：

  * [Azure Cosmos DB 分析存放區](analytical-store-introduction.md)是完全隔離的資料行存放區，有了該存放區，Synapse Link 可在 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 中對全域散發的操作資料大規模進行無「擷取、轉換和下載」(ETL) 的分析。  商務分析師、資料工程師、資料科學家現在可以使用 Synapse Spark 或 Synapse SQL，以互通的方式執行近即時的商業智慧、分析、機器學習管線，而不會影響其在 Azure Cosmos DB 上的交易工作負載效能。 如需詳細資訊，請參閱 [Azure Cosmos DB 中 Synapse Link 的優點](synapse-link.md)。

## <a name="next-steps"></a>後續步驟

若要深入了解，請參閱下列文件：

* [適用於 Azure Cosmos DB 的 Azure Synapse Link](synapse-link.md) 

* [Azure Cosmos DB 分析存放區](analytical-store-introduction.md)

* [使用適用於 Azure Cosmos DB 的 Azure Synapse Link](configure-synapse-link.md)

* [關於適用於 Azure Cosmos DB 的 Azure Synapse Link 常見問題](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics 中的 Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)

* [Azure Synapse Analytics 中的無伺服器 SQL 集區執行時間支援](../synapse-analytics/sql/on-demand-workspace-overview.md)