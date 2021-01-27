---
title: 什麼是 Azure Synapse Analytics？
description: Azure Synapse Analytics 的概觀
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 7882fc6a6823a93d09c04a8ae2c19005df4843d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917088"
---
# <a name="what-is-azure-synapse-analytics"></a>什麼是 Azure Synapse Analytics？

企業分析必須在任何類型的資料上進行大規模作業，不論是未經處理、精簡或高度策劃的資料都是如此。 這通常需要企業將巨量資料和資料倉儲技術與複雜的資料管線結合，以便在關聯式存放區和資料湖中處理資料。 這類解決方案很不容易建立、維護及保護。 其複雜度會讓企業所需的深入解析更難以傳達。

**Azure Synapse** 是一種整合式分析服務，可讓您在資料倉儲和巨量資料系統之間，更快地取得深入解析。 Azure Synapse 會將企業資料倉儲中所用的 **SQL** 技術、巨量資料所用的 **Spark** 技術、用於資料整合和 ETL/ELT 的 **管線**，以及與其他 Azure 服務 (例如 **Power BI**、**CosmosDB** 和 **AzureML**) 的深度整合，完美地結合在一起。

## <a name="key-features--benefits"></a>主要功能及優點

### <a name="industry-leading-sql"></a>領先業界的 SQL

* **SYNAPSE SQL** 是適用于 t-sql 的分散式查詢系統，可啟用資料倉儲和資料虛擬化案例，並擴充 t-sql 以解決串流和機器學習案例。
* Synapse SQL 提供 **無伺服器** 和 **專用** 的資源模型，能根據您的需求提供耗用量和計費選項。 針對可預測的效能和成本，請建立專用 SQL 集區來為 SQL 資料表中所儲存的資料保留處理效能。 針對非計畫或暴增的工作負載，請使用永遠可用的無伺服器 SQL 端點。
* 請使用內建的 **串流** 功能，將雲端資料來源中的資料放入 SQL 資料表
* 若要整合 AI 與 SQL，請使用 **機器學習** 模型，以利用 [T-SQL PREDICT 函式來為資料評分](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

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

### <a name="built-in-data-integration-via-pipelines"></a>透過管線的內建資料整合

Azure Synapse 內建與 Azure Data Factory 相同的資料整合引擎和體驗，可讓您建立豐富的大規模 ETL 管線，而不需離開 Azure Synapse Analytics。

* 從 90 個以上的資料來源內嵌資料
* 搭配資料流程活動的無程式碼 ETL
* 協調 Notebook、Spark 作業、預存程序、SQL 指令碼等等

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

## <a name="engage-with-the-synapse-engineering-team"></a>洽詢 Synapse 工程團隊

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse)：詢問開發問題。
- [Microsoft 問與答的問題頁面](/answers/topics/azure-synapse-analytics.html)：詢問技術問題。

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure Synapse Analytics](get-started.md)
* [建立工作區](quickstart-create-workspace.md)
* [使用無伺服器 SQL 集區](quickstart-sql-on-demand.md)
