---
title: 使用 .NET 進行與 Azure 突觸分析一起阿帕奇火花
description: 瞭解如何使用 .NET 和 Apache Spark 在 Azure Synapse 分析筆記本中執行批處理、即時流式處理、機器學習和編寫臨時查詢。
author: mamccrea
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: mamccrea
ms.reviewer: jrasnick
ms.openlocfilehash: 6943be033653d88b272817d2dcf58042aaaab616
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430509"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>使用 .NET 進行與 Azure 突觸分析一起阿帕奇火花

[阿帕奇 Spark 的 .NET](https://dot.net/spark)是免費、開源的,並且跨平臺 .NET 支援 Spark。 阿帕奇 Spark 的 .NET 為 Spark 提供了 .NET 綁定,允許您通過 C# 和 F# 訪問 Spark API。 使用 .NET 表示 Apache Spark,您可以使用 .NET 編寫和執行 Spark 的使用者定義的函數。 .NET Spark API 使您能夠訪問 Spark 的所有方面,説明您分析數據,包括 Spark SQL 和結構化流式處理。

您可以透過 Spark 批次處理作業定義或使用互動式 Azure 突觸分析筆記本使用 .NET 分析數據。 在本文中,您將瞭解如何使用這兩種技術將 .NET 用於 Apache Spark 和 Azure 突觸。 

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>使用 Spark 工作定義提交批次處理工作

存取本教學,瞭解如何使用 Azure 突觸分析[為 Synapse Spark 池建立 Apache Spark 作業定義](apache-spark-job-definitions.md)。 如果尚未打包應用以提交到 Azure 同步,請完成以下步驟。

1. 運行以下命令以發佈應用。 請務必將*mySparkApp*替換為應用的路徑。

   **在 Windows 上:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.0 -r ubuntu.16.04-x64
   ```

   **在 Linux 上:**

### <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure 突觸分析筆記本中用於 Apache Spark 的 NET

創建新筆記本時,您可以選擇要表達業務邏輯的語言內核。 對多種語言(包括 C#)有內核支援。

要在 Azure 同步分析筆記本中使用 .NET 用於 Apache Spark,請選擇 **.NET Spark (C#)** 作為內核,並將筆記本附加到現有的 Spark 池。

   ```bash
   zip -r publish.zip
   ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure 突觸分析筆記本中用於 Apache Spark 的 NET 

筆記本是為 Apache Spark 管道和方案原型製作 .NET 的絕佳選項。 您可以快速高效地開始使用、理解、篩選、顯示和可視化數據。 數據工程師、數據科學家、業務分析師和機器學習工程師都能夠通過共用的互動式文檔進行協作。 您會立即看到數據流覽的結果,並可以在同一筆記本中可視化數據。

### <a name="how-to-use-notebooks"></a>如何使用筆記本

創建新筆記本時,請選擇要表達業務邏輯的語言內核。 對多種語言(包括 C#)有內核支援。 

要在 Azure 同步分析筆記本中使用 .NET 用於 Apache Spark,請選擇 **.NET Spark (C#)** 作為內核,並將筆記本附加到現有的 Spark 池。 

.NET Spark 筆記本基於 .NET 互動式體驗,並提供互動式 C# 體驗,能夠使用 .NET 用於開箱即用的`spark`Spark,Spark 會話變數 已預定義。

### <a name="sparknet-c-kernel-features"></a>Spark.NET C# 核心功能

在 Azure 同步分析筆記本中使用 .NET 表示 Apache Spark 時,可以使用以下功能:

* 聲明性 HTML:使用 HTML 語法(如標頭、專案符號清單,甚至顯示圖像)從儲存格生成輸出。
* 簡單的 C# 語句(如分配、列印到主控台、引發異常等)。
* 多行 C# 代碼塊(如 if 語句、foreach 循環、類定義等)。
* 訪問標準 C# 函式庫(如系統、LINQ、枚舉等)。
* 支援[C# 8.0 語言功能](/dotnet/csharp/whats-new/csharp-8?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。
* "火花"作為預定義的變數,讓您訪問 Apache Spark 工作階段。
* 支援定義[可以在 Apache Spark 執行的 .NET 使用者定義的函數](https://github.com/dotnet/spark/blob/master/examples/Microsoft.Spark.CSharp.Examples/Sql)。
* 支援使用`XPlot.Plotly`庫使用不同的圖表(如線圖、條形圖或直方圖)和佈局(如單張、疊加等)可視化 Spark 作業的輸出。
* 能夠將 NuGet 包包含在 C# 筆記本中。

## <a name="next-steps"></a>後續步驟

* [阿帕奇火花文檔的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
* [.NET 互動](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)