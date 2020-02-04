---
title: 快速入門 - 適用於 .NET 的 Azure Key Vault 用戶端程式庫 (SDK v3)
description: 了解如何使用 .NET 用戶端程式庫 (v3)，從 Azure Key Vault 建立、擷取和刪除秘密
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 9b92796e477ea0dd6795015edd3f400dd2cc9aa7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773751"
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


## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [.NET Core 2.1 SDK 或更新版本](https://dotnet.microsoft.com/download/dotnet-core/2.1)。
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 或 [Azure PowerShell](/powershell/azure/overview)

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

本快速入門會使用預先建立的 Azure 金鑰保存庫。 您可以遵循 [Azure CLI 快速入門](quick-create-cli.md)、[Azure PowerShell 快速入門](quick-create-powershell.md)或 [Azure 入口網站快速入門](quick-create-portal.md)中的步驟來建立金鑰保存庫。 或者，您也可以直接執行下面的 Azure CLI 命令。

> [!Important]
> 每個金鑰保存庫必須有唯一的名稱。 在下列範例中，以您的金鑰保存庫名稱取代 <your-unique-keyvault-name>。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>建立服務主體

若要驗證雲端式 .NET 應用程式，最簡單的方法是使用受控識別；如需詳細資訊，請參閱[使用 App Service 受控識別存取 Azure Key Vault](managed-identity.md)。 不過，為了簡單起見，本快速入門會建立 .NET 主控台應用程式。 使用 Azure 來驗證桌面應用程式時，必須使用服務主體和存取控制原則。

請使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令來建立服務主體：

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

這項作業會傳回一系列的金鑰/值組。 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

請記下 clientId 和 clientSecret，以供在下面的[向金鑰保存庫驗證](#authenticate-to-your-key-vault)步驟中使用。

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>對服務主體授與金鑰保存庫的存取權

建立金鑰保存庫的存取原則，其藉由將 clientId 傳遞至 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，將權限授予您的服務主體。 對服務主體授與金鑰和祕密的取得、列出及設定權限。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

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

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

每次呼叫 `setx` 時，都應該會收到「成功：指定的值已儲存」的回應。

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

- 實作[使用 .NET 進行 Azure Key Vault 的服務對服務驗證](service-to-service-authentication.md)
- 閱讀 [Azure Key Vault 概觀](key-vault-overview.md)
- 參閱 [Azure Key Vault 開發人員指南](key-vault-developers-guide.md)
- 了解[金鑰、祕密與憑證](about-keys-secrets-and-certificates.md)
- 檢閱 [Azure Key Vault 最佳做法](key-vault-best-practices.md)
