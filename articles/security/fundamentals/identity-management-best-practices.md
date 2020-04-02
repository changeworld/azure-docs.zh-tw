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
ms.openlocfilehash: ffd9919092cdf2481767e58f10ba6525d56ca4a8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548463"
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

撰寫本文的目的是提供一個通用路線圖,以我們的"[保護身份基礎結構安全 5 個步驟](steps-secure-identity.md)"為指導的部署後,提供更強大的安全態勢的一般路線圖,該清單將引導您瀏覽我們的一些核心功能和服務。

意見和技術會隨著時間改變，這篇文章將會定期進行更新以反映這些變更。

本文討論的 Azure 身分識別管理和存取控制安全性最佳作法包括：

* 將身分識別視為主要安全性周邊
* 集中管理身分識別
* 管理連線的租戶
* 啟用單一登入
* 開啟條件存取
* 排程常規安全改進
* 啟用密碼管理
* 對使用者強制執行多重要素驗證
* 使用角色型存取控制
* 降低特殊權限帳戶的暴露風險
* 控制資源所在的位置
* 使用 Azure AD 進行儲存認證

## <a name="treat-identity-as-the-primary-security-perimeter"></a>將身分識別視為主要安全性周邊

許多人認為身分識別是安全性的主要周邊。 這種看法擺脫了傳統以網路安全性為主的觀點。 網路周邊持續變得更容易滲透，而且周邊防禦已不如 [BYOD](https://aka.ms/byodcg) 裝置和雲端應用程式遽增之前那麼有效。

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) 是用於管理身分識別和存取的 Azure 解決方案。 Azure AD 是 Microsoft 提供的多租用戶雲端式目錄和身分識別管理服務。 它將核心目錄服務、應用程式存取管理及身分識別保護結合到單個解決方案。

下列各節列出使用 Azure AD 的身分識別和存取安全性最佳做法。

**最佳實踐**:圍繞使用者和服務標識進行中心安全控制和檢測。
**詳細資訊**:使用 Azure AD 將控制項和識別配置在一起。

## <a name="centralize-identity-management"></a>集中管理身分識別

在[混合式身分識別](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?)案例中，建議您整合內部部署與雲端目錄。 集成使 IT 團隊能夠從一個位置管理帳戶,而不管帳戶的創建位置如何。 集成還通過提供用於訪問雲和本地資源的共同標識,幫助使用者提高工作效率。

**最佳實務**:建立單個 Azure AD 實例。 一致性和單個權威來源將提高清晰度,並降低人為錯誤和配置複雜性的安全風險。
**詳細資訊**:指定單個 Azure AD 目錄作為公司和組織帳戶的權威源。

**最佳做法**：整合您的內部部署目錄與 Azure AD。  
**詳細資料**：使用 [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect) 同步處理內部部署目錄與雲端目錄。

> [!Note]
> 有[一些因素會影響 Azure AD 連線的效能](../../active-directory/hybrid/plan-connect-performance-factors.md)。 確保 Azure AD Connect 有足夠的容量來防止性能不佳的系統妨礙安全性和工作效率。 大型或複雜的組織(預配超過 100,000 個對象的組織)應遵循[建議](../../active-directory/hybrid/whatis-hybrid-identity.md)來優化其 Azure AD Connect 實現。

**最佳做法**:不要將帳戶同步到現有 Active Directory 實例中具有高許可權的 Azure AD。
**詳細資訊**:不要變更篩選出這些帳號的預設[Azure AD 連線設定](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)。 此配置可降低對手從雲轉向本地資產的風險(這可能會引發重大事件)。

**最佳做法**：開啟密碼雜湊同步處理。  
**詳細資訊**:密碼哈希同步是一種用於將使用者密碼哈希從本地活動目錄實例同步到基於雲的 Azure AD 實例的功能。 此同步有助於防止從以前的攻擊中重播洩露的憑據。

即使您選擇使用與 Active Directory 同盟服務 (AD FS) 或其他身分識別提供者的同盟，仍可選擇性地設定密碼雜湊同步處理作為備用方式，以防內部部署伺服器失敗或暫時無法使用。 此同步使用戶能夠使用使用與登錄到其本地活動目錄實例相同的密碼登錄到服務。 它還允許身份保護通過將同步密碼哈希值與已知被洩露的密碼進行比較來檢測受攻擊的憑據,如果使用者在未連接到 Azure AD 的其他服務上使用相同的電子郵件地址和密碼。

如需詳細資訊，請參閱[使用 Azure AD Connect 同步實作密碼雜湊同步處理](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)。

**最佳實務**:對於新應用程式開發,請使用 Azure AD 進行身份驗證。
**詳細資訊**:使用正確的功能支援身份驗證:

  - 員工 Azure AD
  - 適用於來賓使用者和外部合作夥伴的[Azure AD B2B](../../active-directory/b2b/index.yml)
  - [Azure AD B2C](../../active-directory-b2c/index.yml)可控制客戶在使用應用程式時註冊、登入及管理其設定檔的方式

未整合內部部署身分識別與雲端身分識別的組織，可能會有更多管理帳戶的額外負荷。 此額外負荷提高錯誤和安全性缺口的可能性。

> [!Note]
> 您需要選擇關鍵帳戶將駐留在哪些目錄,以及使用的管理員工作站是否由新的雲服務或現有進程管理。 使用現有的管理和身份預配過程可以降低某些風險,但也可能造成攻擊者破壞本地帳戶並轉向雲的風險。 您可能希望對不同的角色使用不同的策略(例如,IT 管理員與業務單位管理員)。 您有兩個選項。 第一個選項是建立未與本地活動目錄實例同步的 Azure AD 帳戶。 將管理員工作站加入 Azure AD,您可以使用 Microsoft Intune 管理和修補該工作站。 第二個選項是使用現有管理員帳戶,通過同步到本地活動目錄實例。 使用 Active Directory 域中的現有工作站進行管理和安全性。

## <a name="manage-connected-tenants"></a>管理連線的租戶
您的安全組織需要可見性來評估風險,並確定是否遵守了組織的策略以及任何法規要求。 應確保安全組織能夠查看連接到生產環境和網路的所有訂閱(透過[Azure ExpressRoute](../../expressroute/expressroute-introduction.md)或[站點到網站VPN)。](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) Azure AD 中的[全域管理員/公司管理員](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions)可以提升他們對[使用者存取管理員角色的訪問許可權](../../role-based-access-control/built-in-roles.md#user-access-administrator),並查看連接到環境的所有訂閱和託管組。

請參閱[提升管理所有 Azure 訂閱和管理組的訪問](../../role-based-access-control/elevate-access-global-admin.md),以確保您和安全組可以查看連接到環境的所有訂閱或管理組。 在評估風險后,應刪除此提升的訪問。

## <a name="enable-single-sign-on"></a>啟用單一登入

在行動第一、雲端第一的世界中，無論是從什麼地方，都要讓使用者能單一登入 (SSO) 至裝置、應用程式和服務，他們才能隨時隨地保有生產力。 當您有多個身分識別解決方案要管理時，這不只會成為 IT 的系統管理問題，對於必須記住多組密碼的使用者而言也是個問題。

將相同的身分識別解決方案使用於您所有的應用程式和資源，即可達成 SSO。 而不論資源位於內部部署或雲端，使用者都可以使用同一組認證來登入及存取他們所需的資源。

**最佳做法**：啟用 SSO。  
**詳細資料**：Azure AD 會[將內部部署 Active Directory 延伸](/azure/active-directory/connect/active-directory-aadconnect)至雲端。 使用者可以使用其主要公司或學校帳戶來登入已加入網域的裝置、公司資源，也能登入完成其作業所需的所有 Web 和 SaaS 應用程式。 使用者不需要記住多組使用者名稱和密碼，而且可根據其組織群組成員資格及其身為員工的狀態，自動佈建 (或解除佈建) 其應用程式存取權。 而且，您可以透過 [Azure AD 應用程式 Proxy](/azure/active-directory/active-directory-application-proxy-get-started) 控制資源庫應用程式或您已開發並發佈之自有內部部署應用程式的存取權。

使用 SSO 讓使用者根據其在 Azure AD 中的公司或學校帳戶存取其 [SaaS 應用程式](/azure/active-directory/active-directory-appssoaccess-whatis)。 這不只適用於 Microsoft SaaS 應用程式，也適用於其他應用程式，例如 [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) 和 [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial)。 您可以將應用程式設定為使用 Azure AD 作為 [SAML 型身分識別](/azure/active-directory/fundamentals-identity)提供者。 為了控制安全性，Azure AD 不會核發允許使用者登入應用程式的權杖，除非他們已透過 Azure AD 獲得存取權。 您可以直接授與存取權，或透過使用者所屬的群組授與。

未建立通用身分識別來對使用者和應用程式建立 SSO 的組織，更容易遭遇使用者有多組密碼的情況。 這些情況會提高使用者重複使用密碼或使用弱式密碼的可能性。

## <a name="turn-on-conditional-access"></a>開啟條件存取

使用者可以使用各種裝置和應用程式，從任何位置存取您組織的資源。 作為 IT 管理員,您希望確保這些設備符合您的安全和合規性標準。 只將焦點放在誰可以存取資源，已不再足夠。

為了平衡安全性和工作效率,您需要考慮如何存取資源,然後才能對訪問控制做出決策。 使用 Azure AD 條件訪問,可以滿足此要求。 借助條件訪問,您可以根據訪問雲應用的條件做出自動存取控制決策。

**最佳做法**：管理和控制公司資源的存取權。  
**詳細資訊**:根據 SaaS 應用與 Azure AD 連線套用的群組、位置和應用程式敏感度設定 Azure AD[條件存取](/azure/active-directory/active-directory-conditional-access-azure-portal)。

**最佳實踐**:阻止舊版身份驗證協定。
**詳細資訊**:攻擊者每天都在利用舊協定中的弱點,尤其是密碼噴射攻擊。 配置條件訪問以阻止舊協定。 有關詳細資訊,請參閱影片[Azure AD:執行和不做](https://www.youtube.com/watch?v=wGk0J4z90GI)。

## <a name="plan-for-routine-security-improvements"></a>排程常規安全改進

安全性始終在不斷發展,在雲和身份管理框架中構建一種定期顯示增長和發現保護環境的新方法非常重要。

身份安全分數是 Microsoft 發布的一組建議的安全控制,旨在為您提供數值分數,以客觀地衡量您的安全狀況並幫助規劃未來的安全改進。 您還可以查看與其他行業的分數相比,以及您自己的趨勢。

**最佳實踐**:根據您所在行業的最佳實踐規劃常規安全審查和改進。
**詳細資訊**:使用身份安全分數功能對隨時間的提升進行排名。

## <a name="enable-password-management"></a>啟用密碼管理

如果您有多個租用戶或想要讓使用者[重設其密碼](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)，請務必使用適當的安全性原則來防止不當使用。

**最佳做法**：為客戶設定自助式密碼重設 (SSPR)。  
**詳細資料**：使用 Azure AD 的[自助式密碼重設](/azure/active-directory-b2c/active-directory-b2c-reference-sspr)功能。

**最佳做法**：監視 SSPR 的使用方式或是否真的正在使用它。  
**詳細資料**：使用 Azure AD [密碼重設註冊活動報告](/azure/active-directory/active-directory-passwords-get-insights)，監視正在註冊的使用者。 Azure AD 提供的報告功能可協助您使用預先建立的報告來回答問題。 如果您已適當地取得授權，則也可以建立自訂查詢。

**最佳實務**:將基於雲端的密碼策略擴展到本地基礎結構。
**詳細資訊**:通過對本地密碼更改執行與基於雲的密碼更改相同的檢查來增強組織中的密碼策略。 為本地 Windows 伺服器活動目錄代理安裝[Azure AD 密碼保護](/azure/active-directory/authentication/concept-password-ban-bad),以將禁用的密碼清單擴展到現有基礎結構。 更改、設置或重置本地密碼的使用者和管理員必須遵守與僅雲使用者相同的密碼策略。

## <a name="enforce-multi-factor-verification-for-users"></a>對使用者強制執行多重要素驗證

建議您要求所有使用者都使用雙步驟驗證。 這包括系統管理員，以及組織中帳戶遭到入侵時會造成重大影響的其他人員 (例如財務人員)。

有很多選項可供您要求使用雙步驟驗證。 最適合您的選擇取決於您的目標、您正在執行的 Azure AD 版本，以及您的授權方案。 請參閱[如何要求使用者使用雙步驟驗證](/azure/active-directory/authentication/howto-mfa-userstates)，以判斷最適合您的選項。 如需有關授權和定價的詳細資訊，請參閱 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 和 [Azure Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 定價頁面。

以下是啟用雙步驟驗證的選項和優點：

**選項 1:** 使用 Azure AD 安全性預設值**為**所有使用者啟用 MFA 和登入方法:這個選項使您能夠透過嚴格的策略輕鬆快速地為環境中的所有使用者強制實施 MFA,以便:

* 質疑管理帳戶和管理登錄機制
* 要求透過 Microsoft 身份驗證器向所有使用者提供 MFA 質詢
* 限制舊版身份驗證協定。

此方法可用於所有許可層,但無法與現有的條件訪問策略混合使用。 您可以在 Azure AD 安全性預設值中找到更多資訊

**選項 2:**[使用變更使用者狀態開啟多重身份認證](../../active-directory/authentication/howto-mfa-userstates.md)。   
**優點**：這是要求使用雙步驟驗證的傳統方法。 同時適用於[雲端與 Azure Multi-Factor Authentication Server 中的 Azure Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-whichversion)。 使用此方法要求使用者在每次登錄並覆蓋條件訪問策略時執行兩步驗證。

要確定需要啟用多重身份驗證的位置,請參閱[哪個版本的 Azure MFA 適合我的組織?](/azure/active-directory/authentication/concept-mfa-whichversion)

**選項 3:**[使用條件存取策略啟用多重身份驗證](/azure/active-directory/authentication/howto-mfa-getstarted)。
**優點**:此選項允許您使用[條件訪問](/azure/active-directory/active-directory-conditional-access-azure-portal)在特定條件下提示進行兩步驗證。 特定條件可以是使用者從不同的位置、不受信任的裝置，或您認為有危險的應用程式登入。 定義您要求使用雙步驟驗證的特定條件，可讓您避免要持續提示使用者，這可能會帶來不愉快的使用者體驗。

這是最具彈性的方法，可為您的使用者啟用雙步驟驗證。 啟用條件存取策略僅適用於雲端中的 Azure 多重身份驗證,是 Azure AD 的進階功能。 您可以在[部署雲端式 Azure Multi-Factor Authentication](/azure/active-directory/authentication/howto-mfa-getstarted)中找到這個方法的詳細資訊。

**選項 4:** 通過評估[Azure AD 標識保護](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)的使用者和登入風險,使用條件存取策略啟用多重身份驗證。   
**優點**：此選項可讓您：

* 偵測會影響貴組織身分識別的潛在弱點。
* 針對偵測到的與您組織的身分識別有關的可疑動作，設定自動回應。
* 調查可疑事件並採取適當動作以解決它們。

這個方法使用 Azure AD Identity Protection 風險評估，根據所有雲端應用程式的使用者和登入風險來判斷是否需要雙步驟驗證。 這個方法需要 Azure Active Directory P2 授權。 您可以在 [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview) 中找到這個方法的詳細資訊。

> [!Note]
> 選項 1 透過更改使用者狀態啟用多重身份驗證,將覆蓋條件存取策略。 由於選項 2 和選項 3 使用條件訪問策略,因此不能將選項 1 與它們一起使用。

未新增額外身分識別保護層 (例如雙步驟驗證) 的組織比較容易遭受認證竊取攻擊。 認證竊取攻擊可能會導致資料洩漏。

## <a name="use-role-based-access-control"></a>使用角色型存取控制

對於使用雲的任何組織而言,雲資源的訪問管理都至關重要。 [基於角色的存取控制 (RBAC)](/azure/role-based-access-control/overview)可説明您管理有權存取 Azure 資源的人員、他們可以對這些資源執行哪些操作以及他們有權存取哪些區域。

指定在 Azure 中負責特定功能的組或各個角色有助於避免可能導致人為和自動化錯誤產生安全風險的混淆。 對於想要強制執行資料存取安全性原則的組織，根據[需要知道](https://en.wikipedia.org/wiki/Need_to_know)和[最低權限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)安全性原則限制存取權限是必須做的事。

您的安全團隊需要查看 Azure 資源,以便評估和修復風險。 如果安全團隊具有運營職責,則他們需要額外的許可權才能完成工作。

您可以使用[RBAC](/azure/role-based-access-control/overview)將許可權分配給特定範圍內的使用者、組和應用程式。 角色指派的範圍可以是訂用帳戶、資源群組或單一資源。

**最佳實踐**:隔離團隊內部的職責,並僅授予使用者執行工作所需的訪問許可權。 不允許在 Azure 訂閱或資源中向所有人授予不受限制的許可權,而應僅允許特定範圍內的某些操作。
**詳細資訊**:使用 Azure[中的內建 RBAC 角色](/azure/role-based-access-control/built-in-roles)向使用者分配許可權。

> [!Note]
> 特定許可權會產生不必要的複雜性和混亂,累積到"遺留"配置中,無需害怕破壞某些內容,就很難修復。 避免資源特定的許可權。 相反,將管理組用於企業範圍的許可權和資源組,以訪問訂閱中的許可權。 避免使用者特定許可權。 相反地，請將存取權指派給 Azure AD 中的群組。

**最佳實務**:授予具有 Azure 職責的安全團隊查看 Azure 資源,以便他們可以評估和修復風險。
**詳細資訊**:授予安全團隊 RBAC[安全讀取器](/azure/role-based-access-control/built-in-roles#security-reader)角色。 您可以使用根管理組或部門管理組,具體取決於職責範圍:

* 負責所有企業資源的團隊**的根管理團隊**
* 具有有限範圍的團隊的**細分管理組**(通常由於監管或其他組織邊界)

**最佳實踐**:向負有直接運營職責的安全團隊授予適當的許可權。
**詳細資訊**:查看 RBAC 內置角色以進行適當的角色分配。 如果內建角色不能滿足組織的特定需求,則可以[為 Azure 資源建立自訂角色](/azure/role-based-access-control/custom-roles)。 與內置角色一樣,您可以在訂閱、資源組和資源作用域的使用者、組和服務主體中分配自定義角色。

**最佳實務**:授予 Azure 安全中心對需要它的安全角色的許可權。 安全中心允許安全團隊快速識別和補救風險。
**詳細資訊**:將具有這些需求的安全團隊添加到 RBAC[安全管理員](/azure/role-based-access-control/built-in-roles#security-admin)角色,以便他們可以查看安全策略、查看安全狀態、編輯安全策略、查看警報和建議以及關閉警報和建議。 可以使用根管理組或段管理組執行此操作,具體取決於職責範圍。

不使用 RBAC 等功能強制實施數據存取控制的組織可能會給其用戶帶來比所需的許可權更多的許可權。 這可以通過允許使用者訪問他們不應具有的數據類型(例如,高業務影響)導致數據洩露。

## <a name="lower-exposure-of-privileged-accounts"></a>降低特殊權限帳戶的暴露風險

保護特殊權限存取是保護企業資產很重要的第一個步驟。 將能夠存取安全資訊或資源的人數降到最低，可以降低惡意使用者取得該存取權，或者授權使用者無意中影響到敏感資源的機率。

特殊權限帳戶是可管理 IT 系統的帳戶。 網路攻擊者會以這些帳戶為目標，來取得組織資料和系統的存取權。 為了保護特殊權限存取，您應該讓帳戶和系統遠離遭遇惡意使用者的風險。

我們建議您擬定並遵循適當計劃以保護特殊權限存取，使網路攻擊者無法取得。 如需有關如何擬定詳細的藍圖，以保護 Azure AD、Microsoft Azure、Office 365 和其他雲端服務所管理或報告的身分識別和存取權，請檢閱[在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)。

以下摘要說明[在 Azure AD 中保護混合式部署和雲端部署的特殊權限存取](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)中找到的最佳做法：

**最佳做法**： 管理、控制及監視特殊權限帳戶的存取權。   
**詳細資料**：開啟 [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)。 開啟 Privileged Identity Management 之後，您會收到有關於特殊權限存取角色有所變更的通知電子郵件訊息。 您目錄中的高特殊權限角色新增了其他使用者時，這些通知將會提供早期警告。

**最佳實務**:確保所有關鍵管理帳戶都受 Azure AD 帳戶管理。
**詳細資訊**:從關鍵管理員角色中刪除任何使用者帳戶(例如,microsoft 帳戶,如hotmail.com、live.com 和outlook.com)。

**最佳實踐**:確保所有關鍵管理員角色都有單獨的管理任務帳戶,以避免網路釣魚和其他攻擊損害管理許可權。
**詳細資訊**:創建一個單獨的管理帳戶,該帳戶已分配執行管理任務所需的許可權。 阻止將這些管理帳戶用於日常生產力工具,如Microsoft Office 365電子郵件或任意 Web 流覽。

**最佳做法**：識別及分類高特殊權限角色中的帳戶。   
**詳細資料**：在開啟 Azure AD Privileged Identity Management 之後，檢視具備全域管理員、特殊權限角色管理員和其他較高特殊權限角色的使用者。 請移除這些角色中不再需要的任何帳戶，並將指派給管理員角色的其餘帳戶分類：

* 個別指派給系統管理使用者，並且可用於非系統管理用途 (例如個人電子郵件)
* 個別指派給系統管理使用者，且指定為僅供系統管理之用
* 在多個使用者之間共用
* 用於緊急存取案例
* 用於自動化指令碼
* 用於外部使用者

**最佳做法**：實作 Just-In-Time (JIT) 存取，可進一步降低權限的暴露時間，並提升使用特殊權限帳戶的能見度。   
**詳細資料**：Azure AD Privileged Identity Management 可讓您：

* 限制使用者只能 JIT 取用其權限。
* 指派縮短持續時間的角色，而且有信心會自動撤銷權限。

**最佳做法**：定義至少兩個緊急存取帳戶。   
**詳細資料**：緊急存取帳戶可協助組織限制現有 Azure Active Directory 環境內的特殊權限存取。 這些帳戶具有高特殊權限，不會指派給特定個人。 緊急存取帳戶僅限用於無法使用一般系統管理帳戶的情況。 組織必須將緊急帳戶的使用量限制於僅只必要的時間量。

請評估已指派或適用於全域管理員角色的帳戶。 如果使用 `*.onmicrosoft.com` 網域 (供緊急存取使用)，並未看到任何僅限雲端的帳戶，請加以建立。 如需詳細資訊，請參閱[在 Azure AD 中管理緊急存取系統管理帳戶](/azure/active-directory/users-groups-roles/directory-emergency-access)。

**最佳實踐**:在緊急情況下,有一個"碎玻璃"程式。
**詳細資訊**:按照[在 Azure AD 中保護混合部署和雲端部署的特權存取](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)的步驟。

**最佳實踐**:要求所有關鍵管理員帳戶無密碼(首選),或需要多重身份驗證。
**詳細資訊**:使用[Microsoft 身份驗證器應用](/azure/active-directory/authentication/howto-authentication-phone-sign-in)無需使用密碼即可登錄到任何 Azure AD 帳戶。 與[適用於企業的 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)一樣,Microsoft 身份驗證器使用基於密鑰的身份驗證來啟用綁定到設備並使用生物識別身份驗證或 PIN 的使用者憑據。

要求所有永久分配給一個或多個 Azure AD 管理員角色的個人使用者在登錄時進行 Azure 多重身份驗證:全域管理員、特權角色管理員、交換連線管理員和 SharePoint 連線管理員。 [為管理員帳戶啟用多重身份驗證](/azure/active-directory/authentication/howto-mfa-userstates),並確保管理員帳戶使用者已註冊。

**最佳實踐**:對於關鍵管理員帳戶,有一個不允許生產任務的管理員工作站(例如,瀏覽和電子郵件)。 這將保護您的管理員帳戶免受使用流覽和電子郵件的攻擊媒介的攻擊,並顯著降低您發生重大事件的風險。
**詳細資訊**:使用管理員工作站。 選擇工作站安全等級:

- 高度安全的生產力設備為流覽和其他工作效率任務提供了高級安全性。
- [特權訪問工作站 (PAW)](/windows-server/identity/securing-privileged-access/privileged-access-workstations)提供專用作業系統,可抵禦互聯網攻擊和敏感任務的威脅媒介。

**最佳實踐**:當員工離開您的組織時,取消預配管理帳戶。
**詳細資訊**:制定一個流程,在員工離開您的組織時禁用或刪除管理員帳戶。

**最佳實踐**:使用當前攻擊技術定期測試管理員帳戶。
**詳細資訊**:使用 Office 365 攻擊模擬器或第三方產品在組織中運行實際的攻擊方案。 這可以説明您在發生實際攻擊之前找到易受攻擊的使用者。

**最佳做法**：採取行動來減輕最常用的攻擊技巧。  
**詳細資料**：[識別系統管理角色中需要切換至工作或學校帳戶的 Microsoft 帳戶](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[確認全域系統管理員帳戶的個別使用者帳戶和郵件轉寄](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[確訂系統管理帳戶的密碼近期做過變更](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[開啟密碼哈希同步](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[所有具有特殊權限角色的使用者和公開的使用者，都必須進行多重要素驗證](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[取得您的 Office 365 安全分數 (如果使用 Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[檢閱 Office 365 安全性與合規性指引 (如果使用 Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[設定 Office 365 活動監視 (如果使用 Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[建立事件/緊急回應計劃擁有者](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[保護內部部署的特殊權限系統管理帳戶](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

如果您不保護特殊權限的存取，則可能發現您有太多具備較高特殊權限角色的使用者，而且比較容易遭受攻擊。 包括網路攻擊者在內的惡意人士通常會以管理帳戶和特殊權限存取的其他元素為目標，以利用認證竊取來取得敏感性資料和系統的存取權。

## <a name="control-locations-where-resources-are-created"></a>控制建立資源的位置

讓雲端操作者能夠執行工作，同時防止他們違反管理組織資源所需的慣例極為重要。 想要控制資源建立位置的組織應將這些位置硬式編碼。

您可以使用 [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) 來建立安全性原則，其定義會描述明確拒絕的動作或資源。 在所需範圍內指派那些原則定義，例如訂用帳戶、資源群組或是個別的資源。

> [!NOTE]
> 安全性原則與 RBAC 不同。 這類原則實際上使用 RBAC 來授權使用者建立這些資源。
>
>

不控制資源建立方式的組織，比較容易遇到使用者因建立超過所需資源而濫用服務的情況。 強化資源建立程序是保護多租用戶案例的重要步驟。

## <a name="actively-monitor-for-suspicious-activities"></a>主動監視可疑的活動

主動身分識別監視系統可快速偵測可疑行為並觸發警示，以便進一步調查。 下表列出兩項可協助組織監視其身分識別的 Azure AD 功能：

**最佳做法**：想辦法識別：

- 嘗試在[不被追蹤](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources)的情況下登入。
- 針對特定帳戶的[暴力密碼破解](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures)攻擊。
- 嘗試從多個位置登入。
- [來自受感染設備的](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices)登錄。
- 可疑的 IP 位址。

**詳細資料**：使用 Azure AD Premium 的[異常報告](/azure/active-directory/active-directory-view-access-usage-reports)。 備妥相關處理和程序，以便 IT 系統管理員每天或依需求執行這些報告 (通常出現在事件回應案例)。

**最佳做法**：採用主動監視系統，該系統會將風險通知您，並可針對您的業務需求調整風險層級 (高、中或低)。   
**詳細資料**：使用[Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection)，它會在自己的儀表板上標示目前的風險，並透過電子郵件傳送每日摘要通知。 若要協助保護貴組織的身分識別，您可設定以風險為基礎的原則，以在達到指定的風險層級時自動回應偵測到的問題。

未主動監視其身分識別系統的組織有洩漏使用者認證的風險。 若不知道有透過這些認證進行的可疑活動，組織便無法減輕這類型的威脅。

## <a name="use-azure-ad-for-storage-authentication"></a>使用 Azure AD 進行儲存認證
[Azure 儲存](/azure/storage/common/storage-auth-aad)支援使用 Azure AD 進行 Blob 儲存和佇列儲存的身份驗證和授權。 使用 Azure AD 認證,可以使用基於 Azure 角色的存取控制向使用者、組和應用程式授予特定許可權,但權限應歸為單個 Blob 容器或佇列的範圍。

我們建議您使用 Azure [AD 儲存的存取認證 。](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

## <a name="next-step"></a>後續步驟

如需更多安全性最佳做法，請參閱 [Azure 安全性最佳做法與模式](best-practices-and-patterns.md)，以便在使用 Azure 設計、部署和管理雲端解決方案時使用。
