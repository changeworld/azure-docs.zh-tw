---
title: 應用程式的終端使用者體驗-Azure Active Directory
description: Azure Active Directory (Azure AD) 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72b3e37a423442194b81e3d10ecc7157201ff8be
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586048"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory 中的應用程式使用者體驗

Azure Active Directory (Azure AD) 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式：

* Azure AD 存取面板
* Office 365 應用程式啟動程式
* 直接登入同盟應用程式
* 同盟、密碼或現有應用程式的深層連結

您選擇要在組織中部署哪一種方法由您自行決定。

## <a name="azure-ad-access-panel"></a>Azure AD 存取面板

https://myapps.microsoft.com 的存取面板是網頁型入口網站，可讓使用者在 Azure Active Directory 中具有組織帳戶，以查看並啟動 Azure AD 系統管理員已授與他們存取權的應用程式。 如果您是 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/) 的使用者，也可以透過 [存取面板] 使用自助式群組管理功能。

![螢幕擷取畫面顯示 Azure AD 存取面板入口網站](media/what-is-single-sign-on/azure-ad-access-panel.png)

根據預設，所有應用程式會同時列在單一頁面上。 但是，您可以使用集合將相關的應用程式群組在一起，並將它們顯示在不同的索引標籤上，使其更容易尋找。 例如，您可以使用集合，針對特定的作業角色、工作、專案等，建立應用程式的邏輯群組。 如需相關資訊，請參閱[如何使用我的應用程式集合自訂使用者存取面板](access-panel-collections.md)。 

存取面板與 Azure 入口網站不同，使用者不需要具備 Azure 訂用帳戶或 Office 365 訂用帳戶。

如需有關 Azure AD 存取面板的詳細資訊，請參閱 [存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="office-365-application-launcher"></a>Office 365 應用程式啟動程式

針對已部署 Office 365 的組織，透過 Azure AD 指派給使用者的應用程式也會出現在 Office 365 入口網站的[https://portal.office.com/myapps](https://portal.office.com/myapps)。 這對於組織中的使用者來說，能夠啟動應用程式又不需要使用第二個入口網站，非常簡單而且方便，建議使用 Office 365 的組織採取這個應用程式啟動解決方案。

![螢幕擷取畫面顯示 Office 365 入口網站](./media/end-user-experiences/microsoft-365-portal-office-com.png)

如需有關 Office 365 應用程式啟動程式的詳細資訊，請參閱 [讓您的應用程式出現在 Office 365 應用程式啟動程式中](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)。

## <a name="direct-sign-on-to-federated-apps"></a>直接登入同盟應用程式

大部分支援 SAML 2.0、WS-同盟或 OpenID Connect 的同盟應用程式也支援使用者在應用程式啟動，然後再透過 Azure AD 的自動重新導向或按一下連結登入。 這稱為服務提供者起始的登入，Azure AD 應用程式資源庫中大部分的同盟應用程式都支援這個方式 (請參閱 Azure 入口網站中應用程式的單一登入設定精靈連結的文件來了解詳細資訊)。

![行動應用程式登入頁面的範例](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>直接登入連結

Azure AD 也支援對支援密碼單一登入、已連結的單一登入，以及任何形式的同盟單一登入的個別應用程式使用直接單一登入連結。

這些連結是特別撰寫的 URL，會透過 Azure AD 登入程序針對特定應用程式傳送給使用者，使用者不需要從 Azure AD 存取面板或 Office 365 啟動。 您可以在 [可用的企業應用程式] 的屬性底下找到這些**使用者存取 url** 。 在 Azure 入口網站中，選取  **Azure Active Directory** > **企業應用程式**。 選取應用程式，然後選取 [**屬性**]。

![Twitter 屬性中的使用者存取 URL 範例](media/end-user-experiences/direct-sign-on-link.png)

您可以複製這些連結，然後貼到任何您想要提供選取應用程式登入連結的位置。 可以是在電子郵件中，或是任何您已設定使用者應用程式存取權的自訂網頁型入口網站中。 以下是 Azure AD 直接單一登入 Twitter 的 URL 範例：

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

類似於存取面板的組織特定 URL，您可以在 myapps.microsoft.com 網域之後加入其中一個作用中或經過驗證的網域做為您的目錄，進一步自訂這個 URL。 這樣可以確保使用者不需要先輸入其使用者識別碼，登入頁面就可以立即載入任何組織品牌：

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

當授權的使用者按一下其中一個應用程式特定連結時，他們會先看到其組織登入頁面 (假設他們尚未登入)，登入之後會重新導向至該應用程式而不會先停在存取面板。 如果使用者遺漏存取應用程式所需的先決條件，例如密碼單一登入瀏覽器延伸，則連結將會提示使用者安裝遺漏的延伸。 如果應用程式的單一登入組態有變更，連結 URL 也會維持不變。

這些連結使用與存取面板和 Office 365 相同的存取控制機制，只有在 Azure 入口網站中已指派給應用程式的使用者或群組能夠順利通過驗證。 不過，任何未經授權的使用者都會看到一個訊息，說明他們未獲得存取權，且會獲得一個載入存取面板的連結，以檢視可存取的應用程式。

## <a name="next-steps"></a>後續步驟

如需部署方案的相關資訊，請參閱 [Azure Active Directory 部署方案](../fundamentals/active-directory-deployment-plans.md)
