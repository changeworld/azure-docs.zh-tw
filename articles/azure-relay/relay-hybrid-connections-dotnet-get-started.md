---
title: Azure 轉送混合式連接-.NET 中的 Websocket
description: '撰寫適用于 Azure 轉送混合式連接 Websocket 的 c # 主控台應用程式。'
services: service-bus-relay
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus-relay
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/01/2018
ms.author: spelluru
ms.openlocfilehash: 13612f8ffa343e483165a8dbdd54d1b2b1f5e2cf
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211927"
---
# <a name="get-started-with-relay-hybrid-connections-websockets-in-net"></a>在 .NET 中開始使用轉送混合式連線 WebSocket
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

在本快速入門中，您將建立 .NET 傳送者與接收者應用程式，以使用 Azure 轉送中的混合式連線 WebSocket 來傳送和接收訊息。 若要對 Azure 轉送有整體上的了解，請參閱 [Azure 轉送](relay-what-is-it.md)。 

在本快速入門中，您會執行下列步驟：

1. 使用 Azure 入口網站建立轉送命名空間。
2. 使用 Azure 入口網站，在該命名空間中建立混合式連線。
3. 撰寫伺服器 (接聽端) 主控台應用程式來接收訊息。
4. 撰寫用戶端 (傳送端) 主控台應用程式來傳送訊息。
5. 執行應用程式。 

## <a name="prerequisites"></a>先決條件

若要完成本教學課程，您需要下列必要條件：

* [Visual Studio 2015 或更新版本](https://www.visualstudio.com)。 本教學課程中的範例使用 Visual Studio 2017。
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-a-namespace"></a>建立命名空間
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>Create a hybrid connection
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>建立伺服器應用程式 (接聽程式)
在 Visual Studio 中，撰寫 C# 主控台應用程式以接聽並接收來自轉送的訊息。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>建立用戶端應用程式 (傳送者)
在 Visual Studio 中，撰寫 C# 主控台應用程式以將訊息傳送至轉送。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>執行應用程式
1. 執行伺服器應用程式。
2. 執行用戶端應用程式並輸入一些文字。
3. 確定伺服器應用程式主控台有顯示用戶端應用程式中所輸入的文字。

    ![執行應用程式](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

恭喜，您已建立完整的混合式連線應用程式！

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已建立 .NET 用戶端和使用 WebSocket 來傳送和接收訊息的伺服器應用程式。 Azure 轉送的混合式連線功能也支援使用 HTTP 來傳送和接收訊息。 若要了解如何搭配使用 HTTP 和 Azure 轉送混合式連線，請參閱 [HTTP 快速入門](relay-hybrid-connections-http-requests-dotnet-get-started.md)。

在本快速入門中，您已使用 .NET Framework 來建立用戶端和伺服器應用程式。 若要了解如何使用 Node.js 撰寫用戶端和伺服器應用程式，請參閱 [Node.js WebSocket 快速入門](relay-hybrid-connections-node-get-started.md)或 [Node.js HTTP 快速入門](relay-hybrid-connections-http-requests-dotnet-get-started.md)。

