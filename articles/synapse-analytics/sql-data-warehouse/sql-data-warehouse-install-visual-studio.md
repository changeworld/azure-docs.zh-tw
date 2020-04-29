---
title: 安裝 Visual Studio 2019
description: 安裝適用于 Synapse SQL 的 Visual Studio 和 SQL Server 開發工具（SSDT）
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f83ae9c8290a52381c8087b46da959d4723d7f4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80745161"
---
# <a name="getting-started-with-visual-studio-2019"></a>Visual Studio 2019 入門

Visual Studio **2019** SQL SERVER DATA TOOLS （SSDT）是單一工具，可讓您執行下列動作：

- 連接、查詢和開發應用程式
- 利用 [物件瀏覽器]，以視覺化方式探索資料模型中的所有物件，包括資料表、視圖、預存程式等等。
- 為您的物件產生 T-sql 資料定義語言（DDL）腳本
- 使用以狀態為基礎的方法與 SSDT 資料庫專案來開發您的資料倉儲
- 將您的資料庫專案與原始檔控制系統（例如 Git）整合 Azure Repos
- 使用自動化伺服器設定持續整合和部署管線，例如 Azure DevOps

## <a name="install-visual-studio-2019"></a>安裝 Visual Studio 2019

請參閱[下載 Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) ，下載並安裝 Visual Studio **16.3 和**更新版本。 在安裝期間，選取資料儲存和處理工作負載。 Visual Studio 2019 中不再需要安裝獨立 SSDT。

## <a name="unsupported-features-in-ssdt"></a>SSDT 中不支援的功能

有些時候，Synapse SQL 的功能版本可能不包含 SSDT 的支援。 目前不支援下列功能：

- [具體化檢視](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [已排序的叢集資料行存放區索引](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [COPY 陳述式](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [工作負載管理](sql-data-warehouse-workload-management.md)-工作負載群組和分類器
- [資料列層級安全性](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - 提交[支援票證，或投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)以取得支援的功能。
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - 提交[支援票證，或投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)以取得支援的功能。
- 不支援[具有條件約束的資料表](sql-data-warehouse-table-constraints.md#table-constraints)。 針對這些資料表物件，請將組建動作設定為 [無]。

## <a name="next-steps"></a>後續步驟

既然您已有最新版本的 SSDT，就可以開始連線[到您](sql-data-warehouse-query-visual-studio.md)的 SQL 集區。
