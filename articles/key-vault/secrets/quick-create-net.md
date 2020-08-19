---
title: 快速入門 - 適用於 .NET 的 Azure Key Vault 用戶端程式庫 (v4)
description: 了解如何使用 .NET 用戶端程式庫 (v4)，從 Azure Key Vault 建立、擷取和刪除秘密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/12/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 84f89b9def3fc5d98ebb669b9f5b86523854f7a8
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185718"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>快速入門：適用於 .NET 的 Azure Key Vault 用戶端程式庫 (SDK v4)

開始使用適用於 .NET 的 Azure Key Vault 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 使用適用於 .NET 的 Key Vault 用戶端程式庫來：

- 提高金鑰和密碼的安全性和控制權。
- 在幾分鐘內建立和匯入加密金鑰。
- 透過雲端規模和全域備援減少延遲。
- 簡化 TLS/SSL 憑證的工作並將其自動化。
- 使用經 FIPS 140-2 Level 2 驗證的 HSM。

[API 參考文件](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.NET Core 3.1 SDK 或更新版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/)

本快速入門假設您是在 Windows 終端機中執行 `dotnet`、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 和 Windows 命令 (例如 [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) 或 [Azure Cloud Shell](https://shell.azure.com/))。

## <a name="setting-up"></a>設定

### <a name="create-new-net-console-app"></a>建立新的 .NET 主控台應用程式

在主控台視窗中，使用 `dotnet new` 命令建立名為 `key-vault-console-app` 的新 .NET 主控台應用程式。

```console
dotnet new console -n key-vault-console-app
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>安裝套件

從主控台視窗安裝適用於 .NET 的 Azure Key Vault 用戶端程式庫：

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

在本快速入門中，您也需要安裝下列套件：

```console
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>建立服務主體

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>對服務主體授與金鑰保存庫的存取權

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environmental-variables"></a>設定環境變數

[!INCLUDE [Set environmental variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>物件模型

適用於 .NET 的 Azure Key Vault 用戶端程式庫可讓您管理金鑰和相關資產，例如憑證和祕密。 以下的程式碼範例會示範如何建立用戶端、設定祕密、擷取祕密，以及刪除秘密。

整個主控台應用程式可於 https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app 取得。

## <a name="code-examples"></a>程式碼範例

### <a name="add-directives"></a>新增指示詞

將下列指示詞新增至程式碼頂端：

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>驗證並建立用戶端

根據上述[設定環境變數](#set-environmental-variables)步驟中的環境變數，驗證您的金鑰保存庫並建立金鑰保存庫用戶端。 金鑰保存庫的名稱會以 "https://\<your-key-vault-name\>.vault.azure.net" 格式，擴充至金鑰保存庫 URI。 以下程式碼使用 ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) 來向金鑰保存庫驗證，也就是讀取環境變數以取得存取權杖。 

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>儲存秘密

既然應用程式已經過驗證，您可以使用 [client.SetSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)，將祕密放入金鑰保存庫中。這需要秘密的名稱，在此範例中，我們將使用 "mySecret"。  

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

您可以確認祕密是否已使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令來加以設定：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>擷取祕密

您現在可以使用 [client.GetSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)擷取先前設定的值。

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

您的祕密現在已另存為 `secret.Value`。

### <a name="delete-a-secret"></a>刪除祕密

最後，讓我們使用 [client.DeleteSecret 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)，從您的金鑰保存庫中刪除秘密。

[!code-csharp[Delete secret](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

您可以使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令，確認祕密是否已經消失：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 Azure CLI 或 Azure PowerShell 來移除金鑰保存庫和對應的資源群組。

### <a name="delete-a-key-vault"></a>刪除 Key Vault
```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>清除 Key Vault
```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```powershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>刪除資源群組

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>範例程式碼

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;

namespace key_vault_console_app
{
    class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
}
```


## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立金鑰保存庫、儲存秘密，並擷取該秘密。 請參閱 [GitHub 中的整個主控台應用程式](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)。

若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
