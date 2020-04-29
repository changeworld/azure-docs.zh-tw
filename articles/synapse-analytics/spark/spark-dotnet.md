---
title: 使用 .NET for Apache Spark 搭配 Azure Synapse 分析
description: 瞭解如何使用 .NET 和 Apache Spark 來進行批次處理、即時串流、機器學習，以及在 Azure Synapse 分析筆記本中撰寫臨機操作查詢。
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430509"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>使用 .NET for Apache Spark 搭配 Azure Synapse 分析

適用于[Apache Spark 的 .net](https://dot.net/spark)為 Spark 的免費、開放原始碼和跨平臺 .net 支援。 適用于 Apache Spark 的 .NET 會提供 Spark 的 .NET 系結，可讓您透過 c # 和 F # 存取 Spark Api。 有了適用于 Apache Spark 的 .NET，您就能夠使用 .NET 來撰寫和執行 Spark 的使用者定義函式。 適用于 Spark 的 .NET Api 可讓您存取 Spark 的所有層面，以協助您分析資料，包括 Spark SQL 和結構化串流。

您可以透過 Spark 批次作業定義或互動式 Azure Synapse 分析筆記本，使用 .NET 分析資料以進行 Apache Spark。 在本文中，您將瞭解如何使用這兩種技術，將 .NET 用於 Apache Spark 搭配 Azure Synapse。 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>使用 Spark 作業定義提交批次作業

請流覽本教學課程，以瞭解如何使用 Azure Synapse 分析來[建立適用于 Synapse Spark 集區的 Apache Spark 作業定義](apache-spark-job-definitions.md)。 如果您尚未封裝您的應用程式以提交至 Azure Synapse，請完成下列步驟。

1. 執行下列命令以發佈您的應用程式。 請務必以您應用程式的路徑取代*mySparkApp* 。

   **在 Windows 上：**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **在 Linux 上：**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse 分析筆記本中適用于 Apache Spark 的 .NET

建立新的筆記本時，您可以選擇想要表達商務邏輯的語言核心。 有數種語言的核心支援，包括 c #。

若要在您的 Azure Synapse 分析筆記本中使用 .NET for Apache Spark，請選取 [ **.Net Spark （c #）** ] 做為核心，並將筆記本連結至現有的 Spark 集區。

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse 分析筆記本中適用于 Apache Spark 的 .NET 

筆記本是針對 Apache Spark 管線和案例建立 .NET 原型的絕佳選項。 您可以快速且有效率地開始使用、瞭解、篩選、顯示及視覺化您的資料。 資料工程師、資料科學家、商務分析師和機器學習工程師都能夠透過共用的互動式檔共同作業。 您會看到資料探索的立即結果，而且可以在相同的筆記本中視覺化您的資料。

### <a name="how-to-use-notebooks"></a>如何使用筆記本

當您建立新的筆記本時，請選擇您想要表達商務邏輯的語言核心。 有數種語言的核心支援，包括 c #。 

若要在您的 Azure Synapse 分析筆記本中使用 .NET for Apache Spark，請選取 [ **.Net Spark （c #）** ] 做為核心，並將筆記本連結至現有的 Spark 集區。 

.NET Spark 筆記本是以 .NET 互動體驗為基礎，提供互動式的 c # 體驗，讓您能夠使用現成可用的 Spark，並已預先定義 Spark 會話`spark`變數。

### <a name="sparknet-c-kernel-features"></a>Spark.NET c # 核心功能

當您在 Azure Synapse 分析筆記本中使用適用于 Apache Spark 的 .NET 時，可使用下列功能：

* 宣告式 HTML：使用 HTML 語法（例如標頭、項目符號清單，甚至是顯示影像），從您的儲存格產生輸出。
* 簡單的 c # 語句（例如指派、列印到主控台、擲回例外狀況等等）。
* 多行 c # 程式碼區塊（例如 if 語句、foreach 迴圈、類別定義等等）。
* 存取 standard c # 程式庫（例如 System、LINQ、可列舉值等等）。
* [C # 8.0 語言功能](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)的支援。
* ' spark ' 做為預先定義的變數，可讓您存取 Apache Spark 會話。
* 支援定義[可以在 Apache Spark 內執行的 .net 使用者定義函數](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)。
* 支援使用不同的`XPlot.Plotly`圖表（例如折線圖、橫條圖或長條圖）和版面配置（例如，單一、重迭等等），使用程式庫來視覺化 Spark 作業的輸出。
* 能夠將 NuGet 套件包含在您的 c # 筆記本中。

## <a name="next-steps"></a>後續步驟

* [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET 互動](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)