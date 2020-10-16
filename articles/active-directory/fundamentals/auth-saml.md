---
title: 使用 Azure Active Directory 的 SAML 驗證
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
ms.openlocfilehash: 05b13e04db8e83a8a10c2d7fe0aea202dfa3b69c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114157"
---
# <a name="saml-authentication-with-azure-active-directory"></a>使用 Azure Active Directory 的 SAML 驗證

安全性聲明標記語言 (SAML) 是在身分識別提供者與服務提供者之間交換驗證和授權資料的開放標準。 SAML 是安全性判斷提示的 XML 型標記語言，也就是服務提供者用來進行存取控制決策的語句。 

SAML 規格會定義三個角色：

* 主體，通常是使用者
* 身分識別提供者 (IdP) 
* 服務提供者 (SP) 


## <a name="use-when"></a>使用時機

您需要為企業 SAML 應用程式提供單一登入 (SSO) 體驗。

SAML 定址為 SSO 的其中一個最重要的使用案例，特別是在安全性網域之間擴充 SSO，也有其他使用案例 (稱為設定檔) 。 

![架構圖](./media/authentication-patterns/saml-auth.png)

## <a name="components-of-system"></a>系統的元件

* **使用者**：從應用程式要求服務。

* **Web 瀏覽器**：與使用者互動的元件。

* **Web 應用**程式：支援 SAML 並使用 Azure AD 作為 IdP 的企業應用程式。

* **Token**： saml 判斷提示 (也稱為 saml 權杖) ，它會攜帶 IdP 對主體 (使用者) 所做的宣告集。 它包含驗證資訊、屬性和授權決策語句。

* **Azure AD**：為 SAML 應用程式提供 SSO 和多重要素驗證的 Enterprise cloud IdP。 它會同步處理、維護及管理使用者的身分識別資訊，同時提供驗證服務給信賴應用程式。 

## <a name="implement-saml-authentication-with-azure-ad"></a>使用 Azure AD 來執行 SAML 驗證

* [使用 Azure Active Directory 整合 SaaS 應用程式的教學課程](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 

* [為非資源庫應用程式設定 SAML 型單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 

* [Azure AD 如何使用 SAML 通訊協定](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)
