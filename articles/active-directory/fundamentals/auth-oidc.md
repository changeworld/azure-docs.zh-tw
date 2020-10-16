---
title: 使用 Azure Active Directory OpenID Connect 驗證
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
ms.openlocfilehash: c6f2ad3b5e86eebfc2d6f1f42f8a2ab0520144b5
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114126"
---
# <a name="openid-connect-authentication-with-azure-active-directory"></a>使用 Azure Active Directory OpenID Connect 驗證

OpenID Connect (OIDC) 是一種驗證通訊協定，它是以用於授權) 的 OAuth2 通訊協定 (為基礎。 OIDC 會使用來自 OAuth2 的標準化訊息流程來提供身分識別服務。 

OIDC 的設計目標是「簡化簡單、複雜的事物」。 OIDC 可讓開發人員在網站和應用程式之間驗證使用者，而不需要擁有及管理密碼檔案。 這會為應用程式產生器提供安全的方式，以驗證目前使用瀏覽器或連接至應用程式之原生應用程式的人員身分識別。

使用者的驗證必須在將檢查使用者會話或認證的身分識別提供者上進行。 若要這樣做，您需要信任的代理程式。 原生應用程式通常會針對該目的啟動系統瀏覽器。 內嵌的視圖被視為不受信任，因為沒有任何專案可防止應用程式對使用者密碼進行窺探。 

除了驗證之外，使用者也可以要求同意。 同意是使用者允許應用程式存取受保護資源的明確許可權。 同意與驗證不同，因為同意只需要針對資源提供一次。 同意會維持有效，直到使用者或系統管理員手動撤銷授與。 

## <a name="use-when"></a>使用時機

需要使用者同意和網頁登入。

![架構圖](./media/authentication-patterns/oidc-auth.png)

## <a name="components-of-system"></a>系統的元件

* **使用者**：從應用程式要求服務。

* **受信任的代理程式**：使用者所互動的元件。 此受信任的代理程式通常是網頁瀏覽器。

* **應用**程式：應用程式或資源伺服器是資源或資料所在的位置。 它會信任身分識別提供者，以安全地驗證及授權受信任的代理程式。 

* **Azure AD**： OIDC 提供者（也稱為身分識別提供者）會安全地管理任何與使用者資訊、使用者存取權，以及流程中合作物件之間的信任關係。 它會驗證使用者的身分識別、授與及撤銷資源的存取權，以及發行權杖。 

## <a name="implement-oidc-with-azure-ad"></a>使用 Azure AD 來執行 OIDC

* [整合應用程式與 Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Microsoft 身分識別平臺上的 OAuth 2.0 和 OpenID Connect 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [Microsoft 身分識別平台和 OpenID Connect 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) 

* [在 Azure Active Directory B2C 中利用 OpenID Connect 的 Web 登入](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) 

* [使用 OpenID Connect 與 Azure AD 保護您的應用程式](https://docs.microsoft.com/learn/modules/secure-app-with-oidc-and-azure-ad/) 

 
