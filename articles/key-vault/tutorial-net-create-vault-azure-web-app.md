---
title: 教學課程：在 .NET 中透過 Azure Web 應用程式使用 Azure Key Vault | Microsoft Docs
description: 在本教學課程中，您會設定 ASP.NET Core 應用程式以從金鑰保存庫讀取祕密。
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: e636ab843a9801097bf770ca12c9d1e512750c91
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198111"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>教學課程：在 .NET 中透過 Azure Web 應用程式使用 Azure Key Vault

Azure Key Vault 可協助您保護秘密，例如 API 金鑰和資料庫連接字串。 它可供您存取您的應用程式、服務和 IT 資源。

在本教學課程中，您會了解如何建立可從 Azure 金鑰保存庫讀取資訊的 Azure Web 應用程式。 此程序會使用 Azure 資源的受控識別。 如需有關 Azure Web 應用程式的詳細資訊，請參閱 [Azure App Service](../app-service/overview.md)。

本教學課程說明如何：

> [!div class="checklist"]
> * 建立金鑰保存庫。
> * 將秘密新增至金鑰保存庫。
> * 從金鑰保存庫擷取祕密。
> * 建立 Azure Web 應用程式。
> * 啟用 Web 應用程式的受控識別。
> * 指派 Web 應用程式的權限。
> * 在 Azure 上執行 Web 應用程式。

在開始之前，請先閱讀 [Key Vault 基本概念](basic-concepts.md)。 

如果您沒有 Azure 訂用帳戶，請建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* 若為 Windows：[.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/download/windows)
* 若為 Mac：[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* 若為 Windows、Mac 和 Linux：
  * [Git](https://git-scm.com/downloads)
  * 此教學課程需要您在本機執行 Azure CLI。 您必須安裝 Azure CLI 2.0.4 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級 CLI，請參閱[安裝 Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>關於受控服務識別

Azure Key Vault 可安全地儲存認證，因此認證不會在程式碼內顯示出來。 不過，您需要向 Azure Key Vault 驗證才能擷取您的金鑰。 若要向 Key Vault 進行驗證，您必須要有認證。 這是典型的啟動程序難題。 受控服務識別 (MSI) 可藉由提供可簡化此程序的「啟動程序身分識別」  來解決此問題。

當您針對 Azure 服務 (例如，Azure 虛擬機器、Azure App Service 或 Azure Functions) 啟用 MSI 時，Azure 會建立[服務主體](basic-concepts.md)。 MSI 會在 Azure Active Directory (Azure AD) 中為服務執行個體執行此作業，並將服務主體的認證插入該執行個體中。

![MSI 圖表](media/MSI.png)

接下來，為了取得存取權杖，您的程式碼會呼叫 Azure 資源上可用的本機中繼資料服務。 您的程式碼會使用從本機 MSI 端點取得的存取權杖，向 Azure Key Vault 服務進行驗證。

## <a name="log-in-to-azure"></a>登入 Azure

若要使用 Azure CLI 登入 Azure，請輸入：

```azurecli
az login
```

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是在其中部署與管理 Azure 資源的邏輯容器。

使用 [az group create](/cli/azure/group#az-group-create) 命令來建立資源群組。

然後，選取資源群組名稱，並填入預留位置。 下列範例會在美國西部位置建立一個資源群組：

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

您會在本教學課程中使用此資源群組。

## <a name="create-a-key-vault"></a>建立金鑰保存庫

若要在資源群組中建立金鑰保存庫，請提供下列資訊：

* 金鑰保存庫名稱：由 3 到 24 個字元組成的字串，只能包含數字 (0-9)、字母 (a-z、A-Z) 和連字號 (-)
* 資源群組名稱
* 位置：美國西部 

在 Azure CLI 中，輸入下列命令：

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行作業的帳戶。

## <a name="add-a-secret-to-the-key-vault"></a>將秘密新增至金鑰保存庫

您現在可以新增祕密。 這可以是 SQL 連接字串，或任何您必須安全保存並可供應用程式使用的其他資訊。

為了在金鑰保存庫中建立名為 **AppSecret** 的秘密，請輸入下列命令： 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

此秘密會儲存值 **MySecret**。

若要以純文字檢視祕密內含的值，請輸入下列命令：

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

此命令會顯示祕密資訊，包括 URI。 

完成這些步驟之後，您應該會有祕密在金鑰保存庫中的 URI。 請記下此資訊，以便稍後在本教學課程中使用。 

## <a name="create-a-net-core-web-app"></a>建立 .NET Core Web 應用程式

若要建立 .NET Core Web 應用程式並將其發佈至 Azure，請遵循[在 Azure 中建立 ASP.NET Core Web 應用程式](../app-service/app-service-web-get-started-dotnet.md)中的指示。 

您也可以觀看這段影片：

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>開啟及編輯解決方案

1. 移至 [頁面]   > [About.cshtml.cs]  檔案。

1. 安裝這些 NuGet 套件：
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. 將下列程式碼匯入到 About.cshtml.cs  檔案：

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   您在 AboutModel 類別中的程式碼應該會顯示如下：

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>執行 Web 應用程式

1. 在 Visual Studio 2019 的主功能表上，選取 [偵錯]   > [啟動]  (偵錯/不偵錯)。 
1. 在瀏覽器中，移至 [關於]  頁面。  
    **AppSecret** 的值隨即顯示。

## <a name="enable-a-managed-identity"></a>啟用受控識別

Azure Key Vault 可安全地儲存認證和其他祕密，但是您的程式碼必須向 Key Vault 進行驗證，才可取得這些項目。 [Azure 資源受控識別概觀](../active-directory/managed-identities-azure-resources/overview.md)可在 Azure AD 中將受控識別自動提供給 Azure 服務，協助解決此問題。 您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要在程式碼中顯示認證。

為了建立此應用程式的身分識別，請在 Azure CLI 中執行 assign-identity 命令：

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

將 \<YourAppName> 取代為 Azure 上已發佈應用程式的名稱。  
    例如，如果已發佈的應用程式名稱為 **MyAwesomeapp.azurewebsites.net**，請將 \<YourAppName> 取代為 **MyAwesomeapp**。

當您將應用程式發佈至 Azure 時，請記下 `PrincipalId`。 步驟 1 中命令的輸出格式應該如下：

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>此程序中的命令等同於前往 [Azure 入口網站](https://portal.azure.com)，並在 Web 應用程式屬性中將 [身分識別/系統指派]  設定切換為 [開啟]  。

## <a name="assign-permissions-to-your-app"></a>對您的應用程式指派權限

在下列命令中，將 \<YourKeyVaultName> 取代為您的金鑰保存庫名稱，並將 \<PrincipalId> 取代為 **PrincipalId** 的值：

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

此命令會賦予 App Service 的身分識別 (MSI) 權限，以在金鑰保存庫上執行**取得**與**列出**作業。

## <a name="publish-the-web-app-to-azure"></a>將 Web 應用程式發佈至 Azure

再次將 Web 應用程式發佈至 Azure，以確認即時 Web 應用程式可以擷取祕密值。

1. 在 Visual Studio 中，選取 **key-vault-dotnet-core-quickstart** 專案。
2. 選取 [發佈]   > [開始]  。
3. 選取 [建立]  。

當您執行應用程式時，您應會發現它可擷取您的秘密值。

現在，您已在 .NET 中成功建立 Web 應用程式，而該應用程式可對金鑰保存庫儲存和擷取祕密。

## <a name="clean-up-resources"></a>清除資源
不再需要時，您可以刪除虛擬機器和金鑰保存庫。

## <a name="next-steps"></a>後續步驟

>[!div class="nextstepaction"]
>[Azure Key Vault 開發人員指南](key-vault-developers-guide.md)
