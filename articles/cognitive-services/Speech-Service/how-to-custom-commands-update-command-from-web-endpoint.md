---
title: 從 web 端點更新命令
titleSuffix: Azure Cognitive Services
description: 從 web 端點更新命令
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571199"
---
# <a name="update-a-command-from-a-web-endpoint"></a>從 web 端點更新命令

如果您的用戶端應用程式需要更新進行中命令的狀態，而沒有語音輸入，您可以使用 web 端點的呼叫來更新命令。

在本文中，您將瞭解如何從 web 端點更新進行中的命令。

## <a name="prerequisites"></a>必要條件
> [!div class = "checklist"]
> * 先前[建立的自訂命令應用程式](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>建立 Azure 函式 

在此範例中，我們需要支援下列輸入 (或此輸入) 子集的 HTTP-Triggered [Azure 函數](https://docs.microsoft.com/azure/azure-functions/) 。

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

讓我們檢查此輸入的索引鍵屬性。

| 屬性 | 說明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 「conversationId」是交談的唯一識別碼，請注意，此識別碼可從用戶端應用程式產生。 |
| **currentCommand** | "currentCommand" 是對話中目前作用中的命令。 |
| **name** | "name" 是命令的名稱，而 "parameters" 是具有參數目前值的對應。 |
| **currentGlobalParameters** | "currentGlobalParameters" 也是「參數」之類的對應，但用於全域參數。 |

Azure 函數的輸出需要支援下列格式。

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

您可以辨識此格式，因為這是 [從用戶端更新命令](./how-to-custom-commands-update-command-from-client.md)時使用的相同格式。 

現在，根據 NodeJS 建立 Azure 函式，然後複製並貼上此程式碼

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

當我們從自訂命令呼叫此 Azure 函式時，我們會傳送目前的交談值，我們會傳回我們想要更新的參數，或者是否要取消目前的命令。

## <a name="update-the-existing-custom-commands-app"></a>更新現有的自訂命令應用程式

現在讓我們使用現有的自訂命令應用程式連結 Azure 函數。

1. 加入名為 IncrementCounter 的新命令。
1. 只新增一個值為 "遞增" 的範例句子。
1. 加入名為 Counter (與上述 Azure 函式中指定的相同名稱的新參數) ，其預設值為0的類型數位。
1. 使用您的 Azure 函式 URL 和已啟用遠端更新的 URL，新增名為 IncrementEndpoint 的新 Web 端點。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="使用遠端更新設定 web 端點":::
1. 建立稱為 "IncrementRule" 的新互動規則，並新增「呼叫 web 端點」動作。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="遞增規則":::
1. 在 [動作] 設定中，選取 [IncrementEndpoint]、[設定成功]，並使用 [計數器的值] 和 [失敗時] 來傳送語音回應，並顯示錯誤訊息。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="設定遞增計數器呼叫端點":::
1. 設定規則的執行後狀態以等待使用者的輸入

## <a name="test-it"></a>進行測試

1. 儲存並定型您的應用程式
1. 按一下 [測試]
1. 傳送幾次「遞增」 (這是 IncrementCounter 命令的範例句子) 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="遞增計數器範例":::

請注意，Azure 函式的每個回合會如何遞增計數器參數的值。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [為自訂命令應用程式啟用 CI/CD 程序](./how-to-custom-commands-deploy-cicd.md)