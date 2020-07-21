---
title: Azure CLI 範例 - Azure SignalR Service
description: 遵循實際範例，瞭解 Azure SignalR Service 的 Azure CLI。 您將瞭解如何使用更多 Azure 服務來建立 SignalR Service。
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 99ebe9c078ef4775e8f0df0545fb0af4b3d1057b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515235"
---
# <a name="azure-cli-reference"></a>Azure CLI 參考

下表包含可針對 Azure SignalR Service 使用 Azure CLI 的 Bash 指令碼連結。

| 指令碼 | 說明 |
|-|-|
|**建立**||
| [建立新的 SignalR 服務和資源群組](scripts/signalr-cli-create-service.md) | 在資源群組中建立具有隨機名稱的新 Azure SignalR Service 資源。  |
|**整合**||
| [建立新 SignalR Service 和設定為使用 SignalR 的 Web 應用程式](scripts/signalr-cli-create-with-app-service.md) | 在資源群組中建立具有隨機名稱的新 Azure SignalR Service 資源。 也會加入新的 Web 應用程式，並 App Service 方案來裝載使用 SignalR Service 的 ASP.NET Core Web 應用程式。 Web 應用程式會透過「應用程式設定」來設定，以連線至新的 SignalR 服務資源。 |
| [建立新 SignalR Service 和設定為使用 SignalR 與 GitHub OAuth 的 Web 應用程式](scripts/signalr-cli-create-with-app-service-github-oauth.md) | 在資源群組中建立具有隨機名稱的新 Azure SignalR Service 資源。 也會加入新的 Azure Web 應用程式和主控方案，以裝載使用 SignalR Service 的 ASP.NET Core Web 應用程式。 Web 應用程式會透過應用程式設定來設定，以使用連接字串連線至新的 SignalR 服務資源，以及使用用戶端密碼來支援 [GitHub 驗證](https://developer.github.com/v3/guides/basics-of-authentication/)，如[驗證教學課程](signalr-concept-authenticate-oauth.md)所述。 Web 應用程式也會設定為使用本機 Git 存放庫部署來源。 |
| | |
