---
title: Azure 活動目錄認證管理操作參考指南
description: 此操作參考指南描述為確保認證管理而應採取的檢查和操作
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
ms.openlocfilehash: 8b4ec003888d75a582d25feef8ed2ce010fa7996
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546241"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure 活動目錄認證管理操作參考指南

Azure AD[操作參考指南](active-directory-ops-guide-intro.md)的這一部分介紹了為保護和管理認證、定義身份驗證體驗、委派分配、測量使用方式和根據企業安全狀態定義存取策略而應採取的檢查和操作。

> [!NOTE]
> 這些建議自發佈之日起是最新的,但可能會隨時間而變化。 隨著Microsoft產品和服務隨時間推移而變化,組織應持續評估其身份實踐。

## <a name="key-operational-processes"></a>關鍵操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者分配給關鍵任務

管理 Azure 活動目錄需要持續執行關鍵操作任務和進程,這些任務和進程可能不是推出專案的一部分。 設置這些任務以優化環境仍然很重要。 關鍵任務及其推薦的擁有者包括:

| Task | 擁有者 |
| :- | :- |
| 在 Azure AD 中管理單一登入 (SSO) 設定生命週期 | IAM 運營團隊 |
| 為 Azure AD 應用程式設計條件存取原則 | InfoSec 架構團隊 |
| 在 SIEM 系統中存檔登入活動 | InfoSec 運營團隊 |
| 在 SIEM 系統中存檔風險事件 | InfoSec 運營團隊 |
| 會審並調查安全報告 | InfoSec 運營團隊 |
| 會審並調查風險事件 | InfoSec 運營團隊 |
| 對標記為 Azure AD 識別保護的風險和漏洞報告的使用者進行會審並調查 | InfoSec 運營團隊 |

> [!NOTE]
> Azure AD 識別保護需要 Azure AD 高級 P2 許可證。 要尋找適合您要求的授權,請參考 [Azure AD 免費版與 Azure AD 進階的功能 。](https://azure.microsoft.com/pricing/details/active-directory/)

在查看清單時,您可能會發現需要為缺少擁有者的任務分配擁有者,或者調整擁有者與上述建議不一致的任務的擁有權。

#### <a name="owner-recommended-reading"></a>業主推薦閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>認證管理

### <a name="password-policies"></a>密碼原則

安全管理密碼是身份和訪問管理中最重要的部分之一,通常也是攻擊的最大目標。 Azure AD 支援一些功能,可説明防止攻擊成功。

使用下表查找緩解需要解決的問題的建議解決方案:

| 問題 | 建議 |
| :- | :- |
| 沒有防止弱密碼的機制 | 開啟 Azure AD[自助服務密碼重置 (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)和[密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| 沒有偵測洩露密碼的機制 | 開啟[密碼群組](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)(PHS) 以取得見解 |
| 使用 AD FS 無法移動到託管身份驗證 | 開啟[AD FS 外聯網智慧鎖定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)與/或[Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| 密碼策略使用基於複雜性的規則,如長度、多個字元集或過期 | 重新考慮微軟[推薦的做法](https://aka.ms/passwordguidance),並切換到密碼管理的方法,並部署 Azure [AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。 |
| 使用者未註冊使用多重身份驗證 (MFA) | [註冊所有使用者的安全資訊](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy),以便將其用作驗證使用者身份及其密碼的機制 |
| 沒有根據使用者風險撤銷密碼 | 部署 Azure AD[識別保護使用者風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy),以使用 SSPR 強制更改洩漏的認證的密碼 |
| 沒有智慧鎖定機制來保護惡意身份驗證免受來自已識別 IP 位址的不良參與者的攻擊 | 使用密碼哈希同步或[直通身份驗證](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)(PTA) 部署雲託管身份驗證 |

#### <a name="password-policies-recommended-reading"></a>密碼原則建議讀取

- [Azure AD 和 AD FS 最佳實務:防禦密碼噴霧攻擊 - 企業移動性和安全性](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>開啟自助服務密碼重置與密碼保護

需要更改或重置密碼的使用者是幫助台呼叫的最大數量和成本來源之一。 除了成本之外,更改密碼作為降低用戶風險的工具是改善組織安全狀況的基本步驟。

至少,建議您部署 Azure AD[自助服務密碼重置](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)(SSPR) 和本地[密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy),以實現:

- 轉移幫助台呼叫。
- 替換使用臨時密碼。
- 替換任何依賴於本地解決方案的現有自助服務密碼管理解決方案。
- [清除組織的弱密碼](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。

> [!NOTE]
> 對於具有 Azure AD 高級 P2 訂閱的組織,建議部署 SSPR 並將其用作[身份保護用戶風險策略](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)的一部分。

### <a name="strong-credential-management"></a>強大的認證

密碼本身不夠安全,無法阻止不良參與者訪問您的環境。 至少,必須啟用具有特權帳戶的任何用戶進行多重身份驗證 (MFA)。 理想情況下,您應該啟用[合併註冊](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined),並要求所有使用者使用[合併註冊體驗](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview)註冊 MFA 和 SSPR。 最終,我們建議您採用一種策略,[提供彈性](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls),以降低因不可預見的情況而鎖定的風險。

![組合使用者體驗流程](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>本地中斷身分驗證復原能力

除了簡單性和啟用洩露認證的好處外,Azure AD 密碼哈希同步 (PHS) 和 Azure MFA 允許使用者存取 SaaS 應用程式和 Office 365,儘管由於[非 Petya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)等網路攻擊導致本地中斷。 也可以在與聯合結合時啟用 PHS。 啟用 PHS 允許在聯合身份驗證服務不可用時備份身份驗證。

如果本地組織缺少中斷恢復能力策略或具有未與 Azure AD 整合的策略,則應部署 Azure AD PHS 並定義包含 PHS 的災難恢復計畫。 啟用 Azure AD PHS 將允許使用者在本地活動目錄不可用時針對 Azure AD 進行身份驗證。

![密碼哈希同步串流](./media/active-directory-ops-guide/active-directory-ops-img5.png)

要更好地瞭解身分驗證選項,請參閱為[Azure Active Directory 混合識別解決方案選擇正確的身份驗證方法](https://docs.microsoft.com/azure/active-directory/hybrid/choose-ad-authn)。

### <a name="programmatic-usage-of-credentials"></a>認證的程式設計使用

使用 PowerShell 的 Azure AD 文稿或使用 Microsoft 圖形 API 的應用程式需要安全身份驗證。 執行這些腳本和工具的憑據管理不當會增加憑據被盜的風險。 如果使用的文稿或應用程式依賴於硬編碼的密碼或密碼提示,應首先檢視設定檔或原始程式碼中的密碼,然後取代這些依賴項,並盡可能使用 Azure 託管識別、整合 Windows 身份驗證或[憑證](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-access-api-with-certificates)。 對於無法使用以前的解決方案的應用程式,請考慮使用[Azure 金鑰保管庫](https://azure.microsoft.com/services/key-vault/)。

如果您確定有具有密碼憑據的服務主體,並且不確定這些密碼認證如何由腳本或應用程式保護,請與應用程式的所有者聯繫,以更好地瞭解使用模式。

Microsoft 還建議您聯繫應用程式擁有者以瞭解使用模式(如果有具有密碼認證的服務主體)。

## <a name="authentication-experience"></a>認證體驗

### <a name="on-premises-authentication"></a>本地身分驗證

使用整合 Windows 身份驗證 (IWA) 或無縫單一登入 (SSO) 託管身份驗證(具有密碼哈希同步或直通身份驗證)的聯合身份驗證是企業網路內部使用本地域控制器的視線的最佳用戶體驗。 它最大限度地減少了憑據提示疲勞,並降低了用戶成為網路釣魚攻擊的犧牲品的風險。 如果您已經在使用與小靈通或 PTA 進行雲端管理的驗證,但使用者在本地身份驗證時仍需要鍵入其密碼,則應立即[部署無縫 SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)。 另一方面,如果您當前與最終遷移到雲託管身份驗證的計劃聯合,則應在遷移專案中實現無縫 SSO。

### <a name="device-trust-access-policies"></a>裝置信任存取原則

就像是貴組織中的使用者，裝置是您想要保護的核心身分識別。 您可以使用裝置的身分識別，隨時隨地保護您的資源。通過以下方法,對設備進行身份驗證並考慮其信任類型可提高您的安全狀態和可用性:

- 避免摩擦,例如,當設備受信任時,使用 MFA
- 封鎖對不受信任的裝置的存取
- 對 Windows 10 裝置,[無縫地為本地資源提供單一登](https://docs.microsoft.com/azure/active-directory/devices/azuread-join-sso)入 。

通過使用以下方法之一,可以將設備標識在 Azure AD 中管理,從而實現此目標:

- 組織可以使用[Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune)來管理設備並強制實施合規性策略,證明設備運行狀況,並根據設備是否符合標準設置條件訪問策略。 Microsoft Intune 可以管理 iOS 設備、Mac 桌面(透過 JAMF 整合)、Windows 桌面(本機使用 Windows 10 的行動裝置管理,並與 Microsoft 端點配置管理員共同管理)和 Android 行動裝置。
- [混合 Azure AD 聯接](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-managed-domains)在具有 Active Directory 域加入電腦設備的環境中提供群組策略或 Microsoft 終結點配置管理員的管理。 組織可以通過小靈通或具有無縫 SSO 的 PTA 部署託管環境。 將設備帶到 Azure AD 可跨雲端和本地資源的 SSO 最大限度地提高使用者工作效率,同時使您能夠透過 [條件存取](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 同時安全地存取雲端和本地資源。

如果網域加入的 Windows 裝置尚未在雲端中註冊,或者網域加入的 Windows 裝置在雲端註冊,但沒有條件存取策略,則應註冊未註冊的設備,並在這兩種情況下,在條件存取策略中使用[混合 Azure AD 聯接作為控制項](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)。

![需要混合裝置的條件存取策略中的螢幕擷取](./media/active-directory-ops-guide/active-directory-ops-img6.png)

如果您使用 MDM 或 Microsoft Intune 管理設備,但在條件存取策略中不使用裝置控制項,則建議使用[「要求裝置](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices#require-device-to-be-marked-as-compliant)」在這些政策中標記為符合控制項。

![需要裝置合規性的條件存取策略中的設定螢幕擷取](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>裝置信任存取原則建議讀取

- [如何:規劃混合 Azure 活動目錄聯接實現](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
- [身分識別與裝置存取設定](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello 企業版

在 Windows 10[中,適用於企業的 Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)在 PC 上使用強的雙重身份驗證替換密碼。 適用於企業的 Windows Hello 為使用者提供了更簡化的 MFA 體驗,並減少了您對密碼的依賴。 如果您尚未開始推出 Windows 10 設備,或者僅部分部署它們,我們建議您升級到 Windows 10,並在所有設備上[啟用適用於企業的 Windows Hello。](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)

如果要瞭解有關無密碼身份驗證的更多內容,請參閱[沒有密碼的 Azure 活動目錄 的世界](https://aka.ms/passwordlessdoc)。

## <a name="application-authentication-and-assignment"></a>應用程式認證及分配

### <a name="single-sign-on-for-apps"></a>套用的單一登入

為整個企業提供標準化的單一登錄機制對於最佳用戶體驗、降低風險、報告能力和治理至關重要。 如果使用支援 SSO 與 Azure AD 的應用程式,但目前配置為使用本地帳戶,則應將這些應用程式重新配置為將 SSO 與 Azure AD 一起使用。 同樣,如果使用 Azure AD 支援 SSO 但正在使用其他識別提供者的任何應用程式,則應將這些應用程式重新配置為在 Azure AD 中使用 SSO。 對於不支援聯合協定但支援以表單的身份驗證的應用程式,我們建議您將應用程式設定為使用 Azure AD 應用程式代理使用[密碼保存 。](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

![基於 AppProxy 密碼的登入](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 如果您沒有機制來發現組織中的非託管應用程式,我們建議您使用雲訪問安全代理解決方案 (CASB)(如[Microsoft 雲端應用安全](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security))實現發現過程。

最後,如果您有 Azure AD 應用程式庫並使用支援 SSO 的應用程式與 Azure AD 一起,我們建議您[在應用程式中列出應用程式](https://docs.microsoft.com/azure/active-directory/develop/howto-app-gallery-listing)。

#### <a name="single-sign-on-recommended-reading"></a>單一登入推薦閱讀

- [什麼是應用程式存取和 Azure 的目錄的單一登入](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>將 AD FS 應用程式移轉到 Azure AD

[將應用從 AD FS 遷移到 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure)可實現安全性、更一致的可管理性和更好的協作體驗的其他功能。 如果在 AD FS 中配置了支援 Azure AD 的 SSO 的應用程式,則應將這些應用程式重新配置為將 SSO 與 Azure AD 一起使用。 如果在 AD FS 中配置了 Azure AD 不支援的不常見配置的應用程式,則應與應用擁有者聯繫以瞭解特殊配置是否屬於應用程式的絕對要求。 如果不需要,則應將應用程式重新配置為將 SSO 與 Azure AD 一起使用。

![Azure AD 作為主識別提供者](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [ADFS 的 Azure AD 連接執行狀況](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)可用於收集有關可能遷移到 Azure AD 的每個應用程式的配置詳細資訊。

### <a name="assign-users-to-applications"></a>將使用者配置給應用程式

最好使用組映射[使用者,](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)因為它們允許更大的靈活性和大規模管理的能力。 使用群組的好處包括[根據屬性的動態群組成員身份](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)與[委派給應用程式擁有者](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-accessmanagement-managing-group-owners)。 因此,如果您已經在使用和管理組,我們建議您採取以下操作來大規模改進管理:

- 將組管理和治理委派給應用程式擁有者。
- 允許自助訪問應用程式。
- 定義動態組,如果用戶屬性可以一致地確定對應用程式的訪問。
- 使用[Azure AD 訪問審核](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)對用於應用程式訪問的組實施證明。

另一方面,如果您發現具有分配給單個使用者的應用程式,請確保圍繞這些應用程式實現[治理](https://docs.microsoft.com/azure/active-directory/governance/index)。

#### <a name="assign-users-to-applications-recommended-reading"></a>將使用者配置給建議讀取的應用程式

- [將使用者和群組指派至 Azure Active Directory 中的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [在 Azure 活動目錄中委派應用註冊權限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Azure Active Directory 中群組的動態成員資格規則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>存取原則

### <a name="named-locations"></a>具名位置

借助 Azure AD 中的[命名位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations),可以在組織中標記受信任的 IP 位址範圍。 Azure AD 使用具名位置來：

- 防止風險事件中出現誤報。 從受信任的網路位置登錄可降低使用者的登錄風險。
- 設定[基於位置的條件存取](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)。

![具名位置](./media/active-directory-ops-guide/active-directory-ops-img10.png)

根據優先權,使用下表查找最符合組織需求的建議解決方案:

| **優先順序** | **案例** | **建議** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | 如果您使用 PHS 或 PTA,並且尚未定義命名位置 | 定義命名位置以改善風險事件的偵測 |
| 2 | 如果您是聯合的,並且不使用「內部公司網路」聲明,並且尚未定義命名位置 | 定義命名位置以改善風險事件的偵測 |
| 3 | 如果在條件存取策略中不使用命名位置,並且條件存取策略中沒有風險或裝置控制項 | 將條件存取原則設定為包含命名位置 |
| 4 | 如果您聯合使用「內部公司網路」聲明,並且尚未定義命名位置 | 定義命名位置以改善風險事件的偵測 |
| 5 | 如果使用具有 MFA 的可信 IP 位址,而不是指定位置,並將其標記為受信任 | 定義命名位置並將其標記為受信任位置,以改進風險事件的檢測 |

### <a name="risk-based-access-policies"></a>基於風險的存取原則

Azure AD 可以計算每個登錄和每個用戶的風險。 在訪問策略中使用風險作為標準可以提供更好的用戶體驗,例如,減少身份驗證提示,提高安全性,例如,僅在需要使用者時提示使用者,並自動執行回應和補救。

![登入風險原則](./media/active-directory-ops-guide/active-directory-ops-img11.png)

如果您已經擁有支援在存取策略中使用風險的 Azure AD 高級 P2 許可證,但未使用它們,我們強烈建議為您的安全狀況增加風險。

#### <a name="risk-based-access-policies-recommended-reading"></a>建議閱讀基於風險的存取原則

- [如何：設定登入風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)
- [如何：設定使用者風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)

### <a name="client-application-access-policies"></a>用戶端應用程式存取原則

Microsoft Intune 應用程式管理 (MAM) 能夠將資料存儲加密、PIN、遠端存儲清理等數據保護控制推送到相容的用戶端行動應用程式(如Outlook Mobile)。 此外,還可以創建條件訪問策略,[以限制](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)從已批准或相容的應用存取雲服務(如 Exchange Online)。

如果您的員工安裝支援 MAM 的應用程式(如 Office 移動應用)來造訪公司資源(如線上交換或 SharePoint Online),並且您也支援 BYOD(自帶設備),我們建議您部署應用程式 MAM 策略,以便在個人擁有的設備中管理應用程式配置,而無需 MDM 註冊,然後更新條件訪問策略,僅允許支援 MAM 的用戶端進行訪問。

![條件存取控制](./media/active-directory-ops-guide/active-directory-ops-img12.png)

如果員工針對公司資源安裝支援 MAM 的應用程式,並且訪問受限於 Intune 託管設備上,則應考慮部署應用程式 MAM 策略來管理個人設備的應用程式配置,並更新條件存取策略,以便僅允許來自支援 MAM 的用戶端進行訪問。

### <a name="conditional-access-implementation"></a>條件存取實作

條件訪問是改善組織安全狀態的重要工具。 因此,遵循以下最佳實踐非常重要:

- 確保所有 SaaS 應用程式至少應用了一個策略
- 避免將 **「所有應用**」篩選器與**區**塊控制相結合,以避免鎖定風險
- 避免使用**所有使用者**作為篩選器,並無意中添加**來賓**
- **將所有「遺留」策略遷移到 Azure 門戶**
- 擷取使用者、裝置和應用程式的所有條件
- 使用條件存取策略[的 MFA,](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)而不是使用**每個使用者的 MFA**
- 具有一個應用程式來套用多個應用程式的核心原則
- 定義空異常群組並將它們新增到策略中,使其具有異常原則
- 規劃沒有 MFA 控制的[碎玻璃](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)帳戶
- 確保跨 Office 365 客戶端應用程式(例如團隊、業務 OneDrive、Outlook 等)獲得一致的體驗。透過服務(如線上交換和共享點線上)實現同一元件
- 政策分配應通過小組而不是個人執行
- 定期檢查策略中使用的異常組,以限制使用者退出安全狀態的時間。 如果您擁有 Azure AD P2,則可以使用存取審核來自動執行該過程

#### <a name="conditional-access-recommended-reading"></a>條件存取建議讀取

- [Azure 活動目錄中的條件存取最佳做法](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [身分識別與裝置存取設定](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure 活動目錄條件存取設定參考](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [一般條件式存取原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>存取表面積

### <a name="legacy-authentication"></a>舊版驗證

強大的認證(如MFA)無法使用舊版身份驗證協定保護應用,這使得它成為惡意參與者的首選攻擊媒介。 鎖定舊版身份驗證對於改善訪問安全狀態至關重要。

舊版身份驗證是一個術語,指應用使用的身份驗證協定,例如:

- 不使用現代身份驗證的舊 Office 用戶端(例如,Office 2010 用戶端)
- 使用郵件協定(如 IMAP/SMTP/POP)的用戶端

攻擊者非常喜歡這些協定 - 事實上,近[100% 的密碼噴霧攻擊](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)使用舊版身份驗證協定! 駭客使用舊版身份驗證協議,因為他們不支援互動式登錄,這是其他安全挑戰(如多重身份驗證和設備身份驗證)所必需的。

如果遺留身份驗證在環境中廣泛使用,則應計劃儘快將舊版客戶端遷移到支援[現代身份驗證](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)的用戶端。 在同一令牌中,如果某些使用者已經使用現代身份驗證,但其他使用者仍使用舊身份驗證,則應執行以下步驟來鎖定舊身份驗證用戶端:

1. 使用[登入活動報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)識別仍在使用舊版身份驗證的使用者並計劃修正:

   a. 升級到支援現代身份驗證的用戶端到受影響的使用者。
   
   b. 計劃一個截止時間範圍,以鎖定以下步驟。
   
   c. 確定哪些舊應用程式對舊版身份驗證具有硬依賴性。 請參閱下面的步驟 3。

2. 為不使用舊版 aus 的使用者禁用來源(例如 Exchange 郵箱)的舊協定,以避免更多曝光。
3. 對於其餘帳戶(理想情況下是非人工標識(如服務帳戶),使用[條件存取限制身份驗證後的舊協定](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417)。

#### <a name="legacy-authentication-recommended-reading"></a>建議讀取舊身份驗證

- [開啟或關閉 POP3 或 IMAP4 對 Exchange 伺服器中信箱的存取](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>同意授予

在非法同意授予攻擊中,攻擊者創建 Azure AD 註冊應用程式,請求訪問數據,如聯絡資訊、電子郵件或文檔。 使用者在登陸惡意網站時,可能會通過網路釣魚攻擊授予惡意應用程式的同意。

以下是具有 Microsoft 雲服務權限的應用清單:

- 具有應用或委派\*的應用。讀取寫入權限
- 具有委派權限的應用程式可以代表使用者讀取、發送或管理電子郵件
- 使用以下權限的應用程式:

| 資源 | 權限 |
| :- | :- |
| Office 365 Exchange Online | 大通。存取使用者.所有 |
| | EWS.存取使用者.所有 |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | 郵件.閱讀.共用 |
| | 郵件.閱讀寫作 |

- 應用授予已登錄使用者的完整使用者類比。 例如：

|資源 | 權限 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

為了避免這種情況,您應該參考[Office 365 中檢測和修復非法同意授予](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants),以識別和修復任何具有非法贈款或具有超過必要授權的應用程式的應用程式。 其次,[完全取消自助服務](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent),[建立治理程式](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)。 最後,安排對應用許可權的定期審核,並在不需要時將其刪除。

#### <a name="consent-grants-recommended-reading"></a>同意給建議閱讀

- [微軟圖形 API 權限](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>使用者與群組設定

下面是如果沒有明確業務需求,可以鎖定的使用者和組設置:

#### <a name="user-settings"></a>使用者設定

- **外部使用者**- 外部協作可以通過團隊、Power BI、在線共用點和 Azure 資訊保護等服務在企業中有機地進行。 如果您有用於控制使用者啟動的外部協作的顯式約束,建議使用[Azure AD 授權管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)或控制操作(如透過幫助台)啟用外部使用者。 如果不想允許為服務進行有機外部協作,則可以[阻止成員完全邀請外部使用者](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations)。 或者,您還可以在外部使用者邀請中[允許或阻止特定網域](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list)。
- **應用註冊**- 啟用應用註冊時,最終使用者可以自行註冊應用程式並授予對其數據的許可權。 應用註冊的典型示例是啟用 Outlook 外掛程式的使用者,或者啟用 Alexa 和 Siri 等語音助理,以便閱讀其電子郵件和日曆或代表他們發送電子郵件。 如果客戶決定關閉應用註冊,InfoSec 和 IAM 團隊必須參與異常管理(根據業務需求需要的應用註冊),因為他們需要使用管理員帳戶註冊應用程式,並且很可能需要設計一個流程來操作流程。
- **管理門戶**- 組織可以鎖定 Azure 門戶中的 Azure AD 邊欄選項卡,以便非管理員無法訪問 Azure 門戶中的 Azure AD 管理並感到困惑。 跳到 Azure AD 管理門戶中的使用者設定以限制存取:

![管理門戶受限存取](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 非管理員仍可以通過命令列和其他程式設計介面存取 Azure AD 管理介面。

#### <a name="group-settings"></a>群組設定

**自助服務組管理/用戶可以創建安全組/O365 組。** 如果雲中的組當前沒有自助服務計劃,客戶可能會決定將其關閉,直到他們準備好使用此功能。

#### <a name="groups-recommended-reading"></a>群組建議閱讀

- [什麼是 Azure Active Directory B2B 共同作業？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [將應用程式與 Azure 活動目錄整合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Azure 活動目錄中的應用、許可權和同意。](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [使用群組管理對 Azure 活動目錄中資源的存取](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [在 Azure 活動目錄中設定自助服務應用程式存取管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>來自非預期的量

攻擊者來自世界各地。 通過使用以位置為條件的條件訪問策略來管理此風險。 條件訪問策略[的位置條件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)使您能夠阻止對沒有業務原因從中登錄的位置進行訪問。

![建立新命名位置](./media/active-directory-ops-guide/active-directory-ops-img14.png)

如果可用,請使用安全資訊和事件管理 (SIEM) 解決方案來分析和尋找跨區域的存取模式。 如果不使用 SIEM 產品,或者它未從 Azure AD 中引入身份驗證資訊,我們建議您使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)來標識跨區域的訪問模式。

## <a name="access-usage"></a>存取使用方式

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD 紀錄已存檔並與事件回應計畫整合

Azure AD 的登錄活動、審核和風險事件對於故障排除、使用方式分析和取證調查至關重要。 Azure AD 通過保留期有限的 REST API 提供對這些源的訪問。 安全資訊和事件管理 (SIEM) 系統或等效存檔技術是長期存儲審核和支援的關鍵。 要啟用 Azure AD 紀錄的長期儲存,必須將它們新增到現有的 SIEM 解決方案或使用 Azure[監視器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor)。 可用作事件回應計劃和調查的一部分的存檔日誌。

#### <a name="logs-recommended-reading"></a>紀錄建議讀取

- [Azure Active Directory 稽核 API 參考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Azure Active Directory 登入活動報告 API 參考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [使用 Azure AD 報告 API 搭配憑證來取得資料](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Azure 的活動目錄識別保護的 Microsoft 圖像](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Office 365 管理活動 API 參考](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [如何使用 Azure Active Directory Power BI 內容套件](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>摘要

安全標識基礎結構有12個方面。 此清單將説明您進一步保護和管理認證、定義身份驗證體驗、委派分配、測量使用方式以及根據企業安全狀態定義存取策略。

- 將擁有者分配給關鍵任務。
- 實施解決方案以檢測弱密碼或洩露的密碼,改進密碼管理和保護,並進一步保護使用者訪問資源。
- 管理設備標識,隨時保護您的資源,不受任何位置的影響。
- 實現無密碼身份驗證。
- 在整個組織中提供標準化的單一登錄機制。
- 將應用從 AD FS 遷移到 Azure AD,以實現更好的安全性和更一致的可管理性。
- 通過使用組將使用者分配給應用程式,以允許更大的靈活性和大規模管理的能力。
- 配置基於風險的訪問策略。
- 鎖定舊版身份驗證協定。
- 發現並補救非法同意授予。
- 鎖定使用者和組設置。
- 啟用 Azure AD 日誌的長期存儲,以便進行故障排除、使用方式分析和取證調查。

## <a name="next-steps"></a>後續步驟

開始使用[識別管理操作檢查與操作](active-directory-ops-guide-govern.md)。
