---
title: 使用 Azure Active Directory 進行 OAUTH 2.0 驗證
description: 使用 Azure Active Directory 達到 OAUTH 2.0 驗證的架構指引。
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
ms.openlocfilehash: daf40a2ced3f753619e9c4723dbe78cd7e51ff21
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577901"
---
# <a name="oauth-20-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 進行 OAuth 2.0 驗證

OAuth 2.0 是適用于授權的產業通訊協定。 它可讓使用者將有限的存取權授與受保護的資源。 OAuth 是專門用來 (HTTP) 的超文字傳輸通訊協定，因此會將用戶端的角色與資源擁有者隔開。 用戶端會要求存取資源擁有者所控制的資源，並由資源伺服器主控。 資源伺服器會在核准資源擁有者時發出存取權杖。 用戶端會使用存取權杖來存取資源伺服器所主控的受保護資源。 

OAuth 2.0 與 OpenID Connect (OIDC) 直接相關。 由於 OIDC 是建置於 OAuth 2.0 之上的驗證和授權層級，因此它不會與 OAuth 1.0 回溯相容。 Azure Active Directory (Azure AD) 支援所有 OAuth 2.0 流程。 

## <a name="use-when"></a>使用時機：

適用于豐富的用戶端 & 新式應用程式案例和 RESTful Web API 存取。

![架構圖表](./media/authentication-patterns/oauth.png)

## <a name="components-of-system"></a>系統的元件

* **使用者** ： (應用程式) ，向 web 應用程式要求服務。 使用者通常是擁有資料的資源擁有者，且具有允許用戶端存取資料或資源的能力。 

* **Web 瀏覽器** ：使用者與其互動的網頁瀏覽器是 OAuth 用戶端。 

* **Web 應用程式** ： web 應用程式（或資源伺服器）是資源或資料所在的位置。 它會信任授權伺服器，以便安全地驗證和授權 OAuth 用戶端。 

* **Azure AD** ： Azure AD 是授權伺服器，也稱為身分識別提供者 (IdP) 。 它會安全地處理與使用者資訊、其存取和信任關係相關的任何動作。 它負責發出可授與及撤銷資源存取權的權杖。

## <a name="implement-oauth-20-with-azure-ad"></a>使用 Azure AD 來執行 OAuth 2。0

* [整合應用程式與 Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [Microsoft 身分識別平臺上的 OAuth 2.0 和 OpenID Connect 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) 

* [應用程式類型和 OAuth2](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types) 

 
