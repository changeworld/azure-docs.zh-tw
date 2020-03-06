---
title: 使用 Visual Studio App Center 和 Azure 服務將驗證新增至您的行動應用程式
description: 瞭解可協助設定使用者驗證並讓行動應用程式使用社交帳戶進行驗證的服務（例如 Visual Studio App Center）、Azure Active Directory 及自訂驗證。
author: elamalani
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 261c12e63f8f348e1673dbc46c89c614caf3283b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78296349"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>在您的行動應用程式中新增驗證和管理使用者身分識別

在您的應用程式中查看使用者及其行為，可讓開發人員為他們建立量身打造的體驗，藉以更有效地與使用者互動。 無論您是為組織內的使用者建立共同作業應用程式的應用程式開發人員，或是您要建立下一個社交網路平臺，都需要一種方法來驗證使用者及管理使用者身分識別。 身分識別管理服務是行動後端服務最重要的一項功能。

使用下列服務，在您的行動應用程式中啟用使用者驗證。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 驗證](/appcenter/auth/)是以雲端為基礎的身分識別管理服務，可供開發人員用來驗證使用者和管理使用者身分識別。 App Center 驗證也會與 Visual Studio App Center 的其他部分整合。 開發人員可以使用使用者身分識別來查看其他服務中的[使用者資料](/appcenter/data/index)，甚至[將推播通知傳送給使用者，而不是個別的裝置](/appcenter/push/push-to-user#setting-user-identity)。 

**主要功能**
- 由 Azure Active Directory B2C （Azure AD B2C）提供技術支援。 
    - 企業級。
    - 具有高可用性。
    - 安全和全域服務。
- 攜帶您自己的身分識別和選項，以使用其他熱門的身分識別和存取管理提供者，例如 Auth0 和 Firebase。
- Azure Active Directory 支援。
    - 連接現有的 Azure AD 租使用者。 
    - 啟用對公司網域進行驗證。
    - 管理敏感性資料的存取權。
- 簡單的使用者體驗和神奇 SDK 體驗，方法是使用 Visual Studio App Center SDK 包裝 Microsoft 驗證程式庫。
- 適用于 iOS、Android、Xamarin 和 React Native 的平臺支援。

**參考**
- [使用 Visual Studio App Center 註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [開始使用 App Center 驗證](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)是一種企業對消費者（B2C）身分識別管理服務，開發人員可用來驗證其客戶。 這個白卷標服務可讓開發人員自訂及控制使用者如何安全地與 web、桌上型電腦、行動裝置或單頁應用程式互動。 透過 Azure AD B2C，使用者可以註冊、登入、重設密碼及編輯設定檔。 Azure AD B2C 可實作某種形式的 OpenID Connect 和 OAuth 2.0 通訊協定。 

**主要功能**
- 使用慣用的身分識別提供者安全地驗證客戶。
- 管理客戶身分識別和存取權。
- 取得社交媒體（例如 Facebook、GitHub、Google、LinkedIn、Twitter、WeChat 和 Weibo）的登入支援。
- 使用業界標準通訊協定（例如 OpenID Connect 或 SAML）連接到您的使用者帳戶，以在各種平臺上進行身分識別管理。
- 提供品牌的註冊和登入體驗。
- 輕鬆地與 CRM 資料庫、行銷分析工具及帳戶驗證系統整合。
- 為客戶捕獲登入、喜好設定和轉換資料。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [Azure AD B2C 文件](/azure/active-directory-b2c/)
- [快速入門](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [範例](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)是 Microsoft 的雲端式身分識別和存取管理服務，可協助您的員工登入並取得存取權：
- 外部資源，例如 Microsoft Office 365、Azure 入口網站，以及數以千計的其他軟體即服務（SaaS）應用程式。
- 內部資源，例如公司網路和內部網路上的應用程式，以及您自己的組織所開發的任何雲端應用程式。

**主要功能**
- 將使用者連接至所需的應用程式，順暢且高度安全的存取。
- 根據使用者、位置、裝置、資料和應用程式內容，提供身分識別和存取的完整身分識別保護和增強安全性。
- 數千個預先整合的應用程式，適用于商業和自訂應用程式，例如 Office 365、Salesforce.com 和 Box。
- 能夠大規模管理存取。

**參考**
- [Azure 入口網站](https://portal.azure.com/)
- [什麼是 Azure AD？](/azure/active-directory/fundamentals/active-directory-whatis)
- [開始使用 Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [快速入門](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)