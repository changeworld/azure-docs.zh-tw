---
title: Azure AD 中適用於管理員的安全存取做法 | Microsoft Docs
description: 確定您組織的系統管理存取權和系統管理員帳戶是安全的。 適用於設定 Azure AD、Azure 和 Microsoft 線上服務的系統架構設計人員和 IT 專業人員。
services: active-directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.date: 04/30/2020
ms.topic: conceptual
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.custom: it-pro
ms.reviewer: martincoetzer; MarkMorow
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd189db10b599c0bc6bd5a3dbae2b1bc21b53b0c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795910"
---
# <a name="securing-privileged-access-for-hybrid-and-cloud-deployments-in-azure-ad"></a>在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取

商務資產的安全性取決於負責管理 IT 系統的特殊權限帳戶是否完整。 網路攻擊者會使用認證竊取攻擊來瞄準管理帳戶，並使用其他特殊權限存取權來嘗試取得敏感性資料。

就雲端服務而言，預防和回應是雲端服務提供者和客戶的共同責任。 若想進一步了解端點和雲端有哪些最新威脅，請參閱 [Microsoft Security Intelligence Report](https://www.microsoft.com/security/operations/security-intelligence-report)。 本文可協助您擬定適當計劃，以期消弭您目前的計劃與此處所述的指導方針之間的落差。

> [!NOTE]
> Microsoft 致力於達到最高水準的信任、透明度、標準一致性和法規遵循性。 請經由 [Microsoft 信任中心 - 安全性](https://www.microsoft.com/trustcenter/security)深入了解 Microsoft 全球事件回應小組如何降低對雲端服務的攻擊所造成的影響，所及 Microsoft 商業產品和雲端服務內建的安全性，並經由 [Microsoft 信任中心 - 合規性](https://www.microsoft.com/trustcenter/compliance)了解 Microsoft 的合規性目標。

傳統上，組織安全性著重於以網路的進入點和離開點為安全界限。 不過，由於網際網路上的 SaaS 應用程式和個人裝置，這種方法已不太有用。 在 Azure AD 中，我們將網路安全界限換成在組織的身分識別層中驗證，以管控指派為特殊權限系統管理角色的使用者。 不論環境是內部部署、雲端或混合式，這些使用者的存取權都必須受到保護。

保護特殊權限存取需要變更：

* 程序、系統管理實作和知識管理
* 技術元件，例如主機防禦、帳戶保護和身分識別管理

在您關注的 Microsoft 服務中管理和報告，以此來保護特殊權限存取。 如果您有內部部署管理帳戶，請參閱[保護特殊權限存取](/windows-server/identity/securing-privileged-access/securing-privileged-access)，以取得在 Active Directory 中保護內部部署和混合式特殊權限存取的指引。

> [!NOTE]
> 本文中的指引主要參照 Azure Active Directory Premium 計劃 P1 和 P2 所包含的 Azure Active Directory 功能。 Azure Active Directory Premium P2 隨附於 EMS E5 套件和 Microsoft 365 E5 套件。 本指引假設您的組織已為使用者購買 Azure AD Premium P2 授權。 如果您沒有這些授權，本指引可能有某些內容不適用於您的組織。 此外，在本文中，「全域管理員」 (或「全域管理員」) 表示與「公司系統管理員」或「租使用者系統管理員」相同的東西。

## <a name="develop-a-roadmap"></a>擬定計劃

Microsoft 建議您擬定並遵循適當計劃以保護特殊權限存取，使網路攻擊者無法取得。 您可以根據組織內的現有功能和特定需求，隨時調整您的計劃。 計劃中的每個階段均應提高對手對您的內部部署、雲端和混合式資產發動特殊權限存取攻擊的成本和難度。 Microsoft 建議下列四個藍圖階段。 最先排定最有效和最快速的實作。 根據 Microsoft 在網路攻擊事件和因應措施方面的經驗，本文可當成您的指南。 此藍圖的時間表為近似值。

![具時程的各個計劃階段](./media/directory-admin-roles-secure/roadmap-timeline.png)

* 階段 1 (24-48 小時)：建議您立即執行的重大事項

* 階段 2 (2-4 週)：減少最常用的攻擊技巧

* 階段 3 (1-3 個月)：建置可見性以及對管理活動的全面掌控

* 階段 4 (六個月後)：繼續建置防禦機制以進一步強化安全性平台

此計劃架構只在充分運用您已部署的 Microsoft 技術。 請考量搭配您已部署或正考慮部署的任何安全性工具 (來自其他廠商)。

## <a name="stage-1-critical-items-to-do-right-now"></a>第 1 階段：需要立即處理的重要項目

![第 1 階段需要優先處理的重要項目](./media/directory-admin-roles-secure/stage-one.png)

計劃的階段 1 著重於可快速輕鬆實作的工作。 我們建議您立即在前 24-48 小時內完成這幾項工作，以確保特殊權限存取能夠得到基本層級的保護。 「受保護的特殊權限存取」計劃的這個階段包含下列動作：

### <a name="general-preparation"></a>一般準備工作

#### <a name="turn-on-azure-ad-privileged-identity-management"></a>開啟 Azure AD Privileged Identity Management

建議您在 Azure AD 實際執行環境中開啟 Azure AD Privileged Identity Management (PIM)。 開啟 PIM 之後，您會收到特殊許可權存取角色變更的通知電子郵件訊息。 這些通知提早警告有其他使用者加入高度特殊權限角色。

Azure AD Privileged Identity Management 隨附於 Azure AD Premium P2 或 EMS E5 中。 為協助您保護存取內部部署和雲端的應用程式及資源，請註冊 [Enterprise Mobility + Security 90 天免費試用](https://www.microsoft.com/cloud-platform/enterprise-mobility-security-trial)。 Azure AD Privileged Identity Management 和 Azure AD Identity Protection 使用 Azure AD 報告、稽核和警示來監視安全性活動。

開啟 Azure AD Privileged Identity Management 之後：

1. 以 Azure AD 生產環境組織的全域管理員帳戶，登入 [Azure 入口網站](https://portal.azure.com/)。

2. 若要選取您想要使用 Privileged Identity Management 的 Azure AD 組織，請在 Azure 入口網站右上角選取您的使用者名稱。

3. 在 Azure 入口網站功能表上，選取 [所有服務]，在清單中篩選出 [Azure AD Privileged Identity Management]。

4. 從 [所有服務] 清單中開啟 Privileged Identity Management，並將其釘選至儀表板。

請確定組織中第一個使用 PIM 的人指派為**安全性系統管理員**和**特殊權限角色管理員**角色。 只有特殊權限角色管理員才能管理使用者的 Azure AD Directory 角色指派。 PIM 精靈會引導您完成初始探索和指派體驗。 您此時可以結束精靈而不進行任何其他變更。

#### <a name="identify-and-categorize-accounts-that-are-in-highly-privileged-roles"></a>識別及分類高特殊權限角色中的帳戶

開啟 Azure AD Privileged Identity Management 之後，請檢視擔任下列 Azure AD 角色的使用者：

* 全域管理員
* 特殊權限角色管理員
* Exchange 系統管理員
* SharePoint 管理員

如果您的組織中沒有 Azure AD Privileged Identity Management，您可以使用 [PowerShell API](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)。 從全域管理員角色開始，因為全域管理員在組織訂閱的所有雲端服務上具有相同的權限。 這些權限無論在何處指派，一律授與：在 Microsoft 365 系統管理中心、Azure 入口網站，或以 Microsoft PowerShell 的 Azure AD 模組指派。

請移除這些角色中不再需要的任何帳戶。 然後，將指派給管理員角色的其餘帳戶分類：

* 指派給系統管理使用者，但也作為非系統管理用途 (例如個人電子郵件)
* 指派給系統管理使用者，且只作為系統管理用途
* 在多個使用者之間共用
* 用於急用緊急存取案例
* 用於自動化指令碼
* 用於外部使用者

#### <a name="define-at-least-two-emergency-access-accounts"></a>定義至少兩個緊急存取帳戶

使用者可能意外遭到自己的角色鎖定。 例如，如果同盟內部部署身分識別提供者無法使用，使用者就無法登入或啟動現有的系統管理員帳戶。 您可以儲備兩個以上的緊急存取帳戶，以防意外失去存取權。

緊急存取帳戶有助於限制 Azure AD 組織內的特殊權限存取。 這些帳戶具有高度特殊權限，不會指派給特定個人。 緊急存取帳戶限用於無法使用一般系統管理帳戶的「急用」緊急情況。 務必管控只在必要時刻才使用緊急帳戶。

請評估已指派或適用於全域管理員角色的帳戶。 如果您未看到任何使用 \*.onmicrosoft.com 網域的僅限雲端帳戶 (用於「急用」緊急存取)，請建立這種帳戶。 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取系統管理帳戶](directory-emergency-access.md)。

#### <a name="turn-on-multi-factor-authentication-and-register-all-other-highly-privileged-single-user-non-federated-admin-accounts"></a>開啟多重要素驗證，並註冊其他所有高度特殊權限的單一使用者非同盟管理帳戶

當所有永久指派給一或多個 Azure AD 系統管理員角色的個別使用者登入時，需要 Azure Multi-Factor Authentication (MFA) ：全域系統管理員、特殊許可權角色管理員、Exchange 系統管理員和 SharePoint 系統管理員。 請使用指南啟用[管理員帳戶的 Multi-factor Authentication (MFA)](../authentication/howto-mfa-userstates.md)，並確定這些使用者全都已在 [https://aka.ms/mfasetup](https://aka.ms/mfasetup) 註冊。 您可以在[保護 Office 365 中的資料和服務存取](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)指南的步驟 2 和步驟 3 下找到詳細資訊。 

## <a name="stage-2-mitigate-frequently-used-attacks"></a>第 2 階段：緩解常用的攻擊

![第 2 階段緩解常用的攻擊](./media/directory-admin-roles-secure/stage-two.png)

計劃的階段 2 著重於降低認證竊取和濫用最常用的攻擊技巧，可以在大約 2-4 週內實作。 「受保護的特殊權限存取」計劃的這個階段包含下列動作。

### <a name="general-preparation"></a>一般準備工作

#### <a name="conduct-an-inventory-of-services-owners-and-admins"></a>進行服務、擁有者和系統管理員的清查

隨著「攜帶您自己的裝置」和在家工作政策的普及，還有無線連線的發展，監視何人連線至您的網路已不可或缺。 安全性稽核可以揭露網路上不受組織支援、風險又高的裝置、應用程式和程式。 如需詳細資訊，請參閱 [Azure 安全性管理和監視概觀](../../security/fundamentals/management-monitoring-overview.md)。 請務必在您的清查程序中納入下列所有工作。

* 識別具有系統管理角色的使用者，以及可供其進行管理的服務。
* 使用 Azure AD PIM 查明組織中哪些使用者具有 Azure AD 的管理員存取權。
* 除了在 Azure AD 中定義的角色以外，Office 365 也隨附一組您可以指派給組織使用者的管理員角色。 每個管理員角色都對應至常用的商務功能，並授權組織中的人在 [Microsoft 365 系統管理中心](https://admin.microsoft.com)執行特定的工作。 使用 Microsoft 365 系統管理中心查明組織中哪些使用者具有 Office 365 的管理員存取權，包括透過不在 Azure AD 中管理的角色。 如需詳細資訊，請參閱[關於 Office 365 系統管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)和 [Office 365 的安全性做法](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)。
* 請在組織依賴的服務中清查，例如 Azure、Intune 或 Dynamics 365。
* 請確定您的帳戶是作為管理用途：

  * 附加工作電子郵件地址
  * 已註冊 Azure Multi-Factor Authentication 或使用 MFA 內部部署
* 要求使用者提供他們需要系統管理存取權的業務理由。
* 移除不需要的個人和服務的系統管理員存取權。

#### <a name="identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts"></a>識別系統管理角色中需要切換至工作或學校帳戶的 Microsoft 帳戶

如果初始全域管理員在開始使用 Azure AD 時，重複使用其現有的 Microsoft 帳戶認證，請將 Microsoft 帳戶換成個別雲端式帳戶或同步處理的帳戶。

#### <a name="ensure-separate-user-accounts-and-mail-forwarding-for-global-administrator-accounts"></a>確認全域系統管理員帳戶的個別使用者帳戶和郵件轉寄

網路攻擊者經常誘騙個人電子郵件帳戶，這種風險讓全域管理員帳戶更不可能使用個人電子郵件地址。 為了杜絕管理員權限的網際網路風險，請為每個具有管理員權限的使用者建立專用帳戶。

* 請務必為使用者建立個別帳戶來執行全域管理員工作
* 請確定全域管理員不會無意中以管理帳戶開啟電子郵件或執行程式
* 請確定這些帳戶會將其電子郵件轉寄至工作信箱

#### <a name="ensure-the-passwords-of-administrative-accounts-have-recently-changed"></a>確訂系統管理帳戶的密碼近期做過變更

請確定所有使用者在過去 90 天內登入其系統管理帳戶並變更密碼至少一次。 此外，請確認任何共用帳戶最近已變更密碼。

#### <a name="turn-on-password-hash-synchronization"></a>開啟密碼雜湊同步處理

Azure AD Connect 會將使用者密碼的雜湊，從內部部署 Active Directory 同步到雲端式 Azure AD 組織。 如果您使用與 Active Directory 同盟服務 (AD FS) 同盟，則可以將密碼雜湊同步處理當作備份。 如果內部部署 Active Directory 或 AD FS 伺服器暫時無法使用，此備份就很有用。

密碼雜湊同步處理可讓使用者以登入內部部署 Active Directory 執行個體時的同一個密碼來登入服務。 密碼雜湊同步處理可讓 Identity Protection 比較密碼雜湊與已知遭盜用的密碼，以偵測遭盜用的認證。 如需詳細資訊，請參閱[使用 Azure AD Connect 同步實作密碼雜湊同步處理](../hybrid/how-to-connect-password-hash-synchronization.md)。

#### <a name="require-multi-factor-authentication-for-users-in-privileged-roles-and-exposed-users"></a>對於具有特殊權限角色的使用者和曝露的使用者，要求多重要素驗證

Azure AD 建議對所有使用者要求多重要素驗證 (MFA)。 務必考量帳戶遭入侵時會帶來嚴重影響的使用者 (例如財務主管)。 MFA 可降低密碼遭盜用時的攻擊風險。

開啟：

* [使用條件式存取原則的 MFA](../authentication/howto-mfa-getstarted.md) (對於組織中的所有使用者)。

如果您使用 Windows Hello 企業版，則使用 Windows Hello 登入體驗可符合 MFA 需求。 如需詳細資訊，請參閱 [Windows Hello](/windows/uwp/security/microsoft-passport)。

#### <a name="configure-identity-protection"></a>設定 Identity Protection

Azure AD Identity Protection 是以演算法為基礎的監視和報告工具，可偵測影響您組織身分識別的潛在弱點。 您可以設定自動化機制來回應這些偵測到的可疑活動，並採取適當動作加以解決。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)。

#### <a name="obtain-your-office-365-secure-score-if-using-office-365"></a>取得您的 Office 365 安全分數 (如果使用 Office 365)

安全分數會查看您所使用 Office 365 服務的設定和活動，並將其與 Microsoft 所建立的基準進行比較。 您將會根據您與安全性實務的一致程度來取得分數。 任何人只要具有 Office 365 商務進階版或企業版訂用帳戶的管理員權限，都可在 [https://securescore.office.com](https://securescore.office.com/) 存取安全分數。

#### <a name="review-the-office-365-security-and-compliance-guidance-if-using-office-365"></a>檢閱 Office 365 安全性與合規性指引 (如果使用 Office 365)

[安全性與合規性方案](https://support.office.com/article/Plan-for-security-and-compliance-in-Office-365-dc4f704c-6fcc-4cab-9a02-95a824e4fb57)概述 Office 365 客戶設定 Office 365 及啟用其他 EMS 功能的方法。 接著，請檢閱如何[保護 Office 365 中的資料和服務存取](https://support.office.com/article/Protect-access-to-data-and-services-in-Office-365-a6ef28a4-2447-4b43-aae2-f5af6d53c68e)的步驟 3-6，以及如何[監視 Office 365 中的安全性和合規性](https://support.office.com/article/Monitor-security-and-compliance-in-Office-365-b62f1722-fd39-44eb-8361-da61d21509b6)的指南。

#### <a name="configure-office-365-activity-monitoring-if-using-office-365"></a>設定 Office 365 活動監視 (如果使用 Office 365)

監視組織中使用 Office 365 的使用者，以識別哪些員工雖然具有管理帳戶，但可能因為並不登入這些入口網站，而不需要存取 Office 365。 如需詳細資訊，請參閱 [Microsoft 365 系統管理中心的活動報告](https://support.office.com/article/Activity-Reports-in-the-Office-365-admin-center-0d6dfb17-8582-4172-a9a9-aed798150263)。

#### <a name="establish-incidentemergency-response-plan-owners"></a>建立事件/緊急回應計劃擁有者

建立成功的事件回應能力需要充分規劃和大量資源。 您必須持續監視網路攻擊，並建立事件處理的優先順序。 收集、分析並報告事件資料，與其他內部群組和計劃擁有者建立關係和溝通管道。 如需詳細資訊，請參閱 [Microsoft 安全性回應中心](https://technet.microsoft.com/security/dn440717)。

#### <a name="secure-on-premises-privileged-administrative-accounts-if-not-already-done"></a>保護內部部署的特殊權限系統管理帳戶 (如果尚未這麼做)

如果您的 Azure Active Directory 組織與內部部署 Active Directory 同步，請遵循[安全性特殊權限存取藍圖](/windows-server/identity/securing-privileged-access/securing-privileged-access)中的指引：此階段包括：

* 為需要進行內部部署系統管理工作的使用者建立個別的管理帳戶
* 為 Active Directory 系統管理員部署特殊權限存取工作站
* 為工作站和伺服器建立唯一的本機系統管理員密碼

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>組織管理 Azure 存取的其他步驟

#### <a name="complete-an-inventory-of-subscriptions"></a>完成訂用帳戶的清查

使用企業版入口網站和 Azure 入口網站，識別您的組織中裝載生產應用程式的訂用帳戶。

#### <a name="remove-microsoft-accounts-from-admin-roles"></a>從管理員角色移除 Microsoft 帳戶

來自其他程式 (例如 Xbox、Live 和 Outlook) 的 Microsoft 帳戶，不應作為組織訂用帳戶的系統管理員帳戶。 請移除所有 Microsoft 帳戶的管理員身分，並換成 Azure AD (例如 chris@contoso.com) 公司或學校帳戶。 基於系統管理目的，請仰賴 Azure AD (而不是其他服務) 中驗證的帳戶。

#### <a name="monitor-azure-activity"></a>監視 Azure 活動

「Azure 活動記錄」提供 Azure 中的訂用帳戶層級事件的記錄。 它提供關於何人建立、更新及刪除了什麼資源，以及這些事件於何時發生的資訊。 如需詳細資訊，請參閱[稽核和接收 Azure 訂用帳戶中關於重要動作的通知](../../azure-monitor/platform/alerts-activity-log.md)。

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>組織透過 Azure AD 對其他雲端應用程式的存取進行管理的其他步驟

#### <a name="configure-conditional-access-policies"></a>設定條件式存取原則

為內部部署和雲端託管應用程式準備條件式存取原則。 如果您有已加入使用者工作場所的裝置，請從[使用 Azure Active Directory 裝置註冊來設定內部部署條件式存取](../devices/overview.md)取得詳細資訊。

## <a name="stage-3-take-control-of-admin-activity"></a>第 3 階段：掌控系統管理活動

![第 3 階段：掌控系統管理活動](./media/directory-admin-roles-secure/stage-three.png)

第 3 階段以第 2 階段的緩解措施為基礎，應該在大約 1-3 個月內實作。 「受保護的特殊權限存取」計劃的這個階段包含下列要項。

### <a name="general-preparation"></a>一般準備工作

#### <a name="complete-an-access-review-of-users-in-administrator-roles"></a>在系統管理員角色中完成使用者的存取權檢閱

有愈來愈多的公司使用者透過雲端服務取得特殊權限存取，這可能導致存取失控。 現今，使用者可能成為 Office 365 的全域管理員、Azure 訂用帳戶管理員，或者，對於 VM 或透過 SaaS 應用程式而具有管理員存取權。

組織應該讓所有員工以無特殊權限使用者身分來處理日常商務交易，然後只在需要時才授與管理員權限。 完成存取權檢閱，以識別並確認有資格啟動管理員權限的使用者。

建議您：

1. 確認哪些使用者是 Azure AD 管理員，隨需啟用 Just-In-Time 管理員存取和角色型安全性控制。
2. 將沒有正當理由可取得管理員特殊權限存取權的使用者轉換為不同角色 (如果沒有符合資格的角色，則將其移除)。

#### <a name="continue-rollout-of-stronger-authentication-for-all-users"></a>繼續對所有使用者施行更嚴格的驗證

高度曝露的使用者需要有新式的增強式驗證，例如 Azure MFA 或 Windows Hello。 高度曝露的使用者例子包括：

* 高層主管
* 高階經理
* 重要的 IT 和安全性人員

#### <a name="use-dedicated-workstations-for-administration-for-azure-ad"></a>以專用的工作站處理 Azure AD 的管理工作

攻擊者可能嘗試以特殊權限帳戶為目標，以破壞資料的完整性和真確性。 攻擊者經常利用惡意程式碼來改變程式邏輯，或窺探管理員輸入認證。 特殊權限存取工作站 (PAW) 提供敏感性工作的專用作業系統，以免遭受網際網路攻擊和威脅載體攻擊。 將這些敏感性工作和帳戶脫離日常使用的工作站和裝置，可提供強大的保護以避免：

* 網路釣魚攻擊
* 應用程式和作業系統的弱點
* 模擬攻擊
* 認證竊取攻擊，例如記錄按鍵輸入、雜湊傳遞和票證傳遞

您可以部署特殊權限存取工作站，以降低管理員在未經強化的桌面環境輸入認證時的風險。 如需詳細資訊，請參閱[特殊權限存取工作站](/windows-server/identity/securing-privileged-access/privileged-access-workstations)。

#### <a name="review-national-institute-of-standards-and-technology-recommendations-for-handling-incidents"></a>檢閱美國國家標準技術局的事件處理建議

美國國家標準與技術的 (NIST) 提供事件處理的指導方針，特別是針對事件相關資料的分析，以及判斷每個事件的適當回應。 如需詳細資訊，請參閱 [NIST Computer Security Incident Handling Guide (SP 800-61，修訂 2)](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)。

#### <a name="implement-privileged-identity-management-pim-for-jit-to-additional-administrative-roles"></a>對其他系統管理角色實作適用於 JIT 的 Privileged Identity Management (PIM)

對於 Azure Active Directory，請使用 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) 功能。 您可以透過下列方式在時間限制內啟用特殊權限角色：

* 啟動管理員權限來執行特定工作
* 在啟用程序期間強制執行 MFA
* 使用警示通知管理員有頻外變更
* 讓使用者在預先設定的一段時間內，保留其特殊權限存取
* 允許安全性管理員：

  * 探索所有特殊權限身分識別
  * 檢視稽核報告
  * 建立存取權檢閱，以識別每個有資格啟動管理員權限的使用者

如果您已經在使用 Azure AD Privileged Identity Management，請視需要調整時間範圍許可權的時間範圍 (例如，維護期間) 。

#### <a name="determine-exposure-to-password-based-sign-in-protocols-if-using-exchange-online"></a>判斷密碼式登入通訊協定的風險 (如果使用 Exchange Online)

建議找出認證遭盜用時將對組織帶來災難的所有潛在使用者。 針對這些使用者，請施行強式驗證需求，並使用 Azure AD 條件式存取，禁止他們以使用者名稱和密碼登入電子郵件。 您可以使用條件式存取來[封鎖舊式驗證](../conditional-access/block-legacy-authentication.md)，也可以透過 Exchange Online 來[封鎖基本驗證](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online)。

#### <a name="complete-a-roles-review-assessment-for-office-365-roles-if-using-office-365"></a>完成 Office 365 角色的角色檢閱評估 (如果使用 Office 365)

評估所有的管理員使用者是否都屬於正確的角色 (根據這項評估刪除並重新指派)。

#### <a name="review-the-security-incident-management-approach-used-in-office-365-and-compare-with-your-own-organization"></a>檢閱 Office 365 中使用的安全性事件管理方法，並與您自己的組織進行比較

您可以從 [Microsoft Office 365 中的安全性事件管理](https://www.microsoft.com/download/details.aspx?id=54302)下載此報告。

#### <a name="continue-to-secure-on-premises-privileged-administrative-accounts"></a>繼續保護內部部署的特殊權限系統管理帳戶

如果您的 Azure Active Directory 連線至內部部署 Active Directory，請遵循[安全性特殊權限存取計劃](/windows-server/identity/securing-privileged-access/securing-privileged-access)的指引：階段 2. 在這個階段，您需要：

* 為所有管理員部署特殊權限存取工作站
* 需要 MFA
* 使用 Just Enough Admin 來維護網域控制站，以縮小網域的受攻擊面
* 部署進階威脅評量來偵測攻擊

### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>組織管理 Azure 存取的其他步驟

#### <a name="establish-integrated-monitoring"></a>建立整合式監視

[Azure 資訊安全中心](../../security-center/security-center-intro.md)：

* 為所有 Azure 訂用帳戶提供整合式安全性監視和原則管理
* 協助偵測可能被忽視的威脅
* 適用於各式各樣的安全性解決方案

#### <a name="inventory-your-privileged-accounts-within-hosted-virtual-machines"></a>清查您在代管虛擬機器內的特殊權限帳戶

您通常不需要為使用者授與您所有 Azure 訂用帳戶或資源的無限制許可權。 使用 Azure AD 管理員角色僅授與使用者執行作業所需的存取權。 例如，使用 Azure AD 管理員角色讓一個管理員僅管理訂用帳戶中的 VM，而讓另一個管理員管理同一個訂用帳戶中的 SQL 資料庫。 如需詳細資訊，請參閱[在 Azure 入口網站中開始使用角色型存取控制](../../role-based-access-control/overview.md)。

#### <a name="implement-pim-for-azure-ad-administrator-roles"></a>為 Azure AD 系統管理員角色實作 PIM

透過具有 Azure AD 系統管理員角色的 Privileged Identity Management，可管理、控制及監視對 Azure 資源的存取。 使用 PIM 可縮短權限的曝光時間，並透過報告和警示更清楚了解權限的使用情形，以加強保護。 如需詳細資訊，請參閱[使用 Privileged Identity Management 管理對 Azure 資源的 RABC 存取](../../role-based-access-control/best-practices.md)。

#### <a name="use-azure-log-integrations-to-send-relevant-azure-logs-to-your-siem-systems"></a>使用 Azure 記錄整合將相關 Azure 記錄傳送至您的 SIEM 系統

Azure 記錄整合可讓您將來自 Azure 資源的未經處理記錄，整合到組織現有的安全性資訊和事件管理 (SIEM) 系統。 [Azure 記錄整合](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)收集來自 Windows 事件檢視器的 Windows 事件，以及下列來源的 Azure 資源：

* Azure 活動記錄
* Azure 資訊安全中心警示
* Azure 資源記錄

### <a name="additional-steps-for-organizations-managing-access-to-other-cloud-apps-via-azure-ad"></a>組織透過 Azure AD 對其他雲端應用程式的存取進行管理的其他步驟

#### <a name="implement-user-provisioning-for-connected-apps"></a>為連線的應用程式實作使用者佈建

Azure AD 可讓您自動建立和維護雲端應用程式 (例如 Dropbox、Salesforce 和 ServiceNow) 中的使用者身分識別。 如需詳細資訊，請參閱[自動化使用 Azure AD 對於 SaaS 應用程式的使用者佈建和解除佈建](../app-provisioning/user-provisioning.md)。

#### <a name="integrate-information-protection"></a>整合資訊保護

Microsoft Cloud App Security 可讓您調查檔案，並根據 Azure 資訊保護分類標籤來設定原則，以更完整檢視和控制雲端資料。 掃描及分類雲端中的檔案，並套用 Azure 資訊保護標籤。 如需詳細資訊，請參閱 [Azure 資訊保護整合](/cloud-app-security/azip-integration)。

#### <a name="configure-conditional-access"></a>設定條件式存取

針對 [SaaS 應用程式](https://azure.microsoft.com/overview/what-is-saas/)和 Azure AD 連線的應用程式，根據群組、位置和應用程式敏感性來設定條件式存取。 

#### <a name="monitor-activity-in-connected-cloud-apps"></a>監視連線的雲端應用程式中的活動

建議使用 [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)，以確保在連線的應用程式中也能保護使用者存取。 這項功能可保護企業存取雲端應用程式時的安全性，並保護您的管理帳戶，可讓您：

* 擴充對雲端應用程式的可見性和控制
* 建立存取、活動和資料共用的原則
* 自動識別風險的活動、異常行為和威脅
* 防止資料外洩
* 盡可能降低風險，並將威脅防護和原則強制執行自動化

Cloud App Security SIEM 代理程式可整合 Cloud App Security 與您的 SIEM 伺服器，讓您集中監視 Office 365 警示和活動。 它會在您的伺服器上執行，並從 Cloud App Security 提取警示和活動，再將其串流至 SIEM 伺服器。 如需詳細資訊，請參閱 [SIEM 整合](/cloud-app-security/siem)。

## <a name="stage-4-continue-building-defenses"></a>階段 4：繼續建立防禦

![第 4 階段：採取主動的安全態度](./media/directory-admin-roles-secure/stage-four.png)

藍圖的第 4 階段應該在六個月後實作。 完成藍圖來強化特殊權限存取保護，以免遭受目前已知的潛在攻擊。 面對未來的安全性威脅，建議持之以恆確保安全性，讓敵人付出更高代價且難以成功入侵您的環境。

保護特殊權限存取攸關保證商務資產的安全性。 但這應該納入完整的安全性計劃中，以持續保證安全性。 此計劃應該包含如下的元素：

* 原則
* 作業
* 資訊安全性
* 伺服器
* 應用程式
* PC
* 裝置
* 雲端網狀架構

建議採取下列做法來管理特殊權限存取帳戶：

* 確保管理員以無特殊權限的使用者身分執行日常業務。
* 必要時才授與特殊權限存取，使用完畢就移除 (Just-In-Time)。
* 保留特殊權限帳戶相關的稽核活動記錄

如需關於如何建置完整安全性計劃的詳細資訊，請參閱 [Microsoft 雲端 IT 架構資源](https://almbok.com/office365/microsoft_cloud_it_architecture_resources)。 若想利用 Microsoft 服務來協助您實作藍圖的任何部分，請連絡 Microsoft 代表，或請參閱[建置重要網路防禦機制以保護您的企業](https://www.microsoft.com/en-us/microsoftservices/campaigns/cybersecurity-protection.aspx)。

「受保護的特殊權限存取」計劃的這個最後階段包含下列要項。

### <a name="general-preparation"></a>一般準備工作

#### <a name="review-admin-roles-in-azure-ad"></a>在 Azure AD 中檢閱管理員角色

判斷目前內建的 Azure AD 管理員角色是否仍在最新狀態，並確保使用者只擔任所需的角色。 在 Azure AD 中，您可以指派個別的系統管理員來負責不同的功能。 如需詳細資訊，請參閱[在 Azure Active Directory 中指派系統管理員角色](directory-assign-admin-roles.md)。

#### <a name="review-users-who-have-administration-of-azure-ad-joined-devices"></a>檢閱有哪些使用者可管理已加入 Azure AD 的裝置

如需詳細資訊，請參閱[如何設定已加入 Azure Active Directory 的混合式裝置](../devices/hybrid-azuread-join-plan.md)。

#### <a name="review-members-of-built-in-office-365-admin-roles"></a>檢閱[內建 Office 365 管理員角色](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)的成員
如果您不使用 Office 365，請略過此步驟。
‎
#### <a name="validate-incident-response-plan"></a>驗證事件回應計劃

若要改善您的計劃，Microsoft 建議您定期驗證您的計劃如預期運作：

* 檢查您現有的計劃是否有缺失
* 根據事後分析，修正現有做法或定義新的做法
* 確保更新的事件回應計劃和做法已普及整個組織


### <a name="additional-steps-for-organizations-managing-access-to-azure"></a>組織管理 Azure 存取的其他步驟 

確認您是否需要[將 Azure 訂用帳戶的擁有權轉移給另一個帳戶](../../cost-management-billing/manage/billing-subscription-transfer.md)。
‎

## <a name="break-glass-what-to-do-in-an-emergency"></a>「急用」：緊急狀況的因應措施

![緊急情況急用存取的帳戶](./media/directory-admin-roles-secure/emergency.jpeg)

1. 將事件的相關資訊告知重要的管理員和安全主管。

2. 檢閱您的攻擊腳本。

3. 存取您的「急用」帳戶使用者名稱和密碼組合，以登入 Azure AD。

4. [提出 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md)以取得 Microsoft 的協助。

5. 查看 [Azure AD 登入報告](../reports-monitoring/overview-reports.md)。 事件發生後，可能經過一段時間才會出現在報告中。

6. 在混合式環境中，如果您的內部部署基礎結構已同盟且您的 AD FS 伺服器無法使用，您可以暫時從同盟驗證切換為使用密碼雜湊同步處理。此參數會將網域同盟還原回受控驗證，直到 AD FS 伺服器可用為止。

7. 監控特殊權限帳戶的電子郵件。

8. 確實儲存相關記錄的備份，以供可能的鑑識和法律調查使用。

如需關於 Microsoft Office 365 如何處理安全性事件的詳細資訊，請參閱 [Microsoft Office 365 中的安全性事件管理](https://aka.ms/Office365SIM)。

## <a name="faq-answers-for-securing-privileged-access"></a>常見問題集：關於保護特殊權限存取的回答  

**問：** 如果尚未執行任何安全存取元件，該怎麼辦？

**答：** 定義至少兩個急用帳戶、將 MFA 指派給您的特殊權限管理員帳戶，並將使用者帳戶與全域管理員帳戶區隔開來。

**問：** 遭到入侵後，應最先處理的問題為何？

**答：** 確定您對高度公開的個人要求最強的驗證。

**問：** 如果我們的特殊權限管理員已停用，將會如何？

**答：** 建立永遠保持最新狀態的全域管理員帳戶。

**問：** 如果只剩下一個全域管理員，且無法觸達，會發生什麼事？

**答：** 使用您的其中一個急用帳戶立即取得特殊權限存取。

**問：** 如何保護組織內的管理員？

**答：** 讓管理員一律以標準「非特殊許可權」使用者的形式來進行日常業務。

**問：** 在 Azure AD 中建立管理員帳戶的最佳做法為何？

**答：** 將特殊權限存取保留給特定管理工作。

**問：** 有哪些工具可減少持續性管理員存取？

**答：** Privileged Identity Management (PIM) 和 Azure AD 管理員角色。

**問：** 將管理員帳戶同步至 Azure AD 時，Microsoft 的定位為何？

**答：** 第 0 層管理帳戶僅用於內部部署 AD 帳戶。 這類帳戶通常不會與雲端的 Azure AD 同步。 第 0 層管理帳戶包括的帳戶、群組及其他資產，可直接或間接控制管理內部部署 Active Directory 樹系、網域、網域控制站和資產。

**問：** 如何防止管理員在入口網站中指派隨機的管理員存取？

**答：** 對所有使用者和大部分的管理員使用非特殊權限帳戶。 首先應確認組織的使用量，以判斷哪幾個管理員帳戶應具有特殊權限。 然後監視新建立的系統管理使用者。

## <a name="next-steps"></a>後續步驟

* [Microsoft 信任中心 (產品安全性)](https://www.microsoft.com/trustcenter/security) – Microsoft 雲端產品和服務的安全性功能

* [Microsoft 信任中心-合規性](https://www.microsoft.com/trustcenter/compliance/complianceofferings) -microsoft 針對雲端服務的一組完整合規性供應專案

* [如何執行風險評定的指引](https://www.microsoft.com/trustcenter/guidance/risk-assessment) - 管理 Microsoft 雲端服務的安全性與合規性需求

### <a name="other-microsoft-online-services"></a>其他 Microsoft Online Services

* [Microsoft Intune 安全性](https://www.microsoft.com/trustcenter/security/intune-security) – Intune 可從雲端提供行動裝置管理、行動應用程式管理，和電腦管理能力。

* [Microsoft Dynamics 365 安全性](https://www.microsoft.com/trustcenter/security/dynamics365-security) – Dynamics 365 是 Microsoft 雲端式解決方案，可統合客戶關係管理 (CRM) 與企業資源規劃 (ERP) 功能。