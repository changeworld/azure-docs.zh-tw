---
title: 使用 SQL 資料同步從 Azure SQL Edge (預覽) 同步資料
description: 了解如何使用 Azure SQL 資料同步從 Azure SQL Edge (預覽) 同步資料
keywords: SQL Edge, 從 SQL Edge 同步資料, SQL Edge 資料同步
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 7971681c3f0c99a11567e6a30e61167c5d42348c
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680503"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>教學課程：使用 SQL 資料同步將 SQL Edge 中的資料同步至 Azure SQL Database

在本教學課程中，您將了解如何使用 Azure SQL 資料同步的「同步群組」，以累加方式將 Azure SQL Edge 中的資料同步至 Azure SQL Database。 「SQL 資料同步」是一種建置在 Azure SQL Database 上的服務，可讓您跨多個 SQL 資料庫和 SQL Server 執行個體，雙向同步您選取的資料。 如需 SQL 資料同步的詳細資訊，請參閱 [Azure SQL 資料同步](../sql-database/sql-database-sync-data.md)。

由於 SQL Edge 是以最新版的 [SQL Server 資料庫引擎](/sql/sql-server/sql-server-technical-documentation/)為建置基礎，因此適用於內部部署 SQL Server 執行個體的任何資料同步處理機制，也都可用來將資料同步至在 Edge 裝置上執行的 SQL Edge 執行個體，或進行反向操作。

## <a name="prerequisites"></a>Prerequisites

本教學課程需要 Windows 電腦，並且需要已設定[適用於 Azure SQL 資料同步的 Data Sync Agent](../sql-database/sql-database-data-sync-agent.md)。

## <a name="before-you-begin"></a>開始之前

* 建立 Azure SQL 資料庫。 如需關於如何使用 Azure 入口網站建立 Azure SQL 資料庫的詳細資訊，請參閱[在 Azure SQL Database 中建立單一資料庫](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)。

* 在您的 Azure SQL Database 部署中建立資料表和其他必要物件。

* 在您的 Azure SQL Edge 部署中建立必要的資料表和物件。 如需詳細資訊，請參閱[將 SQL Database DAC 套件用於 SQL Edge](deploy-dacpac.md)。

* 將 Azure SQL Edge 執行個體註冊至適用於 Azure SQL 資料同步的 Data Sync Agent。如需詳細資訊，請參閱[新增內部部署 SQL Server 資料庫](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)。

## <a name="sync-data-between-an-azure-sql-database-and-sql-edge"></a>在 Azure SQL 資料庫與 SQL Edge 之間同步資料

要使用 SQL 資料同步設定 Azure SQL 資料庫與 SQL Edge 執行個體之間的同步，必須執行三個主要步驟：  

1. 使用 Azure 入口網站建立同步群組。 如需詳細資訊，請參閱[建立同步群組](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)。 您可以使用單一「中樞」資料庫來建立多個同步群組，以將不同 SQL Edge 執行個體的資料同步至 Azure 中的一個或多個 SQL 資料庫。

2. 將同步成員新增至同步群組。 如需詳細資訊，請參閱[新增同步成員](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)。

3. 設定同步群組，以選取將包含在同步中的資料表。 如需詳細資訊，請參閱[設定同步群組](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)。

完成上述步驟後，您會有一個包含 Azure SQL 資料庫和 SQL Edge 執行個體的同步群組。

如需有關「SQL 資料同步」的詳細資訊，請參閱下列文章：

* [適用於 Azure SQL Data Sync 的 Data Sync Agent](../sql-database/sql-database-data-sync-agent.md)

* [最佳做法](../sql-database/sql-database-best-practices-data-sync.md)和[如何針對 Azure SQL 資料同步問題進行疑難排解](../sql-database/sql-database-troubleshoot-data-sync.md)

* [使用 Azure 監視器記錄監視 SQL 資料同步](../sql-database/sql-database-sync-monitor-oms.md)

* 使用 [Transact-SQL](../sql-database/sql-database-update-sync-schema.md) 或 [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md) 更新同步結構描述

## <a name="next-steps"></a>後續步驟

* [使用 PowerShell 在 Azure SQL Database 與 Azure SQL Edge 之間進行同步](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md)。 在本教學課程中，請將 `OnPremiseServer` 資料庫詳細資料取代為 Azure SQL Edge 詳細資料。
