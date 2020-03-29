---
title: 使用門戶使用事件網格監視 Azure 媒體服務事件
description: 本文說明如何訂閱事件方格以監視 Azure 媒體服務事件。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure 媒體服務, 串流, 廣播, 即時, 離線
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 01/21/2020
ms.author: juliako
ms.openlocfilehash: 18503e64dc6f38daab61599153cd0e0fb6fadb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509218"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>在 Azure 入口網站中使用事件方格建立和監視媒體服務事件

Azure Event Grid 是一項雲端事件服務。 此服務使用[事件訂閱](../../event-grid/concepts.md#event-subscriptions)將事件消息路由到訂閱者。 媒體事件包含了回應資料變更時所需的一切資訊。 因為 eventType 屬性開頭為 “Microsoft.Media”，所以您可以藉此識別出媒體服務事件。 如需詳細資訊，請參閱[媒體服務事件結構描述](media-services-event-schemas.md)。

在本文中，您會使用 Azure 入口網站訂閱 Azure 媒體服務帳戶的事件。 然後，您會觸發事件以檢視結果。 通常，您會將事件傳送至可處理事件資料及採取行動的端點。 在本文中，我們將事件發送到收集和顯示消息的 Web 應用。

當您完成時，您會看到事件資料已傳送至 Web 應用程式。

## <a name="prerequisites"></a>Prerequisites 

* 擁有有效的 Azure 訂用帳戶。
* 建立新的 Azure 媒體服務帳戶，如[此快速入門](create-account-cli-quickstart.md)所述。

## <a name="create-a-message-endpoint"></a>建立訊息端點

在訂閱媒體服務帳戶的事件之前，我們要先建立事件訊息的端點。 通常，端點會根據事件資料採取動作。 在本文中，您會部署[預先建置的 Web 應用程式](https://github.com/Azure-Samples/azure-event-grid-viewer)以顯示事件訊息。 已部署的解決方案包含 App Service 方案、App Service Web 應用程式，以及 GitHub 中的原始程式碼。

1. 選取 [部署至 Azure]****，將解決方案部署至您的訂用帳戶。 在 Azure 入口網站中，提供參數的值。

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

1. 部署需要幾分鐘的時間才能完成。 成功部署之後，檢視 Web 應用程式，確定它正在執行。 在網頁瀏覽器中，瀏覽至：`https://<your-site-name>.azurewebsites.net`

如果您切換到「Azure 事件方格檢視器」網站，您會看到其中還沒有任何事件。
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>訂閱媒體服務事件

您可訂閱主題，告知 Event Grid 您想要追蹤的事件，以及要將事件傳送至何處。

1. 在入口網站中選取您的媒體服務帳戶，然後選取 [事件]****。
1. 若要將事件傳送至您的檢視器應用程式，請使用端點的 Web Hook。 

   ![選取 Web Hook](./media/monitor-events-portal/select-web-hook.png)

1. 事件訂用帳戶中會預先填入您媒體服務帳戶的值。 
1. 選擇 **"網站類型"** 的"Web 掛鉤"。
1. 在此主題中，我們將 [訂閱所有事件類型]**** 保留為核取狀態。 不過，您可以將其取消核取，並篩選特定事件類型。 
1. 按一下 [選取端點]**** 連結。

    針對 Web Hook 端點，提供您的 Web 應用程式 URL，並將 `api/updates` 新增至首頁 URL。 

1. 按 [確認選取項目]****。
1. 按下 [建立] ****。
1. 請為您的訂用帳戶指定名稱。

   ![選取記錄](./media/monitor-events-portal/create-subscription.png)

1. 再次檢視 Web 應用程式，並注意訂用帳戶的驗證事件已傳送給它。 

    Event Grid 會傳送驗證事件，以便端點確認它要接收事件資料。 端點必須將 `validationResponse` 設定為 `validationCode`。 如需詳細資訊，請參閱 [Event Grid 安全性和驗證](../../event-grid/security-authentication.md)。 您可以檢視 Web 應用程式的程式碼，以查看其驗證訂用帳戶的方式。

現在，我們將觸發事件以查看事件方格如何將訊息散發至您的端點。

## <a name="send-an-event-to-your-endpoint"></a>將事件傳送至端點

您可以藉由執行編碼作業來觸發媒體服務帳戶的事件。 您可以依照[本快速入門](stream-files-dotnet-quickstart.md)的說明為檔案編碼，並開始傳送事件。 如果您訂閱了所有事件，您將看到如下的畫面：

> [!TIP]
> 選取眼睛圖示來展開事件資料。 如果您要檢視所有事件，請不要重新整理頁面。

![訂用訂用帳戶事件](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>後續步驟

[上傳、編碼和串流](stream-files-tutorial-with-api.md)
