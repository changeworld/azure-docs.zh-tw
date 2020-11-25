---
title: Azure Synapse Analytics 筆記本總覽
description: 本文概述透過 Azure Synapse Analytics 筆記本提供的功能。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4387aadd70ddb373df1bdfa61cbe9ed7f2af283d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919484"
---
# <a name="azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 筆記本
Synapse Studio (preview) 筆記本是一個 web 介面，可讓您建立包含即時程式碼、視覺效果和敘述文字的檔案。 筆記本是驗證想法和使用快速實驗從您的資料取得見解的絕佳位置。 

使用 Azure Synapse Studio 筆記本，您可以：

* 無須設定即可開始使用。
* 使用內建的企業安全性功能保護資料的安全。
* 分析包括原始格式 (CSV、txt、JSON 等)、經處理的檔案格式 (parquet、Delta Lake、ORC 等)，以及針對 Spark 和 SQL 的 SQL 表格式資料檔案等各種資料。
* 透過增強的撰寫功能和內建的資料視覺效果，提高生產力。

本節包含混合語言、建立資料視覺效果、參數化筆記本、建立管線等等的相關文章。 其中也包含可讓您開始進行筆記本開發的參考和教學課程。

## <a name="create-manage-and-use-notebooks"></a>建立、管理及使用筆記本
您可以使用 Synapse Studio UI 來管理筆記本。 

若要深入瞭解如何建立和管理筆記本，請參閱下列文章：
  - 管理筆記本
    - [建立筆記本](./spark/../apache-spark-development-using-notebooks.md#create-a-notebook)
    - [開發筆記本](./spark/../apache-spark-development-using-notebooks.md#develop-notebooks)
    - [將資料帶入筆記本](./spark/../apache-spark-development-using-notebooks.md#bring-data-to-a-notebook)
    - [使用魔術命令和臨時表使用多個語言](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)
    - [使用資料格魔術命令](./spark/../apache-spark-development-using-notebooks.md#magic-commands)
  - 開發
    - [設定 Spark 會話設定](./spark/../apache-spark-development-using-notebooks.md#spark-session-config)
    - [使用 Microsoft Spark 公用程式](./spark/../microsoft-spark-utilities.md)
    - [使用筆記本和程式庫將資料視覺化](./spark/../apache-spark-data-visualization.md)
    - [將筆記本整合至管線](./spark/../apache-spark-development-using-notebooks.md#integrate-a-notebook)


## <a name="next-steps"></a>後續步驟
筆記本也廣泛用於資料準備、資料視覺效果、機器學習和其他大型資料案例。 若要深入瞭解如何將筆記本用於資料分析和大型資料案例，請流覽下列教學課程：
  - [建立筆記本](./spark/../../quickstart-apache-spark-notebook.md)
  - [使用 Synapse Studio 筆記本建立視覺效果](./spark/../apache-spark-data-visualization-tutorial.md)
  - [使用 Apache Spark MLlib 建立機器學習模型](./spark/../apache-spark-machine-learning-mllib-notebook.md)
  - [使用 Azure AutoML 建立機器學習模型](./spark/../apache-spark-azure-machine-learning-tutorial.md)