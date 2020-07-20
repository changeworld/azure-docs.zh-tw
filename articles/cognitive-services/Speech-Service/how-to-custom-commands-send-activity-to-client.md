---
title: 將自訂命令活動傳送至用戶端應用程式
titleSuffix: Azure Cognitive Services
description: 在本文中，您將瞭解如何將活動從自訂命令應用程式傳送至執行語音 SDK 的用戶端應用程式。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 520b38f4c733e7bf28a2a06429ad14d016c5bd28
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027608"
---
# <a name="send-custom-commands-activity-to-client-application"></a>將自訂命令活動傳送至用戶端應用程式

在本文中，您將瞭解如何將活動從自訂命令應用程式傳送至執行語音 SDK 的用戶端應用程式。

您會完成下列工作：

- 從您的自訂命令應用程式定義和傳送自訂 JSON 承載
- 從 c # UWP 語音 SDK 用戶端應用程式接收並視覺化自訂 JSON 承載內容

## <a name="prerequisites"></a>必要條件
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)或更高版本。 本指南使用 Visual Studio 2019
> * 適用于語音服務的 Azure 訂用帳戶金鑰：[免費取得一個，](get-started.md)或在[Azure 入口網站](https://portal.azure.com)上建立
> * 先前[建立的自訂命令應用程式](quickstart-custom-commands-application.md)
> * 啟用語音 SDK 的用戶端應用程式：[如何：使用語音 SDK 與用戶端應用程式整合](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-send-activity-to-client"></a>設定將活動傳送至用戶端 
1. 開啟您先前建立的自訂命令應用程式
1. 選取 [ **TurnOnOff**命令]，在 [完成規則] 下選取 [ **ConfirmationResponse** ]，然後選取 [**新增動作**]
1. 在 [**新增動作-類型**] 底下，選取 [**將活動傳送至用戶端**]。
1. 將下列 JSON 複製到**活動內容**
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
1. 按一下 [**儲存**] 以建立具有傳送活動動作的新規則，**訓練**並**發佈**變更

   > [!div class="mx-imgBorder"]
   > ![傳送活動完成規則](media/custom-commands/send-activity-to-client-completion-rules.png)

## <a name="integrate-with-client-application"></a>與用戶端應用程式整合

在[如何：使用語音 Sdk 設定用戶端應用程式（預覽）](./how-to-custom-commands-setup-speech-sdk.md)中，您已使用語音 SDK 建立 UWP 用戶端應用程式來處理命令 `turn on the tv` ，例如， `turn off the fan` 。 新增一些視覺效果之後，您就可以看到這些命令的結果。

若要使用表示 on 或**off**的文字來新增加**上**標籤的方塊，請將下列 StackPanel XML 區塊新增至 `MainPage.xaml` 。

```xml
<StackPanel Orientation="Vertical" H......>
......
</StackPanel>
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
<MediaElement ....../>
```

### <a name="add-reference-libraries"></a>新增參考程式庫

由於您已建立 JSON 承載，因此您需要加入[JSON.NET](https://www.newtonsoft.com/json)程式庫的參考以處理還原序列化。

1. 以滑鼠右鍵用戶端您的解決方案。
1. 選擇 [**管理解決方案的 NuGet 套件**]，選取 **[流覽]** 
1. 如果您已經**在上安裝Newtonsoft.js**，請確定其版本至少為12.0.3。 如果沒有，請移至 [**管理方案的 NuGet 套件-更新**]，搜尋**上的Newtonsoft.js**來更新它。 本指南使用版本12.0.3。

    > [!div class="mx-imgBorder"]
    > ![傳送活動裝載](media/custom-commands/send-activity-to-client-json-nuget.png)

1. 此外，請確定 NuGet package **NETCore. microsoft.netcore.universalwindowsplatform**至少是6.2.10。 本指南使用版本6.2.10。

在 ' MainPage ' 中，新增

```C#
using Newtonsoft.Json; 
using Windows.ApplicationModel.Core;
using Windows.UI.Core;
```

### <a name="handle-the-received-payload"></a>處理已接收的裝載

在中 `InitializeDialogServiceConnector` ，以 `ActivityReceived` 下列程式碼取代事件處理常式。 修改過的 `ActivityReceived` 事件處理常式將會從活動中解壓縮承載，並分別變更電視或風扇的視覺狀態。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);
    var name = activity?.name != null ? activity.name.ToString() : string.Empty;

    if (name.Equals("UpdateDeviceState"))
    {
        Debug.WriteLine("Here");
        var state = activity?.device != null ? activity.state.ToString() : string.Empty;
        var device = activity?.device != null ? activity.device.ToString() : string.Empty;

        if (state.Equals("on") || state.Equals("off"))
        {
            switch (device)
            {
                case "tv":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_TV.Text = state; });
                    break;
                case "fan":
                    await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
                        CoreDispatcherPriority.Normal, () => { State_Fan.Text = state; });
                    break;
                default:
                    NotifyUser($"Received request to set unsupported device {device} to {state}");
                    break;
            }
        }
        else { 
            NotifyUser($"Received request to set unsupported state {state}");
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>試試看

1. 啟動應用程式
1. 選取 [啟用麥克風]
1. 選取 [交談] 按鈕
1. 比如`turn on the tv`
1. 電視的視覺狀態應該變更為「開啟」
   > [!div class="mx-imgBorder"]
   > ![傳送活動裝載](media/custom-commands/send-activity-to-client-turn-on-tv.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：設定 web 端點（預覽）](./how-to-custom-commands-setup-web-endpoints.md)
