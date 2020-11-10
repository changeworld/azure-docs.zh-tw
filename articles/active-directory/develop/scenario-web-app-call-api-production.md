---
title: 移至生產環境的 web 應用程式呼叫 web Api-Microsoft 身分識別平臺 |蔚藍
description: 瞭解如何移至生產環境的 web 應用程式以呼叫 web Api。
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
ms.openlocfilehash: f38755e669a62e9fe74aa6281daccdf39e1f117c
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443563"
---
# <a name="a-web-app-that-calls-web-apis-move-to-production"></a>呼叫 web Api 的 web 應用程式：移至生產環境

既然您已瞭解如何取得權杖來呼叫 web Api，請瞭解如何移至生產環境。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>後續步驟

試用 ASP.NET Core web 應用程式的完整、漸進式教學課程，以深入瞭解。 教學課程會：

- 說明如何將使用者登入多個使用者或國家雲端，或使用社交身分識別。
- 呼叫 Microsoft Graph。
- 呼叫數個 Microsoft Api。
- 處理累加式同意。
- 呼叫您自己的 web API。

[ASP.NET Core Web 應用程式教學課程](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial#scope-of-this-tutorial)

<!--- Removing this diagram as it's already shown from the next step linked tutorial

![Tutorial overview](media/scenarios/aspnetcore-webapp-tutorial.svg)

--->
