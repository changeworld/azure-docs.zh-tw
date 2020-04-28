---
title: 強式身分識別基礎的四個步驟-Azure AD
description: 本主題說明混合式身分識別客戶可採取的四個步驟，以建立強身份識別基礎。
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
ms.openlocfilehash: d3eb98f543e17981be0d5b9ab08fa4e146659b47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74206787"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>具有 Azure Active Directory 的強身份識別基礎的四個步驟

管理應用程式和資料的存取權不會再依賴傳統的網路安全性界限策略，例如周邊網路和防火牆，因為應用程式可以快速地移至雲端。 現在，組織必須信任其身分識別解決方案，以控制誰和誰可以存取組織的應用程式和資料。 有更多組織允許員工攜帶自己的裝置來工作，並將其裝置從任何可連線到網際網路的地方使用。 在組織選擇執行的身分識別解決方案中，確保這些裝置符合規範且安全已成為一項重要考慮。 在現今的數位工作場所，身分識別是任何移至雲端之組織的[主要控制平面](https://www.microsoft.com/security/technology/identity-access-management?rtc=1)。

在採用 Azure Active Directory （Azure AD）混合式身分識別解決方案時，組織會取得高階功能的存取權，透過自動化、委派、自助式和單一登入功能來解除生產力。 它可讓您的工作者從任何需要執行工作的地方存取公司資源，同時讓您的 IT 小組能夠適當地存取適當的資源來建立安全的產能，藉此管理該存取權。

根據我們的學習，此最佳做法檢查清單可協助您快速部署建議的動作，以在您的組織中建立*強大*的身分識別基礎：

* 輕鬆連接到應用程式
* 自動為每位使用者建立一個身分識別
* 安全地強化您的使用者
* 讓您的見解

## <a name="step-1---connect-to-apps-easily"></a>步驟 1-輕鬆連接到應用程式

藉由將您的應用程式與 Azure AD 連線，您可以藉由啟用單一登入（SSO）並進行使用者布建，來改善使用者生產力和安全性。 藉由在單一位置管理您的應用程式，Azure AD，您可以將系統管理負擔降至最低，並針對您的安全性和合規性政策達成單一控制點。

本節涵蓋的選項可讓您管理使用者對應用程式的存取、啟用內部應用程式的安全遠端存取，以及將您的應用程式遷移至 Azure AD 的優點。

### <a name="make-apps-available-to-your-users-seamlessly"></a>讓您的使用者順暢地使用應用程式

Azure AD 可讓系統管理員[將應用程式新增](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)至[Azure 入口網站](https://portal.azure.com/)中的企業應用程式資源庫。 將應用程式新增至 [企業應用程式] 資源庫，可讓您更輕鬆地將應用程式設定為使用 Azure AD 做為身分識別提供者。 此外，它也可讓您使用條件式存取原則來管理使用者對應用程式的存取權，並設定應用程式的單一登入（SSO），讓使用者不必重複輸入密碼，而且會自動登入內部部署和雲端式應用程式。

一旦將應用程式新增至 Azure AD 資源庫之後，使用者就可以查看指派給他們的應用程式，並視需要搜尋並要求其他應用程式。 Azure AD 提供[數種方法](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)讓使用者存取其應用程式：

* 存取面板/我的應用程式
* Office 365 應用程式啟動器
* 直接登入同盟應用程式
* 直接登入連結

若要深入瞭解使用者對應用程式的存取，請參閱這篇文章中的**步驟 3--讓您的使用者**更容易。

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>將應用程式從 Active Directory 同盟服務遷移至 Azure AD

將單一登入設定從 Active Directory 同盟服務（ADFS）遷移至 Azure AD 可提供安全性的額外功能、更一致的管理性和共同作業。 為獲得最佳結果，建議您將應用程式從 AD FS 遷移至 Azure AD。 將您的應用程式驗證和授權帶入 Azure AD 可提供下列優點：

* 管理成本
* 管理風險
* 提高生產力
* 解決合規性與治理

若要深入瞭解，請參閱將[您的應用程式遷移至 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)白皮書。

### <a name="enable-secure-remote-access-to-apps"></a>啟用對應用程式的安全遠端存取

[Azure AD 應用程式 Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)提供簡單的解決方案，讓組織將內部部署應用程式發佈至雲端，以供需要以安全方式存取內部應用程式的遠端使用者使用。 單一登入 Azure AD 之後，使用者可以透過外部 Url 或內部應用程式入口網站來存取雲端和內部部署應用程式。

Azure AD 應用程式 Proxy 提供下列優點：

* 將 Azure AD 擴充至內部部署資源
  * 雲端級別安全性和保護
  * 可輕鬆啟用的功能，例如條件式存取和多重要素驗證
* 周邊網路中沒有任何元件（例如 VPN 和傳統的反向 proxy 解決方案）
* 沒有所需的輸入連線
* 雲端和內部部署中跨裝置、資源和應用程式的單一登入（SSO）
* 讓終端使用者隨時隨地都能享有生產力

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>使用 Microsoft Cloud App Security 探索影子 IT

現代企業 IT 部門通常不會知道使用者用來執行其工作的所有雲端應用程式。 當 IT 系統管理員詢問他們認為員工使用多少雲端應用程式時，平均為30或40。 實際上，您組織中的員工會使用超過1000個不同的應用程式。 80% 的員工使用未獲批准的應用程式，但沒有人已審核，而且可能不符合您的安全性與合規性原則。

[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) （MCAS）可協助您找出有用的應用程式，讓使用者可以批准並新增至企業應用程式庫，讓使用者受益于 SSO 和條件式存取等功能。

<em>「**Cloud App Security**可協助我們確保我們的人員可以使用雲端和 SaaS 應用程式，以支援有助於保護 Accenture 的基本安全性原則。」</em> --- [John Blasi，管理總監，資訊安全性，Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

除了偵測影子 IT 之外，MCAS 也可以判斷應用程式的風險層級，防止未經授權存取公司資料、可能的資料洩漏，以及應用程式中的其他固有安全性風險。

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>步驟 2-自動為每位使用者建立一個身分識別

將內部部署和雲端式目錄一起帶入 Azure AD 混合式身分識別解決方案，可讓您在雲端中布建現有的身分識別，以重複使用現有的內部部署 Active Directory 投資。 解決方案會同步處理內部部署身分識別與 Azure AD，同時讓內部部署 Active Directory 以任何現有的治理解決方案執行，做為身分識別的主要來源。 Microsoft 的 Azure AD 混合式身分識別解決方案涵蓋內部部署和雲端式的功能，並建立通用的使用者身分識別，以便驗證和授權所有資源（不論其位置為何）。

整合您的內部部署目錄與 Azure AD 可讓您的使用者更具生產力，並藉由提供通用身分識別來存取雲端和內部部署資源，以避免使用者跨應用程式和服務使用多個帳戶。 使用多個帳戶是終端使用者和 IT 的痛苦點。 從使用者的觀點來看，擁有多個帳戶表示必須記住多個密碼。 為了避免這種情況，許多使用者會針對每個帳戶重複使用相同的密碼，這在安全性的觀點上是不正確的。 從 IT 的觀點來看，重複使用通常會導致更多的密碼重設和技術服務人員成本，以及終端使用者抱怨。

Azure AD Connect 是用來將內部部署身分識別同步至 Azure AD 的工具，然後可以用來存取雲端應用程式。 一旦識別 Azure AD 之後，即可布建至 SaaS 應用程式（例如 Salesforce 或 Concur）。

在本節中，我們會列出針對雲端提供高可用性、新式驗證，以及減少內部部署使用量的建議。

> [!NOTE]
> 如果您想要深入瞭解 Azure AD Connect，請參閱[什麼是 Azure AD Connect 同步？](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis)

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>設定 Azure AD Connect 的預備伺服器，並讓它保持在最新狀態

Azure AD Connect 在布建流程中扮演重要角色。 如果同步處理伺服器因任何原因而離線，內部部署的變更將不會在雲端中更新，並會對使用者造成存取問題。 請務必定義容錯移轉策略，讓系統管理員能夠在同步處理伺服器離線後快速繼續同步處理。

若要在您的主要 Azure AD Connect 伺服器離線時提供高可用性，建議您為 Azure AD Connect 部署個別的[預備伺服器](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-staging-server)。 部署伺服器可讓系統管理員透過簡單的設定交換器，將預備伺服器「升級」到生產環境。 在預備模式中設定待命伺服器也可讓您測試和部署新的設定變更，並在解除委任舊的伺服器時引進新的伺服器。

> [!TIP]
> Azure AD Connect 會定期更新。 因此，強烈建議您將預備伺服器保持在最新的階段，以利用每個新版本所提供的效能改進、bug 修正和新功能。

### <a name="enable-cloud-authentication"></a>啟用雲端驗證

具有內部部署 Active Directory 的組織應該使用 Azure AD Connect，將其目錄延伸至 Azure AD，並設定適當的驗證方法。 為您的組織[選擇正確的驗證方法](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)，是將應用程式移至雲端的第一步。 這是很重要的元件，因為它會控制對所有雲端資料和資源的存取。

在 Azure AD 中啟用內部部署目錄物件的雲端驗證最簡單且建議的方法，是啟用[密碼雜湊同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)處理（PHS）。 或者，某些組織可能會考慮啟用[傳遞驗證](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)（PTA）。

無論您選擇 PHS 或 PTA，別忘了啟用[無縫單一登入](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)，以允許使用者在您的公司網路上使用 Windows 7 和8部裝置時，不需要在應用程式中經常輸入其使用者名稱和密碼，即可存取雲端應用程式。 若無單一登入，使用者必須記住應用程式特定的密碼，並登入每個應用程式。 同樣地，IT 人員也需要為每個應用程式建立和更新使用者帳戶，例如 Office 365、Box 和 Salesforce。 使用者必須記住他們的密碼，再加上登入每個應用程式的時間。 為整個企業提供標準化的單一登入機制，對於最佳使用者體驗、降低風險、回報和治理的能力非常重要。

對於已經使用 AD FS 或其他內部部署驗證提供者的組織而言，隨著身分識別提供者移至 Azure AD 會降低複雜性並提升可用性。 除非您有使用同盟的特定使用案例，否則我們建議您從同盟驗證遷移至 PHS 和無縫 SSO 或 PTA 和無縫 SSO，以享有降低的內部部署使用量和雲端所提供的彈性，並提升使用者體驗。 如需詳細資訊，請參閱[從同盟遷移至密碼雜湊同步處理以進行 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/plan-migrate-adfs-password-hash-sync)。

### <a name="enable-automatic-deprovisioning-of-accounts"></a>啟用自動解除布建帳戶

針對您的應用程式啟用自動布建和解除布建，是在多個系統上管理身分識別生命週期的最佳策略。 Azure AD 支援將使用者帳戶[自動化、以原則為基礎的布建和](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-automatic-user-provisioning-portal)解除布建到各種熱門的 SaaS 應用程式（例如 ServiceNow 和 Salesforce），以及其他可執行[SCIM 2.0 通訊協定](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)的專案。 不同于需要自訂程式碼或手動上傳 CSV 檔案的傳統布建解決方案，布建服務會裝載在雲端中，而且功能預先整合的連接器可以使用 Azure 入口網站進行設定和管理。 自動解除布建的一個主要優點是，它會在離開組織時，立即從關鍵 SaaS 應用程式移除使用者的身分識別，以協助保護您的組織。

如需深入了解自動化使用者佈建，請參閱 [自動化使用 Azure Active Directory 對於 SaaS 應用程式的使用者佈建和解除佈建](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

## <a name="step-3---empower-your-users-securely"></a>步驟 3-讓您的使用者安全地進行

在現今的數位工作場所，請務必平衡安全性與生產力。 不過，終端使用者常常會在安全性措施上推送，以降低其生產力和雲端應用程式的存取權。 為了協助解決這個情況，Azure AD 提供自助功能，讓使用者維持生產力，同時將系統管理負擔降到最低。

本節列出的建議可讓您的使用者在保持不間斷的狀態下，移除組織中的摩擦。

### <a name="enable-self-service-password-reset-for-all-users"></a>為所有使用者啟用自助式密碼重設

Azure 的[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)（SSPR）提供一個簡單的方法，讓 IT 系統管理員能夠在不需要系統管理員介入的情況下，重設和解除鎖定其密碼或帳戶。 系統包含詳細的報告，可追蹤使用者何時存取系統與通知來警示您誤用或濫用。

根據預設，Azure AD 可以在執行密碼重設時解除鎖定帳戶。 不過，當您啟用[內部部署 Azure AD Connect 整合](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#on-premises-integration)時，您也可以選擇分隔這兩項作業，讓使用者可以解除鎖定其帳戶，而不需要重設密碼。

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>確保所有使用者都已註冊 MFA 和 SSPR

Azure 提供的報告可供您和您的組織使用，以確保使用者已註冊 MFA 和 SSPR。 尚未註冊的使用者可能必須在此程式中獲得教育。

MFA 登[入報告](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-reporting)包含 mfa 使用量的相關資訊，並可讓您深入瞭解 mfa 在您組織中的運作方式。 存取 Azure AD 的登入活動（以及審核和風險偵測）對於疑難排解、使用方式分析和辯論調查很重要。

同樣地，您也可以使用「[自助式密碼管理」報告](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting)來判斷誰（或尚未註冊 SSPR）。

### <a name="self-service-app-management"></a>自助應用程式管理

在您的使用者可以從其存取面板自行探索應用程式之前，您必須啟用您想要允許使用者自行探索並要求存取權的任何應用程式的[自助式應用程式存取權](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-manage-self-service-access)。 自助應用程式存取是一種絕佳的方式，可讓使用者自行探索應用程式，並選擇性地允許商務群組核准這些應用程式的存取權。 您可以允許商務群組從其存取面板直接管理指派給這些使用者的[密碼單一登入應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery#configure-password-sso-for-an-azure-ad-gallery-app)的認證。

### <a name="self-service-group-management"></a>自助式群組管理

使用群組時，將使用者指派給應用程式是最佳的對應，因為它們可提供極大的彈性和大規模管理的能力：

* 以屬性為基礎的使用動態群組成員資格
* 委派給應用程式擁有者

Azure AD 可讓您使用安全性群組和 Office 365 群組來管理資源的存取權。 這些群組可以由可核准或拒絕成員資格要求並委派群組成員資格控制的群組擁有者來管理。 這項功能稱為[自助式群組管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-self-service-management)，可讓未獲派系統管理角色的群組擁有者建立和管理群組，而不需要依賴系統管理員來處理其要求，藉此節省時間。

## <a name="step-4---operationalize-your-insights"></a>步驟 4-讓您的見解

安全性相關事件和相關警示的審核和記錄是有效原則的重要元件，可確保使用者保持生產力，而且您的組織是安全的。 安全性記錄和報告可協助回答下列問題：

* 您使用的是您所支付的費用嗎？
* 我的租使用者中是否有任何可疑或惡意的情況？
* 安全性事件期間受到影響的物件為何？

安全性記錄和報告會提供可疑活動的電子記錄，並協助您偵測可能指出從外部嘗試或成功滲透網路以及內部攻擊的模式。 您可以使用審核來監視使用者活動、記載法規合規性、進行法庭分析等等。 警示會提供安全性事件通知。

### <a name="assign-least-privileged-admin-roles-for-operations"></a>指派最低許可權管理員角色以進行作業

當您考慮到作業的方法時，有幾個要考慮的管理層級。 第一個層級會將系統管理負擔放在您的全域管理員。 一律使用全域管理員角色，可能適用于較小的公司。 但是對於具有技術支援人員和負責特定工作之系統管理員的大型組織而言，指派全域管理員的角色可能會造成安全性風險，因為它會讓這些人員能夠管理高於和超過其應該能夠執行的工作。

在此情況下，您應該考慮下一個層級的系統管理。 使用 Azure AD，您可以將終端使用者指定為「受限的系統管理員」，以在較低許可權的角色中管理工作。 例如，您可以將「[安全性讀取](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#security-reader)者」角色指派給技術支援人員，讓他們能夠以唯讀存取權來管理安全性相關的功能。 或者，將[驗證系統管理員](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#authentication-administrator)角色指派給個人，讓他們能夠重設非密碼認證或讀取和設定 Azure 服務健康狀態，是合理的做法。

若要深入瞭解，請參閱[Azure Active Directory 中的系統管理員角色許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>使用 Azure AD Connect Health 監視混合式元件（Azure AD Connect 同步處理、AD FS）

Azure AD Connect 和 AD FS 是很重要的元件，可能會中斷生命週期管理和驗證，而且最終會導致中斷。 因此，您應該部署 Azure AD Connect Health，以便監視和報告這些元件。

若要深入瞭解，請參閱[使用 Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)的 [讀取監視器] AD FS。

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>使用 Azure 監視器收集資料記錄以進行分析

[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)是適用于所有 Azure AD 記錄的統一監視入口網站，可提供深入解析、先進分析和智慧型機器學習。 透過 Azure 監視器，您可以在入口網站中使用計量和記錄，並透過 Api 取用，以深入瞭解資源的狀態和效能。 它會在入口網站中啟用單一的玻璃體驗，同時透過支援傳統協力廠商 SIEM 系統的 Api 和資料匯出選項，啟用各種產品整合。 Azure 監視器也能讓您設定警示規則，以取得通知，或對影響資源的問題採取自動化動作。

![Azure 監視器](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>為您的領導和每天建立自訂儀表板

沒有 SIEM 解決方案的組織可以下載 Azure AD 的[Power BI 內容套件](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-power-bi-content-pack)。 Power BI 內容套件包含預先建立的報表，可協助您瞭解您的使用者如何採用及使用 Azure AD 功能，讓您深入瞭解目錄中的所有活動。 您也可以建立自己的[自訂儀表板](https://docs.microsoft.com/power-bi/service-dashboards)，並與您的領導小組共用，以報告每日活動。 儀表板是一種很好的方式，可以監視您的業務，並一目了然地查看所有最重要的計量。 儀表板上的視覺效果可能來自一或多個基礎資料集，以及來自一或多份基礎報表。 儀表板將內部部署和雲端的資料結合在一起，提供不受資料位置限制的合併檢視。

![Power BI 自訂儀表板](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>瞭解您的支援電話驅動程式

當您依照本文所述的方式執行混合式身分識別解決方案時，您最終應該會注意到您的支援電話已減少。 您可以藉由執行 Azure 的自助式密碼重設來減輕忘記密碼和帳戶鎖定等常見問題，同時啟用自助式應用程式存取可讓使用者自行探索並要求存取應用程式，而不需要依賴您的 IT 人員。

如果您不認為支援呼叫減少，建議您在嘗試確認 SSPR 或自助應用程式存取權是否已正確設定，或是否有任何其他可系統地解決的新問題時，分析您的支援電話驅動程式。

*「在我們的數位轉型旅程中，我們需要可靠的身分識別和存取管理提供者，以促進我們、合作夥伴和雲端服務提供者之間順暢且安全的整合，以提供有效的生態系統;Azure AD 是提供我們所需功能和可見度的最佳選擇，讓我們能夠偵測和回應風險。」* --- [Yazan Almasri，Aramex 的全域資訊安全總監](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>監視您的應用程式使用方式以驅動見解

除了探索影子 IT 外，使用[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)監視組織中的應用程式使用量，可以協助您的組織在移動時充分利用雲端應用程式的承諾。 它可以讓您透過改良的活動可見度來控制您的資產，並提升雲端應用程式的重要資料保護。 使用 MCAS 監視組織中的應用程式使用量，可協助您回答下列問題：

* 員工使用哪些待批准應用程式來儲存資料？
* 機密資料在何處和何時會儲存在雲端？
* 誰正在存取雲端中的敏感性資料？

*「有了 Cloud App Security，我們可以快速找出異常並採取動作」。* --- [Eric LePenske，資深經理，資訊安全，Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>摘要

執行混合式身分識別解決方案有許多層面，但這四個步驟的檢查清單可協助您快速完成身分識別基礎結構，讓使用者更具生產力且更安全。

* 輕鬆連接到應用程式
* 自動為每位使用者建立一個身分識別
* 安全地強化您的使用者
* 讓您的見解

我們希望這份檔是為您的組織建立強身份識別基礎的實用藍圖。

## <a name="identity-checklist"></a>身分識別檢查清單

我們建議您在開始旅程到組織中更穩固的身分識別基礎時，列印下列檢查清單以供參考。

### <a name="today"></a>Today

|完成了嗎？|項目|
|:-|:-|
||試驗群組的自助式密碼重設（SSPR）|
||使用 Azure AD Connect Health 監視混合式元件|
||指派最低許可權管理員角色以進行操作|
||使用 Microsoft Cloud App Security 探索影子 IT|
||使用 Azure 監視器收集資料記錄以進行分析|

### <a name="next-two-weeks"></a>接下來的兩周

|完成了嗎？|項目|
|:-|:-|
||讓您的使用者可以使用應用程式|
||適用于所選 SaaS 應用程式的試驗 Azure AD 布建|
||設定 Azure AD Connect 的預備伺服器，並讓它保持在最新狀態|
||開始將應用程式從 ADFS 遷移至 Azure AD|
||為您的領導和每天建立自訂儀表板|

### <a name="next-month"></a>下個月

|完成了嗎？|項目|
|:-|:-|
||監視您的應用程式使用方式以驅動見解|
||試驗安全的應用程式遠端存取|
||確保所有使用者都已註冊 MFA 和 SSPR|
||啟用雲端驗證|

### <a name="next-three-months"></a>接下來三個月

|完成了嗎？|項目|
|:-|:-|
||啟用自助式應用程式管理|
||啟用自助式群組管理|
||監視您的應用程式使用方式以驅動見解|
||瞭解您的支援電話驅動程式|

## <a name="next-steps"></a>後續步驟

瞭解如何使用 Azure Active Directory 的功能來增加您的安全狀態，以及此五步驟檢查清單-用[來保護身分識別基礎結構的五個步驟](https://aka.ms/securitysteps)。

瞭解 Azure AD 中的身分識別功能如何藉由提供解決方案和功能來協助您加速轉換到雲端管控的管理，讓組織能夠快速採用並將其身分識別管理從傳統內部部署系統移至 Azure AD- [Azure AD 如何為內部部署工作負載提供雲端控管的管理](https://aka.ms/cloudgoverned)。
