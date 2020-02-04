---
title: 教學課程 - 在 .NET 中搭配使用 Azure Key Vault 與 Windows 虛擬機器 | Microsoft Docs
description: 在本教學課程中，您會設定 ASP.NET Core 應用程式以從金鑰保存庫讀取祕密。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 5082ed06b4ce5baf3869fc035654be3c7a45f29f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845300"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>教學課程：在 .NET 中搭配使用 Azure Key Vault 與 Windows 虛擬機器

Azure Key Vault 可協助您保護秘密，例如，API 金鑰、存取應用程式時所需的資料庫連接字串、服務和 IT 資源。

在本教學課程中，您將了解如何讓主控台應用程式從 Azure Key Vault 讀取資訊。 若要這樣做，請使用 Azure 資源的受控識別。 

本教學課程說明如何：

> [!div class="checklist"]
> * 建立資源群組。
> * 建立金鑰保存庫。
> * 將秘密新增至金鑰保存庫。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure 虛擬機器。
> * 啟用虛擬機器的[受控識別](../active-directory/managed-identities-azure-resources/overview.md)。
> * 對 VM 身分識別指派權限。

在開始之前，請先閱讀 [Key Vault 基本概念](basic-concepts.md)。 

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若為 Windows、Mac 和 Linux：
  * [Git](https://git-scm.com/downloads)
  * 此教學課程需要您在本機執行 Azure CLI。 您必須安裝 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI 2.0](https://review.docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="about-managed-service-identity"></a>關於受控服務識別

Azure Key Vault 可安全地儲存認證，因此認證不會在程式碼內顯示出來。 不過，您需要向 Azure Key Vault 驗證才能擷取您的金鑰。 若要向 Key Vault 進行驗證，您必須要有認證。 這是典型的啟動程序難題。 受控服務識別 (MSI) 可藉由提供可簡化此程序的「啟動程序身分識別」  來解決此問題。

當您針對 Azure 服務 (例如，Azure 虛擬機器、Azure App Service 或 Azure Functions) 啟用 MSI 時，Azure 會建立[服務主體](basic-concepts.md)。 MSI 會在 Azure Active Directory (Azure AD) 中為服務執行個體執行此作業，並將服務主體的認證插入該執行個體中。 

![MSI](media/MSI.png)

接下來，為了取得存取權杖，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務。 為了向 Azure Key Vault 服務進行驗證，您的程式碼會使用從本機 MSI 端點取得的存取權杖。 

## <a name="create-resources-and-assign-permissions"></a>建立資源並指派權限

在您開始編碼之前，您必須建立一些資源，將祕密放入您的金鑰保存庫，以及指派權限。

### <a name="sign-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

### <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。 使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。 

此範例會建立位於美國西部位置的資源群組：

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

您會在本教學課程中使用新建立的資源群組。

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>建立金鑰保存庫，並且以祕密填入

藉由提供 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 命令以及下列資訊，在您的資源群組中建立金鑰保存庫：

* 金鑰保存庫名稱：由 3 到 24 個字元組成的字串，只能包含數字 (0-9)、字母 (a-z、A-Z) 和連字號 (-)
* 資源群組名稱
* 位置：美國西部 

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
此時，您的 Azure 帳戶是唯一獲得授權在這個新的金鑰保存庫上執行作業的帳戶。

現在使用 [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) 命令將祕密新增至您的金鑰保存庫


為了在金鑰保存庫中建立名為 **AppSecret** 的秘密，請輸入下列命令：

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

此秘密會儲存值 **MySecret**。

### <a name="create-a-virtual-machine"></a>建立虛擬機器
使用下列其中一個方法來建立虛擬機器：

* [Azure CLI](../virtual-machines/windows/quick-create-cli.md)
* [PowerShell](../virtual-machines/windows/quick-create-powershell.md)
* [Azure 入口網站](../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>將身分識別指派給 VM
使用 [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 命令，為虛擬機器建立系統指派的身分識別：

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

請注意下列程式碼中所顯示的系統指派身分識別。 上述命令的輸出會是： 

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>對 VM 身分識別指派權限
使用 [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 命令，對您的金鑰保存庫指派先前建立的身分識別權限：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>登入虛擬機器

若要登入虛擬機器，請遵循[連接和登入執行 Windows 的 Azure 虛擬機器](../virtual-machines/windows/connect-logon.md)中的指示。

## <a name="set-up-the-console-app"></a>設定主控台應用程式

使用 `dotnet` 命令來建立主控台應用程式並安裝必要的套件。

### <a name="install-net-core"></a>安裝 .NET Core

若要安裝 .NET Core，請移至 [.NET 下載](https://www.microsoft.com/net/download)頁面。

### <a name="create-and-run-a-sample-net-app"></a>建立和執行 .NET 應用程式範例

開啟命令提示字元。

您可以執行下列命令以在主控台中列印出「Hello World」：

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

### <a name="install-the-packages"></a>安裝套件

 從主控台視窗中，安裝本快速入門所需的 .NET 套件：

 ```console
dotnet add package System.IO;
dotnet add package System.Net;
dotnet add package System.Text;
dotnet add package Newtonsoft.Json;
dotnet add package Newtonsoft.Json.Linq;
```

## <a name="edit-the-console-app"></a>編輯主控台應用程式

開啟 Program.cs  檔案並新增這些套件：

```csharp
using System;
using System.IO;
using System.Net;
using System.Text;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

編輯類別檔案，使其包含下列三步驟程序中的程式碼：

1. 從 VM 上的本機 MSI 端點擷取權杖。 這樣做也會從 Azure AD 擷取權杖。
2. 將權杖傳遞給金鑰保存庫，然後擷取祕密。 
3. 將保存庫名稱和密碼名稱新增至要求。

```csharp
 class Program
    {
        static void Main(string[] args)
        {
            // Step 1: Get a token from the local (URI) Managed Service Identity endpoint, which in turn fetches it from Azure AD
            var token = GetToken();

            // Step 2: Fetch the secret value from your key vault
            System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
        }

        static string GetToken()
        {
            WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
            request.Headers.Add("Metadata", "true");
            WebResponse response = request.GetResponse();
            return ParseWebResponse(response, "access_token");
        }
        
        static string FetchSecretValueFromKeyVault(string token)
        {
            //Step 3: Add the vault name and secret name to the request.
            WebRequest kvRequest = WebRequest.Create("https://<YourVaultName>.vault.azure.net/secrets/<YourSecretName>?api-version=2016-10-01");
            kvRequest.Headers.Add("Authorization", "Bearer "+  token);
            WebResponse kvResponse = kvRequest.GetResponse();
            return ParseWebResponse(kvResponse, "value");
        }

        private static string ParseWebResponse(WebResponse response, string tokenName)
        {
            string token = String.Empty;
            using (Stream stream = response.GetResponseStream())
            {
                StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                String responseString = reader.ReadToEnd();

                JObject joResponse = JObject.Parse(responseString);    
                JValue ojObject = (JValue)joResponse[tokenName];             
                token = ojObject.Value.ToString();
            }
            return token;
        }
    }
```

上述程式碼會示範如何在 Windows 虛擬機器中使用 Azure Key Vault 執行作業。

## <a name="clean-up-resources"></a>清除資源

不再需要時，請刪除虛擬機器和金鑰保存庫。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
