---
title: 從用戶端應用程式更新命令
titleSuffix: Azure Cognitive Services
description: 從用戶端應用程式更新命令
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 1bffb09d0f49bbd0059e8a528d67bfe215f0650d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654336"
---
# <a name="update-a-command-from-the-client"></a>從用戶端更新命令

在本文中，我們將瞭解如何從用戶端應用程式更新進行中的命令。

## <a name="prerequisites"></a>先決條件
> [!div class = "checklist"]
> * 先前[建立的自訂命令應用程式](quickstart-custom-commands-application.md)

## <a name="update-the-state-of-a-command"></a>更新命令的狀態

如果您的用戶端應用程式需要更新進行中命令的狀態，而不使用語音輸入，您可以傳送事件來更新命令。

為了說明此案例，若要更新進行中命令的狀態 (TurnOnOff) 我們可以傳送下列事件活動。 

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

讓我們檢查此活動的索引鍵屬性。

| 屬性 | 說明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **type** | 活動的類型是 "event"，而事件的名稱必須是 "RemoteUpdate"。 |
| **value** | 屬性 "value" 包含更新目前命令所需的屬性。 |
| **updatedCommand** | 屬性 "updatedCommand" 包含命令的名稱，"updatedParameters" 是具有參數名稱及其更新值的對應。 |
| **cancel** | 如果需要取消進行中的命令，請將 [取消] 屬性設定為 true。 |
| **updatedGlobalParameters** | 屬性 "updatedGlobalParameters" 也是對應，就像是 "updatedParameters"，但用於全域參數。 |
| **processTurn** | 如果傳送活動之後需要處理回合，請將 "processTurn" 屬性設定為 true。 |

您可以在自訂命令入口網站中測試此案例。

1. 開啟您先前建立的自訂命令應用程式。 
1. 按一下 [定型]，然後進行測試。
1. 傳送 "turn"。
1. 開啟側邊面板，然後按一下 [活動編輯器]。
1. 輸入並傳送上一節中指定的 RemoteCommand 事件。
    > [!div class="mx-imgBorder"]
    > ![傳送遠端命令](media/custom-commands/send-remote-command-activity.png)

請注意，參數 "OnOff" 的值如何使用來自用戶端的活動（而非語音或文字）設定為 "on"。

## <a name="update-the-catalog-of-the-parameter-for-a-command"></a>更新命令的參數類別目錄

當您為參數設定有效選項的清單時，參數的值會針對應用程式全域定義。 

在我們的範例中，SubjectDevice 參數會有一份固定的支援值清單，而不論交談。

如果您需要針對每個交談將新專案新增至參數的目錄，您可以傳送下列活動。

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
在此活動中，我們將 "身歷聲" 的專案新增至 "TurnOnOff" 命令中 "SubjectDevice" 參數的目錄。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/custom-commands/update-catalog-with-remote-activity.png" alt-text="更新類別目錄":::

請注意幾件事。
1. 您只需要將此活動傳送一次， (在您啟動連線) 之後，最好立即傳送。
1. 傳送此活動之後，您應該等候事件 ParameterCatalogsUpdated 送回用戶端。

## <a name="add-additional-context-from-the-client-application"></a>從用戶端應用程式新增其他內容

您可以為每個交談設定用戶端應用程式的其他內容，稍後可用於自訂命令應用程式。 

例如，想想要傳送連接至自訂命令應用程式之裝置的識別碼和名稱的案例。

若要測試此案例，讓我們在目前的應用程式中建立新的命令。
1. 建立名為 GetDeviceInfo 的新命令。
1. 使用「取得裝置資訊」新增範例句子。
1. 在完成規則「完成」中，新增包含 clientCoNtext 屬性的傳送語音回應動作。
    > ![傳送具有內容的語音回應](media/custom-commands/send-speech-response-context.png)
1. 儲存、定型及測試您的應用程式。
1. 在 [測試] 視窗中，傳送活動以更新用戶端內容。
    > ```json
    >{
    >   "type": "event",
    >   "name": "RemoteUpdate",
    >   "value": {
    >     "clientContext": {
    >       "deviceId": "12345",
    >       "deviceName": "My device"
    >     },
    >     "processTurn": false
    >   }
    >}
    > ```
1. 傳送文字「取得裝置資訊」。
    > ![傳送用戶端內容活動](media/custom-commands/send-client-context-activity.png)

請注意幾件事。
1. 您只需要將此活動傳送一次， (在您啟動連線) 之後，最好立即傳送。
1. 您可以使用 clientCoNtext 的複雜物件。
1. 您可以在語音回應中使用 clientCoNtext，以傳送活動以及呼叫 web 端點。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [從 Web 端點更新命令](./how-to-custom-commands-update-command-from-web-endpoint.md)
