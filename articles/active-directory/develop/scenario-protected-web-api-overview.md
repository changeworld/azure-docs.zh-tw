---
title: 受保護的 web API-總覽
titleSuffix: Microsoft identity platform
description: 瞭解如何 (總覽) 建立受保護的 web API。
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
ms.openlocfilehash: c9cc8e6fcc796cfc7a09cf7bc1431646a6603af0
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442968"
---
# <a name="scenario-protected-web-api"></a>案例：受保護的 web API

在此案例中，您會瞭解如何公開 web API。 您也會瞭解如何保護 web API，讓只有經過驗證的使用者可以存取它。

若要使用您的 web API，您必須啟用具有公司和學校帳戶的已驗證使用者，或啟用 Microsoft 個人帳戶。

## <a name="specifics"></a>特性

以下是您需要知道用來保護 web Api 的特定資訊：

- 您的應用程式註冊必須公開至少一個 *範圍* 或一個 *應用程式角色* 。
  - 範圍是由代表使用者呼叫的 web Api 所公開。
  - 應用程式角色是由 daemon 應用程式所呼叫的 web Api 所公開， (會代表自己呼叫您的 web API) 。
- 如果您建立新的 web API 應用程式註冊，請選擇您的 web API 所接受的 [存取權杖版本](reference-app-manifest.md#accesstokenacceptedversion-attribute) `2` 。 針對舊版 web Api，可接受的權杖版本可以是 `null` ，但此值會限制只有組織的登入物件，且不支援 (MSA) 的個人 Microsoft 帳戶。
- Web API 的程式碼設定必須驗證呼叫 web API 時所使用的權杖。
- 控制器動作中的程式碼必須驗證權杖中的角色或範圍。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

請移至此案例的 [應用程式註冊](scenario-protected-web-api-app-registration.md)中的下一篇文章。
