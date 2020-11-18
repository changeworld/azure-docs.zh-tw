---
title: 教學課程 - 在 Azure App Service 上建置和保護 Web 應用程式 | Azure
description: 在本教學課程中，您會了解如何使用 Azure App Service 來建置 Web 應用程式、啟用驗證、呼叫 Azure 儲存體，以及呼叫 Microsoft Graph。
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428220"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>教學課程：在 App Service 中啟用驗證並存取儲存體和 Microsoft Graph

本教學課程說明常見的應用程式案例，您會了解如何：

- [(A) 設定 Web 應用程式的驗證](scenario-secure-app-authentication-app-service.md)並限制對您組織中使用者的存取權。
- [(B) 安全地存取 Azure 儲存體](scenario-secure-app-access-storage.md)，代表 Web 應用程式使用受控識別進行。
- (C) 在 Microsoft Graph 中[代表已登入使用者](scenario-secure-app-access-microsoft-graph-as-user.md)或[代表 Web 應用程式](scenario-secure-app-access-microsoft-graph-as-app.md)使用受控識別存取資料。
- [清除資源](scenario-secure-app-clean-up-resources.md)，該資源是您為本教學課程建立的。

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Microsoft 身分識別平台中的應用程式案例" border="false":::

若要開始，請了解如何啟用 Web 應用程式的驗證。

> [!div class="nextstepaction"]
> [設定 Web 應用程式的驗證](scenario-secure-app-authentication-app-service.md)
