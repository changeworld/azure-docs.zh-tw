---
title: 受保護的 Web API-總覽
titleSuffix: Microsoft identity platform
description: 瞭解如何建立受保護的 Web API （總覽）。
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f96393adf0eaed8a28bc1a2ec0def6b0386bd7ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701972"
---
# <a name="scenario-protected-web-api"></a>案例：受保護的 Web API

在此案例中，我們將示範如何公開 Web API，以及如何加以保護，讓只有經過驗證的使用者可以存取 API。 您會想要讓已驗證的使用者同時使用公司和學校帳戶，或個人 Microsoft 個人帳戶來使用您的 Web API。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>瞭解

以下是保護 web Api 所需瞭解的一些細節：

- 您的應用程式註冊必須公開至少一個範圍。 Web API 所接受的權杖版本取決於登入物件。
- Web API 的程式碼設定必須驗證呼叫 Web API 時所使用的權杖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-protected-web-api-app-registration.md)
