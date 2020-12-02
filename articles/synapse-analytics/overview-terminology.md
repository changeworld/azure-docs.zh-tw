---
title: 術語 - Azure Synapse Analytics (工作區預覽)
description: 帶領使用者了解 Azure Synapse Analytics 的參考指南
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c58ee46a608ccdcbb01a082ee278d9e0f8a07f6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030674"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics 術語

[!INCLUDE [preview](includes/note-preview.md)]

本文件將引導您了解 Azure Synapse Analytics 的基本概念。

## <a name="basics"></a>基本

**Synapse 工作區** 是在 Azure 中進行雲端式企業分析的安全共同作業界限。 工作區會部署在特定區域中，並且具有相關聯的 ADLS Gen2 帳戶和檔案系統 (用於儲存暫存資料)。 工作區位在資源群組底下。

工作區可讓您使用 SQL 和 Apache Spark 執行分析。 可供 SQL 和 Spark 分析使用的資源會組織成 SQL 和 Spark **集區**。 

## <a name="linked-services"></a>連結的服務

工作區可以包含任意數目的 **連結服務**，基本上是連接字串，用於定義工作區連線到外部資源所需的連線資訊。

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** 可在 Synapse 工作區中執行 T-SQL 型分析。 Synapse SQL 有兩種耗用模型：專用和無伺服器。  若為專用模型，請使用專用的 **SQL 集區**。 一個工作區可以具有任意數目的集區。 若要使用無伺服器模型，請使用 **無伺服器 SQL 集區**。 每個工作區都有其中一個集區。

* **SQL 要求** - 例如透過專用 SQL 集區或無伺服器 SQL 集區執行查詢的作業。
* **SQL 指令碼** - 儲存在檔案中的 SQL 命令集。 SQL 指令碼可以包含一或多個 SQL 陳述式。 可以用來透過專用 SQL 集區或無伺服器 SQL 隨選執行 SQL 要求。

## <a name="apache-spark-for-synapse"></a>適用於 Synapse 的 Apache Spark

若要使用 Spark 分析，請在 Synapse 工作區中建立及使用 **無伺服器 Apache Spark 集區**。 當您開始使用 Spark 集區時，工作區會建立一個 **spark 工作階段**，用以處理與該工作階段相關聯的資源。 

在 Synapse 中有兩種方式可使用 Spark：
* **Spark Notebooks**，用來執行資料科學和工程，使用 Scala、PySpark、C# 和 SparkSQL
* **Spark 作業定義**，可讓您使用 jar 檔案執行批次 Spark 作業。

版本支援：
* Spark 2.4
* Python 3.6.1
* Scala 2.11.12
* .NET for Apache Spark 1.0
* Delta Lake 0.3。  

## <a name="pipelines"></a>Pipelines

* **資料整合** - 其功能可用來在各種來源之間內嵌資料，以及協調在工作區中或工作區外部執行的活動。
* **資料流程** - 提供完全視覺化的體驗，您不需要撰寫任何程式碼來進行巨量資料轉換。 所有最佳化和執行都會以無伺服器的方式處理。
* **管線** - 一起執行某個工作的活動邏輯群組。
* **活動** - 定義要對資料執行的動作，例如複製資料、執行 Notebook 或 SQL 指令碼。
* **觸發程序** - 執行管線。 可以手動執行或自動執行 (排程、輪轉視窗或以事件為基礎)
* **整合資料集** - 具名的資料檢視，只會指向或參考要在活動中作為輸入和輸出的資料。 其屬於連結服務。

## <a name="next-steps"></a>後續步驟

* [開始使用 Azure Synapse Analytics](get-started.md)
* [建立工作區](quickstart-create-workspace.md)
* [使用無伺服器 SQL 集區](quickstart-sql-on-demand.md)

