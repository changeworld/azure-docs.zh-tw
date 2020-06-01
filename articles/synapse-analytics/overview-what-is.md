---
title: 什麼是 Azure Synapse Analytics？
description: Azure Synapse Analytics 的概觀
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 0f34b853728c91d9e3c6e32396c0f7eb427af101
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656200"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>什麼是 Azure Synapse Analytics (工作區預覽)？

[!INCLUDE [preview](includes/note-preview.md)]

現今的企業分析皆需要在任何類型的資料上進行大規模作業，不論是未經處理、精簡或高度策劃的資料都是如此。 在過去，建立這些類型的分析解決方案都需要企業將巨量資料和資料倉儲技術 (例如 Spark 和 SQL) 結合在一起。 接著，必須將這些項目整合到豐富的資料管線，以便在關聯式存放區和資料湖中處理資料。  

建立、設定、保護和維護這類解決方案相當不容易，因此會延遲智慧型深入解析的即時擷取。

**Azure Synapse** 是一種整合式分析服務，可讓您在資料倉儲和巨量資料分析系統之間，更快地取得所有資料的深入解析，且不論資料規模為何。 此服務將企業資料倉儲中所用的 **SQL** 技術、巨量資料分析中所用的 **Spark** 技術，以及協調活動和資料移動的**管線**完美地結合在一起。

Azure Synapse 隨附 Web 原生的 **Studio** 使用者體驗，可提供管理、監視、編碼和安全性的單一體驗和模型。

Azure Synapse 為企業提供最簡單且最快速的方式，讓他們能夠使用最熟悉的分析方式來對任何規模的任何資料收集深入解析。 其與 **Power BI** 緊密整合，讓資料工程師能夠建立端對端的分析解決方案，來提供商業智慧。

此外，Azure Synapse 可透過透過其內建的 **AzureML** 支援，使用機器學習來協助預測模型的建置和進行進階分析。

## <a name="key-features--benefits"></a>主要功能及優點

### <a name="industry-leading-sql"></a>領先業界的 SQL

* **Synapse SQL** 是一種分散式查詢系統，可讓企業使用資料工程師熟悉的標準 T-SQL 體驗來實作資料倉儲和資料虛擬化案例。 此系統也可擴充 SQL 的功能，進而處理串流和機器學習案例。

* Synapse SQL 提供**無伺服器**和**已佈建**的資源模型，能根據您的需求提供耗用量和計費選項。 針對可預測的效能和成本，請佈建集區來為 SQL 資料表中所儲存的資料保留處理效能。 針對非計畫或暴增的工作負載，請使用無伺服器且永遠可用的 SQL 端點。
* 請使用內建的**串流**功能，將雲端資料來源中的資料放入 SQL 資料表
* 若要整合 AI 與 SQL，請使用**機器學習**模型，以利用 T-SQL PREDICT 函式來為資料評分

### <a name="industry-standard-apache-spark"></a>業界標準的 Apache Spark

**適用於 Synapse Spark 的 Apache Spark** 會深入且完美地整合 Apache Spark (這是最受歡迎的開放原始碼巨量資料引擎，可用於資料準備、資料工程、ETL 和機器學習)。

* Apache Spark 2.4 具有搭配 SparkML 演算法和 AzureML 整合的 ML 模型，且內建 Linux Foundation Delta Lake 支援。
* 簡化的資源模型，讓您不必擔心如何管理叢集。
* 快速的 Spark 啟動和主動進行自動調整。
* Spark 的內建 .NET 支援，可讓您在 Spark 應用程式中重複使用您的 C# 專業知識和現有的 .NET 程式碼。

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>在 Data Lake 上交互操作 SQL 和 Apache Spark

Azure Synapse 移除了同時使用 SQL 和 Spark 的傳統技術障礙。 您可以根據需求和專長，順暢地混搭這兩項功能。

* 共用與 Hive 相容的中繼資料系統可讓 Spark 或 Hive 順暢地使用資料湖中檔案上定義的資料表。
* SQL 和 Spark 可以直接探索和分析儲存在資料湖中的 Parquet、CSV、TSV 和 JSON 檔案。
* 可快速調整 SQL 和 Spark 資料庫之間的資料載入和卸載

### <a name="built-in-orchestration-via-pipelines"></a>透過管線進行的內建協調流程

Azure Synapse 內建與 Azure Data Factory 相同的資料整合引擎和體驗，可讓您建立豐富的資料管線，而不需要使用個別的協調流程引擎。

* 在 Azure Synapse 與 90 個以上的內部部署資料來源之間移動資料
* 可協調 Notebook、管線、Spark 作業、SQL 指令碼、預存程序
* 搭配資料流程活動的無程式碼 ETL

### <a name="unified-management-monitoring-and-security"></a>統一的管理、監視和安全性

Azure Synapse 為企業提供單一方式來管理分析資源、監視使用方式和活動，以及強制執行安全性。

* 將使用者指派給角色，以簡化分析資源的存取
* 對資料和程式碼進行更細微的存取控制
* 使用單一儀表板監視 SQL 和 Spark 上的資源、使用量和使用者

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** 是將所有項目結合在一起以供資料工程師使用的 Web 原生體驗，以便工程師在一個位置上執行所需的每項工作，進而建立完整的解決方案。

* 在一個位置上建立端對端分析解決方案：內嵌、探索、準備、協調、視覺化
* 為撰寫 SQL 或 Spark 程式碼的資料工程師帶來領先業界的生產力：製作、偵錯和效能最佳化
* 與企業 CI/CD 程序整合

## <a name="next-steps"></a>後續步驟

* [建立工作區](quickstart-create-workspace.md)
* [使用 Synapse Studio](quickstart-synapse-studio.md)
* [建立 SQL 集區](quickstart-create-sql-pool-portal.md)
* [使用 SQL 隨選](quickstart-sql-on-demand.md)
* [建立 Apache Spark 集區](quickstart-create-apache-spark-pool-portal.md)
