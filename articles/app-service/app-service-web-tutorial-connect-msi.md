---
title: 教學課程：使用受控識別存取資料
description: 了解如何使用受控識別，讓資料庫連線更加安全，以及如何將此受控服務識別套用到其他 Azure 服務。
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate, devx-track-azurecli
ms.openlocfilehash: 2c19ee2b8e7ec3c695b2c76c46402c118c559b40
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736233"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>教學課程：使用受控識別保護來自 App Service 的 Azure SQL Database 連線

[App Service](overview.md) 可在 Azure 中提供可高度擴充、自我修復的 Web 主控服務。 它也為您的應用程式提供[受控識別](overview-managed-identity.md)，這是用於保護 [Azure SQL Database](/azure/sql-database/) 和其他 Azure 服務存取權的周全解決方案。 App Service 中的受控識別可藉由從應用程式刪除祕密 (例如連接字串中的認證)，讓應用程式更加安全。 在本教學課程中，您會將受控識別新增至您在下列其中一個教學課程所建置的 Web 應用程式範例： 

- [教學課程：在 Azure 中搭配 Azure SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)
- [教學課程：在 Azure App Service 中建置 ASP.NET Core 和 Azure SQL Database 應用程式](tutorial-dotnetcore-sqldb-app.md)

當您完成時，範例應用程式不需要使用者名稱和密碼，即可安全地連線到 SQL Database。

> [!NOTE]
> 本教學課程所涵蓋的步驟支援下列版本：
> 
> - .NET Framework 4.7.2 和更新版本
> - .NET Core 2.2 和更新版本
>

您將了解：

> [!div class="checklist"]
> * 啟用受控身分識別
> * 將 SQL Database 存取權授與受控識別
> * 設定 Entity Framework 以將 Azure AD 驗證用於 SQL Database
> * 使用 Azure AD 驗證從 Visual Studio 連線至 SQL Database

> [!NOTE]
>Azure AD 驗證與內部部署 Active Directory (AD DS) 中的 [整合式 Windows 驗證](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))_不同_。 AD DS 和 Azure AD 使用完全不同的驗證通訊協定。 如需詳細資訊，請參閱 [Azure AD Domain Services 文件](../active-directory-domain-services/index.yml)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必要條件

本文會從您結束[教學課程：在 Azure 中搭配 SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)或[教學課程：在 Azure App Service 中建置 ASP.NET Core 和 SQL Database 應用程式](tutorial-dotnetcore-sqldb-app.md)。 如果您尚未進行，請先依照這兩個教學課程的其中一個來進行。 或者，您可以使用 SQL Database 針對自己的 .NET 應用程式調整步驟。

若要使用 SQL Database 作為後端對您的應用程式進行偵錯，請確定您已允許從您的電腦進行用戶端連線。 如果尚未這麼做，請遵循[使用 Azure 入口網站管理伺服器層級 IP 防火牆規則](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)中的步驟來新增用戶端 IP。

備妥環境以使用 Azure CLI。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>將資料庫存取權授與 Azure AD 使用者

首先，將 Azure AD 使用者指派為伺服器的 Active Directory 管理員，對 SQL Database 啟用 Azure AD 驗證。 此使用者與您用來註冊 Azure 訂用帳戶的 Microsoft 帳戶不同。 這必須是您已在 Azure AD 中建立、匯入、同步處理或受邀加入的使用者。 若要進一步了解允許的 Azure AD 使用者，請參閱 [SQL Database 中的 Azure AD 功能和限制](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)。

如果您的 Azure AD 租用戶還沒有使用者，請依照[使用 Azure Active Directory 新增或刪除使用者](../active-directory/fundamentals/add-users-azure-active-directory.md)中的步驟建立一個。

使用 [`az ad user list`](/cli/azure/ad/user#az-ad-user-list) 尋找 Azure AD 使用者的物件識別碼，並取代 *\<user-principal-name>* 。 結果會儲存到變數中。

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> 若要在 Azure AD 中查看所有使用者主體名稱的清單，請執行 `az ad user list --query [].userPrincipalName`。
>

在 Cloud Shell 中使用 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) 命令，將此 Azure AD 使用者新增為 Active Directory 管理員。 在下列命令中，將 *\<server-name>* 取代為伺服器名稱 (不含 `.database.windows.net` 尾碼)。

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

如需關於新增 Active Directory 管理員的詳細資訊，請參閱[為伺服器佈建 Azure Active Directory 管理員](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)

## <a name="set-up-visual-studio"></a>設定 Visual Studio

### <a name="windows-client"></a>Windows 用戶端
Visual Studio for Windows 會與 Azure AD 驗證整合。 若要啟用在 Visual Studio 中的開發和偵錯，請從功能表中選取 [檔案] > [帳戶設定]，然後按一下 [新增帳戶]，以在 Visual Studio 中新增您的 Azure AD 使用者。

若要設定 Azure 服務驗證的 Azure AD 使用者，請從功能表中選取 [工具] > [選項]，然後選取 [Azure 服務驗證] > [帳戶選取]。 選取您新增的 Azure AD 使用者，然後按一下 [確定]。

現在您已可開始將 SQL Database 作為後端，使用 Azure AD 驗證開發和偵錯您的應用程式。

### <a name="macos-client"></a>macOS 用戶端

Visual Studio for Mac 不會與 Azure AD 驗證整合。 不過，稍後會用到的 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 程式庫可以使用 Azure CLI 中的權杖。 若要在 Visual Studio 中啟用開發和偵錯工具，您必須先在本機電腦上安裝 [Azure CLI](/cli/azure/install-azure-cli)。

在本機電腦上安裝 Azure CLI 後，請使用 Azure AD 使用者以下列命令登入 Azure CLI：

```azurecli
az login --allow-no-subscriptions
```
現在您已可開始將 SQL Database 作為後端，使用 Azure AD 驗證開發和偵錯您的應用程式。

## <a name="modify-your-project"></a>修改專案

您所遵循的專案步驟取決於其屬於 ASP.NET 專案還是 ASP.NET Core 專案。

- [修改 ASP.NET](#modify-aspnet)
- [修改 ASP.NET Core](#modify-aspnet-core)

### <a name="modify-aspnet"></a>修改 ASP.NET

在 Visual Studio 中，開啟套件管理員主控台，並新增 NuGet 套件 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)：

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

在 Web.config 中，從檔案頂端開始處理，並進行下列變更：

- 在 `<configSections>` 中，新增下列區段宣告：

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- 在右側 `</configSections>` 標記下方，為 `<SqlAuthenticationProviders>` 新增下列 XML 指令碼。

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- 尋找名為 `MyDbConnection` 的連接字串，並將其 `connectionString` 值取代為 `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`。 將 _\<server-name>_ 和 _\<db-name>_ 取代為您的伺服器名稱和資料庫名稱。

> [!NOTE]
> 您剛註冊的 SqlAuthenticationProvider 是以您稍早安裝的 AppAuthentication 程式庫為基礎。 根據預設，其會使用系統指派的身分識別。 若要利用使用者指派的身分識別，您需要提供其他組態。 請參閱 AppAuthentication 程式庫的[連接字串支援](/dotnet/api/overview/azure/service-to-service-authentication#connection-string-support)。

這就是要連線至 SQL Database 所需的所有項目。 在 Visual Studio 中進行偵錯時，程式碼會使用您在[設定 Visual Studio](#set-up-visual-studio) 中所設定的 Azure AD 使用者。 稍後，您將設定 SQL Database 以允許來自 App Service 應用程式受控識別的連線。

輸入 `Ctrl+F5` 以再次執行應用程式。 瀏覽器中的相同 CRUD 應用程式此時會使用 Azure AD 驗證直接連線至 Azure SQL Database。 此設定可讓您從 Visual Studio 執行資料庫移轉。

### <a name="modify-aspnet-core"></a>修改 ASP.NET Core

在 Visual Studio 中，開啟套件管理員主控台，並新增 NuGet 套件 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)：

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

在 [ASP.NET Core 和 SQL Database 教學課程](tutorial-dotnetcore-sqldb-app.md)中完全不會使用連接字串 `MyDbConnection`，因為本機開發環境會使用 Sqlite 資料庫檔案，Azure 生產環境則會使用來自 App Service 的連接字串。 在使用 Active Directory 驗證時，您會希望這兩個環境使用相同的連接字串。 在 appsettings.json 中，將 `MyDbConnection` 連接字串的值取代為：

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

接下來，您要提供 Entity Framework 資料庫內容，以及 SQL Database 的存取權杖。 在 Data\MyDatabaseContext.cs 中，將下列程式碼新增至空白 `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` 建構函式的大括弧內：

```csharp
var connection = (SqlConnection)Database.GetDbConnection();
connection.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> 為了能簡單明瞭地說明，此示範程式碼是同步的。

這就是要連線至 SQL Database 所需的所有項目。 在 Visual Studio 中進行偵錯時，程式碼會使用您在[設定 Visual Studio](#set-up-visual-studio) 中所設定的 Azure AD 使用者。 稍後，您將設定 SQL Database 以允許來自 App Service 應用程式受控識別的連線。 `AzureServiceTokenProvider` 類別會快取記憶體中的權杖，並在到期之前從 Azure AD 擷取權杖。 您不需使用任何自訂程式碼來重新整理權杖。

> [!TIP]
> 如果您設定的 Azure AD 使用者有權存取多個租用戶，請搭配所需的租用戶識別碼來呼叫 `GetAccessTokenAsync("https://database.windows.net/", tenantid)`，以取得適當的存取權杖。

輸入 `Ctrl+F5` 以再次執行應用程式。 瀏覽器中的相同 CRUD 應用程式此時會使用 Azure AD 驗證直接連線至 Azure SQL Database。 此設定可讓您從 Visual Studio 執行資料庫移轉。

## <a name="use-managed-identity-connectivity"></a>使用受控識別連線

接著，您將設定 App Service 應用程式，以使用系統指派的受控識別連線至 SQL Database。

> [!NOTE]
> 雖然本節中的指示適用於系統指派的身分識別，但您也可以輕鬆使用使用者指派的身分識別。 要執行這項作業， 您需要變更 `az webapp identity assign command`，才能指派所需的使用者指派身分識別。 然後，在建立 SQL 使用者時，請務必使用使用者指派的身分識別資源名稱，不要使用網站名稱。

### <a name="enable-managed-identity-on-app"></a>啟用應用程式上的受控識別

若要啟用 Azure 應用程式的受控識別，請在 Cloud Shell 中使用 [az webapp identity assign](/cli/azure/webapp/identity#az-webapp-identity-assign) 命令。 在下列命令中，取代 *\<app-name>* 。

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

輸出的範例如下：

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>將權限授與受控識別

> [!NOTE]
> 如有需要，您可以將身分識別新增至 [Azure AD 群組](../active-directory/fundamentals/active-directory-manage-groups.md)，然後將 SQL Database 存取權授與 Azure AD 群組，而不是身分識別。 例如，下列命令會將上一個步驟中的受控識別新增至名為 _myAzureSQLDBAccessGroup_ 的新群組：
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

在 Cloud Shell 中，使用 SQLCMD 命令登入 SQL Database。 以您的伺服器名稱取代 _\<server-name>_ ，將 _\<db-name>_ 取代為應用程式所使用的資料庫名稱，並且將 _\<aad-user-name>_ 和 _\<aad-password>_ 取代為r Azure AD 使用者的憑證。

```bash
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

在您所需資料庫的 SQL 提示字元中執行下列命令，以授與您應用程式所需的權限。 例如， 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* 是 Azure AD 中的受控識別名稱。 如果身分識別是由系統指派的，則名稱一律會與您 App Service 應用程式名稱相同。 若要為 Azure AD 群組授與權限，請改用群組的顯示名稱 (例如 *myAzureSQLDBAccessGroup*)。

輸入 `EXIT` 以返回 Cloud Shell 提示字元。

> [!NOTE]
> 受控識別的後端服務也會[維護權杖快取](overview-managed-identity.md#obtain-tokens-for-azure-resources)，只有當目標資源過期時，才會更新其權杖。 如果您在設定 SQL Database 權限時發生錯誤，並在嘗試透過應用程式取得權杖 *之後* 嘗試修改權限，則在快取的權杖過期前，您實際上不會取得具有更新權限的新權杖。

> [!NOTE]
> 內部部署 SQL Server 不支援 AAD，這包括 MSI。 

### <a name="modify-connection-string"></a>修改連接字串

請記住，您在 Web.config 或 appsettings.json 中所做的相同變更也適用於受控識別，因此您唯一要做的，就是在 App Service 中移除現有的連接字串，也就是 Visual Studio 在第一次部署您的應用程式時建立的字串。 使用下列命令，但將 *\<app-name>* 取代為您的應用程式名稱。

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>發佈您的變更

現在只剩下將您的變更發佈至 Azure。

**如果您來自 [教學課程：在 Azure 中搭配 SQL Database 來建置 ASP.NET 應用程式](app-service-web-tutorial-dotnet-sqldatabase.md)** ，請在 Visual Studio 中發佈變更。 在 [方案總管] 中，以滑鼠右鍵按一下 [DotNetAppSqlDb] 專案，然後選取 [發佈]。

![從方案總管發佈](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

在發佈頁面中，按一下 [發佈]。 

**如果您來自 [教學課程：在 Azure App Service 中建置 ASP.NET Core 和 SQL Database 應用程式](tutorial-dotnetcore-sqldb-app.md)** ，請使用 Git 和下列命令來發佈變更：

```bash
git commit -am "configure managed identity"
git push azure main
```

當新的網頁顯示待辦事項清單時，表示應用程式正使用受控識別連線到資料庫。

![Code First 移轉之後的 Azure 應用程式](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

您現在應該能夠如以往一樣編輯待辦事項清單。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>後續步驟

您已了解如何︰

> [!div class="checklist"]
> * 啟用受控身分識別
> * 將 SQL Database 存取權授與受控識別
> * 設定 Entity Framework 以將 Azure AD 驗證用於 SQL Database
> * 使用 Azure AD 驗證從 Visual Studio 連線至 SQL Database

前往下一個教學課程，了解如何將自訂的 DNS 名稱對應至 Web 應用程式。

> [!div class="nextstepaction"]
> [將現有的自訂 DNS 名稱對應至 Azure App Service](app-service-web-tutorial-custom-domain.md)