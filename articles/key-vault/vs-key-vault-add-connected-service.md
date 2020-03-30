---
title: 使用 Visual Studio 在 ASP.NET 專案中新增 Key Vault 支援 - Azure Key Vault | Microsoft Docs
description: 使用本教學課程，以協助您了解如何在 ASP.NET 或 ASP.NET Core Web 應用程式中新增 Key Vault 支援。
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 1c12cf4a7bd097c6d33d032065734b477920644b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457008"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>使用 Visual Studio 連線服務在 Web 應用程式中新增 Key Vault

在本教學課程中，您將了解如何輕鬆地新增所需的項目，以便開始在 Visual Studio 中使用 Azure Key Vault 來管理 Web 專案的祕密，而不論您使用的是 ASP.NET Core 或任何類型的 ASP.NET 專案。 通過使用 Visual Studio 中的"已連接服務"功能，您可以讓 Visual Studio 自動添加連接到 Azure 中金鑰保存庫所需的所有 NuGet 包和配置設置。

針對「連線服務」在您專案中為啟用 Key Vault 所做的變更，如需詳細資料，請參閱 [Key Vault 連線服務 - 我的 ASP.NET 4.7.1 專案發生什麼情形](#how-your-aspnet-framework-project-is-modified)或 [Key Vault 連線服務 - 我的 ASP.NET Core 專案發生什麼情形](#how-your-aspnet-core-project-is-modified)。

## <a name="prerequisites"></a>Prerequisites

- **Azure 訂閱**。 如果您沒有訂閱，請註冊[一個免費帳戶](https://azure.microsoft.com/pricing/free-trial/)。
- **Visual Studio 2019 版本 16.3**或更高版本，或安裝**Web 開發**工作負載的 Visual **Studio 2017 版本 15.7。** [立即下載](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)。
- 對於 Visual Studio 2017 的ASP.NET（不是核心），您需要 .NET 框架 4.7.1 或更高版本的開發工具，這些工具預設未安裝。 若要安裝它們，請啟動 Visual Studio 安裝程式、選擇 [修改]****，然後選擇 [個別元件]****，接著在右側展開 [ASP.NET 與網頁程式開發]****，並選擇 [.NET Framework 4.7.1 開發工具]****。
- ASP.NET 4.7.1 或更高版本，或ASP.NET酷睿 2.0 或更高版本的 Web 專案打開。

## <a name="add-key-vault-support-to-your-project"></a>在專案中新增 Key Vault 支援

開始之前，請確保您已登錄到視覺化工作室。 使用用於 Azure 訂閱的相同帳戶登錄。 然後打開ASP.NET 4.7.1 或更高版本，或ASP.NET Core 2.0 Web 專案，然後執行以下步驟：

1. 在**解決方案資源管理器**中，按右鍵要向其添加金鑰保存庫支援的專案，然後選擇"**添加** > **連接的服務**"。
   [連線服務] 頁面隨即出現，並顯示您可新增至專案的服務。
1. 在可用服務的功能表中，選擇 [運用 Azure Key Vault 保護祕密的安全]****。

   ![選擇 [運用 Azure Key Vault 保護祕密的安全]](media/vs-key-vault-add-connected-service/KeyVaultConnectedService1.PNG)

1. 選擇要使用的訂閱，然後選擇新的金鑰保存庫或現有金鑰保存庫。 如果選擇新的金鑰保存庫，將顯示 **"編輯"** 連結。 選擇它來配置新的金鑰保存庫。

   ![選取您的訂用帳戶](media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

1. 在 **"編輯 Azure 金鑰保存庫"** 中，輸入要用於金鑰保存庫的名稱。

1. 選擇現有**資源組**，或選擇創建具有自動生成的唯一名稱的新資源組。  如果要創建具有其他名稱的新組，可以使用[Azure 門戶](https://portal.azure.com)，然後關閉頁面並重新啟動以重新載入資源組的清單。
1. 選擇在其中創建金鑰保存庫**的位置**。 如果您的 Web 應用程式裝載於 Azure 中，請選擇裝載該 Web 應用程式的區域以獲得最佳效能。
1. 選擇**定價層**。 如需詳細資訊，請參閱 [Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)。
1. 選擇 **"確定"** 以接受配置選項。
1. 選擇現有金鑰保存庫或配置了新的金鑰保存庫後，在 Visual Studio 的**Azure 金鑰保存庫**選項卡中，選擇 **"添加"** 以添加連接的服務。
1. 選擇 **"管理存儲在此金鑰保存庫"連結中的秘密**，以打開金鑰保存庫**的"機密"** 頁。 如果關閉了頁面或專案，則可以通過選擇 **"所有服務"** 並在[Azure 門戶](https://portal.azure.com)中導航到該頁面，然後在 **"安全**"下選擇**金鑰保存庫**，然後選擇金鑰保存庫。
1. 在您創建的關鍵保存庫的"金鑰保存庫"部分中，選擇 **"機密**"，然後**生成/導入**。

   ![產生/匯入祕密](media/vs-key-vault-add-connected-service/azure-generate-secrets.png)

1. 輸入機密（如*MySecret）* 並將其任何字串值作為測試，然後選擇 **"創建**"按鈕。

   ![建立祕密](media/vs-key-vault-add-connected-service/azure-create-a-secret.png)

1. (選擇性) 輸入其他祕密，但此時請將它命名為 *Secrets--MySecret*，藉以將它放置於分類中。 此語法指定包含機密"MySecret"的類別"機密"。

現在，您可透過程式碼存取祕密。 後續步驟會根據您使用的是 ASP.NET 4.7.1 或 ASP.NET Core 而有所差異。

## <a name="access-your-secrets-in-code-aspnet-core"></a>在代碼中訪問您的機密（ASP.NET核心）

1. 在解決方案資源管理器中，按右鍵專案，然後選擇 **"管理 NuGet 包**"。 在 **"流覽"** 選項卡中，查找並安裝這兩個 NuGet 包[：Microsoft.Azure.Services.App 身份驗證](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)和 .NET 核心 2，添加[Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)或 .NET 核心 3，添加[Microsoft.Azure.KeyVault.Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core)。

1. 對於 .NET 核心 2，選擇`Program.cs`選項卡並將`BuildWebHost`程式類中的定義更改為以下內容：

   ```csharp
        public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration((ctx, builder) =>
               {
                   var keyVaultEndpoint = GetKeyVaultEndpoint();
                   if (!string.IsNullOrEmpty(keyVaultEndpoint))
                   {
                       var azureServiceTokenProvider = new AzureServiceTokenProvider();
                       var keyVaultClient = new KeyVaultClient(
                           new KeyVaultClient.AuthenticationCallback(
                               azureServiceTokenProvider.KeyVaultTokenCallback));
                       builder.AddAzureKeyVault(
                           keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                   }
               }
            ).UseStartup<Startup>();

        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    }
   ```

   對於 .NET 核心 3，請使用以下代碼。

   ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureAppConfiguration((context, config) =>
                {
                    var keyVaultEndpoint = GetKeyVaultEndpoint();
                    if (!string.IsNullOrEmpty(keyVaultEndpoint))
                    {
                        var azureServiceTokenProvider = new AzureServiceTokenProvider();
                        var keyVaultClient = new KeyVaultClient(
                            new KeyVaultClient.AuthenticationCallback(
                                azureServiceTokenProvider.KeyVaultTokenCallback));
                        config.AddAzureKeyVault(keyVaultEndpoint, keyVaultClient, new DefaultKeyVaultSecretManager());
                    }
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        private static string GetKeyVaultEndpoint() => "https://<YourKeyVaultName>.vault.azure.net";
    ```

1. 接下來打開其中一個分頁檔，如*Index.cshtml.cs*並編寫以下代碼：
   1. 包含此使用指令`Microsoft.Extensions.Configuration`的引用：

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. 添加組態變數。

      ```csharp
      private static readonly IConfiguration _configuration;
      ```

   1. 添加此建構函式或用以下替換現有建構函式：

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. 更新 `OnGet` 方法。 使用在上述命令中創建的秘密名稱更新此處顯示的預留位置值。

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameThatWasCreatedAbove>"];
       }
       ```

   1. 要在運行時確認該值，請添加要顯示`ViewData["Message"]`到 *.cshtml*檔的代碼以在消息中顯示機密。

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

您可以在本地運行應用，以驗證金鑰是否已成功從金鑰保存庫獲取。

## <a name="access-your-secrets-aspnet"></a>訪問您的機密（ASP.NET）

您可以設置配置，以便 Web.config 檔在`appSettings`元素中具有一個虛擬值，在運行時由真實值替換。 然後，您可以通過`ConfigurationManager.AppSettings`資料結構訪問此。

1. 編輯 Web.config 檔。  查找應用設置標記、添加屬性`configBuilders="AzureKeyVault"`和添加行：

   ```xml
      <add key="mysecret" value="dummy"/>
   ```

1. 在`About`*HomeController.cs*中編輯方法，以顯示確認值。

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["mysecret"];
   }
   ```
1. 在調試器下本地運行應用，切換到 **"關於**"選項卡，並驗證顯示來自金鑰保存庫的值。

## <a name="clean-up-resources"></a>清除資源

不再需要資源群組時，請加以刪除。 這會刪除 Key Vault 及相關資源。 若要透過入口網站刪除資源群組：

1. 在入口網站頂端的 [搜尋] 方塊中，輸入資源群組的名稱。 當您在搜尋結果中看到本快速入門中使用的資源群組時，請加以選取。
2. 選取 [刪除資源群組]****。
3. 在 **"資源組名稱類型："** 框中，輸入資源組的名稱，然後選擇 **"刪除**"。

## <a name="troubleshooting"></a>疑難排解

如果您的金鑰保存庫運行在與您登錄到 Visual Studio 的帳戶不同的 Microsoft 帳戶上（例如，金鑰保存庫在您的工作帳戶上運行，但 Visual Studio 正在使用您的私人帳戶），則Program.cs檔中會收到錯誤，該視覺化工作室無法訪問金鑰保存庫。 若要修正此問題：

1. 轉到[Azure 門戶](https://portal.azure.com)並打開金鑰保存庫。

1. 選擇**訪問策略**，然後**添加訪問策略**，然後選擇您作為主體登錄的帳戶。

1. 在視覺化工作室中，選擇 **"檔** > **帳戶設置**"。
從"**全部帳戶**"部分**選擇"添加帳戶**"。 使用您選擇的帳戶登錄，作為訪問策略的主體。

1. 選擇**工具** > **選項**，然後查找**Azure 服務身份驗證**。 然後選擇您剛剛添加到視覺化工作室的帳戶。

現在，當您調試應用程式時，Visual Studio 會連接到金鑰保存庫所在的帳戶。

## <a name="how-your-aspnet-core-project-is-modified"></a>如何修改ASP.NET核心專案

本節標識使用 Visual Studio 添加金鑰保存庫連接服務時對ASP.NET專案所做的準確更改。

### <a name="added-references-for-aspnet-core"></a>添加了ASP.NET核心的引用

影響專案檔案 .NET 引用和 NuGet 包引用。

| 類型 | 參考資料 |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

### <a name="added-files-for-aspnet-core"></a>添加了ASP.NET核心的檔

- `ConnectedService.json`添加了，它記錄有關已連接服務提供者、版本和連結文檔的一些資訊。

### <a name="project-file-changes-for-aspnet-core"></a>ASP.NET核心的專案檔案更改

- 添加了"已連結的服務項組`ConnectedServices.json`"和"檔"。

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>ASP.NET核心的啟動設置.json 更改

- 已將下列環境變數項目新增至 IIS Express 設定檔及符合您 Web 專案名稱的設定檔：

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>ASP.NET核心的 Azure 上的更改

- 已建立資源群組 (或使用了現有的資源群組)。
- 已在指定的資源群組中建立 Key Vault。

## <a name="how-your-aspnet-framework-project-is-modified"></a>如何修改ASP.NET框架專案

本節標識使用 Visual Studio 添加金鑰保存庫連接服務時對ASP.NET專案所做的準確更改。

### <a name="added-references-for-aspnet-framework"></a>添加了ASP.NET框架的引用

影響專案檔案 .NET 引用`packages.config`和 （NuGet 引用）。

| 類型 | 參考資料 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

### <a name="added-files-for-aspnet-framework"></a>為ASP.NET框架添加了檔

- `ConnectedService.json`添加了，它記錄有關連接服務提供者、版本和文檔連結的一些資訊。

### <a name="project-file-changes-for-aspnet-framework"></a>ASP.NET框架的專案檔案更改

- 已新增連線服務 ItemGroup 和 ConnectedServices.json 檔案。
- 對[新增的參考](#added-references-for-aspnet-framework)一節中所述 .NET 組件的參考。

### <a name="webconfig-or-appconfig-changes"></a>web.config 或 app.config 的變更

- 已新增下列組態項目：

    ```xml
    <configSections>
      <section
           name="configBuilders"
           type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
           restartOnExternalChanges="false"
           requirePermission="false" />
    </configSections>
    <configBuilders>
      <builders>
        <add
             name="AzureKeyVault"
             vaultName="vaultname"
             type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
             vaultUri="https://vaultname.vault.azure.net" />
      </builders>
    </configBuilders>
    ```

### <a name="changes-on-azure-for-aspnet-framework"></a>ASP.NET框架的 Azure 上的更改

- 已建立資源群組 (或使用了現有的資源群組)。
- 已在指定的資源群組中建立 Key Vault。

## <a name="next-steps"></a>後續步驟

如果遵循本教程，金鑰保存庫許可權將設置為使用自己的 Azure 訂閱運行，但對於生產方案來說，這可能不可取。 您可以創建託管標識來管理應用的金鑰保存庫存取權限。 請參閱[使用託管標識提供金鑰保存庫身份驗證](/azure/key-vault/managed-identity)。

通過閱讀[金鑰保存庫開發人員指南](key-vault-developers-guide.md)，瞭解有關金鑰保存庫開發的更多內容。
