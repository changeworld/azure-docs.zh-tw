---
title: 教學課程`:` 使用受控識別來存取 Azure SQL Database - Windows - Azure AD
description: 本教學課程逐步引導您使用 Windows VM 系統指派的受控識別，以存取 Azure SQL Database。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13be33843172f505ed8f12293137c0808e9bd2a0
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85920372"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>教學課程：使用 Windows VM 系統指派的受控識別來存取 Azure SQL

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

本教學課程說明如何將系統指派的身分識別用於 Windows 虛擬機器 (VM)，以存取 Azure SQL Database。 受控服務身分識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 您會了解如何：

> [!div class="checklist"]
>
> * 將您的 VM 存取權授與 Azure SQL Database
> * 啟用 Azure AD 驗證
> * 在資料庫中建立內含的使用者，以代表 VM 系統指派的身分識別
> * 使用 VM 身分識別取得存取權杖，並使用它查詢 Azure SQL Database

## <a name="prerequisites"></a>必要條件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>啟用

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>授與存取權

若要為 Azure SQL Database 中的資料庫授與您的 VM 存取權，您可以使用現有的[邏輯 SQL 伺服器](../../azure-sql/database/logical-servers.md)，或建立新的伺服器。 若要使用 Azure 入口網站建立新的伺服器和資料庫，請遵循此 [Azure SQL 快速入門](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)的作法。 在 [Azure SQL 文件](https://docs.microsoft.com/azure/sql-database/)中也有使用 Azure CLI 和 Azure PowerShell 的快速入門作法。

為資料庫授與 VM 存取權的作業有兩個步驟：

1. 啟用伺服器的 Azure AD 驗證。
2. 在資料庫中建立**內含的使用者**，以代表 VM 系統指派的身分識別。

### <a name="enable-azure-ad-authentication"></a>啟用 Azure AD 驗證

**若要[設定 Azure AD 驗證](/azure/sql-database/sql-database-aad-authentication-configure)：**

1. 在 Azure 入口網站中，選取左側導覽中的 [SQL 伺服器]。
2. 按一下要啟用 Azure AD 驗證的 SQL 伺服器。
3. 在刀鋒視窗的 [設定] 區段中，按一下 [Active Directory 系統管理員]。
4. 在命令列中，按一下 [設定系統管理員]。
5. 選取要設為伺服器系統管理員的 Azure AD 使用者帳戶，然後按一下 [選取]。
6. 在命令列中，按一下 [儲存]。

### <a name="create-contained-user"></a>建立內含的使用者

本節將說明如何在資料庫中建立內含的使用者，以代表 VM 系統指派的身分識別。 在此步驟中，您需要 [Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 在開始之前，先閱讀以下文章了解 Azure AD 整合的背景會很有幫助：

- [SQL Database 和 Azure Synapse Analytics 的通用驗證 (MFA 的 SSMS 支援)](/azure/sql-database/sql-database-ssms-mfa-authentication)
- [使用 SQL Database 或 Azure Synapse Analytics 設定和管理 Azure Active Directory 驗證](/azure/sql-database/sql-database-aad-authentication-configure)

SQL DB 需要唯一的 AAD 顯示名稱。 因此，AAD 帳戶 (例如使用者、群組和服務主體 (應用程式)) 和針對受控識別啟用的 VM 名稱，在 AAD 中都必須具有唯一定義的顯示名稱。 SQL DB 會在 T-SQL 建立這類使用者時檢查 AAD 顯示名稱，如果這不是唯一的名稱，命令就無法要求為指定的帳戶提供唯一的 AAD 顯示名稱。

**若要建立內含的使用者：**

1. 啟動 SQL Server Management Studio。
2. 在 [連線到伺服器] 對話方塊中，在 [伺服器名稱] 欄位中輸入您的伺服器名稱。
3. 在 [驗證] 欄位中，選取 [具 MFA 支援的 Active Directory - 通用]。
4. 在 [使用者名稱] 欄位中，輸入您設為伺服器系統管理員的 Azure AD 帳戶名稱，例如 helen@woodgroveonline.com。
5. 按一下 [選項]。
6. 在 [連 線至資料庫] 欄位中，輸入您想要設定的非系統資料庫的名稱。
7. 按一下 [ **連接**]。 完成登入程序。
8. 在 [物件總管] 中展開 [資料庫] 資料夾。
9. 在使用者資料庫上按一下滑鼠右鍵，然後按一下 [新增查詢]。
10. 在查詢視窗中，輸入下列這一行，然後按一下工具列中的 [執行]︰

    > [!NOTE]
    > 下列命令中的 `VMName` 是在必要條件一節中已將系統指派的身分識別啟用的 VM 名稱。

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    命令應該會順利完成，為 VM 系統指派的身分識別建立內含的使用者。
11. 清除查詢視窗，輸入下列這一行，然後按一下工具列中的 [執行]︰

    > [!NOTE]
    > 下列命令中的 `VMName` 是在必要條件一節中已將系統指派的身分識別啟用的 VM 名稱。

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    命令應該會順利完成，將讀取整個資料庫的能力授與包含的使用者。

在 VM 中執行的程式碼現在可以使用其系統指派的受控識別取得權杖，並使用此權杖向伺服器進行驗證。

## <a name="access-data"></a>存取資料

本節將說明如何使用 VM 系統指派的受控識別來取得存取權杖，以用來呼叫 Azure SQL。 Azure SQL 原生支援 Azure AD 驗證，因此可直接接受使用適用於 Azure 資源的受控識別所取得的存取權杖。 您使用 **access token** 方法來建立 SQL 連線。 這是 Azure AD 與 Azure SQL 整合的一部分，與在連接字串上提供認證不同。

以下是使用存取權杖來開啟與 SQL 之連線的 .NET 程式碼範例。 此程式碼必須在 VM 上執行，才能夠存取 VM 系統指派的受控識別端點。 必須要有 **.NET Framework 4.6** (或更新版本) 或 **.NET Core 2.2** (或更新版本)，才能使用存取權杖方法。 將 AZURE-SQL-SERVERNAME 和 DATABASE 的值取代為實際值。 請注意，Azure SQL 的資源識別碼是 `https://database.windows.net/`。

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

或者，另一個測試端對端安裝程式，而不需要在 VM 上撰寫和部署應用程式的快速方法，是使用 PowerShell。

1. 在入口網站中，瀏覽至 [虛擬機器] 並移至您的 Windows 虛擬機器，在 [概觀] 中按一下 [連線]。
2. 輸入您建立 Windows VM 時新增的**使用者名稱**和**密碼**。
3. 現在您已經建立虛擬機器的**遠端桌面連線**，請在遠端工作階段中開啟 **PowerShell**。
4. 使用 PowerShell 的 `Invoke-WebRequest`，向本機受控識別的端點提出要求，以取得 Azure SQL 的存取權杖。

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    將來自 JSON 物件的回應轉換為 PowerShell 物件。

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    擷取回應中的存取權杖。

    ```powershell
    $AccessToken = $content.access_token
    ```

5. 開啟伺服器的連線。 記得取代 AZURE-SQL-SERVERNAME 和 DATABASE 的值。

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    接下來，建立查詢並傳送至伺服器。 記得取代 TABLE 的值。

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

檢查 `$DataSet.Tables[0]` 的值以檢視查詢的結果。

## <a name="disable"></a>停用

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何使用系統指派的受控識別，來存取 Azure SQL Database。 若要深入了解 Azure SQL Database，請參閱：

> [!div class="nextstepaction"]
> [Azure SQL Database](/azure/sql-database/sql-database-technical-overview)
