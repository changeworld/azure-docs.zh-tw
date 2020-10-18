---
title: Azure 中的安全性技術功能-Microsoft Azure
description: Azure 中的安全性服務簡介，可協助您保護雲端中的資料、資源和應用程式。
services: security
author: terrylanfear
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 07/13/2020
ms.author: terrylan
ms.openlocfilehash: 3ac044666b8f723e3cc26fd7a30f5dd8a8f77d90
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168030"
---
# <a name="azure-security-technical-capabilities"></a>Azure 安全性技術功能
本文提供 Azure 中的安全性服務簡介，可協助您保護雲端中的資料、資源和應用程式，並符合企業的安全性需求。

## <a name="azure-platform"></a>Azure 平台

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 是一個由基礎結構和應用程式服務所組成的雲端平台，內含整合式資料服務與進階分析以及開發人員工具和服務，並裝載在 Microsoft 的公用雲端資料中心內。 客戶可以使用 Azure 來實現許多不同的功能和案例，從基本的計算、網路和儲存，到行動和 Web 應用程式服務，再到物聯網之類的完整雲端案例都是如此，並可搭配開放原始碼技術來使用，以及部署為混合式雲端或裝載於客戶的資料中心內。 Azure 以建置組塊的形式提供雲端技術，以協助公司行號節省成本、快速創新，以及主動管理系統。 當您建置 IT 資產或將其移轉至雲端提供者時，您可以依賴該組織的能力來保護您的應用程式和資料，並利用他們所提供的安全性控制來管理您雲端架構資產的安全性。

在所有雲端運算提供者中，只有 Microsoft Azure 會為各個團隊提供可在其不同的雲端技能集和專案複雜度層級內運作、安全且一致的應用程式平台和基礎結構即服務，其內含整合式資料服務和分析功能，不管資料是放在 Microsoft 還是非 Microsoft 平台，它都能從這些資料中挖掘出情報，Microsoft Azure 也內含開放式架構和工具，可供團隊選擇是要整合雲端與內部部署，還是要在內部部署資料中心內部署 Azure 雲端服務。 Azure 是 Microsoft 受信任雲端的一部分，因此客戶可以依賴 Azure 來獲得領先業界的安全性、可靠性、合規性、隱私權以及由眾多人員、合作夥伴和程序所構成的巨大網路，以在雲端環境中支援組織。

透過 Microsoft Azure，您可以：

- 加快雲端創新速度。

- 利用深入見解來支援商業決策和應用程式。

- 免費建置及隨處部署。

- 保護其業務。

## <a name="security-technical-capabilities-to-fulfil-your-responsibility"></a>履行您責任的安全性技術功能

Microsoft Azure 提供的服務可協助您符合安全性、隱私權和合規性需求。 下圖說明各種可用的 Azure 服務，可讓您根據產業標準建立安全且符合規範的應用程式基礎結構。

![可用的安全性技術功能 - 概略圖](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>管理及控制身分識別和使用者存取

Azure 可讓您管理使用者的身分識別和認證並控制存取權，協助您保護商務及個人資訊。

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft 身分識別與存取管理解決方案可協助 IT 保護跨公司資料中心和雲端的應用程式和資源存取，以啟用其他層級的驗證，例如多重要素驗證和條件式存取原則。 透過進階的安全性報告、稽核和警示來監視可疑活動，有助於減緩潛在的安全性問題。 [Azure Active Directory Premium](../../active-directory/active-directory-whatis.md) 提供數千個雲端應用程式的單一登入，以及您在內部部署環境中執行之 web 應用程式的存取權。

Azure Active Directory (Azure AD) 的安全性優點包括下列功能：

- 為您的混合式企業中的每個使用者建立和管理單一身分識別，讓使用者、群組和裝置保持同步。

- 提供您的應用程式 (包括數千個預先整合的 SaaS 應用程式) 的單一登入存取。

- 藉由同時對內部部署和雲端應用程式強制以規則為基礎的 Multi-Factor Authentication，啟用應用程式存取安全性。

- 透過 Azure AD 應用程式 Proxy 佈建對內部部署 Web 應用程式的安全遠端存取。

[Azure Active Directory 入口網站](https://aad.portal.azure.com/)可作為 Azure 入口網站的一部分。 您可以從此儀表板取得組織狀態的總覽，以及輕鬆地管理目錄、使用者或應用程式存取。

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

以下是 Azure 身分識別管理的核心功能：

- 單一登入

- Multi-Factor Authentication

- 安全性監視、警示以及機器學習服務型報告

- 消費者身分識別與存取管理

- 裝置註冊

- Privileged Identity Management

- 身分識別保護

#### <a name="single-sign-on"></a>單一登入

[單一登入 (SSO) ](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) 表示只要使用單一使用者帳戶登入一次，就能夠存取您需要進行商務的所有應用程式和資源。 登入之後，您可以存取所有需要的應用程式，而不需要再驗證一次 (例如，輸入密碼)。

許多組織依賴軟體即服務 (SaaS) 應用程式，例如 Microsoft 365、Box 和 Salesforce，以提供使用者生產力。 在過去，IT 人員必須在每個 SaaS 應用程式中個別建立並更新使用者帳戶，使用者則必須記住每個 SaaS 應用程式的密碼。

[Azure AD 將內部部署 Active Directory 延伸至雲端](../../active-directory/manage-apps/what-is-single-sign-on.md)，讓使用者能夠使用其主要組織帳戶來登入已加入網域的裝置和公司資源，以及其工作所需的所有 Web 和 SaaS 應用程式。

不只使用者不需要管理多組使用者名稱和密碼，還可根據組織群組，以及其身為員工的狀態，自動佈建或解除佈建其應用程式的存取權。 [Azure AD 引進了安全性和存取管理控制](../../active-directory/active-directory-enterprise-apps-manage-sso.md) ，可讓您跨 SaaS 應用程式集中管理使用者的存取權。

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[Azure Multi-Factor Authentication (MFA) ](../../active-directory/authentication/multi-factor-authentication.md) 是一種驗證方法，需要使用多個驗證方法，並在使用者登入和交易中新增重要的第二層安全性。 [MFA 有助於保護](../../active-directory/authentication/concept-mfa-howitworks.md) 對資料和應用程式的存取，同時滿足使用者對簡單登入程式的需求。 它可以透過一些驗證選項 (例如電話、文字訊息，或行動應用程式通知或驗證代碼，以及第三方 OAuth 權杖) 來提供強大的驗證功能。

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>安全性監視、警示以及機器學習服務型報告

安全性監視和警示以及機器學習式報告，會識別不一致的存取模式，可以協助您保護企業安全。 您可以使用 Azure Active Directory 的存取和使用情況報告來了解貴組織的目錄完整性和安全性。 利用此資訊，目錄管理員更能夠判斷可能發生安全性風險的位置，以便適當地規劃來減輕這些風險。

在 Azure 入口網站或透過 [Azure Active Directory 入口網站](https://aad.portal.azure.com/)，以下列方式分類[報告](../../active-directory/active-directory-reporting-azure-portal.md)：

- 異常報告 – 包含我們發現異常的登入事件。 我們的目標在於使您注意這類活動，並讓您能夠決定事件是否可疑。

- 整合式應用程式報告–可讓您深入瞭解雲端應用程式在組織中的使用方式。 Azure Active Directory 提供與數千個雲端應用程式的整合。

- 錯誤報告 – 指出將帳戶佈建至外部應用程式時可能發生的錯誤。

- 使用者特定報告–顯示特定使用者的裝置和登入活動資料。

- 活動記錄 – 包含過去 24 小時、過去 7 天或過去 30 天內所有稽核事件的記錄，以及群組活動變更、密碼重設和登錄活動。

#### <a name="consumer-identity-and-access-management"></a>消費者身分識別與存取管理

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) 是高可用性的全域身分識別管理服務，適用于可調整為數百個身分識別的取用者面向應用程式。 此服務可跨行動及 Web 平台進行整合。 可自訂的使用經驗讓您的消費者可以使用其現有的社交帳戶，或是建立新的認證來登入您所有的應用程式。

在過去，想要將取用 [者註冊並登入](../../active-directory-b2c/overview.md) 其應用程式的應用程式開發人員，將會撰寫自己的程式碼。 而且他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。 Azure Active Directory B2C 為您的組織提供更理想的做法，透過安全且以標準為基礎的平台以及更大的可延伸原則組合，協助將取用者身分識別管理整合至應用程式。

當您使用 Azure Active Directory B2C 時，您的取用者可以使用現有的社交帳戶 (Facebook、Google、Amazon、LinkedIn) 註冊應用程式，或是建立新的認證 (電子郵件地址與密碼，或使用者名稱與密碼)。

#### <a name="device-registration"></a>裝置註冊

[Azure AD 裝置註冊](../../active-directory/devices/overview.md) 是裝置型 [條件式存取](../../active-directory/devices/overview.md) 案例的基礎。 當裝置已註冊時，Azure AD 裝置註冊會在使用者登入時，對裝置提供用來驗證裝置的身分識別。 然後，您可以使用已驗證的裝置和裝置的屬性，針對裝載于雲端和內部部署的應用程式，強制執行條件式存取原則。

結合行動 [裝置管理 (MDM) ](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) 解決方案（例如 Intune）時，Azure Active Directory 中的裝置屬性會以裝置的其他相關資訊進行更新。 這可讓您建立條件式存取規則，以強制從裝置存取，以符合您的安全性和合規性標準。

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) 可讓您管理、控制和監視特殊許可權身分識別，以及 Azure AD 或線上服務等其他 Microsoft Microsoft 365 中的資源存取權。

有時候，使用者需要在 Azure 或 Microsoft 365 資源或其他 SaaS 應用程式中執行特殊許可權的作業。 這通常表示組織必須授與他們永久的 Azure AD 特殊權限存取權。 這會提高雲端資源的安全性風險，因為組織無法滴水不漏地監視這些使用者利用其管理員權限的所作所為。 此外，如果擁有特殊權限存取權的使用者帳戶遭到入侵，這個缺口可能會影響其整體的雲端安全性。 Azure AD 特殊權限身分識別管理有助於解決此風險。

Azure AD Privileged Identity Management 可讓您：

- 查看哪些使用者是 Azure AD 管理員

- 依需求啟用 Microsoft Online Services （例如 Microsoft 365 和 Intune）的「及時」系統管理存取權

- 取得有關系統管理員存取記錄與系統管理員指派變更的報告

- 取得有關特殊權限角色存取的警示

#### <a name="identity-protection"></a>身分識別保護

[Azure AD Identity Protection](../../active-directory/identity-protection/overview.md) 是一項安全性服務，可針對風險偵測和潛在弱點（會影響組織的身分識別）提供合併的觀點。 Identity Protection 使用現有 Azure Active Directory 的異常偵測功能 (可透過 Azure AD 的異常活動報告取得) ，並引進可即時偵測異常的新風險偵測類型。

## <a name="secure-resource-access"></a>安全資源存取

在 Azure 中從計費觀點著手的存取控制。 造訪 [Azure 帳戶中心](https://account.windowsazure.com/subscriptions)所存取 Azure 帳戶的擁有者是帳戶管理員 (AA) 。 訂用帳戶是計費容器，但它們也可做為安全性界限：每個訂用帳戶都有一個服務管理員 (SA)，此管理員可以使用 Azure 入口網站來新增、移除及修改該訂用帳戶中的 Azure 資源。 新訂用帳戶的預設 SA 為 AA，但 AA 可以在 Azure 帳戶中心變更 SA。

![Azure 中受保護的資源存取](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

訂用帳戶也會與目錄產生關聯。 目錄會定義一組使用者。 他們可以是公司或學校中建立目錄的使用者，也可以是外部使用者 (也就是 Microsoft 帳戶)。 訂用帳戶可供已被指派為服務管理員 (SA) 或共同管理員 (CA) 的某些目錄使用者存取；唯一的例外就是，由於舊版原因，Microsoft 帳戶 (先前稱為 Windows Live ID) 不需存在目錄中，即可被指派為 SA 或 CA。

安全性導向公司應該將焦點放在提供員工所需的確切權限。 權限太多可能會讓帳戶暴露在攻擊者的威脅下。 權限太少則會讓員工無法有效率地完成工作。 Azure[角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)透過提供更細緻的 azure 存取管理來協助解決此問題。

![受保護的資源存取](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

RBAC 可讓您區隔小組內的職責，而僅授與使用者執行作業所需的存取權。 您可以不授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許執行特定的動作。 例如，使用 RBAC 讓一位員工管理某個訂用帳戶中的虛擬機器，而讓另一位員工管理相同訂用帳戶中的 SQL 資料庫。

![Azure 中受保護的資源存取 (RBAC)](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>資料安全性與加密

在雲端保護資料的其中一個關鍵是考慮您的資料可能會發生的狀態，以及哪些控制項適用於該狀態。 如要了解 Azure 資料安全性和加密最佳做法，相關建議將以下列資料的狀態為主。

- 待用︰這包括實體媒體 (磁碟或光碟) 上以靜態方式存在的所有資訊儲存物件、容器和類型。
- 傳輸中：當資料在元件、位置或程式之間傳輸（例如透過網路）時，跨服務匯流排 (從內部部署到雲端，反之亦然，包括混合式連線（例如 ExpressRoute) ），或在輸入/輸出過程中，它會被視為正在進行的動作。

### <a name="encryption-at-rest"></a>待用加密

待用加密在 [Azure 資料加密](encryption-atrest.md)中有詳細的討論。

### <a name="encryption-in-transit"></a>傳輸中加密

保護傳輸中的資料應該是您的資料保護策略中不可或缺的部分。 由於資料會從許多位置來回移動，一般會建議您一律使用 SSL/TLS 通訊協定來交換不同位置的資料。 在某些情況下，建議您使用虛擬私人網路 (VPN)，隔離您的內部部署與雲端基礎結構之間的整個通訊通道。

對於在內部部署基礎結構與 Azure 之間移動的資料，您應該考慮適當的防護措施，例如 HTTPS 或 VPN。

對於需要從位於內部部署的多個工作站安全存取 Azure 的組織而言，請使用 [Azure 站對站 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)。

對於需要從位於內部部署的一個工作站安全存取 Azure 的組織而言，請使用[點對站 VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)。

較大的資料集可以透過專用的高速 WAN 連結 (例如 [ExpressRoute](https://azure.microsoft.com/services/expressroute/)) 移動。 如果您選擇使用 ExpressRoute，您也可以使用 [SSL/TLS](https://support.microsoft.com/kb/257591) 或其他通訊協定，在應用程式層級加密資料，以提供額外的保護。

如果您透過 Azure 入口網站與 Azure 儲存體互動，則所有交易都會透過 HTTPS 發生。 透過 HTTPS 的[儲存體 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) 也可用來與 [Azure 儲存體](https://azure.microsoft.com/services/storage/)和 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) 互動。

無法保護傳輸中資料的組織比較容易遭受 [中間人攻擊](https://technet.microsoft.com/library/gg195821.aspx)、 [竊聽](https://technet.microsoft.com/library/gg195641.aspx)及會話劫持攻擊。 這些攻擊可能是取得機密資料存取權的第一步。

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
[Web 應用程式防火牆 (WAF) ](../../application-gateway/waf-overview.md) 是 [應用程式閘道](../../application-gateway/overview.md) 的一項功能，可提供 Web 應用程式的集中式保護，免于遭受常見的惡意探索和弱點攻擊。

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
> 如需更詳細的規則清單及其保護，請參閱下列 [核心規則集](../../application-gateway/waf-overview.md)：

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

Azure App Service 會使用 Azure 雲端服務和虛擬機器使用的相同反惡意程式碼方案。 若要深入瞭解，請參閱我們的 [反惡意程式碼檔](antimalware.md)。

## <a name="secure-your-network"></a>保護您的網路
Microsoft Azure 包括強大網路基礎結構以支援您的應用程式和服務連線需求。 在 Azure 的資源之間、內部部署與 Azure 託管資源之間，以及到網際網路和 Azure 與來自網際網路和 Azure，可能會有網路連線。

[Azure 網路基礎結構](../../virtual-machines/windows/infrastructure-example.md)可讓 Azure 資源與[虛擬網路 (VNet)](../../virtual-network/virtual-networks-overview.md) 彼此安全地連線。 VNet 是您的網路在雲端中的身分。 VNet 是專屬於您訂用帳戶的 Azure 雲端網路邏輯隔離。 您可以將 VNet 連線到內部部署網路。

![保護網路 (保護)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

如果您需要基本網路層級存取控制 (根據 IP 位址和 TCP 或 UDP 通訊協定) ，您可以使用 [網路安全性群組](../../virtual-network/virtual-network-vnet-plan-design-arm.md)。 網路安全性群組 (NSG) 是基本可設定狀態封包篩選防火牆，並可讓您根據 [5-tuple](https://www.techopedia.com/definition/28190/5-tuple)來控制存取權。

Azure 網路支援自訂您 Azure 虛擬網路上網路流量之路由行為的能力。 您可以在 Azure 中 [設定使用者定義的路由](../../virtual-network/virtual-networks-udr-overview.md) 來完成這項作業。

[強制通道](https://www.petri.com/azure-forced-tunneling) 是一種機制，可讓您用來確保您的服務不能起始與網際網路上裝置的連線。

Azure 支援內部部署網路的專用 WAN 連結連線以及採用 [ExpressRoute](../../expressroute/expressroute-introduction.md)的 Azure 虛擬網路。 Azure 與網站之間的連結會使用不經由公用網際網路的專用連線。 如果您的 Azure 應用程式正在多個資料中心內執行，您可以使用 [Azure 流量管理員](../../traffic-manager/traffic-manager-overview.md) ，以智慧方式將使用者的要求路由傳送到應用程式的各個實例。 您也可以將流量路由傳送到不在 Azure 中執行的服務，只要可以從網際網路存取這些服務即可。

## <a name="virtual-machine-security"></a>虛擬機器安全性

[Azure 虛擬機器](../../virtual-machines/index.yml) 可讓您以敏捷的方式部署各式各樣的計算解決方案。 利用 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP 與 Azure BizTalk 服務的支援，就可以在近乎所有的作業系統上，使用任何語言部署所有工作負載。

透過 Azure，您可以使用來自安全性廠商（例如 Microsoft、Symantec、Trend 微和 Kaspersky）的 [反惡意程式碼軟體](antimalware.md) ，以保護您的虛擬機器免于惡意檔案、廣告軟體和其他威脅。

適用於 Azure 雲端服務和虛擬機器的 Microsoft Antimalware 是即時保護功能，有助於識別和移除病毒、間諜軟體和其他惡意軟體。 Microsoft Antimalware 會提供可設定的警示，在已知的惡意或垃圾軟體嘗試自行安裝或在您的 Azure 系統上執行時發出警示。

[Azure 備份](../../backup/backup-overview.md) 是可調整的解決方案，可保護您的應用程式資料，而不需要資本投資和最少量的營運成本。 應用程式錯誤可能導致資料損毀，而人為錯誤可能會將 Bug 導入應用程式中。 使用 Azure 備份，您執行 Windows 與 Linux 的虛擬機器會受到保護。

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md) 有助於協調工作負載和應用程式的複寫、容錯移轉及復原，因此能夠在主要位置發生故障時，透過次要位置提供工作負載和應用程式。

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>確定合規性：雲端服務的審慎檢查檢查清單

Microsoft 開發了[雲端服務審查評鑑檢查表](https://aka.ms/cloudchecklist.download)以協助組織在考慮移轉至雲端時練習審查評鑑程序。 它所提供的結構適用於任何規模和類型的組織 (私人企業及公部門組織，包括各層級的政府組織與非營利機構)，可讓他們找出自己的效能、服務、資料管理和控管目標與需求。 這可讓他們比較不同雲端服務提供者所提供的供應項目，最終形成雲端服務合約的的基礎。

檢查表所提供的架構逐條符合雲端服務合約的新版國際標準，也就是 ISO/IEC 19086。 這項標準為組織提供了一套統一的考量事項，幫助他們決定要採用哪種雲端服務，並建立共同基準點供他們比較雲端服務供應項目。

此檢查表可促使他們在經過徹底審查後才移轉至雲端，讓他們有結構化的指引和一致、可重複的方法來選擇雲端服務提供者。

採用雲端不再只是技術方面的決策。 因為檢查表需求涉及各個組織層面，因此可用來招集所有重要的內部決策者，CIO、CISO 以及法律、風險管理、採購和合規性等方面的專業人員。 合理推論，這會增加決策流程和做出有根據結定的效率，從而減少在採用時遇到意外障礙的可能性。

此外，此檢查表還能：

- 在雲端採用程序的一開始就對決策者揭示重要討論主題。

- 支援完整的商務討論，探討相關法規和組織自己在隱私權、個人識別資訊 (PII) 和資料安全性方面的目標。

- 協助組織找出可能會影響雲端專案的任何潛在問題。

- 使用相同的詞彙、定義、度量和交付成果對每個提供者提供一組一致的問題，以簡化不同雲端服務提供者供應項目的比較程序。

## <a name="azure-infrastructure-and-application-security-validation"></a>Azure 基礎結構和應用程式安全性驗證

[Azure 作業安全性](operational-security.md) 是指使用者可在 Microsoft Azure 中保護其資料、應用程式和其他資產的服務、控制及功能。

![安全性驗證 (偵測)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure 作業安全性的基礎架構涵蓋透過 Microsoft 特有之各種功能獲得的知識，包括 Microsoft 安全性開發週期 (SDL)、Microsoft Security Response Center 方案，以及對網路安全性威脅型態的深層認知。

### <a name="microsoft-azure-monitor"></a>Microsoft Azure 監視

[Azure 監視器](../../azure-monitor/index.yml) 是適用于混合式雲端的 IT 管理解決方案。 您可以單獨使用或擴充現有的 System Center 部署，Azure 監視器記錄可為您基礎結構的雲端式管理提供最大的彈性和控制。

![Azure 監視器](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

有了 Azure 監視器，您就可以管理任何雲端中的任何實例，包括內部部署、Azure、AWS、Windows Server、Linux、VMware 和 OpenStack，成本比競爭解決方案低。 Azure 監視器為雲端優先的世界打造了一個新方法來管理您的企業，這是最快速、最符合成本效益的方式，可符合新的商業挑戰並適應新的工作負載、應用程式和雲端環境。

### <a name="azure-monitor-logs"></a>Azure 監視器記錄

[Azure 監視器記錄](https://azure.microsoft.com/documentation/services/log-analytics) 會藉由將受控資源中的資料收集到中央存放庫，來提供監視服務。 此資料可能包含事件、效能資料或透過 API 提供的自訂資料。 所收集的資料即可用於警示、分析和匯出。

![Azure 監視器記錄](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

這個方法可讓您合併各種來源的資料，以便結合來自 Azure 服務與現有內部部署環境的資料。 它也可清楚劃分資料的收集與對該資料採取的動作，而讓所有類型的資料可以使用所有的動作。

### <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](../../security-center/security-center-intro.md) 可協助您防止、偵測和回應威脅，並加強對 Azure 資源安全性的可見度和控制權。 它提供您 Azure 訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

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

[Azure 監視器](../../azure-monitor/overview.md) 提供特定資源類型的相關資訊指標。 它會針對來自 Azure 基礎結構 (活動記錄) 及每個個別的 Azure 資源 (診斷記錄) 的資料，提供視覺效果、查詢、路由、警示、自動調整及自動化功能。

雲端應用程式相當複雜，且具有許多移動組件。 監視會提供資料，以確保應用程式持續運作並以健全的狀態執行。 它也可協助您預防潛在問題，或是針對過去所發生的問題進行疑難排解。

![顯示您可以使用監視資料來深入瞭解應用程式的圖表。](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
除此之外，您還可以使用監視資料來取得應用程式的深入解析。 這些知識可協助您提升應用程式效能或維護性，或是將原本需要手動介入的動作自動化。

在偵測網路漏洞，並確認您的 IT 安全性和法規治理模型的合規性時，稽核您的網路安全性非常重要。 透過 [安全性群組] 檢視，您可以擷取已設定的網路安全性群組和安全性規則，以及有效的安全性規則。 在套用的規則清單中，您可以決定已開啟的連接埠，並評估網路弱點。

### <a name="network-watcher"></a>網路監看員

[網路監看員](../../network-watcher/network-watcher-monitoring-overview.md)是一項區域性服務，可讓您監視與診斷位於和進出 Azure 的網路層級條件。 網路監看員提供的網路診斷和視覺效果工具，可幫助您了解、診斷及洞悉您在 Azure 中的網路。 這項服務包括封包擷取、下一個躍點、IP 流量驗證、安全性群組檢視、NSG 流量記錄。 案例層級監視可提供端對端的網路資源檢視，而非個別的網路資源監視。

### <a name="storage-analytics"></a>儲存體分析

[儲存體分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) 可以儲存包含與儲存體服務要求相關的匯總交易統計資料和容量資料的計量。 報告的交易是屬於 API 作業層級以及儲存體服務層級，而報告的容量則是屬於儲存體服務層級。 度量資料可用來分析儲存體服務使用量、診斷針對儲存體服務提出之要求的問題，以及提升使用服務的應用程式效能。

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 是適用于多個平臺上的 網頁程式開發人員的可延伸應用程式效能管理 (APM) 服務。 您可以使用它來監視即時 Web 應用程式。 它將會自動偵測效能異常。 其中包括強大的分析工具可協助您診斷問題，並了解使用者如何運用您的應用程式。 它是設計來協助您持續改善效能和可用性。 它適用於各種不同平台上的應用程式，包括裝載在內部部署或雲端的 .NET、Node.js 和 Java EE。 它可與您的 devOps 程序整合，並有與各種開發工具的連接點。

它可監視︰

- **要求率、回應時間和失敗率** - 找出哪些頁面在每天哪些時段最受歡迎，以及使用者位於何處。 查看哪些頁面的表現最好。 如果您的回應時間和失敗率隨著要求增加而提高，您或許有資源配置問題。

- **相依比率、回應時間和失敗率** - 找出外部服務是否會使您降低效能。

- **例外狀況**：分析彙總的統計資料，或挑選特定執行個體並深入了解堆疊追蹤和相關要求。 伺服器和瀏覽器例外狀況都會報告。

- **頁面檢視和載入效能** - 由使用者的瀏覽器報告。

- **來自網頁的 AJAX 呼叫** -比率、回應時間和失敗率。

- **使用者和會話計數。**

- 你的Windows 或 Linux 伺服器中的**效能計數器**，例如 CPU、記憶體和網路使用量。

- 來自 Docker 或 Azure 的**主機診斷**。

- 來自您應用程式的**診斷追蹤記錄** - 讓您使追蹤事件與要求相互關聯。

- 您在用戶端或伺服器程式碼中自行撰寫的**自訂事件和計量**，可追蹤商務事件，例如銷售專案或贏得的遊戲。

應用程式的基礎結構通常由許多元件所組成 – 或許是虛擬機器、儲存體帳戶和虛擬網路，或者 web 應用程式、資料庫、資料庫伺服器和第三方服務。 您看不到這些元件作為個別的實體，而是看到它們作為單一實體相關且彼此相依的組件。 您會想要將其當成群組來部署、管理和監視。 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 可讓您將方案中的資源作為群組使用。

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

- [稽核和記錄](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [網路犯罪](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [設計與作業安全性 (英文)](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [加密](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [身分識別和存取管理](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [網路安全性](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [威脅管理](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)
