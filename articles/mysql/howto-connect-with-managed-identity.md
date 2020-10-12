---
title: 使用受控識別來連線 - 適用於 MySQL 的 Azure 資料庫
description: 了解如何使用受控識別進行連線和驗證，以對適用於 MySQL 的 Azure 資料庫進行驗證
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7733148777cde2a487e5c93d7750eb7a24ff531c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88999388"
---
# <a name="connect-with-managed-identity-to-azure-database-for-mysql"></a>使用受控識別連線到適用於 MySQL 的 Azure 資料庫

此文章說明如何將使用者指派的身分識別用於 Azure 虛擬機器 (VM)，以存取適用於 MySQL 的 Azure 資料庫伺服器。 受控服務身分識別由 Azure 自動管理，並可讓您驗證支援 Azure AD 驗證的服務，而不需要將認證插入程式碼中。 

您會了解如何：

- 將您的 VM 存取權授與適用於 MySQL 的 Azure 資料庫伺服器
- 在資料庫中建立使用者，以代表 VM 使用者指派的身分識別
- 使用 VM 身分識別取得存取權杖，並使用其查詢適用於 MySQL 的 Azure 資料庫伺服器
- 在 C# 範例應用程式中實作權杖擷取

> [!IMPORTANT]
> 使用受控識別進行連線時，僅適用于 MySQL 5.7 和更新版本。

## <a name="prerequisites"></a>Prerequisites

- 如果您不熟悉適用於 Azure 資源的受控識別功能，請參閱此[概觀](../../articles/active-directory/managed-identities-azure-resources/overview.md)。 如果您沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 若要執行所需的資源建立和角色管理，您的帳戶必須在適當的範圍 (您的訂用帳戶或資源群組) 上具備「擁有者」權限。 如果您需要角色指派的協助，請參閱[使用角色型存取控制來管理 Azure 訂用帳戶資源的存取權](../../articles/role-based-access-control/role-assignments-portal.md)。
- 您需要 Azure VM (例如執行 Ubuntu Linux)，以供您使用受控識別來存取資料庫
- 您需要已設定 [Azure AD 驗證](howto-configure-sign-in-azure-ad-authentication.md)的適用於 MySQL 的 Azure 資料庫資料庫伺服器
- 若要遵循此 C# 範例，請先完成如何[使用 C# 連線](connect-csharp.md)的指南

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>為您的 VM 建立使用者指派的受控識別

使用 [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 命令，在您的訂用帳戶中建立身分識別。 您可以使用您的虛擬機器執行所在的相同資源群組，或使用不同的資源群組。

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

若要在接下來的步驟中設定身分識別，請使用 [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) 命令，以將身分識別的資源識別碼與用戶端識別碼儲存在變數中。

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

我們現在可以使用 [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 命令，將使用者指派的身分識別指派至 VM：

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

若要完成設定，請顯示 [用戶端識別碼] 的值，您在接下來的幾個步驟中需要此值：

```bash
echo $clientID
```

## <a name="creating-a-mysql-user-for-your-managed-identity"></a>為您的受控識別建立 MySQL 使用者

現在，以 Azure AD 系統管理員使用者身分連線到您的 MySQL 資料庫，然後執行下列 SQL 陳述式：

```sql
SET aad_auth_validate_oids_in_tenant = OFF;
CREATE AADUSER 'myuser' IDENTIFIED BY 'CLIENT_ID';
```

當使用 `myuser` 使用者名稱 (以您選擇的名稱取代) 進行驗證時，受控識別現在具有存取權。

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>正在從 Azure 執行個體中繼資料服務擷取存取權杖

您的應用程式現在可以從 Azure 執行個體中繼資料服務擷取存取權杖，並用來向資料庫進行驗證。

藉由對 `http://169.254.169.254/metadata/identity/oauth2/token` 發出 HTTP 要求並傳遞下列參數，即可完成此權杖擷取：

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (您先前擷取的項目)

您將會得到包含 `access_token` 欄位的 JSON 結果 - 此長文字值是受控識別存取權杖，在連線到資料庫時，您應該用來作為密碼。

針對測試目的，您可以在殼層中執行下列命令。 請注意，您必須安裝 `curl`、`jq` 和 `mysql` 用戶端。

```bash
# Retrieve the access token
accessToken=$(curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token)

# Connect to the database
mysql -h SERVER --user USER@SERVER --enable-cleartext-plugin --password=$accessToken
```

您現在已連線到先前設定的資料庫。

## <a name="connecting-using-managed-identity-in-c"></a>在 C# 中使用受控識別進行連線

此節將說明如何使用 VM 使用者指派的受控識別來取得存取權杖，以用來呼叫適用於 MySQL 的 Azure 資料庫。 適用於 MySQL 的 Azure 資料庫原生支援 Azure AD 驗證，因此可以直接接受使用 Azure 資源的受控識別所取得的存取權杖。 建立與 MySQL 的連線時，您將存取權杖傳遞至密碼欄位。

以下是使用存取權杖來開啟與 MySQL 之連線的 .NET 程式碼範例。 此程式碼必須在 VM 上執行，才能夠存取 VM 使用者指派的受控識別端點。 必須要有 .NET Framework 4.6 (或更新版本) 或 .NET Core 2.2 (或更新版本)，才能使用存取權杖方法。 取代 HOST、USER、DATABASE 和 CLIENT_ID 的值。

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using System.Threading.Tasks;
using MySql.Data.MySqlClient;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static async Task Main(string[] args)
        {
            //
            // Get an access token for MySQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for MySQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
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
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the MySQL server using the access token.
            //
            var builder = new MySqlConnectionStringBuilder
            {
                Server = Host,
                Database = Database,
                UserID = User,
                Password = accessToken,
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT VERSION()";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine("\nConnected!\n\nMySQL version: {0}", reader.GetString(0));
                        }
                    }
                }
            }
        }
    }
}
```

執行時，此命令會提供如下的輸出：

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

MySQL version: 5.7.27
```

## <a name="next-steps"></a>後續步驟

* 檢閱[使用「適用於 MySQL 的 Azure 資料庫」進行 Azure Active Directory 驗證](concepts-azure-ad-authentication.md)的整體概念
