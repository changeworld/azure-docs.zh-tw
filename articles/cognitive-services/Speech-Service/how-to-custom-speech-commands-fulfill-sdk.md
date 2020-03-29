---
title: 如何使用語音 SDK 實現來自用戶端的命令
titleSuffix: Azure Cognitive Services
description: 在本文中，我們將解釋如何使用語音 SDK 處理用戶端上的自訂命令活動。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367733"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>使用語音 SDK 實現來自用戶端的命令（預覽）

要使用自訂命令應用程式完成任務，可以將自訂負載發送到已連接的用戶端設備。

在本文中，您將：

- 定義自訂 JSON 負載，並從自訂命令應用程式
- 從 C# UWP 語音 SDK 用戶端應用程式接收和視覺化自訂 JSON 有效負載內容

## <a name="prerequisites"></a>Prerequisites

- [視覺工作室 2019](https://visualstudio.microsoft.com/downloads/)
- 語音服務的 Azure 訂閱金鑰
  - [免費獲取一個](get-started.md)或在[Azure 門戶](https://portal.azure.com)上創建
- 以前創建的自訂命令應用
  - [快速入門：使用參數創建自訂命令（預覽）](./quickstart-custom-speech-commands-create-parameters.md)
- 啟用語音 SDK 的用戶端應用程式
  - [快速入門：使用語音 SDK 連接到自訂命令應用程式（預覽版）](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>可選：快速入門

本文將逐步介紹如何使用戶端應用程式與自訂命令應用程式進行對話。 如果您喜歡直接潛入，本文中使用的完整、即用即用的原始程式碼可在[語音 SDK 示例](https://aka.ms/csspeech/samples)中。

## <a name="fulfill-with-json-payload"></a>使用 JSON 有效負載實現

1. 從[語音工作室](https://speech.microsoft.com/)打開以前創建的自訂命令應用程式
1. 檢查 **"完成規則"** 部分，以確保您擁有以前創建的規則，該規則已回應使用者
1. 要將有效負載直接發送到用戶端，請使用"發送活動"操作創建新規則

   > [!div class="mx-imgBorder"]
   > ![發送活動完成規則](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | 設定 | 建議的值 | 描述 |
   | ------- | --------------- | ----------- |
   | 規則名稱 | 更新設備狀態 | 描述規則目的的名稱 |
   | 條件 | 所需參數`OnOff`- 和`SubjectDevice` | 確定規則何時可以運行的條件 |
   | 動作 | `SendActivity`（見下文） | 規則條件為 true 時要執行的操作 |

   > [!div class="mx-imgBorder"]
   > ![發送活動有效負載](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>為設備打開或關閉狀態創建視覺物件

在[快速入門中：使用語音 SDK（預覽）連接到自訂命令應用程式，](./quickstart-custom-speech-commands-speech-sdk.md)您創建了一個語音 SDK 用戶端應用程式，該應用程式`turn on the tv`處理`turn off the fan`命令，如 。 現在添加一些視覺物件，以便可以看到這些命令的結果。

使用以下 XML 添加帶有文本指示 **"開****"或"關"** 的標籤框`MainPage.xaml.cs`

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

## <a name="handle-customizable-payload"></a>處理可自訂的有效負載

現在，您已經創建了 JSON 有效負載，您可以添加對[JSON.NET](https://www.newtonsoft.com/json)庫的引用來處理反序列化。

> [!div class="mx-imgBorder"]
> ![發送活動有效負載](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

在`InitializeDialogServiceConnector`將以下內容添加到事件`ActivityReceived`處理常式中。 附加代碼將從活動中提取有效負載，並相應地更改電視或風扇的可視狀態。

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

## <a name="try-it-out"></a>試做

1. 啟動應用程式
1. 選擇啟用麥克風
1. 選擇"通話"按鈕
1. 說`turn on the tv`
1. 電視的視覺狀態應更改為"打開"

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [如何：將驗證添加到自訂命令參數（預覽）](./how-to-custom-speech-commands-validations.md)
