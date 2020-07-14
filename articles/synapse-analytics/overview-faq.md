---
title: 常見問題集 - Azure Synapse Analytics (工作區預覽)
description: Azure Synapse Analytics (工作區預覽) 常見問題集
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 43f0dfa5fd413d201eb8c63aa752a6fd8db68f6c
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85807871"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Azure Synapse Analytics (工作區預覽) 常見問題集

您將在本指南中找到 Synapse Analytics 最常遇到的問題。

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>一般

### <a name="q-what-is-azure-synapse-analytics"></a>問：什麼是 Azure Synapse Analytics？=

A：Azure Synapse 是適用於 BI、AI 和持續型智能的整合式資料平台。 其會透過單一平台連線各種分析執行階段 (例如 SQL 和 Spark)，以提供統一的方式來執行下列動作：

- 保護您的分析資源 (包括網路)，以及管理集區、資料和開發成品的單一登入存取。
- 輕鬆地進行監視，並快速地對工作區活動在任何層級上發生的事件進行最佳化、反應和偵錯。
- 跨引擎管理您的中繼資料。 建立 Spark 資料表，這會在您的 Azure Synapse 資料庫中自動提供。
- 透過統一的使用者體驗與資料互動。 Synapse Studio 會將巨量資料開發人員、資料工程師、DBA、資料分析師和資料科學家帶到相同的平台上。

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>問：Azure Synapse Analytics 的主要元件有哪些

A：Azure Synapse 具有下列功能：

- 分析功能會透過 SQL 集區或 SQL 隨選 (預覽) 提供 (無伺服器)。
- 完全支援 Scala、Python、SparkSQL 和 C# 的 Apache Spark 集區 (預覽)
- 提供無程式碼巨量資料轉換體驗的資料流程
- 資料整合與協調流程，可用來整合您的資料，並讓所有程式碼開發能夠運作
- Studio 可透過單一 Web UI 存取所有這些功能

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>問：Azure Synapse Analytics 與 Azure SQL 資料倉儲有何關聯

A：Azure Synapse 分析是 Azure SQL 資料倉儲進入分析平台的演進，其中包含作為資料倉儲解決方案的 SQL 集區。 此平台結合資料探索、擷取、轉換、準備和服務分析層。

## <a name="use-cases"></a>使用案例

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>問：如何在 Azure Synapse 中重新命名已發佈的成品 (資料集、筆記本和 SQL 指令碼等)？

A：若要重新命名已發佈的成品檔案，請先複製檔案，並將新的檔案重新命名為您偏好的名稱。 您必須手動將成品的所有參考更新為新的檔案名稱，並刪除舊的名稱。

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>問：對 Synapse SQL 集區而言，什麼是良好的使用案例

A：SQL 集區是您資料倉儲需求的核心。 對於資料倉儲的[價格/效能](https://azure.microsoft.com/services/sql-data-warehouse/compare/)來說，這是居於領導地位的解決方案。 SQL 集區是領先業界的雲端資料倉儲解決方案，因為您可以：

- 處理大量且混合的各種工作負載，而不會影響效能，因為其可以高度並行，並且區隔工作負載
- 透過進階功能 (從網路安全性到精密存取皆涵蓋在內)，輕鬆保護您的資料
- 受益於各式各樣的生態系統

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>問：對 Synapse 中的 Spark 而言，什麼是良好的使用案例

A：我們的第一個目標是提供絕佳的資料工程經驗，以透過批次或串流的方式轉換湖上的資料。 其可以緊密但簡單地與我們的資料協調流程整合，讓您開發工作可以簡單明瞭地執行。

Spark 的另一個使用案例是讓資料科學家可以執行下列作業：

- 擷取特徵
- 探索資料
- 將模型定型

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>問：對 Synapse 中的 SQL 隨選而言，什麼是良好的使用案例

A：SQL 隨選是針對資料湖資料進行的查詢服務。 其藉由提供熟悉的 T-SQL 語法來就地查詢資料 (不需要將資料複製或載入至特製存放區)，將您所有資料的存取權普及化。

使用案例的範例如下：

- 基本探索和探勘 - 為資料分析師、新興資料科學家和資料工程師提供簡單的方法，使其能夠透過讀取 T-SQL 查詢，先透過結構描述深入了解其資料湖中的資料
- 邏輯資料倉儲 - 資料分析師可以執行 T-SQL 語言的完整表達方式，以直接查詢和分析位於 Azure 儲存體的資料，並使用熟悉的 BI 工具 (例如 Azure 分析服務和 Power BI Premium 等) 重新執行 Starlight Query 查詢來重新整理儀表板
- 「單一查詢」ETL - 可讓資料工程師以大規模平行處理的方式，將 Azure 儲存體資料從某種格式轉換成另一種格式、進行篩選及彙總等等，並將查詢結果保存在 Azure 儲存體，讓其可立即在 Starlight Query 或其他感興趣的服務中進行進一步處理

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>問：對 Synapse 中的資料流程而言，什麼是良好的使用案例

A：資料流程可讓工程師在無須撰寫程式碼的情況下開發圖形化的轉換邏輯。 產生的資料流程會作為資料整合及協調流程內的活動來執行。 資料流程活動可以透過現有的排程、控制、流程和監視功能來運作。

## <a name="security-and-access"></a>安全性和存取

A：端對端單一登入體驗是 Synapse Analytics 中的重要驗證程序。 透過完整 AAD 整合來管理和傳遞身分識別是必須的。

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>問：如何取得 ADLS Gen2 中檔案和資料夾的存取權

A：檔案和資料夾的存取權目前是透過 ADLS Gen2 來管理。 如需詳細資訊，請參閱 [Data Lake 儲存體的存取控制](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>問：我可以使用第三方商業智慧工具來存取 Azure Synapse Analytics 嗎

A：是，您可以使用第三方商務應用程式 (例如 Tableau 和 Power BI) 來連線到 SQL 集區和 SQL 隨選。 Spark 支援 IntelliJ。

## <a name="next-steps"></a>後續步驟

- [建立工作區](quickstart-create-workspace.md)
- [使用 Synapse Studio](quickstart-synapse-studio.md)
- [建立 SQL 集區](quickstart-create-sql-pool-portal.md)
- [使用 SQL 隨選](quickstart-sql-on-demand.md)
- [建立 Apache Spark 集區](quickstart-create-apache-spark-pool-portal.md) 
