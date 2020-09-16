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
ms.openlocfilehash: 921a56dca8f1cda67e6f32458914fef4ac2d324c
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601297"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure Active Directory 一般操作指南參考

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明將 Azure Active Directory (Azure AD) 的一般作業優化所應採取的檢查和動作。

> [!NOTE]
> 這些建議是從發行日期開始的最新狀態，但可能會隨著時間而改變。 當 Microsoft 產品和服務隨著時間演進時，組織應該持續評估其操作實務。

## <a name="key-operational-processes"></a>重要的操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給重要工作

管理 Azure Active Directory 需要持續執行重要的作業工作和進程，這可能不是首度發行專案的一部分。 您也必須設定這些工作來優化您的環境。 主要工作和其建議的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 提高身分識別安全分數的磁片磁碟機 | InfoSec 作業小組 |
| 維護 Azure AD Connect 伺服器 | IAM 作業小組 |
| 定期執行和分級 IdFix 報表 | IAM 作業小組 |
| Azure AD Connect Health 警示同步與 AD FS 分級 | IAM 作業小組 |
| 如果未使用 Azure AD Connect Health，則客戶有對等的程式和工具可監視自訂基礎結構 | IAM 作業小組 |
| 如果未使用 AD FS，則客戶有對等的程式和工具可監視自訂基礎結構 | IAM 作業小組 |
| 監視混合式記錄： Azure AD App Proxy 連接器 | IAM 作業小組 |
| 監視混合式記錄：傳遞驗證代理程式 | IAM 作業小組 |
| 監視混合式記錄：密碼回寫服務 | IAM 作業小組 |
| 監視混合式記錄：內部部署密碼保護閘道 | IAM 作業小組 |
| 監視混合式記錄： Azure MFA NPS 擴充功能 (適用)  | IAM 作業小組 |

當您複習清單時，可能會發現您需要為缺少擁有者的工作指派擁有者，或針對擁有者未與上述建議一致的工作，調整其擁有權。

#### <a name="owners-recommended-reading"></a>擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)
- [Azure 中的治理](../../governance/index.yml)

## <a name="hybrid-management"></a>混合式管理

### <a name="recent-versions-of-on-premises-components"></a>內部部署元件的最新版本

擁有最新版本的內部部署元件，可為客戶提供最新的安全性更新、效能改進，以及有助於進一步簡化環境的功能。 大部分的元件都有自動升級設定，可將升級程式自動化。

這些元件包括：

- Azure AD Connect
- Azure AD 應用程式 Proxy 連接器
- Azure AD 傳遞驗證代理程式
- Azure AD Connect Health 代理程式

除非已建立一個，否則您應定義一個程式來升級這些元件，並盡可能依賴自動升級功能。 如果您找到六個或更多個月後的元件，您應該儘快升級。

#### <a name="hybrid-management-recommended-reading"></a>建議閱讀混合式管理

- [Azure AD Connect：自動升級](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [瞭解 Azure AD 應用程式 Proxy 連接器 |自動更新](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD Connect Health 警示基準

組織應部署 [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) ，以進行 Azure AD Connect 和 AD FS 的監視和報告。 Azure AD Connect 和 AD FS 是可破壞生命週期管理和驗證的重要元件，因而導致中斷。 Azure AD Connect Health 有助於監視及深入瞭解您的內部部署身分識別基礎結構，進而確保您環境的可靠性。

![Azure AD Connect Heath 架構](./media/active-directory-ops-guide/active-directory-ops-img16.png)

當您監視環境的健全狀況時，您必須立即解決任何高嚴重性警示，後面接著較低嚴重性警示。

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health 建議閱讀

- [Azure AD Connect Health 代理程式安裝](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>內部部署代理程式記錄檔

某些身分識別和存取管理服務需要內部部署代理程式，才能啟用混合式案例。 範例包括密碼重設、傳遞驗證 (PTA) 、Azure AD 應用程式 Proxy，以及 Azure MFA NPS 延伸模組。 這是作業小組使用 System Center Operations Manager 或 SIEM 等解決方案來封存和分析元件代理程式記錄，以將這些元件的健康情況進行基準和監視的關鍵。 您的 Infosec 作業小組或技術服務人員也必須瞭解如何針對錯誤模式進行疑難排解。

#### <a name="on-premises-agents-logs-recommended-reading"></a>內部部署代理程式記錄建議的讀取

- [疑難排解應用程式 Proxy](../manage-apps/application-proxy-troubleshoot.md)
- [自助式密碼重設疑難排解 - Azure Active Directory](../authentication/active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes)
- [了解 Azure AD 應用程式 Proxy 連接器](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect：針對傳遞驗證進行疑難排解](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [針對 Azure MFA NPS 擴充功能的錯誤碼進行疑難排解](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>內部部署代理程式管理

採用最佳作法可協助您進行內部部署代理程式的最佳操作。 請考慮下列最佳做法：

- 建議每個連接器群組使用多個 Azure AD 應用程式 proxy 連接器，藉由避免存取 proxy 應用程式時的單一失敗點，提供順暢的負載平衡和高可用性。 如果在生產環境中處理應用程式的連接器群組中目前只有一個連接器，則應該部署至少兩個連接器來進行冗余。
- 建立和使用應用程式 proxy 連接器群組以進行偵測，對於疑難排解案例以及將新的內部部署應用程式上線時，都很有用。 我們也建議您在連接器機器中安裝網路工具，例如 Message Analyzer 和 Fiddler。
- 建議使用多個傳遞驗證代理程式，藉由避免驗證流程中的單一失敗點來提供順暢的負載平衡和高可用性。 請務必部署至少兩個傳遞驗證代理程式以進行冗余。

#### <a name="on-premises-agents-management-recommended-reading"></a>內部部署代理程式管理建議的讀取

- [了解 Azure AD 應用程式 Proxy 連接器](../manage-apps/application-proxy-connectors.md)
- [Azure AD 傳遞驗證-快速入門](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>大規模管理

### <a name="identity-secure-score"></a>身分識別安全分數

身分 [識別安全分數](./identity-secure-score.md) 提供組織安全性狀態的可量化量值。 您可以不斷地審視並解決回報的結果，並努力獲得最高的分數。 此分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

![安全分數](./media/active-directory-ops-guide/active-directory-ops-img17.png)

如果您的組織目前沒有任何程式可監視身分識別安全分數的變更，建議您執行方案並指派擁有者來監視和驅動改進動作。 組織應該儘快補救分數影響高於30的改進動作。

### <a name="notifications"></a>通知

Microsoft 會將電子郵件通訊傳送給系統管理員，以通知服務的各種變更、需要的設定更新，以及需要系統管理員介入的錯誤。 客戶必須設定通知電子郵件地址，以便將通知傳送給適當的小組成員，這些成員可以在所有通知中進行確認並採取行動。 建議您將多個收件者新增至 [訊息中心](/office365/admin/manage/message-center) 並要求通知 (包括 Azure AD Connect Health 通知，) 傳送至通訊群組清單或共用信箱。 如果您只有一個具有電子郵件地址的全域管理員帳戶，請務必至少設定兩個具備電子郵件功能的帳戶。

Azure AD：會使用兩個「寄件者」位址 <o365mc@email2.microsoft.com> ，它會傳送訊息中心通知; 以及傳送與 <azure-noreply@microsoft.com> 下列相關的通知：

- [Azure AD 存取權評論](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD 特殊權限身分識別管理](../privileged-identity-management/pim-email-notifications.md)
- [企業應用程式過期憑證通知](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- 企業應用程式佈建服務通知

請參閱下表，以瞭解傳送的通知類型以及要檢查的位置：

| 通知來源 | 傳送內容 | 檢查的位置 |
|:-|:-|:-|
| 技術連絡人 | 同步錯誤 | Azure 入口網站屬性分頁 |
| 訊息中心 | 身分識別服務和 Microsoft 365 後端服務的事件和降低通知 | Office 入口網站 |
| Identity Protection 每週摘要 | 身分識別保護摘要 | Azure AD Identity Protection 分頁 |
| Azure AD Connect Health | 警示通知 | Azure 入口網站-Azure AD Connect Health blade |
| 企業代理程式更新 | 憑證即將到期和布建錯誤時的通知 | Azure 入口網站-企業應用程式 blade (每個應用程式都有自己的電子郵件地址設定)  |

#### <a name="notifications-recommended-reading"></a>建議閱讀的通知

- [變更您組織的位址、技術連絡人及其他資訊](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>操作介面區

### <a name="ad-fs-lockdown"></a>AD FS 鎖定

組織會將應用程式設定為直接驗證 Azure AD 受益于 [Azure AD 智慧鎖定](../authentication/concept-sspr-howitworks.md)。 如果您在 Windows Server 2012 R2 中使用 AD FS，請執行 AD FS 的 [外部網路鎖定保護](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)。 如果您使用 Windows Server 2016 或更新版本上的 AD FS，請執行 [外部網路智慧鎖定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)。 建議您至少啟用外部網路鎖定，以包含對內部部署 Active Directory 進行暴力密碼破解攻擊的風險。 但是，如果您已在 Windows 2016 或更高版本中 AD FS，您也應該啟用外部網路智慧鎖定，以協助降低 [密碼噴灑](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) 攻擊。

如果 AD FS 只用于 Azure AD 同盟，則有一些端點可關閉，以將受攻擊面區域降至最低。 例如，如果 AD FS 只用于 Azure AD，您應該停用已啟用 **usernamemixed** 和 **windowstransport**之端點以外的 ws-trust 端點。

### <a name="access-to-machines-with-on-premises-identity-components"></a>使用內部部署身分識別元件存取機器

組織應以與內部部署網域相同的方式，鎖定具有內部部署混合式元件的電腦存取權。 例如，備份操作員或 Hyper-v 系統管理員應該無法登入 Azure AD Connect 伺服器來變更規則。

Active Directory 管理層模型的設計目的，是要使用一組緩衝區區域保護身分識別系統， (第0層) 的環境，以及攻擊者經常入侵的高風險工作站資產。 ![此圖顯示階層模型的三個層級](./media/active-directory-ops-guide/active-directory-ops-img18.png)

階層 [模型](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) 是由三個層級所組成，而且只包含系統管理帳戶，而不包含標準使用者帳戶。

- **第0層**  -直接控制環境中的企業身分識別。 第 0 層包括可直接或間接對 Active Directory 樹系、網域或網域控制站進行系統管理控制的帳戶、群組和其他資產及其上的所有資產。 所有第 0 層資產的安全性敏感度相當於它們全都在彼此的有效控制之下。
- **第1層**  -企業伺服器和應用程式的控制。 第 1 層資產包括伺服器作業系統、雲端服務和企業應用程式。 第 1 層系統管理員帳戶對於這些資產上裝載的大量商業價值具有系統管理控制權。 常見的範例角色是伺服器系統管理員，其可利用會影響所有企業服務的能力來維護這些作業系統。
- **第2層**  -控制使用者工作站和裝置。 第 2 層系統管理員帳戶對於使用者工作站和裝上裝載的大量商業價值具有系統管理控制權。 範例包括技術支援中心及電腦支援系統管理員，因為他們會影響到幾乎所有使用者資料的完整性。

鎖定對內部部署身分識別元件的存取，例如 Azure AD Connect、AD FS 和 SQL 服務，其方式與網域控制站相同。

## <a name="summary"></a>[摘要]

安全的身分識別基礎結構有七個層面。 這份清單將協助您找出 Azure Active Directory (Azure AD) 的作業優化所應採取的動作。

- 將擁有者指派給主要工作。
- 將內部部署混合式元件的升級程式自動化。
- 部署 Azure AD Connect Health，以進行 Azure AD Connect 和 AD FS 的監視和報告。
- 使用 System Center Operations Manager 或 SIEM 解決方案來封存和分析元件代理程式記錄，以監視內部部署混合式元件的健康情況。
- 藉由使用身分識別安全分數來測量安全性狀態，以實行安全性改進。
- 鎖定 AD FS。
- 鎖定具有內部部署身分識別元件的電腦存取權。

## <a name="next-steps"></a>後續步驟

請參閱 [Azure AD 部署計畫](active-directory-deployment-plans.md) ，以取得您尚未部署的任何功能的執行詳細資料。