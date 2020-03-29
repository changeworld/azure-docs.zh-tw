---
title: 將資料載入到 Azure 存儲環境 - 團隊資料科學流程
description: 瞭解如何將資料引入存儲和處理資料的各種目標環境。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f8eab59d810fb825dbebf80d01d8efd2dd0a9841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720532"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>將資料載入至儲存體環境以便進行分析

團隊資料科學流程要求在每個階段以最適當的方式引入或載入資料。 資料目標可以包括 Azure Blob 存儲、SQL Azure 資料庫、Azure VM 上的 SQL 伺服器、HDInsight（Hadoop）、同步分析和 Azure 機器學習。 

下列文章說明如何將資料內嵌至可儲存和處理資料的各種目標環境中。

* 內嵌至/自 [Azure Blob 儲存體](move-azure-blob.md)
* 內嵌至 [Azure VM 上的 SQL Server](move-sql-server-virtual-machine.md)
* 到[Azure SQL 資料庫](move-sql-azure.md)
* 內嵌至 [Hive 資料表](move-hive-tables.md)
* 內嵌至 [SQL 分割資料表](parallel-load-sql-partitioned-tables.md)
* 內嵌自[內部部署 SQL Server](move-sql-azure-adf.md)

技術和業務需求以及資料的初始位置、格式和大小將決定最佳資料引入計畫。 最好的計畫有幾個步驟並不罕見。 比方說，這一系列工作可以包含資料瀏覽、前置處理、清除、向下取樣和模型定型。  Azure 資料工廠是一種推薦的 Azure 資源，用於協調資料移動和轉換。
