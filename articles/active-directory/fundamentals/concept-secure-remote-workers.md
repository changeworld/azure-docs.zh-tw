---
title: 使用 Azure Active Directory 快速回應安全的身分識別
description: 使用 Azure AD 雲端式身分識別快速回應威脅
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e0e0ad9086a7945201b1752126253f12eb751bf
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320030"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>使用 Azure AD 快速回應安全身分識別

在現今的世界中，您可能會想要保護您的背景工作，特別是當您需要快速回應並快速提供許多服務的存取權時。 本文的目的是要提供要採取的所有動作的簡短清單，協助您根據所擁有的授權類型，找出部署 Azure AD 功能的順序，並設定其優先順序。 Azure AD 提供許多功能，並為您的身分識別提供許多層級的安全性，而流覽哪些功能是相關的，有時可能會很困難。 許多組織已在雲端中，或快速移至雲端，這份檔旨在讓您快速部署服務，並將您的身分識別作為主要考慮來保護。 

每個資料表都會提供一致的安全性建議，同時保護系統管理員和使用者身分識別免于受到主要安全性攻擊 (缺口重新執行、網路釣魚和密碼噴灑) ，同時將使用者的影響降到最低，並改善使用者體驗。 

本指南也可讓系統管理員以安全且受保護的方式來設定 SaaS 和內部部署應用程式的存取權，而且適用于雲端或混合式 (同步) 身分識別，並適用于在遠端或辦公室工作的使用者。

這份檢查清單會藉由說明如何執行下列作業，協助您快速部署重要的建議動作來立即保護組織：

- 強化認證。
- 減少受攻擊面。
- 將威脅回應自動化。
- 利用雲端智慧。
- 啟用終端使用者自助服務。

## <a name="prerequisites"></a>必要條件

本指南假設您已在 Azure AD 中建立您的僅限雲端或混合式身分識別。 如需選擇身分識別類型的說明，請參閱文章， [為您的 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>摘要

安全的身分識別基礎結構有許多層面，但這份檢查清單著重于安全且安全的身分識別基礎結構，讓使用者可以從遠端工作。 保護您的身分識別只是安全性案例的一部分，因此也應該考慮保護資料、應用程式和裝置。

### <a name="guidance-for-azure-ad-free-or-office-365-customers"></a>Azure AD Free 或 Office 365 客戶的指引。

為了保護使用者身分識別，Azure AD Free 或 Office 365 應用程式應該採取的建議有很多，下表旨在強調下列授權訂閱的主要動作：

- Office 365 (O365 E1、E3、E5、F1、A1、A3、A5) 
- Office 365 Business (Essentials、Business、Business Premium) 
- Microsoft 365 (M365 Business，A1) 
- Azure AD Free (隨附于 Azure、Dynamics 365、Intune 和 Power Platform) 

| 建議的動作 | 詳細資料 |
| --- | --- |
| [啟用安全性預設值](concept-fundamentals-security-defaults.md) | 藉由啟用 MFA 和封鎖舊版驗證，來保護所有使用者身分識別和應用程式 |
| 如果使用混合式身分識別[，請啟用密碼雜湊同步](../hybrid/how-to-connect-password-hash-synchronization.md)處理 ()  | 提供驗證的冗余和改進安全性 (包括智慧型鎖定、IP 鎖定，以及探索洩漏認證的能力。 )  |
| [啟用 ADFS 智慧型鎖定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (（如果適用）)  | 防止您的使用者遇到來自惡意活動的外部網路帳戶鎖定。 |
| 如果使用受控識別[，請啟用 Azure Active Directory 智慧鎖定](../authentication/howto-password-smart-lockout.md) ()  | 智慧型鎖定可協助鎖定不良的動作專案，而這些動作專案試圖猜測使用者的密碼或使用暴力密碼破解方法來進入。 |
| [停用使用者對應用程式的同意](../manage-apps/configure-user-consent.md) | 系統管理員同意工作流程可讓系統管理員以安全的方式將存取權授與需要系統管理員核准的應用程式，讓使用者不會公開公司資料。 Microsoft 建議停用未來的使用者同意作業以協助減少介面區並降低此風險。 |
| [整合資源庫中支援的 SaaS 應用程式，以 Azure AD 及啟用單一登入](../manage-apps/add-application-portal.md) | Azure AD 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的某些應用程式可能就在可從 Azure 入口網站直接存取的資源庫中。 利用改良的使用者體驗 (SSO) ，以遠端安全地存取公司 SaaS 應用程式 |
| [自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md) 布建和解除布建 (如果適用)  | 在雲端 (SaaS) 使用者需要存取的應用程式中，自動建立使用者身分識別和角色。 除了建立使用者身分識別之外，自動布建還包括維護和移除使用者身分識別的狀態或角色變更，從而提高組織的安全性。 |
| [啟用安全的混合式存取：使用現有的應用程式傳遞控制器和網路保護繼承應用程式](../manage-apps/secure-hybrid-access.md) (如果適用)  | 使用您現有的應用程式傳遞控制器或網路，將您的內部部署和雲端舊版驗證應用程式連接到 Azure AD，以發佈並保護這些應用程式。 |
| [啟用自助式密碼重設](../authentication/tutorial-enable-sspr.md) (適用于僅限雲端的帳戶)  | 當使用者無法登入其裝置或應用程式時，這項功能可減少技術支援中心的來電和喪失生產力。 |
| [可能的話，請使用非全域系統管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 僅賦予您的系統管理員存取其所需存取區域的權限。 並非所有系統管理員都必須是全域管理員。 |
| [啟用 Microsoft 的密碼指引](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求使用者依照設定的排程變更其密碼，停用複雜性需求，而您的使用者會更容易記住其密碼並維護其安全性。 |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Azure AD Premium 方案1客戶的指引。

下表旨在強調下列授權訂閱的主要動作：

- Azure Active Directory Premium P1 (Azure AD P1) 
- Enterprise Mobility + Security (EMS E3) 
- Microsoft 365 (M365 E3、A3、F1、F3) 

| 建議的動作 | 詳細資料 |
| --- | --- |
| [啟用 Azure MFA 和 SSPR 的綜合註冊體驗，以簡化使用者註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md) | 可讓使用者在單一常見體驗中註冊 Azure Multi-Factor Authentication 和自助密碼重設。 |
| [為您的組織設定 MFA 設定](../authentication/howto-mfa-getstarted.md) | 確保帳戶受到保護，不會受到多重要素驗證的危害 |
| [啟用自助式密碼重設](../authentication/tutorial-enable-sspr.md) | 當使用者無法登入其裝置或應用程式時，這項功能可減少技術支援中心的來電和喪失生產力 |
| 如果使用混合式身分識別，請[執行密碼回寫](../authentication/tutorial-enable-sspr-writeback.md) ()  | 可讓雲端中的密碼變更回寫至內部部署 Windows Server Active Directory 環境。 |
| 建立和啟用條件式存取原則 | [MFA 可讓系統管理員保護已獲指派系統管理許可權的帳戶。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [封鎖舊版驗證通訊協定，因為與舊版驗證通訊協定相關聯的風險增加。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [適用于所有使用者和應用程式的 MFA，可為您的環境建立平衡的 MFA 原則，以保護您的使用者和應用程式。](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [需要 Azure 管理的 MFA，藉由對任何存取 Azure 資源的使用者要求多重要素驗證，來保護您的特殊許可權資源。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| 如果使用混合式身分識別[，請啟用密碼雜湊同步](../hybrid/how-to-connect-password-hash-synchronization.md)處理 ()  | 提供驗證的冗余和改進安全性 (包括智慧型鎖定、IP 鎖定，以及探索洩漏認證的能力。 )  |
| [啟用 ADFS 智慧型鎖定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (（如果適用）)  | 防止您的使用者遇到來自惡意活動的外部網路帳戶鎖定。 |
| 如果使用受控識別[，請啟用 Azure Active Directory 智慧鎖定](../authentication/howto-password-smart-lockout.md) ()  | 智慧型鎖定可協助鎖定不良的動作專案，而這些動作專案試圖猜測使用者的密碼或使用暴力密碼破解方法來進入。 |
| [停用使用者對應用程式的同意](../manage-apps/configure-user-consent.md) | 系統管理員同意工作流程可讓系統管理員以安全的方式將存取權授與需要系統管理員核准的應用程式，讓使用者不會公開公司資料。 Microsoft 建議停用未來的使用者同意作業以協助減少介面區並降低此風險。 |
| [使用應用程式 Proxy 啟用對內部部署繼承應用程式的遠端存取](../manage-apps/application-proxy-add-on-premises-application.md) | 啟用 Azure AD 的應用程式 Proxy，並與繼承應用程式整合，讓使用者能夠使用其 Azure AD 帳戶登入，以安全地存取內部部署應用程式。 |
| [啟用安全的混合式存取：使用現有的應用程式傳遞控制器和網路保護繼承應用程式](../manage-apps/secure-hybrid-access.md) (如適用) 。 | 使用您現有的應用程式傳遞控制器或網路，將您的內部部署和雲端舊版驗證應用程式連接到 Azure AD，以發佈並保護這些應用程式。 |
| [整合資源庫中支援的 SaaS 應用程式，以 Azure AD 及啟用單一登入](../manage-apps/add-application-portal.md) | Azure AD 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的某些應用程式可能就在可從 Azure 入口網站直接存取的資源庫中。 利用改良的使用者經驗 (SSO) ，以遠端安全的方式提供公司 SaaS 應用程式的存取權。 |
| [自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md) 布建和解除布建 (如果適用)  | 在雲端 (SaaS) 使用者需要存取的應用程式中，自動建立使用者身分識別和角色。 除了建立使用者身分識別之外，自動布建還包括維護和移除使用者身分識別的狀態或角色變更，從而提高組織的安全性。 |
| [啟用條件式存取–以裝置為基礎](../conditional-access/require-managed-devices.md) | 使用以裝置為基礎的條件式存取來改善安全性和使用者體驗。 此步驟可確保使用者只能從符合安全性和合規性標準的裝置進行存取。 這些裝置也稱為受控裝置。 受管理的裝置可以是與 Intune 相容或混合式 Azure AD 加入的裝置。 |
| [啟用密碼保護](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 防止使用者使用弱且容易猜測的密碼。 |
| [指定超過一個全域管理員](../users-groups-roles/directory-emergency-access.md) | 在發生緊急狀況時，請指定至少兩個僅限雲端的永久全域管理員帳戶。 這些帳戶並非每天使用，而且應該有複雜的長密碼。 中斷玻璃帳戶可確保您可以在緊急情況下存取服務。 |
| [可能的話，請使用非全域系統管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 僅賦予您的系統管理員存取其所需存取區域的權限。 並非所有系統管理員都必須是全域管理員。 |
| [啟用 Microsoft 的密碼指引](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求使用者依照設定的排程變更其密碼，停用複雜性需求，而您的使用者會更容易記住其密碼並維護其安全性。 |
| [建立來賓使用者存取權的計劃](../external-identities/what-is-b2b.md) | 讓使用者使用自己的公司、學校或社交身分識別登入您的應用程式和服務，以與來賓使用者共同作業。 |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Azure AD Premium 方案2客戶的指引。

下表旨在強調下列授權訂閱的主要動作：

- Azure Active Directory Premium P2 (Azure AD P2) 
- Enterprise Mobility + Security (EMS E5) 
- Microsoft 365 (M365 E5、A5) 

| 建議的動作 | 詳細資料 |
| --- | --- |
| [啟用 Azure MFA 和 SSPR 的綜合註冊體驗，以簡化使用者註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md) | 可讓使用者在單一常見體驗中註冊 Azure Multi-Factor Authentication 和自助密碼重設。 |
| [為您的組織設定 MFA 設定](../authentication/howto-mfa-getstarted.md) | 確保帳戶受到保護，不會受到多重要素驗證的危害 |
| [啟用自助式密碼重設](../authentication/tutorial-enable-sspr.md) | 當使用者無法登入其裝置或應用程式時，這項功能可減少技術支援中心的來電和喪失生產力 |
| 如果使用混合式身分識別，請[執行密碼回寫](../authentication/tutorial-enable-sspr-writeback.md) ()  | 可讓雲端中的密碼變更回寫至內部部署 Windows Server Active Directory 環境。 |
| [啟用 Identity Protection 原則以強制執行 MFA 註冊](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | 管理 Azure Multi-Factor Authentication (MFA) 的推出。 |
| [啟用 Identity Protection 使用者和登入風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md) | 啟用 Identity Protection 使用者和登入原則。 建議的登入原則是以中度風險登入為目標，且需要 MFA。 針對使用者原則，其應鎖定需要密碼變更動作的高風險使用者。 |
| 建立和啟用條件式存取原則 | [MFA 可讓系統管理員保護已獲指派系統管理許可權的帳戶。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [封鎖舊版驗證通訊協定，因為與舊版驗證通訊協定相關聯的風險增加。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [需要 Azure 管理的 MFA，藉由對任何存取 Azure 資源的使用者要求多重要素驗證，來保護您的特殊許可權資源。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| 如果使用混合式身分識別[，請啟用密碼雜湊同步](../hybrid/how-to-connect-password-hash-synchronization.md)處理 ()  | 提供驗證的冗余和改進安全性 (包括智慧型鎖定、IP 鎖定，以及探索洩漏認證的能力。 )  |
| [啟用 ADFS 智慧型鎖定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (（如果適用）)  | 防止您的使用者遇到來自惡意活動的外部網路帳戶鎖定。 |
| 如果使用受控識別[，請啟用 Azure Active Directory 智慧鎖定](../authentication/howto-password-smart-lockout.md) ()  | 智慧型鎖定可協助鎖定不良的動作專案，而這些動作專案試圖猜測使用者的密碼或使用暴力密碼破解方法來進入。 |
| [停用使用者對應用程式的同意](../manage-apps/configure-user-consent.md) | 系統管理員同意工作流程可讓系統管理員以安全的方式將存取權授與需要系統管理員核准的應用程式，讓使用者不會公開公司資料。 Microsoft 建議停用未來的使用者同意作業以協助減少介面區並降低此風險。 |
| [使用應用程式 Proxy 啟用對內部部署繼承應用程式的遠端存取](../manage-apps/application-proxy-add-on-premises-application.md) | 啟用 Azure AD 的應用程式 Proxy，並與繼承應用程式整合，讓使用者能夠使用其 Azure AD 帳戶登入，以安全地存取內部部署應用程式。 |
| [啟用安全的混合式存取：使用現有的應用程式傳遞控制器和網路保護繼承應用程式](../manage-apps/secure-hybrid-access.md) (如適用) 。 | 使用您現有的應用程式傳遞控制器或網路，將您的內部部署和雲端舊版驗證應用程式連接到 Azure AD，以發佈並保護這些應用程式。 |
| [整合資源庫中支援的 SaaS 應用程式，以 Azure AD 及啟用單一登入](../manage-apps/add-application-portal.md) | Azure AD 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的某些應用程式可能就在可從 Azure 入口網站直接存取的資源庫中。 利用改良的使用者經驗 (SSO) ，以遠端安全的方式提供公司 SaaS 應用程式的存取權。 |
| [自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md) 布建和解除布建 (如果適用)  | 在雲端 (SaaS) 使用者需要存取的應用程式中，自動建立使用者身分識別和角色。 除了建立使用者身分識別之外，自動布建還包括維護和移除使用者身分識別的狀態或角色變更，從而提高組織的安全性。 |
| [啟用條件式存取–以裝置為基礎](../conditional-access/require-managed-devices.md) | 使用以裝置為基礎的條件式存取來改善安全性和使用者體驗。 此步驟可確保使用者只能從符合安全性和合規性標準的裝置進行存取。 這些裝置也稱為受控裝置。 受管理的裝置可以是與 Intune 相容或混合式 Azure AD 加入的裝置。 |
| [啟用密碼保護](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 防止使用者使用弱且容易猜測的密碼。 |
| [指定超過一個全域管理員](../users-groups-roles/directory-emergency-access.md) | 在發生緊急狀況時，請指定至少兩個僅限雲端的永久全域管理員帳戶。 這些帳戶並非每天使用，而且應該有複雜的長密碼。 中斷玻璃帳戶可確保您可以在緊急情況下存取服務。 |
| [可能的話，請使用非全域系統管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 僅賦予您的系統管理員存取其所需存取區域的權限。 並非所有系統管理員都必須是全域管理員。 |
| [啟用 Microsoft 的密碼指引](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求使用者依照設定的排程變更其密碼，停用複雜性需求，而您的使用者會更容易記住其密碼並維護其安全性。 |
| [建立來賓使用者存取權的計劃](../external-identities/what-is-b2b.md) | 讓使用者使用自己的公司、學校或社交身分識別登入您的應用程式和服務，以與來賓使用者共同作業。 |
| [啟用 Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 可讓您管理、控制及監視您組織中重要資源的存取權，以確保系統管理員只有在需要時才可存取，並具有核准 |

## <a name="next-steps"></a>接下來的步驟

- 如需 Azure AD 個別功能的詳細部署指引，請參閱 [Azure AD 專案部署計畫](active-directory-deployment-plans.md)。

- 如需端對端 Azure AD 部署檢查清單，請參閱[Azure Active Directory 功能部署指南》](active-directory-deployment-checklist-p2.md)一文。