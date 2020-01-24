---
title: 將 web 應用程式呼叫 web Api 移至生產環境-Microsoft 身分識別平臺 |Azure
description: 瞭解如何將呼叫 web Api 的 web 應用程式移至生產環境。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 439b7e07b2e2aa77afd027fa57cc6f7705626d81
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701632"
---
# <a name="web-app-that-calls-web-apis---move-to-production"></a>呼叫 web Api 的 web 應用程式-移至生產環境

現在您已瞭解如何取得權杖以呼叫 Web Api，請瞭解如何進入生產環境。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

若要深入瞭解，請嘗試完整的 ASP.NET Core web 應用程式漸進式教學課程，其中顯示：

- 如何使用多個目標物件、國家雲端或社交身分識別登入使用者
- 呼叫 Microsoft Graph
- 呼叫數個 Microsoft Api
- 處理累加式同意
- 呼叫您自己的 Web API

> [!div class="nextstepaction"]
> [ASP.NET Core web 應用程式教學課程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
