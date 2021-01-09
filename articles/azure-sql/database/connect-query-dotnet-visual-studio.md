---
title: 使用 Visual Studio 搭配 .NET 和 C# 查詢
description: 使用 Visual Studio 建立連線到 Azure SQL Database 資料庫或 Azure SQL 受控執行個體的 C# 應用程式，然後執行查詢。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705195"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>快速入門：在 Visual Studio 使用 .NET 和 C# 連線和查詢資料庫
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

本快速入門說明如何在 Visual Studio 中使用 [.NET Framework](https://www.microsoft.com/net/) 與 C# 程式碼，利用 Transact-SQL 陳述式來查詢 Azure SQL 或 Synapse SQL 中的資料庫。

## <a name="prerequisites"></a>必要條件

若要完成本快速入門，您需要：

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community、Professional 或 Enterprise 版。
- 您可在其中執行查詢的資料庫。

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>建立查詢 Azure SQL Database 中資料庫的程式碼

1. 在 Visual Studio 中，建立新專案。 
   
1. 在 [新增專案]對話方塊中，選取 [Visual C#]、[主控台應用程式 (.NET Framework)]。
   
1. 輸入 *sqltest* 作為專案名稱，然後選取 [確定]。 隨即建立新專案。 
   
1. 選取 [專案] > [管理 NuGet 套件]。 
   
1. 在 [NuGet 套件管理員] 中，選取 [瀏覽] 索引標籤，然後搜尋並選取 **Microsoft.Data.SqlClient**。
   
1. 在 **Microsoft.Data.SqlClient** 頁面上，選取 [安裝]。 
   - 出現提示時，選取 [確定]以繼續安裝。 
   - 如果 [接受授權]視窗出現時，選取 [我接受]。
   
1. 當安裝完成時，您可以關閉 [NuGet 套件管理員]。 
   
1. 在程式碼編輯器中，以下列程式碼取代 **Program.cs** 內容。 將 `<your_server>`、`<your_username>`、`<your_password>` 及 `<your_database>` 取代為您的值。
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>執行程式碼

1. 若要執行應用程式，請選取 [偵錯] > [開始偵錯]，或在工具列上選取 [開始]，或按下 **F5**。
1. 確認已傳回資料庫名稱和定序，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- 了解如何對 Windows/Linux/macOS [使用 .NET Core 來連線及查詢 Azure SQL Database 資料庫](connect-query-dotnet-core.md)。  
- 了解 [使用命令列以開始使用在 Windows/Linux/macOS 上的 .NET Core](/dotnet/core/tutorials/using-with-xplat-cli)。
- 深入了解如何[使用 SSMS 設計您的第一個 Azure SQL Database 資料庫](design-first-database-tutorial.md)或[使用 .NET 設計您的第一個 Azure SQL Database 資料庫](design-first-database-csharp-tutorial.md)。
- 如需 .NET 的詳細資訊，請參閱 [.NET 文件](/dotnet/)。
- 重試邏輯範例：[使用 ADO.NET 復原連線到 Azure SQL][step-4-connect-resiliently-to-sql-with-ado-net-a78n]。


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
