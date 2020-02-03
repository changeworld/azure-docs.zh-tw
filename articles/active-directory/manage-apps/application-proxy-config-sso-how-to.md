---
title: 如何設定應用程式 Proxy 應用程式的單一登入
description: 如何快速針對應用程式 Proxy 應用程式設定單一登入
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712027"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>如何針對應用程式 Proxy 應用程式設定單一登入

單一登入 (SSO) 可讓您的使用者在不需要多次驗證的情況下存取應用程式。 它允許單一驗證在雲端中針對 Azure Active Directory 發生，並允許服務或連接器模擬使用者，以完成來自應用程式的任何其他驗證挑戰。

## <a name="how-to-configure-single-sign-on"></a>如何設定單一登入
若要設定 SSO，請先確定您的應用程式已透過 Azure Active Directory 針對預先驗證進行設定。 若要進行此設定，請移至**Azure Active Directory** -&gt;**企業應用程式** -&gt; 應用程式 -&gt; 應用程式**Proxy**-**所有應用**程式。&gt; 在這個頁面上，您會看到 [預先驗證] 欄位，請確定它已設定為 [Azure Active Directory]。 

如需預先驗證方法的詳細資訊，請參閱[應用程式發佈文件](application-proxy-add-on-premises-application.md)的步驟 4。

   ![Azure 入口網站中的預先驗證方法](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>針對應用程式 Proxy 應用程式設定單一登入模式
我們會設定單一登入的特定類型。 登入方法是根據後端應用程式所使用的驗證類型來分類。 應用程式 Proxy 應用程式支援三種登入類型：

-   **密碼登入**：密碼登入可用於任何使用使用者名稱和密碼欄位進行登入的應用程式。 設定步驟位於[為 Azure AD 資源庫應用程式設定密碼單一登入](configure-password-single-sign-on-non-gallery-applications.md)。

-   **整合式 Windows 驗證**：對於使用整合式 Windows 驗證 (IWA) 的應用程式，單一登入是透過 Kerberos 限制委派 (KCD) 來啟用。 此方法會提供應用程式 Proxy 連接器在 Active Directory 中模擬使用者並代表他們傳送及接收權杖的權限。 您可以在[使用 KCD 進行單一登入文件](application-proxy-configure-single-sign-on-with-kcd.md)中找到設定 KCD 的詳細資料。

-   **標頭形式的登入**：標頭形式的登入是透過合作關係啟用，並且需要一些額外設定。 如需合作關係的詳細資料，以及針對使用標頭進行驗證之應用程式設定單一登入的逐步指示，請參閱 [Azure AD 的 PingAccess 文件](application-proxy-configure-single-sign-on-with-ping-access.md)。

-   **Saml 單一登入**：使用 saml 單一登入，Azure AD 使用使用者的 Azure AD 帳戶向應用程式進行驗證。 Azure AD 與應用程式透過連線通訊協定，進行登入資訊通訊。 使用 SAML 單一登入時，您可以根據您在 SAML 宣告中定義的規則，將使用者對應至特定的應用程式角色。 如需設定 SAML 單一登入的相關資訊，請參閱[使用應用程式 Proxy 進行單一登入的 SAML](application-proxy-configure-single-sign-on-on-premises-apps.md)。

每個選項都可透過在 [企業應用程式] 中移至您的應用程式，然後開啟左側功能表上的 [單一登入] 頁面來找到。 請注意，如果您的應用程式是在舊的入口網站中建立，您可能不會看到所有這些選項。

在此頁面上，您還會看到一個額外登入選項：連結的登入。 應用程式 Proxy 也支援此選項。 不過，此選項並不會將單一登入新增至應用程式。 不過，應用程式可能已經使用另一項服務 (例如 Active Directory 同盟服務) 來實作單一登入。 

這個選項可讓系統管理員建立應用程式連結，以供使用者首次存取應用程式時使用。 例如，如果有一個應用程式設定為使用 Active Directory 同盟服務 2.0 來驗證使用者，系統管理員可以使用 [連結的登入] 選項在存取面板上建立應用程式的連結。

## <a name="next-steps"></a>後續步驟
- [使用應用程式 Proxy 進行單一登入的密碼保存庫](application-proxy-configure-single-sign-on-password-vaulting.md)
- [可供使用應用程式 Proxy 單一登入的 Kerberos 限制委派](application-proxy-configure-single-sign-on-with-kcd.md)
- [使用應用程式 Proxy 單一登入的標頭型驗證](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [使用應用程式 Proxy 進行單一登入的 SAML](application-proxy-configure-single-sign-on-on-premises-apps.md)。
