---
title: ActiveDirectoryInteractive 連接到 SQL
description: C# 程式碼範例 (具有說明)，使用 SqlAuthenticationMethod.ActiveDirectoryInteractive 模式來連線到 Azure SQL Database。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: vanto
ms.date: 04/23/2020
ms.openlocfilehash: bef6e6c5ef795c192a846700fc046aa20274502d
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673413"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>使用 Azure Multi-Factor Authentication 連接到 Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文提供連接到 Azure SQL Database 的 c # 程式。 此程式會使用支援 [Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)的互動式模式驗證。

如需有關 SQL 工具 Multi-Factor Authentication 支援的詳細資訊，請參閱 [SQL Server Data Tools (SSDT) 中的 Azure Active Directory 支援 ](/sql/ssdt/azure-active-directory)。

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL Database 的 Multi-Factor Authentication

從 .NET Framework 版本4.7.2 開始，列舉 [`SqlAuthenticationMethod`](/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) 具有新的值： `ActiveDirectoryInteractive` 。 在用戶端 c # 程式中，列舉值會指示系統使用 Azure Active Directory (Azure AD) 互動模式，該模式支援 Multi-Factor Authentication 連接至 Azure SQL Database。 執行程式的使用者會看到下列對話方塊：

* 對話方塊會顯示 Azure AD 使用者名稱，並要求使用者密碼。

   如果使用者的網域與 Azure AD 同盟，這個對話方塊就不會出現，因為不需要密碼。

   如果 Azure AD 原則強加于使用者 Multi-Factor Authentication，則會顯示接下來的兩個對話方塊。

* 使用者第一次進行 Multi-Factor Authentication 時，系統會顯示一個對話方塊，要求您提供行動電話號碼來傳送文字訊息。 每個訊息會提供 *驗證碼* ，使用者必須在下一個對話方塊中輸入該驗證碼。

* 此對話方塊會要求系統傳送到行動電話的 Multi-Factor Authentication 驗證碼。

如需如何將 Azure AD 設定為需要 Multi-Factor Authentication 的相關資訊，請參閱 [在雲端中開始使用 Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)。

如需這些對話方塊的螢幕擷取畫面，請參閱 [設定 SQL Server Management Studio 和 Azure AD 的多重要素驗證](authentication-mfa-ssms-configure.md)。

> [!TIP]
> 您可以使用 [.NET API 瀏覽器工具頁面](/dotnet/api/)來搜尋 .NET Framework api。
>
> 您也可以直接使用選擇性的 [？ term = &lt; 搜尋值 &gt; 參數](/dotnet/api/?term=SqlAuthenticationMethod)進行搜尋。

## <a name="configure-your-c-application-in-the-azure-portal"></a>在 Azure 入口網站中設定您的 C# 應用程式

開始之前，您應該先建立並使用 [邏輯 SQL server](logical-servers.md) 。

### <a name="register-your-app-and-set-permissions"></a>註冊應用程式並設定權限

若要使用 Azure AD 驗證，您的 C# 程式必須註冊為 Azure AD 應用程式。 若要註冊應用程式，您必須是 Azure AD 管理員或受指派為 Azure AD「應用程式開發人員」  角色的使用者。 如需指派角色的詳細資訊，請參閱 [使用 Azure Active Directory 將系統管理員和非系統管理員角色指派給使用者](../../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

完成應用程式註冊會產生並顯示 **應用程式識別碼** 。 您的程式必須包含此識別碼才能連線。

若要為應用程式註冊與設定必要權限，請遵循下列步驟：

1. 在 [Azure 入口網站中，選取 [ **Azure Active Directory**  >  **應用程式註冊**  >  **新註冊** ]。

    ![應用程式註冊](./media/active-directory-interactive-connect-azure-sql-db/image1.png)

    建立應用程式註冊之後，就會產生並顯示 **應用程式識別碼** 值。

    ![顯示的應用程式識別碼](./media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. 選取 [API 權限]  >  [新增權限]。

    ![已註冊應用程式的權限設定](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. 選取 [ **我的組織使用的 api** ] > 輸入 **Azure SQL Database** 到搜尋 > 並選取 [ **Azure SQL Database** ]。

    ![將存取權新增至 Azure SQL Database 的 API](./media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. 選取 **委派許可權**  >  **user_impersonation**  >  **新增許可權** 。

    ![將權限委派給 Azure SQL Database 的 API](./media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-server"></a>為您的伺服器設定 Azure AD 管理員

若要執行 c # 程式， [邏輯 SQL server](logical-servers.md) 系統管理員必須為您的伺服器指派 Azure AD 系統管理員。

在 [ **SQL server** ] 頁面上，選取 [ **Active Directory 管理員**  >  **設定管理員** ]。

如需 Azure SQL Database Azure AD 系統管理員和使用者的詳細資訊，請參閱 [使用 SQL Database 設定和管理 Azure Active Directory 驗證](authentication-aad-configure.md#provision-azure-ad-admin-sql-database)中的螢幕擷取畫面。

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>將非管理員使用者新增至特定資料庫 (選用)

[邏輯 SQL server](logical-servers.md)的 Azure AD 管理員可以執行 c # 範例程式。 若 Azure AD 使用者在資料庫內，他們可以執行程式。 已經存在於資料庫並擁有 `ALTER ANY USER` 權限的 Azure AD SQL 管理員或 Azure AD 使用者可以新增使用者。

您可以使用 SQL 命令將使用者新增至資料庫 [`Create User`](/sql/t-sql/statements/create-user-transact-sql) 。 例如 `CREATE USER [<username>] FROM EXTERNAL PROVIDER`。

如需詳細資訊，請參閱 [使用 Azure Active Directory 驗證，以 SQL Database、受控執行個體或 Azure Synapse Analytics 進行驗證](authentication-aad-overview.md)。

## <a name="new-authentication-enum-value"></a>新的驗證列舉值

C # 範例依賴 [`System.Data.SqlClient`](/dotnet/api/system.data.sqlclient) 命名空間。 Multi-Factor Authentication 的特殊興趣是列舉 `SqlAuthenticationMethod` ，其具有下列值：

* `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   使用此值搭配 Azure AD 的使用者名稱，以執行 Multi-Factor Authentication。 這個值是目前文章的焦點。 它會藉由顯示使用者密碼的對話方塊來產生互動式體驗，然後在此使用者將 Multi-Factor Authentication 設定為 Multi-Factor Authentication 驗證。 此值從 .NET Framework 4.7.2 版開始可供使用。

* `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  針對「同盟」  帳戶使用此值。 針對同盟帳戶，Windows 網域已知使用者名稱。 此驗證方法不支援 Multi-Factor Authentication。

* `SqlAuthenticationMethod.ActiveDirectoryPassword`

  針對要求 Azure AD 使用者名稱和密碼的驗證使用此值。 Azure SQL Database 會進行驗證。 這個方法不支援 Multi-Factor Authentication。

> [!NOTE]
> 如果您使用 .NET Core，您會想要使用 [SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) 命名空間。 如需詳細資訊，請參閱下列 [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)。

## <a name="set-c-parameter-values-from-the-azure-portal"></a>從 Azure 入口網站設定 C# 參數值

為了成功執行 C# 程式，您必須將適當的值指派給靜態欄位。 此處列出欄位和範例值， 此外，也會顯示您可以取得所需值的 Azure 入口網站位置。

| 靜態欄位名稱 | 範例值 | 在 Azure 入口網站中的位置 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL server**  > **依名稱篩選** |
| AzureAD_UserID | "auser \@ abc.onmicrosoft.com" | **Azure Active Directory**  > **使用者**  > **新增來賓使用者** |
| Initial_DatabaseName | "myDatabase" | **SQL server**  > **SQL 資料庫** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory**  > **應用程式註冊**  > **依名稱搜尋**  > **應用程式識別碼** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory**  > **應用程式註冊**  > **依名稱搜尋**  > *[您的應用程式註冊]*  > **設定**  > **RedirectURIs**<br /><br />在本文中，任何有效的值都適用于 RedirectUri，因為此處未使用。 |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>使用 SQL Server Management Studio 驗證

在您執行 c # 程式之前，最好先確認 SQL Server Management Studio (SSMS) 中的設定和設定是否正確。 如此一來，任何 C# 程式失敗就會是原始程式碼的問題。

### <a name="verify-server-level-firewall-ip-addresses"></a>確認伺服器層級防火牆 IP 位址

在您計畫執行 C# 程式的相同組建中，從相同的電腦執行 SSMS。 針對這項測試，任何 **驗證** 模式都沒問題。 如果有任何指示表示伺服器不接受您的 IP 位址，請參閱 [伺服器層級和資料庫層級防火牆規則](firewall-configure.md) 以取得協助。

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>確認 Azure Active Directory Multi-Factor Authentication

再次執行 SSMS，這次將 **驗證** 設定為 **具有 MFA 的 Azure Active Directory 通用** 驗證。 此選項需要 SSMS 17.5 或更新版本。

如需詳細資訊，請參閱 [設定 SSMS 的 Multi-Factor Authentication 和 Azure AD](authentication-mfa-ssms-configure.md)。

> [!NOTE]
> 如果您是資料庫中的來賓使用者，您也必須提供資料庫的 Azure AD 功能變數名稱：選取 **選項** [  >  **AD 功能變數名稱] 或 [租使用者識別碼** ]。 若要在 Azure 入口網站中尋找功能變數名稱，請選取 **Azure Active Directory**  >  **自訂功能變數名稱** 。 在 C# 範例程式中，提供網域名稱並非必要。

## <a name="c-code-example"></a>C# 程式碼範例

> [!NOTE]
> 如果您使用 .NET Core，您會想要使用 [SqlClient](/dotnet/api/microsoft.data.sqlclient?view=sqlclient-dotnet-core-1.1) 命名空間。 如需詳細資訊，請參閱下列 [blog](https://devblogs.microsoft.com/dotnet/introducing-the-new-microsoftdatasqlclient/)。

C# 程式範例依賴 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 組件。

若要安裝此套件，請在 Visual Studio 中，選取 [ **Project**  >  **管理 NuGet 封裝** ]。 搜尋並安裝 **Microsoft.IdentityModel.Clients.ActiveDirectory** 。

這是 c # 原始程式碼的範例。

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
        static public string Az_SQLDB_svrName = "<Your server>";
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

這是 c # 測試輸出的範例。

```C#
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>後續步驟

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database 仍然支援 PowerShell Azure Resource Manager 模組，但所有未來的開發都是針對 Az.Sql 模組。 如需這些 Cmdlet，請參閱 [AzureRM.Sql](/powershell/module/AzureRM.Sql/) \(英文\)。 Az 模組和 AzureRm 模組中命令的引數本質上完全相同。

& [AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)