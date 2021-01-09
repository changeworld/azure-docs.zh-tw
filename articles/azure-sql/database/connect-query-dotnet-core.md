---
title: 使用 .NET Core 連線並查詢資料
description: 此主題說明如何使用 .NET Core 來建立可連線到 Azure SQL Database 中資料庫或 Azure SQL 受控執行個體的程式，並使用 Transact-SQL 陳述式進行查詢。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705205"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>快速入門：使用 .NET Core (C#) 查詢資料庫
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

在本快速入門中，您將使用 [.NET Core](https://www.microsoft.com/net/) 和 C# 程式碼來連線到資料庫。 然後，您將使用 Transact-SQL 陳述式來查詢資料。

> [!TIP]
> 下列 Microsoft Learn 課程模組可協助您免費學習如何[開發和設定可查詢 Azure SQL Database 中資料庫的 ASP.NET 應用程式](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- 已安裝[適用於您作業系統的 .NET Core](https://www.microsoft.com/net/core)。
- 您可在其中執行查詢的資料庫。 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>建立新的 .NET Core 專案

1. 開啟命令提示字元，並建立名為 sqltest 的資料夾。 瀏覽至此資料夾並執行此命令。

    ```cmd
    dotnet new console
    ```

    此命令會建立新的應用程式專案檔案，包括最初的 C# 程式碼檔案 (**Program.cs**)、XML 組態檔 (**sqltest.csproj**)，以及所需的二進位檔。

2. 在文字編輯器中，開啟 **sqltest.csproj** 並在 `<Project>` 標記之間貼上以下 XML。 此 XML 會將 `System.Data.SqlClient` 新增為相依性。

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>插入程式碼以查詢 Azure SQL Database 中的資料庫

1. 在文字編輯器中開啟 **Program.cs**。

2. 使用以下程式碼取代內容，並為您的伺服器、資料庫、使用者名稱和密碼新增適當的值。

> [!NOTE]
> 若要使用 ADO.NET 連接字串，請用下面這一行取代程式碼中設定伺服器、資料庫、使用者名稱和密碼的那 4 行。 在字串中設定您的使用者名稱和密碼。
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行以下命令。

   ```cmd
   dotnet restore
   dotnet run
   ```

2. 確認是否傳回資料列。

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. 選擇 **Enter** 關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [使用命令列以開始使用在 Windows/Linux/macOS 上的 .NET Core](/dotnet/core/tutorials/using-with-xplat-cli)。
- 了解如何[使用 .NET Framework 和 Visual Studio，來連線及查詢 Azure SQL Database 或 Azure SQL 受控執行個體](connect-query-dotnet-visual-studio.md)。  
- 了解如何[使用 SSMS 設計您的第一個資料庫](design-first-database-tutorial.md)，或是[使用 C# 和 ADO.NET 設計及連線資料庫](design-first-database-csharp-tutorial.md)。
- 如需 .NET 的詳細資訊，請參閱 [.NET 文件](/dotnet/)。
