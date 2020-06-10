---
title: 使用 T-SQL 將 SQL Server Windows 使用者和群組遷移至 SQL 受控執行個體
description: 了解如何將 SQL Server 執行個體中的 Windows 使用者和群組遷移至 Azure SQL 受控執行個體
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: a050e38f037ee0ed2741cfa2e509e21fa9e1151d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310218"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>教學課程：使用 T-SQL DDL 語法，將 SQL Server 執行個體中的 Windows 使用者和群組遷移至 Azure SQL 受控執行個體
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> 在本文中，用來將使用者和群組遷移至 SQL 受控執行個體的語法處於**公開預覽**狀態。

本文會引導您使用 T-SQL 語法，完成將您 SQL Server 中的內部部署 Windows 使用者和群組遷移至 Azure SQL 受控執行個體的程序。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 建立 SQL Server 的登入
> - 建立遷移的測試資料庫
> - 建立登入、使用者和角色
> - 將您的資料庫備份及還原至 SQL 受控執行個體 (MI)
> - 使用 ALTER USER 語法，手動將使用者遷移至 MI
> - 以新的對應使用者測試驗證

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，必須符合下列必要條件：

- Windows 網域與 Azure Active Directory (Azure AD) 同盟。
- 可存取 Active Directory 以建立使用者/群組。
- 內部部署環境中的現有 SQL Server。
- 現有的 SQL 受控執行個體。 請參閱[快速入門：建立 SQL 受控執行個體](instance-create-quickstart.md)。
  - SQL 受控執行個體中的 `sysadmin` 必須用來建立 Azure AD 登入。
- [為 SQL 受控執行個體建立 Azure AD 管理員](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。
- 您可以連線至您網路內的 SQL 受控執行個體。 如需詳細資訊，請參閱下列文章：
  - [將您的應用程式連線到 Azure SQL 受控執行個體](connect-application-instance.md)
  - [快速入門：設定從內部部署連線至 Azure SQL 受控執行個體的點對站連線](point-to-site-p2s-configure.md)
  - [在 Azure SQL 受控執行個體中設定公用端點](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL 語法

以下 T-SQL DDL 語法可用來支援透過 Azure AD 驗證將 SQL Server 執行個體內 Windows 使用者和群組遷移至 SQL 受控執行個體的作業。

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>引數

_domainName_</br>
指定使用者的網域名稱。

_userName_</br>
指定在資料庫內識別的使用者名稱。

_= loginName\@domainName.com_</br>
將使用者重新對應至 Azure AD 登入

_groupName_</br>
指定在資料庫內識別的群組名稱。

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>第 1 部分：在 SQL Server 中為 Windows 使用者和群組建立登入

> [!IMPORTANT]
> 下列語法會在您的 SQL Server 中建立使用者和群組登入。 在執行下列語法之前，您必須先確定使用者和群組存在於您的 Active Directory (AD) 中。 </br> </br>
> 使用者：testUser1、testGroupUser </br>
> 群組：遷移 - testGroupUser 必須屬於 AD 中的遷移群組

下列範例會為網域 _aadsqlmi_ 下名為 _testUser1_ 的帳戶建立 SQL Server 中的登入。

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

建立此測試的資料庫。

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>第 2 部分：建立 Windows 使用者和群組，然後新增角色和權限

使用下列語法建立測試使用者。

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

檢查使用者權限：

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

建立角色，並將您的測試使用者指派給這個角色：

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

使用下列查詢顯示指派給特定角色的使用者名稱：

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

使用下列語法建立群組。 然後，將群組新增至角色 `db_owner`。

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

使用下列語法，建立測試資料表並新增一些資料：

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>第 3 部分：將個別使用者資料庫備份及還原至 SQL 受控執行個體

使用[使用備份和還原複製資料庫](/sql/relational-databases/databases/copy-databases-with-backup-and-restore)一文來建立遷移資料庫的備份，或使用下列語法建立：

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

請遵循我們的[快速入門：將資料庫還原到 SQL 受控執行個體](restore-sample-database-quickstart.md)。

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>第 4 部分：將使用者遷移至 SQL 受控執行個體

執行 ALTER USER 命令，以在 SQL 受控執行個體上完成遷移程序。

1. 使用 SQL 受控執行個體的 Azure AD 管理員帳戶登入您的 SQL 受控執行個體。 然後，使用下列語法，在 SQL 受控執行個體中建立您的 Azure AD 登入。 如需詳細資訊，請參閱[教學課程：Azure SQL Database 中使用 Azure AD 伺服器主體 (登入) 的 SQL 受控執行個體安全性](aad-security-configure-tutorial.md)。

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. 檢查遷移的資料庫、資料表和主體是否正確。

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. 使用 ALTER USER 語法，將內部部署使用者對應至 Azure AD 登入。

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. 使用 ALTER USER 語法，將內部部署群組對應至 Azure AD 登入。

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>第 5 部分：測試 Azure AD 使用者或群組驗證

使用先前使用 ALTER USER 語法對應至 Azure AD 登入的使用者，測試對 SQL 受控執行個體的驗證。

1. 使用您的 Azure SQL 受控執行個體訂用帳戶，以 `aadsqlmi\testUser1` 登入同盟 VM
1. 使用 SQL Server Management Studio (SSMS)，透過 **Active Directory 整合式**驗證登入您的 SQL 受控執行個體，以連線至資料庫 `migration`。
    1. 您也可以使用 testUser1@aadsqlmi.net 認證搭配 SSMS 選項 [Active Directory - 通用，具 MFA 支援] 進行登入。 不過，在此情況下，您無法使用單一登入機制，而必須輸入密碼。 您不需要使用同盟 VM 來登入 SQL 受控執行個體。
1. 作為角色成員 **SELECT** 的一份子，您可以從 `test` 資料表中選取

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

使用 Windows 群組 `migration` 的成員，對 SQL 受控執行個體進行驗證測試。 使用者 `aadsqlmi\testGroupUser` 在遷移前應已新增至群組 `migration`。

1. 使用您的 Azure SQL 受控執行個體訂用帳戶，以 `aadsqlmi\testGroupUser` 登入同盟 VM
1. 使用 SSMS 搭配 **Active Directory 整合式**驗證，連線到 Azure SQL 受控執行個體伺服器和資料庫 `migration`
    1. 您也可以使用 testGroupUser@aadsqlmi.net 認證搭配 SSMS 選項 [Active Directory - 通用，具 MFA 支援] 進行登入。 不過，在此情況下，您無法使用單一登入機制，而必須輸入密碼。 您不需要使用同盟 VM 來登入 SQL 受控執行個體。
1. 作為 `db_owner` 角色的一份子，您可以建立新的資料表。

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> 由於 Azure SQL Database 的已知設計問題，以群組成員身分執行的資料表陳述式建立作業將會失敗，並出現下列錯誤： </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> 目前的因應措施，是在上述情況下使用現有架構來建立資料表 <dbo.new>

## <a name="next-steps"></a>後續步驟

[教學課程：使用 DMS 將 SQL Server 離線遷移到 Azure SQL 受控執行個體](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
