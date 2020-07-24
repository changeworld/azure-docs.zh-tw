---
title: 受保護的 Web API-總覽
titleSuffix: Microsoft identity platform
description: 瞭解如何建立受保護的 Web API （總覽）。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4e530f76c8301dc74f73b675befa6f0710aedab7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026623"
---
# <a name="scenario-protected-web-api"></a>案例：受保護的 Web API

在此案例中，您將瞭解如何公開 Web API。 您也將瞭解如何保護 Web API，讓只有經過驗證的使用者可以存取它。

若要使用您的 Web API，您必須同時啟用具有公司和學校帳戶的已驗證使用者，或啟用 Microsoft 個人帳戶。

## <a name="prerequisites"></a>先決條件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>特性

以下是保護 web Api 所需瞭解的特定資訊：

- 您的應用程式註冊必須公開至少一個*範圍*或一個*應用程式角色*。
  - 範圍是由代表使用者呼叫的 web Api 所公開。
  - 應用程式角色是由 daemon 應用程式所呼叫的 web Api 所公開（會代表自己呼叫您的 Web API）。
- 如果您建立新的 Web API 應用程式註冊，請選擇您的 Web API 接受的[存取權杖版本](reference-app-manifest.md#accesstokenacceptedversion-attribute) `2` 。 對於舊版 web Api，接受的權杖版本可以是 `null` ，但此值會將登入物件限制為僅限組織，且不支援個人 Microsoft 帳戶（MSA）。
- Web API 的程式碼設定必須驗證呼叫 Web API 時所使用的權杖。
- 控制器動作中的程式碼必須驗證權杖中的角色或範圍。

## <a name="next-steps"></a>接下來的步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-protected-web-api-app-registration.md)
