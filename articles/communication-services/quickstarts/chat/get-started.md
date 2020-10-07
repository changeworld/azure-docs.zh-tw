---
title: 快速入門 - 將聊天新增至您的應用程式
titleSuffix: An Azure Communication Services quickstart
description: 本快速入門說明如何將 Azure 通訊服務的聊天新增至您的應用程式。
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: 2d0d80be367dda3689566dec2ade6fd7fc7c01fc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665356"
---
# <a name="quickstart-add-chat-to-your-app"></a>快速入門：將聊天新增至您的應用程式

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

使用 Azure 通訊服務的聊天用戶端程式庫，將即時聊天加入您的應用程式，以開始使用 Azure 通訊服務。 在本快速入門中，我們將使用聊天用戶端程式庫來建立聊天對話，讓使用者可以彼此聊天。 若要深入瞭解「聊天」的概念，請造訪[聊天概念文件](../../concepts/chat/concepts.md)。

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除通訊服務訂閱，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。 深入了解如何[清除資源](../create-communication-resource.md#clean-up-resources)。

## <a name="next-steps"></a>下一步

在此快速入門中，您已了解如何：

> [!div class="checklist"]
> * 建立聊天用戶端
> * 建立有兩個使用者的對話
> * 將訊息傳送至對話
> * 從對話接收訊息
> * 移除對話中的使用者

> [!div class="nextstepaction"]
> [試用聊天 Hero 應用程式](../../samples/chat-hero-sample.md)

您可能也會想要：

 - 深入了解[聊天概念](../../concepts/chat/concepts.md)
 - 熟悉[聊天用戶端程式庫](../../concepts/chat/sdk-features.md)
