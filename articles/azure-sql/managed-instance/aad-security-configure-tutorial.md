---
title: 使用 Azure AD 伺服器主體 (登入) 的 SQL 受控執行個體安全性
description: 了解可用來保護 Azure SQL 受控執行個體以及使用 Azure AD 伺服器主體 (登入) 的技術和功能
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 05103052308b6dbf1314348f7d45abc9cba79827
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/22/2020
ms.locfileid: "84706421"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>教學課程：使用 Azure AD 伺服器主體 (登入) 的 Azure SQL 受控執行個體中的安全性
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL 受控執行個體幾乎提供了最新的 SQL Server (Enterprise Edition) 資料庫引擎所具有的所有安全性功能：

- 在隔離的環境中限制存取
- 使用需要身分識別的驗證機制：Azure Active Directory (Azure AD) 和 SQL 驗證
- 使用授權搭配角色型成員資格和權限
- 啟用安全性功能

在本教學課程中，您會了解如何：

> [!div class="checklist"]
>
> - 為受控執行個體建立 Azure AD 伺服器主體 (登入)
> - 將權限授與受控執行個體中的 Azure AD 伺服器主體 (登入)
> - 從 Azure AD 伺服器主體 (登入) 建立 Azure AD 使用者
> - 指派權限給 Azure AD 使用者和受控資料庫安全性
> - 對 Azure AD 使用者使用模擬功能
> - 對 Azure AD 使用者使用跨資料庫查詢
> - 了解安全性功能，例如威脅防護、稽核、資料遮罩和加密

若要深入了解，請參閱 [Azure SQL 受控執行個體概觀](sql-managed-instance-paas-overview.md)。 

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列必要條件：

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- 受控執行個體
  - 遵循這篇文章：[快速入門：建立受控執行個體](instance-create-quickstart.md)
- 能夠存取受控執行個體並[已為受控執行個體佈建 Azure AD 系統管理員](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。 若要深入了解，請參閱：
  - [將您的應用程式連線至受控執行個體](connect-application-instance.md)
  - [SQL 受控執行個體連線架構](connectivity-architecture-overview.md)
  - [使用 SQL 設定及管理 Azure Active Directory 驗證](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>限制存取 

受控執行個體可透過私人 IP 位址存取。 與隔離的 SQL Server 環境非常類似，應用程式或使用者需要先有 SQL 受控執行個體網路 (VNet) 的存取權，才能建立連線。 如需詳細資訊，請參閱[將您的應用程式連線到SQL 受控執行個體](connect-application-instance.md)。

您也可以使用與 Azure SQL Database 相同的方式，在受控執行個體上設定服務端點，以允許公用連線。
如需詳細資訊，請參閱[在 Azure SQL 受控執行個體中設定公用端點](public-endpoint-configure.md)。

> [!NOTE]
> 即使已啟用服務端點，[Azure SQL Database 防火牆規則](../database/firewall-configure.md)仍不適用。 Azure SQL 受控執行個體有其自己的[內建防火牆](management-endpoint-verify-built-in-firewall.md)可用來管理連線。

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>使用 SSMS 建立 Azure AD 伺服器主體 (登入)

第一個 Azure AD 伺服器主體 (登入) 可以由作為 `sysadmin` 的標準 SQL 系統管理員帳戶 (非 Azure AD) 建立，或由佈建程式期間建立的受控執行個體 Azure AD 系統管理員來建立。 如需詳細資訊，請參閱[為 SQL 受控執行個體佈建 Azure Active Directory 系統管理員](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)。

請參閱下列文章來取得連線到 SQL 受控執行個體的範例：

- [快速入門：設定 Azure VM 以連線到 SQL 受控執行個體](connect-vm-instance-configure.md)
- [快速入門：設定從內部部署連線至 SQL 受控執行個體的點對站連線](point-to-site-p2s-configure.md)

1. 使用 [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms)，透過屬於 `sysadmin` 的標準 SQL 登入帳戶 (非 Azure AD) 或 SQL 受控執行個體的 Azure AD 系統管理員來登入受控執行個體。

2. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。

3. 在查詢視窗中，使用下列語法來為 Azure AD 帳戶建立本機登入：

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    此範例會為 nativeuser@aadsqlmi.onmicrosoft.com 帳戶建立登入。

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. 在工具列上，選取 [執行] 以建立登入。

5. 藉由執行下列 T-SQL 命令來檢查新增的登入：

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](./media/aad-security-configure-tutorial/native-login.png)

如需詳細資訊，請參閱 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current)。

## <a name="grant-permissions-to-create-logins"></a>授與建立登入的權限

若要建立其他 Azure AD 伺服器主體 (登入)，必須將 SQL Server 角色或權限授與主體 (SQL 或 Azure AD)。

### <a name="sql-authentication"></a>SQL 驗證

- 如果登入是 SQL 主體，只有屬於 `sysadmin` 角色一部分的登入可以使用 create 命令來為 Azure AD 帳戶建立登入。

### <a name="azure-ad-authentication"></a>Azure AD 驗證

- 若要讓新建立的 Azure AD 伺服器主體 (登入) 有能力為其他 Azure AD 使用者、群組或應用程式建立其他登入，請為登入授與 `sysadmin` 或 `securityadmin` 伺服器角色。
- 最少必須將 **ALTER ANY LOGIN** 權限授與 Azure AD 伺服器主體 (登入)，其才能建立其他 Azure AD 伺服器主體 (登入)。
- 根據預設，會為主要伺服器中新建的 Azure AD 伺服器主體 (登入) 授與的標準權限是：**CONNECT SQL** 和 **VIEW ANY DATABASE**。
- `sysadmin` 伺服器角色可以授與給受控執行個體中的多個 Azure AD 伺服器主體 (登入)。

若要將登入新增至 `sysadmin` 伺服器角色：

1. 再次登入受控執行個體，或使用現有連往 Azure AD 系統管理員或 SQL 主體 (屬於 `sysadmin`) 的連線。

1. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。

1. 使用下列 T-SQL 語法，為 Azure AD 伺服器主體 (登入) 授與 `sysadmin` 伺服器角色：

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    下列範例會`sysadmin`將伺服器角色授與給登入nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>使用 SSMS 建立其他 Azure AD 伺服器主體 (登入)

建立 Azure AD 伺服器主體 (登入) 並為其提供 `sysadmin` 權限之後，該登入就可以使用 **FROM EXTERNAL PROVIDER** 子句與 **CREATE LOGIN** 來建立其他登入。

1. 使用 SQL Server Management Studio，以 Azure AD 伺服器主體 (登入) 連線至受控執行個體。 輸入 SQL 受控執行個體的主機名稱。 若要在 SSMS 中驗證，使用 Azure AD 帳戶登入時會有三個選項可供選擇：

   - Active Directory - 通用，具 MFA 支援
   - Active Directory - 密碼
   - Active Directory - 整合式 </br>

     ![ssms-login-prompt.png](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     如需詳細資訊，請參閱[通用驗證 (多重要素驗證的 SSMS 支援)](../database/authentication-mfa-ssms-overview.md)。

1. 選取 [Active Directory - 通用，具 MFA 支援]。 此時會出現 Multi-Factor Authentication 登入視窗。 使用 Azure AD 密碼來登入。

    ![mfa-login-prompt.png](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. 在 SSMS 的 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。
1. 在查詢視窗中，使用下列語法來為另一個 Azure AD 帳戶建立登入：

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    這個範例會為 Azure AD 使用者 bob@aadsqlmi.net (其網域 aadsqlmi.net 會與 Azure AD aadsqlmi.onmicrosoft.com 網域同盟) 建立登入。

    執行下列 T-SQL 命令。 同盟的 Azure AD 帳戶是內部部署 Windows 登入和使用者在 SQL 受控執行個體的替代項目。

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. 使用 [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current) 語法在受控執行個體中建立資料庫。 此資料庫將會用來在下一節測試使用者登入。
    1. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。
    1. 在查詢視窗中，使用下列語法來建立名為 **MyMITestDB** 的資料庫。

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. 為 Azure AD 中的群組建立 SQL 受控執行個體登入。 該群組必須先存在於 Azure AD，您才能將登入新增到 SQL 受控執行個體。 請參閱[使用 Azure Active Directory 建立基本群組並新增成員](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 建立 mygroup 群組，並對此群組新增成員。

1. 在 SQL Server Management Studio 中開啟新的查詢視窗。

    這個範例假設 Azure AD 中有一個稱為 _mygroup_ 的群組。 執行下列命令：

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. 作為測試，請使用新建立的登入或群組來登入受控執行個體。 開啟連往受控執行個體的新連線，並在驗證時使用新的登入。
1. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後針對新連線選擇 [新增查詢]。
1. 執行下列命令，檢查新建立的 Azure AD 伺服器主體 (登入) 的伺服器權限：

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> 只有在新增為 Azure AD 群組的一部分時，才支援在 SQL 受控執行個體登入中使用 Azure AD 來賓使用者。 Azure AD 來賓使用者是受邀從另一個 Azure AD 執行個體加入受控執行個體所屬 Azure AD 執行個體的帳戶。 例如，joe@contoso.com (Azure AD 帳戶) 或 steve@outlook.com (Microsoft 帳戶) 可以新增至 Azure AD aadsqlmi 執行個體中的群組。 在將使用者新增至群組後，便能使用 **CREATE LOGIN** 語法在 SQL 受控執行個體的**主要**資料庫中，為該群組建立登入。 屬於此群組成員的來賓使用者可以使用其目前的登入 (例如 joe@contoso.com 或 steve@outlook.com) 連線至受控執行個體。

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>從 Azure AD 伺服器主體 (登入) 建立 Azure AD 使用者

對個別資料庫授權的方式，與 SQL 受控執行個體在 SQL Server 的資料庫中所進行的方式極為類似。 您可以從資料庫中的現有登入建立使用者，並對其提供該資料庫的權限或將其新增至資料庫角色。

現在，我們已經建立好名為 **MyMITestDB** 的資料庫，以及只有預設權限的登入，接下來我們要從該登入建立使用者。 目前，登入可以連線至受控執行個體，並看到所有資料庫，但不能與資料庫互動。 如果您使用具有預設權限的 Azure AD 帳戶來登入，並嘗試展開新建立的資料庫，則會看到下列錯誤：

![ssms-db-not-accessible.png](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

如需如何授與資料庫權限的詳細資訊，請參閱[資料庫引擎權限使用者入門](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions)。

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>建立 Azure AD 使用者並建立範例資料表

1. 使用 SQL Server Management Studio，以 `sysadmin` 帳戶登入您的受控執行個體。
1. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。
1. 在查詢視窗中，使用下列語法從 Azure AD 伺服器主體 (登入) 建立 Azure AD 使用者：

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    下列範例會從登入 bob@aadsqlmi.net: 建立使用者 bob@aadsqlmi.net

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. 此外也支援從群組形式的 Azure AD 伺服器主體 (登入) 建立 Azure AD 使用者。

    下列範例會為存在於 Azure AD 執行個體的 Azure AD 群組 _mygroup_ 建立登入。

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    所有屬於 *mygroup* 的使用者都可以存取 **MyMITestDB** 資料庫。

    > [!IMPORTANT]
    > 從 Azure AD 伺服器主體 (登入) 建立 **USER** 時，請指定與 **LOGIN** 中的 login_name 相同的 user_name。

    如需詳細資訊，請參閱 [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current)。

1. 在新的查詢視窗中，使用下列 T-SQL 命令建立測試資料表：

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. 使用所建立的使用者在 SSMS 中建立連線。 您會發現您無法看到 `sysadmin` 稍早所建立的資料表 **TestTable**。 我們必須對使用者提供讀取資料庫資料的權限。

1. 您可以藉由執行下列命令來檢查使用者目前擁有的權限：

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>將使用者新增至資料庫層級的角色

若要讓使用者能夠看到資料庫中的資料，我們可以提供[資料庫層級角色](/sql/relational-databases/security/authentication-access/database-level-roles)給使用者。

1. 使用 SQL Server Management Studio，以 `sysadmin` 帳戶登入您的受控執行個體。

1. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。

1. 使用下列 T-SQL 語法，對 Azure AD 使用者授與 `db_datareader` 資料庫角色：

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    下列範例會對使用者 bob@aadsqlmi.net 和群組 mygroup 提供 **MyMITestDB** 資料庫上的 `db_datareader` 權限：

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. 藉由執行下列命令，檢查資料庫中所建立的 Azure AD 使用者是否存在：

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. 使用已新增至 `db_datareader` 角色的使用者，建立連往受控執行個體的新連線。
1. 在 [物件總管] 中展開資料庫以查看資料表。

    ![ssms-test-table.png](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. 開啟新的查詢視窗，並執行下列 SELECT 陳述式：

    ```sql
    SELECT *
    FROM TestTable
    ```

    您能否查看資料表中的資料？ 您應該會看見系統傳回資料行。

    ![ssms-test-table-query.png](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>模擬 Azure AD 伺服器層級主體 (登入)

SQL 受控執行個體支援模擬 Azure AD 伺服器層級主體 (登入)。

### <a name="test-impersonation"></a>測試模擬

1. 使用 SQL Server Management Studio，以 `sysadmin` 帳戶登入您的受控執行個體。

1. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。

1. 在查詢視窗中，使用下列命令來建立新的預存程序：

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. 使用下列命令查看您在執行預存程序時所模擬的使用者是否為 **bob\@aadsqlmi.net**。

    ```sql
    Exec dbo.usp_Demo
    ```

1. 藉由使用 EXECUTE AS LOGIN 陳述式來測試模擬：

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> 只有屬於 `sysadmin` 角色一部分的 SQL 伺服器層級主體 (登入) 可以執行下列鎖定 Azure AD 主體的作業：
>
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="use-cross-database-queries"></a>使用跨資料庫查詢

系統支援讓具有 Azure AD 伺服器主體 (登入) 的 Azure AD 帳戶進行跨資料庫查詢。 若要使用 Azure AD 群組來測試跨資料庫查詢，我們需要建立另一個資料庫和資料表。 如果已有既存的資料庫和資料表，則可以略過建立步驟。

1. 使用 SQL Server Management Studio，以 `sysadmin` 帳戶登入您的受控執行個體。
1. 在 [物件總管] 中，以滑鼠右鍵按一下伺服器，然後選擇 [新增查詢]。
1. 在查詢視窗中，使用下列命令來建立名為 **MyMITestDB2** 的資料庫和名為 **TestTable2** 的資料表：

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. 在新的查詢視窗中，執行下列命令以在新的資料庫 **MyMITestDB2** 中建立使用者 mygroup，並對 mygroup 授與該資料庫的 SELECT 權限：

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. 使用 SQL Server Management Studio，以 Azure AD 群組 mygroup 成員的身分登入受控執行個體。 開啟新的查詢視窗，並執行跨資料庫 SELECT 陳述式：

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    您應該會看到來自 **TestTable2** 的資料表結果。

## <a name="additional-supported-scenarios"></a>其他支援的案例

- Azure AD 伺服器主體 (登入) 支援 SQL 代理程式管理和作業執行功能。
- Azure AD 伺服器主體 (登入) 可以執行資料庫備份和還原作業。
- [稽核](auditing-configure.md)與 Azure AD 伺服器主體 (登入) 和驗證事件相關的所有陳述式。
- 屬於 `sysadmin` 伺服器角色成員的 Azure AD 伺服器主體 (登入) 有專用管理員連線。
- 使用 [sqlcmd 公用程式](/sql/tools/sqlcmd-utility)和 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 工具時可支援 Azure AD 伺服器主體 (登入)。
- 來自 Azure AD 伺服器主體 (登入) 的登入事件支援登入觸發程序。
- Service Broker 和 DB 電子郵件可使用 Azure AD 伺服器主體 (登入) 來設定。

## <a name="next-steps"></a>後續步驟

### <a name="enable-security-features"></a>啟用安全性功能

請參閱 [SQL 受控執行個體功能](sql-managed-instance-paas-overview.md#security-features)一文，以取得完整的資料庫保護方式清單。 將會討論的安全性功能如下：

- [SQL 受控執行個體稽核](auditing-configure.md)
- [一律加密](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [威脅偵測](threat-detection-configure.md)
- [動態資料遮罩](/sql/relational-databases/security/dynamic-data-masking)
- [資料列層級安全性](/sql/relational-databases/security/row-level-security)
- [透明資料加密 (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL 受控執行個體功能

如需 SQL 受控執行個體功能的完整概觀，請參閱：

> [!div class="nextstepaction"]
> [SQL 受控執行個體功能](sql-managed-instance-paas-overview.md)
