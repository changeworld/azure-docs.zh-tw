---
title: 安裝 Visual Studio 2019
description: '安裝適用于 Synapse SQL 的 Visual Studio 和 SQL Server 開發工具 (SSDT) '
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b70694011aa0467d8ca037ac309ffb0811c79c01
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737943"
---
# <a name="getting-started-with-visual-studio-2019"></a>Visual Studio 2019 使用者入門

Visual Studio **2019** SQL SERVER DATA TOOLS (SSDT) 是單一工具，可讓您執行下列作業：

- 連接、查詢和開發應用程式
- 利用物件瀏覽器以視覺化方式流覽資料模型中的所有物件，包括資料表、視圖、預存程式等。
- 為您的物件產生 T-sql 資料定義語言 (DDL) 腳本
- 使用以狀態為基礎的方法搭配 SSDT 資料庫專案來開發您的資料倉儲
- 將您的資料庫專案與具有 Azure Repos 的 Git 等原始檔控制系統整合
- 使用自動化伺服器（例如 Azure DevOps）設定持續整合和部署管線

## <a name="install-visual-studio-2019"></a>安裝 Visual Studio 2019

若要下載並安裝 Visual Studio **16.3 和** 更新版本，請參閱 [下載 Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 。 在安裝期間，選取資料儲存和處理工作負載。 在 Visual Studio 2019 中不再需要安裝獨立 SSDT。

## <a name="unsupported-features-in-ssdt"></a>SSDT 中不支援的功能

有些時候，Synapse SQL 的功能版本可能不包含 SSDT 的支援。 目前不支援下列功能：


- [工作負載管理](sql-data-warehouse-workload-management.md) -工作負載群組和分類器
- 包含資料表值函數的資料[列層級安全性](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) () 
  - 提交 [支援票證或投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) ，以取得支援的功能。
  - 提交 [支援票證或投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) ，以取得支援的功能。
- 具有[識別欄位](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest&preserve-view=true)的資料表
- 某些 T-sql 功能，例如：
   - 在 [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql)字串函數的 *GROUP 子句內*。

## <a name="next-steps"></a>後續步驟

既然您已有最新版本的 SSDT，您就可以 [連接](sql-data-warehouse-query-visual-studio.md) 到 SQL 集區。