---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: 7943ea13d93fa68b76d333bda5438fb5738f5cd1
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050376"
---
## <a name="c-program-example"></a>C# 程式範例

本文的下一節會呈現使用 ADO.NET 將 TRANSACT-SQL (T-SQL) 陳述式傳送到 SQL Database 的 C# 程式。 C# 程式會示範下列動作：

- [使用 ADO.NET 連線至 SQL Database](#cs_1_connect)
- [可傳回 T-SQL 陳述式的方法](#cs_2_return)
    - 建立資料表
    - 在資料表中填入資料
    - 更新、刪除及選取資料
- [將 T-SQL 提交至資料庫](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>實體關聯圖 (ERD)

`CREATE TABLE` 陳述式包含 **REFERENCES** 關鍵字，可建立兩個資料表之間的「外部索引鍵」(FK) 關聯性。 如果您使用 tempdb，請使用一對前置破折號將 `--REFERENCES` 關鍵字註解化。

ERD 會顯示兩個資料表之間的關聯性。 **tabEmployee.DepartmentCode**「子」資料行中的值受限於 **tabDepartment.DepartmentCode**「父」資料行中的值。

![顯示外部索引鍵的 ERD](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> 您可以選擇編輯 T-SQL，將前置 `#` 新增至資料表名稱，以將其建立為 tempdb 中的暫存資料表。 沒有測試資料庫可供使用時，這很適合用於示範。 在外部索引鍵使用期間不會強制執行任何參考，而在程式完成執行之後，暫存資料表會在連線關閉時自動刪除。

### <a name="to-compile-and-run"></a>編譯和執行

C# 程式在邏輯上是一個 .cs 檔案，而實際上會分成數個程式碼區塊，讓您更容易了解每個區塊。 若要編譯及執行此程式，請執行下列步驟：

1. 在 Visual Studio 中建立 C# 專案。 專案類型應該是「主控台」，可在 [範本] > [Visual C#] > [Windows 桌面] > [主控台應用程式 (.NET Framework)] 下找到。

1. 在 Program.cs 檔案中，利用下列步驟取代程式碼的起始行：

    1. 複製並貼上下列程式碼區塊 (以其出現的相同順序)，請參閱[連線到資料庫](#cs_1_connect)、[產生 T-SQL](#cs_2_return)，以及[提交至資料庫](#cs_3_submit)。

    1. 變更 `Main` 方法中的下列值：

        - *cb.DataSource*
        - *cb.UserID*
        - *cb.Password*
        - *cb.InitialCatalog*

1. 確認已參照 *System.Data.dll* 組件。 若要確認，請展開 [方案總管] 窗格中的 [參考] 節點。

1. 若要從 Visual Studio 建置及執行程式，請選取 [啟動] 按鈕。 報告輸出會顯示在程式視窗中，而 GUID 值會隨著測試回合有所不同。

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"/>

### <a name="connect-to-sql-database-using-adonet"></a>使用 ADO.NET 連線至 SQL Database

```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
    class Program
    {
        static void Main(string[] args)
        {
            try
            {
                var cb = new SqlConnectionStringBuilder();
                cb.DataSource = "your_server.database.windows.net";
                cb.UserID = "your_user";
                cb.Password = "your_password";
                cb.InitialCatalog = "your_database";

                using (var connection = new SqlConnection(cb.ConnectionString))
                {
                    connection.Open();

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
                        "@csharpParmDepartmentName", "Legal");

                    Submit_6_Tsql_SelectEmployees(connection);
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }

            Console.WriteLine("View the report output here, then press any key to end the program...");
            Console.ReadKey();
        }
```

<a name="cs_2_return"/>

### <a name="methods-that-return-t-sql-statements"></a>可傳回 T-SQL 陳述式的方法

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

static string Build_4_Tsql_UpdateJoin()
{
    return @"
        DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';

        -- Promote everyone in one department (see @parm...).
        UPDATE empl
        SET
            empl.EmployeeLevel += 1
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName1;
    ";
}

static string Build_5_Tsql_DeleteJoin()
{
    return @"
        DECLARE @DName2  nvarchar(128);
        SET @DName2 = @csharpParmDepartmentName;  --'Legal';

        -- Right size the Legal department.
        DELETE empl
        FROM
            tabEmployee   as empl
        INNER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        WHERE
            dept.DepartmentName = @DName2

        -- Disband the Legal department.
        DELETE tabDepartment
            WHERE DepartmentName = @DName2;
    ";
}

static string Build_6_Tsql_SelectEmployees()
{
    return @"
        -- Look at all the final Employees.
        SELECT
            empl.EmployeeGuid,
            empl.EmployeeName,
            empl.EmployeeLevel,
            empl.DepartmentCode,
            dept.DepartmentName
        FROM
            tabEmployee   as empl
        LEFT OUTER JOIN
            tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
        ORDER BY
            EmployeeName;
    ";
}
```

<a name="cs_3_submit"/>

### <a name="submit-t-sql-to-the-database"></a>將 T-SQL 提交至資料庫

```csharp
static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("Now, SelectEmployees (6)...");

    string tsql = Build_6_Tsql_SelectEmployees();

    using (var command = new SqlCommand(tsql, connection))
    {
        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                    reader.GetGuid(0),
                    reader.GetString(1),
                    reader.GetInt32(2),
                    (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                    (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
            }
        }
    }
}

static void Submit_Tsql_NonQuery(
    SqlConnection connection,
    string tsqlPurpose,
    string tsqlSourceCode,
    string parameterName = null,
    string parameterValue = null
    )
{
    Console.WriteLine();
    Console.WriteLine("=================================");
    Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

    using (var command = new SqlCommand(tsqlSourceCode, connection))
    {
        if (parameterName != null)
        {
            command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                parameterName,
                parameterValue);
        }
        int rowsAffected = command.ExecuteNonQuery();
        Console.WriteLine(rowsAffected + " = rows affected.");
    }
}
} // EndOfClass
}
```