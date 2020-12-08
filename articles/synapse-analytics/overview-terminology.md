---
title: 術語 - Azure Synapse Analytics
description: 帶領使用者了解 Azure Synapse Analytics 的參考指南
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 7f0ca83c194aae6f4a8d04d70c2d4f3746ad2ca5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446706"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics 術語

本文件將引導您了解 Azure Synapse Analytics 的基本概念。

## <a name="basics"></a>基本

**Synapse 工作區** 是在 Azure 中進行雲端式企業分析的安全共同作業界限。 工作區會部署在特定區域中，並且具有相關聯的 ADLS Gen2 帳戶和檔案系統 (用於儲存暫存資料)。 工作區位在資源群組底下。

工作區可讓您使用 SQL 和 Apache Spark 執行分析。 可供 SQL 和 Spark 分析使用的資源會組織成 SQL 和 Spark **集區**。 

## <a name="linked-services"></a>連結的服務

工作區可以包含任意數目的 **連結服務**，基本上是連接字串，用於定義工作區連線到外部資源所需的連線資訊。

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** 可在 Synapse 工作區中執行 T-SQL 型分析。 Synapse SQL 有兩種耗用模型：專用和無伺服器。  若為專用模型，請使用專用的 **SQL 集區**。 一個工作區可以具有任意數目的集區。 若要使用無伺服器模型，請使用 **無伺服器 SQL 集區**。 每個工作區都有其中一個集區。

在 Synapse Studio 中，您可以藉由建立及執行 **SQL 指令碼** 來使用 SQL 集區。

## <a name="apache-spark-for-synapse"></a>適用於 Synapse 的 Apache Spark

若要使用 Spark 分析，請在 Synapse 工作區中建立及使用 **無伺服器 Apache Spark 集區**。 當您開始使用 Spark 集區時，工作區會建立一個 **spark 工作階段**，用以處理與該工作階段相關聯的資源。 

在 Synapse 中有兩種方式可使用 Spark：
* **Spark Notebooks**，用來執行資料科學和工程，使用 Scala、PySpark、C# 和 SparkSQL
* **Spark 作業定義**，可讓您使用 jar 檔案執行批次 Spark 作業。

## <a name="pipelines"></a>Pipelines

管線是 Azure Synapse 提供資料整合的方式，可讓您在服務之間移動資料並協調活動。

* **管線** 是一起執行某個工作的活動邏輯群組。
* **活動** 會定義要在管線中對資料執行的動作，例如複製資料、執行 Notebook 或 SQL 指令碼。
* **資料流程** 是一種特定類型的活動，可針對使用 Synapse Spark 的資料轉換提供無程式碼的體驗。
* **觸發程序** - 執行管線。 可以手動執行或自動執行 (排程、輪轉視窗或以事件為基礎)
* **整合資料集** - 具名的資料檢視，只會指向或參考要在活動中作為輸入和輸出的資料。 其屬於連結服務。

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure Synapse Analytics](get-started.md)
* [建立工作區](quickstart-create-workspace.md)
* [使用無伺服器 SQL 集區](quickstart-sql-on-demand.md)

