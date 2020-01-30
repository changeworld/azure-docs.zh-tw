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
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773344"
---
# <a name="scenario-protected-web-api"></a>案例：受保護的 Web API

在此案例中，您將瞭解如何公開 Web API。 您也將瞭解如何保護 Web API，讓只有經過驗證的使用者可以存取它。

若要使用您的 Web API，您必須同時啟用具有公司和學校帳戶的已驗證使用者，或啟用 Microsoft 個人帳戶。

## <a name="prerequisites"></a>必要條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>瞭解

以下是保護 web Api 所需瞭解的特定資訊：

- 您的應用程式註冊必須公開至少一個範圍。 Web API 所接受的權杖版本取決於登入物件。
- Web API 的程式碼設定必須驗證呼叫 Web API 時所使用的權杖。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-protected-web-api-app-registration.md)
