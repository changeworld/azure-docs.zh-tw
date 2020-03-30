---
title: 使用視覺化工作室應用中心和 Azure 服務向移動應用添加身份驗證
description: 瞭解説明設置使用者身份驗證並使移動應用程式使用社交帳戶、Azure 活動目錄和自訂身份驗證進行身份驗證的服務（如 Visual Studio 應用中心）。
author: codemillmatt
ms.assetid: 34a8a070-2222-4faf-9090-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 820005bca008ea3bdb59659c815da3ec36beb0d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241042"
---
# <a name="add-authentication-and-manage-user-identities-in-your-mobile-apps"></a>在移動應用中添加身份驗證和管理使用者身份

通過查看使用者及其在整個應用程式中的行為，開發人員可以通過為使用者創建量身定制的體驗來更好地吸引使用者。 無論您是為組織內的使用者構建協作應用程式的應用程式開發人員，還是正在創建下一個社交網路平臺，您都需要一種方法來驗證使用者和管理使用者身份。 身份管理服務是移動後端服務最重要的功能之一。

使用以下服務在移動應用中啟用使用者身份驗證。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[應用中心身份驗證](/appcenter/auth/)是一種基於雲的身份管理服務，開發人員可以使用該服務對使用者進行身份驗證和管理使用者身份。 應用中心 Auth 還與視覺工作室應用中心的其他部分集成。 開發人員可以使用使用者身份查看其他服務中的[使用者資料](/appcenter/data/index)，甚至[向使用者而不是單個設備發送推送通知](/appcenter/push/push-to-user#setting-user-identity)。 

**主要功能**
- 由 Azure 活動目錄 B2C（Azure AD B2C）提供支援。 
    - 企業級。
    - 具有高可用性。
    - 安全和全球服務。
- 攜帶您自己的身份以及使用其他常用標識和訪問管理提供程式（如 Auth0 和 Firebase）的選項。
- Azure 活動目錄支援。
    - 連接現有的 Azure AD 租戶。 
    - 啟用針對公司域的身份驗證。
    - 管理對敏感性資料的訪問。
- 通過將 Microsoft 身份驗證庫與視覺化工作室應用中心 SDK 包裝，實現簡單的使用者體驗和神奇的 SDK 體驗。
- 對 iOS、Android、Xamarin 和反應本機平臺支援。

**引用**
- [使用視覺化工作室應用中心註冊](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs) 
- [開始使用應用中心 Auth](/appcenter/auth/)

## <a name="azure-active-directory-b2c"></a>Azure Active Directory B2C
[Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)是一種企業對消費者 （B2C） 標識管理服務，開發人員可以使用該服務來驗證其客戶。 此白標服務使開發人員能夠自訂和控制使用者如何安全地與其 Web、桌面、移動或單頁應用程式進行交互。 透過 Azure AD B2C，使用者可以註冊、登入、重設密碼及編輯設定檔。 Azure AD B2C 可實作某種形式的 OpenID Connect 和 OAuth 2.0 通訊協定。 

**主要功能**
- 使用首選身份供應商安全地驗證客戶。
- 管理客戶身份和存取權限。
- 獲得對Facebook、GitHub、谷歌、LinkedIn、推特、微信和微博等社交媒體的登錄支援。
- 使用行業標準協定（如 OpenID Connect 或 SAML）連接到使用者帳戶，使身份管理在各種平臺上成為可能。
- 提供品牌註冊和登錄體驗。
- 輕鬆與 CRM 資料庫、行銷分析工具和帳戶驗證系統集成。
- 捕獲客戶的登錄、首選項和轉換資料。

**引用**
- [Azure 門戶](https://portal.azure.com/)
- [Azure AD B2C 文檔](/azure/active-directory-b2c/)
- [快速入門](/azure/active-directory-b2c/active-directory-b2c-quickstarts-web-app)
- [樣品](/azure/active-directory-b2c/code-samples)

## <a name="azure-active-directory"></a>Azure Active Directory
[Azure 活動目錄](https://azure.microsoft.com/services/active-directory/)是 Microsoft 基於雲的標識和訪問管理服務，可説明員工登錄並訪問：
- 外部資源，如 Microsoft Office 365、Azure 門戶和數千個其他軟體作為服務 （SaaS） 應用程式。
- 內部資源，例如公司網路和內部網路上的應用程式，以及您自己的組織所開發的任何雲端應用程式。

**主要功能**
- 通過將使用者連接到所需的應用程式，實現無縫、高度安全的訪問。
- 基於使用者、位置、設備、資料和應用程式上下文對身份和訪問進行全面的身份保護和增強的安全性。
- 用於商業和自訂應用程式（如 Office 365、Salesforce.com 和 Box）的數千個預集成應用。
- 能夠大規模管理訪問。

**引用**
- [Azure 門戶](https://portal.azure.com/)
- [什麼是 Azure AD？](/azure/active-directory/fundamentals/active-directory-whatis)
- [開始使用 Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis)
- [快速入門](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)