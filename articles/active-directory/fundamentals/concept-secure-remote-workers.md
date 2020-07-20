---
title: 使用 Azure Active Directory 快速回應安全身分識別
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
ms.openlocfilehash: 713afb7b277fba65dc4c860e8bdd6b62b4e0147d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204945"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>使用 Azure AD 快速回應安全身分識別

在現今的世界中，很難嘗試保護您的工作者，特別是當您需要快速回應並快速提供許多服務的存取權時。 本文旨在提供要採取的所有動作的簡短清單，協助您根據所擁有的授權類型，識別和排定部署 Azure AD 功能的順序。 Azure AD 提供許多功能，並為您的身分識別提供許多安全性層級，因此，流覽哪一項功能可能會很困難。 許多組織已在雲端中，或快速地移至雲端，這份檔的目的是讓您快速部署服務，並將您的身分識別作為主要考慮。 

每份資料表都提供一致的安全性建議，同時保護系統管理員和使用者身分識別免于主要的安全性攻擊（缺口、網路釣魚和密碼噴灑），同時將使用者的影響降至最低，並改善使用者體驗。 

本指南也可讓系統管理員以安全且受保護的方式，設定對 SaaS 和內部部署應用程式的存取權，並適用于雲端或混合式（已同步處理）身分識別，並適用于從遠端或在辦公室工作的使用者。

這份檢查清單會藉由說明如何執行下列作業，協助您快速部署重要的建議動作來立即保護組織：

- 強化認證。
- 減少受攻擊面。
- 將威脅回應自動化。
- 利用雲端智慧。
- 啟用終端使用者自助服務。

## <a name="prerequisites"></a>必要條件

本指南假設您已在 Azure AD 中建立了您的雲端或混合式身分識別。 如需選擇身分識別類型的說明，請參閱文章：為[您的 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>摘要

安全身分識別基礎結構有許多層面，但此檢查清單著重于安全且安全的身分識別基礎結構，讓使用者可以從遠端工作。 保護您的身分識別只是安全性案例的一部分，您也應該考慮保護資料、應用程式和裝置。

### <a name="guidance-for-azure-ad-free-or-office-365-customers"></a>Azure AD Free 或 Office 365 客戶的指導方針。

有一些建議，Azure AD Free 或 Office 365 應用程式客戶應採取來保護他們的使用者身分識別，下表旨在強調下列授權訂閱的主要動作：

- Office 365 （O365 E1、E3、E5、F1、A1、A3、A5）
- Office 365 Business （Essentials、Business、Business Premium）
- Microsoft 365 （M365 Business，A1）
- Azure AD Free （包含在 Azure、Dynamics 365、Intune 和 Power 平臺中）

| 建議的動作 | 詳細資料 |
| --- | --- |
| [啟用安全性預設值](concept-fundamentals-security-defaults.md) | 藉由啟用 MFA 並封鎖舊版驗證，來保護所有使用者身分識別和應用程式 |
| [啟用密碼雜湊同步](../hybrid/how-to-connect-password-hash-synchronization.md)處理（如果使用混合式身分識別） | 提供驗證的冗余和改善安全性（包括智慧型鎖定、IP 鎖定，以及探索洩露認證的功能）。 |
| [啟用 ADFS 智慧型鎖定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)（如果適用） | 保護您的使用者不會遇到惡意活動的外部網路帳戶鎖定。 |
| [啟用 Azure Active Directory 智慧鎖定](../authentication/howto-password-smart-lockout.md)（如果使用受控識別） | 智慧鎖定可協助鎖定嘗試猜測使用者密碼或使用暴力方法來取得的不良執行者。 |
| [停用應用程式的使用者同意](../manage-apps/configure-user-consent.md) | 系統管理員同意工作流程可讓系統管理員以安全的方式，授與需要系統管理員核准的應用程式存取權，讓使用者不會公開公司資料。 Microsoft 建議停用未來的使用者同意作業以協助減少介面區並降低此風險。 |
| [將支援的 SaaS 應用程式從資源庫整合到 Azure AD 並啟用單一登入](../manage-apps/add-application-portal.md) | Azure AD 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的某些應用程式可能就在可從 Azure 入口網站直接存取的資源庫中。 利用改良的使用者體驗（SSO），從遠端安全地提供公司 SaaS 應用程式的存取權 |
| [自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md)布建和解除布建（如果適用） | 在使用者需要存取的雲端（SaaS）應用程式中，自動建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動布建還包括維護和移除使用者身分識別，做為狀態或角色的變更，進而提高組織的安全性。 |
| [啟用安全的混合式存取：使用現有的應用程式傳遞控制器和網路保護繼承應用程式](../manage-apps/secure-hybrid-access.md)（如果適用） | 藉由將您的內部部署和雲端舊版驗證應用程式連接至現有的應用程式傳遞控制器或網路，以發佈和保護它們的 Azure AD。 |
| [啟用自助式密碼重設](../authentication/tutorial-enable-sspr.md)（適用于僅限雲端的帳戶） | 當使用者無法登入他們的裝置或應用程式時，這項功能可減少技術支援中心的電話和喪失生產力。 |
| [可能的話，請使用非全域系統管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 僅賦予您的系統管理員存取其所需存取區域的權限。 並非所有系統管理員都必須是全域管理員。 |
| [啟用 Microsoft 的密碼指引](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求使用者依照設定的排程變更其密碼，停用複雜性需求，而您的使用者會更容易記住其密碼並維護其安全性。 |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Azure AD Premium 方案1客戶的指導方針。

下表旨在強調下列授權訂閱的主要動作：

- Azure Active Directory Premium P1 （Azure AD P1）
- Enterprise Mobility + Security （EMS E3）
- Microsoft 365 （M365 E3、A3、F1、F3）

| 建議的動作 | 詳細資料 |
| --- | --- |
| [啟用 Azure MFA 和 SSPR 的結合註冊體驗，以簡化使用者註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md) | 可讓使用者在單一常見體驗中註冊 Azure Multi-Factor Authentication 和自助密碼重設。 |
| [為您的組織設定 MFA 設定](../authentication/howto-mfa-getstarted.md) | 確保帳戶受到保護，不會受到多重要素驗證的危害 |
| [啟用自助式密碼重設](../authentication/tutorial-enable-sspr.md) | 當使用者無法登入其裝置或應用程式時，這項功能可減少技術服務人員的來電和喪失生產力 |
| [執行密碼回寫](../authentication/tutorial-enable-sspr-writeback.md)（如果使用混合式身分識別） | 可讓雲端中的密碼變更回寫至內部部署 Windows Server Active Directory 環境。 |
| 建立和啟用條件式存取原則 | [適用于系統管理員的 MFA，用以保護已指派管理許可權的帳戶。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [封鎖舊版驗證通訊協定，因為與舊版驗證通訊協定相關聯的風險增加。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [適用于所有使用者和應用程式的 MFA，為您的環境建立平衡的 MFA 原則，保護您的使用者和應用程式。](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [需要 MFA for Azure 管理以針對存取 Azure 資源的任何使用者要求多重要素驗證，以保護您的特殊許可權資源。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [啟用密碼雜湊同步](../hybrid/how-to-connect-password-hash-synchronization.md)處理（如果使用混合式身分識別） | 提供驗證的冗余和改善安全性（包括智慧型鎖定、IP 鎖定，以及探索洩露認證的功能）。 |
| [啟用 ADFS 智慧型鎖定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)（如果適用） | 保護您的使用者不會遇到惡意活動的外部網路帳戶鎖定。 |
| [啟用 Azure Active Directory 智慧鎖定](../authentication/howto-password-smart-lockout.md)（如果使用受控識別） | 智慧鎖定可協助鎖定嘗試猜測使用者密碼或使用暴力方法來取得的不良執行者。 |
| [停用應用程式的使用者同意](../manage-apps/configure-user-consent.md) | 系統管理員同意工作流程可讓系統管理員以安全的方式，授與需要系統管理員核准的應用程式存取權，讓使用者不會公開公司資料。 Microsoft 建議停用未來的使用者同意作業以協助減少介面區並降低此風險。 |
| [使用應用程式 Proxy 啟用內部部署繼承應用程式的遠端存取](../manage-apps/application-proxy-add-on-premises-application.md) | 啟用 Azure AD 應用程式 Proxy，並與繼承應用程式整合，讓使用者使用其 Azure AD 帳戶來登入，以安全地存取內部部署應用程式。 |
| [啟用安全的混合式存取：使用現有的應用程式傳遞控制器和網路保護繼承應用程式](../manage-apps/secure-hybrid-access.md)（如果適用）。 | 藉由將您的內部部署和雲端舊版驗證應用程式連接至現有的應用程式傳遞控制器或網路，以發佈和保護它們的 Azure AD。 |
| [將支援的 SaaS 應用程式從資源庫整合到 Azure AD 並啟用單一登入](../manage-apps/add-application-portal.md) | Azure AD 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的某些應用程式可能就在可從 Azure 入口網站直接存取的資源庫中。 利用改良的使用者體驗（SSO），從遠端安全地提供公司 SaaS 應用程式的存取權。 |
| [自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md)布建和解除布建（如果適用） | 在使用者需要存取的雲端（SaaS）應用程式中，自動建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動布建還包括維護和移除使用者身分識別，做為狀態或角色的變更，進而提高組織的安全性。 |
| [啟用條件式存取–以裝置為基礎](../conditional-access/require-managed-devices.md) | 使用裝置型條件式存取來改善安全性和使用者體驗。 此步驟可確保使用者只能從符合安全性和合規性標準的裝置進行存取。 這些裝置也稱為受控裝置。 受管理的裝置可以是與 Intune 相容或已加入混合式 Azure AD 的裝置。 |
| [啟用密碼保護](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 防止使用者使用弱式和容易猜測的密碼。 |
| [指定超過一個全域管理員](../users-groups-roles/directory-emergency-access.md) | 在發生緊急狀況時，請指定至少兩個僅限雲端的永久全域管理員帳戶。 這些帳戶並非每天使用，而且應該有複雜的長密碼。 中斷玻璃帳戶可確保您可以在緊急情況下存取服務。 |
| [可能的話，請使用非全域系統管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 僅賦予您的系統管理員存取其所需存取區域的權限。 並非所有系統管理員都必須是全域管理員。 |
| [啟用 Microsoft 的密碼指引](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求使用者依照設定的排程變更其密碼，停用複雜性需求，而您的使用者會更容易記住其密碼並維護其安全性。 |
| [建立來賓使用者存取權的計劃](../b2b/what-is-b2b.md) | 讓他們使用自己的工作、學校或社交身分識別登入您的應用程式和服務，以與來賓使用者共同作業。 |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Azure AD Premium 計畫2客戶的指導方針。

下表旨在強調下列授權訂閱的主要動作：

- Azure Active Directory Premium P2 （Azure AD P2）
- Enterprise Mobility + Security （EMS E5）
- Microsoft 365 （M365 E5，A5）

| 建議的動作 | 詳細資料 |
| --- | --- |
| [啟用 Azure MFA 和 SSPR 的結合註冊體驗，以簡化使用者註冊體驗](../authentication/howto-registration-mfa-sspr-combined.md) | 可讓使用者在單一常見體驗中註冊 Azure Multi-Factor Authentication 和自助密碼重設。 |
| [為您的組織設定 MFA 設定](../authentication/howto-mfa-getstarted.md) | 確保帳戶受到保護，不會受到多重要素驗證的危害 |
| [啟用自助式密碼重設](../authentication/tutorial-enable-sspr.md) | 當使用者無法登入其裝置或應用程式時，這項功能可減少技術服務人員的來電和喪失生產力 |
| [執行密碼回寫](../authentication/tutorial-enable-sspr-writeback.md)（如果使用混合式身分識別） | 可讓雲端中的密碼變更回寫至內部部署 Windows Server Active Directory 環境。 |
| [啟用身分識別保護原則以強制執行 MFA 註冊](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | 管理 Azure 多重要素驗證（MFA）的推出。 |
| [啟用 Identity Protection 使用者和登入風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md) | 啟用 Identity Protection 使用者和登入原則。 建議的登入原則是以中等風險登入為目標，而且需要 MFA。 針對使用者原則，它應該將目標設為需要密碼變更動作的高風險使用者。 |
| 建立和啟用條件式存取原則 | [適用于系統管理員的 MFA，用以保護已指派管理許可權的帳戶。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [封鎖舊版驗證通訊協定，因為與舊版驗證通訊協定相關聯的風險增加。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [需要 MFA for Azure 管理以針對存取 Azure 資源的任何使用者要求多重要素驗證，以保護您的特殊許可權資源。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [啟用密碼雜湊同步](../hybrid/how-to-connect-password-hash-synchronization.md)處理（如果使用混合式身分識別） | 提供驗證的冗余和改善安全性（包括智慧型鎖定、IP 鎖定，以及探索洩露認證的功能）。 |
| [啟用 ADFS 智慧型鎖定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)（如果適用） | 保護您的使用者不會遇到惡意活動的外部網路帳戶鎖定。 |
| [啟用 Azure Active Directory 智慧鎖定](../authentication/howto-password-smart-lockout.md)（如果使用受控識別） | 智慧鎖定可協助鎖定嘗試猜測使用者密碼或使用暴力方法來取得的不良執行者。 |
| [停用應用程式的使用者同意](../manage-apps/configure-user-consent.md) | 系統管理員同意工作流程可讓系統管理員以安全的方式，授與需要系統管理員核准的應用程式存取權，讓使用者不會公開公司資料。 Microsoft 建議停用未來的使用者同意作業以協助減少介面區並降低此風險。 |
| [使用應用程式 Proxy 啟用內部部署繼承應用程式的遠端存取](../manage-apps/application-proxy-add-on-premises-application.md) | 啟用 Azure AD 應用程式 Proxy，並與繼承應用程式整合，讓使用者使用其 Azure AD 帳戶來登入，以安全地存取內部部署應用程式。 |
| [啟用安全的混合式存取：使用現有的應用程式傳遞控制器和網路保護繼承應用程式](../manage-apps/secure-hybrid-access.md)（如果適用）。 | 藉由將您的內部部署和雲端舊版驗證應用程式連接至現有的應用程式傳遞控制器或網路，以發佈和保護它們的 Azure AD。 |
| [將支援的 SaaS 應用程式從資源庫整合到 Azure AD 並啟用單一登入](../manage-apps/add-application-portal.md) | Azure AD 有一個資源庫，其中包含數千個預先整合的應用程式。 您組織使用的某些應用程式可能就在可從 Azure 入口網站直接存取的資源庫中。 利用改良的使用者體驗（SSO），從遠端安全地提供公司 SaaS 應用程式的存取權。 |
| [自動化 SaaS 應用程式的使用者](../app-provisioning/user-provisioning.md)布建和解除布建（如果適用） | 在使用者需要存取的雲端（SaaS）應用程式中，自動建立使用者身分識別和角色。 除了建立使用者身分識別以外，自動布建還包括維護和移除使用者身分識別，做為狀態或角色的變更，進而提高組織的安全性。 |
| [啟用條件式存取–以裝置為基礎](../conditional-access/require-managed-devices.md) | 使用裝置型條件式存取來改善安全性和使用者體驗。 此步驟可確保使用者只能從符合安全性和合規性標準的裝置進行存取。 這些裝置也稱為受控裝置。 受管理的裝置可以是與 Intune 相容或已加入混合式 Azure AD 的裝置。 |
| [啟用密碼保護](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 防止使用者使用弱式和容易猜測的密碼。 |
| [指定超過一個全域管理員](../users-groups-roles/directory-emergency-access.md) | 在發生緊急狀況時，請指定至少兩個僅限雲端的永久全域管理員帳戶。 這些帳戶並非每天使用，而且應該有複雜的長密碼。 中斷玻璃帳戶可確保您可以在緊急情況下存取服務。 |
| [可能的話，請使用非全域系統管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 僅賦予您的系統管理員存取其所需存取區域的權限。 並非所有系統管理員都必須是全域管理員。 |
| [啟用 Microsoft 的密碼指引](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求使用者依照設定的排程變更其密碼，停用複雜性需求，而您的使用者會更容易記住其密碼並維護其安全性。 |
| [建立來賓使用者存取權的計劃](../b2b/what-is-b2b.md) | 讓他們使用自己的工作、學校或社交身分識別登入您的應用程式和服務，以與來賓使用者共同作業。 |
| [啟用 Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 可讓您管理、控制及監視組織中重要資源的存取權，以確保系統管理員只有在必要時才有存取權，並可進行核准 |

## <a name="next-steps"></a>後續步驟

- 如需 Azure AD 個別功能的詳細部署指引，請參閱[Azure AD 專案部署計畫](active-directory-deployment-plans.md)。

- 如需端對端 Azure AD 部署檢查清單，請參閱[Azure Active Directory 功能部署指南](active-directory-deployment-checklist-p2.md)一文