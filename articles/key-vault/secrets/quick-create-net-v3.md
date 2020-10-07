---
title: 快速入門 - 適用於 .NET 的 Azure Key Vault 用戶端程式庫 (SDK v3)
description: 了解如何使用 .NET 用戶端程式庫 (v3)，從 Azure Key Vault 建立、擷取和刪除秘密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 2e5f3357baa774c9690e079a82b11d2c651ebe9b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "87078867"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>快速入門：適用於 .NET 的 Azure Key Vault 用戶端程式庫 (SDK v3)

開始使用適用於 .NET 的 Azure Key Vault 用戶端程式庫。 請遵循下列步驟來安裝套件，並試用基本工作的程式碼範例。

> [!NOTE]
> 本快速入門使用 3.0.4 版本的 Microsoft.Azure.KeyVault 用戶端程式庫。 若要使用最新版本的 Key Vault 用戶端程式庫，請參閱[適用於 .NET 的 Azure Key Vault 用戶端程式庫 (SDK v4)](quick-create-net.md)。 

Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 使用適用於 .NET 的 Key Vault 用戶端程式庫來：

- 提高金鑰和密碼的安全性和控制權。
- 在幾分鐘內建立和匯入加密金鑰。
- 透過雲端規模和全域備援減少延遲。
- 簡化 TLS/SSL 憑證的工作並將其自動化。
- 使用經 FIPS 140-2 Level 2 驗證的 HSM。

[API 參考文件](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [程式庫原始程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> 每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.NET Core 3.1 SDK 或更新版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/)

本快速入門假設您是在 Windows 終端機中執行 `dotnet`、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 和 Windows 命令 (例如 [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6)、[Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6) 或 [Azure Cloud Shell](https://shell.azure.com/))。

## <a name="setting-up"></a>設定

### <a name="create-new-net-console-app"></a>建立新的 .NET 主控台應用程式

在主控台視窗中，使用 `dotnet new` 命令建立名為 `akv-dotnet` 的新 .NET 主控台應用程式。


```console
dotnet new console -n akvdotnet
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
dotnet add package Microsoft.Azure.KeyVault
```

在本快速入門中，您也需要安裝下列套件：

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>建立服務主體

[!INCLUDE [Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>對服務主體授與金鑰保存庫的存取權

[!INCLUDE [Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

## <a name="object-model"></a>物件模型

適用於 .NET 的 Azure Key Vault 用戶端程式庫可讓您管理金鑰和相關資產，例如憑證和祕密。 下面的程式碼範例會示範如何設定祕密和擷取祕密。

整個主控台應用程式可於 https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet 取得。

## <a name="code-examples"></a>程式碼範例

### <a name="add-directives"></a>新增指示詞

將下列指示詞新增至程式碼頂端：

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>向金鑰保存庫進行驗證

這個 .NET 快速入門會仰賴環境變數來儲存不應該放在程式碼中的認證。 

在建置和執行應用程式前，請先使用 `setx` 命令將 `akvClientId`、`akvClientSecret`、`akvTenantId` 和 `akvSubscriptionId` 環境變數設定為您在前面記下的值。

**Windows**

```console
setx akvClientId "<your-clientID>"
setx akvClientSecret "<your-clientSecret>"
```

**Linux**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

**MacOS**

```bash
export akvClientId = "<your-clientID>"
export akvClientSecret = "<your-clientSecret>"
```

請將這些環境變數指派給程式碼中的字串，然後藉由將字串傳遞至 [KeyVaultClient 類別](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)來驗證應用程式：

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>儲存秘密

現在，應用程式已進行過驗證，因此您可以使用 [SetSecretAsync 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync)將祕密放入金鑰保存庫中。這會用到金鑰保存庫的 URL，其格式為 `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`。 其也需要祕密的名稱 -- 我們使用的是「mySecret」。 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

您可以確認祕密是否已使用 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 命令來加以設定：

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>擷取祕密

您現在可以使用 [GetSecretAsync 方法](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)來擷取先前設定的值。

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

您的祕密現在已另存為 `keyvaultSecret.Value;`。

## <a name="clean-up-resources"></a>清除資源

若不再需要，您可以使用 Azure CLI 或 Azure PowerShell 來移除金鑰保存庫和對應的資源群組。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立金鑰保存庫、儲存秘密，並擷取該秘密。 請參閱 [GitHub 中的整個主控台應用程式](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)。

若要深入了解 Key Vault 以及要如何將其與應用程式整合，請繼續閱讀下列文章。

- 實作[使用 .NET 進行 Azure Key Vault 的服務對服務驗證](../general/service-to-service-authentication.md)
- 閱讀 [Azure Key Vault 概觀](../general/overview.md)
- 參閱 [Azure Key Vault 開發人員指南](../general/developers-guide.md)
- 檢閱 [Azure Key Vault 最佳做法](../general/best-practices.md)
