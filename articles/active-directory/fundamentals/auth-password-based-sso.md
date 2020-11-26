---
title: 以密碼為基礎的驗證 Azure Active Directory
description: 使用 Azure Active Directory 達成密碼驗證的架構指引。
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
ms.openlocfilehash: 5bd6a5c8af117bf6cb39969a5f1b1f17ff08681c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172817"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>以密碼為基礎的驗證 Azure Active Directory

以密碼為基礎的單一 Sign-On (SSO) 會針對應用程式使用現有的驗證程式。 當您啟用密碼型 SSO 時，Azure Active Directory (Azure AD) 會收集、加密並安全地將使用者認證儲存在目錄中。 當使用者嘗試登入時，Azure AD 提供使用者名稱和密碼給應用程式。

當應用程式使用使用者名稱和密碼進行驗證，而不是存取權杖和標頭時，請選擇密碼型 SSO。 以密碼為基礎的 SSO 支援任何具有 HTML 登入頁面的雲端式應用程式。 

## <a name="use-when"></a>使用時機

您必須使用預先驗證進行保護，並透過密碼保存庫將 SSO 提供給 web 應用程式。

![架構圖](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>系統的元件

* **使用者**：從我的應用程式或直接造訪網站存取以格式為基礎的應用程式。 

* **Web 瀏覽器**：使用者與之互動的元件，以存取應用程式的外部 URL。 使用者透過 MyApps 延伸模組存取表單架構應用程式。 

* **MyApps 延伸** 模組：識別已設定的密碼型 SSO 應用程式，並將認證提供給登入表單。 MyApps 延伸模組會安裝在網頁瀏覽器上。 

* **Azure AD**：驗證使用者。

## <a name="implement-password-based-sso-with-azure-ad"></a>使用 Azure AD 來執行密碼型 SSO

* [什麼是以密碼為基礎的 SSO](../manage-apps/what-is-single-sign-on.md) 

* [針對雲端應用程式設定以密碼為基礎的 SSO ](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)

* [使用應用程式 Proxy 為內部部署應用程式設定密碼型 SSO](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)

