---
title: .NET Framework 教學課程：Azure 應用程式組態中的動態設定
description: 在本教學課程，您將了解如何使用 Azure 應用程式組態，以動態方式更新 .NET Framework 應用程式的組態資料。
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 49ab9090dd2f1860bed84d0494cb862212840e82
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207044"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>教學課程：在 .NET Framework 應用程式中使用動態組態

應用程式組態 .NET 用戶端程式庫可支援依需求更新一組組態設定，而不會造成應用程式重新啟動。 若要實作此功能，請先從組態提供者的選項取得 `IConfigurationRefresher` 的執行個體，然後在程式碼任意處對該執行個體呼叫 `TryRefreshAsync`。

若要讓設定持續更新，並避免設定存放區有太多呼叫，每一項設定會使用一個快取。 在設定的快取值到期前，重新整理作業都不會更新值，即使值在設定存放區中已變更也是如此。 每個要求的預設到期時間為 30 秒，但您可以視需要加以覆寫。

本教學課程會示範如何在程式碼中實作動態設定更新。 本文會以快速入門中介紹的應用程式作為基礎。 繼續進行之前，請先完成[使用應用程式組態建立 .NET Framework 應用程式](./quickstart-dotnet-app.md)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 設定您的 .NET Framework 應用程式，使其在應用程式組態存放區發生變更時更新其組態。
> * 在您的應用程式中插入最新的組態。
## <a name="prerequisites"></a>Prerequisites

- Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 或更新版本](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>建立應用程式組態存放區

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. 選取 [組態總管]   > [+ 建立]   > [索引鍵/值]  以新增下列索引鍵/值組：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 |

    目前先讓 [標籤]  和 [內容類型]  保持空白。

7. 選取 [套用]  。

## <a name="create-a-net-framework-console-app"></a>建立.NET Framework 主控台應用程式

1. 啟動 Visual Studio，然後選取 [檔案]   > [新增]   > [專案]  。

1. 在 [建立新專案]  中，依照 [主控台]  專案類型篩選，然後按一下 [主控台應用程式 (.NET Framework)]  。 按 [下一步]  。

1. 在 [設定您的新專案]  中，輸入專案名稱。 在 [架構]  底下，選取 [.NET Framework 4.7.1]  或更高版本。 按一下頁面底部的 [新增]  。

## <a name="reload-data-from-app-configuration"></a>從應用程式設定重新載入資料
1. 以滑鼠右鍵按一下專案，然後選取 [管理 NuGet 套件]  。 在 [瀏覽]  索引標籤上，搜尋 *Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet 套件，並將其新增至您的專案。 如果您找不到該套件，請選取 [包含發行前版本]  核取方塊。

1. 開啟 Program.cs  ，並將參考新增至 .NET Core 應用程式組態提供者。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. 新增兩個變數來儲存組態相關物件。

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. 使用指定的重新整理選項，更新連線至應用程式組態的 `Main` 方法。

    ```csharp
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
    ```
    `ConfigureRefresh` 方法可用來指定相關設定，以用來在系統觸發重新整理作業時，使用應用程式組態存放區來更新組態資料。 您可以藉由在提供給 `AddAzureAppConfiguration` 方法的選項上呼叫 `GetRefresher` 方法來擷取 `IConfigurationRefresher` 的執行個體，而且此執行個體上的 `TryRefreshAsync` 方法可用來在程式碼中的任意處觸發重新整理作業。

    > [!NOTE]
    > 組態設定的預設快取到期時間為 30 秒，但可加以複寫，方法是在以引數形式傳遞至 `ConfigureRefresh` 方法的選項初始設定式上呼叫 `SetCacheExpiration` 方法。

1. 新增名為 `PrintMessage()` 的方法，以對應用程式組態中的組態資料觸發手動重新整理。

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>於本機建置並執行應用程式

1. 設定名為 **ConnectionString** 的環境變數，並將其設定為應用程式組態存放區的存取金鑰。 如果您使用 Windows 命令提示字元，請執行下列命令，然後重新啟動命令提示字元以讓變更生效：

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    如果您使用 Windows PowerShell，請執行下列命令：

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. 重新啟動 Visual Studio，以讓變更生效。 

1. 按 Ctrl + F5 以建置並執行主控台應用程式。

    ![本機應用程式啟動](./media/dotnet-app-run.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。 選取 [所有資源]  ，然後選取您在快速入門中建立的應用程式組態存放區執行個體。

1. 選取 [組態總管]  ，然後更新下列索引鍵的值：

    | Key | 值 |
    |---|---|
    | TestApp:Settings:Message | Azure 應用程式設定的值 - 已更新 |

1. 回到執行中的應用程式，按 Enter 鍵以觸發重新整理，並在命令提示字元或 PowerShell 視窗中列印更新後的值。

    ![本機應用程式重新整理](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > 快取到期時間已使用 `SetCacheExpiration` 方法設定為 10 秒，並已指定重新整理作業的設定，因此，只有在該設定自上次重新整理後已經過至少 10 秒才會更新該組態設定的值。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已啟用 .NET Framework 應用程式，以動態方式從應用程式組態重新整理組態設定。 若要了解如何使用 Azure 受控服務識別來簡化對應用程式組態的存取，請繼續進行下一個教學課程。

> [!div class="nextstepaction"]
> [受控識別整合](./howto-integrate-azure-managed-service-identity.md)
