---
title: 教學課程 - 在 .NET 中搭配使用 Azure Key Vault 與虛擬機器 | Microsoft Docs
description: 在本教學課程中，您會設定 ASP.NET Core 應用程式以從金鑰保存庫讀取祕密。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b4f832750f7a94a6a60cbb1d3ba630925dd4fff2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021726"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>教學課程：在 .NET 中搭配使用 Azure Key Vault 與虛擬機器

Azure Key Vault 可協助您保護秘密，例如，API 金鑰、存取應用程式時所需的資料庫連接字串、服務和 IT 資源。

在本教學課程中，您將了解如何讓主控台應用程式從 Azure Key Vault 讀取資訊。 應用程式會使用虛擬機器受控識別，向 Key Vault 進行驗證。 

本教學課程說明如何：

> [!div class="checklist"]
> * 建立資源群組。
> * 建立金鑰保存庫。
> * 將秘密新增至金鑰保存庫。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure 虛擬機器。
> * 啟用虛擬機器的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)。
> * 對 VM 身分識別指派權限。

在開始之前，請先閱讀 [Key Vault 基本概念](basic-concepts.md)。 

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

若為 Windows、Mac 和 Linux：
  * [Git](https://git-scm.com/downloads)
  * [.NET Core 3.1 SDK 或更新版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
  * [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-resources-and-assign-permissions"></a>建立資源並指派權限

在您開始編碼之前，您必須建立一些資源，將祕密放入您的金鑰保存庫，以及指派權限。

### <a name="sign-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>建立資源群組和金鑰保存庫

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>將祕密填入金鑰保存庫

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>建立虛擬機器
使用下列其中一個方法來建立 Windows 或 Linux 虛擬機器：

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure 入口網站](../../virtual-machines/windows/quick-create-portal.md) | [Azure 入口網站](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>將身分識別指派給 VM
使用 [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 命令，為虛擬機器建立系統指派的身分識別：

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

請注意下列程式碼中所顯示的系統指派身分識別。 上述命令的輸出會是： 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>對 VM 身分識別指派權限
使用 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，對您的金鑰保存庫指派先前建立的身分識別權限：

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>登入虛擬機器

若要登入虛擬機器，請遵循[連線和登入 Azure Windows 虛擬機器](../../virtual-machines/windows/connect-logon.md)或[連線和登入 Azure Linux 虛擬機器](../../virtual-machines/linux/login-using-aad.md)中的指示。

## <a name="set-up-the-console-app"></a>設定主控台應用程式

使用 `dotnet` 命令來建立主控台應用程式並安裝必要的套件。

### <a name="install-net-core"></a>安裝 .NET Core

若要安裝 .NET Core，請移至 [.NET 下載](https://www.microsoft.com/net/download)頁面。

### <a name="create-and-run-a-sample-net-app"></a>建立和執行 .NET 應用程式範例

開啟命令提示字元。

您可以執行下列命令以在主控台中列印出「Hello World」：

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>安裝套件

從主控台視窗安裝適用於 .NET 的 Azure Key Vault 祕密用戶端程式庫：

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

對於本快速入門，您將需要安裝下列身分識別套件，向 Azure Key Vault 進行驗證：

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>編輯主控台應用程式

開啟 Program.cs 檔案並新增這些套件：

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

新增以下幾行，更新 URI 以反映金鑰保存庫的 `vaultUri`。 以下程式碼使用 ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) 來向金鑰保存庫進行驗證，這會使用來自應用程式受控識別的權杖進行驗證。 如果金鑰保存庫遭到節流，其也會使用指數輪詢來進行重試。

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

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
```

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除虛擬機器和金鑰保存庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
