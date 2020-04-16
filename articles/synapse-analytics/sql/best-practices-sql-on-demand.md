---
title: Azure 突觸分析中 SQL 按需(預覽)的最佳做法
description: 在使用 SQL 按需(預覽)時,應瞭解的建議和最佳實踐。
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d4203141973c10fe7673f6ab9dedbc3bfdc8999
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429066"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure 突觸分析中 SQL 按需(預覽)的最佳做法

在本文中,您將找到一系列使用 SQL 按需(預覽)的最佳做法。 SQL 按需是 Azure 同步分析中的附加資源。

## <a name="general-considerations"></a>一般考量

SQL 按需允許您查詢 Azure 儲存帳戶中的檔案。 它沒有本地存儲或引入功能。 因此,查詢目標的所有檔都是 SQL 按需的外部檔。 與從存儲讀取檔相關的一切都可能會影響查詢性能。

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>依需為 Azure 儲存帳戶和 SQL 提供共同定位

為了最大程度地減少延遲,請共同定位 Azure 儲存帳戶和 SQL 按需終結點。 工作區創建期間預配的存儲帳戶和終結點位於同一區域。

為了獲得最佳性能,如果使用 SQL 按需存取其他儲存帳戶,請確保它們位於同一區域。 如果它們不在同一區域中,則數據在遠端區域和終結點區域之間的網路傳輸的延遲會增加。

## <a name="azure-storage-throttling"></a>Azure 儲存限制

多個應用程式和服務可以存取您的儲存帳戶。 當應用程式、服務和 SQL 按需工作負載生成的合併 IOPS 或輸送量超過存儲帳戶的限制時,就會發生存儲限制。 因此,您將遇到對查詢性能的重大負面影響。

檢測到限制后,SQL 按需已內建處理此方案。 SQL 按需將以較慢的速度發出存儲請求,直到解決限制問題。

> [!TIP]
> 為了獲得最佳的查詢執行,在查詢執行期間,不應將存儲帳戶與其他工作負載重壓。

## <a name="prepare-files-for-querying"></a>準備用於查詢的檔案

如果可能,您可以準備檔案以取得最佳效能:

- 將 CSV 轉換為鑲木地板 - 鑲木地板為柱形格式。 由於已壓縮,其檔大小小於具有相同數據的 CSV 檔。 SQL 按需讀取它所需的時間和存儲請求將更少。
- 如果查詢以單個大型檔為目標,則可以將其拆分為多個較小的檔。
- 請嘗試將 CSV 檔案大小保持在 10 GB 以下。
- 最好為單個 OPENROWSET 路徑或外部表位置提供大小相等的檔。
- 以將分割區儲存到不同的資料夾或檔案名稱來劃分資料 - 檢查[使用檔案名稱與檔案路徑函數來定位特定分割區](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)。

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>使用檔案資訊與檔案路徑函數定位特定分割區

數據通常以分區方式組織。 您可以指示 SQL 按需查詢特定資料夾和檔。 此函數將減少查詢需要讀取和處理的文件數量和數據量。 額外的好處是,你會取得更好的表現。

有關詳細資訊,請查看[檔名](develop-storage-files-overview.md#filename-function)和[檔路徑](develop-storage-files-overview.md#filepath-function)函數以及如何[查詢特定檔](query-specific-files.md)的範例。

如果存儲的數據未分區,請考慮對其進行分區,以便可以使用這些函數優化針對這些檔的查詢。 當查詢從 SQL 按需[分區 Spark 表](develop-storage-files-spark-tables.md)時,查詢將自動僅定位所需的檔。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>使用 CETAS 提高查詢效能和聯結

[CETAS](develop-tables-cetas.md)是 SQL 按需提供的最重要功能之一。 CETAS 是一個並行操作,用於創建外部表元數據並將 SELECT 查詢結果匯出到存儲帳戶中的一組檔。

可以使用 CETAS 將常用的查詢部分(如聯接的引用表)儲存到一組新的檔。 接下來,您可以加入此外部表,而不是在多個查詢中重複公共聯接。

當 CETAS 生成 Parquet 檔時,當第一個查詢針對此外部表時,將自動創建統計資訊,從而提高性能。

## <a name="next-steps"></a>後續步驟

查看[疑難解答](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)文章,查看常見問題和解決方案。 如果您正在使用 SQL 池而不是按需使用 SQL,請參閱 SQL[池的最佳實踐](best-practices-sql-pool.md)一文,瞭解特定指南。
