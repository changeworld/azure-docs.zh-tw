---
title: 強式身分識別基礎的四個步驟-Azure AD
description: 本主題說明混合式身分識別客戶可採用的四個步驟，以建立強式身分識別基礎。
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4543a46c9362ac6d20cc7597de9f59c1a7520163
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90600791"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>使用 Azure Active Directory 的強式身分識別基礎進行四個步驟

因為將應用程式快速移至雲端，所以管理應用程式和資料的存取權不會再依賴傳統的網路安全性界限策略，例如周邊網路和防火牆。 現在，組織必須信任其身分識別解決方案，以控制誰和誰可以存取組織的應用程式和資料。 有更多的組織允許員工攜帶自己的裝置來工作，並從可連線至網際網路的任何地方使用其裝置。 在組織選擇實施的身分識別解決方案中，確保這些裝置的相容性和安全已成為重要的考慮。 在現今的數位工作場所，身分識別是任何移至雲端之組織的 [主要控制平面](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) 。

在採用 Azure Active Directory (Azure AD) 混合式身分識別解決方案時，組織會取得高階功能的存取權，透過自動化、委派、自助服務和單一登入功能來發揮生產力。 它可讓您的員工從任何需要的位置存取公司資源，同時讓您的 IT 小組可以存取正確資源的正確存取權，以建立安全的生產力。

根據我們的學習，此最佳作法檢查清單可協助您快速部署建議的動作，以在您的組織中建立 *強大* 的身分識別基礎：

* 輕鬆連接至應用程式
* 自動為每個使用者建立一個身分識別
* 安全地強化您的使用者
* 讓您的見解

## <a name="step-1---connect-to-apps-easily"></a>步驟 1-輕鬆連接至應用程式

藉由將您的應用程式與 Azure AD 連接，您可以啟用單一登入 (SSO) 並進行使用者布建，以提升使用者的生產力和安全性。 藉由在單一位置管理您的應用程式，Azure AD，您可以將系統管理額外負荷降到最低，並針對您的安全性和合規性原則達成單一控制點。

本節涵蓋的選項可讓您管理使用者對應用程式的存取、啟用對內部應用程式的安全遠端存取，以及將您的應用程式遷移至 Azure AD 的優點。

### <a name="make-apps-available-to-your-users-seamlessly"></a>讓您的使用者能夠順暢地使用應用程式

Azure AD 可讓系統管理員 [將應用程式新增](../manage-apps/add-application-portal.md) 至 [Azure 入口網站](https://portal.azure.com/)中的企業應用程式資源庫。 將應用程式新增至企業應用程式庫，可讓您更輕鬆地將應用程式設定為使用 Azure AD 作為身分識別提供者。 它也可讓您使用條件式存取原則來管理使用者對應用程式的存取，並設定單一登入 (SSO) 應用程式，讓使用者不需要重複輸入其密碼，並會自動登入內部部署和雲端式應用程式。

一旦將應用程式新增至 Azure AD 資源庫，使用者就可以看到指派給他們的應用程式，並視需要搜尋和要求其他應用程式。 Azure AD 提供 [數種方法](../manage-apps/end-user-experiences.md) 讓使用者存取其應用程式：

* 存取面板/我的應用程式
* Microsoft 365 應用程式啟動器
* 直接登入同盟應用程式
* 直接登入連結

若要深入瞭解使用者對應用程式的存取權，請參閱這篇文章中的 **步驟 3--強化您的使用者** 。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>將應用程式從 Active Directory 同盟服務遷移至 Azure AD

將單一登入設定從 Active Directory 同盟服務 (ADFS) 遷移至 Azure AD 可提供安全性的額外功能、更一致的管理性，以及共同作業。 為了獲得最佳結果，建議您將應用程式從 AD FS 遷移至 Azure AD。 將您的應用程式驗證和授權帶入 Azure AD 可提供下列優點：

* 管理成本
* 管理風險
* 提高生產力
* 解決合規性和治理

若要深入瞭解，請參閱將 [您的應用程式遷移至 Azure Active Directory](https://aka.ms/migrateapps/whitepaper) 白皮書。

### <a name="enable-secure-remote-access-to-apps"></a>啟用對應用程式的安全遠端存取

[Azure AD 應用程式 Proxy](../manage-apps/what-is-application-proxy.md) 提供簡單的解決方案，可讓組織將內部部署應用程式發佈至雲端，以供需要以安全方式存取內部應用程式的遠端使用者。 Azure AD 單一登入之後，使用者可以透過外部 Url 或內部應用程式入口網站來存取雲端和內部部署應用程式。

Azure AD 應用程式 Proxy 提供下列優點：

* 將 Azure AD 擴充至內部部署資源
  * 雲端級別安全性和保護
  * 很容易啟用的條件式存取和 Multi-Factor Authentication 等功能
* 周邊網路中沒有任何元件，例如 VPN 和傳統反向 proxy 解決方案
* 沒有所需的輸入連線
* 單一登入 (SSO) 跨雲端和內部部署的裝置、資源和應用程式
* 讓終端使用者隨時隨地都能發揮生產力

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>使用 Microsoft Cloud App Security 探索影子 IT

現代企業 IT 部門通常不會知道使用者用來執行其工作的所有雲端應用程式。 當 IT 系統管理員詢問他們認為其員工使用多少個雲端應用程式時，平均為30或40。 事實上，您組織中的員工使用的平均是超過1000個不同的應用程式。 80% 的員工使用非獲批准的應用程式，這些應用程式都未經過檢查，且可能不符合您的安全性和合規性政策。

[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) 可協助您識別有用的應用程式，這些應用程式可能會批准並新增至企業應用程式資源庫，讓使用者可以從 SSO 和條件式存取等功能獲益。

<em>「**Cloud App Security** 可協助我們確保我們的人員能夠正確地使用我們的雲端和 SaaS 應用程式，以支援協助保護 Accenture 的基礎安全性原則。」</em> --- [John Blasi，管理總監，資訊安全，Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了偵測影子 IT，MCAS 還可以判斷應用程式的風險層級、防止未經授權的存取公司資料、可能的資料洩漏，以及其他應用程式中的安全性風險。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步驟 2-自動為每個使用者建立一個身分識別

在 Azure AD 混合式身分識別解決方案中整合內部部署和雲端式目錄，可讓您在雲端中布建現有的身分識別，以重複使用現有的內部部署 Active Directory 投資。 解決方案會使用 Azure AD 來同步處理內部部署身分識別，同時讓內部部署 Active Directory 以任何現有的治理解決方案執行，作為身分識別的真實來源。 Microsoft 的 Azure AD 混合式身分識別解決方案可跨越內部部署和雲端式的功能，無論其位置為何，都能為所有資源的驗證和授權建立一般使用者身分識別。

將您的內部部署目錄與 Azure AD 整合可讓使用者更具生產力，並藉由提供通用身分識別來存取雲端和內部部署資源，以防止使用者在應用程式和服務中使用多個帳戶。 使用多個帳戶是終端使用者和 IT 的難題。 從終端使用者的觀點來看，擁有多個帳戶表示必須記住多個密碼。 為了避免發生這種情況，許多使用者會對每個帳戶重複使用相同的密碼，這對安全性的觀點而言並不正確。 從 IT 的觀點來看，重複使用通常會導致更多的密碼重設和技術服務人員成本，以及終端使用者抱怨。

Azure AD Connect 是用來將內部部署身分識別同步處理至 Azure AD 的工具，然後可用來存取雲端應用程式。 一旦 Azure AD 中的身分識別，就可以布建至 SaaS 應用程式（例如 Salesforce 或 Concur）。

在本節中，我們會列出為雲端提供高可用性、新式驗證以及降低內部部署使用量的建議。

> [!NOTE]
> 如果您想要深入瞭解 Azure AD Connect，請參閱 [什麼是 Azure AD Connect 同步處理？](./how-to-connect-sync-whatis.md)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>針對 Azure AD Connect 設定預備伺服器，並將它保持在最新狀態

Azure AD Connect 在布建過程中扮演著重要的角色。 如果同步伺服器因為任何原因而離線，內部部署的變更將不會在雲端中更新，並且會導致使用者存取問題。 請務必定義容錯移轉策略，讓系統管理員能夠在同步伺服器離線之後快速繼續同步處理。

若要在主要 Azure AD Connect 伺服器離線的情況下提供高可用性，建議您為 Azure AD Connect 部署個別的 [預備伺服器](./how-to-connect-sync-staging-server.md) 。 部署伺服器可讓系統管理員透過簡單的設定參數，將預備伺服器「升級」至生產環境。 在預備模式中設定待命伺服器也可讓您測試和部署新的設定變更，並在解除舊的伺服器時引進新的伺服器。

> [!TIP]
> Azure AD Connect 會定期更新。 因此，強烈建議您保持預備伺服器的最新版本，以利用每個新版本所提供的效能改進、bug 修正及新功能。

### <a name="enable-cloud-authentication"></a>啟用雲端驗證

使用內部部署 Active Directory 的組織應該將其目錄延伸至 Azure AD 使用 Azure AD Connect，並設定適當的驗證方法。 為您的組織[選擇正確的驗證方法](./choose-ad-authn.md)，是將應用程式移至雲端的第一步。 它是一個重要的元件，因為它會控制對所有雲端資料和資源的存取。

在 Azure AD 中啟用內部部署目錄物件的 cloud authentication 最簡單且建議的方法，是啟用 (PHS) 的 [密碼雜湊同步](./how-to-connect-password-hash-synchronization.md) 處理。 或者，某些組織可能會考慮啟用 [傳遞驗證](./how-to-connect-pta-quick-start.md) (PTA) 。

無論您選擇 PHS 或 PTA，別忘了啟用 [無縫單一登入](./how-to-connect-sso.md) ，可讓使用者存取雲端應用程式，而不需要在公司網路上使用 Windows 7 和8裝置時，在應用程式中持續輸入其使用者名稱和密碼。 若沒有單一登入，使用者必須記住應用程式特定的密碼，並登入每個應用程式。 同樣地，IT 人員必須為每個應用程式（例如 Microsoft 365、Box 和 Salesforce）建立及更新使用者帳戶。 使用者必須記住他們的密碼，並花時間登入每個應用程式。 為整個企業提供標準化的單一登入機制，對最佳使用者體驗、降低風險、報告和治理的能力非常重要。

針對已在使用 AD FS 或其他內部部署驗證提供者的組織，移至 Azure AD 作為身分識別提供者可降低複雜度並提高可用性。 除非您有使用同盟的特定使用案例，否則建議您從同盟驗證遷移至 PHS 和無縫 sso 或 PTA 和無縫 SSO，以享有降低內部部署使用量的優點，以及雲端提供的彈性，並提升使用者體驗。 如需詳細資訊，請參閱 [Azure Active Directory 的從同盟遷移至密碼雜湊同步](./plan-migrate-adfs-password-hash-sync.md)處理。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>啟用帳戶的自動解除布建

為您的應用程式啟用自動化布建和解除布建，是在多個系統間管理身分識別生命週期的最佳策略。 Azure AD 支援將使用者帳戶 [自動化、以原則為基礎的布建和](../app-provisioning/configure-automatic-user-provisioning-portal.md) 解除布建至各式各樣的熱門 SaaS 應用程式（例如 ServiceNow 和 Salesforce），以及其他可執行 [SCIM 2.0 通訊協定](../app-provisioning/use-scim-to-provision-users-and-groups.md)的應用程式。 不同于傳統的布建解決方案（需要自訂程式碼或手動上傳 CSV 檔案），布建服務會裝載于雲端，並具備可使用 Azure 入口網站設定及管理的預先整合連接器。 自動解除布建的主要優點是，它會在使用者離開組織時，立即從關鍵 SaaS 應用程式中移除使用者的身分識別，以協助保護您的組織。

如需深入了解自動化使用者佈建，請參閱 [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](../app-provisioning/user-provisioning.md)。

## <a name="step-3---empower-your-users-securely"></a>步驟 3-安全地強化您的使用者

在現今的數位工作場所，請務必在安全性與生產力之間取得平衡。 不過，終端使用者通常會回頭回復安全性措施，以降低其生產力和雲端應用程式的存取權。 為了協助解決這個情況，Azure AD 提供自助功能，可讓使用者保持生產力，同時將系統管理額外負荷降到最低。

本節列出在您的使用者獲得更順暢的情況下，如何讓您的使用者能夠移除組織中的摩擦的建議。

### <a name="enable-self-service-password-reset-for-all-users"></a>為所有使用者啟用自助式密碼重設

Azure 的 [自助式密碼重設](../authentication/tutorial-enable-sspr.md) (SSPR) 為 IT 系統管理員提供簡單的方法，讓使用者可以在不需要系統管理員介入的情況下重設及解除鎖定其密碼或帳戶。 系統包含詳細的報告，可追蹤使用者何時存取系統與通知來警示您誤用或濫用。

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。 但是，當您啟用 [內部部署 Azure AD Connect 整合](../authentication/concept-sspr-howitworks.md#on-premises-integration)時，您也可以選擇分隔這兩項作業，讓使用者可以解除鎖定其帳戶，而不需要重設密碼。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>確定所有使用者都已註冊 MFA 和 SSPR

Azure 提供的報表可供您和您的組織使用，以確保使用者已註冊 MFA 和 SSPR。 尚未註冊的使用者可能需要在此程式上獲得教育。

MFA 登 [入報告](../authentication/howto-mfa-reporting.md) 包含 mfa 使用方式的相關資訊，並可讓您深入瞭解 mfa 在您組織中的運作方式。 將登入活動 (和審核和風險偵測) Azure AD 的存取權，對於疑難排解、使用方式分析和辯論調查而言很重要。

同樣地，您可以使用 [自助密碼管理報告](../authentication/howto-sspr-reporting.md) 來判斷誰已 (或尚未) 註冊 SSPR。

### <a name="self-service-app-management"></a>自助應用程式管理

您必須先針對您想要允許使用者自行探索和要求存取權的任何應用程式，啟用 [自助應用程式存取](../manage-apps/access-panel-manage-self-service-access.md) 權，您的使用者才能從其存取面板自行探索應用程式。 自助應用程式存取是讓使用者能夠自行探索應用程式，並選擇性地允許商務群組核准存取這些應用程式的絕佳方式。 您可以讓商務群組從其存取面板直接管理指派給這些使用者的 [密碼單一登入應用程式](../manage-apps/application-sign-in-problem-password-sso-gallery.md#configure-password-sso-for-an-azure-ad-gallery-app) 的認證。

### <a name="self-service-group-management"></a>自助式群組管理

使用群組時，將使用者指派給應用程式是最好的對應，因為它們可提供絕佳的彈性和大規模管理的能力：

* 以屬性為基礎的動態群組成員資格
* 委派給應用程式擁有者

Azure AD 能讓您使用安全性群組和 Microsoft 365 群組來管理資源的存取權。 這些群組可以由群組擁有者管理，該擁有者可以核准或拒絕成員資格要求，以及委派群組成員資格的控制權。 這項功能稱為「 [自助群組管理](../users-groups-roles/groups-self-service-management.md)」，可讓未獲派系統管理角色的群組擁有者建立及管理群組，而不需要依賴系統管理員來處理其要求，藉此節省時間。

## <a name="step-4---operationalize-your-insights"></a>步驟 4-讓您的見解

安全性相關事件和相關警示的審核和記錄是有效原則的重要元件，可確保使用者保持生產力，且您的組織是安全的。 安全性記錄和報告可協助回答下列問題：

* 您使用的是您所支付的費用嗎？
* 我的租使用者中是否有任何可疑或惡意的情況？
* 安全性事件期間受影響的物件？

安全性記錄和報告會提供可疑活動的電子記錄，並協助您偵測可能指出從外部嘗試或成功滲透網路以及內部攻擊的模式。 您可以使用稽核來監視使用者活動、文件合規性、執行鑑識分析等等。 警示會提供安全性事件通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>指派作業的最低特殊許可權管理員角色

當您考慮操作的方法時，需要考慮幾個層級的管理。 第一個層級會將管理的負擔放在全域管理員 (的) 。 一律使用「全域管理員」角色，可能適用于小型公司。 但對於具有協助服務中心人員和負責特定工作之系統管理員的較大型組織而言，指派全域管理員的角色可能會造成安全性風險，因為它可讓這些人員管理上述工作的能力，並超越他們應該能夠執行的工作。

在此情況下，您應該考慮下一個層級的系統管理。 您可以使用 Azure AD 將終端使用者指定為「受限的系統管理員」，以便管理許可權較低的角色中的工作。 例如，您可以將「 [安全性讀者](../users-groups-roles/directory-assign-admin-roles.md#security-reader) 」角色指派給技術支援中心人員，讓他們能夠以唯讀存取權管理安全性相關功能。 或者，將「 [驗證管理員](../users-groups-roles/directory-assign-admin-roles.md#authentication-administrator) 」角色指派給個人，讓他們能夠重設非密碼認證或讀取和設定 Azure 服務健康狀態，是合理的。

若要深入瞭解，請參閱 [Azure Active Directory 中的系統管理員角色許可權](../users-groups-roles/directory-assign-admin-roles.md)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 監視混合式元件 (Azure AD Connect 同步處理、AD FS) 

Azure AD Connect 和 AD FS 是很重要的元件，可能會破壞生命週期管理和驗證，最後導致中斷。 因此，您應該部署 Azure AD Connect Health 來監視和報告這些元件。

若要深入瞭解，請參閱使用 Azure AD Connect Health 的「讀取 [監視器 AD FS](./how-to-connect-health-adfs.md)。

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure 監視器收集資料記錄以進行分析

[Azure 監視器](../../azure-monitor/overview.md) 是適用于所有 Azure AD 記錄的統一監視入口網站，可提供深入見解、先進的分析及智慧型機器學習。 使用 Azure 監視器，您可以使用入口網站中的計量和記錄，也可以透過 Api 來取得資源的狀態和效能更高的可見度。 它可在入口網站中啟用單一的半透明體驗，同時透過 Api 和資料匯出選項來啟用各式各樣的產品整合，以支援傳統的協力廠商 SIEM 系統。 Azure 監視器也可讓您設定警示規則，以取得通知或針對影響您資源的問題採取自動化動作。

![Azure 監視器](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>為您的領導和您的每天建立自訂儀表板

沒有 SIEM 解決方案的組織可以下載適用于 Azure AD 的 [Power BI 內容套件](../reports-monitoring/howto-use-azure-monitor-workbooks.md) 。 Power BI 內容套件包含預先建立的報表，可協助您瞭解使用者如何採用和使用 Azure AD 的功能，讓您可以深入瞭解目錄中的所有活動。 您也可以建立自己的 [自訂儀表板](/power-bi/service-dashboards) ，並與您的領導小組共用以報告日常活動。 儀表板很適合用來監視您的業務，並一眼就能查看所有重要的計量。 儀表板上的視覺效果可能來自一或多個基礎資料集，以及來自一或多份基礎報表。 儀表板將內部部署和雲端的資料結合在一起，提供不受資料位置限制的合併檢視。

![Power BI 自訂儀表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>瞭解支援電話驅動程式

當您依照本文中所述的方式執行混合式身分識別解決方案時，您應該會注意到支援電話的縮減。 藉由執行 Azure 的自助式密碼重設來減輕遺忘密碼和帳戶鎖定等常見問題，同時啟用自助應用程式存取權可讓使用者自行探索和要求存取應用程式，而不需依賴您的 IT 人員。

如果您不觀察支援電話的減少，建議您分析支援電話驅動程式，確認是否已正確設定 SSPR 或自助應用程式存取，或者是否有任何其他可以系統地解決的新問題。

*「在我們的數位轉型旅程中，我們需要可靠的身分識別和存取管理提供者，以在有效的生態系統之間加速但安全地整合美國、合作夥伴與雲端服務提供者;Azure AD 是提供所需功能和可見度的最佳選項，讓我們能夠偵測和回應風險。」* --- [Yazan Almasri，Aramex 的全球資訊安全總監](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>監視應用程式的使用方式來推動見解

除了探索影子 IT 之外，使用 [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) 監視組織內的應用程式使用方式，可以協助您的組織在移動時充分利用雲端應用程式的承諾。 它可協助您透過改善對活動的可見度來掌控資產，並提高跨雲端應用程式的重要資料保護。 使用 MCAS 監視您組織中的應用程式使用狀況，可協助您回答下列問題：

* 員工使用哪些待批准的應用程式來儲存資料？
* 敏感性資料儲存在雲端的位置和時機為何？
* 誰正在存取雲端中的敏感性資料？

*「有了 Cloud App Security，我們可以迅速找出異常狀況並採取動作。」* --- [Eric LePenske，資訊安全，Accenture 資深經理](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>[摘要]

有許多層面可以執行混合式身分識別解決方案，但這四個步驟的檢查清單可協助您快速完成身分識別基礎結構，讓使用者更具生產力且更安全。

* 輕鬆連接至應用程式
* 自動為每個使用者建立一個身分識別
* 安全地強化您的使用者
* 讓您的見解

我們希望這份檔在為您的組織建立強式身分識別基礎時，是很有用的藍圖。

## <a name="identity-checklist"></a>身分識別檢查清單

當您在組織中開始使用更穩固的身分識別基礎時，建議您列印下列檢查清單以供參考。

### <a name="today"></a>今天

|完成了嗎？|Item|
|:-|:-|
||針對群組 (SSPR) 試驗自助式密碼重設|
||使用 Azure AD Connect Health 監視混合式元件|
||指派操作的最低許可權管理員角色|
||使用 Microsoft Cloud App Security 探索影子 IT|
||使用 Azure 監視器收集資料記錄以進行分析|

### <a name="next-two-weeks"></a>接下來兩周

|完成了嗎？|Item|
|:-|:-|
||讓您的使用者可以使用應用程式|
||適用于所選 SaaS 應用程式的試驗 Azure AD 布建|
||針對 Azure AD Connect 設定預備伺服器，並將它保持在最新狀態|
||開始將應用程式從 ADFS 遷移至 Azure AD|
||為您的領導和您的每天建立自訂儀表板|

### <a name="next-month"></a>下個月

|完成了嗎？|Item|
|:-|:-|
||監視應用程式的使用方式來推動見解|
||試驗安全的應用程式遠端存取|
||確定所有使用者都已註冊 MFA 和 SSPR|
||啟用雲端驗證|

### <a name="next-three-months"></a>接下來三個月

|完成了嗎？|Item|
|:-|:-|
||啟用自助應用程式管理|
||啟用自助式群組管理|
||監視應用程式的使用方式來推動見解|
||瞭解支援電話驅動程式|

## <a name="next-steps"></a>後續步驟

瞭解如何使用 Azure Active Directory 的功能和這五個步驟的檢查清單，來增加安全的狀態- [保護您的身分識別基礎結構的五個步驟](https://aka.ms/securitysteps)。

瞭解 Azure AD 中的身分識別功能如何協助您加速轉換至雲端管理的管理，方法是提供解決方案和功能，讓組織能夠快速地採用和移動更多的身分識別管理，從傳統的內部部署系統，到 Azure AD 的 [Azure AD 如何為內部部署工作負載提供雲端控管的管理](https://aka.ms/cloudgoverned)。