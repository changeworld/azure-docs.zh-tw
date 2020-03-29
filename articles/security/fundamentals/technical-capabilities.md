---
title: Azure 中的安全技術能力 - 微軟 Azure
description: Azure 中安全服務簡介，可説明您保護雲中的資料、資源和應用程式。
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2019
ms.author: TomSh
ms.openlocfilehash: 61afad1d9994fd703bd8df047d1861baddeae997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845354"
---
# <a name="azure-security-technical-capabilities"></a>Azure 安全性技術功能
本文介紹了 Azure 中的安全服務，可説明您保護雲中的資料、資源和應用程式並滿足企業的安全需求。

## <a name="azure-platform"></a>Azure 平台

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 是一個由基礎結構和應用程式服務所組成的雲端平台，內含整合式資料服務與進階分析以及開發人員工具和服務，並裝載在 Microsoft 的公用雲端資料中心內。 客戶可以使用 Azure 來實現許多不同的功能和案例，從基本的計算、網路和儲存，到行動和 Web 應用程式服務，再到物聯網之類的完整雲端案例都是如此，並可搭配開放原始碼技術來使用，以及部署為混合式雲端或裝載於客戶的資料中心內。 Azure 以建置組塊的形式提供雲端技術，以協助公司行號節省成本、快速創新，以及主動管理系統。 當您建置 IT 資產或將其移轉至雲端提供者時，您可以依賴該組織的能力來保護您的應用程式和資料，並利用他們所提供的安全性控制來管理您雲端架構資產的安全性。

在所有雲端運算提供者中，只有 Microsoft Azure 會為各個團隊提供可在其不同的雲端技能集和專案複雜度層級內運作、安全且一致的應用程式平台和基礎結構即服務，其內含整合式資料服務和分析功能，不管資料是放在 Microsoft 還是非 Microsoft 平台，它都能從這些資料中挖掘出情報，Microsoft Azure 也內含開放式架構和工具，可供團隊選擇是要整合雲端與內部部署，還是要在內部部署資料中心內部署 Azure 雲端服務。 Azure 是 Microsoft 受信任雲端的一部分，因此客戶可以依賴 Azure 來獲得領先業界的安全性、可靠性、合規性、隱私權以及由眾多人員、合作夥伴和程序所構成的巨大網路，以在雲端環境中支援組織。

透過 Microsoft Azure，您可以：

- 加快雲端創新速度。

- 利用深入見解來支援商業決策和應用程式。

- 免費建置及隨處部署。

- 保護其業務。

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>安全技術能力，以履行您的責任

Microsoft Azure 提供的服務可説明您滿足安全性、隱私性和合規性需求。 下圖有助於解釋各種 Azure 服務，這些服務可用於基於行業標準構建安全且合規的應用程式基礎結構。

![可用的安全性技術功能 - 概略圖](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>管理和控制身份和使用者訪問

Azure 可讓您管理使用者的身分識別和認證並控制存取權，協助您保護商務及個人資訊。

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft 身份和訪問管理解決方案可説明 IT 保護對跨公司資料中心和雲的應用程式和資源的訪問，從而實現更多級別的驗證，如多重要素驗證和條件訪問政策。 透過進階的安全性報告、稽核和警示來監視可疑活動，有助於減緩潛在的安全性問題。 [Azure 活動目錄高級](../../active-directory/active-directory-whatis.md)版提供對數千個雲應用的單一登入和對本地運行的 Web 應用的訪問。

Azure Active Directory (Azure AD) 的安全性優點包括下列功能：

- 為您的混合式企業中的每位使用者建立和管理單一身分識別，讓使用者、群組和裝置保持同步。

- 提供您的應用程式 (包括數千個預先整合的 SaaS 應用程式) 的單一登入存取。

- 藉由同時對內部部署和雲端應用程式強制以規則為基礎的 Multi-Factor Authentication，啟用應用程式存取安全性。

- 透過 Azure AD 應用程式 Proxy 佈建對內部部署 Web 應用程式的安全遠端存取。

[Azure 活動目錄門戶](https://aad.portal.azure.com/)作為 Azure 門戶的一部分提供。 通過此儀表板，您可以獲取組織狀態的概述，並輕鬆管理目錄、使用者或應用程式訪問。

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

以下是核心 Azure 標識管理功能：

- 單一登入

- Multi-Factor Authentication

- 安全性監視、警示以及機器學習服務型報告

- 消費者身分識別與存取管理

- 裝置註冊

- Privileged Identity Management

- 身分識別保護

#### <a name="single-sign-on"></a>單一登入

[單一登入 （SSO）](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/)意味著只需使用單個使用者帳戶登錄一次，即可訪問開展業務所需的所有應用程式和資源。 登入之後，您可以存取所有需要的應用程式，而不需要再驗證一次 (例如，輸入密碼)。

許多組織依賴軟體即服務 （SaaS） 應用程式（如 Office 365、Box 和 Salesforce）來提高最終使用者的工作效率。 在過去，IT 人員必須在每個 SaaS 應用程式中個別建立並更新使用者帳戶，使用者則必須記住每個 SaaS 應用程式的密碼。

[Azure AD 將本地活動目錄擴展到雲](../../active-directory/manage-apps/what-is-single-sign-on.md)中，使使用者能夠使用其主組織帳戶，不僅登錄到其加入域的裝置管理公司資源，還可以登錄到作業所需的所有 Web 和 SaaS 應用程式。

不只使用者不需要管理多組使用者名稱和密碼，還可根據組織群組，以及其身為員工的狀態，自動佈建或解除佈建其應用程式的存取權。 [Azure AD 引入了安全和訪問治理控制項](../../active-directory/active-directory-enterprise-apps-manage-sso.md)，使您能夠跨 SaaS 應用程式集中管理使用者的存取權限。

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure 多重要素驗證 （MFA）](../../active-directory/authentication/multi-factor-authentication.md)是一種身份驗證方法，需要使用多個驗證方法，並且向使用者登錄和事務添加了關鍵的第二層安全性。 [MFA 有助於保護](../../active-directory/authentication/concept-mfa-howitworks.md)對資料和應用程式的訪問，同時滿足使用者對簡單登錄過程的需求。 它可以透過一些驗證選項 (例如電話、文字訊息，或行動應用程式通知或驗證代碼，以及第三方 OAuth 權杖) 來提供強大的驗證功能。

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>安全性監視、警示以及機器學習服務型報告

安全性監視和警示以及機器學習式報告，會識別不一致的存取模式，可以協助您保護企業安全。 您可以使用 Azure Active Directory 的存取和使用情況報告來了解貴組織的目錄完整性和安全性。 利用此資訊，目錄管理員更能夠判斷可能發生安全性風險的位置，以便適當地規劃來減輕這些風險。

在 Azure 入口網站或透過 [Azure Active Directory 入口網站](https://aad.portal.azure.com/)，以下列方式分類[報告](../../active-directory/active-directory-reporting-azure-portal.md)：

- 異常報告 – 包含我們發現異常的登入事件。 我們的目標在於使您注意這類活動，並讓您能夠決定事件是否可疑。

- 集成應用程式報告 – 深入瞭解組織中如何使用雲應用程式。 Azure Active Directory 提供與數千個雲端應用程式的整合。

- 錯誤報告 – 指出將帳戶佈建至外部應用程式時可能發生的錯誤。

- 特定于使用者的報告 – 顯示特定使用者的設備和登錄活動資料。

- 活動記錄 – 包含過去 24 小時、過去 7 天或過去 30 天內所有稽核事件的記錄，以及群組活動變更、密碼重設和登錄活動。

#### <a name="consumer-identity-and-access-management"></a>消費者身分識別與存取管理

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)是一種高度可用的全域身份管理服務，適用于面向消費者的應用程式，可擴展到數億個標識。 此服務可跨行動及 Web 平台進行整合。 可自訂的使用經驗讓您的消費者可以使用其現有的社交帳戶，或是建立新的認證來登入您所有的應用程式。

過去，想要[註冊並註冊消費者](../../active-directory-b2c/overview.md)的應用程式開發人員會編寫自己的代碼。 而且他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。 Azure Active Directory B2C 為您的組織提供更理想的做法，透過安全且以標準為基礎的平台以及更大的可延伸原則組合，協助將取用者身分識別管理整合至應用程式。

當您使用 Azure Active Directory B2C 時，您的取用者可以使用現有的社交帳戶 (Facebook、Google、Amazon、LinkedIn) 註冊應用程式，或是建立新的認證 (電子郵件地址與密碼，或使用者名稱與密碼)。

#### <a name="device-registration"></a>裝置註冊

[Azure AD 設備註冊](../../active-directory/devices/overview.md)是基於設備[的條件訪問](../../active-directory/devices/overview.md)方案的基礎。 當裝置已註冊時，Azure AD 裝置註冊會在使用者登入時，對裝置提供用來驗證裝置的身分識別。 然後，可以使用經過身份驗證的設備和設備的屬性來為雲和本地託管的應用程式強制實施條件訪問策略。

與[行動裝置管理 （MDM）](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft)解決方案（如 Intune）結合使用有關設備的其他資訊更新 Azure 活動目錄中的裝置屬性。 這允許您創建條件訪問規則，強制從設備進行訪問，以滿足您的安全和合規性標準。

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) 可讓您管理、控制和監視特殊權限身分識別，以及存取 Azure AD 和 Office 365 或 Microsoft Intune 等其他 Microsoft Online 服務的資源。

使用者有時候需要在 Azure 或 Office 365 資源或其他 SaaS 應用程式中執行特殊權限的作業。 這通常表示組織必須授與他們永久的 Azure AD 特殊權限存取權。 這會提高雲端資源的安全性風險，因為組織無法滴水不漏地監視這些使用者利用其管理員權限的所作所為。 此外，如果擁有特殊權限存取權的使用者帳戶遭到入侵，這個缺口可能會影響其整體的雲端安全性。 Azure AD 特殊權限身分識別管理有助於解決此風險。

Azure AD Privileged Identity Management 可讓您：

- 查看哪些使用者是 Azure AD 管理員

- 視需要啟用 Office 365 和 Intune 等 Microsoft Online Services 的 "just-in-time" 系統管理存取權限

- 取得有關系統管理員存取記錄與系統管理員指派變更的報告

- 取得有關特殊權限角色存取的警示

#### <a name="identity-protection"></a>身分識別保護

[Azure AD 標識保護](../../active-directory/identity-protection/overview.md)是一種安全服務，它提供風險檢測和影響組織身份的潛在漏洞的整合視圖。 標識保護使用現有的 Azure 活動目錄的異常檢測功能（可通過 Azure AD 的異常活動報告提供），並引入了新的風險檢測類型，可以即時檢測異常。

## <a name="secure-resource-access"></a>安全資源訪問

在 Azure 中從計費觀點著手的存取控制。 訪問[Azure 帳戶中心](https://account.windowsazure.com/subscriptions)訪問的 Azure 帳戶的擁有者是帳戶管理員 （AA）。 訂用帳戶是計費容器，但它們也可做為安全性界限：每個訂用帳戶都有一個服務管理員 (SA)，此管理員可以使用 Azure 入口網站來新增、移除及修改該訂用帳戶中的 Azure 資源。 新訂用帳戶的預設 SA 為 AA，但 AA 可以在 Azure 帳戶中心變更 SA。

![Azure 中受保護的資源存取](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

訂用帳戶也會與目錄產生關聯。 目錄會定義一組使用者。 他們可以是公司或學校中建立目錄的使用者，也可以是外部使用者 (也就是 Microsoft 帳戶)。 訂用帳戶可供已被指派為服務管理員 (SA) 或共同管理員 (CA) 的某些目錄使用者存取；唯一的例外就是，由於舊版原因，Microsoft 帳戶 (先前稱為 Windows Live ID) 不需存在目錄中，即可被指派為 SA 或 CA。

安全性導向公司應該將焦點放在提供員工所需的確切權限。 權限太多可能會讓帳戶暴露在攻擊者的威脅下。 權限太少則會讓員工無法有效率地完成工作。 [Azure 角色型存取控制 (RBAC)](../../role-based-access-control/overview.md) 可以為 Azure 提供更細緻的存取管理來協助解決這個問題。

![受保護的資源存取](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。 您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許執行特定的動作。 例如，使用 RBAC 讓一位員工管理某個訂用帳戶中的虛擬機器，而讓另一位員工管理相同訂用帳戶中的 SQL 資料庫。

![Azure 中受保護的資源存取 (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>資料安全性與加密

在雲端保護資料的其中一個關鍵是考慮您的資料可能會發生的狀態，以及哪些控制項適用於該狀態。 如要了解 Azure 資料安全性和加密最佳做法，相關建議將以下列資料的狀態為主。

- 待用︰這包括實體媒體 (磁碟或光碟) 上以靜態方式存在的所有資訊儲存物件、容器和類型。

- 傳輸中：當資料在元件、位置或程式之間傳輸時（例如通過網路、跨服務匯流排（從本地到雲，反之亦然，包括混合連接（如 ExpressRoute），或在輸入/輸出過程中，人們認為它是在運動。

### <a name="encryption-at-rest"></a>待用加密

若要實現待用加密，請執行下列每個動作：

支援至少一個下表詳述的建議加密模型來加密資料。

| 加密模型 |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| 伺服器加密 | 伺服器加密 | 伺服器加密 | 用戶端加密
| 使用服務管理的金鑰的伺服器端加密 | 在 Azure Key Vault 中使用客戶管理的金鑰的伺服器端加密 | 使用本地客戶託管金鑰的伺服器端加密 |
| • Azure 資源提供者執行加密和解密作業 <br> • Microsoft 管理金鑰 <br>• 完整的雲端功能 | • Azure 資源提供者執行加密和解密作業<br>• 客戶透過 Azure Key Vault 控制金鑰<br>• 完整的雲端功能 | • Azure 資源提供者執行加密和解密作業 <br>• 客戶控制本地金鑰 <br> • 完整的雲端功能| • Azure 服務無法查看解密的資料 <br>• 客戶將金鑰保留在內部部署環境 (或其他安全的存放區中)。 金鑰無法供 Azure 服務使用 <br>• 縮減的雲端功能|

### <a name="enabling-encryption-at-rest"></a>啟用待用加密

**找出資料的所有存放位置**

靜態加密的目標是加密所有資料。 這樣做可以避免遺失重要資料或所有存放位置。 列舉應用程式所儲存的所有資料。

> [!Note]
> 不只是「應用程式資料」或「PII」，而是所有與應用程式相關的資料，包括帳戶的中繼資料 (訂用帳戶對應、合約資訊、PII)。

請細想您使用了哪些存放區來儲存資料。 例如：

- 外部儲存體 (例如，SQL Azure、Document DB、HDInsights、Data Lake 等)

- 暫存儲存體 (任何包含租用戶資料的本機快取)

- 記憶體中快取 (可以置入分頁檔。)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>在 Azure 中利用現有的待用加密支援

對於您使用的每個存儲，利用現有的靜態加密支援。

- Azure 儲存體：請參閱[待用資料的 Azure 儲存體服務加密](../../storage/common/storage-service-encryption.md)。

- SQL Azure：請參閱[透明資料加密 (TDE)、SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)

- VM 與本機磁碟儲存體 ([Azure 磁碟加密](../azure-security-disk-encryption-overview.md))

對於支援使用 Azure 磁碟加密的 VM 和本機磁碟儲存體：

#### <a name="iaas"></a>IaaS

具有 IaaS VM (Windows 或 Linux) 的服務應使用 [Azure 磁碟加密](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx)來加密包含客戶資料的磁碟區。

#### <a name="paas-v2"></a>PaaS v2

在 PaaS v2 上執行且使用 Service Fabric 的服務可以使用適用於虛擬機器擴展集 [VMSS] 的 Azure 磁碟加密來加密其 PaaS v2 VM。

#### <a name="paas-v1"></a>PaaS v1

PaaS v1 目前不支援 Azure 磁碟加密。 因此，您必須使用應用程式層級加密來加密保存的待用資料。  這包括 (但不限於) 應用程式資料、暫存檔案、記錄和損毀傾印。

大部分的服務都應該會嘗試利用儲存體資源提供者的加密功能。 有些服務則必須進行明確加密，例如，保存的金鑰資料 (憑證、根金鑰/主要金鑰) 都必須儲存在 Key Vault。

如果您支援使用客戶管理的金鑰的服務端加密，您就必須讓客戶有辦法將金鑰拿給我們。 支援且建議的辦法是與 Azure Key Vault (AKV) 整合。 在此情況下，客戶就能在 Azure Key Vault 中新增及管理其金鑰。 客戶可以透過[開始使用 Key Vault](https://go.microsoft.com/fwlink/?linkid=521402) 來了解如何使用 AKV。

若要與 Azure Key Vault 整合，您必須新增程式碼以在需要解密時向 AKV 要求金鑰。

- 請參閱 [Azure Key Vault – 逐步解說](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/)來了解如何與 AKV 整合。

如果您支援客戶管理的金鑰，則需要提供 UX 給客戶以指定要使用哪個 Key Vault (或 Key Vault URI)。

由於待用加密涉及到主機、基礎結構和租用戶資料的加密，因此如果系統失敗或有惡意活動而導致金鑰遺失，就可能意味著所有加密資料也將跟著遺失。 因此，您所設計的待用加密解決方案務必要有完整的災害復原劇本，以便能從系統失敗和惡意活動中復原。

實現靜態加密的服務通常仍然容易受到加密金鑰或資料在主機磁碟機上未加密（例如，在主機作業系統的分頁檔中）的影響。因此，服務必須確保其服務的主機卷被加密。 為了做到這一點，計算團隊已啟用主機加密的部署，其會使用 [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) \(英文\) NKP 以及 DCM 服務與代理程式的擴充來加密主機磁碟區。

大部分服務會實作在標準 Azure VM 上。 這類服務應該會在計算團隊將其啟用時自動獲得[主機加密](../azure-security-disk-encryption-overview.md)。 在計算團隊管理的叢集中執行的服務，皆已在 Windows Server 2016 推出時自動啟用主機加密。

### <a name="encryption-in-transit"></a>傳輸中加密

保護傳輸中的資料應該是您的資料保護策略中不可或缺的部分。 由於資料會從許多位置來回移動，一般會建議您一律使用 SSL/TLS 通訊協定來交換不同位置的資料。 在某些情況下，建議您使用虛擬私人網路 (VPN)，隔離您的內部部署與雲端基礎結構之間的整個通訊通道。

對於在內部部署基礎結構與 Azure 之間移動的資料，您應該考慮適當的防護措施，例如 HTTPS 或 VPN。

對於需要從位於內部部署的多個工作站安全存取 Azure 的組織而言，請使用 [Azure 站對站 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)。

對於需要從位於內部部署的一個工作站安全存取 Azure 的組織而言，請使用[點對站 VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)。

較大的資料集可以透過專用的高速 WAN 連結 (例如 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)) 移動。 如果您選擇使用 ExpressRoute，您也可以使用 [SSL/TLS](https://support.microsoft.com/kb/257591) 或其他通訊協定，在應用程式層級加密資料，以提供額外的保護。

如果您透過 Azure 入口網站與 Azure 儲存體互動，則所有交易都會透過 HTTPS 發生。 透過 HTTPS 的[儲存體 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 也可用來與 [Azure 儲存體](https://azure.microsoft.com/services/storage/)和 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 互動。

未能保護傳輸中資料的組織更容易受到[中間人攻擊](https://technet.microsoft.com/library/gg195821.aspx)、[竊聽](https://technet.microsoft.com/library/gg195641.aspx)和會話劫持的影響。 這些攻擊可能是取得機密資料存取權的第一步。

若要深入了解 Azure VPN 選項，請閱讀[規劃與設計 VPN 閘道](../../vpn-gateway/vpn-gateway-about-vpngateways.md)一文。

### <a name="enforce-file-level-data-encryption"></a>強制執行檔案層級資料加密

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) 會使用加密、身分識別和授權原則，協助您保護檔案和電子郵件。 Azure RMS 可跨多個裝置運作 — 手機、平板電腦和 PC。保護您的組織內部和外部 因為 Azure RMS 新增資料所屬的保護層級，所以即使資料脫離您組織的範圍，這項功能仍然可行。

當您使用 Azure RMS 來保護檔案時，您要使用業界標準的密碼編譯搭配 [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) 的完整支援。 當您利用 Azure RMS 進行資料保護時，即使檔案被複製到不受 IT 控制的儲存體 (例如雲端儲存體服務)，也保證該檔案持續受到保護。 同樣的情況會出現在透過電子郵件共用的檔案，檔案會以電子郵件的附件形式受到保護，並提供如何開啟受保護附件的指示。
規劃 Azure RMS 採用時，建議執行下列作業︰

- 安裝 [RMS 共用應用程式](https://technet.microsoft.com/library/dn339006.aspx)。 此應用程式會藉由安裝 Office 增益集來與 Office 應用程式整合，讓使用者可以輕鬆地直接保護檔案。

- 設定應用程式和服務以支援 Azure RMS

- 建立可反映您的業務需求的[自訂範本](https://technet.microsoft.com/library/dn642472.aspx)。 例如︰最高秘密資料的範本應套用於所有最高機密相關的電子郵件。

[資料分類](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)和檔案保護能力不佳的組織可能更容易受資料外洩。 沒有適當的檔案保護，組織將無法取得商業見解、監督濫用情形，以及防止檔案被惡意存取。

> [!Note]
> 若要深入了解 Azure RMS，請閱讀[開始使用 Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx) 一文。

## <a name="secure-your-application"></a>保護您的應用程式
Azure 負責保護您的應用程式執行所在的基礎結構和平台，而保護您的應用程式本身是您的責任。 換句話說，您需要以安全的方式來開發、部署及管理您的應用程式程式碼和內容。 如果沒有這麼做，您的應用程式程式碼或內容仍然可能受到威脅。

### <a name="web-application-firewall"></a>Web 應用程式防火牆
[Web 應用程式防火牆 （WAF）](../../application-gateway/waf-overview.md)是[應用程式閘道](../../application-gateway/overview.md)的一項功能，它為您的 Web 應用程式提供集中保護，防止常見的漏洞和漏洞。

Web 應用程式防火牆會根據 [OWASP 核心規則集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 或 2.2.9 中的規則提供保護。 Web 應用程式已逐漸成為利用常見已知弱點的惡意攻擊目標。 這些攻擊中最常見的是 SQL 插入式攻擊、跨網站指令碼攻擊等等。 想要防止應用程式的程式碼受到這類攻擊會非常困難，而且可能需要對多層次的應用程式拓撲執行嚴格的維護、修補和監視工作。 集中式 Web 應用程式防火牆有助於簡化安全性管理作業，且更加確保應用程式管理員能夠對抗威脅或入侵。 相較於保護每個個別的 Web 應用程式，WAF 方案還可透過在中央位置修補已知弱點，更快地因應安全性威脅。 現有的應用程式閘道可以輕易地轉換成已啟用 Web 應用程式防火牆的應用程式閘道。

Web 應用程式防火牆防禦的一些常見 Web 弱點包括︰

- SQL 插入式攻擊保護

- 跨網站指令碼保護

- 常見 Web 攻擊保護，例如命令插入式攻擊、HTTP 要求走私、HTTP 回應分割和遠端檔案包含攻擊

- 防範 HTTP 通訊協定違規

- 防範 HTTP 通訊協定異常行為，例如遺漏主機使用者代理程式和接受標頭

- 防範 Bot、編目程式和掃描器

- 偵測一般應用程式錯誤組態 (也就是 Apache、IIS 等)

> [!Note]
> 有關更詳細的規則及其保護清單，請參閱以下[核心規則集](../../application-gateway/waf-overview.md)：

Azure 也提供幾項簡單易用的功能，可協助保護您的應用程式的輸入和輸出流量。 Azure 也藉由外部提供的功能來掃描您 Web 應用程式的弱點，協助客戶保護他們的應用程式程式碼。

- [為應用程式設定 Azure Active Directory 驗證](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [啟用傳輸層安全性 (TLS/SSL) - HTTPS 來保護傳入應用程式的流量](../../app-service/configure-ssl-bindings.md)

  - [強制所有傳入流量透過 HTTPS 連線](http://microsoftazurewebsitescheatsheet.info/)

  - [啟用 Strict Transport Security (HSTS)](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [根據用戶端 IP 位址限制對應用程式的存取](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [根據用戶端行為 (要求頻率和並行) 限制對應用程式的存取](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [使用 Tinfoil 安全性掃描來掃描 Web 應用程式程式碼的弱點](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [設定 TLS 相互驗證以要求用戶端憑證來連線到 Web 應用程式](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [設定保護應用程式對外部資源連線的用戶端憑證](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [移除標準伺服器標頭以避免工具記錄應用程式的特徵](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [使用點對站 VPN 保護應用程式與私人網路中資源的連線](../../app-service/web-sites-integrate-with-vnet.md)

- [使用混合式連接保護應用程式與私人網路中資源的連線](../../app-service/app-service-hybrid-connections.md)

Azure App Service 會使用 Azure 雲端服務和虛擬機器使用的相同反惡意程式碼方案。 要瞭解有關此內容的詳細資訊，請參閱我們的[反惡意軟體文檔](antimalware.md)。

## <a name="secure-your-network"></a>保護您的網路
Microsoft Azure 包括強大網路基礎結構以支援您的應用程式和服務連線需求。 在 Azure 的資源之間、內部部署與 Azure 託管資源之間，以及到網際網路和 Azure 與來自網際網路和 Azure，可能會有網路連線。

[Azure 網路基礎結構](../../virtual-machines/windows/infrastructure-example.md)可讓 Azure 資源與[虛擬網路 (VNet)](../../virtual-network/virtual-networks-overview.md) 彼此安全地連線。 VNet 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端網路邏輯隔離。 您可以將 VNet 連線到內部部署網路。

![保護網路 (保護)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

如果您需要基本的網路級存取控制（基於 IP 位址和 TCP 或 UDP 協定），則可以使用[網路安全性群組](../../virtual-network/virtual-network-vnet-plan-design-arm.md)。 網路安全性群組 (NSG) 是基本可設定狀態封包篩選防火牆，並可讓您根據 [5-tuple](https://www.techopedia.com/definition/28190/5-tuple)來控制存取權。

Azure 網路支援自訂您 Azure 虛擬網路上網路流量之路由行為的能力。 可以通過在 Azure 中配置[使用者定義的路由](../../virtual-network/virtual-networks-udr-overview.md)來執行此操作。

[強制隧道](https://www.petri.com/azure-forced-tunneling)是一種機制，可用於確保不允許服務啟動與 Internet 上的設備的連接。

Azure 支援內部部署網路的專用 WAN 連結連線以及採用 [ExpressRoute](../../expressroute/expressroute-introduction.md)的 Azure 虛擬網路。 Azure 與網站之間的連結會使用不經由公用網際網路的專用連線。 如果 Azure 應用程式在多個資料中心中運行，則可以使用[Azure 流量管理器](../../traffic-manager/traffic-manager-overview.md)在應用程式的實例之間智慧路由使用者的請求。 您也可以將流量路由傳送到不在 Azure 中執行的服務，只要可以從網際網路存取這些服務即可。

## <a name="virtual-machine-security"></a>虛擬機器安全性

[Azure 虛擬機器](../../virtual-machines/index.yml)允許您以敏捷的方式部署各種計算解決方案。 利用 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 與 Azure BizTalk 服務的支援，就可以在近乎所有的作業系統上，使用任何語言部署所有工作負載。

使用 Azure，您可以使用來自 Microsoft、賽門鐵克、趨勢科技和卡巴斯基等安全供應商[的反惡意軟體](antimalware.md)來保護虛擬機器免受惡意檔、廣告軟體和其他威脅。

適用於 Azure 雲端服務和虛擬機器的 Microsoft Antimalware 是即時保護功能，有助於識別和移除病毒、間諜軟體和其他惡意軟體。 Microsoft Antimalware 會提供可設定的警示，在已知的惡意或垃圾軟體嘗試自行安裝或在您的 Azure 系統上執行時發出警示。

[Azure 備份](../../backup/backup-overview.md)是一個可擴展的解決方案，可以零資本投資和最低的運營成本保護您的應用程式資料。 應用程式錯誤可能導致資料損毀，而人為錯誤可能會將 Bug 導入應用程式中。 使用 Azure 備份，您執行 Windows 與 Linux 的虛擬機器會受到保護。

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) 有助於協調工作負載和應用程式的複寫、容錯移轉及復原，因此能夠在主要位置發生故障時，透過次要位置提供工作負載和應用程式。

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>確保合規性：雲服務盡職調查清單

Microsoft 開發了[雲端服務審查評鑑檢查表](https://aka.ms/cloudchecklist.download)以協助組織在考慮移轉至雲端時練習審查評鑑程序。 它所提供的結構適用於任何規模和類型的組織 (私人企業及公部門組織，包括各層級的政府組織與非營利機構)，可讓他們找出自己的效能、服務、資料管理和控管目標與需求。 這可讓他們比較不同雲端服務提供者所提供的供應項目，最終形成雲端服務合約的的基礎。

檢查表所提供的架構逐條符合雲端服務合約的新版國際標準，也就是 ISO/IEC 19086。 這項標準為組織提供了一套統一的考量事項，幫助他們決定要採用哪種雲端服務，並建立共同基準點供他們比較雲端服務供應項目。

此檢查表可促使他們在經過徹底審查後才移轉至雲端，讓他們有結構化的指引和一致、可重複的方法來選擇雲端服務提供者。

採用雲端不再只是技術方面的決策。 因為檢查表需求涉及各個組織層面，因此可用來招集所有重要的內部決策者，CIO、CISO 以及法律、風險管理、採購和合規性等方面的專業人員。 合理推論，這會增加決策流程和做出有根據結定的效率，從而減少在採用時遇到意外障礙的可能性。

此外，此檢查表還能：

- 在雲端採用程序的一開始就對決策者揭示重要討論主題。

- 支援完整的商務討論，探討相關法規和組織自己在隱私權、個人識別資訊 (PII) 和資料安全性方面的目標。

- 協助組織找出可能會影響雲端專案的任何潛在問題。

- 使用相同的詞彙、定義、度量和交付成果對每個提供者提供一組一致的問題，以簡化不同雲端服務提供者供應項目的比較程序。

## <a name="azure-infrastructure-and-application-security-validation"></a>Azure 基礎結構和應用程式安全驗證

[Azure 操作安全性](operational-security.md)是指使用者可用於保護其資料、應用程式和 Microsoft Azure 中的其他資產的服務、控制項和功能。

![安全性驗證 (偵測)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure 作業安全性的基礎架構涵蓋透過 Microsoft 特有之各種功能獲得的知識，包括 Microsoft 安全性開發週期 (SDL)、Microsoft Security Response Center 方案，以及對網路安全性威脅型態的深層認知。

### <a name="microsoft-azure-monitor"></a>微軟 Azure 監視器

[Azure 監視器](../../azure-monitor/index.yml)是混合雲的 IT 管理解決方案。 Azure 監視器日誌單獨使用或擴展現有系統中心部署，可為您提供基礎架構基於雲的管理的最大靈活性和控制力。

![Azure 監視器](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

借助 Azure 監視器，您可以以比競爭解決方案更低的成本管理任何雲中的任何實例，包括本地、Azure、AWS、Windows 伺服器、Linux、VMware 和 OpenStack。 Azure 監視器專為雲優先世界構建，是管理企業的新方法，是應對新業務挑戰和適應新工作負載、應用程式和雲環境的最快、最具成本效益的方法。

### <a name="azure-monitor-logs"></a>Azure 監視器記錄

[Azure 監視器日誌](https://azure.microsoft.com/documentation/services/log-analytics)通過將來自託管資源的資料收集到中央存儲庫來提供監視服務。 此資料可能包含事件、效能資料或透過 API 提供的自訂資料。 所收集的資料即可用於警示、分析和匯出。

![Azure 監視器記錄](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

這個方法可讓您合併各種來源的資料，以便結合來自 Azure 服務與現有內部部署環境的資料。 它也可清楚劃分資料的收集與對該資料採取的動作，而讓所有類型的資料可以使用所有的動作。

### <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](../../security-center/security-center-intro.md)利用加強對您 Azure 資源的能見度及安全性控制權，來協助您預防、偵測及回應威脅。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

資訊安全中心會分析 Azure 資源的安全性狀態，以識別潛在的安全性弱點。 建議清單會引導您完成設定所需控制項的程序。

範例包括：

- 佈建反惡意程式碼，以協助識別及移除惡意軟體

- 設定網路安全性群組和規則，以控制對 VM 的流量

- 佈建 Web 應用程式防火牆，以協助抵禦以 Web 應用程式為目標的攻擊

- 部署遺漏的系統更新

- 處理不符合建議基準的作業系統組態

資訊安全中心會自動收集、分析和整合下列來源的記錄檔資料：Azure 資源、網路，以及反惡意程式碼程式和防火牆等夥伴解決方案。 偵測到威脅時，會建立安全性警示。 偵測範例包括：

- 已受到危害的 VM 與已知的惡意 IP 位址進行通訊

- 使用 Windows 錯誤報告偵測到進階的惡意程式碼

- 針對 VM 的暴力密碼破解攻擊

- 來自整合式反惡意程式碼程式和防火牆的安全性警示

### <a name="azure-monitor"></a>Azure 監視器

[Azure 監視器](../../azure-monitor/overview.md)提供指向特定類型資源資訊的指標。 它會針對來自 Azure 基礎結構 (活動記錄) 及每個個別的 Azure 資源 (診斷記錄) 的資料，提供視覺效果、查詢、路由、警示、自動調整及自動化功能。

雲端應用程式相當複雜，且具有許多移動組件。 監視會提供資料，以確保應用程式持續運作並以健全的狀態執行。 它也可協助您預防潛在問題，或是針對過去所發生的問題進行疑難排解。

![Azure 監視器](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png) 除此之外，您還可以使用監視資料來取得應用程式的深入解析。 這些知識可協助您提升應用程式效能或維護性，或是將原本需要手動介入的動作自動化。

在偵測網路漏洞，並確認您的 IT 安全性和法規治理模型的合規性時，稽核您的網路安全性非常重要。 透過 [安全性群組] 檢視，您可以擷取已設定的網路安全性群組和安全性規則，以及有效的安全性規則。 在套用的規則清單中，您可以決定已開啟的連接埠，並評估網路弱點。

### <a name="network-watcher"></a>網路監看員

[網路監看員](../../network-watcher/network-watcher-monitoring-overview.md)是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路層級條件。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 這項服務包括封包擷取、下一個躍點、IP 流量驗證、安全性群組檢視、NSG 流量記錄。 案例層級監視可提供端對端的網路資源檢視，而非個別的網路資源監視。

### <a name="storage-analytics"></a>儲存體分析

[存儲分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)可以存儲指標，其中包括有關存儲服務請求的聚合事務統計資訊和容量資料。 報告的交易是屬於 API 作業層級以及儲存體服務層級，而報告的容量則是屬於儲存體服務層級。 度量資料可用來分析儲存體服務使用量、診斷針對儲存體服務提出之要求的問題，以及提升使用服務的應用程式效能。

### <a name="application-insights"></a>Application Insights

[應用程式見解](../../azure-monitor/app/app-insights-overview.md)是適用于多個平臺上的 Web 開發人員的可擴展應用程式性能管理 （APM） 服務。 您可以使用它來監視即時 Web 應用程式。 它將會自動偵測效能異常。 其中包括強大的分析工具可協助您診斷問題，並了解使用者如何運用您的應用程式。 它是設計來協助您持續改善效能和可用性。 它適用於各種不同平台上的應用程式，包括裝載在內部部署或雲端的 .NET、Node.js 和 Java EE。 它可與您的 devOps 程序整合，並有與各種開發工具的連接點。

它可監視︰

- **要求率、回應時間和失敗率** - 找出哪些頁面在每天哪些時段最受歡迎，以及使用者位於何處。 查看哪些頁面的表現最好。 如果您的回應時間和失敗率隨著要求增加而提高，您或許有資源配置問題。

- **相依比率、回應時間和失敗率** - 找出外部服務是否會使您降低效能。

- **例外狀況**：分析彙總的統計資料，或挑選特定執行個體並深入了解堆疊追蹤和相關要求。 伺服器和瀏覽器例外狀況都會報告。

- **頁面檢視和載入效能** - 由使用者的瀏覽器報告。

- **來自網頁的AJAX呼叫**- 速率、回應時間和失敗率。

- **使用者和會話計數。**

- 你的Windows 或 Linux 伺服器中的**效能計數器**，例如 CPU、記憶體和網路使用量。

- 來自 Docker 或 Azure 的**主機診斷**。

- 來自您應用程式的**診斷追蹤記錄** - 讓您使追蹤事件與要求相互關聯。

- 自訂在用戶端或伺服器代碼中編寫的**事件和指標**，以跟蹤業務事件，如銷售的專案或贏得的遊戲。

應用程式的基礎結構通常由許多元件所組成 – 或許是虛擬機器、儲存體帳戶和虛擬網路，或者 web 應用程式、資料庫、資料庫伺服器和第三方服務。 您看不到這些元件作為個別的實體，而是看到它們作為單一實體相關且彼此相依的組件。 您會想要將其當成群組來部署、管理和監視。 [Azure 資源管理器](../../azure-resource-manager/management/overview.md)使您能夠作為一個組處理解決方案中的資源。

您可以透過單一、協調的作業來部署、更新或刪除方案的所有資源。 您會使用部署的範本，且該範本可以用於不同的環境，例如測試、預備和生產環境。 Resource Manager 會提供安全性、稽核和標記功能，以協助您在部署後管理您的資源。

**使用 Resource Manager 的優點**

Resource Manager 會提供數個優點：

- 您可以以群組形式部署、管理及監視解决方案的所有資源，而不是個別處理這些資源。

- 您可以在整個發展週期中重複部署你的解決方案，並確信您的資源會部署在一致的狀態中。

- 您可以透過宣告式範本而非指令碼來管理基礎結構。

- 您可以定義之間的相依性，使得以正確的順序部署資源。

- 因為角色型存取控制 (RBAC) 會自然地整合至管理平台，您可以將存取控制套用至資源群組中的所有服務。

- 您可以將標籤套用至資源，以便以邏輯方式組織訂用帳戶中的所有資源。

- 您可以檢視共用相同標籤之資源群組的成本，以釐清您的組織的計費方式。

> [!Note]
> Resource Manager 提供一個部署和管理方案的新方式。 如果您使用較舊的部署模型並想要了解這些變更，請參閱[了解 Resource Manager 部署和傳統部署](../../azure-resource-manager/management/deployment-models.md)。

## <a name="next-steps"></a>後續步驟

閱讀一些有深度的安全性主題，以深入了解安全性：

- [審核和日誌記錄](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [網路犯罪](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [設計與作業安全性 (英文)](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [加密][](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [身份和訪問管理](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [網路安全](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [威脅管理](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
