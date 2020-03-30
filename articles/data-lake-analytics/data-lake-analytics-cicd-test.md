---
title: 如何測試 Azure Data Lake Analytics 程式碼
description: 了解如何為 Azure Data Lake Analytics 的 U-SQL 指令碼和擴充 C# 程式碼新增測試案例。
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913950"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>測試 Azure Data Lake Analytics 程式碼

Azure 資料湖提供[U-SQL](data-lake-analytics-u-sql-get-started.md)語言。 U-SQL 將聲明性 SQL 與命令性 C# 相結合，可在任何規模上處理資料。 在本文中，您將瞭解如何為 U-SQL 和擴展的 C# 使用者定義運算子 （UDO） 代碼創建測試案例。

## <a name="test-u-sql-scripts"></a>測試 U-SQL 指令碼

U-SQL 腳本已針對在 Azure 或本地電腦上運行的可執行代碼進行編譯和優化。 編譯和最佳化程序會將整個 U-SQL 指令碼視為一體。 不能對每個語句執行傳統的單元測試。 不過，只要使用 U-SQL 測試 SDK 和本機執行 SDK，您就能進行指令碼層級的測試。

### <a name="create-test-cases-for-u-sql-script"></a>為 U-SQL 指令碼建立測試案例

Azure Data Lake Tools for Visual Studio 可讓您建立 U-SQL 指令碼測試案例。

1. 在 [方案總管] 中以滑鼠右鍵按一下 U-SQL 指令碼，然後選取 [建立單元測試]****。

1. 建立新的測試專案，或將測試案例插入現有的測試專案中。

   ![Data Lake Tools for Visual Studio -- 建立 U-SQL 測試專案組態](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>管理測試資料來源

測試 U-SQL 指令碼時，您需要測試輸入檔案。 要管理測試資料，請在**解決方案資源管理器**中，按右鍵 U-SQL 專案，然後選擇**屬性**。 您可以在**測試資料來源**中輸入源。

![Data Lake Tools for Visual Studio -- 設定專案測試資料來源](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

在 U-SQL 測試 SDK 中調用`Initialize()`介面時，將創建一個臨時本地資料根資料夾，該資料夾位於測試專案的工作目錄下。 在運行 U-SQL 腳本測試案例之前，測試資料來源資料夾中的所有檔和資料夾都將複製到臨時本地資料根資料夾。 您可以利用分號分隔測試資料資料夾路徑，以新增其他測試資料來源資料夾。

### <a name="manage-the-database-environment-for-testing"></a>管理測試的資料庫環境

如果 U-SQL 腳本使用 U-SQL 資料庫物件或查詢 U-SQL 資料庫物件，則需要在運行 U-SQL 測試案例之前初始化資料庫環境。 調用預存程序時，此方法可能是必需的。 U-SQL 測試 SDK 中的 `Initialize()` 介面可協助您將 U-SQL 專案參考的所有資料庫，部署至測試專案工作目錄中的暫存本機資料根資料夾。

有關如何管理 U-SQL 專案的 U-SQL 資料庫專案引用的詳細資訊，請參閱引用[U-SQL 資料庫專案](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)。

### <a name="verify-test-results"></a>驗證測試結果

`Run()` 介面會傳回作業執行結果。 *0*表示成功 *，1*表示失敗。 您也可以使用 C# 判斷提示函式來驗證輸出。

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中執行測試案例

U-SQL 指令碼測試專案建置於 C# 單元測試架構之上。 生成專案後，選擇 **"測試** > **Windows** > **測試資源管理器**"。 可以從**測試資源管理器**運行測試案例。 或者，按右鍵單元測試中的 .cs 檔，然後選擇 **"運行測試**"。

## <a name="test-c-udos"></a>測試 C# UDO

### <a name="create-test-cases-for-c-udos"></a>為 C# UDO 建立測試案例

您可以使用 C# 單元測試框架來測試 C# 使用者定義的運算子 （UDO）。 在測試 UDO 時，需要準備對應的 **IRowset** 物件作為輸入。

創建**IRowset**物件的方法有兩種：

- 從檔載入資料以創建**IRowset**：

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- 使用資料收集中的資料創建**IRowset**：

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>驗證測試結果

呼叫 UDO 函式後，您可以使用 C# 判斷提示函式，透過結構描述和資料列集值驗證來檢驗結果。 您可以將**U-SQL C++ UDO 單元測試專案**添加到解決方案中。 為此，請在視覺化工作室**中選擇"檔>">專案**。

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中執行測試案例

生成專案後，選擇 **"測試** > **Windows** > **測試資源管理器**"。 可以從**測試資源管理器**運行測試案例。 或者，按右鍵單元測試中的 .cs 檔，然後選擇 **"運行測試**"。

## <a name="run-test-cases-in-azure-pipelines"></a>在 Azure 管道中運行測試案例<a name="run-test-cases-in-azure-devops"></a>

**U-SQL 指令碼測試專案**和 **C# UDO 測試專案**皆會繼承 C# 單元測試專案。 Azure 管道中的[視覺化工作室測試工作](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts)可以運行這些測試案例。

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>在 Azure 管道中運行 U-SQL 測試案例

對於 U-SQL 測試，請確保`CPPSDK`載入到生成電腦上，然後將`CPPSDK`路徑傳遞給`USqlScriptTestRunner(cppSdkFolderFullPath: @"")`。

#### <a name="what-is-cppsdk"></a>什麼是 CPPSDK？

CPPSDK 是包含 Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 的套件。 此包包括 U-SQL 運行時所需的環境。 您可以在 Azure Data Lake Tools for Visual Studio 安裝資料夾下取得此套件：

- 在 Visual Studio 2015 中，其位於 `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- 在 Visual Studio 2017 中，其位於 `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- 對於視覺工作室 2019， 它下`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>在 Azure 管道生成代理中準備 CPPSDK

在 Azure 管道中準備 CPPSDK 依賴項的最常見方法是：

1. 壓縮包含 CPPSDK 庫的資料夾。

1. 將 .zip 檔案簽入您的來源控制系統中。 .ZIP 檔案可確保簽入 CPPSDK 資料夾下的所有庫，以便檔不會因為`.gitignore`檔而被忽略。

1. 在組建管線中解壓縮 .zip 檔案。

1. 指向`USqlScriptTestRunner`生成電腦上的此解壓縮資料夾。

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>在 Azure 管道中運行 C# UDO 測試案例

對於 C# UDO 測試，請確保引用 UDO 所需的以下程式集。

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

如果您透過 [Nuget 套件 Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) 參考組件，請務必在組建管線中新增 NuGet 還原工作。

## <a name="next-steps"></a>後續步驟

- [如何設定 Azure Data Lake Analytics 的 CI/CD 管線](data-lake-analytics-cicd-overview.md)
- [在本機電腦上執行 U-SQL 指令碼](data-lake-analytics-data-lake-tools-local-run.md)
- [使用 U-SQL 資料庫專案開發 U-SQL 資料庫](data-lake-analytics-data-lake-tools-develop-usql-database.md)
