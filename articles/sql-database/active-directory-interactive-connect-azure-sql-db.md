---
title: ActiveDirectory 互動式連接到 SQL
description: C# 程式碼範例 (具有說明)，使用 SqlAuthenticationMethod.ActiveDirectoryInteractive 模式來連線到 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73692294"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>使用 Azure 多重要素驗證連接到 Azure SQL 資料庫

本文提供了連接到 Azure SQL 資料庫的 C# 程式。 該程式使用互動式模式身份驗證，支援[Azure 多重要素驗證](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)。

有關 SQL 工具的多重要素驗證支援的詳細資訊，請參閱[SQL 伺服器資料工具 （SSDT） 中的 Azure 活動目錄支援](https://docs.microsoft.com/sql/ssdt/azure-active-directory)。

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL 資料庫的多重要素驗證

從 .NET 框架版本 4.7.2 開始[`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod)，枚舉具有一`ActiveDirectoryInteractive`個新值： 。 在用戶端 C# 程式中，枚舉值指示系統使用 Azure 活動目錄 （Azure AD） 互動式模式，該模式支援多重要素驗證以連接到 Azure SQL 資料庫。 執行程式的使用者會看到下列對話方塊：

* 對話方塊會顯示 Azure AD 使用者名稱，並要求使用者密碼。

   如果使用者的域與 Azure AD 聯合，則不會出現此對話方塊，因為不需要密碼。

   如果 Azure AD 策略對使用者施加多重要素驗證，則顯示接下來的兩個對話方塊。

* 使用者首次通過多重要素驗證時，系統將顯示一個對話方塊，要求輸入手機號碼以向其傳送簡訊。 每個訊息會提供*驗證碼*，使用者必須在下一個對話方塊中輸入該驗證碼。

* 要求多因素身份驗證驗證碼的對話方塊，系統已發送到行動電話。

有關如何配置 Azure AD 以需要多重要素驗證的資訊，請參閱[在雲中開始使用 Azure 多重要素驗證](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)。

有關這些對話方塊的螢幕截圖，請參閱為[SQL 伺服器管理工作室和 Azure AD 配置多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

> [!TIP]
> 您可以使用[.NET API 瀏覽器工具頁搜索 .NET](https://docs.microsoft.com/dotnet/api/)框架 API。
>
> 您也可以直接使用[可選的"術語]&lt;搜索值&gt;參數進行搜索](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)。

## <a name="configure-your-c-application-in-the-azure-portal"></a>在 Azure 入口網站中設定您的 C# 應用程式

開始之前，您應已建立可用的 [Azure SQL Database 伺服器](sql-database-get-started-portal.md)。

### <a name="register-your-app-and-set-permissions"></a>註冊應用程式並設定權限

若要使用 Azure AD 驗證，您的 C# 程式必須註冊為 Azure AD 應用程式。 若要註冊應用程式，您必須是 Azure AD 管理員或受指派為 Azure AD「應用程式開發人員」** 角色的使用者。 有關分配角色的詳細資訊，請參閱[將管理員和非管理員角色分配給具有 Azure 活動目錄 的使用者](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

完成應用註冊將生成並顯示**應用程式 ID。** 您的程式必須包含此識別碼才能連線。

若要為應用程式註冊與設定必要權限，請遵循下列步驟：

1. 在 Azure 門戶中，選擇**Azure 活動目錄** > **應用註冊** > **"新註冊**"。

    ![應用程式註冊](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    創建應用註冊後，將生成並顯示**應用程式 ID**值。

    ![顯示的應用程式識別碼](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. 選擇**API 許可權** > **添加許可權**。

    ![已註冊應用程式的權限設定](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. 選擇**組織使用**>在搜索>中鍵入**Azure SQL 資料庫**的 API，然後選擇**Azure SQL 資料庫**。

    ![將存取權新增至 Azure SQL Database 的 API](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. 選擇 **"添加許可權** > **user_impersonation** > 委派**許可權**。

    ![將權限委派給 Azure SQL Database 的 API](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>為您的 SQL Database 伺服器設定 Azure AD 管理員

要運行 C# 程式，Azure SQL 伺服器管理員需要為 SQL 資料庫伺服器分配 Azure AD 管理員。 

在**SQL 伺服器**頁上，**選擇活動目錄管理員** > **集管理員**。

有關 Azure AD 管理員和 Azure SQL 資料庫使用者的詳細資訊，請參閱使用[SQL 資料庫 配置和管理 Azure 活動目錄身份驗證](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)中的螢幕截圖。

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>將非管理員使用者新增至特定資料庫 (選用)

SQL Database 伺服器的 Azure AD 管理員可以執行 C# 範例程式。 若 Azure AD 使用者在資料庫內，他們可以執行程式。 已經存在於資料庫並擁有 `ALTER ANY USER` 權限的 Azure AD SQL 管理員或 Azure AD 使用者可以新增使用者。

您可以使用 SQL[`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql)命令將使用者添加到資料庫中。 例如 `CREATE USER [<username>] FROM EXTERNAL PROVIDER`。

如需詳細資訊，請參閱[使用 Azure Active Directory 驗證向 SQL Database、受控執行個體或 SQL 資料倉儲進行驗證](sql-database-aad-authentication.md)。

## <a name="new-authentication-enum-value"></a>新的驗證列舉值

C# 示例依賴于[`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)命名空間。 對多重要素驗證特別感興趣的是枚舉`SqlAuthenticationMethod`，它具有以下值：

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   使用具有 Azure AD 使用者名的此值來實現多重要素驗證。 這個值是目前文章的焦點。 它通過顯示使用者密碼的對話方塊，然後用於多重要素驗證驗證（如果對此使用者強制實施多重要素驗證）來生成互動式體驗。 此值從 .NET Framework 4.7.2 版開始可供使用。

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  針對「同盟」** 帳戶使用此值。 針對同盟帳戶，Windows 網域已知使用者名稱。 此身份驗證方法不支援多重要素驗證。

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  針對要求 Azure AD 使用者名稱和密碼的驗證使用此值。 Azure SQL Database 會進行驗證。 此方法不支援多重要素驗證。

## <a name="set-c-parameter-values-from-the-azure-portal"></a>從 Azure 入口網站設定 C# 參數值

為了成功執行 C# 程式，您必須將適當的值指派給靜態欄位。 此處列出欄位和範例值， 還顯示的還有 Azure 門戶位置，您可以在其中獲取所需的值。

| 靜態欄位名稱 | 範例值 | 在 Azure 入口網站中的位置 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL 伺服器** > **按名稱篩選** |
| AzureAD_UserID | "使用者\@abc.onmicrosoft.com" | **Azure 活動目錄** > **使用者** > **新來賓使用者** |
| Initial_DatabaseName | "myDatabase" | **SQL 伺服器** > **SQL 資料庫** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure 活動目錄** > **應用註冊** > **按名稱** > **應用程式 ID**搜索 |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure 活動目錄** > **應用註冊** > **按名稱** > 搜索 *[應用註冊]* > **設置** > **重定向 URI**<br /><br />對於本文，任何有效值對於重定向 Uri 都很好，因為它不在此處使用。 |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>使用 SQL 伺服器管理工作室進行驗證

在運行 C# 程式之前，最好在 SQL 伺服器管理工作室 （SSMS） 中檢查您的設置和配置是否正確。 如此一來，任何 C# 程式失敗就會是原始程式碼的問題。

### <a name="verify-sql-database-firewall-ip-addresses"></a>驗證 SQL Database 防火牆 IP 位址

在您計畫執行 C# 程式的相同組建中，從相同的電腦執行 SSMS。 對於此測試，任何**身份驗證**模式都正常。 如果有任何指示表示資料庫伺服器防火牆不接受您的 IP 位址，請參閱 [Azure SQL Database 伺服器層級和資料庫層級防火牆規則](sql-database-firewall-configure.md)以取得說明。

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>驗證 Azure 活動目錄多重要素驗證

再次執行 SSMS，但是這次將**驗證**設為 **Active Directory - 通用 (具有 MFA 支援)**。 此選項需要 SSMS 17.5 或更新版本。

有關詳細資訊，請參閱為[SSMS 和 Azure AD 配置多重要素驗證](sql-database-ssms-mfa-authentication-configure.md)。

> [!NOTE]
> 如果您是資料庫中的來賓使用者，還需要為資料庫提供 Azure AD 功能變數名稱：選擇**選項** > **AD 功能變數名稱或租戶 ID**。 要在 Azure 門戶中查找功能變數名稱，請選擇**Azure 活動目錄** > **自訂功能變數名稱**。 在 C# 範例程式中，提供網域名稱並非必要。

## <a name="c-code-example"></a>C# 程式碼範例

C# 程式範例依賴 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 組件。

要安裝此包，請在視覺化工作室中，選擇 **"專案管理** > **NuGet 包**"。 搜尋並安裝 **Microsoft.IdentityModel.Clients.ActiveDirectory**。

這是 C# 原始程式碼的示例。

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

這是 C# 測試輸出的示例。

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL 資料庫仍然支援 PowerShell Azure 資源管理器模組，但所有後續開發都針對 Az.Sql 模組。 有關這些 Cmdlet，請參閱[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模組和 AzureRm 模組中命令的參數基本相同。

- [獲取 AzSqlServerActive目錄管理員](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
