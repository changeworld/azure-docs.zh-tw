---
title: 使用 Java 查詢資料庫
description: 說明如何使用 Java 來建立可連線到 Azure SQL 資料庫的程式，並使用 T-SQL 陳述式加以查詢。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768650"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>快速入門：使用 Java 查詢 Azure SQL 資料庫

在本快速入門中，您會使用 Java 來連線至 Azure SQL 資料庫，並使用 T-SQL 陳述式來查詢資料。

## <a name="prerequisites"></a>Prerequisites

- 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure SQL 資料庫](sql-database-single-database-get-started.md)
- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) 相關軟體

  # <a name="macos"></a>[macOS](#tab/macos)

  安裝 Homebrew 和 Java，然後使用**在 macOS 上使用 SQL Server 建立 Java 應用程式** 中的 **1.2** 和 [1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/) 步驟來安裝 Maven。

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  安裝 Java、Java 開發套件，然後使用**在 Ubuntu 上使用 SQL Server 建立 Java 應用程式** 中的 **1.2**、**1.3** 和 [1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/) 步驟來安裝 Maven。

  # <a name="windows"></a>[Windows](#tab/windows)

  安裝 Java，然後使用**在 Windows 上使用 SQL Server 建立 Java 應用程式** 中的 **1.2** 和 [1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/) 步驟來安裝 Maven。

  ---

> [!IMPORTANT]
> 本文中已撰寫的指令碼會使用 **Adventure Works** 資料庫。

> [!NOTE]
> 您也可以視情況選擇使用 Azure SQL 受控執行個體。
>
> 若要建立和設定，請使用 [Azure 入口網站](sql-database-managed-instance-get-started.md)、[PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) 或 [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)，然後設定[現場](sql-database-managed-instance-configure-p2s.md)或 [VM](sql-database-managed-instance-configure-vm.md) 連線能力。
>
> 若要載入資料，請參閱[使用 BACPAC 檔案還原](sql-database-import.md) (搭配 [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works))，或參閱[還原 Wide World Importers 資料庫](sql-database-managed-instance-get-started-restore.md)。

## <a name="get-sql-server-connection-information"></a>取得 SQL Server 連線資訊

取得連線到 Azure SQL 資料庫所需的連線資訊。 在後續程序中，您將需要完整的伺服器名稱或主機名稱、資料庫名稱和登入資訊。

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

2. 選取 [SQL 資料庫]  或開啟 [SQL 受控執行個體]  頁面。

3. 在 [概觀]  頁面上，針對單一資料庫檢閱 [伺服器名稱]  旁的完整伺服器名稱，若為受控執行個體，則檢閱 [主機]  旁的完整伺服器名稱。 若要複製伺服器名稱或主機名稱，請將滑鼠暫留在其上方，然後選取 [複製]  圖示。 

## <a name="create-the-project"></a>建立專案

1. 從命令提示字元，建立名為 sqltest  的新 Maven 專案。

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. 將資料夾切換到 *sqltest*，並使用您慣用的文字編輯器開啟 pom.xml  。 使用下列代碼將 **Microsoft JDBC Driver for SQL Server** 新增到專案的相依性。

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. 此外，在 pom.xml  中，將下列屬性新增至您的專案。 如果您沒有屬性區段，您可以將它新增在相依性後面。

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 儲存並關閉 pom.xml  。

## <a name="add-code-to-query-database"></a>新增程式碼以查詢資料庫

1. 您的 Maven 專案中應該已經有名為 App.java  的檔案，位於：

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. 開啟檔案，並以下列程式碼取代其內容。 然後，為您的伺服器、資料庫、使用者和密碼新增適當的值。

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > 程式碼範例會使用適用於 Azure SQL 的 **AdventureWorksLT** 範例資料庫。

## <a name="run-the-code"></a>執行程式碼

1. 在命令提示字元中，執行應用程式。

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. 請確認前 20 個資料列已傳回，然後關閉應用程式視窗。

## <a name="next-steps"></a>後續步驟

- [設計您的第一個 Azure SQL 資料庫](sql-database-design-first-database.md)  

- [Microsoft JDBC Driver for SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [回報問題/發問](https://github.com/microsoft/mssql-jdbc/issues)  
