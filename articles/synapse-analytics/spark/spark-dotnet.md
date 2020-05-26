---
title: 使用適用於 Apache Spark 的 .NET 搭配 Azure Synapse Analytics
description: 了解如何使用 .NET 和 Apache Spark 在 Azure Synapse Analytics 筆記本中進行批次處理、即時串流、機器學習及撰寫特定查詢。
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 3882352c7e1d484818a58d7bd4410cbd66bd6637
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587794"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>使用適用於 Apache Spark 的 .NET 搭配 Azure Synapse Analytics

[適用於 Apache Spark 的 .NET](https://dot.net/spark) 是 Spark 的免費開放原始碼和跨平台 .NET 支援。 適用於 Apache Spark 的 .NET 會為 Spark 提供 .NET 繫結，讓您可透過 C# 和 F# 存取 Spark API。 有了適用於 Apache Spark 的 .NET，您就能夠使用 .NET 來撰寫和執行 Spark 的使用者定義函式。 適用於 Spark 的 .NET API 可讓您存取所有可協助您分析資料的 Spark 層面，包括 Spark SQL 和結構化串流。

您可以透過 Spark 批次工作定義或互動式 Azure Synapse Analytics 筆記本，使用適用於 Apache Spark 的 .NET 來分析資料。 在本文中，您將了解如何使用這兩種技術來搭配 Azure Synapse 使用適用於 Apache Spark 的 .NET。

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>使用 Spark 作業定義提交批次工作

請瀏覽教學課程，了解如何使用 Azure Synapse Analytics 來[建立適用於 Synapse Spark 集區的 Apache Spark 作業定義](apache-spark-job-definitions.md)。 如果您尚未封裝您的應用程式來提交至 Azure Synapse，請完成下列步驟。

1. 執行下列命令來發佈您的應用程式。 請務必將 mySparkApp 取代為應用程式的路徑。

   **在 Windows 上：**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **在 Linux 上：**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 筆記本中適用於 Apache Spark 的 .NET

建立新的筆記本時，您可以選擇想要表達商務邏輯的語言核心。 我們有數種語言的核心支援，包括 C#。

若要在您的 Azure Synapse Analytics 筆記本中使用適用於Apache Spark 的 .NET，請選取 [.NET Spark (C#)] 作為核心，然後將筆記本連結到現有的 Spark 集區。

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 筆記本中適用於 Apache Spark 的 .NET 

筆記本是為適用於 Apache Spark 的 .NET 管線和案例建立原型的絕佳選項。 您可以快速且有效率地開始使用、了解、篩選、顯示及視覺化您的資料。 資料工程師、資料科學家、商務分析師和機器學習工程師都能夠透過共用的互動式文件共同作業。 您會看到資料探索的立即結果，而且可以在相同的筆記本中視覺化您的資料。

### <a name="how-to-use-notebooks"></a>如何使用筆記本

當您建立新的筆記本時，您可以選擇想要表達商務邏輯的語言核心。 我們有數種語言的核心支援，包括 C#。

若要在您的 Azure Synapse Analytics 筆記本中使用適用於Apache Spark 的 .NET，請選取 [.NET Spark (C#)] 作為核心，然後將筆記本連結到現有的 Spark 集區。

.NET Spark 筆記本會以 .NET 互動體驗為基礎，提供互動式的 C# 體驗，讓您能夠使用預先定義的 Spark 工作階段變數 `spark` 來直接使用適用於 Spark 的 .NET。

### <a name="sparknet-c-kernel-features"></a>Spark.NET C# 核心功能

當您在 Azure Synapse Analytics 筆記本中使用適用於 Apache Spark 的 .NET 時，您可以使用下列功能：

* 宣告式 HTML：使用 HTML 語法從您的儲存格產生輸出，例如標頭、項目符號清單，甚至是顯示影像。
* 簡單的 C# 陳述式 (例如指派、列印到主控台、擲回例外狀況等等)。
* 多行 C# 程式碼區塊 (例如 if 陳述式、foreach 迴圈、類別定義等等)。
* 存取標準 C# 程式庫 (例如 System、LINQ、Enumerable 等等)。
* 支援 [C# 8.0 語言功能](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* 'spark' 是預先定義的變數，可讓您存取 Apache Spark 工作階段。
* 支援定義[可以在 Apache Spark 內執行的 .NET 使用者定義函式](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)。
* 支援透過 `XPlot.Plotly` 程式庫使用不同圖表 (例如折線圖、橫條圖或長條圖) 和版面配置 (例如，單一、重疊等等)，來視覺化 Spark 作業的輸出。
* 能夠將 NuGet 套件包含在您的 C# 筆記本中。

## <a name="next-steps"></a>後續步驟

* [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET 互動](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)