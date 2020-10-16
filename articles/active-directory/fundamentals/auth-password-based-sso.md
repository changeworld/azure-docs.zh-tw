---
title: 以密碼為基礎的驗證 Azure Active Directory
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
ms.openlocfilehash: f05bf8f5f7ec4907c2cd61ff48e3438dfa1e097a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114125"
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

* **MyApps 延伸**模組：識別已設定的密碼型 SSO 應用程式，並將認證提供給登入表單。 MyApps 延伸模組會安裝在網頁瀏覽器上。 

* **Azure AD**：驗證使用者。

## <a name="implement-password-based-sso-with-azure-ad"></a>使用 Azure AD 來執行密碼型 SSO

* [什麼是以密碼為基礎的 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [針對雲端應用程式設定以密碼為基礎的 SSO ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [使用應用程式 Proxy 為內部部署應用程式設定密碼型 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
