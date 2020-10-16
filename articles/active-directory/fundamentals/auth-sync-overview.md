---
title: Azure Active Directory authentication 和同步處理通訊協定總覽
description: 達到此驗證模式的架構指引
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d881dc3fe3e3caa1058cf97834735910b0de1d9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114154"
---
# <a name="azure-active-directory-integrations-with-legacy-authentication-and-synchronization-protocols"></a>使用舊版驗證和同步處理通訊協定的 Azure Active Directory 整合

Microsoft Azure Active Directory (Azure AD) 可讓您整合許多驗證和同步處理通訊協定。 驗證整合可讓您使用 Azure AD 及其安全性和管理功能，幾乎不需要變更使用舊版驗證方法的應用程式。 同步整合可讓您將使用者和群組資料同步至 Azure AD，然後再將使用者 Azure AD 管理功能。 某些同步處理模式也會啟用自動化布建。

## <a name="authentication-patterns"></a>驗證模式

下表提供驗證模式和其功能。 選取要查看的驗證模式名稱

* 詳細描述

* 使用時機

* 架構圖

* 系統元件的說明

* 如何實行整合的連結

 

| 驗證模式| 驗證| 授權| 多因素驗證| 條件式存取 |
| - |- | - | - | - |
| [以標頭為基礎的驗證](auth-header-based.md)|![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [LDAP 驗證](auth-ldap.md)| ![核取標記](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 驗證](auth-oauth2.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [OIDC authentication](auth-oidc.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [以密碼為基礎的 SSO 驗證](auth-password-based-sso.md )| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [RADIUS 驗證]( auth-radius.md)| ![核取標記](./media/authentication-patterns/check.png)| | ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [遠端桌面閘道服務](auth-remote-desktop-gateway.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [SAML 驗證](auth-saml.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [Windows 驗證-Kerberos 限制委派](auth-kcd.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>同步處理模式

下表提供同步處理模式和其功能。 選取要查看的模式名稱

* 詳細描述

* 使用時機

* 架構圖

* 系統元件的說明

* 如何實行整合的連結



| 同步處理模式| 目錄同步作業| 使用者佈建 |
| - | - | - |
| [目錄同步作業](sync-directory.md)| ![核取標記](./media/authentication-patterns/check.png)|  |
| [LDAP 同步處理](sync-ldap.md)| ![核取標記](./media/authentication-patterns/check.png)|  |
| [SCIM 同步處理](sync-scim.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |

