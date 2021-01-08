---
title: 使用 .NET 進行 Apache Spark
description: 了解如何使用 .NET 和 Apache Spark 在 Azure Synapse Analytics 筆記本中進行批次處理、即時串流、機器學習及撰寫特定查詢。
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 895d766cc1e70e6a001904770d6f3ef12b6945cd
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018656"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>使用適用於 Apache Spark 的 .NET 搭配 Azure Synapse Analytics

[適用于 Apache Spark 的 .net](https://dot.net/spark) 提供適用于 Spark 的免費 [開放原始](https://github.com/dotnet/spark)碼和跨平臺 .net 支援。 

它提供 Spark 的 .NET 系結，可讓您透過 c # 和 F # 存取 Spark Api。 使用 .NET 進行 Apache Spark，您也可以針對以 .NET 撰寫的 Spark 撰寫和執行使用者定義函數。 適用於 Spark 的 .NET API 可讓您存取所有可協助您分析資料的 Spark DataFrames 層面，包括 Spark SQL、Delta Lake 和結構化串流。

您可以透過 Spark 批次工作定義或互動式 Azure Synapse Analytics 筆記本，使用適用於 Apache Spark 的 .NET 來分析資料。 在本文中，您將了解如何使用這兩種技術來搭配 Azure Synapse 使用適用於 Apache Spark 的 .NET。

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>使用 Spark 作業定義提交批次工作

請瀏覽教學課程，了解如何使用 Azure Synapse Analytics 來[建立適用於 Synapse Spark 集區的 Apache Spark 作業定義](apache-spark-job-definitions.md)。 如果您尚未封裝您的應用程式以提交至 Azure Synapse，請完成下列步驟。

1. 執行下列命令來發佈您的應用程式。 請務必將 mySparkApp 取代為應用程式的路徑。

   **在 Windows 上：**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r win-x64
   ```
   
   **在 Linux 上：**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

2. 壓縮 [發佈] 資料夾的內容， `publish.zip` 例如，由於步驟1所建立的內容。 所有元件都應該在 ZIP 檔案的第一層，而且不應該有中繼資料夾層。 這表示當您解壓縮時 `publish.zip` ，所有元件都會解壓縮至您目前的工作目錄中。

    **在 Windows 上：**

    使用類似 [7-Zip](https://www.7-zip.org/) 或 [WinZip](https://www.winzip.com/)的解壓縮程式，將檔案解壓縮到 bin 目錄中，並包含所有已發佈的二進位檔。

    **在 Linux 上：**

    開啟 bash shell，並將所有已發佈的二進位檔放入 bin 目錄中，然後執行下列命令。

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>Azure Synapse Analytics 筆記本中適用於 Apache Spark 的 .NET 

筆記本是為適用於 Apache Spark 的 .NET 管線和案例建立原型的絕佳選項。 您可以快速且有效率地開始使用、了解、篩選、顯示及視覺化您的資料。 

資料工程師、資料科學家、商務分析師和機器學習工程師都能夠透過共用的互動式文件共同作業。 您會看到資料探索的立即結果，而且可以在相同的筆記本中視覺化您的資料。

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>如何使用適用於 Apache Spark 的 .NET 筆記本

當您建立新的筆記本時，您可以選擇想要表達商務邏輯的語言核心。 核心支援適用于多種語言，包括 c #。

若要在 Azure Synapse Analytics 筆記本中使用 .NET 進行 Apache Spark，請選取 **.Net Spark (c # )** 作為核心，然後將筆記本連接到現有的無伺服器 Apache Spark 集區。

.NET Spark 筆記本以 [.net 互動式](https://github.com/dotnet/interactive) 體驗為基礎，並提供互動式的 c # 體驗，讓您能夠使用已預先定義之 spark 會話變數的現成 .net 功能 `spark` 。

### <a name="install-nuget-packages-in-notebooks"></a>在筆記本中安裝 NuGet 套件

您可以在 `#r nuget` nuget 套件名稱之前使用魔術命令，將您選擇的 NuGet 套件安裝到您的筆記本中。 下圖顯示範例：

![顯示如何使用 #r 安裝 Spark .NET 筆記本 NuGet 套件的螢幕擷取畫面](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

若要深入瞭解如何在筆記本中使用 NuGet 套件，請參閱 [.Net 互動式檔](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md)集。

### <a name="net-for-apache-spark-c-kernel-features"></a>適用於 Apache Spark 的 .NET C# 核心功能

當您在 Azure Synapse Analytics 筆記本中使用適用於 Apache Spark 的 .NET 時，您可以使用下列功能：

* 宣告式 HTML：使用 HTML 語法從您的儲存格產生輸出，例如標頭、項目符號清單，甚至是顯示影像。
* 簡單的 C# 陳述式 (例如指派、列印到主控台、擲回例外狀況等等)。
* 多行 C# 程式碼區塊 (例如 if 陳述式、foreach 迴圈、類別定義等等)。
* 存取標準 C# 程式庫 (例如 System、LINQ、Enumerable 等等)。
* 支援 C# 8.0 語言功能。
* `spark` 作為預先定義的變數，讓您能夠存取 Apache Spark 會話。
* 支援定義[可以在 Apache Spark 內執行的 .NET 使用者定義函式](/dotnet/spark/how-to-guides/udf-guide)。 建議您 [在 .net 中撰寫和呼叫 udf 來 Apache Spark 的互動式環境](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) ，以瞭解如何在 .net 中使用 udf 來 Apache Spark 互動式體驗。
* 支援透過 `XPlot.Plotly` 程式庫使用不同圖表 (例如折線圖、橫條圖或長條圖) 和版面配置 (例如，單一、重疊等等)，來視覺化 Spark 作業的輸出。
* 能夠將 NuGet 套件包含在您的 C# 筆記本中。

## <a name="next-steps"></a>後續步驟

* [適用於 Apache Spark 的 .NET 文件](/dotnet/spark/)
* [適用于 Apache Spark 互動指南的 .NET](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET 互動](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
