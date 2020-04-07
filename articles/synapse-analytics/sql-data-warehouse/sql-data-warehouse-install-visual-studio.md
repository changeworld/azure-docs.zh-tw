---
title: 安裝 Visual Studio 2019
description: 安裝視覺化工作室與 SQL 伺服器開發工具 (SSDT) 以使用突觸 SQL
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
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745161"
---
# <a name="getting-started-with-visual-studio-2019"></a>開始使用視覺工作室 2019

Visual Studio **2019** SQL 伺服器資料工具 (SSDT) 是一個工具,允許您執行以下操作:

- 連線、查詢及開發應用程式
- 利用物件資源管理器直觀地流覽數據模型中的所有物件,包括表、視圖、存儲過程等。
- 為物件產生 T-SQL 資料定義語言 (DDL) 文稿
- 使用基於狀態的方法使用 SSDT 資料庫專案開發資料主目錄
- 將資料庫專案與來源控制系統(如 Git)與 Azure 儲存庫整合
- 與 Azure DevOps 等自動化伺服器設定持續整合和部署導管

## <a name="install-visual-studio-2019"></a>安裝 Visual Studio 2019

請參閱[下載視覺工作室 2019](https://visualstudio.microsoft.com/downloads/)下載和安裝視覺工作室**16.3 及以上**. 在安裝過程中,選擇數據存儲和處理工作負載。 Visual Studio 2019 不再需要安裝獨立 SSDT。

## <a name="unsupported-features-in-ssdt"></a>SSDT 不支援的功能

有時,Synapse SQL 的功能版本可能不包括對 SSDT 的支援。 目前不支援以下功能:

- [具體化檢視](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [有序的叢集列儲存索引](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [COPY 語句](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [工作負載管理](sql-data-warehouse-workload-management.md)- 工作負載器和分類器
- [資料列層級安全性](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - 提交[票證或投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)以獲得支援功能。
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - 提交[票證或投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)以獲得支援功能。
- 不支援[具有約束的表](sql-data-warehouse-table-constraints.md#table-constraints)。 對於這些表物件,將生成操作設置為"無"。

## <a name="next-steps"></a>後續步驟

現在,您已經擁有最新版本的 SSDT,就可以[連接到](sql-data-warehouse-query-visual-studio.md)SQL 池了。
