---
title: Azure 活動目錄常規操作指南參考
description: 此操作參考指南描述為確保常規操作而應採取的檢查和操作
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422954"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Azure 活動目錄常規操作指南參考

Azure AD[操作參考指南](active-directory-ops-guide-intro.md)的這一部分介紹了為優化 Azure 活動目錄 （Azure AD） 的一般操作而應執行的檢查和操作。

> [!NOTE]
> 這些建議自發布之日起是最新的，但可能會隨時間而變化。 隨著 Microsoft 產品和服務隨時間推移而變化，組織應持續評估其運營實踐。

## <a name="key-operational-processes"></a>關鍵操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者分配給要徑任務

管理 Azure 活動目錄需要持續執行關鍵操作任務和進程，這些任務和進程可能不是推出專案的一部分。 設置這些任務以優化環境仍然很重要。 要徑任務及其推薦的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 推動對身份安全分數的改進 | InfoSec 運營團隊 |
| 維護 Azure AD 連接伺服器 | IAM 運營團隊 |
| 定期執行和會審 IdFix 報告 | IAM 運營團隊 |
| 分診 Azure AD 連接同步和 AD FS 的運行狀況警報 | IAM 運營團隊 |
| 如果不使用 Azure AD 連接運行狀況，則客戶具有等效的過程和工具來監視自訂基礎結構 | IAM 運營團隊 |
| 如果不使用 AD FS，則客戶具有監視自訂基礎結構的等效流程和工具 | IAM 運營團隊 |
| 監視混合日誌：Azure AD 應用代理連接器 | IAM 運營團隊 |
| 監視混合日誌：傳遞身份驗證代理 | IAM 運營團隊 |
| 監視混合日誌：密碼回寫服務 | IAM 運營團隊 |
| 監控混合日誌：本地密碼保護閘道 | IAM 運營團隊 |
| 監視混合日誌：Azure MFA NPS 擴展（如果適用） | IAM 運營團隊 |

在查看清單時，您可能會發現需要為缺少擁有者的任務分配擁有者，或者調整擁有者與上述建議不一致的任務的擁有權。

#### <a name="owners-recommended-reading"></a>業主推薦閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>混合管理

### <a name="recent-versions-of-on-premises-components"></a>本機群組件的最新版本

擁有最新版本的本機群組件可為客戶提供所有最新的安全更新、性能改進以及有助於進一步簡化環境的功能。 大多陣列件都有自動升級設置，這將自動執行升級過程。

這些元件包括：

- Azure AD Connect
- Azure AD 應用程式 Proxy 連接器
- Azure AD 直通身份驗證代理
- Azure AD 連接運行狀況代理

除非已經建立，否則應定義升級這些元件的過程，並盡可能依賴自動升級功能。 如果發現元件落後六個月或更長時間，應儘快升級。

#### <a name="hybrid-management-recommended-reading"></a>混合管理推薦閱讀

- [Azure AD Connect：自動升級](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [瞭解 Azure AD 應用程式代理連接器 |自動更新](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Azure AD 連接運行狀況警報基線

組織應部署[Azure AD 連接運行狀況](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health)，以監視和報告 Azure AD 連接和 AD FS。 Azure AD 連接和 AD FS 是可能中斷生命週期管理和身份驗證並導致中斷的關鍵元件。 Azure AD 連接運行狀況可説明監視和獲取對本地標識基礎結構的見解，從而確保環境的可靠性。

![Azure AD 連接希思架構](./media/active-directory-ops-guide/active-directory-ops-img16.png)

在監視環境運行狀況時，必須立即解決任何高嚴重性警報，然後是較低的嚴重性警報。

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD 連接運行狀況建議讀取

- [Azure AD Connect Health 代理程式安裝](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>本地代理日誌

某些標識和訪問管理服務需要本地代理才能啟用混合方案。 示例包括密碼重設、直通身份驗證 （PTA）、Azure AD 應用程式代理和 Azure MFA NPS 擴展。 操作團隊必須使用系統中心操作管理器或 SIEM 等解決方案存檔和分析元件代理日誌，從而對這些元件的運行狀況進行基線和監視。 同樣重要的是，您的 Infosec 運營團隊或説明台瞭解如何排除錯誤模式的故障。

#### <a name="on-premises-agents-logs-recommended-reading"></a>本地代理記錄建議讀取

- [疑難排解應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [自助式密碼重設疑難排解 - Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [瞭解 Azure AD 應用程式代理連接器](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD 連接：故障排除直通身份驗證](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [對 Azure MFA NPS 擴展的錯誤代碼進行故障排除](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>本地代理管理

採用最佳實踐有助於本地代理的最佳操作。 請考慮以下最佳實踐：

- 建議每個連接器組使用多個 Azure AD 應用程式代理連接器，通過避免訪問代理應用程式時的單點故障來提供無縫的負載平衡和高可用性。 如果連接器組中目前只有一個連接器來處理生產中的應用程式，則應至少部署兩個連接器以進行冗余。
- 為調試目的創建和使用應用代理連接器組可用於故障排除方案和在加入新的本地應用程式時。 我們還建議在連接器電腦中安裝網路工具，如消息分析器和 Fiddler。
- 建議使用多個直通身份驗證代理，通過避免身份驗證流期間的單點故障來提供無縫的負載平衡和高可用性。 請務必至少部署兩個傳遞身份驗證代理以進行冗余。

#### <a name="on-premises-agents-management-recommended-reading"></a>本地代理管理建議讀取

- [瞭解 Azure AD 應用程式代理連接器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Azure AD 直通身份驗證 - 快速入門](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>規模管理

### <a name="identity-secure-score"></a>身分識別安全分數

[標識安全分數](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)提供了組織安全狀態的可量化度量。 不斷審查和處理報告的調查結果，並努力獲得盡可能的最高分數是關鍵。 此分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

![安全分數](./media/active-directory-ops-guide/active-directory-ops-img17.png)

如果您的組織當前沒有計劃來監視身份安全分數中的更改，建議您實施計畫並分配擁有者來監視和推動改進操作。 組織應儘快修復分數影響高於 30 的改進操作。

### <a name="notifications"></a>通知

Microsoft 向管理員發送電子郵件通信，以通知服務中的各種更改、所需的配置更新以及需要管理員干預的錯誤。 客戶必須設置通知電子郵件地址，以便通知發送給能夠確認並對所有通知執行操作的適當團隊成員。 我們建議您將多個收件者添加到[Office 365 郵件中心](https://docs.microsoft.com/office365/admin/manage/message-center)，並要求將通知（包括 Azure AD 連接運行狀況通知）發送到通訊群組清單或共用郵箱。 如果您只有一個具有電子郵件地址的全域管理員帳戶，請確保配置至少兩個支援電子郵件的帳戶。

Azure AD 使用兩個"From"位址：，<o365mc@email2.microsoft.com>它發送 Office 365 消息中心通知;和<azure-noreply@microsoft.com>， 發送與：

- [Azure AD 存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD 標識保護](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD 特殊權限身分識別管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [企業應用程式過期憑證通知](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- 企業應用程式佈建服務通知

請參閱下表以瞭解發送的通知類型以及檢查位置：

| 通知源 | 發送的內容 | 在哪裡檢查 |
|:-|:-|:-|
| 技術連絡人 | 同步錯誤 | Azure 門戶 - 屬性邊欄選項卡 |
| Office 365 訊息中心 | 身份服務和 O365 後端服務的事故和降級通知 | 辦公室門戶 |
| 身份保護每週摘要 | 身份保護文摘 | Azure AD 標識保護邊欄選項卡 |
| Azure AD Connect Health | 警示通知 | Azure 門戶 - Azure AD 連接運行狀況邊欄選項卡 |
| 企業代理程式更新 | 證書即將過期和預配錯誤的通知 | Azure 門戶 - 企業應用程式邊欄選項卡（每個應用都有自己的電子郵件地址設置） |

#### <a name="notifications-recommended-reading"></a>通知建議閱讀

- [更改組織的位址、技術連絡人等 - Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>動作表面積

### <a name="ad-fs-lockdown"></a>AD FS 鎖定

組織，它們配置應用程式以直接從 Azure AD 進行身份驗證，這些好處來自 [Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)。 如果在 Windows 伺服器 2012 R2 中使用 AD FS，則實施 AD FS [外網鎖定保護](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)。 如果您在 Windows 伺服器 2016 或更高版本中使用 AD FS，則實施 [外聯網智慧鎖定](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)。 至少，我們建議您啟用外網鎖定，以包含對本地活動目錄進行暴力攻擊的風險。 但是，如果您在 Windows 2016 或更高版本中具有 AD FS，則還應啟用外聯網智慧鎖定，這將有助於緩解[密碼噴射](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)攻擊。

如果 AD FS 僅用於 Azure AD 聯合，則可以關閉一些終結點以最小化攻擊表面積。 例如，如果 AD FS 僅用於 Azure AD，則應禁用 WS-Trust 終結點，而不是為**使用者名混合**和**視窗傳輸**啟用的終結點。

### <a name="access-to-machines-with-on-premises-identity-components"></a>訪問具有本地標識元件的電腦

組織應鎖定對具有本地混合元件的電腦的訪問，其方式與本地域相同。 例如，備份操作員或 Hyper-V 管理員不應能夠登錄到 Azure AD 連接伺服器以更改規則。

Active Directory 管理層模型旨在使用一組緩衝區保護標識系統，這些緩衝區介於對環境的完全控制 （第 0 層）和攻擊者經常危害的高風險工作站資產之間。 ![此圖顯示階層模型的三個層級](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[層模型](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)由三個級別組成，僅包括管理帳戶，不包括標準使用者帳戶。

- **第 0** 層 - 直接控制環境中的企業標識。 第 0 層包括可直接或間接對 Active Directory 樹系、網域或網域控制站進行系統管理控制的帳戶、群組和其他資產及其上的所有資產。 所有第 0 層資產的安全性敏感度相當於它們全都在彼此的有效控制之下。
- **第 1** 層 - 控制企業伺服器和應用程式。 第 1 層資產包括伺服器作業系統、雲端服務和企業應用程式。 第 1 層系統管理員帳戶對於這些資產上裝載的大量商業價值具有系統管理控制權。 常見的範例角色是伺服器系統管理員，其可利用會影響所有企業服務的能力來維護這些作業系統。
- **第 2** 層 - 控制使用者工作站和設備。 第 2 層系統管理員帳戶對於使用者工作站和裝上裝載的大量商業價值具有系統管理控制權。 範例包括技術支援中心及電腦支援系統管理員，因為他們會影響到幾乎所有使用者資料的完整性。

鎖定對本地標識元件（如 Azure AD 連接、AD FS 和 SQL 服務）的訪問，與網域控制站的訪問相同。

## <a name="summary"></a>總結

安全標識基礎結構有七個方面。 此清單將説明您找到為優化 Azure 活動目錄 （Azure AD） 的操作而應執行的操作。

- 將擁有者分配給要徑任務。
- 自動執行本地混合元件的升級過程。
- 部署 Azure AD 連接運行狀況，用於監視和報告 Azure AD 連接和 AD FS。
- 使用系統中心操作管理器或 SIEM 解決方案存檔和分析元件代理日誌，從而監視本地混合元件的運行狀況。
- 使用身份安全分數測量安全狀態，實現安全改進。
- 鎖定 AD FS。
- 鎖定對具有本地標識元件的電腦的訪問。

## <a name="next-steps"></a>後續步驟

有關尚未部署的任何功能的實現詳細資訊，請參閱[Azure AD 部署計畫](active-directory-deployment-plans.md)。
