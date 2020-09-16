---
title: 應用程式的終端使用者體驗-Azure Active Directory
description: Azure Active Directory (Azure AD) 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 8968fd54968f3115641d2315a534ba61a247a06d
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90605119"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory 中的應用程式使用者體驗

Azure Active Directory (Azure AD) 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式：

* Azure AD 我的應用程式
* Microsoft 365 應用程式啟動器
* 直接登入同盟應用程式
* 同盟、密碼或現有應用程式的深層連結

您選擇要在組織中部署哪一種方法由您自行決定。

## <a name="azure-ad-my-apps"></a>Azure AD 我的應用程式

我的應用程式 https://myapps.microsoft.com 是以網頁為基礎的入口網站，可讓使用者在 Azure Active Directory 中具有組織帳戶的使用者，來查看和啟動 Azure AD 系統管理員已授與存取權的應用程式。 如果您是 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/)的終端使用者，您也可以透過我的應用程式使用自助式群組管理功能。

依預設，所有應用程式都會在單一頁面上一起列出。 但是，您可以使用集合將相關的應用程式群組在一起，並將它們顯示在不同的索引標籤上，使其更容易尋找。 例如，您可以使用集合來建立特定工作角色、工作、專案等的應用程式邏輯群組。 如需詳細資訊，請參閱 [我的應用程式入口網站上的建立集合](access-panel-collections.md)。 

我的應用程式與 Azure 入口網站不同，不需要使用者擁有 Azure 訂用帳戶或 Microsoft 365 訂用帳戶。

如需有關 Azure AD 我的應用程式的詳細資訊，請參閱 [我的應用程式的簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 應用程式啟動器

針對已部署 Microsoft 365 的組織，透過 Azure AD 指派給使用者的應用程式也會出現在 Office 365 入口網站中 [https://portal.office.com/myapps](https://portal.office.com/myapps) 。 這可讓組織中的使用者輕鬆且方便地啟動其應用程式，而不需要使用第二個入口網站，而且是使用 Microsoft 365 之組織的建議應用程式啟動解決方案。

如需有關 Office 365 應用程式啟動程式的詳細資訊，請參閱 [讓您的應用程式出現在 Office 365 應用程式啟動程式中](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)。

## <a name="direct-sign-on-to-federated-apps"></a>直接登入同盟應用程式

大部分支援 SAML 2.0、WS-同盟或 OpenID Connect 的同盟應用程式也支援使用者在應用程式啟動，然後再透過 Azure AD 的自動重新導向或按一下連結登入。 這就是所謂的服務提供者起始登入，而 Azure AD 應用程式資源庫中大部分的同盟應用程式都支援此 (如需詳細資料) ，請參閱 Azure 入口網站中的應用程式的單一登入設定 wizard 連結的檔。

## <a name="direct-sign-on-links"></a>直接登入連結

Azure AD 也支援對支援密碼單一登入、已連結的單一登入，以及任何形式的同盟單一登入的個別應用程式使用直接單一登入連結。

這些連結是特別撰寫的 Url，會透過特定應用程式的 Azure AD 登入程式傳送給使用者，而不需要使用者從 Azure AD 我的應用程式或 Microsoft 365 啟動它們。 這些 **使用者存取 url** 可在 [可用的企業應用程式] 的屬性下找到。 在 Azure 入口網站中，選取 [Azure Active Directory] > [企業應用程式]。 選取應用程式，然後選取 [ **屬性**]。

![Twitter 屬性中的使用者存取 URL 範例](media/end-user-experiences/direct-sign-on-link.png)

您可以複製這些連結，然後貼到任何您想要提供選取應用程式登入連結的位置。 可以是在電子郵件中，或是任何您已設定使用者應用程式存取權的自訂網頁型入口網站中。 以下是 Azure AD 直接單一登入 Twitter 的 URL 範例：

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

類似于我的應用程式的組織特定 Url，您可以在 *myapps.microsoft.com* 網域之後，為您的目錄新增其中一個作用中或已驗證的網域，以進一步自訂此 URL。 這樣可以確保使用者不需要先輸入其使用者識別碼，登入頁面就可以立即載入任何組織品牌：

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

當授權的使用者按一下其中一個應用程式特定的連結時，他們會先看到組織的登入頁面 (假設他們尚未登入) ，而且登入之後會重新導向至其應用程式，而不會先在我的應用程式上停止。 如果使用者遺漏存取應用程式所需的先決條件，例如密碼單一登入瀏覽器延伸，則連結將會提示使用者安裝遺漏的延伸。 如果應用程式的單一登入組態有變更，連結 URL 也會維持不變。

這些連結使用與我的應用程式和 Microsoft 365 相同的存取控制機制，而且只有在 Azure 入口網站中指派給應用程式的使用者或群組才能成功驗證。 不過，未經授權的使用者將會看到一則訊息，說明它們尚未被授與存取權，並提供載入我的應用程式的連結，以查看他們具有存取權的可用應用程式。

## <a name="next-steps"></a>後續步驟

* [應用程式管理快速入門系列](view-applications-portal.md)
* [什麼是單一登入？](what-is-single-sign-on.md)
* [整合 Azure Active Directory 與應用程式入門指南](plan-an-application-integration.md)
