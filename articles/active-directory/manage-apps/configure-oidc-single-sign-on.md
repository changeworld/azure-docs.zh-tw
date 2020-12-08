---
title: 瞭解 Azure Active Directory 中應用程式 (SSO) 的 OIDC 型單一登入
description: 瞭解 Azure Active Directory 中應用程式 (SSO) 的 OIDC 型單一登入。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperfq2
ms.openlocfilehash: 487bf3c83be16ee80838b3bbe8bf8532eb6ffe12
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780054"
---
# <a name="understand-oidc-based-single-sign-on"></a>瞭解 OIDC 為基礎的單一登入
在應用程式管理的 [快速入門系列](view-applications-portal.md) 中，您已瞭解如何使用 Azure AD 作為應用程式 (IdP) 的身分識別提供者。 本文將詳細說明使用 OpenID Connect 標準來執行單一登入的應用程式。 

## <a name="before-you-begin"></a>開始之前
將應用程式新增至 Azure Active Directory 租使用者的程式，取決於所執行之應用程式的單一登入類型。 若要深入瞭解可使用 Azure AD 進行身分識別管理的應用程式可用的單一登入選項，請參閱 [單一登入選項](sso-options.md)。 本文涵蓋以 OIDC 為基礎的應用程式。


## <a name="basic-oidc-configuration"></a>基本 OIDC 設定
在 [快速入門系列](add-application-portal-setup-oidc-sso.md)中，有一篇文章說明如何設定單一登入。 在其中，您會瞭解如何將 OIDC 型應用程式新增至您的 Azure 租使用者。

新增使用 OIDC standard 進行單一登入的應用程式，會有很大的好處。 以下顯示如何將 OIDC 型應用程式新增至您的租使用者的簡短影片。

在 Azure Active Directory 中新增以 OIDC 為基礎的應用程式

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

若要深入瞭解使用者和系統管理員的同意，請參閱 [瞭解使用者和系統管理員同意](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)。

## <a name="next-steps"></a>後續步驟

- [應用程式管理快速入門系列](add-application-portal-setup-oidc-sso.md)
- [單一登入選項](sso-options.md)
- [操作說明：讓任何 Azure Active Directory (AD) 使用者以多租用戶應用程式的模式登入](../develop/howto-convert-app-to-be-multi-tenant.md)
