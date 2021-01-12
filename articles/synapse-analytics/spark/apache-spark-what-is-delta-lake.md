---
title: 什麼是 Delta Lake
description: Delta Lake 的總覽，以及它在 Azure Synapse Analytics 中的運作方式
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: cd582c1175d6bff0aa18ee77dde8b7734b6acf25
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121017"
---
# <a name="what-is-delta-lake"></a>什麼是 Delta Lake

Azure Synapse Analytics 與 Linux Foundation Delta Lake 相容。 Delta Lake 是開放原始碼的儲存層，可將 ACID (不可部分完成性、一致性、隔離和持久性) 交易提供給 Apache Spark 和大型資料工作負載。

Azure Synapse 隨附的 Delta Lake 目前版本具有 Scala、PySpark 和 .NET 的語言支援。 頁面底部有連結可取得更詳細的範例和檔。

## <a name="key-features"></a>主要功能

| 功能 | 描述 |
| --- | --- |
| **ACID 交易** | 通常會透過多個進程和管線來擴展資料 lake，其中有些會與讀取同時寫入資料。 在 Delta Lake 和新增交易之前，資料工程師必須經歷手動錯誤的程式，以確保資料完整性。 Delta Lake 將熟悉的 ACID 交易帶入資料 lake。 它提供可序列化能力，這是最強的隔離層級。 深入瞭解 [Delta Lake：將交易記錄解壓縮](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html)。|
| **可調整的元資料處理** | 在大資料中，即使中繼資料本身也可以是「big data」。 Delta Lake 處理中繼資料的方式就像資料一樣，利用 Spark 的分散式處理能力來處理其所有中繼資料。 如此一來，Delta Lake 就能輕鬆地以數十億個分割區和檔案處理 pb 規模的資料表。 |
| **時間移動 (資料版本控制)** | 「復原」變更或回到先前版本的功能，是交易的主要功能之一。 Delta Lake 提供資料的快照集，可讓您還原至較早的資料版本，以進行審核、復原或重現實驗。 深入瞭解 [大規模資料 lake 的 Delta Lake 時間移動簡介](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html)。 |
| **開啟格式** | Apache Parquet 是 Delta Lake 的基準格式，可讓您運用格式的原生有效壓縮和編碼配置。 |
| **整合批次和串流來源和接收** | Delta Lake 中的資料表同時是批次資料表以及串流來源和接收。 串流資料內嵌、批次歷程記錄回填和互動式查詢全都是現成的工作。 |
| **強制執行架構** | 強制執行架構有助於確保資料類型正確且必要的資料行存在，防止錯誤的資料導致資料不一致。 如需詳細資訊，請參閱 [深入瞭解 Delta Lake：架構強制 & 演進](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **架構演進** | Delta Lake 可讓您變更可自動套用的資料表架構，而不需要撰寫遷移 DDL。 如需詳細資訊，請參閱 [深入瞭解 Delta Lake：架構強制 & 演進](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **審核歷程記錄** | Delta Lake 交易記錄檔會記錄對資料所做的每項變更詳細資料，以提供變更的完整審核記錄。 |
| **更新和刪除** | Delta Lake 針對各種功能支援 Scala/JAVA/Python 和 SQL Api。 合併、更新和刪除作業的支援可協助您符合合規性需求。 如需詳細資訊，請參閱宣佈 delta lake [0.6.1 版本](https://delta.io/news/delta-lake-0-6-1-released/)，並使用 Python api 宣佈 delta Lake  [0.7 版本](https://delta.io/news/delta-lake-0-7-0-released/) 和 [簡單、可靠的 upsert 和刪除](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html)，其中包含合併、更新和刪除 DML 命令的程式碼片段。 |
| **100% 與 Apache Spark API 相容** | 開發人員可以使用 Delta Lake 與其現有的資料管線，因為它與現有的 Spark 執行完全相容，因此幾乎不需要變更。 |

如需完整文件，請參閱 [Delta Lake 文件頁面](https://docs.delta.io/latest/delta-intro.html)

如需詳細資訊，請參閱 [Delta Lake 專案](https://github.com/delta-io/delta)。

## <a name="next-steps"></a>後續步驟

- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](../index.yml)