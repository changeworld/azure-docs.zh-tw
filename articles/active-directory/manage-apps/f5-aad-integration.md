---
title: 使用 F5 Azure AD 安全的混合式存取 |Microsoft Docs
description: 適用于安全混合式存取的 F5 大型 IP 存取原則管理員和 Azure Active Directory 整合
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e011417b936ed83b4658e6dad25bf8e8ee88aed
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317831"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>適用于安全混合式存取的 F5 大型 IP 存取原則管理員和 Azure Active Directory 整合

行動和不斷演進的威脅環境激增，將對資源存取和治理進行額外的審查，將 [零信任](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) 所有現代化計畫的前端和中心。
在 Microsoft 和 F5 中，我們瞭解到，這項數位轉型通常是適用于任何企業的多年旅程，可能會讓重要的資源在現代化之前公開。 創世背後的 F5 大型 IP 和 Azure Active Directory 安全的混合式存取 (SHA) 的目標不僅是改善內部部署應用程式的遠端存取，也是強化這些易受攻擊服務的整體安全性狀態。

針對內容，研究會預估60-80% 的內部部署應用程式本質上是舊版，或無法直接整合 Azure Active Directory (AD) 。 同樣的研究也指出，這些系統在舊版的 SAP、Oracle、SAGE 及其他提供重要服務的已知工作負載上執行。

SHA 藉由讓組織繼續使用其 F5 投資來滿足更優質的網路和應用程式的投資，來解決這個失明。 結合 Azure AD 它會以新式身分識別控制平面來橋接異類應用程式環境。

擁有大量 IP 已發佈服務的 Azure AD 預先驗證存取權可提供許多優點：

- 透過[Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-overview)、 [MS 驗證](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)器、[線上身分識別線上 (FIDO) 金鑰](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)和[憑證型驗證](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)的無密碼驗證

-  (MFA) 的先占式 [條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) 和 [多重要素驗證 ](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

- 身分[識別保護](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#:~:text=Identity%20Protection%20is%20a%20tool%20that%20allows%20organizations,detection%20data%20to%20third-party%20utilities%20for%20further%20analysis)-透過使用者和會話風險分析進行適應性控制

- [洩漏的認證偵測](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)

- [自助式密碼重設 (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-sspr)

- [合作夥伴](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users) 共同作業-控管來賓存取權的權利管理

- [Cloud App Security (CASB) ](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) -適用于完整的應用程式探索和控制

- 威脅監視-適用于 advanced 威脅分析的[Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)

- [Azure AD 入口網站](https://azure.microsoft.com/features/azure-portal/)-用於管理身分識別和存取的單一控制平面

## <a name="scenario-description"></a>案例描述

作為應用程式傳遞控制器 (ADC) 和 SSL VPN，大型 IP 系統可提供本機和遠端存取所有類型的服務，包括：

- 新式和舊版 web 應用程式

- 非 web 應用程式

- REST 和 SOAP Web API 服務

其本機流量管理員 (LTM) 可透過反向 proxy 功能，安全發佈服務。 同時，精密的存取原則管理員 (APM) 以一組更豐富的功能來擴充大型 IP， (SSO) 啟用同盟和單一登入。

整合是以 APM 和 Azure AD 之間的標準同盟信任為基礎，這是大多數 SHA 使用案例的通用，包括 [SSL VPN 案例](f5-aad-password-less-vpn.md)。 安全性聲明標記語言 (SAML) 、OAuth 和 Open ID Connect (OIDC) 資源也不會有任何例外狀況，因為它們也可以安全地進行遠端存取。 在某些情況下，大型 IP 會成為零信任存取所有服務（包括 SaaS 應用程式）的扼點。

與 Azure AD 整合的大型 IP 功能，可讓您透過新式的控制項（例如無 [密碼驗證](https://www.microsoft.com/security/business/identity/passwordless) 和 [條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)）來保護舊版或非 Azure AD 整合服務所需的通訊協定轉換。 在此案例中，大型 IP 會繼續將其角色作為反向 proxy 來達成，同時以每個服務為基礎來處理 Azure AD 的預先驗證和授權。

下圖中的步驟1-4 說明在服務提供者起始的流程中，使用者、大型 IP 和 Azure AD 之間的前端預先驗證交換。 步驟5-6 顯示後續的 APM 會話擴充和 SSO 對個別的後端服務。

![影像顯示高層級架構](./media/f5-aad-integration/integration-flow-diagram.png)

| 步驟 | 說明 |
|:------|:-----------|
| 1. | 使用者會在入口網站中選取應用程式圖示，解析 SAML SP (大 IP) 的 URL |
| 2. | 大型 IP 會將使用者重新導向至 SAML IDP (Azure AD) 進行預先驗證|
| 3. | Azure AD 處理 CA 原則和 [會話控制項](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) 以進行授權|
| 4. | 使用者重新導向回到大 IP，以呈現 Azure AD 所簽發的 SAML 宣告 |
| 5. | 大型 IP 要求任何額外的會話資訊，以包含在 [SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) 和 [角色型存取控制中， (RBAC) ](https://docs.microsoft.com/azure/role-based-access-control/overview) 發佈的服務 |
| 6. | 大型 IP 會將用戶端要求轉送至後端服務

## <a name="user-experience"></a>使用者體驗

無論是直接員工、分支機搆或取用者，大部分的使用者都已經熟悉 Office 365 登入體驗，所以透過 SHA 存取大型 IP 服務的方式也很陌生。

使用者現在可以在  [MyApps](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) 或 [O365 launchpads](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) 中找到其大量 IP 發佈的服務，並將自助功能提供給一組更廣泛的服務，無論裝置或位置類型為何。 如果偏好，使用者甚至可以直接透過 BIG-IPs 專屬的 Webtop 入口網站來繼續存取已發佈的服務。 登出時，SHA 可確保使用者的會話會在兩端終止，也就是大型 IP 和 Azure AD，以確保服務能完全受到保護以防止未經授權的存取。  

提供的螢幕擷取畫面來自 Azure AD 應用程式入口網站，使用者可以安全地存取該入口網站，以找出他們的大型 IP 已發佈服務，以及管理其帳戶屬性。  

![螢幕擷取畫面顯示 woodgrove myapps 資源庫](media/f5-aad-integration/woodgrove-app-gallery.png)

![螢幕擷取畫面顯示 [woodgrove myaccounts 自助服務] 頁面](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>深入解析與分析

大型 IP 的角色對於任何企業而言都是不可或缺的，因此應該監視已部署的大型 IP 實例，以確保已發佈的服務在 SHA 層級和操作方面都是高可用性。

有幾個選項可用來在本機或透過安全性資訊和事件管理來遠端記錄事件 (SIEM) 解決方案，讓您可以關閉儲存和處理遙測資料。 用於監視 Azure AD 和 SHA 特定活動的高度有效解決方案，是使用 [Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview) 和 [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/overview)，同時提供：

- 深入瞭解您的組織，可能跨多個雲端和內部部署位置，包括大型 IP 基礎結構

- 單一控制平面提供所有信號的組合視圖，避免依賴複雜且不同的工具

![此影像顯示監視流程](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>必要條件

將 F5 BIG IP 與適用于 SHA 的 Azure AD 整合有下列必要條件：

- 在下列任一平臺上執行的 F5 大型 IP 實例：

  - 實體設備

  - 虛擬機器虛擬版，例如 Microsoft Hyper-V、VMware ESXi、Linux KVM 和 Citrix 管理程式

  - 雲端虛擬版本，例如 Azure、VMware、KVM、社區 Xen、MS Hyper-v、AWS、OpenStack 和 Google Cloud

    大型 IP 實例的實際位置可以是內部部署或任何支援的雲端平臺（包括 Azure），但前提是它有網際網路連線、發佈的資源，以及任何其他必要的服務（例如 Active Directory）。  

- 使用中的 F5 BIG IP APM 授權，透過下列其中一個選項：

   - F5 BIG-IP® Best 套件組合 (或)

   - F5 BIG-IP 存取原則管理員™獨立授權

   - F5 大型 IP 存取原則管理員™ (APM) 現有的大型 IP F5 大型 IP®本機流量管理員™ (LTM) 的附加元件授權

   - 90天的大型 IP 存取原則管理員™ (APM) [試用版授權](https://www.f5.com/trial/big-ip-trial.php)

- 透過下列其中一種選項 Azure AD 授權：

   - Azure AD [免費訂](https://docs.microsoft.com/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) 用帳戶提供以無密碼驗證執行 SHA 的最低核心需求

   - [Premium 訂](https://azure.microsoft.com/pricing/details/active-directory/)用帳戶提供在前言中概述的所有額外價值，[包括條件式存取](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)、 [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)和身分[識別保護](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

不需要先前的經驗或 F5 大型 IP 知識就能實行 SHA，但我們建議您熟悉自己的 F5 大型 IP 術語。 F5 豐富的 [知識庫](https://www.f5.com/services/resources/glossary) 也是開始建立大型 IP 知識的絕佳位置。

## <a name="deployment-scenarios"></a>部署案例

下列教學課程提供有關如何針對大型 IP 和 Azure AD SHA 執行一些較常見模式的詳細指引：

- [F5 BIG-Azure 部署中的 IP 逐步解說](f5-bigip-deployment-guide.md)

- [F5 BIG IP APM 和 Azure AD SSO 至 Kerberos 應用程式](https://docs.microsoft.com/azure/active-directory/saas-apps/kerbf5-tutorial#configure-f5-single-sign-on-for-kerberos-application)

- [F5 大型 IP APM 和 Azure AD SSO 到標頭型應用程式](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial#configure-f5-single-sign-on-for-header-based-application)

- [使用 Azure AD SHA 保護 F5 BIG IP SSL-VPN](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>其他資源

- [密碼結束時，請前往無密碼](https://www.microsoft.com/security/business/identity/passwordless)

- [Azure Active Directory 安全的混合式存取](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Microsoft 零信任 framework 來啟用遠端工作](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [開始使用 Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>後續步驟

請考慮使用您現有的大型 IP 基礎結構或部署試用實例， (POC) 來執行 SHA 概念證明。 將[大型 IP 虛擬版 (前端) VM 部署到 Azure](f5-bigip-deployment-guide.md)需要大約30分鐘的時間，此時您將會有：

- 完全安全的平臺，以建立 SHA 概念證明的模型

- 進入生產階段前實例、完全安全的平臺，可用來測試新的大型 IP 系統更新和修補程式

同時，您應該找出一個或兩個應用程式，其目標是透過大型 IP 進行發佈，並使用 SHA 來保護。  

我們的建議是從尚未透過大型 IP 發佈的應用程式開始，以避免潛在的生產服務中斷。 本文所述的指導方針將協助您熟悉建立各種大型 IP 設定物件和設定 SHA 的一般程式。 完成之後，您應該可以使用任何其他新的服務來執行相同的作業，此外也有足夠的知識，可將現有的大型 IP 已發佈服務轉換成 SHA （最簡單的工作）。

下列互動式指南將逐步解說如何實行 SHA 的高階程式，並查看終端使用者體驗。

[![此影像顯示互動式指南](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
