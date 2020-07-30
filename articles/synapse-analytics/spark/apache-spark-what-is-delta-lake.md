---
title: 什麼是 Delta Lake
description: 差異 Lake 的總覽，以及它如何作為 Azure Synapse 分析的一部分運作
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 374b8bb094a93c6c608d6bf95d2b074f661c4a6a
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87384730"
---
# <a name="what-is-delta-lake"></a>什麼是 Delta Lake

Azure Synapse 分析與 Linux Foundation Delta Lake 相容。 Delta Lake 是一種開放原始碼儲存層，可將 ACID （不可部分完成性、一致性、隔離和耐久性）交易帶入 Apache Spark 和 big data 工作負載。

Azure Synapse 中包含的 Delta Lake 目前版本具有 Scala、PySpark 和 .NET 的語言支援。 頁面底部有連結可取得更詳細的範例和檔。

## <a name="key-features"></a>主要功能

| 功能 | 說明 |
| --- | --- |
| **ACID 交易** | 資料 lake 通常會透過多個進程和管線來填入，其中有些是透過讀取同時寫入資料。 在 Delta Lake 和新增交易之前，資料工程師必須經歷手動錯誤的程式，以確保資料完整性。 Delta Lake 將熟悉的 ACID 交易帶入資料 lake。 它提供可序列化能力，這是最強層級的隔離等級。 [深入瞭解 Delta Lake：解壓縮交易記錄](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)檔。|
| **可調整的元資料處理** | 在 big data 中，即使中繼資料本身也可以是「big data」。 Delta Lake 會將中繼資料視為資料，利用 Spark 的分散式處理能力來處理其所有的中繼資料。 因此，Delta Lake 可以輕鬆地處理具有數十億個數據分割和檔案的 pb 規模資料表。 |
| **時間移動（資料版本控制）** | 「復原」變更或回到先前版本的功能是交易的重要功能之一。 Delta Lake 提供資料的快照，可讓您還原為舊版的資料，以進行審核、復原或重現實驗。 深入瞭解[Lake 大規模資料的差異 Lake 時間旅遊簡介](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)。 |
| **開啟格式** | Apache Parquet 是 Delta Lake 的基準格式，可讓您運用格式為原生的有效率壓縮和編碼配置。 |
| **整合的批次和串流來源與接收** | Delta Lake 中的資料表同時是批次資料表以及串流來源和接收。 串流資料內嵌、批次歷程記錄回填和互動式查詢都只是現成的工作。 |
| **架構強制執行** | 架構強制可協助確保資料類型是正確的，而且需要的資料行存在，以防止資料不一致的錯誤。 如需詳細資訊，請參閱[深入瞭解 Delta Lake：架構強制 & 演進](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **架構演進** | Delta Lake 可讓您對可自動套用的資料表架構進行變更，而不需要撰寫遷移 DDL。 如需詳細資訊，請參閱[深入瞭解 Delta Lake：架構強制 & 演進](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Audit 歷程記錄** | Delta Lake transaction 記錄檔會記錄對資料所做的每項變更的詳細資訊，以提供完整的變更審核記錄。 |
| **更新和刪除** | Delta Lake 支援 Scala/JAVA/Python 和 SQL Api 以提供各種功能。 「合併」、「更新」和「刪除」作業的支援可協助您符合合規性需求。 如需詳細資訊，請參閱[宣佈 Delta lake 0.6.1 版本](https://delta.io/news/delta-lake-0-6-1-released/)、使用 Python api 宣佈 delta Lake [0.7 版本](https://delta.io/news/delta-lake-0-7-0-released/)和[簡單可靠的更新插入和刪除](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)，其中包含 merge、update 和 delete DML 命令的程式碼片段。 |
| **與 Apache Spark API 相容的100%** | 開發人員可以將 Delta Lake 與現有的資料管線搭配使用，而不需要變更，因為它與現有的 Spark 部署完全相容。 |

如需完整的檔，請參閱[Delta Lake 檔頁面](https://docs.delta.io/latest/delta-intro.html)

如需詳細資訊，請參閱[Delta Lake Project](https://github.com/delta-io/delta)。

## <a name="next-steps"></a>後續步驟

- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
