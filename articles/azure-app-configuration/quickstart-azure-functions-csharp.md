---
title: Azure 應用程式設定搭配 Azure Functions 的快速入門 | Microsoft Docs
description: 在本快速入門中，使用 Azure 應用程式組態和 C# 建立 Azure Functions 應用程式。 建立並連線至應用程式組態存放區。 在本機測試函式。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: b5c659a673ece8fd7fbb9566d8bb84201a668a7f
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964077"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>快速入門：使用 Azure 應用程式組態建立 Azure Functions 應用程式

在本快速入門中，您會將 Azure 應用程式組態服務納入 Azure Functions 應用程式中，以集中儲存和管理您所有的應用程式設定 (與您的程式碼分開)。

## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/dotnet)
- 包含 **Azure 開發** 工作負載的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)。
- [Azure Functions 工具](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. 選取 [組態總管] > [+ 建立] > [索引鍵/值] 以新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 |

    目前先讓 [標籤]  和 [內容類型]  保持空白。

8. 選取 [套用]  。

## <a name="create-a-functions-app"></a>建立 Functions 應用程式

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>連線至應用程式組態存放區
此專案會[在 .NET Azure Functions 中使用相依性插入](/azure/azure-functions/functions-dotnet-dependency-injection)，並新增 Azure 應用程式組態作為額外的組態來源。

1. 以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]  。 在 [瀏覽] 索引標籤上，搜尋下列 NuGet 套件並新增至您的專案。
   - [Microsoft.Extensions.Configuration.AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) 4.1.0 版或更新版本
   - [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) 1.1.0 版或更新版本 

2. 使用下列程式碼，新增 Startup.cs 檔案。 其會定義名為 `Startup` 的類別，以實作 `FunctionsStartup` 抽象類別。 元件屬性用來指定 Azure Functions 啟動期間所使用的類型名稱。

    藉由呼叫 `AddAzureAppConfiguration()`，覆寫 `ConfigureAppConfiguration` 方法，並將 Azure 應用程式組態提供者新增為額外的組態來源。 `Configure` 方法會保留空白，因為您目前不需要註冊任何服務。
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. 開啟 Function1.cs，並新增下列命名空間。

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   新增用於透過相依性插入取得 `IConfiguration` 執行個體的建構函式。

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. 更新 `Run` 方法，以讀取組態中的值。

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   `Function1` 類別和 `Run` 方法不得為靜態。 如果已自動產生 `static` 修飾元，請予以移除。

## <a name="test-the-function-locally"></a>在本機測試函式

1. 設定名為 **ConnectionString** 的環境變數，並將其設定為應用程式組態存放區的存取金鑰。 如果您使用 Windows 命令提示字元，請執行下列命令，然後重新啟動命令提示字元以讓變更生效：

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 Windows PowerShell，請執行下列命令：

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 macOS 或 Linux，請執行下列命令：

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. 按 F5 測試您的函式。 如果出現提示，請接受 Visual Studio 所發出要下載及安裝 **Azure Functions Core (CLI)** 工具的要求。 您可能也需要啟用防火牆例外狀況，工具才能處理 HTTP 要求。

3. 從 Azure Functions 執行階段輸出複製函式的 URL。

    ![VS 中的函式偵錯快速入門](./media/quickstarts/function-visual-studio-debugging.png)

4. 將 HTTP 要求的 URL 貼到瀏覽器的網址列。 下圖顯示瀏覽器中對於函式傳回之本機 GET 要求所做出的回應。

    ![快速入門函式啟動本機](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立新的應用程式組態存放區，並透過[應用程式組態提供者](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)將其與 Azure Functions 應用程式搭配使用。 若要了解如何將 Azure Functions 應用程式更新為以動態方式重新整理組態，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [啟用 Azure Functions 中的動態組態](./enable-dynamic-configuration-azure-functions-csharp.md)