---
title: 如何測試 Azure Data Lake Analytics 程式碼
description: 了解如何為 Azure Data Lake Analytics 的 U-SQL 指令碼和擴充 C# 程式碼新增測試案例。
services: data-lake-analytics
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: how-to
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: 44426598daf1808ef0aee233968b04d2dc7c165f
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/24/2020
ms.locfileid: "87129911"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>測試 Azure Data Lake Analytics 程式碼

Azure Data Lake 提供了[U-SQL](data-lake-analytics-u-sql-get-started.md)語言。 U-SQL 結合了宣告式 SQL 與命令式 c # 來處理任何規模的資料。 在本檔中，您將瞭解如何為 U-SQL 和擴充的 c # 使用者定義的運算子（UDO）程式碼建立測試案例。

## <a name="test-u-sql-scripts"></a>測試 U-SQL 指令碼

已針對可執行檔程式碼進行編譯和優化，以在 Azure 或您的本機電腦上執行此腳本。 編譯和最佳化程序會將整個 U-SQL 指令碼視為一體。 您不能對每個語句執行傳統的單元測試。 不過，只要使用 U-SQL 測試 SDK 和本機執行 SDK，您就能進行指令碼層級的測試。

### <a name="create-test-cases-for-u-sql-script"></a>為 U-SQL 指令碼建立測試案例

Azure Data Lake Tools for Visual Studio 可讓您建立 U-SQL 指令碼測試案例。

1. 在 [方案總管] 中以滑鼠右鍵按一下 U-SQL 指令碼，然後選取 [建立單元測試]****。

1. 建立新的測試專案，或將測試案例插入現有的測試專案中。

   ![Data Lake Tools for Visual Studio -- 建立 U-SQL 測試專案組態](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>管理測試資料來源

測試 U-SQL 指令碼時，您需要測試輸入檔案。 若要管理測試資料，請在**方案總管**中，以滑鼠右鍵按一下 [U-SQL] 專案，然後選取 [**屬性**]。 您可以在 [**測試資料來源**] 中輸入來源。

![Data Lake Tools for Visual Studio -- 設定專案測試資料來源](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

當您呼叫 `Initialize()` U-SQL 測試 SDK 中的介面時，系統會在測試專案的工作目錄下建立暫存本機資料根資料夾。 測試資料來源資料夾中的所有檔案和資料夾都會先複製到暫存本機資料根資料夾，然後再執行 U-SQL 腳本測試案例。 您可以利用分號分隔測試資料資料夾路徑，以新增其他測試資料來源資料夾。

### <a name="manage-the-database-environment-for-testing"></a>管理測試的資料庫環境

如果您的 U-SQL 腳本使用或查詢的是 U-SQL 資料庫物件，您必須先初始化資料庫環境，然後再執行 SQL-DMO 測試案例。 呼叫預存程式時，可能需要此方法。 U-SQL 測試 SDK 中的 `Initialize()` 介面可協助您將 U-SQL 專案參考的所有資料庫，部署至測試專案工作目錄中的暫存本機資料根資料夾。

如需如何管理 U-sql 專案之 U-SQL 資料庫專案參考的詳細資訊，請參閱[參考一個 u-sql 資料庫專案](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)。

### <a name="verify-test-results"></a>驗證測試結果

`Run()` 介面會傳回作業執行結果。 *0*表示成功， *1*表示失敗。 您也可以使用 C# 判斷提示函式來驗證輸出。

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中執行測試案例

U-SQL 指令碼測試專案建置於 C# 單元測試架構之上。 建立專案之後，請選取 [**測試**] [  >  **Windows**  >  **test Explorer**]。 您可以從 [**測試瀏覽器**] 執行測試案例。 或者，以滑鼠右鍵按一下單元測試中的 .cs 檔案，然後選取 [**執行測試**]。

## <a name="test-c-udos"></a>測試 C# UDO

### <a name="create-test-cases-for-c-udos"></a>為 C# UDO 建立測試案例

您可以使用 c # 單元測試架構來測試您的 c # 使用者定義運算子（Udo）。 在測試 UDO 時，需要準備對應的 **IRowset** 物件作為輸入。

有兩種方式可建立**IRowset**物件：

- 從檔案載入資料以建立**IRowset**：

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

- 使用資料集合中的資料來建立**IRowset**：

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

呼叫 UDO 函式後，您可以使用 C# 判斷提示函式，透過結構描述和資料列集值驗證來檢驗結果。 您可以將 **[U-SQL c # UDO 單元測試] 專案**新增至您的方案。 若要這麼做，請在 Visual Studio 中選取 [檔案] **> 新的 > 專案**。

### <a name="run-test-cases-in-visual-studio"></a>在 Visual Studio 中執行測試案例

建立專案之後，請選取 [**測試**] [  >  **Windows**  >  **test Explorer**]。 您可以從 [**測試瀏覽器**] 執行測試案例。 或者，以滑鼠右鍵按一下單元測試中的 .cs 檔案，然後選取 [**執行測試**]。

## <a name="run-test-cases-in-azure-pipelines"></a>在 Azure Pipelines 中執行測試案例<a name="run-test-cases-in-azure-devops"></a>

**U-SQL 指令碼測試專案**和 **C# UDO 測試專案**皆會繼承 C# 單元測試專案。 Azure Pipelines 中的[Visual Studio 測試](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts)工作可以執行這些測試案例。

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>在 Azure Pipelines 中執行 U-SQL 測試案例

若為 U-SQL 測試，請確定您已 `CPPSDK` 在組建電腦上載入，然後將 `CPPSDK` 路徑傳遞至 `USqlScriptTestRunner(cppSdkFolderFullPath: @"")` 。

#### <a name="what-is-cppsdk"></a>什麼是 CPPSDK？

CPPSDK 是包含 Microsoft Visual C++ 14 和 Windows SDK 10.0.10240.0 的套件。 此套件包含了 U-SQL 執行時間所需的環境。 您可以在 Azure Data Lake Tools for Visual Studio 安裝資料夾下取得此套件：

- 在 Visual Studio 2015 中，其位於 `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- 在 Visual Studio 2017 中，其位於 `C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- 針對 Visual Studio 2019，它位於`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>在 Azure Pipelines 組建代理程式中準備 CPPSDK

在 Azure Pipelines 中準備 CPPSDK 相依性最常見的方式如下：

1. 將包含 CPPSDK 程式庫的資料夾壓縮。

1. 將 .zip 檔案簽入您的來源控制系統中。 .Zip 檔案可確保您簽入 CPPSDK 資料夾下的所有程式庫，因此檔案不會被忽略 `.gitignore` 。

1. 在組建管線中解壓縮 .zip 檔案。

1. 指向 `USqlScriptTestRunner` 組建電腦上這個解壓縮的資料夾。

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>在 Azure Pipelines 中執行 c # UDO 測試案例

針對 c # UDO 測試，請務必參考下列元件（Udo 所需）。

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.Types
- Microsoft.Analytics.UnitTest

如果您透過 [Nuget 套件 Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) 參考組件，請務必在組建管線中新增 NuGet 還原工作。

## <a name="next-steps"></a>後續步驟

- [如何設定 Azure Data Lake Analytics 的 CI/CD 管線](data-lake-analytics-cicd-overview.md)
- [在本機電腦上執行 U-SQL 指令碼](data-lake-analytics-data-lake-tools-local-run.md)
- [使用 U-SQL database 專案來開發 U-SQL database](data-lake-analytics-data-lake-tools-develop-usql-database.md)
