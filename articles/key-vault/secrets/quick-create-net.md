---
title: 快速入門 - 適用於 .NET 的 Azure Key Vault 秘密用戶端程式庫 (版本 4)
description: 了解如何使用 .NET 用戶端程式庫 (版本 4)，從 Azure Key Vault 建立、擷取和刪除秘密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: ecd5fd4f5af883d26f904181796a78f61669b37a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187352"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>快速入門：適用於 .NET 的 Azure Key Vault 秘密用戶端程式庫 (SDK v4)

開始使用適用於 .NET 的 Azure Key Vault 秘密用戶端程式庫。 [Azure Key Vault](../general/overview.md) 是一項雲端服務，可為祕密提供安全的存放區。 您也可以安全地儲存金鑰、密碼、憑證和其他祕密。 您可以透過 Azure 入口網站建立和管理 Azure 金鑰保存庫。 在本快速入門中，您會了解如何使用 .NET 用戶端程式庫，從 Azure Key Vault 建立、擷取和刪除秘密

Key Vault 用戶端程式庫資源：

[API 參考文件](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet&preserve-view=true) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

如需有關 Key Vault 及秘密的詳細資訊，請參閱：
- [Key Vault 概觀](../general/overview.md)
- [秘密概觀](about-secrets.md)。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK 或更新版本](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Key Vault - 您可以使用 [Azure 入口網站](../general/quick-create-portal.md)、[Azure CLI](../general/quick-create-cli.md) 或 [Azure PowerShell](../general/quick-create-powershell.md) 建立。

本快速入門使用的是 `dotnet` 和 Azure CLI

## <a name="setup"></a>安裝程式

本快速入門會使用 Azure 身分識別程式庫搭配 Azure CLI，向 Azure 服務驗證使用者。 開發人員也可以使用 Visual Studio 或 Visual Studio Code 來驗證其呼叫。如需詳細資訊，請參閱[使用 Azure 身分識別用戶端程式庫驗證用戶端](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)。

### <a name="sign-in-to-azure"></a>登入 Azure

1. 執行 `login` 命令。

    ```azurecli-interactive
    az login
    ```

    如果 CLI 可以開啟預設瀏覽器，它會執行這項操作，並載入 Azure 登入頁面。

    否則，請在 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 中開啟瀏覽器頁面，並輸入顯示在終端機中的授權碼。

2. 請在瀏覽器中使用您的帳戶認證登入。


### <a name="create-new-net-console-app"></a>建立新的 .NET 主控台應用程式

1. 在命令殼層中，執行下列命令以建立名為 `key-vault-console-app` 的專案：

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. 變更為新建立的 *key-vault-console-app* 目錄，然後執行下列命令來建置專案：

    ```dotnetcli
    dotnet build
    ```

    建置輸出應該不會有警告或錯誤。
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>安裝套件

從命令殼層安裝適用於 .NET 的 Azure Key Vault 秘密用戶端程式庫：

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

針對本快速入門，您也需要安裝適用於Azure 身分識別的 Azure SDK 用戶端程式庫：

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="grant-access-to-your-key-vault"></a>授與對金鑰保存庫的存取權

建立金鑰保存庫的存取原則，將祕密權限授與服務主體

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

#### <a name="set-environment-variables"></a>設定環境變數

此應用程式使用金鑰保存庫名稱作為稱為 `KEY_VAULT_NAME` 的環境變數。

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS 或 Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>物件模型

適用於 .NET 的 Azure Key Vault 秘密用戶端程式庫可讓您管理秘密。 以下的[程式碼範例](#code-examples)會示範如何建立用戶端、設定祕密、擷取祕密，以及刪除秘密。

## <a name="code-examples"></a>程式碼範例

### <a name="add-directives"></a>新增指示詞

將下列指示詞新增至 *Program.cs* 的上方：

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

在本快速入門中，已登入的使用者是用來向金鑰保存庫進行驗證，這是本機開發的慣用方法。 針對部署至 Azure 的應用程式，應將受控識別指派給 App Service 或虛擬機器。如需詳細資訊，請參閱[受控識別概觀](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。

在下列範例中，金鑰保存庫的名稱會以 "https://\<your-key-vault-name\>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。 這個範例會使用 ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential) 類別，允許在各種不同的環境中使用相同的程式碼，搭配不同的選項來提供身分識別。 如需詳細資訊，請參閱[預設 Azure 認證驗證](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme?#defaultazurecredential)。 

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>儲存秘密

既然主控台應用程式已通過驗證，請將秘密新增至金鑰保存庫。 針對這項工作，使用 [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) 方法。 在此範例中，方法的第一個參數會接受密碼&mdash;"mySecret" 的名稱。

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> 如果秘密名稱存在，則上述程式碼會建立該秘密的新版本。


### <a name="retrieve-a-secret"></a>擷取祕密

您現在可以使用 [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) 方法來擷取先前設定的值。

```csharp
var secret = await client.GetSecretAsync(secretName);
``````

您的祕密現在已另存為 `secret.Value`。

### <a name="delete-a-secret"></a>刪除祕密

最後，讓我們使用 [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) 方法，從您的金鑰保存庫中刪除秘密。

```csharp
await client.StartDeleteSecretAsync(secretName);
```

## <a name="sample-code"></a>範例程式碼

請完成下列步驟來修改 .NET Core 主控台應用程式，以與 Key Vault 互動：

1. 將 *Program.cs* 中的程式碼取代為下列程式碼：

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>測試和驗證

1. 執行下列命令來執行應用程式。

    ```dotnetcli
    dotnet run
    ```

1. 當系統提示時，輸入祕密值。 例如，mySecretPassword。

    下列輸出的變化會出現：

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 Azure CLI 或 Azure PowerShell 來移除金鑰保存庫和對應的資源群組。

### <a name="delete-a-key-vault"></a>刪除 Key Vault

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>清除 Key Vault

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>刪除資源群組

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>後續步驟

若要深入了解 Key Vault 以及如何將其與應用程式整合，請參閱下列文章：

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 參閱[從 App Service 應用程式存取 Key Vault 教學課程](../general/tutorial-net-create-vault-azure-web-app.md)
- 參閱[從虛擬機器存取 Key Vault 教學課程](../general/tutorial-net-virtual-machine.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)