---
title: 從用戶端應用程式更新命令
titleSuffix: Azure Cognitive Services
description: 瞭解如何從用戶端應用程式更新命令。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 765bcbd0521f93bacb0799595e6fbef565d0f313
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963618"
---
# <a name="update-a-command-from-a-client-app"></a>從用戶端應用程式更新命令

在本文中，您將瞭解如何從用戶端應用程式更新進行中的命令。

## <a name="prerequisites"></a>Prerequisites
> [!div class = "checklist"]
> * 先前[建立的自訂命令應用程式](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>更新命令的狀態

如果您的用戶端應用程式要求您在沒有語音輸入的情況下更新進行中命令的狀態，您可以傳送事件來更新命令。

為了說明這種情況，請傳送下列事件活動，以更新正在進行的命令 (`TurnOnOff`) 的狀態： 

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "updatedCommand": {
      "name": "TurnOnOff",
      "updatedParameters": {
        "OnOff": "on"
      },
      "cancel": false
    },
    "updatedGlobalParameters": {},
    "processTurn": true
  }
}
```

讓我們回顧這個活動的重要屬性：

| 屬性 | 說明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | 活動的類型為 `"event"` ，而事件的名稱必須是 `"RemoteUpdate"` 。 |
| **value** | 屬性 `"value"` 包含更新目前命令所需的屬性。 |
| **updatedCommand** | 屬性 `"updatedCommand"` 包含命令的名稱。 在該屬性內， `"updatedParameters"` 是具有參數名稱及其更新值的對應。 |
| **cancel** | 如果需要取消進行中的命令，請將屬性設定 `"cancel"` 為 `true` 。 |
| **updatedGlobalParameters** | 屬性 `"updatedGlobalParameters"` 就像這樣的對應 `"updatedParameters"` ，但它是用於全域參數。 |
| **processTurn** | 如果在傳送活動之後需要處理回合，請將屬性設定 `"processTurn"` 為 `true` 。 |

您可以在自訂命令入口網站中測試此案例：

1. 開啟您先前建立的自訂命令應用程式。 
1. 選取 [ **定型** ]，然後進行 **測試**。
1. 傳送 `turn` 。
1. 開啟側邊面板，然後選取 [ **活動編輯器**]。
1. 輸入並傳送 `RemoteCommand` 上一節所指定的事件。
    > [!div class="mx-imgBorder"]
    > ![顯示遠端命令事件的螢幕擷取畫面。](media/custom-commands/send-remote-command-activity.png)

請注意，參數值如何 `"OnOff"` `"on"` 透過用戶端的活動（而非語音或文字）設定為。

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>更新命令的參數類別目錄

當您為參數設定有效選項的清單時，參數的值會針對應用程式全域定義。 

在我們的範例中， `SubjectDevice` 參數會有一份固定的支援值清單，而不論交談為何。

如果您想要將新的專案加入至每個交談的參數目錄，您可以傳送下列活動：

```json
{
  "type": "event",
  "name": "RemoteUpdate",
  "value": {
    "catalogUpdate": {
      "commandParameterCatalogs": {
        "TurnOnOff": [
          {
            "name": "SubjectDevice",
            "values": {
              "stereo": [
                "cd player"
              ]
            }
          }
        ]
      }
    },
    "processTurn": false
  }
}
```
透過此活動，您可以在命令中將的專案加入 `"stereo"` 至參數的目錄 `"SubjectDevice"` `"TurnOnOff"` 。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="顯示目錄更新的螢幕擷取畫面。":::

請注意幾件事：
- 您只需要將此活動一次傳送給 (在您啟動連線) 之後，請立即傳送。
- 傳送此活動之後，您應該等候事件 `ParameterCatalogsUpdated` 傳送回用戶端。

## <a name="add-more-context-from-the-client-application"></a>從用戶端應用程式新增更多內容

您可以為每個交談設定用戶端應用程式的其他內容，稍後可用於自訂命令應用程式。 

例如，想想要傳送連接至自訂命令應用程式之裝置的識別碼和名稱的案例。

若要測試此案例，讓我們在目前的應用程式中建立新的命令：
1. 建立名為的新命令 `GetDeviceInfo` 。
1. 加入的範例句子 `get device info` 。
1. 在 **完成規則中**，加入包含屬性的「 **傳送語音回應** 」動作 `clientContext` 。
   ![顯示傳送具有內容的語音回應的螢幕擷取畫面。](media/custom-commands/send-speech-response-context.png)
1. 儲存、定型和測試您的應用程式。
1. 在 [測試] 視窗中，傳送活動以更新用戶端內容。

    ```json
    {
       "type": "event",
       "name": "RemoteUpdate",
       "value": {
         "clientContext": {
           "deviceId": "12345",
           "deviceName": "My device"
         },
         "processTurn": false
       }
    }
    ```
1. 傳送文字 `get device info` 。
   ![顯示傳送用戶端內容之活動的螢幕擷取畫面。](media/custom-commands/send-client-context-activity.png)

請注意下列事項：
- 您只需要將此活動一次傳送給 (在您啟動連線) 之後，請立即傳送。
- 您可以使用的複雜物件 `clientContext` 。
- 您可以使用 `clientContext` 語音回應來傳送活動，以及呼叫 web 端點。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [從 Web 端點更新命令](./how-to-custom-commands-update-command-from-web-endpoint.md)
