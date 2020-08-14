---
title: 使用 Azure Functions 執行資料庫清除工作
description: 使用 Azure Functions 排程可連接到 Azure SQL Database 以定期清除資料列的工作。
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/02/2019
ms.openlocfilehash: 0b5e255d7d108eb063ece4e5489a8762261a0bed
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207264"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>使用 Azure Functions 連接到 Azure SQL Database

本文說明如何使用 Azure Functions 建立連接至 Azure SQL Database 或 Azure SQL 受控執行個體的排程工作。 函式程式碼會清除資料庫中資料表中的資料列。 新的 c # 函數是根據 Visual Studio 2019 中預先定義的計時器觸發程式範本所建立。 若要支援此案例，您也必須在函式應用程式中設定資料庫連接字串以作為設定。 針對 Azure SQL 受控執行個體您必須 [啟用公用端點](../azure-sql/managed-instance/public-endpoint-configure.md) ，才能從 Azure Functions 連接。 此案例會對資料庫使用大量作業。 

如果這是您第一次使用 C# Functions，則您應該先閱讀 [Azure Functions C# 開發人員參考資料](functions-dotnet-class-library.md)。

## <a name="prerequisites"></a>必要條件

+ 完成 [使用 Visual Studio 建立您的第一個](functions-create-your-first-function-visual-studio.md) 函式一文中的步驟，以建立以2.x 版或更新版本的執行時間為目標的區域函式應用程式。 您還必須已將專案發佈至 Azure 中的函數應用程式。

+ 本文章將示範在 AdventureWorksLT 範例資料庫的 **SalesOrderHeader** 資料表中執行大量清除作業的 Transact-SQL 命令。 若要建立 AdventureWorksLT 範例資料庫，請完成 [使用 Azure 入口網站在 Azure SQL Database 中建立資料庫](../azure-sql/database/single-database-create-quickstart.md)一文中的步驟。

+ 在此快速入門中，您必須加入您所使用電腦的公用 IP 位址[伺服器層級防火牆規則](../azure-sql/database/firewall-create-server-level-portal-quickstart.md)。 需要此規則才能從您的本機電腦存取 SQL Database 實例。  

## <a name="get-connection-information"></a>取得連線資訊

當您 [使用 Azure 入口網站在 Azure SQL Database 中完成建立資料庫](../azure-sql/database/single-database-create-quickstart.md)時，您必須取得所建立資料庫的連接字串。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. 從左側功能表中選取 [SQL Database]****，然後選取 [SQL 資料庫]**** 頁面上的資料庫。

1. 選取 [設定]**** 下的 [連接字串]****，然後複製完整的 **ADO.NET** 連接字串。 針對 Azure SQL 受控執行個體複製公用端點的連接字串。

    ![複製 ADO.NET 連接字串。](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>設定連接字串

函式應用程式可在 Azure 中主控函式的執行。 在函數應用程式設定中儲存連接字串和其他機密資料是最佳安全性做法。 使用應用程式設定可避免意外洩露連接字串與您的程式碼。 您可以直接從 Visual Studio 存取函數應用程式的應用程式設定。

您必須先將應用程式發佈至 Azure。 如果您尚未這麼做，請[將您的函式應用程式發行至 Azure](functions-develop-vs.md#publish-to-azure)。

1. 在方案總管中，以滑鼠右鍵按一下函式應用程式專案，然後選擇 [**發佈**  >  **編輯 Azure App Service 設定**]。 在 [新增應用程式設定名稱]**** 中選取 [新增設定]****，輸入 `sqldb_connection`，然後選取 [確定]****。

    ![函數應用程式的應用程式設定。](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. 在新的 **sqldb_connection** 設定中，在 **Local** 欄位中貼上您在上一節中複製的連接字串，並將 `{your_username}` 和 `{your_password}` 預留位置取代為實際的值。 選取 [從本機插入值]****，將更新的值複製到 **Remote** 欄位中，然後選取 [確定]****。

    ![新增 SQL 連接字串設定。](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    連接字串會以加密方式儲存在 Azure 中 (**Remote**)。 若要避免遺漏祕密，應從原始檔控制中排除 local.settings.json 專案檔 (**Local**)，例如使用 .gitignore 檔案。

## <a name="add-the-sqlclient-package-to-the-project"></a>將 SqlClient 套件加入專案

您需要新增包含 SqlClient 程式庫的 NuGet 套件。 需要此資料存取程式庫才能連接到 SQL Database。

1. 在 Visual Studio 2019 中開啟您的本機函數應用程式專案。

1. 在 [方案總管] 中，於函數應用程式上按一下滑鼠右鍵，然後選擇 [管理 NuGet 套件]****。

1. 在 [瀏覽]**** 索引標籤上搜尋 ```System.Data.SqlClient```，並在找到後加以選取。

1. 在 **System.Data.SqlClient** 頁面中，選取版本 `4.5.1`，然後按一下 [安裝]****。

1. 當安裝完成時，檢閱所做的變更，然後按一下 [確定]**** 來關閉 [預覽]**** 視窗。

1. 如果 [授權接受] **** 視窗出現時，按一下 [我接受]****。

現在，您可以加入 C# 函數程式碼來連接到 SQL Database。

## <a name="add-a-timer-triggered-function"></a>新增計時器觸發函式

1. 在方案總管中，以滑鼠右鍵按一下函數應用程式專案，然後選擇 [**加入**  >  **新的 Azure 函數**]。

1. 選取 **Azure Functions** 範本後，將新的項目命名為 `DatabaseCleanup.cs`，然後選取 [新增]****。

1. 在 [新增 Azure 函數]**** 對話方塊中，選擇 [計時器觸發程序]****，然後選擇 [確定]****。 此對話方塊會建立計時器觸發函數的程式碼檔。

1. 在檔案頂端開啟新的程式碼，並加入下列 using 陳述式：

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. 使用下列程式碼來取代現有的 `Run` 函式：

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    此功能每 15 秒執行一次，以根據出貨日期更新 `Status` 資料行。 若要深入了解計時器觸發程序的相關詳細資訊，請參閱 [Azure Functions 的計時器觸發程序](functions-bindings-timer.md)。

1. 按 **F5** 鍵以啟動函數應用程式。 [Azure Functions Core Tools](functions-develop-local.md) 執行視窗會在 Visual Studio 背後開啟。

1. 在啟動之後的 15 秒，該函數就會執行。 觀察輸出並記下 **SalesOrderHeader** 資料表中更新的資料列數目。

    ![檢視函數記錄。](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    在第一次執行時，您應該更新 32 個資料列。 下列執行不更新任何資料列，除非您對 SalesOrderHeader 資料表資料進行變更，以便 `UPDATE` 陳述式選取更多資料列。

如果您打算[發佈此函數](functions-develop-vs.md#publish-to-azure)，請記住將 `TimerTrigger` 屬性變更為比每 15 秒更合理的 [cron 排程](functions-bindings-timer.md#ncrontab-expressions)。

## <a name="next-steps"></a>後續步驟

接下來，了解如何使用。 將 Functions 與 Logic Apps 搭配使用以與其他服務整合。

> [!div class="nextstepaction"]
> [建立與 Logic Apps 整合的函數](functions-twitter-email.md)

如需 Functions 的詳細資訊，請參閱下列文章：

+ [Azure Functions 開發人員參考](functions-reference.md)  
   可供程式設計人員撰寫函數程式碼及定義觸發程序和繫結時參考。
+ [測試 Azure Functions](functions-test-a-function.md)  
  說明可用於測試函式的各種工具和技巧。  
