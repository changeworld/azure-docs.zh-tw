---
title: Azure Active Directory 一般操作指南參考
description: 此操作參考指南說明保護一般作業時應採取的檢查和動作
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "75422954"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 一般操作指南參考

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明將 Azure Active Directory （Azure AD）的一般作業優化時，應採取的檢查和動作。

> [!NOTE]
> 這些建議是在發行日期之後的最新版本，但可能會隨著時間而改變。 組織應持續評估其營運實務，因為 Microsoft 產品和服務會隨著時間而演進。

## <a name="key-operational-processes"></a>關鍵操作程式

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給主要工作

管理 Azure Active Directory 需要持續執行重要的作業工作和進程，這可能不是首度發行專案的一部分。 您必須設定這些工作來優化您的環境，這仍然很重要。 主要工作和其建議的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 促進身分識別安全分數的改善 | InfoSec 營運小組 |
| 維護 Azure AD Connect 伺服器 | IAM 作業小組 |
| 定期執行 IdFix 報表並進行分級 | IAM 作業小組 |
| Azure AD Connect Health 警示同步處理和 AD FS 的分級 | IAM 作業小組 |
| 如果未使用 Azure AD Connect Health，則客戶具有對等的進程和工具來監視自訂基礎結構 | IAM 作業小組 |
| 如果未使用 AD FS，則客戶具有對等的進程和工具來監視自訂基礎結構 | IAM 作業小組 |
| 監視混合式記錄： Azure AD App Proxy 連接器 | IAM 作業小組 |
| 監視混合式記錄：傳遞驗證代理程式 | IAM 作業小組 |
| 監視混合式記錄：密碼回寫服務 | IAM 作業小組 |
| 監視混合式記錄：內部部署密碼保護閘道 | IAM 作業小組 |
| 監視混合式記錄： Azure MFA NPS 擴充功能（如果適用） | IAM 作業小組 |

當您檢查清單時，您可能會發現需要為遺漏擁有者的工作指派擁有者，或為擁有者未與上述建議一致的工作調整擁有權。

#### <a name="owners-recommended-reading"></a>擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>混合式管理

### <a name="recent-versions-of-on-premises-components"></a>最新版本的內部部署元件

擁有最新版本的內部部署元件可為客戶提供最新的安全性更新、效能改進，以及有助於進一步簡化環境的功能。 大部分元件都有自動升級設定，這會自動化升級程式。

這些元件包括：

- Azure AD Connect
- Azure AD 應用程式 Proxy 連接器
- Azure AD 傳遞驗證代理程式
- Azure AD Connect Health 代理程式

除非已建立一個，否則您應該定義一個程式來升級這些元件，並盡可能依賴自動升級功能。 如果您找到六個或更多個月後的元件，您應該儘快升級。

#### <a name="hybrid-management-recommended-reading"></a>混合式管理建議閱讀

- [Azure AD Connect：自動升級](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [瞭解 Azure AD 應用程式 Proxy 連接器 |自動更新](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health 警示基準

組織應部署[Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) ，以進行 Azure AD Connect 和 AD FS 的監視和報告。 Azure AD Connect 和 AD FS 是可中斷生命週期管理和驗證，因而導致中斷的重要元件。 Azure AD Connect Health 有助於監視並深入瞭解您的內部部署身分識別基礎結構，進而確保環境的可靠性。

![Azure AD Connect Heath 架構](./media/active-directory-ops-guide/active-directory-ops-img16.png)

當您監視環境的健康情況時，您必須立即解決任何高嚴重性警示，後面接著嚴重性較低的警示。

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health 建議閱讀

- [Azure AD Connect Health 代理程式安裝](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>內部部署代理程式記錄檔

某些身分識別和存取管理服務需要內部部署代理程式來啟用混合式案例。 範例包括密碼重設、傳遞驗證（PTA）、Azure AD 應用程式 Proxy 和 Azure MFA NPS 延伸模組。 這是作業小組使用 System Center Operations Manager 或 SIEM 之類的解決方案來封存和分析元件代理程式記錄，來基準和監視這些元件之健全狀況的關鍵。 同樣的，您的 Infosec 營運小組或服務台也必須瞭解如何針對錯誤模式進行疑難排解。

#### <a name="on-premises-agents-logs-recommended-reading"></a>內部部署代理程式記錄建議的讀取

- [疑難排解應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [自助式密碼重設疑難排解 - Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [了解 Azure AD 應用程式 Proxy 連接器](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect：針對傳遞驗證進行疑難排解](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [針對 Azure MFA NPS 擴充功能的錯誤碼進行疑難排解](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>內部部署代理程式管理

採用最佳作法可協助內部部署代理程式的最佳操作。 請考慮下列最佳做法：

- 建議每個連接器群組使用多個 Azure AD 應用程式 proxy 連接器，藉由避免在存取 proxy 應用程式時出現單一失敗點，以提供順暢的負載平衡和高可用性。 如果您目前在連接器群組中只有一個連接器會處理生產環境中的應用程式，您應該部署至少兩個連接器來進行冗余。
- 建立和使用應用程式 proxy 連接器群組以進行偵錯工具，對於疑難排解案例和登入新的內部部署應用程式很有用。 我們也建議您在連接器機器中安裝網路工具，例如 Message Analyzer 和 Fiddler。
- 建議使用多個傳遞驗證代理程式，藉由避免在驗證流程期間發生單一失敗點，以提供順暢的負載平衡和高可用性。 請務必部署至少兩個傳遞驗證代理程式以進行冗余。

#### <a name="on-premises-agents-management-recommended-reading"></a>內部部署代理程式管理建議閱讀

- [了解 Azure AD 應用程式 Proxy 連接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD 傳遞驗證-快速入門](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>大規模管理

### <a name="identity-secure-score"></a>身分識別安全分數

身分[識別安全分數](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)提供可量化的組織安全性狀態量值。 這是持續審查和解決報告結果，並努力盡可能獲得最高分的關鍵。 此分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

![安全分數](./media/active-directory-ops-guide/active-directory-ops-img17.png)

如果您的組織目前沒有任何程式可監視身分識別安全分數的變更，建議您執行計畫，並指派擁有者來監視和驅動改進動作。 組織應儘快補救具有高於30的分數影響的改進動作。

### <a name="notifications"></a>通知

Microsoft 會將電子郵件通訊傳送給系統管理員，以通知服務中的各種變更、所需的設定更新，以及需要系統管理員介入的錯誤。 客戶必須設定通知電子郵件地址，以便將通知傳送給適當的小組成員，讓他們能夠對所有通知進行認可並採取行動。 建議您將多個收件者新增至[Office 365 訊息中心](https://docs.microsoft.com/office365/admin/manage/message-center)，並要求將通知（包括 Azure AD Connect Health 通知）傳送到通訊群組清單或共用信箱。 如果您只有一個具有電子郵件地址的全域系統管理員帳戶，請務必設定至少兩個具備電子郵件功能的帳戶。

Azure AD：會使用兩個「寄件者」位址 <o365mc@email2.microsoft.com> ，它會傳送 Office 365 訊息中心通知; 和，它會傳送與 <azure-noreply@microsoft.com> 下列內容相關的通知：

- [Azure AD 存取評論](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD 特殊權限身分識別管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [企業應用程式過期憑證通知](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- 企業應用程式佈建服務通知

請參閱下表以瞭解傳送的通知類型，以及檢查它們的位置：

| 通知來源 | 傳送的內容 | 檢查位置 |
|:-|:-|:-|
| 技術連絡人 | 同步錯誤 | Azure 入口網站-屬性分頁 |
| Office 365 訊息中心 | 身分識別服務和 O365 後端服務的事件和降低通知 | Office 入口網站 |
| Identity Protection 每週摘要 | 身分識別保護摘要 | Azure AD Identity Protection 分頁 |
| Azure AD Connect Health | 警示通知 | Azure 入口網站 Azure AD Connect Health 分頁 |
| 企業代理程式更新 | 憑證即將到期和布建錯誤時的通知 | Azure 入口網站-企業應用程式分頁（每個應用程式都有自己的電子郵件地址設定） |

#### <a name="notifications-recommended-reading"></a>建議的閱讀通知

- [變更您組織的位址、技術連絡人及更多 Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>操作介面區

### <a name="ad-fs-lockdown"></a>AD FS 鎖定

組織會將應用程式設定為直接驗證，Azure AD 從 [Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)獲益。 如果您使用 Windows Server 2012 R2 中的 AD FS，請執行 AD FS [外部網路鎖定保護](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)。 如果您使用 Windows Server 2016 或更新版本上的 AD FS，請執行 [外部網路智慧鎖定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)。 建議您至少啟用外部網路鎖定，以包含對內部部署 Active Directory 進行暴力密碼破解攻擊的風險。 不過，如果您在 Windows 2016 或更新版本中有 AD FS，您也應該啟用外部網路智慧鎖定，協助減輕[密碼噴灑](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)攻擊。

如果 AD FS 僅用於 Azure AD 同盟，則可以關閉一些端點，將受攻擊面區域降至最低。 例如，如果 AD FS 僅用於 Azure AD，您應該停用 WS-TRUST 端點，而不是啟用**usernamemixed**和**windowstransport**的端點。

### <a name="access-to-machines-with-on-premises-identity-components"></a>存取具有內部部署身分識別元件的電腦

組織應該以與內部部署網域相同的方式，鎖定具有內部部署混合式元件的電腦存取權。 例如，備份操作員或 Hyper-v 系統管理員不應能夠登入 Azure AD Connect 伺服器來變更規則。

Active Directory 系統管理層模型的設計目的是要使用一組緩衝區域來保護身分識別系統，而該環境會在完全控制環境（第0層）和攻擊者經常洩露的高風險工作站資產之間。 ![此圖顯示階層模型的三個層級](./media/active-directory-ops-guide/active-directory-ops-img18.png)

階層[模型](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)是由三個層級所組成，而且只包含系統管理帳戶，而不是標準使用者帳戶。

- **第0層**  -直接控制環境中的企業身分識別。 第 0 層包括可直接或間接對 Active Directory 樹系、網域或網域控制站進行系統管理控制的帳戶、群組和其他資產及其上的所有資產。 所有第 0 層資產的安全性敏感度相當於它們全都在彼此的有效控制之下。
- **第1層**  -控制企業伺服器和應用程式。 第 1 層資產包括伺服器作業系統、雲端服務和企業應用程式。 第 1 層系統管理員帳戶對於這些資產上裝載的大量商業價值具有系統管理控制權。 常見的範例角色是伺服器系統管理員，其可利用會影響所有企業服務的能力來維護這些作業系統。
- **第2層**  -控制使用者工作站和裝置。 第 2 層系統管理員帳戶對於使用者工作站和裝上裝載的大量商業價值具有系統管理控制權。 範例包括技術支援中心及電腦支援系統管理員，因為他們會影響到幾乎所有使用者資料的完整性。

鎖定內部部署身分識別元件（例如 Azure AD Connect、AD FS 和 SQL 服務）的存取方式，與網域控制站相同。

## <a name="summary"></a>摘要

安全身分識別基礎結構有七個層面。 這份清單可協助您找出您應該採取的動作，以將 Azure Active Directory （Azure AD）的作業優化。

- 將擁有者指派給主要工作。
- 將內部部署混合式元件的升級程式自動化。
- 部署 Azure AD Connect Health 以進行 Azure AD Connect 和 AD FS 的監視和報告。
- 藉由使用 System Center Operations Manager 或 SIEM 解決方案來封存和分析元件代理程式記錄，以監視內部部署混合式元件的健康情況。
- 藉由使用身分識別安全分數來測量您的安全性狀態，以執行安全性改進。
- 鎖定 AD FS。
- 鎖定具有內部部署身分識別元件之電腦的存取權。

## <a name="next-steps"></a>後續步驟

請參閱[Azure AD 部署計畫](active-directory-deployment-plans.md)，以取得您尚未部署之任何功能的執行詳細資料。
