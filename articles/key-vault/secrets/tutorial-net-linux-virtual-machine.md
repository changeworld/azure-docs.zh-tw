---
title: 教學課程 - 使用 Linux 虛擬機器和 ASP.NET 主控台應用程式將秘密儲存在 Azure Key Vault 中 | Microsoft Docs
description: 在本教學課程中，您將了解如何設定 ASP.NET Core 應用程式以從 Azure Key Vault 讀取祕密。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 50810dd1fbf2d97989df47b537ef5c574be059d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426167"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>教學課程：使用 Linux VM 和 .NET 應用程式將這些秘密儲存在 Azure Key Vault 中

Azure Key Vault 可協助您保護秘密，例如存取您的應用程式、服務和 IT 資源所需的 API 金鑰和資料庫連接字串。

在本教學課程中，您會設定 .NET 主控台應用程式，以使用 Azure 資源的受控識別從 Azure Key Vault 讀取資訊。 您會了解如何：

> [!div class="checklist"]
> * 建立金鑰保存庫
> * 將秘密儲存在 Key Vault 中
> * 建立 Azure Linux 虛擬機器
> * 啟用虛擬機器的[受控識別](../../active-directory/managed-identities-azure-resources/overview.md)
> * 授與主控台應用程式從 Key Vault 讀取資料所需的權限
> * 從 Key Vault 擷取祕密

在進一步討論之前，請先閱讀[金鑰保存庫的基本概念](../general/basic-concepts.md)。

## <a name="prerequisites"></a>Prerequisites

* [Git](https://git-scm.com/downloads)。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* [Azure CLI 2.0 或更新版本](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)或 Azure Cloud Shell。

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>了解受控服務識別

Azure Key Vault 可安全地儲存認證，因此認證不會在您的程式碼中，而是必須向 Azure Key Vault 進行驗證，才可加以擷取。 不過，若要向 Key Vault 進行驗證，您必須要有認證。 這是典型的啟動程序問題。 透過 Azure 和 Azure Active Directory (Azure AD)，受控服務識別 (MSI) 可提供啟動程序身分識別，讓您更輕鬆地開始執行工作。

當您為 Azure 服務 (例如虛擬機器、App Service 或 Functions) 啟用 MSI 時，Azure 會為 Azure Active Directory 中的服務執行個體建立服務主體。 Azure 會將服務主體的認證插入該服務的執行個體中。

![MSI](../media/MSI.png)

接下來，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務，以取得存取權杖。
您的程式碼會使用從本機 MSI_ENDPOINT 取得的存取權杖，向 Azure Key Vault 服務進行驗證。

## <a name="sign-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

使用 `az group create` 命令建立資源群組。 Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

在美國西部位置建立資源群組。 挑選您的資源群組名稱，並取代下列範例中的 `YourResourceGroupName`：

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

您在本教學課程中都將使用此資源群組。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

接著，在您的資源群組中建立金鑰保存庫。 請提供下列資訊：

* 金鑰保存庫名稱：長度介於 3 到 24 個字元，且只能包含數字、字母和連字號 (0-9、a-z、A-Z 和 \-) 的字串。
* 資源群組名稱
* 位置：美國西部 

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

此時，您的 Azure 帳戶是唯一獲得授權在這個新的保存庫上執行任何作業的帳戶。

## <a name="add-a-secret-to-the-key-vault"></a>將秘密新增至金鑰保存庫

現在，您可以新增祕密。 在實際的案例中，您可以儲存 SQL 連接字串，或任何您必須安全保存但可供應用程式使用的其他資訊。

在此教學課程中請輸入下列命令，以在金鑰保存庫中建立秘密。 此秘密名為 **AppSecret**，且其值為 **MySecret**。

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>建立 Linux 虛擬機器

使用 `az vm create` 命令建立 VM。

下列範例會建立名為 myVM  的 VM，並新增名為 azureuser  的使用者帳戶。 `--generate-ssh-keys` 參數可用來自動產生 SSH 金鑰，並將其置於預設金鑰位置 (~/.ssh  )。 若要改為使用一組特定金鑰，請使用 `--ssh-key-value` 選項。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

建立虛擬機器和支援資源需要幾分鐘的時間。 下列範例輸出顯示 VM 建立作業成功。

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

請從 VM 的輸出中記下您的 `publicIpAddress`。 您將在後續步驟中使用此位址來存取 VM。

## <a name="assign-an-identity-to-the-vm"></a>將身分識別指派給 VM

執行下列命令，對虛擬機器建立系統指派的身分識別：

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

命令的輸出應為：

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

記下 `systemAssignedIdentity`。 您在下一個步驟將會用到它。

## <a name="give-the-vm-identity-permission-to-key-vault"></a>為 Key Vault 授與 VM 身分識別權限

現在，您可以為 Key Vault 授與您所建立的身分識別的權限。 執行以下命令：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>登入 VM

使用終端機登入虛擬機器。

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>在 Linux 上安裝 .NET Core

在您的 Linux VM 上：

執行下列命令，將 Microsoft 產品金鑰註冊為可信任：

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

根據作業系統設定所需的版本裝載套件摘要：

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

安裝 .NET 並檢查版本：

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>建立和執行 .NET 應用程式範例

執行下列命令。 您應該會看到 "Hello World" 列印至主控台。

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>編輯主控台應用程式以擷取您的秘密

開啟 Program.cs 檔案並新增這些套件：

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

這是兩個步驟的程序，會變更類別檔案中，讓應用程式能夠存取金鑰保存庫中的祕密。

1. 從 VM 上的本機 MSI 端點擷取權杖，進而從 Azure Active Directory 擷取權杖。
1. 將權杖傳遞給 Key Vault 並擷取祕密。

   編輯類別檔案，使其包含下列程式碼：

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
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
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
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

現在，您已了解如何在執行於 Azure Linux 虛擬機器的 .NET 應用程式中執行 Azure Key Vault 的作業。

## <a name="clean-up-resources"></a>清除資源

請刪除不再需要的資源群組、虛擬機器和所有相關的資源。 若要這樣做，請選取 VM 的資源群組，然後選取 [刪除]  。

使用 `az keyvault delete` 命令刪除金鑰保存庫：

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
