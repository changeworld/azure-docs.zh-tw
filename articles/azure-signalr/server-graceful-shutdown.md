---
title: 正常停止您的應用程式伺服器。
description: 本文提供正常關機 SignalR 應用程式伺服器的相關資訊
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 06aa91ff414e5575f7b1a743d2cc17765437ef72
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797474"
---
# <a name="server-graceful-shutdown"></a>伺服器正常關機
Microsoft Azure SignalR Service 提供可正常關機伺服器的兩種模式。 

使用這項功能的主要優點是防止您的客戶發生意外的連接中斷。 

相反地，您可以等候用戶端連線關閉自己的商務邏輯，或甚至將用戶端連接遷移至另一部伺服器，而不會遺失資料。 

## <a name="how-it-works"></a>運作方式

一般來說，正常關機程式中會有四個階段：

1. **將伺服器設為離線**

    這表示不會再將用戶端連接路由至此伺服器。

2. **觸發 `OnShutdown` 程式攔截**

    您可以為您在伺服器中擁有的每個中樞註冊關機勾點。
    當我們從 Azure SignalR Service 取得 **FINACK** 回應之後，系統就會針對已註冊的訂單進行呼叫，這表示此伺服器已在 Azure SignalR Service 中設定為離線。

    您可以在這個階段廣播訊息或執行一些清除作業，一旦執行了所有關機勾點，我們將繼續進行下一個階段。

3. **等到所有用戶端連線完成**，視您選擇的模式而定，它可能是：

    **設定為 WaitForClientsToClose 的模式**

    Azure SignalR Service 將會保留現有的用戶端。

    您可能必須設計一種方法，例如將關閉訊息廣播到所有用戶端，然後讓您的用戶端決定何時要關閉/重新連接本身。

    閱讀 [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample) 以取得範例使用方式，我們會廣播「結束」訊息來觸發用戶端在關機攔截中關閉。

    **設定為 MigrateClients 的模式**

    Azure SignalR Service 會嘗試將這部伺服器上的用戶端連線重新路由傳送到另一個有效的伺服器。 
    
    在此情況下 `OnConnectedAsync` ， `OnDisconnectedAsync` 會在新的伺服器和舊的伺服器上，分別使用的 `IConnectionMigrationFeature` 集合來觸發，而 `HttpContext` 這可用來識別是否正在遷移用戶端連線-在我們遷移的環境中。這在具狀態的情況下特別有用。

    用戶端連接將會在目前的訊息傳遞之後立即遷移，這表示下一個訊息將會路由傳送至新的伺服器。

4. **停止伺服器連接**

    在所有用戶端連線都已關閉/遷移之後，或預設 (30 秒) 超過預設值時，

    SignalR Server SDK 會繼續進行此階段的關機程式，並關閉所有伺服器連接。

    如果無法關閉/遷移用戶端連接，仍會予以捨棄。 例如，沒有任何適用的目標伺服器/目前的用戶端對伺服器訊息尚未完成。

## <a name="sample-codes"></a>範例代碼。

當下列情況時，請新增下列選項 `AddAzureSignalR` ：

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>`OnConnected` `OnDisconnected` 設定正常關機模式並設定為 `MigrateClients` 。

我們引進了 "IConnectionMigrationFeature" 來指出連接是否正在遷移-in/out。

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```