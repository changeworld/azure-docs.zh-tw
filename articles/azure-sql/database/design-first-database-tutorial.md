---
title: 教學課程：使用 SSMS 設計您的第一個關聯式資料庫
description: 了解如何使用 SQL Server Management Studio 在 Azure SQL Database 中設計您的第一個關聯式資料庫。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.custom: sqldbrb=1
ms.openlocfilehash: 1e0ab1d6c1266b37dfcba461fbbdc373fc526783
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362158"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-using-ssms"></a>教學課程：使用 SSMS 在 Azure SQL Database 中設計關聯式資料庫
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


Azure SQL Database 是 Microsoft Cloud (Azure) 中的關聯式資料庫即服務 (DBaaS)。 在本教學課程中，您會了解如何使用 Azure 入口網站和 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)：

> [!div class="checklist"]
>
> - 使用 Azure 入口網站建立資料庫*
> - 使用 Azure 入口網站設定伺服器層級的 IP 防火牆規則
> - 使用 SSMS 連接到資料庫
> - 使用 SSMS 建立資料表
> - 使用 BCP 大量載入資料
> - 使用 SSMS 查詢資料

*如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

> [!TIP]
> 下列 Microsoft Learn 模組可協助您免費學習如何[開發和設定可查詢 Azure SQL Database 的 ASP.NET 應用程式](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/) (包括建立簡易資料庫)。
> [!NOTE]
> 基於本教學課程的目的，我們是使用 Azure SQL Database。 您也可以使用彈性集區中的集區資料庫或 SQL 受控執行個體。 如需連線至 SQL 受控執行個體，請參閱這些 SQL 受控執行個體快速入門：[快速入門：設定 Azure VM 以連線到 Azure SQL 受控執行個體](../managed-instance/connect-vm-instance-configure.md)和[快速入門：設定從內部部署連線至 Azure SQL 受控執行個體的點對站連線](../managed-instance/point-to-site-p2s-configure.md)。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您已安裝︰

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (最新版本)
- [BCP 和 SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (最新版本)

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="create-a-blank-database-in-azure-sql-database"></a>在 Azure SQL Database 中建立空白的資料庫

使用一組定義的計算和儲存體資源建立 Azure SQL Database 的資料庫。 此資料庫建立於 [Azure 資源群組](../../active-directory-b2c/overview.md)內，並使用[邏輯 SQL 伺服器](logical-servers.md)進行管理。

遵循以下步驟來建立空白資料庫。

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。
2. 在 [新增] 頁面上，選取 [Azure Marketplace] 區段中的 [資料庫]，然後按一下 [精選] 區段中的 [SQL Database]。

   ![建立空白資料庫](./media/design-first-database-tutorial/create-empty-database.png)

3. 使用下列資訊填寫 **SQL Database** 表單，如上圖所示︰

    | 設定       | 建議的值 | 描述 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **資料庫名稱** | *yourDatabase* | 如需有效的資料庫名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers)。 |
    | **訂用帳戶** | *yourSubscription*  | 如需訂用帳戶的詳細資訊，請參閱[訂用帳戶](https://account.windowsazure.com/Subscriptions)。 |
    | **資源群組** | *yourResourceGroup* | 如需有效的資源群組名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 |
    | **選取來源** | 空白資料庫 | 指定應建立空白資料庫。 |

4. 按一下 [伺服器] 來使用現有伺服器，或建立及設定新的伺服器。 選取現有的伺服器，或按一下 [建立新伺服器] 並且在 [新伺服器] 表單中填寫下列資訊︰

    | 設定       | 建議的值 | 描述 |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **伺服器名稱** | 任何全域唯一名稱 | 如需有效的伺服器名稱，請參閱[命名規則和限制](/azure/architecture/best-practices/resource-naming)。 |
    | **伺服器管理員登入** | 任何有效名稱 | 如需有效的登入名稱，請參閱[資料庫識別碼](/sql/relational-databases/databases/database-identifiers)。 |
    | **密碼** | 任何有效密碼 | 您的密碼至少要有 8 個字元，而且必須使用下列幾種字元的其中三種︰大寫字元、小寫字元、數字和非英數字元。 |
    | **位置** | 任何有效位置 | 如需區域的相關資訊，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。 |

    ![建立資料庫伺服器](./media/design-first-database-tutorial/create-database-server.png)

5. 按一下 [選取]。
6. 按一下 [定價層] 可指定服務層、DTU 或虛擬核心的數目，以及儲存體數量。 您可以瀏覽 DTU/虛擬核心數目的選項，以及可供您每個服務層級使用的儲存體。

    在選取服務層、DTU 或 vCore 數目和儲存體數量之後，按一下 [套用]。

7. 為空白資料庫輸入 [定序]\(在此教學課程中使用預設值)。 如需定序的詳細資訊，請參閱[定序](/sql/t-sql/statements/collations)。

8. 您現在已完成 **SQL Database** 表單，請按一下 [建立] 來佈建資料庫。 這個步驟可能需要幾分鐘的時間。

9. 在工具列上，按一下 [通知] 以監視部署程序。

   ![此螢幕擷取畫面顯示正在進行部署的 [通知] 功能表。](./media/design-first-database-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>建立伺服器層級 IP 防火牆規則

Azure SQL Database 會在伺服器層級建立 IP 防火牆。 此防火牆會防止外部應用程式和工具連線到伺服器及伺服器上的任何資料庫，除非防火牆規則允許其 IP 通過防火牆。 若要啟用對資料庫的外部連線，您必須先新增 IP 位址 (或 IP 位址範圍) 的 IP 防火牆規則新增。 依照下列步驟來建立[伺服器層級的 IP 防火牆規則](firewall-configure.md)。

> [!IMPORTANT]
> Azure SQL Database 會透過連接埠 1433 通訊。 如果您嘗試從公司網路連線到這項服務，您網路的防火牆可能不允許透過連接埠 1433 的輸出流量。 若情況如此，除非系統管理員開啟連接埠 1433，否則您無法連線至您的資料庫。

1. 部署完成後，從 Azure 入口網站的功能表中選取 [SQL 資料庫]，或從任何頁面搜尋並選取 [SQL 資料庫]。  

1. 在 [SQL 資料庫] 頁面上，選取 [您的資料庫]。 資料庫的概觀頁面隨即開啟，其中會顯示完整**伺服器名稱** (例如 `contosodatabaseserver01.database.windows.net`)，並提供進一步的組態選項。

   ![伺服器名稱](./media/design-first-database-tutorial/server-name.png)

1. 請複製此完整伺服器名稱，以便從 SQL Server Management Studio 連線到伺服器和資料庫。

1. 在工具列上按一下 [設定伺服器防火牆]。 伺服器的 [防火牆設定] 頁面會隨即開啟。

   ![伺服器層級 IP 防火牆規則](./media/design-first-database-tutorial/server-firewall-rule.png)

1. 按一下工具列上的 [新增用戶端 IP]，將目前的 IP 位址新增至新的 IP 防火牆規則。 IP 防火牆規則可以針對單一 IP 位址或 IP 位址範圍開啟連接埠 1433。

1. 按一下 [檔案] 。 系統便會為目前的 IP 位址建立伺服器層級 IP 防火牆規則，以便在伺服器上開啟連接埠 1433。

1. 依序按一下 [確定]，然後關閉 [防火牆設定] 頁面。

您的 IP 位址現在可以通過 IP 防火牆。 您現在可以使用 SQL Server Management Studio 或您選擇的其他工具來連線至您的資料庫。 務必使用先前建立的伺服器管理帳戶。

> [!IMPORTANT]
> 根據預設，已對所有 Azure 服務啟用透過 SQL Database IP 防火牆存取。 按一下此頁面上的 [關閉] 即可對所有 Azure 服務停用。

## <a name="connect-to-the-database"></a>連接至資料庫

使用 [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) 建立對資料庫的連線。

1. 開啟 SQL Server Management Studio。
2. 在 [連線至伺服器] 對話方塊中，輸入下列資訊：

   | 設定       | 建議的值 | 描述 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **伺服器類型** | 資料庫引擎 | 這是必要的值。 |
   | **伺服器名稱** | 完整伺服器名稱 | 例如，*yourserver.database.windows.net*。 |
   | **驗證** | SQL Server 驗證 | 在本教學課程中，我們只設定了 SQL 驗證這個驗證類型。 |
   | **登入** | 伺服器系統管理員帳戶 | 您在建立伺服器時所指定的帳戶。 |
   | **密碼** | 伺服器系統管理員帳戶的密碼 | 這是您在建立伺服器時所指定的密碼。 |

   ![連線至伺服器](./media/design-first-database-tutorial/connect.png)

3. 按一下 [連接到伺服器] 對話方塊中的 [選項]。 在 [連線到資料庫] 區段中，輸入 *yourDatabase* 以連線到這個資料庫。

    ![連線到伺服器上的 DB](./media/design-first-database-tutorial/options-connect-to-db.png)  

4. 按一下 [ **連接**]。 [物件總管] 視窗隨即在 SSMS 中開啟。

5. 在 [物件總管] 中，展開 [資料庫]，然後展開 *yourDatabase* 以檢視範例資料庫中的物件。

   ![資料庫物件](./media/design-first-database-tutorial/connected.png)  

## <a name="create-tables-in-your-database"></a>在資料庫中建立資料表

使用四個資料表建立資料庫結構描述，其會使用 [Transact-SQL](/sql/t-sql/language-reference) 建立大學的學生管理系統模型：

- 個人
- 課程
- 學生
- 功勞

下圖顯示這些資料表是如何彼此相互關聯。 在這當中有部分資料表會參考其他資料表的資料欄。 例如，Student 資料表會參考 Person 資料表的 PersonId 資料行。 研究圖表，以了解在本教學課程中資料表彼此關連的方式。 如需深入了解建立有效資料庫資料表的方式，請參閱[建立有效資料庫資料表 ](https://msdn.microsoft.com/library/cc505842.aspx)。 如需選擇資料類型的相關資訊，請參閱[資料類型 (英文)](/sql/t-sql/data-types/data-types-transact-sql)。

> [!NOTE]
> 您亦可使用 [SQL Server Management Studio 中的資料表設計工具](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools)，建立和設計您的資料表。

![資料表關聯性](./media/design-first-database-tutorial/tutorial-database-tables.png)

1. 在 [物件總管] 中，以滑鼠右鍵按一下 *yourDatabase*，然後選取 [新增查詢]。 隨即開啟已連線到您資料庫的空白查詢視窗。

2. 在查詢視窗中，執行下列查詢以在資料庫中建立四個資料表︰

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![建立資料表](./media/design-first-database-tutorial/create-tables.png)

3. 在 [物件總管] 中展開 *yourDatabase* 之下的 **Tables** 節點，以查看您建立的資料表。

   ![建立 ssms 資料表](./media/design-first-database-tutorial/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>將資料載入到資料表

1. 在 [下載] 資料夾中建立一個名為 *sampleData* 的資料夾，以存放您資料庫的範例資料。

2. 以滑鼠右鍵按一下下列連結，將其儲存至 *sampleData* 資料夾。

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. 開啟命令提示字元視窗，並瀏覽至 *sampleData* 資料夾。

4. 執行下列命令，將範例資料插入資料表中，並以適用於您環境的值取代 *server*、*database*、*user*, 和 *password* 的值。

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

您現在已將範例資料載入至先前建立的資料表。

## <a name="query-data"></a>查詢資料

執行下列查詢，以從資料庫資料表中擷取資訊。 如需深入了解關於撰寫 SQL 查詢的資訊，請參閱[撰寫 SQL 查詢](https://technet.microsoft.com/library/bb264565.aspx)。 第一個查詢會聯結所有四個資料表，以尋找由「Dominick Pope」授課且成績高於 75% 的學生。 第二個查詢會連結所有四個資料表，以尋找「Noe Coleman」曾註冊的課程。

1. 在 [SQL Server Management Studio] 查詢視窗中，執行下列查詢︰

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. 在查詢視窗中，執行下列查詢︰

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解許多基本資料庫工作。 您已了解如何︰

> [!div class="checklist"]
>
> - 使用 Azure 入口網站建立資料庫*
> - 使用 Azure 入口網站設定伺服器層級的 IP 防火牆規則
> - 使用 SSMS 連接到資料庫
> - 使用 SSMS 建立資料表
> - 使用 BCP 大量載入資料
> - 使用 SSMS 查詢資料

前進到下一個教學課程，了解如何使用 Visual Studio 和 C# 設計資料庫。

> [!div class="nextstepaction"]
> [使用 C# 和 ADO.NET 在 Azure SQL Database 中設計關聯式資料庫](design-first-database-csharp-tutorial.md)
