---
title: Azure Active Directory authentication 和同步處理通訊協定總覽
description: 整合 Azure AD 與舊版驗證通訊協定和同步處理模式的架構指引
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
ms.openlocfilehash: ab63bc5bd2819a239741da525eebb2404a47bbf9
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441193"
---
# <a name="azure-active-directory-integrations-with-authentication-and-synchronization-protocols"></a>Azure Active Directory 與驗證和同步處理通訊協定的整合

Microsoft Azure Active Directory (Azure AD) 可讓您整合許多驗證和同步處理通訊協定。 驗證整合可讓您使用 Azure AD 及其安全性和管理功能，幾乎不需要變更使用舊版驗證方法的應用程式。 同步整合可讓您將使用者和群組資料同步至 Azure AD，然後再將使用者 Azure AD 管理功能。 某些同步處理模式也會啟用自動化布建。

## <a name="legacy-authentication-protocols"></a>舊版驗證通訊協定

下表提供驗證 Azure AD 與舊版驗證通訊協定和其功能的整合。 選取驗證通訊協定的名稱以查看

* 詳細描述

* 使用時機

* 架構圖

* 系統元件的說明

* 如何實行整合的連結

 

| 驗證通訊協定| 驗證| 授權| 多因素驗證| 條件式存取 |
| - |- | - | - | - |
| [標頭型驗證](auth-header-based.md)|![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [LDAP 驗證](auth-ldap.md)| ![核取標記](./media/authentication-patterns/check.png)| | |  |
| [OAuth 2.0 驗證](auth-oauth2.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [OIDC authentication](auth-oidc.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [以密碼為基礎的 SSO 驗證](auth-password-based-sso.md )| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [RADIUS 驗證]( auth-radius.md)| ![核取標記](./media/authentication-patterns/check.png)| | ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [遠端桌面閘道服務](auth-remote-desktop-gateway.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [安全殼層 (SSH)](auth-ssh.md) |  ![核取標記](./media/authentication-patterns/check.png)| | ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [SAML 驗證](auth-saml.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |
| [Windows 驗證-Kerberos 限制委派](auth-kcd.md)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png)| ![核取標記](./media/authentication-patterns/check.png) |


 
## <a name="synchronization-patterns"></a>同步處理模式

下表提供與同步處理模式和其功能的 Azure AD 整合。 選取要查看的模式名稱

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

