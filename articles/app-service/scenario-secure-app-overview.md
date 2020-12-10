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
ms.custom: azureday1
ms.openlocfilehash: 17931ee92072059d94cd950e9ce8ef6588f7e891
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905497"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>教學課程：在 App Service 中啟用驗證並存取儲存體和 Microsoft Graph

本教學課程說明常見的應用程式案例，您可以從中了解如何：

- [設定 Web 應用程式的驗證](scenario-secure-app-authentication-app-service.md)並限制對您組織中使用者的存取權。 請參閱圖表中的 A。
- 使用受控識別針對 Web 應用程式[安全地存取 Azure 儲存體](scenario-secure-app-access-storage.md)。 請參閱圖表中的 B。
- 使用受控識別[針對已登入的使用者](scenario-secure-app-access-microsoft-graph-as-user.md)或[針對 Web 應用程式](scenario-secure-app-access-microsoft-graph-as-app.md)存取 Microsoft Graph 中的資料。 請參閱圖表中的 C。
- [清除資源](scenario-secure-app-clean-up-resources.md)，該資源是您為本教學課程建立的。

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="此圖顯示 Microsoft 身分識別平台中的應用程式案例。" border="false":::

若要開始，請了解如何啟用 Web 應用程式的驗證。

> [!div class="nextstepaction"]
> [設定 Web 應用程式的驗證](scenario-secure-app-authentication-app-service.md)
