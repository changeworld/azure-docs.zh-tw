---
title: 安裝 Visual Studio 2019
description: 安裝適用于 SQL 分析的 Visual Studio 和 SQL Server 開發工具（SSDT）
services: sql-data-warehouse
ms.custom: azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b9ad0f4aedc5a095bfa70dec1b67e52cd4d152e0
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195334"
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
請參閱[下載 Visual Studio 2019][] ，下載並安裝 Visual Studio **16.3 和**更新版本。 在安裝期間，選取資料儲存和處理工作負載。 Visual Studio 2019 中不再需要安裝獨立 SSDT。

## <a name="unsupported-features-in-ssdt"></a>SSDT 中不支援的功能

有些時候 SQL 分析的功能版本可能不包含 SSDT 的支援。 目前不支援下列功能：

- [具體化視圖](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)（進行中）
- 已排序的叢集資料行存放區[索引](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-)（進行中）
- [COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（進行中）
- [工作負載管理](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management)-工作負載群組和分類器（進行中）
- [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - 請提交支援票證或在[此](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)投票，以取得支援的功能。
- [動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - 請提交支援票證或在[此](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)投票，以取得支援的功能。 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest)函數 
- 不支援[具有條件約束的資料表](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints)。 針對這些資料表物件，請將組建動作設定為 [無]。

## <a name="next-steps"></a>後續步驟

既然您已有最新版本的 SSDT，就可以開始連線[到您][connect]的 SQL 集區。

<!--Anchors-->

<!--Image references-->

<!--Articles-->

[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->

[下載 Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
