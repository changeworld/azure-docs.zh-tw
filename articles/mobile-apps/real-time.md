---
title: 使用 Azure SignalR 服務輕鬆構建即時移動應用程式
description: 瞭解允許您使用 Azure SignalR 服務構建即時應用程式的服務。
author: codemillmatt
ms.assetid: 34a8a070-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 242a929e4a4ebe00dd0a2101b27ab8a8bef3fbeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240821"
---
# <a name="build-real-time-applications-with-azure-signalr-service"></a>使用 Azure SignalR 服務構建即時應用程式

使用[Azure SignalR 服務](https://azure.microsoft.com/services/signalr-service/)，可以輕鬆地向應用程式添加即時功能。 此即時功能允許服務將內容更新推送到連接的移動應用程式。 因此，應用程式會更新，而無需輪詢伺服器或提交新的 HTTP 更新要求。

## <a name="azure-signalr-service-features"></a>Azure 信號R 服務功能
- 為廣泛的用戶端（如 Web 和移動瀏覽器、桌面應用程式、伺服器進程、IoT 設備和遊戲機）提供廣泛的用戶端支援。
- 允許多個實例協同工作，以擴展到數百萬個移動應用程式連接。 它還支援多個全域區域，用於分片、高可用性或災害復原目的。
- 為不同的消息模式提供豐富的 API，用於將消息發送到特定連接、所有連接或屬於特定使用者或放置在任意組中的連接子集。

## <a name="references"></a>參考
   - [Azure 門戶](https://portal.azure.com)
   - [Azure SignalR Service 文件](/azure/azure-signalr/signalr-overview)
   - [教學課程](/azure/azure-signalr/signalr-tutorial-authenticate-azure-functions)