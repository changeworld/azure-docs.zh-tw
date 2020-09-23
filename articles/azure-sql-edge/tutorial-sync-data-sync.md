---
title: 使用 SQL 資料同步從 Azure SQL Edge 同步資料
description: 了解如何使用 Azure SQL 資料同步從 Azure SQL Edge 同步資料
keywords: SQL Edge, 從 SQL Edge 同步資料, SQL Edge 資料同步
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5830cedfdce54c4cd91eb60a5cd2145309e965b0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904120"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-sql-database-by-using-sql-data-sync"></a>教學課程：使用 SQL 資料同步將 SQL Edge 中的資料同步至 Azure SQL Database

在本教學課程中，您將了解如何使用 Azure SQL 資料同步的「同步群組」，以累加方式將 Azure SQL Edge 中的資料同步至 Azure SQL Database。 「SQL 資料同步」是建置在 Azure SQL Database 上的服務，可讓您跨 Azure SQL Database 與 SQL Server 執行個體中的多個資料庫，雙向同步您選取的資料。 如需 SQL 資料同步的詳細資訊，請參閱 [Azure SQL 資料同步](../azure-sql/database/sql-data-sync-data-sql-server-sql-database.md)。

由於 SQL Edge 是以最新版的 [SQL Server 資料庫引擎](/sql/sql-server/sql-server-technical-documentation/)為建置基礎，因此適用於 SQL Server 執行個體的任何資料同步處理機制，也都可用來將資料同步至在 Edge 裝置上執行的 SQL Edge 執行個體，或進行反向操作。

## <a name="prerequisites"></a>必要條件

本教學課程需要 Windows 電腦，並且需要已設定[適用於 Azure SQL 資料同步的 Data Sync Agent](../azure-sql/database/sql-data-sync-agent-overview.md)。

## <a name="before-you-begin"></a>開始之前

* 在 Azure SQL Database 中建立資料庫。 如需關於如何使用 Azure 入口網站建立資料庫的詳細資訊，請參閱[在 Azure SQL Database 中建立單一資料庫](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)。

* 在您的 Azure SQL Database 部署中建立資料表和其他必要物件。

* 在您的 Azure SQL Edge 部署中建立必要的資料表和物件。 如需詳細資訊，請參閱[將 SQL Database DAC 套件用於 SQL Edge](deploy-dacpac.md)。

* 將 Azure SQL Edge 執行個體註冊至適用於 Azure SQL 資料同步的 Data Sync Agent。如需詳細資訊，請參閱[新增 SQL Server 資料庫](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-on-prem)。

## <a name="sync-data-between-a-database-in-azure-sql-database-and-sql-edge"></a>在 Azure SQL Database 中的資料庫與 SQL Edge 之間同步資料

要使用 SQL 資料同步設定 Azure SQL Database 中的資料庫與 SQL Edge 執行個體之間的同步，必須執行三個主要步驟：  


1. 使用 Azure 入口網站建立同步群組。 如需詳細資訊，請參閱[建立同步群組](../azure-sql/database/sql-data-sync-sql-server-configure.md#create-sync-group)。 您可以使用單一「中樞」資料庫來建立多個同步群組，以將不同 SQL Edge 執行個體的資料同步至 Azure SQL Database 中的一或多個資料庫。 

2. 將同步成員新增至同步群組。 如需詳細資訊，請參閱[新增同步成員](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members)。

3. 設定同步群組，以選取將包含在同步中的資料表。 如需詳細資訊，請參閱[設定同步群組](../azure-sql/database/sql-data-sync-sql-server-configure.md#add-sync-members)。

完成上述步驟後，您會有一個包含 Azure SQL Database 中的資料庫和 SQL Edge 執行個體的同步群組。

如需有關「SQL 資料同步」的詳細資訊，請參閱下列文章：

* [適用於 Azure SQL Data Sync 的 Data Sync Agent](../azure-sql/database/sql-data-sync-agent-overview.md)

* [最佳做法](../azure-sql/database/sql-data-sync-best-practices.md)和[如何針對 Azure SQL 資料同步問題進行疑難排解](../azure-sql/database/sql-data-sync-troubleshoot.md)

* [使用 Azure 監視器記錄監視 SQL 資料同步](../azure-sql/database/sql-data-sync-monitor-sync.md)

* 使用 [Transact-SQL](../azure-sql/database/sql-data-sync-update-sync-schema.md) 或 [PowerShell](../azure-sql/database/scripts/update-sync-schema-in-sync-group.md) 更新同步結構描述

## <a name="next-steps"></a>後續步驟


* [使用 PowerShell 在 Azure SQL Database 與 Azure SQL Edge 之間進行同步](../azure-sql/database/scripts/sql-data-sync-sync-data-between-azure-onprem.md)。 在本教學課程中，請將 `OnPremiseServer` 資料庫詳細資料取代為 Azure SQL Edge 詳細資料。
