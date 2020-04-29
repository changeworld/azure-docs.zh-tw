---
title: 如何使用語音 SDK 滿足用戶端的命令
titleSuffix: Azure Cognitive Services
description: 在本文中，我們會說明如何使用語音 SDK 來處理用戶端上的自訂命令活動。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367733"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>使用語音 SDK 滿足用戶端的命令（預覽）

若要使用自訂命令應用程式來完成工作，您可以將自訂承載傳送至已連線的用戶端裝置。

在本文中，您將會：

- 從您的自訂命令應用程式定義和傳送自訂 JSON 承載
- 從 c # UWP 語音 SDK 用戶端應用程式接收並視覺化自訂 JSON 承載內容

## <a name="prerequisites"></a>先決條件

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- 適用于語音服務的 Azure 訂用帳戶金鑰
  - [免費取得一個](get-started.md)或在[Azure 入口網站](https://portal.azure.com)上建立
- 先前建立的自訂命令應用程式
  - [快速入門：使用參數來建立自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
- 啟用語音 SDK 的用戶端應用程式
  - [快速入門：使用語音 SDK 連接到自訂命令應用程式（預覽）](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>選擇性：快速入門

本文會逐步說明如何讓用戶端應用程式與您的自訂命令應用程式交談。 如果您想要深入瞭解，您可以在[語音 SDK 範例](https://aka.ms/csspeech/samples)中取得本文中使用的完整、可立即編譯的原始程式碼。

## <a name="fulfill-with-json-payload"></a>使用 JSON 承載完成

1. 從[語音 Studio](https://speech.microsoft.com/)開啟您先前建立的自訂命令應用程式
1. 請檢查 [**完成規則**] 區段，以確定您先前建立的規則會回應給使用者
1. 若要將承載直接傳送至用戶端，請使用「傳送活動」動作建立新的規則

   > [!div class="mx-imgBorder"]
   > ![傳送活動完成規則](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 設定 | 建議的值 | 描述 |
   | ------- | --------------- | ----------- |
   | 規則名稱 | UpdateDeviceState | 描述規則用途的名稱 |
   | 條件 | 必要參數- `OnOff`和`SubjectDevice` | 判斷規則何時可執行檔條件 |
   | 動作 | `SendActivity`（請參閱下文） | 規則條件為 true 時要採取的動作 |

   > [!div class="mx-imgBorder"]
   > ![傳送活動裝載](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>建立裝置開啟或關閉狀態的視覺效果

在[快速入門：使用語音 Sdk 連接到自訂命令應用程式（預覽）](./quickstart-custom-speech-commands-speech-sdk.md)中，您已建立可處理命令的語音 SDK `turn on the tv`客戶`turn off the fan`端應用程式，例如、。 現在新增一些視覺效果，讓您可以查看這些命令的結果。

使用新增至的下列 XML，以指示開啟或**關閉**的文字來新增加**上**標籤的方塊`MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>處理可自訂的承載

既然您已建立 JSON 承載，您可以加入[JSON.NET](https://www.newtonsoft.com/json)程式庫的參考來處理還原序列化。

> [!div class="mx-imgBorder"]
> ![傳送活動裝載](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

在`InitializeDialogServiceConnector`中，將下列專案`ActivityReceived`新增至您的事件處理常式。 額外的程式碼會從活動中解壓縮承載，並據此變更電視或風扇的視覺狀態。

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
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

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：將驗證新增至自訂命令參數（預覽）](./how-to-custom-speech-commands-validations.md)
