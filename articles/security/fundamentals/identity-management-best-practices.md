---
title: Azure 身分識別和存取安全性的最佳做法 | Microsoft Docs
description: 本文提供使用內建 Azure 功能的一些身分識別管理和存取控制最佳作法。
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 23a0932141d9ddb0fec16a0ce007ee57bb0f1a5b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410193"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure 身分識別管理和存取控制安全性最佳作法

本文會討論一系列的 Azure 身分識別管理和存取控制安全性最佳做法。 這些最佳作法衍生自我們的 [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) 經驗和客戶的經驗。

針對每個最佳做法，我們會說明︰

* 最佳作法是什麼
* 您為何想要啟用該最佳作法
* 如果無法啟用最佳作法，結果可能為何
* 最佳作法的可能替代方案
* 如何學習啟用最佳作法

這篇「Azure 身分識別管理和存取控制安全性最佳作法」是以共識意見及 Azure 平台功能和特性集 (因為在撰寫本文時已存在) 為基礎。

撰寫本文的目的是要在您根據「[保護身分識別基礎結構的 5 個步驟](steps-secure-identity.md)」(這會引導您完成一些核心功能和服務) 的指導完成部署後，提供能實現更健全安全性狀態的一般藍圖。

意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure 身分識別管理和存取控制安全性最佳作法包括：

* 將身分識別視為主要安全性周邊
* 集中管理身分識別
* 管理已連線的租用戶
* 啟用單一登入
* 開啟條件式存取
* 規劃例行的安全性改進
* 啟用密碼管理
* 對使用者強制執行多重要素驗證
* 使用角色型存取控制
* 降低特殊權限帳戶的暴露風險
* 控制資源所在的位置
* 使用 Azure AD 來驗證儲存體

## <a name="treat-identity-as-the-primary-security-perimeter"></a>將身分識別視為主要安全性周邊

許多人認為身分識別是安全性的主要周邊。 這種看法擺脫了傳統以網路安全性為主的觀點。 網路周邊持續變得更容易滲透，而且周邊防禦已不如 [BYOD](/mem/intune/fundamentals/byod-technology-decisions) 裝置和雲端應用程式遽增之前那麼有效。

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用於管理身分識別和存取的 Azure 解決方案。 Azure AD 是 Microsoft 提供的多租用戶雲端式目錄和身分識別管理服務。 它將核心目錄服務、應用程式存取管理及身分識別保護結合到單個解決方案。

下列各節列出使用 Azure AD 的身分識別和存取安全性最佳做法。

**最佳做法** ：以使用者和服務的身分識別為中心進行安全性控制和偵測。
**詳細資料** ：使用 Azure AD 來共置控制和身分識別。

## <a name="centralize-identity-management"></a>集中管理身分識別

在[混合式身分識別](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?)案例中，建議您整合內部部署與雲端目錄。 不論在何處建立帳戶，整合都可讓您的 IT 團隊集中管理帳戶。 此外，整合可提供一個通用身分識別來存取雲端和內部部署資源，讓使用者變得更有生產力。

**最佳做法** ：建立單一 Azure AD 執行個體。 一致性和單一授權來源會提高透明度，減少人為錯誤和設定複雜性帶來的安全性風險。
**詳細資料** ：指定單一 Azure AD 目錄作為公司和組織帳戶的授權來源。

**最佳做法** ：整合您的內部部署目錄與 Azure AD。  
**詳細資料** ：使用 [Azure AD Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) 同步處理內部部署目錄與雲端目錄。

> [!Note]
> 有各種[會影響 Azure AD Connect 效能的因素](../../active-directory/hybrid/plan-connect-performance-factors.md)。 請確保 Azure AD Connect 有足夠的容量，以免表現不佳的系統妨礙安全性和生產力。 大型或複雜的組織 (佈建超過 100,000 個物件的組織) 應遵循[建議](../../active-directory/hybrid/whatis-hybrid-identity.md)，以將其 Azure AD Connect 實作最佳化。

**最佳做法** ：請勿將帳戶同步處理到在現有 Active Directory 執行個體中具有高權限的 Azure AD。
**詳細資料** ：請勿變更會篩選掉這些帳戶的預設 [Azure AD Connect 設定](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)。 此設定可降低敵人從雲端轉向內部部署資產的風險 (這可能會造成重大事件)。

**最佳做法** ：開啟密碼雜湊同步處理。  
**詳細資料** ：密碼雜湊同步處理功能可用來將使用者密碼雜湊從內部部署 Active Directory 執行個體同步處理至雲端式 Azure AD 執行個體。 這項同步處理有助於防止外洩的認證從先前的攻擊中重新執行。

即使您選擇使用與 Active Directory 同盟服務 (AD FS) 或其他身分識別提供者的同盟，仍可選擇性地設定密碼雜湊同步處理作為備用方式，以防內部部署伺服器失敗或暫時無法使用。 此同步處理可讓使用者透過其登入內部部署 Active Directory 執行個體時所使用的相同密碼來登入服務。 此外，如果有使用者在未連線至 Azure AD 的其他服務上使用相同的電子郵件地址和密碼，Identity Protection 也將能夠藉由比較已同步處理的密碼雜湊與已知遭到破解的密碼，來偵測遭破解的認證。

如需詳細資訊，請參閱[使用 Azure AD Connect 同步實作密碼雜湊同步處理](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)。

**最佳做法** ：針對新的應用程式開發，請使用 Azure AD 來進行驗證。
**詳細資料** ：使用正確的功能來支援驗證：

  - 適用於員工的 Azure AD
  - 適用於來賓使用者和外部合作夥伴的 [Azure AD B2B](../../active-directory/external-identities/index.yml)
  - [Azure AD B2C](../../active-directory-b2c/index.yml)，用來控制客戶註冊及登入的方式，並在其使用您的應用程式時管理其設定檔

未整合內部部署身分識別與雲端身分識別的組織，可能會有更多管理帳戶的額外負荷。 此額外負荷提高錯誤和安全性缺口的可能性。

> [!Note]
> 您需要選擇重要帳戶將位於哪些目錄，以及所使用的系統管理工作站會由新的雲端服務還是現有程序來管理。 使用現有的管理和身分識別佈建程序可能會降低部分風險，但也可能會造成原本危害內部部署帳戶的攻擊者轉向攻擊雲端的風險。 您可以針對不同的角色 (例如，IT 管理員與業務單位管理員) 使用不同的策略。 您有兩個選項。 第一個選項是建立不會與內部部署 Active Directory 執行個體同步處理 Azure AD 帳戶。 將系統管理工作站加入 Azure AD，以便能使用 Microsoft Intune 來進行管理和修補。 第二個選項是藉由同步處理至內部部署 Active Directory 執行個體，來使用現有的系統管理員帳戶。 請使用 Active Directory 網域中的現有工作站來進行管理並確保安全。

## <a name="manage-connected-tenants"></a>管理已連線的租用戶
您的安全性組織需要可見性才能評估風險，並判斷是否有遵循組織的原則及任何法規需求。 您應該確定安全性組織能夠了解所有連線到生產環境和網路的訂用帳戶 (透過 [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 或[站對站 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md))。 Azure AD 中的[全域管理員/公司管理員](../../active-directory/roles/permissions-reference.md#company-administrator-permissions)可以提高其對[使用者存取管理員](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色的存取權，並查看連線到您環境的所有訂用帳戶和受控群組。

請參閱[提高存取權以管理所有 Azure 訂用帳戶和管理群組](../../role-based-access-control/elevate-access-global-admin.md)，以確保您和您的安全性群組可以檢視連線到您環境的所有訂用帳戶或管理群組。 在評估過風險之後，就應該移除此提高了權限的存取權。

## <a name="enable-single-sign-on"></a>啟用單一登入

在行動第一、雲端第一的世界中，無論是從什麼地方，都要讓使用者能單一登入 (SSO) 至裝置、應用程式和服務，他們才能隨時隨地保有生產力。 當您有多個身分識別解決方案要管理時，這不只會成為 IT 的系統管理問題，對於必須記住多組密碼的使用者而言也是個問題。

將相同的身分識別解決方案使用於您所有的應用程式和資源，即可達成 SSO。 而不論資源位於內部部署或雲端，使用者都可以使用同一組認證來登入及存取他們所需的資源。

**最佳做法** ：啟用 SSO。  
**詳細資料** ：Azure AD 會 [將內部部署 Active Directory 延伸](../../active-directory/hybrid/whatis-hybrid-identity.md)至雲端。 使用者可以使用其主要公司或學校帳戶來登入已加入網域的裝置、公司資源，也能登入完成其作業所需的所有 Web 和 SaaS 應用程式。 使用者不需要記住多組使用者名稱和密碼，而且可根據其組織群組成員資格及其身為員工的狀態，自動佈建 (或解除佈建) 其應用程式存取權。 而且，您可以透過 [Azure AD 應用程式 Proxy](../../active-directory/manage-apps/application-proxy.md) 控制資源庫應用程式或您已開發並發佈之自有內部部署應用程式的存取權。

使用 SSO 讓使用者根據其在 Azure AD 中的公司或學校帳戶存取其 [SaaS 應用程式](../../active-directory/manage-apps/what-is-single-sign-on.md)。 這不只適用於 Microsoft SaaS 應用程式，也適用於其他應用程式，例如 [Google Apps](../../active-directory/saas-apps/google-apps-tutorial.md) 和 [Salesforce](../../active-directory/saas-apps/salesforce-tutorial.md)。 您可以將應用程式設定為使用 Azure AD 作為 [SAML 型身分識別](../../active-directory/fundamentals/active-directory-whatis.md)提供者。 為了控制安全性，Azure AD 不會核發允許使用者登入應用程式的權杖，除非他們已透過 Azure AD 獲得存取權。 您可以直接授與存取權，或透過使用者所屬的群組授與。

未建立通用身分識別來對使用者和應用程式建立 SSO 的組織，更容易遭遇使用者有多組密碼的情況。 這些情況會提高使用者重複使用密碼或使用弱式密碼的可能性。

## <a name="turn-on-conditional-access"></a>開啟條件式存取

使用者可以使用各種裝置和應用程式，從任何位置存取您組織的資源。 身為 IT 管理員，您會想要確保這些裝置符合您的安全性與合規性標準。 只將焦點放在誰可以存取資源，已不再足夠。

為了平衡安全性與生產力，您必須先考量資源的存取方式，才能進行存取控制決策。 有了 Azure AD 條件式存取，您就能夠因應這項需求。 使用條件式存取，您就可以根據條件做出自動化存取控制決策，以便存取雲端應用程式。

**最佳做法** ：管理和控制公司資源的存取權。  
**詳細資料** ：針對 SaaS 應用程式和連線到 Azure AD 的應用程式，根據群組、位置和應用程式敏感性來設定通用的 Azure AD [條件式存取原則](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)。

**最佳做法** ：封鎖舊版的驗證通訊協定。
**詳細資料** ：攻擊者每天都會惡意探索舊版通訊協定中的弱點，密碼噴灑攻擊尤其是如此。 請設定條件式存取來[封鎖舊版通訊協定](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md)。

## <a name="plan-for-routine-security-improvements"></a>規劃例行的安全性改進

安全性會不斷演化，因此請務必在雲端和身分識別管理架構中建置可定期顯示發展情形的辦法，並探索可用來保護環境的新方法。

身分識別安全分數是 Microsoft 所發佈的一組建議安全性控制，其作用是向您提供數值分數以便您可以客觀地估量安全性狀態，並協助您規劃未來要如何改進安全性。 您也可以檢視所獲分數與其他產業分數的比較，以及您在一段時間內的趨勢。

**最佳做法** ：根據所處產業的最佳做法來規劃例行的安全性審查和改進。
**詳細資料** ：使用「身分識別安全分數」功能來針對一段時間內的改進進行排名。

## <a name="enable-password-management"></a>啟用密碼管理

如果您有多個租用戶或想要讓使用者[重設其密碼](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)，請務必使用適當的安全性原則來防止不當使用。

**最佳做法** ：為使用者設定自助式密碼重設 (SSPR)。  
**詳細資料** ：使用 Azure AD [自助式密碼重設](../../active-directory-b2c/user-flow-self-service-password-reset.md)功能。

**最佳做法** ：監視 SSPR 的使用方式或是否真的正在使用它。  
**詳細資料** ：使用 Azure AD [密碼重設註冊活動報告](../../active-directory/authentication/howto-sspr-reporting.md)，監視正在註冊的使用者。 Azure AD 提供的報告功能可協助您使用預先建立的報告來回答問題。 如果您已適當地取得授權，則也可以建立自訂查詢。

**最佳做法** ：將雲端式密碼原則延伸至內部部署基礎結構。
**詳細資料** ：如同您對雲端式密碼變更所做的，藉由對內部部署密碼變更執行相同的檢查來強化組織的密碼原則。 在內部部署環境中安裝 Windows Server Active Directory 代理程式的 [Azure AD 密碼保護](../../active-directory/authentication/concept-password-ban-bad.md)，將禁用密碼清單擴充至現有的基礎結構。 在內部部署變更、設定或重設密碼的使用者和管理員，都必須遵循與僅限雲端使用者相同的密碼原則。

## <a name="enforce-multi-factor-verification-for-users"></a>對使用者強制執行多重要素驗證

建議您要求所有使用者都使用雙步驟驗證。 這包括系統管理員，以及組織中帳戶遭到入侵時會造成重大影響的其他人員 (例如財務人員)。

有很多選項可供您要求使用雙步驟驗證。 最適合您的選擇取決於您的目標、您正在執行的 Azure AD 版本，以及您的授權方案。 請參閱[如何要求使用者使用雙步驟驗證](../../active-directory/authentication/howto-mfa-userstates.md)，以判斷最適合您的選項。 如需有關授權和定價的詳細資訊，請參閱 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 定價頁面。

以下是啟用雙步驟驗證的選項和優點：

**選項 1** ：使用 Azure AD 安全性預設值來為所有使用者和登入方法啟用 MFA **優點** ：此選項可讓您使用嚴格的原則，輕鬆快速地為環境中的所有使用者強制執行 MFA，以便：

* 挑戰系統管理帳戶和系統管理登入機制
* 要求所有使用者都要透過 Microsoft Authenticator 進行 MFA 挑戰
* 限制舊版的驗證通訊協定。

此方法適用於所有授權層級，但不能與現有的條件式存取原則混合使用。 您可以在[Azure AD 安全性預設值](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)中找到詳細資訊

**選項 2** ： [藉由變更使用者狀態來啟用 Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-userstates.md)。   
**優點** ：這是要求使用雙步驟驗證的傳統方法。 同時適用於[雲端與 Azure Multi-Factor Authentication Server 中的 Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)。 如果使用這種方法，則會要求使用者在每次登入時執行雙步驟驗證，並且會覆寫條件式存取原則。

若要判斷哪裡需要啟用 Multi-Factor Authentication，請參閱[哪個 Azure MFA 版本適合我的組織？](../../active-directory/authentication/concept-mfa-howitworks.md)。

**選項 3** ： [透過條件式存取原則來啟用 Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)。
**優點** ：此選項可讓您使用 [條件式存取](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)，在特定條件下提示使用雙步驟驗證。 特定條件可以是使用者從不同的位置、不受信任的裝置，或您認為有危險的應用程式登入。 定義您要求使用雙步驟驗證的特定條件，可讓您避免要持續提示使用者，這可能會帶來不愉快的使用者體驗。

這是最具彈性的方法，可為您的使用者啟用雙步驟驗證。 啟用條件式存取原則，只適用於雲端的 Azure Multi-Factor Authentication，而且是 Azure AD 的進階功能。 您可以在[部署雲端式 Azure Multi-Factor Authentication](../../active-directory/authentication/howto-mfa-getstarted.md)中找到這個方法的詳細資訊。

**選項 4** ：藉由評估 [風險型條件式存取原則](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md)來為條件式存取原則啟用 Multi-Factor Authentication。   
**優點** ：此選項可讓您：

* 偵測會影響貴組織身分識別的潛在弱點。
* 針對偵測到的與您組織的身分識別有關的可疑動作，設定自動回應。
* 調查可疑事件並採取適當動作以解決它們。

這個方法使用 Azure AD Identity Protection 風險評估，根據所有雲端應用程式的使用者和登入風險來判斷是否需要雙步驟驗證。 這個方法需要 Azure Active Directory P2 授權。 您可以在 [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) 中找到這個方法的詳細資訊。

> [!Note]
> 選項2，藉由變更使用者狀態來啟用 Multi-Factor Authentication，會覆寫條件式存取原則。 因為選項3和4使用條件式存取原則，所以您無法搭配使用選項2與它們。

未新增額外身分識別保護層 (例如雙步驟驗證) 的組織比較容易遭受認證竊取攻擊。 認證竊取攻擊可能會導致資料洩漏。

## <a name="use-role-based-access-control"></a>使用角色型存取控制

對於使用雲端的任何組織而言，管理雲端資源的存取是一件非常重要的事。 [Azure 角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)可協助您管理可存取 azure 資源的人員、這些資源的用途，以及他們有權存取的區域。

指定群組或個別角色來負責處理 Azure 中的特定功能，可避免因為責任混亂而導致會產生安全性風險的人為和自動化錯誤。 對於想要強制執行資料存取安全性原則的組織，根據[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全性原則限制存取權限是必須做的事。

安全性小組必須了解您的 Azure 資源，才能評估和補救風險。 如果安全性小組肩負營運責任，則需要有額外的權限來執行其工作。

您可以使用 [RBAC](../../role-based-access-control/overview.md) 來將權限指派給特定範圍的使用者、群組及應用程式。 角色指派的範圍可以是訂用帳戶、資源群組或單一資源。

**最佳做法** ：請區隔小組內的職責，而僅授與使用者執行作業所需的存取權。 不要授與每個人 Azure 訂用帳戶或資源中無限制的權限，而是只允許在特定範圍執行特定的動作。
**詳細資料** ：使用 azure 中的 [內建角色](../../role-based-access-control/built-in-roles.md) ，將許可權指派給使用者。

> [!Note]
> 特定的權限會造成不必要的複雜性和混淆，從而累積成「舊版」設定，令人難以進行修正而不害怕造成破壞。 避免資源特定的許可權。 相反地，請針對整個企業的權限使用管理群組，並針對訂用帳戶內的權限使用資源群組。 請避免使用者特定的權限。 相反地，請將存取權指派給 Azure AD 中的群組。

**最佳做法** ：向肩負 Azure 責任的安全性小組授與查看 Azure 資源的存取權，以便其能夠評估及補救風險。
**詳細資料** ：向安全性小組授與 RBAC [安全性讀者](../../role-based-access-control/built-in-roles.md#security-reader)角色。 視責任範圍而定，您可以使用根管理群組或區段管理群組：

* **根管理群組** 適用於負責處理所有企業資源的小組
* **區段管理群組** 適用於範圍有限的小組 (通常是因為法規或其他組織界限)

**最佳做法** ：向具有直接營運責任的安全性小組授與適當權限。
**詳細資料** ：請檢閱 RBAC 內建角色以進行適當的角色指派。 如果內建角色不符合您組織的特定需求，您可以建立 [Azure 自訂角色](../../role-based-access-control/custom-roles.md)。 和內建角色一樣，您也可以將自訂角色指派給訂用帳戶、資源群組和資源範圍的使用者、群組和服務主體。

**最佳做法** ：向需要 Azure 資訊安全中心存取權的安全性角色授與此存取權。 資訊安全中心可讓安全性小組快速找出並補救風險。
**詳細資料** ：將具有這些需求的安全性小組新增至 RBAC [安全性管理員](../../role-based-access-control/built-in-roles.md#security-admin)角色，使其可以檢視安全性原則、檢視安全性狀態、編輯安全性原則、檢視警示和建議，以及解除警示和建議。 視責任範圍而定，您可以使用根管理群組或區段管理群組來執行這項操作。

未使用 RBAC 等功能來強制執行資料存取控制的組織，可能會對其使用者提供超過所需的權限。 讓使用者能夠存取其不該存取的資料類型 (例如，高度業務衝擊) 可能會導致資料洩露。

## <a name="lower-exposure-of-privileged-accounts"></a>降低特殊權限帳戶的暴露風險

保護特殊權限存取是保護企業資產很重要的第一個步驟。 將能夠存取安全資訊或資源的人數降到最低，可以降低惡意使用者取得該存取權，或者授權使用者無意中影響到敏感資源的機率。

特殊權限帳戶是可管理 IT 系統的帳戶。 網路攻擊者會以這些帳戶為目標，來取得組織資料和系統的存取權。 為了保護特殊權限存取，您應該讓帳戶和系統遠離遭遇惡意使用者的風險。

我們建議您擬定並遵循適當計劃以保護特殊權限存取，使網路攻擊者無法取得。 如需有關如何建立詳細的藍圖，以保護 Azure AD、Microsoft Azure、Microsoft 365 及其他雲端服務中所管理或報告的身分識別和存取，請參閱 [Azure AD 中的保護混合式部署和雲端部署的](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)特殊許可權存取。

以下摘要說明[在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)中找到的最佳做法：

**最佳做法** ：管理、控制及監視特殊權限帳戶的存取權。   
**詳細資料** ：開啟 [Azure AD Privileged Identity Management](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)。 開啟 Privileged Identity Management 之後，您會收到有關於特殊權限存取角色有所變更的通知電子郵件訊息。 您目錄中的高特殊權限角色新增了其他使用者時，這些通知將會提供早期警告。

**最佳做法** ：確定所有重要的系統管理員帳戶都是受控 Azure AD 帳戶。
**詳細資料** ：從重要的系統管理員角色移除任何取用者帳戶 (例如，hotmail.com、live.com 和 outlook.com 等 Microsoft 帳戶)。

**最佳做法** ：確定所有重要的系統管理員角色都使用個別的帳戶來進行系統管理工作，以免網路釣魚等攻擊危害系統管理權限。
**詳細資料** ：建立另一個系統管理員帳戶，並為其指派要執行系統管理工作所需的權限。 封鎖使用這些系統管理帳戶來取得每日生產力工具，例如 Microsoft 365 電子郵件或任意網頁流覽。

**最佳做法** ：識別及分類具備高特殊權限角色的帳戶。   
**詳細資料** ：在開啟 Azure AD Privileged Identity Management 之後，檢視具備全域管理員、特殊權限角色管理員和其他較高特殊權限角色的使用者。 請移除這些角色中不再需要的任何帳戶，並將指派給管理員角色的其餘帳戶分類：

* 個別指派給系統管理使用者，並且可用於非系統管理用途 (例如個人電子郵件)
* 個別指派給系統管理使用者，且指定為僅供系統管理之用
* 在多個使用者之間共用
* 用於緊急存取案例
* 用於自動化指令碼
* 用於外部使用者

**最佳做法** ：實作 Just-In-Time (JIT) 存取，以進一步降低權限的暴露時間，並提升使用特殊權限帳戶對您的能見度。   
**詳細資料** ：Azure AD Privileged Identity Management 可讓您：

* 限制使用者只能 JIT 取用其權限。
* 指派縮短持續時間的角色，而且有信心會自動撤銷權限。

**最佳做法** ：定義至少兩個緊急存取帳戶。   
**詳細資料** ：緊急存取帳戶可協助組織限制現有 Azure Active Directory 環境內的特殊權限存取。 這些帳戶具有高特殊權限，不會指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的情況。 組織必須將緊急帳戶的使用量限制於僅只必要的時間量。

請評估已指派或適用於全域管理員角色的帳戶。 如果使用 `*.onmicrosoft.com` 網域 (供緊急存取使用)，並未看到任何僅限雲端的帳戶，請加以建立。 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取系統管理帳戶](../../active-directory/users-groups-roles/directory-emergency-access.md)。

**最佳做法** ：備妥「急用」流程以應付緊急狀況。
**詳細資料** ：請遵循 [在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)中的步驟。

**最佳做法** ：要求所有重要的系統管理員帳戶都必須是不需要密碼的 (建議選項)，或必須使用 Multi-Factor Authentication。
**詳細資料** ：使用 [Microsoft Authenticator 應用程式](../../active-directory/authentication/howto-authentication-passwordless-phone.md)來登入任何 Azure AD 帳戶 (而不需使用密碼)。 和 [Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-identity-verification)一樣，Microsoft Authenticator 也會使用金鑰型驗證來啟用使用者認證，此認證已繫結至裝置並且會使用生物特徵辨識驗證或 PIN。

所有永久指派給一或多個 Azure AD 管理員角色的個別使用者在進行登入時，都必須進行 Azure Multi-Factor Authentication：全域管理員、特殊權限角色管理員、Exchange Online 管理員和 SharePoint Online 管理員。 啟用[管理員帳戶的 Multi-factor Authentication](../../active-directory/authentication/howto-mfa-userstates.md)，並確定管理員帳戶使用者已完成註冊。

**最佳做法** ：針對重要的系統管理員帳戶，準備不允許進行生產工作 (例如，瀏覽和電子郵件) 的系統管理工作站。 這會保護系統管理員帳戶，使其免於遭受使用瀏覽和電子郵件的攻擊媒介，並大幅降低遭遇重大事件的風險。
**詳細資料** ：使用系統管理工作站。 選擇工作站的安全性等級：

- 高安全性的生產力裝置可為瀏覽等生產力工作提供進階的安全性。
- [特殊權限存取工作站 (PAW)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 會提供敏感性工作的專用作業系統，以免遭受網際網路攻擊和威脅載體攻擊。

**最佳做法** ：當員工不再於組織中任職時，取消佈建系統管理員帳戶。
**詳細資料** ：備妥流程以在員工不再於組織中任職時停用或刪除系統管理員帳戶。

**最佳做法** ：定期使用目前的攻擊技術來測試系統管理員帳戶。
**詳細資料** ：使用 Microsoft 365 攻擊模擬器或協力廠商服務，在您的組織中執行真實的攻擊案例。 這可協助您在發生真正的攻擊之前就先找出易受攻擊的使用者。

**最佳做法** ：採取步驟來減輕由最常被使用的攻擊技巧所造成的損害。  
**詳細資料** ： [識別系統管理角色中需要切換至公司或學校帳戶的 Microsoft 帳戶](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[確認全域系統管理員帳戶的個別使用者帳戶和郵件轉寄](../../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[確訂系統管理帳戶的密碼近期做過變更](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[開啟密碼雜湊同步處理](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[所有具有特殊權限角色的使用者和公開的使用者，都必須進行多重要素驗證](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[如果使用 Microsoft 365，請 (取得 Microsoft 365 的安全分數) ](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[如果使用 Microsoft 365，請參閱 Microsoft 365 的安全性指引 () ](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[如果使用 Microsoft 365) ，請設定 Microsoft 365 活動監視 (](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[建立事件/緊急回應計劃擁有者](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[保護內部部署的特殊權限系統管理帳戶](../../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

如果您不保護特殊權限的存取，則可能發現您有太多具備較高特殊權限角色的使用者，而且比較容易遭受攻擊。 包括網路攻擊者在內的惡意人士通常會以管理帳戶和特殊權限存取的其他元素為目標，以利用認證竊取來取得敏感性資料和系統的存取權。

## <a name="control-locations-where-resources-are-created"></a>控制建立資源的位置

讓雲端操作者能夠執行工作，同時防止他們違反管理組織資源所需的慣例極為重要。 想要控制資源建立位置的組織應將這些位置硬式編碼。

您可以使用 [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 來建立安全性原則，其定義會描述明確拒絕的動作或資源。 在所需範圍內指派那些原則定義，例如訂用帳戶、資源群組或是個別的資源。

> [!NOTE]
> 安全性原則與 RBAC 不同。 這類原則實際上使用 RBAC 來授權使用者建立這些資源。
>
>

不控制資源建立方式的組織，比較容易遇到使用者因建立超過所需資源而濫用服務的情況。 強化資源建立程序是保護多租用戶案例的重要步驟。

## <a name="actively-monitor-for-suspicious-activities"></a>主動監視可疑的活動

主動身分識別監視系統可快速偵測可疑行為並觸發警示，以便進一步調查。 下表列出兩項可協助組織監視其身分識別的 Azure AD 功能：

**最佳做法** ：想辦法識別：

- 嘗試在[不被追蹤](../../active-directory/reports-monitoring/howto-find-activity-reports.md)的情況下登入。
- 針對特定帳戶的[暴力密碼破解](../../active-directory/reports-monitoring/howto-find-activity-reports.md)攻擊。
- 嘗試從多個位置登入。
- 從 [受感染的裝置登入](../../active-directory/reports-monitoring/howto-find-activity-reports.md)。
- 可疑的 IP 位址。

**詳細資料** ：使用 Azure AD Premium 的 [異常報告](../../active-directory/reports-monitoring/overview-reports.md)。 備妥相關處理和程序，以便 IT 系統管理員每天或依需求執行這些報告 (通常出現在事件回應案例)。

**最佳做法** ：採用主動監視系統，該系統會將風險通知您，並可針對您的業務需求調整風險層級 (高、中或低)。   
**詳細資料** ：使用 [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md)，它會在自己的儀表板上標示目前的風險，並透過電子郵件傳送每日摘要通知。 若要協助保護貴組織的身分識別，您可設定以風險為基礎的原則，以在達到指定的風險層級時自動回應偵測到的問題。

未主動監視其身分識別系統的組織有洩漏使用者認證的風險。 若不知道有透過這些認證進行的可疑活動，組織便無法減輕這類型的威脅。

## <a name="use-azure-ad-for-storage-authentication"></a>使用 Azure AD 來驗證儲存體
[Azure 儲存體](../../storage/common/storage-auth-aad.md)可為 Blob 儲存體和佇列儲存體支援以 Azure AD 進行驗證和授權。 透過 Azure AD 驗證，您可以使用 Azure 角色型存取控制，將特定權限授與給使用者、群組和應用程式，並向下授與到個別 Blob 容器或佇列的範圍。

建議您使用 [Azure AD 來驗證對儲存體的存取](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)。

## <a name="next-step"></a>後續步驟

如需更多安全性最佳做法，請參閱 [Azure 安全性最佳做法與模式](best-practices-and-patterns.md)，以便在使用 Azure 設計、部署和管理雲端解決方案時使用。