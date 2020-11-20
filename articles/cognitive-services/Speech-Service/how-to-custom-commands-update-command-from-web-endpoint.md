---
title: 從 Web 端點更新命令
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用 web 端點的呼叫來更新命令的狀態。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: a24f1337a68f38db273688e9a91c65ac2f4736b4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963601"
---
# <a name="update-a-command-from-a-web-endpoint"></a>從 Web 端點更新命令

如果您的用戶端應用程式需要更新進行中的命令，而不使用語音輸入，您可以使用 web 端點的呼叫來更新命令。

在本文中，您將瞭解如何從 web 端點更新進行中的命令。

## <a name="prerequisites"></a>Prerequisites
> [!div class = "checklist"]
> * 先前[建立的自訂命令應用程式](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>建立 Azure 函式 

在此範例中，您需要 HTTP 觸發的 [Azure](https://docs.microsoft.com/azure/azure-functions/) 函式，以支援下列輸入 (或此輸入) 的子集：

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

讓我們來看看此輸入的重要屬性：

| 屬性 | 說明 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | 交談的唯一識別碼。 請注意，此識別碼可從用戶端應用程式產生。 |
| **currentCommand** | 交談中目前作用中的命令。 |
| **name** | 命令的名稱。 `parameters`屬性是具有目前參數值的對應。 |
| **currentGlobalParameters** | 類似的對應 `parameters` ，但用於全域參數。 |

Azure 函數的輸出需要支援下列格式：

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

您可以辨識此格式，因為它與您 [從用戶端更新命令](./how-to-custom-commands-update-command-from-client.md)時使用的相同。 

現在，根據 Node.js 建立 Azure 函數。 複製/貼上此程式碼：

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

當您從自訂命令呼叫此 Azure 函式時，您將會傳送交談的目前值。 您將會傳回您想要更新的參數，或者，如果您想要取消目前的命令。

## <a name="update-the-existing-custom-commands-app"></a>更新現有的自訂命令應用程式

讓我們使用現有的自訂命令應用程式來連結 Azure function：

1. 加入名為的新命令 `IncrementCounter` 。
1. 只加入一個含有值的範例句子 `increment` 。
1. 將名為的新參數 `Counter` (與預設值為之類型的 Azure 函數) 中指定的名稱相同 `Number` `0` 。
1. 使用您的 Azure 函式 URL 來新增名為的新 web 端點 `IncrementEndpoint` ，並將 **遠端更新** 設定為 [ **已啟用**]。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="顯示如何設定具有遠端更新之 web 端點的螢幕擷取畫面。":::
1. 建立稱為 **IncrementRule** 的新互動規則，並新增「 **呼叫 web 端點** 」動作。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="顯示建立互動規則的螢幕擷取畫面。":::
1. 在 [動作設定] 中，選取 `IncrementEndpoint` 。 將設定為 [ **成功** ] 以使用的值 **傳送語音回應** `Counter` ，並設定 [ **失敗** 時]，並顯示錯誤訊息。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="螢幕擷取畫面，顯示設定呼叫 web 端點的遞增計數器。":::
1. 設定規則的執行後狀態以 **等待使用者的輸入**。

## <a name="test-it"></a>進行測試

1. 儲存並定型您的應用程式。
1. 選取 [測試]。
1. 傳送 `increment` 幾次 (這是命令) 的範例句子 `IncrementCounter` 。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="顯示遞增計數器範例的螢幕擷取畫面。":::

請注意 Azure 函數如何 `Counter` 在每個回合上遞增參數的值。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [為自訂命令應用程式啟用 CI/CD 程序](./how-to-custom-commands-deploy-cicd.md)
