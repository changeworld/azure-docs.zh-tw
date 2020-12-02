---
title: 將資料載入 Azure 儲存體環境-Team Data 科學流程
description: 瞭解如何將資料內嵌至儲存和處理資料的各種目標環境中。
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
ms.openlocfilehash: 46e911ad6bdf5a478d46e425f8700740ece56c6e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452661"
---
# <a name="load-data-into-storage-environments-for-analytics"></a>將資料載入至儲存體環境以便進行分析

小組資料科學程式要求資料必須在每個階段中以最適當的方式內嵌或載入。 資料目的地可以包含 Azure Blob 儲存體、SQL Azure 資料庫、Azure VM 上的 SQL Server、HDInsight (Hadoop) 、Azure Synapse Analytics 和 Azure Machine Learning。 

下列文章說明如何將資料內嵌至可儲存和處理資料的各種目標環境中。

* 內嵌至/自 [Azure Blob 儲存體](move-azure-blob.md)
* 內嵌至 [Azure VM 上的 SQL Server](move-sql-server-virtual-machine.md)
* 若要 [Azure SQL Database](move-sql-azure.md)
* 內嵌至 [Hive 資料表](move-hive-tables.md)
* 內嵌至 [SQL 分割資料表](parallel-load-sql-partitioned-tables.md)
* 內嵌自[內部部署 SQL Server](move-sql-azure-adf.md)

技術和商務需求，以及資料的初始位置、格式和大小，將會決定最佳的資料內嵌計畫。 最好的計畫有幾個步驟並不常見。 比方說，這一系列工作可以包含資料瀏覽、前置處理、清除、向下取樣和模型定型。  Azure Data Factory 是可協調資料移動和轉換的建議 Azure 資源。
