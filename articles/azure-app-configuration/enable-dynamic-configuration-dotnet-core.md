---
title: 教學課程：使用 .NET Core 應用程式中的動態設定
titleSuffix: Azure App Configuration
description: 在本教學課程，您將了解如何以動態方式更新 .NET Core 應用程式的設定資料
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: aebe7772c673162d60f35d6a81725ba1452d16a8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207079"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>教學課程：使用 .NET Core 應用程式中的動態設定

應用程式設定 .NET Core 用戶端程式庫可支援依需求更新一組組態設定，而不會造成應用程式重新啟動。 若要實作此功能，請先從組態提供者的選項取得 `IConfigurationRefresher` 的執行個體，然後在程式碼任意處對該執行個體呼叫 `TryRefreshAsync`。

若要讓設定持續更新，並避免設定存放區有太多呼叫，每一項設定會使用一個快取。 在設定的快取值到期前，重新整理作業都不會更新值，即使值在設定存放區中已變更也是如此。 每個要求的預設到期時間為 30 秒，但您可以視需要加以覆寫。

本教學課程會示範如何在程式碼中實作動態設定更新。 本文會以快速入門中介紹的應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式設定建立 .NET Core 應用程式](./quickstart-dotnet-core-app.md)。

您可以使用任何程式碼編輯器來進行本教學課程中的步驟。 Windows、macOS 及 Linux 平台上都有提供的 [Visual Studio Code](https://code.visualstudio.com/) 是一個絕佳的選項。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定您的 .NET Core 應用程式，使其在應用程式組態存放區發生變更時更新其組態。
> * 在您的應用程式中使用最新的組態。

## <a name="prerequisites"></a>Prerequisites

若要進行本教學課程，請安裝 [.NET Core SDK](https://dotnet.microsoft.com/download)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>從應用程式設定重新載入資料

開啟 Program.cs  ，並更新該檔案來將參考新增至 `System.Threading.Tasks` 命名空間，以在 `AddAzureAppConfiguration` 方法中指定重新整理組態，並使用 `TryRefreshAsync` 方法觸發手動重新整理。

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

`ConfigureRefresh` 方法可用來指定相關設定，以用來在系統觸發重新整理作業時，使用應用程式組態存放區來更新組態資料。 您可以藉由在提供給 `AddAzureAppConfiguration` 方法的選項上呼叫 `GetRefresher` 方法來擷取 `IConfigurationRefresher` 的執行個體，而且此執行個體上的 `TryRefreshAsync` 方法可用來在程式碼任意處觸發重新整理作業。
    
> [!NOTE]
> 組態設定的預設快取到期時間為 30 秒，但可加以複寫，方法是在以引數形式傳遞至 `ConfigureRefresh` 方法的選項初始設定式上呼叫 `SetCacheExpiration` 方法。

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 設定名為 **ConnectionString** 的環境變數，並將其設定為應用程式組態存放區的存取金鑰。 如果您使用 Windows 命令提示字元，請執行下列命令，然後重新啟動命令提示字元以讓變更生效：

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 Windows PowerShell，請執行下列命令：

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 macOS 或 Linux，請執行下列命令：

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. 執行下列命令來建置主控台應用程式：

    ```console
     dotnet build
    ```

1. 建置成功完成後，請執行下列命令以於本機執行應用程式：

    ```console
     dotnet run
    ```

    ![快速入門應用程式啟動本機](./media/quickstarts/dotnet-core-app-run.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]  ，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

1. 選取 [組態總管]  ，然後更新下列索引鍵的值：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 - 已更新 |

1. 按 Enter 鍵來觸發重新整理，並在命令提示字元或 PowerShell 視窗中印出更新後的值。

    ![快速入門的本機應用程式重新整理](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > 快取到期時間已使用 `SetCacheExpiration` 方法設定為 10 秒，並已指定重新整理作業的設定，因此，只有在該設定自上次重新整理後已經過至少 10 秒才會更新該組態設定的值。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用 .NET Core 應用程式，以動態方式從應用程式組態重新整理組態設定。 若要了解如何使用 Azure 受控服務識別來簡化對應用程式組態的存取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
