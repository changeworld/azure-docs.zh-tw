---
title: 安裝 Visual Studio 2019
description: 安裝用於 SQL 分析的視覺化工作室和 SQL 伺服器開發工具 （SSDT）
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
ms.openlocfilehash: d0a1772706fa838f51322d5f5d5bd1b46eb9144a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351632"
---
# <a name="getting-started-with-visual-studio-2019"></a>開始使用視覺工作室 2019
Visual Studio **2019** SQL 伺服器資料工具 （SSDT） 是一個工具，允許您執行以下操作：

- 連接、查詢和開發應用程式
- 利用物件資源管理器直觀地流覽資料模型中的所有物件，包括表、視圖、預存程序等。
- 為物件生成 T-SQL 資料定義語言 （DDL） 腳本
- 使用基於狀態的方法使用 SSDT 資料庫專案開發資料倉儲
- 將資料庫專案與源控制系統（如 Git）與 Azure 存儲庫集成
- 與 Azure DevOps 等自動化伺服器設置持續集成和部署管道

## <a name="install-visual-studio-2019"></a>安裝 Visual Studio 2019
請參閱[下載視覺工作室 2019][]下載和安裝視覺工作室**16.3 及以上**. 在安裝過程中，選擇資料存儲和處理工作負載。 Visual Studio 2019 不再需要安裝獨立 SSDT。

## <a name="unsupported-features-in-ssdt"></a>SSDT 中不支援的功能

有時 SQL 分析的功能版本可能不包括對 SSDT 的支援。 當前不支援以下功能：

- [具體化視圖](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)（正在進行中）
- [有序的群集列存儲索引](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-)（正在進行中）
- [COPY 語句](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest)（正在進行中）
- [工作負載管理](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management)- 工作負載組和分類器（正在進行中）
- [資料列層級安全性](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - 在此處提交支援票證[或投票以獲得](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)支援功能。
- [動態資料遮罩](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - 在此處提交支援票證[或投票以獲得](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)支援功能。 
- [預測](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest)功能 
- 不支援[具有約束的表](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints)。 對於這些表物件，將生成操作設置為"無"。

## <a name="next-steps"></a>後續步驟

現在，您已經擁有最新版本的 SSDT，就可以[連接到](sql-data-warehouse-query-visual-studio.md)SQL 池了。




<!--Other-->

[下載 Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
