---
title: 設定 Web 端點 (預覽)
titleSuffix: Azure Cognitive Services
description: 設定自訂命令的 Web 端點
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 1b7b5e209329bd5dc4c95f81f61fecf48fb74e40
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362105"
---
# <a name="set-up-web-endpoints"></a>設定 Web 端點

在本文中，您將了解如何在自訂命令應用程式中設定 Web 端點，以便能從用戶端應用程式提出 HTTP 要求。 您將完成下列工作：

- 在自訂命令應用程式中設定 Web 端點
- 在自訂命令應用程式中呼叫 Web 端點
- 接收 Web 端點回應 
- 將 Web 端點回應整合到自訂 JSON 承載、進行傳送，並從 C# UWP 語音 SDK 用戶端應用程式將其視覺化

## <a name="prerequisites"></a>Prerequisites
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
> * 適用於語音服務的 Azure 訂用帳戶金鑰：[免費取得一個金鑰](overview.md#try-the-speech-service-for-free)或 [Azure 入口網站](https://portal.azure.com)上建立金要
> * 先前[建立的自訂命令應用程式](quickstart-custom-commands-application.md)
> * 具有語音 SDK 功能的用戶端應用程式：[操作說明：將活動傳送至用戶端應用程式](./how-to-custom-commands-setup-speech-sdk.md)

## <a name="setup-web-endpoints"></a>設定 Web 端點

1. 開啟您先前建立的自訂命令應用程式。 
1. 移至 [Web 端點]，然後按一下 [新增 Web 端點]。

   > [!div class="mx-imgBorder"]
   > ![新增 Web 端點](media/custom-commands/setup-web-endpoint-new-endpoint.png)

   | 設定 | 建議的值 | 描述 |
   | ------- | --------------- | ----------- |
   | 名稱 | UpdateDeviceState | Web 端點的名稱。 |
   | URL | https://webendpointexample.azurewebsites.net/api/DeviceState | 您想要作為自訂命令應用程式通訊目標端點的 URL。 |
   | 方法 | POST | 允許與端點進行的互動 (例如 GET、POST)。|
   | 標題 | 金鑰：應用程式，值：採用 applicationId 的前 8 個數字 | 要包含在要求標頭中的標頭參數。|

    > [!NOTE]
    > - 使用 [Azure Function](https://docs.microsoft.com/azure/azure-functions/) 建立的範例 Web 端點，會與儲存電視和風扇裝置狀態的資料庫連結
    > - 只有範例端點才需要建議的標頭
    > - 為確保標頭的值在我們的範例端點中是唯一的，請採用您 applicationId 的前 8 個數字
    > - 在真實世界中，Web 端點可以是管理裝置的 [IOT 中樞](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)端點

1. 按一下 [檔案] 。

## <a name="call-web-endpoints"></a>呼叫 Web 端點

1. 移至 [TurnOnOff] 命令、選取 [完成規則] 底下的 [ConfirmationResponse]，然後選取 [新增動作]。
1. 在 [新增動作 - 類型] 底下，選取 [呼叫 Web 端點]
1. 在 [編輯動作 - 端點] 中，選取 [UpdateDeviceState]，這是我們所建立的 Web 端點。  
1. 在 [設定] 中，輸入下列值： 
   > [!div class="mx-imgBorder"]
   > ![呼叫 Web 端點動作參數](media/custom-commands/setup-web-endpoint-edit-action-parameters.png)

   | 設定 | 建議的值 | 描述 |
   | ------- | --------------- | ----------- |
   | 端點 | UpdateDeviceState | 您想要在此動作中呼叫的 Web 端點。 |
   | 查詢參數 | item={SubjectDevice}&&value={OnOff} | 要附加至 Web 端點 URL 的查詢參數。  |
   | 本文內容 | N/A | 要求的本文內容。 |

    > [!NOTE]
    > - 只有範例端點才需要建議的查詢參數

1. 在 [成功時 - 要執行的動作] 中，選取 [傳送語音回應]。
    
    在 [簡單編輯器] 中，輸入 `{SubjectDevice} is {OnOff}`。
   
   > [!div class="mx-imgBorder"]
   > ![成功時呼叫 Web 端點動作](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-response.png)

   | 設定 | 建議的值 | 描述 |
   | ------- | --------------- | ----------- |
   | 要執行的動作 | 傳送語音回應 | 針對 Web 端點的要求成功時所要執行的動作 |
   
   > [!NOTE]
   > - 您也可以使用 `{YourWebEndpointName.FieldName}` 直接存取 HTTP 回應中的欄位。 例如：`{UpdateDeviceState.TV}`

1. 在 [失敗時 - 要執行的動作] 中，選取 [傳送語音回應]

    在 [簡單編輯器] 中，輸入 `Sorry, {WebEndpointErrorMessage}`。

   > [!div class="mx-imgBorder"]
   > ![失敗時呼叫 Web 端點動作](media/custom-commands/setup-web-endpoint-edit-action-on-fail.png)

   | 設定 | 建議的值 | 描述 |
   | ------- | --------------- | ----------- |
   | 要執行的動作 | 傳送語音回應 | 針對 Web 端點的要求失敗時所要執行的動作 |

   > [!NOTE]
   > - `{WebEndpointErrorMessage}` 是選擇性的。 如果您不想要公開任何錯誤訊息，則可以將其移除。
   > - 在我們的範例端點內，我們會傳回 HTTP 回應，以及常見錯誤的詳細錯誤訊息，例如遺漏標頭參數。 

### <a name="try-it-out-in-test-portal"></a>在測試入口網站中試用
- 成功時的回應\
儲存、定型和測試
   > [!div class="mx-imgBorder"]
   > ![成功時呼叫 Web 端點動作](media/custom-commands/setup-web-endpoint-on-success-response.png)
- 失敗時的回應\
移除其中一個查詢參數、儲存、重新定型和測試
   > [!div class="mx-imgBorder"]
   > ![成功時呼叫 Web 端點動作](media/custom-commands/setup-web-endpoint-on-fail-response.png)

## <a name="integrate-with-client-application"></a>與用戶端應用程式整合

在[操作說明：將活動傳送至用戶端應用程式 (預覽)](./how-to-custom-commands-send-activity-to-client.md)中，您已新增**將活動傳送至用戶端**動作。 無論**呼叫 Web 端點**動作是否成功，都會將活動傳送至用戶端應用程式。
不過，在大部分的情況下，您只想要在 Web 端點呼叫成功時，才將活動傳送至用戶端應用程式。 在此範例中，這是當裝置的狀態成功更新時。

1. 刪除您先前新增的**將活動傳送至用戶端**動作。
1. 編輯呼叫 Web 端點： 
    1. 在 [設定] 中，確定 [查詢參數] 是 `item={SubjectDevice}&&value={OnOff}`
    1. 在 [成功時] 中，將 [要執行的動作] 變更為 [將活動傳送至用戶端]
    1. 將下列 JSON 複製到 [活動內容]
   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```
    > [!div class="mx-imgBorder"]
    > ![成功時傳送活動](media/custom-commands/setup-web-endpoint-edit-action-on-success-send-activity.png)
   
現在，只有當針對 Web 端點的要求成功時，您才會將活動傳送至用戶端。

### <a name="create-visuals-for-syncing-device-state"></a>建立用於同步裝置狀態的視覺效果
將下列 XML 新增至 `"EnableMicrophoneButton"` 區塊上方的 `MainPage.xaml`。

```xml
<Button x:Name="SyncDeviceStateButton" Content="Sync Device State"
        Margin="0,10,10,0" Click="SyncDeviceState_ButtonClicked"
        Height="35"/>
<Button x:Name="EnableMicrophoneButton" ......
        .........../>
```

### <a name="sync-device-state"></a>同步裝置狀態 

在 `MainPage.xaml.cs` 中，新增參考 `using Windows.Web.Http;`。 將下列程式碼新增至 `MainPage` 類別。 這個方法會將 GET 要求傳送至範例端點，並擷取應用程式目前的裝置狀態。 請務必將 `<your_app_name>` 變更為您在自訂命令 Web 端點的**標頭**中所使用的內容

```C#
private async void SyncDeviceState_ButtonClicked(object sender, RoutedEventArgs e)
{
    //Create an HTTP client object
    var httpClient = new HttpClient();

    //Add a user-agent header to the GET request. 
    var your_app_name = "<your-app-name>";

    Uri endpoint = new Uri("https://webendpointexample.azurewebsites.net/api/DeviceState");
    var requestMessage = new HttpRequestMessage(HttpMethod.Get, endpoint);
    requestMessage.Headers.Add("app", $"{your_app_name}");

    try
    {
        //Send the GET request
        var httpResponse = await httpClient.SendRequestAsync(requestMessage);
        httpResponse.EnsureSuccessStatusCode();
        var httpResponseBody = await httpResponse.Content.ReadAsStringAsync();
        dynamic deviceState = JsonConvert.DeserializeObject(httpResponseBody);
        var TVState = deviceState.TV.ToString();
        var FanState = deviceState.Fan.ToString();
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(
            CoreDispatcherPriority.Normal,
            () =>
            {
                State_TV.Text = TVState;
                State_Fan.Text = FanState;
            });
    }
    catch (Exception ex)
    {
        NotifyUser(
            $"Unable to sync device status: {ex.Message}");
    }
}
```

## <a name="try-it-out"></a>試試看

1. 啟動應用程式
1. 按一下 [同步裝置狀態]。\
如果您在上一節有使用 `turn on tv` 試用應用程式，則會看到電視顯示為「開啟」。
    > [!div class="mx-imgBorder"]
    > ![同步裝置狀態](media/custom-commands/setup-web-endpoint-sync-device-state.png)
1. 選取 [啟用麥克風]
1. 選取 [說話] 按鈕
1. 說出 `turn on the fan`
1. 風扇的視覺狀態應該會變更為「開啟」
    > [!div class="mx-imgBorder"]
    > ![開啟風扇](media/custom-commands/setup-web-endpoint-turn-on-fan.png)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [為自訂命令應用程式啟用 CI/CD 程序](./how-to-custom-commands-deploy-cicd.md)