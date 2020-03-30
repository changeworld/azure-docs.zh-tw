---
title: 應用程式的最終使用者體驗 - Azure 活動目錄
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266620"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory 中的應用程式使用者體驗

Azure Active Directory (Azure AD) 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式：

* Azure AD 存取面板
* Office 365 應用程式啟動程式
* 直接登入同盟應用程式
* 同盟、密碼或現有應用程式的深層連結

您選擇要在組織中部署哪一種方法由您自行決定。

## <a name="azure-ad-access-panel"></a>Azure AD 存取面板

accesshttps://myapps.microsoft.com面板是一個基於 Web 的門戶，允許 Azure 活動目錄中具有組織帳戶的最終使用者查看和啟動 Azure AD 管理員授予其存取權限的應用程式。 如果您是 [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/) 的使用者，也可以透過 [存取面板] 使用自助式群組管理功能。

![螢幕截圖顯示 Azure AD 訪問面板門戶](media/what-is-single-sign-on/azure-ad-access-panel.png)

預設情況下，所有應用程式都一起列在一頁上。 但是，您可以使用集合將相關應用程式組合在一起，並在單獨的選項卡上顯示它們，從而更易於查找它們。 例如，可以使用集合為特定作業角色、任務、專案等創建應用程式的邏輯分組。 有關詳細資訊，請參閱[如何使用"我的應用"集合自訂使用者訪問面板](access-panel-collections.md)。 

存取面板與 Azure 入口網站不同，使用者不需要具備 Azure 訂用帳戶或 Office 365 訂用帳戶。

如需有關 Azure AD 存取面板的詳細資訊，請參閱 [存取面板簡介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="office-365-application-launcher"></a>Office 365 應用程式啟動程式

對於已部署 Office 365 的組織，通過 Azure AD 分配給使用者的應用程式也會顯示在 中的 Office [https://portal.office.com/myapps](https://portal.office.com/myapps)365 門戶中。 這對於組織中的使用者來說，能夠啟動應用程式又不需要使用第二個入口網站，非常簡單而且方便，建議使用 Office 365 的組織採取這個應用程式啟動解決方案。

![螢幕截圖顯示 Office 365 門戶](./media/end-user-experiences/microsoft-365-portal-office-com.png)

如需有關 Office 365 應用程式啟動程式的詳細資訊，請參閱 [讓您的應用程式出現在 Office 365 應用程式啟動程式中](https://msdn.microsoft.com/office/office365/howto/connect-your-app-to-o365-app-launcher)。

## <a name="direct-sign-on-to-federated-apps"></a>直接登入同盟應用程式

大部分支援 SAML 2.0、WS-同盟或 OpenID Connect 的同盟應用程式也支援使用者在應用程式啟動，然後再透過 Azure AD 的自動重新導向或按一下連結登入。 這稱為服務提供者起始的登入，Azure AD 應用程式資源庫中大部分的同盟應用程式都支援這個方式 (請參閱 Azure 入口網站中應用程式的單一登入設定精靈連結的文件來了解詳細資訊)。

![移動應用登錄頁示例](./media/end-user-experiences/workdaymobile.png)

## <a name="direct-sign-on-links"></a>直接登入連結

Azure AD 也支援對支援密碼單一登入、已連結的單一登入，以及任何形式的同盟單一登入的個別應用程式使用直接單一登入連結。

這些連結是特別撰寫的 URL，會透過 Azure AD 登入程序針對特定應用程式傳送給使用者，使用者不需要從 Azure AD 存取面板或 Office 365 啟動。 這些**使用者訪問 URL**可以在可用企業應用程式的屬性下找到。 在 Azure 門戶中，選擇**Azure 活動目錄** > **企業應用程式**。 選擇應用程式，然後選擇**屬性**。

![Twitter 屬性中的使用者訪問 URL 示例](media/end-user-experiences/direct-sign-on-link.png)

您可以複製這些連結，然後貼到任何您想要提供選取應用程式登入連結的位置。 可以是在電子郵件中，或是任何您已設定使用者應用程式存取權的自訂網頁型入口網站中。 以下是 Azure AD 直接單一登入 Twitter 的 URL 範例：

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

類似於存取面板的組織特定 URL，您可以在 myapps.microsoft.com 網域之後加入其中一個作用中或經過驗證的網域做為您的目錄，進一步自訂這個 URL。 這樣可以確保使用者不需要先輸入其使用者識別碼，登入頁面就可以立即載入任何組織品牌：

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

當授權的使用者按一下其中一個應用程式特定連結時，他們會先看到其組織登入頁面 (假設他們尚未登入)，登入之後會重新導向至該應用程式而不會先停在存取面板。 如果使用者遺漏存取應用程式所需的先決條件，例如密碼單一登入瀏覽器延伸，則連結將會提示使用者安裝遺漏的延伸。 如果應用程式的單一登入組態有變更，連結 URL 也會維持不變。

這些連結使用與存取面板和 Office 365 相同的存取控制機制，只有在 Azure 入口網站中已指派給應用程式的使用者或群組能夠順利通過驗證。 不過，任何未經授權的使用者都會看到一個訊息，說明他們未獲得存取權，且會獲得一個載入存取面板的連結，以檢視可存取的應用程式。

## <a name="next-steps"></a>後續步驟

如需部署方案的相關資訊，請參閱 [Azure Active Directory 部署方案](../fundamentals/active-directory-deployment-plans.md)
