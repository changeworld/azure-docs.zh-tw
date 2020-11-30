---
title: Azure AD 安全混合式存取 | Microsoft Docs
description: 本文說明可將舊版的內部部署、公用雲端或私人雲端應用程式與 Azure AD 整合的合作夥伴解決方案。 藉由將應用程式傳遞控制器或網路連線至 Azure AD，以保護您的舊版應用程式。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 521439da044fb4fc3c2f578f28658215266138d9
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317090"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>安全的混合式存取：使用 Azure Active Directory 保護繼承應用程式

您現在可以藉由將內部部署和雲端舊版驗證應用程式連接至 Azure Active Directory (AD) 來保護這些應用程式：

- [Azure AD 應用程式 Proxy](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [您現有的應用程式傳遞控制器和網路](#sha-through-networking-and-delivery-controllers)

- [虛擬私人網路 (VPN) 和 Software-Defined 周邊 (SDP) 應用程式](#sha-through-vpn-and-sdp-applications)

您可以使用 Azure AD [條件式存取](../conditional-access/overview.md) 和 Azure AD [Identity Protection](../identity-protection/overview-identity-protection.md)等 Azure AD 功能，在所有應用程式中橋接間距並加強安全性狀態。

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>透過 Azure AD 應用程式 Proxy (SHA) 的安全混合式存取
  
您可以使用 [應用程式 Proxy](./what-is-application-proxy.md) ，為您的內部部署 web 應用程式提供 [安全的遠端存取](./application-proxy.md) 。 您的使用者不需要使用 VPN。 使用者可以在 [單一登入](./add-application-portal-setup-sso.md)之後，從任何裝置輕鬆連接至其應用程式，以獲得好處。 應用程式 Proxy 可提供遠端存取作為服務，並可讓您 [輕鬆地將內部部署應用程式發佈](./application-proxy-add-on-premises-application.md) 給公司網路以外的使用者。 它可協助您調整雲端存取管理，而不需要修改您的內部部署應用程式。 [規劃 Azure AD 的應用程式 Proxy 部署](./application-proxy-deployment-plan.md) ，做為下一個步驟。

## <a name="azure-ad-partner-integrations"></a>Azure AD 合作夥伴整合

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA 到網路和傳遞控制器

除了 Azure AD 的 [應用程式 Proxy](./what-is-application-proxy.md)，讓您能夠使用 [零信任 framework](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)，Microsoft 與協力廠商提供者合作。 您可以使用現有的網路和傳遞控制器，並輕鬆地保護對您商務程式很重要的繼承應用程式，但在 Azure AD 之前無法保護。 您可能已經擁有開始保護這些應用程式所需的一切。

![影像顯示網路夥伴和應用程式 proxy 的安全混合式存取](./media/secure-hybrid-access/secure-hybrid-access.png)

下列網路廠商提供預先建立的解決方案，以及與 Azure AD 整合的詳細指導方針。

- [Akamai 企業應用程式存取 (EAA)](../saas-apps/akamai-tutorial.md)

- [Citrix 應用程式傳遞控制器 (ADC)](../saas-apps/citrix-netscaler-tutorial.md)

- [F5 Big-IP APM](f5-aad-integration.md)

- [坎普](../saas-apps/kemp-tutorial.md)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA 透過 VPN 和 SDP 應用程式

您可以使用 VPN 和 SDP 解決方案，在任何位置都能安全地從任何裝置存取您的商業網路，同時保護組織的資料。 藉由將 Azure AD 作為身分識別提供者 (IDP) ，您可以使用新式驗證和授權方法（例如 Azure AD [單一登入](./what-is-single-sign-on.md) 和 [多重要素驗證](../authentication/concept-mfa-howitworks.md) ）來保護您的內部部署繼承應用程式。  

![影像顯示與 VPN 夥伴和應用程式 proxy 的安全混合式存取 ](./media/secure-hybrid-access/app-proxy-vpn.png)

下列 VPN 和 SDP 廠商提供預先建立的解決方案，以及與 Azure AD 整合的詳細指導方針。

• [Cisco AnyConnect](../saas-apps/cisco-anyconnect.md)

• [Fortinet](../saas-apps/fortigate-ssl-vpn-tutorial.md)

• [F5 大型 IP APM](f5-aad-password-less-vpn.md)

• [Palo Alto Networks 全域保護](../saas-apps/paloaltoadmin-tutorial.md)

• [Zscaler 私用存取 (ZPA) ](../saas-apps/zscalerprivateaccess-tutorial.md)
