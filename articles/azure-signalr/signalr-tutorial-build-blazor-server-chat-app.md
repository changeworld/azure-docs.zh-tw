---
title: 教學課程：建置 Blazor Server 聊天應用程式 - Azure SignalR
description: 在本教學課程中，您將了解如何使用 Azure SignalR Service 來建置和修改 Blazor Server 應用程式
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: 16fd15a5939cc6c268a80e88401f05042a206075
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516810"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>教學課程：建置 Blazor Server 聊天應用程式

本教學課程說明如何建置和修改 Blazor Server 應用程式。 您將學習如何：

> [!div class="checklist"]
> * 使用 Blazor Server 應用程式建立簡單的聊天室。
> * 修改 Razor 元件。
> * 使用元件中的事件處理和資料繫結。
> * 快速部署至 Visual Studio 中的 Azure App Service。
> * 將本機 SignalR 遷移至 Azure SignalR Service。

## <a name="prerequisites"></a>必要條件
* 安裝 [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (版本 >= 3.0.100)
* 安裝 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (版本 >= 16.3)
> Visual Studio 2019 預覽版本也適用，此版本隨附以較新的 .Net Core 版本為目標的最新 Blazor Server 應用程式範本。

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>在 Blazor Server 應用程式中建置本機聊天室

從 Visual Studio 2019 16.2.0 版開始，Azure SignalR Service 為內建的 Web 應用程式發佈程序，您在管理 Web 應用程式與 SignalR Service 之間的相依性時將更為便利。 您可以在開發人員本機環境中使用本機 SignalR，並同時使用 Azure App Service 的 Azure SignalR Service，而無須變更任何程式碼。

1. 建立聊天 Blazor 應用程式
   
   在 Visual Studio 中，選擇 [建立新專案] -> [Blazor 應用程式] -> (命名應用程式並選擇資料夾) -> [Blazor Server 應用程式]。 請確定您已安裝 .NET Core SDK 3.0 +，讓 Visual Studio 能夠正確辨識目標 Framework。

   [ ![blazor-chat-create](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   或執行 cmd
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. 新增 `BlazorChatSampleHub.cs` 檔案，以實作聊天的 `Hub`。
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. 在 `Startup.Configure()` 中新增中樞的端點。
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. 安裝 `Microsoft.AspNetCore.SignalR.Client` 套件以使用 SignalR 用戶端。

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. 在 `Pages` 資料夾下建立 `ChatRoom.razor`，以實作 SignalR 用戶端。 請依照下列步驟操作，或直接複製 [ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor)。

   1. 新增頁面連結和參考。
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. 將程式碼新增至新的 SignalR 用戶端以傳送和接收訊息。
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. 在 `@code` 前面新增轉譯組件，讓 UI 與 SignalR 用戶端互動。
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. 更新 `NavMenu.razor` 以在 `NavMenuCssClass` (例如 rest) 下插入聊天室的輸入功能表。

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. 更新 `site.css` 以最佳化聊天區域的泡泡圖檢視。 在結尾處附加下列程式碼。

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. 按一下 <kbd>F5</kbd> 以執行應用程式。 您將能夠以如下方式聊天。

   [ ![blazor-chat](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>發佈至 Azure

   到目前為止，Blazor 應用程式均使用本機 SignalR，而在部署至 Azure App Service 時，建議您使用 [Azure SignalR Service](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service)，這樣可讓您將 Blazor Server 應用程式擴大為大量的並行 SignalR 連線。 此外，SignalR Service 的全球姓和高效能資料中心可大幅縮短因地理位置而造成的延遲。

> [!IMPORTANT]
> 在 Blazor Server 應用程式中，UI 狀態會在伺服器端維護，這表示在此情況下需要伺服器黏性。 如果有單一應用程式伺服器，則可藉由設計來確保伺服器黏性。 不過，如果有多個應用程式伺服器，則用戶端的交涉和連線有可能會通往不同的伺服器，而導致 Blazor 應用程式中發生 UI 錯誤。 因此，您必須在 `appsettings.json`中啟用如下的伺服器黏性：
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. 以滑鼠右鍵按一下專案，然後瀏覽至 `Publish`。

   * 目標：Azure
   * 特定目標：所有類型的 **Azure App Service** 均受支援。
   * App Service：建立新的 App Service，或選取現有的 App Service。

   [ ![blazor-chat-profile](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. 新增 Azure SignalR Service 相依性

   發行設定檔建立後，您可以在 [服務相依性] 底下看到建議的訊息。 按一下 [設定] 以建立新的服務，或在面板中選取現有的 Azure SignalR Service。

   [ ![blazor-chat-dependency](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   服務相依性將會執行下列動作，讓您的應用程式在 Azure 上自動切換至 Azure SignalR Service。

   * 更新 [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) 以使用 Azure SignalR Service。
   * 新增 Azure SignalR Service NuGet 套件參考。
   * 更新設定檔屬性以儲存相依性設定。
   * 根據您的選擇設定秘密存放區。
   * 新增 `appsettings` 設定，讓您的應用程式以選取的 Azure SignalR Service 作為目標。

   [ ![blazor-chat-dependency-summary](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. 發佈應用程式

   現已做好發佈的準備。 它會在發佈完成後自動瀏覽頁面。 
   > [!NOTE]
   > 由於 Azure App Service 的部署啟動有延遲性，在第一次瀏覽頁面時可能無法立即運作，且會嘗試重新整理頁面以提供一些延遲。
   > 此外，您還可以透過 <kbd>F12</kbd> 使用瀏覽器偵錯工具模式，以驗證流量已重新導向至 Azure SignalR Service。

   [ ![blazor-chat-azure](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>深入主題：在本機開發中啟用 Azure SignalR Service

1. 新增 Azure SignalR SDK 的參考

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. 在 `Startup.ConfigureServices()` 中新增對 Azure SignalR Service 的呼叫。

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. 在 `appsetting.json` 中或使用[祕密管理員](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager)工具來設定 Azure SignalR Service `ConnectionString`

> [!NOTE]
> 您可以對 SignalR SDK 使用 [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) 以取代步驟 2。
> 
> 1. 新增在 `appsetting.json` 中開啟 Azure SignalR Service 的設定
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ServerStickyMode": "Required",
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. 指派裝載啟動組件以使用 Azure SignalR SDK。 編輯 `launchSettings.json`，並在 `environmentVariables` 內新增如下的設定。
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[有任何問題嗎？請告訴我們。](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>清除資源

若要清除在本教學課程中建立的資源，請使用 Azure 入口網站刪除資源群組。

## <a name="next-steps"></a>下一步

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 使用 Blazor Server 應用程式建立簡單的聊天室。
> * 修改 Razor 元件。
> * 使用元件中的事件處理和資料繫結。
> * 快速部署至 Visual Studio 中的 Azure App Service。
> * 將本機 SignalR 遷移至 Azure SignalR Service。

深入了解高可用性。
> [!div class="nextstepaction"]
> [復原和災害復原](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>其他資源

* [ASP.NET Core Blazor](/aspnet/core/blazor)
