---
title: 移至生產 web 應用程式以呼叫 web Api-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將呼叫 web Api 的 web 應用程式移至生產環境。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6404d00b87b9ee745b9e3a92c646404e574417c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881656"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>呼叫 web Api 的 web 應用程式：移至生產環境

現在您已瞭解如何取得權杖以呼叫 web Api，請瞭解如何進入生產環境。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請嘗試 ASP.NET Core web 應用程式的完整、漸進式教學課程。 教學課程會：

- 示範如何將使用者登入多個物件或國家雲端，或使用社交身分識別。
- 呼叫 Microsoft Graph。
- 會呼叫數個 Microsoft Api。
- 處理累加式同意。
- 呼叫您自己的 Web API。

> [!div class="nextstepaction"]
> [ASP.NET Core Web 應用程式教學課程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
