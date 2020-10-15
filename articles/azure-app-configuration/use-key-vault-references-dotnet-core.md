---
title: 在 ASP.NET Core 應用程式中使用 Azure 應用程式組態 Key Vault 參考的教學課程 | Microsoft Docs
description: 在此教學課程中，您將了解如何從 ASP.NET Core 應用程式中使用 Azure 應用程式組態的 Key Vault 參考
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/08/2020
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 3e6403f41d8e4b52ca64e9fa452524fa25efe870
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213248"
---
# <a name="tutorial-use-key-vault-references-in-an-aspnet-core-app"></a>教學課程：在 ASP.NET Core 應用程式中使用 Key Vault 參考

在此教學課程中，您將了解如何搭配 Azure Key Vault 一起使用 Azure 應用程式組態服務。 應用程式組態與 Key Vault 是互補服務，將在大部分的應用程式部署中並行使用。

應用程式組態可協助您一起使用這些服務，方法是建立參考 Key Vault 中所儲存之值的金鑰。 當應用程式組態建立此類金鑰時，它會儲存 Key Vault 值的 URI，而不是值本身。

您的應用程式會使用應用程式組態用戶端提供者擷取 Key Vault 參考，就像是儲存在應用程式組態中的其他任何金鑰一樣。 在此案例中，儲存在應用程式組態中的值是參考 Key Vault 中之值的 URI。 它們不是 Key Vault 值或認證。 用戶端提供者會將金鑰視為 Key Vault 參考，因此它會使用 Key Vault 擷取其值。

您的應用程式會負責適當地驗證應用程式組態和 Key Vault。 這兩個服務不會直接通訊。

此教學課程說明如何在程式碼中實作 Key Vault 參考。 本文會以快速入門中介紹的 Web 應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式設定建立 ASP.NET Core 應用程式](./quickstart-aspnet-core-app.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 例如，[Visual Studio Code](https://code.visualstudio.com/) 是適用於 Windows、macOS 與 Linux 作業系統的跨平台程式碼編輯器。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立可參考 Key Vault 中儲存之值的應用程式組態金鑰。
> * 從 ASP.NET Core Web 應用程式存取此金鑰的值。

## <a name="prerequisites"></a>Prerequisites

開始此教學課程之前，請先安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>建立保存庫

1. 選取 Azure 入口網站左上角的 [建立資源]  選項：

    ![Key Vault 建立完成後的輸出](./media/quickstarts/search-services.png)
1. 在 [搜尋] 方塊中輸入 **Key Vault**。
1. 從結果清單，選取左側的 [金鑰保存庫]  。
1. 在 [金鑰保存庫]  中，選取 [新增]  。
1. 在 [建立金鑰保存庫]  的右側，提供下列資訊：
    - 選取 [訂用帳戶]  以選擇訂用帳戶。
    - 在 [資源群組]  中，選取 [新建]  ，然後輸入資源群組名稱。
    - 在 [金鑰保存庫名稱]  中，需要唯一的名稱。 針對此教學課程，請輸入 **Contoso-vault2**。
    - 在 [區域]  下拉式清單中，選擇一個位置。
1. 將其他 [建立金鑰保存庫]  選項維持為預設值。
1. 選取 [建立]  。

此時，您的 Azure 帳戶是唯一獲得授權可存取此新保存庫的帳戶。

![Key Vault 建立完成後的輸出](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>將祕密新增至 Key Vault

若要將祕密新增至保存庫，您只需要採取一些額外步驟。 在此案例中，請新增一則您可以用來測試 Key Vault 擷取的訊息。 此訊息稱為 **Message**，而您要將 "Hello from Key Vault" 的值儲存於其中。

1. 從 Key Vault 屬性頁面，選取 [祕密]  。
1. 選取 [產生/匯入]  。
1. 在 [建立祕密]  窗格中，輸入下列值：
    - **上傳選項**：輸入**手動**。
    - **Name**：輸入**訊息**。
    - **值**：輸入 **Hello from Key Vault**。
1. 保留其他 [建立祕密]  屬性的預設值。
1. 選取 [建立]  。

## <a name="add-a-key-vault-reference-to-app-configuration"></a>將 Key Vault 參考新增至應用程式組態

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]  ，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

1. 選取 [組態總管]  。

1. 選取 [+ 建立]   > [金鑰保存庫參考]  ，然後指定下列值：
    - **金鑰**：選取 **TestApp:Settings:KeyVaultMessage**。
    - **標籤**︰將此值保留空白。
    - **訂用帳戶**、**資源群組**與**金鑰保存庫**：輸入與您在上一節中建立之金鑰保存庫對應的值。
    - **祕密**：選取您在上一節中所建立、名為 **Message** 的祕密。

## <a name="connect-to-key-vault"></a>連線到 Key Vault

1. 在此教學課程中，您將使用服務主體來驗證 Key Vault。 若要建立此服務主體，請使用 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 命令：

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    此作業會傳回一系列的金鑰/值組：

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 執行下列命令，讓服務主體存取您的金鑰保存庫：

    ```cmd
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. 新增環境變數，以儲存 *clientId*、*clientSecret* 和 *tenantId* 的值。

    #### <a name="windows-command-prompt"></a>[Windows 命令提示字元](#tab/cmd)

    ```cmd
    setx AZURE_CLIENT_ID <clientId-of-your-service-principal>
    setx AZURE_CLIENT_SECRET <clientSecret-of-your-service-principal>
    setx AZURE_TENANT_ID <tenantId-of-your-service-principal>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    $Env:AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    $Env:AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AZURE_CLIENT_ID = <clientId-of-your-service-principal>
    export AZURE_CLIENT_SECRET = <clientSecret-of-your-service-principal>
    export AZURE_TENANT_ID = <tenantId-of-your-service-principal>
    ```

    ---

    > [!NOTE]
    > 這些 Key Vault 認證只會在您的應用程式中使用。 您的應用程式會使用這些認證直接驗證 Key Vault。 它們永遠不會傳遞至應用程式組態服務。

1. 重新啟動您的終端機，以載入這些新的環境變數。

## <a name="update-your-code-to-use-a-key-vault-reference"></a>更新您的程式碼以使用 Key Vault 參考

1. 執行下列命令，以新增對必要 NuGet 套件的參考：

    ```dotnetcli
    dotnet add package Azure.Identity
    ```

1. 開啟 *Program.cs*，並加入對下列必要套件的參考：

    ```csharp
    using Azure.Identity;
    ```

1. 藉由呼叫 `config.AddAzureAppConfiguration` 方法將 `CreateWebHostBuilder` 方法更新為使用應用程式設定。 包含 `ConfigureKeyVault` 選項，並將正確的認證傳遞給您的 Key Vault。

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                            .ConfigureKeyVault(kv =>
                            {
                                kv.SetCredential(new DefaultAzureCredential());
                            });
                });
            })
            .UseStartup<Startup>());
    ```

1. 當您初始化與應用程式組態的連線時，您藉由呼叫 `ConfigureKeyVault` 方法設定了與 Key Vault 的連線。 初始化之後，您就可以存取 Key Vault 參考的值，就像存取一般應用程式組態金鑰的值一樣。

    若要查看此程序的運作情形，請在 [檢視]   > [主目錄]  資料夾中開啟 *Index.cshtml*。 以下列程式碼取代其內容：

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]
        and @Configuration["TestApp:Settings:KeyVaultMessage"]</h1>
    ```

    您要以與組態值 **TestApp:Settings:Message** 相同的方式存取 Key Vault 參考的值 **TestApp:Settings:KeyVaultMessage**。

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 若要使用 .NET Core CLI 來建置應用程式，請在命令殼層中執行下列命令：

    ```dotnetcli
    dotnet build
    ```

1. 建置完成後，請使用下列命令在本機執行 Web 應用程式：

    ```dotnetcli
    dotnet run
    ```

1. 開啟瀏覽器視窗，並前往 `http://localhost:5000` (這是本機所裝載 Web 應用程式的預設 URL)。

    ![快速入門本機應用程式啟動](./media/key-vault-reference-launch-local.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在此教學課程中，您建立了參考 Key Vault 中儲存之值的應用程式組態金鑰。 若要了解如何新增可簡化對應用程式組態和 Key Vault 存取的 Azure 受控服務識別，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
