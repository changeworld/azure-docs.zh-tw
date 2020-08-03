---
title: 教學課程：開始使用 Azure Synapse Analytics
description: 在本教學課程中，您將了解設定和使用 Azure Synapse Analytics 的基本步驟。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 7affc5aad89fd79e6ba6480f7bf10d37f90dc5e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075871"
---
# <a name="get-started-with-azure-synapse-analytics"></a>開始使用 Azure Synapse Analytics

本教學課程是 Azure Synapse Analytics 主要功能領域的逐步指南。 對於想要引導式導覽 Azure Synapse Analytics 重要案例的人員，本教學課程是絕佳的起點。 在遵循教學課程中的步驟之後，您將會有一個功能完整的 Synapse 工作區，您可以在其中使用 SQL、隨選 SQL 和 Apache Spark 來開始分析資料。

您將了解：
* 在 Azure 訂用帳戶中佈建 Synapse 工作區
* 在 ADLSGEN2 帳戶上設定存取控制，使其可順暢地與 Synapse 工作區搭配使用
* 將 NYCTaxi 範例資料載入至 Synapse 工作區，使其可供 SQL、隨選 SQL 和 Spark 使用
* 使用 Synapse Studio 編輯和執行 SQL 指令碼和 Synapse 筆記本
* 查詢 SQL 資料表和 Spark 資料表
* 將資料從 SQL 資料表載入至 Spark 資料框架
* 將資料從 Spark 資料框架載入至 SQL 資料表
* 探索 ADLSGEN2 帳戶的內容
* 使用 Spark 和隨選 SQL 來分析 ADLSGEN2 帳戶中的 parquet 資料檔案 
* 建置每小時自動執行 Synapse 筆記本的資料管線

「依序」遵循下面所示的步驟，而且您會導覽許多功能，並了解如何練習其核心功能。

* [步驟 1 - 建立並設定 Synapse 工作區](get-started-create-workspace.md)
* [步驟 2 - 使用 SQL 集區進行分析](get-started-analyze-sql-pool.md)
* [步驟 3 - 使用 Spark 進行分析](get-started-analyze-spark.md)
* [步驟 4 - 使用隨選 SQL 進行分析](get-started-analyze-sql-on-demand.md)
* [步驟 5 - 分析儲存體帳戶中的資料](get-started-analyze-storage.md)
* [步驟 6 - 使用管線進行協調](get-started-pipelines.md)
* [步驟 7 - 使用 Power BI 視覺化資料](get-started-visualize-power-bi.md)
