---
title: Azure Active Directory 驗證管理操作參考指南
description: 此操作參考指南說明保護驗證管理時應採取的檢查和動作
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
ms.openlocfilehash: f25abb70a95f559cf0cc14efa6cf9f0e81ec9ec0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80876287"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory 驗證管理操作參考指南

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明您應該採取哪些檢查和動作來保護和管理認證、定義驗證體驗、委派指派、測量使用量，以及根據企業安全性狀態定義存取原則。

> [!NOTE]
> 這些建議是在發行日期之後的最新版本，但可能會隨著時間而改變。 組織應持續評估其身分識別實務，因為 Microsoft 產品和服務會隨著時間而演進。

## <a name="key-operational-processes"></a>關鍵操作程式

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給主要工作

管理 Azure Active Directory 需要持續執行重要的作業工作和進程，這可能不是首度發行專案的一部分。 您必須設定這些工作來優化您的環境，這仍然很重要。 主要工作和其建議的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 管理 Azure AD 中單一登入（SSO）設定的生命週期 | IAM 作業小組 |
| 設計 Azure AD 應用程式的條件式存取原則 | InfoSec 架構團隊 |
| 封存 SIEM 系統中的登入活動 | InfoSec 營運小組 |
| 封存 SIEM 系統中的風險事件 | InfoSec 營運小組 |
| 分級和調查安全性報告 | InfoSec 營運小組 |
| 分級和調查風險事件 | InfoSec 營運小組 |
| 分級和調查從 Azure AD Identity Protection 標示有風險和弱點報告的使用者 | InfoSec 營運小組 |

> [!NOTE]
> Azure AD Identity Protection 需要 Azure AD Premium P2 授權。 若要尋找您需求的正確授權，請參閱 [比較 Azure AD Free 和 Azure AD Premium 版本的正式推出功能](https://azure.microsoft.com/pricing/details/active-directory/)。

當您檢查清單時，您可能會發現需要為遺漏擁有者的工作指派擁有者，或為擁有者未與上述建議一致的工作調整擁有權。

#### <a name="owner-recommended-reading"></a>擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure 中的治理](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="credentials-management"></a>認證管理

### <a name="password-policies"></a>密碼原則

安全地管理密碼是身分識別和存取管理其中一個最重要的部分，通常是攻擊的最大目標。 Azure AD 支援數個可協助防止攻擊成功的功能。

使用下表來尋找建議的解決方案，以減輕需要解決的問題：

| 問題 | 建議 |
| :- | :- |
| 沒有任何機制可防範弱式密碼 | 啟用 Azure AD[自助式密碼重設（SSPR）](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)和[密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) |
| 沒有偵測到洩漏密碼的機制 | 啟用[密碼雜湊同步](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization)處理（PHS）以取得見解 |
| 使用 AD FS 且無法移至受控驗證 | 啟用[AD FS 外部網路智慧鎖定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)和/或[Azure AD 智慧鎖定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout) |
| 密碼原則會使用以複雜度為基礎的規則，例如長度、多個字元集或過期 | 請重新考慮[Microsoft 建議做法](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf)，並將您的方法切換至密碼管理，並部署[Azure AD 密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)。 |
| 使用者未註冊為使用多重要素驗證（MFA） | [註冊所有使用者的安全性資訊](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-mfa-policy)，以便用來驗證使用者身分識別及其密碼的機制 |
| 不會根據使用者風險撤銷密碼 | 部署 Azure AD [Identity Protection 使用者風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)，以使用 SSPR 強制對洩漏的認證進行密碼變更 |
| 沒有任何智慧型鎖定機制可保護來自已識別 IP 位址的錯誤執行者的惡意驗證 | 使用密碼雜湊同步處理或[傳遞驗證](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start)（PTA）部署雲端管理的驗證 |

#### <a name="password-policies-recommended-reading"></a>建議的密碼原則閱讀

- [Azure AD 和 AD FS 最佳做法：防禦密碼噴灑攻擊-Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>啟用自助式密碼重設和密碼保護

需要變更或重設其密碼的使用者，是大量和服務台呼叫成本的最大來源之一。 除了成本之外，將密碼變更為降低使用者風險的工具是改善組織安全性狀態的基本步驟。

建議您至少部署 Azure AD[自助式密碼重設](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)（SSPR）和內部部署[密碼保護](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy)以完成下列動作：

- 轉移服務台電話。
- 取代暫時密碼的使用。
- 取代任何依賴內部部署解決方案的現有自助式密碼管理解決方案。
- [排除](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)組織中的弱式密碼。

> [!NOTE]
> 針對具有 Azure AD Premium P2 訂用帳戶的組織，建議您部署 SSPR，並將其作為身分[識別保護使用者風險原則](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)的一部分。

### <a name="strong-credential-management"></a>強式認證管理

密碼本身的保護不足以防止不良的執行者取得您的環境的存取權。 具有特殊許可權帳戶的任何使用者至少必須啟用多重要素驗證（MFA）。 在理想的情況下，您應該啟用[合併的註冊](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)，並要求所有使用者註冊 MFA 和 SSPR，並使用[結合的註冊體驗](https://docs.microsoft.com/azure/active-directory/user-help/user-help-security-info-overview)。 最後，我們建議您採用策略來[提供復原能力](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls)，以降低因未預期的情況而鎖定的風險。

![結合的使用者體驗流程](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>內部部署中斷驗證復原

除了簡單性的優點並啟用洩漏的認證偵測之外，Azure AD 密碼雜湊同步處理（PHS）和 Azure MFA 可讓使用者存取 SaaS 應用程式和 Office 365，而不論內部部署中斷是因為網路攻擊之類的[NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)。 您也可以啟用 PHS，同時與同盟搭配使用。 啟用 PHS 可在同盟服務無法使用時，允許驗證回退。

如果您的內部部署組織缺少中斷復原策略，或其中一個未與 Azure AD 整合，您應該部署 Azure AD PHS，並定義包含 PHS 的嚴重損壞修復計畫。 啟用 Azure AD PHS 可讓使用者在您的內部部署 Active Directory 無法使用時，針對 Azure AD 進行驗證。

![密碼雜湊同步處理流程](./media/active-directory-ops-guide/active-directory-ops-img5.png)

若要進一步瞭解您的驗證選項，請參閱[為您的 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](../hybrid/choose-ad-authn.md)。

### <a name="programmatic-usage-of-credentials"></a>以程式設計方式使用認證

Azure AD 使用 PowerShell 的腳本或使用 Microsoft Graph API 的應用程式，都需要安全驗證。 執行這些腳本和工具的認證管理不佳，會增加認證竊取的風險。 如果您使用的腳本或應用程式依賴硬式編碼的密碼或密碼提示，您應該先複習 config 檔案或原始程式碼中的密碼，然後取代這些相依性，並使用 Azure 受控識別、整合式 Windows 驗證或[憑證](../reports-monitoring/tutorial-access-api-with-certificates.md)（如果可能的話）。 針對不可能發生先前解決方案的應用程式，請考慮使用[Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

如果您判斷有具有密碼認證的服務主體，而且不確定腳本或應用程式如何保護這些密碼認證，請洽詢應用程式的擁有者，以進一步瞭解使用模式。

Microsoft 也建議您洽詢應用程式擁有者，以瞭解使用模式（如果有具有密碼認證的服務主體）。

## <a name="authentication-experience"></a>驗證體驗

### <a name="on-premises-authentication"></a>內部部署驗證

使用整合式 Windows 驗證（IWA）或無縫單一登入（SSO）管理驗證搭配密碼雜湊同步處理或傳遞驗證的同盟驗證，是在公司網路內具有內部部署網域控制站可供使用的最佳使用者體驗。 它可將認證提示疲勞降至最低，並降低使用者遭受受害者到網路釣魚攻擊的風險。 如果您已使用 PHS 或 PTA 雲端管理的驗證，但使用者在內部部署驗證時仍需要輸入密碼，則您應該立即[部署無縫 SSO](../hybrid/how-to-connect-sso.md)。 相反地，如果您目前與計畫同盟，最終會遷移至雲端管理的驗證，則您應該在遷移專案中執行無縫 SSO。

### <a name="device-trust-access-policies"></a>裝置信任存取原則

就像是貴組織中的使用者，裝置是您想要保護的核心身分識別。 您可以使用裝置的身分識別，隨時隨地保護您的資源。驗證裝置和其信任類型的帳戶處理，可透過下列方式來改善安全性狀態和可用性：

- 當裝置受信任時，使用 MFA 避免摩擦
- 封鎖不受信任裝置的存取
- 針對 Windows 10 裝置，請[以順暢的單一登入來提供內部部署資源](../devices/azuread-join-sso.md)。

您可以使用下列其中一種方法，在 Azure AD 中導入裝置身分識別並加以管理，藉此達成此目標：

- 組織可以使用[Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune)來管理裝置及強制執行合規性政策、證明裝置健康情況，並根據裝置是否符合規範設定條件式存取原則。 Microsoft Intune 可以管理 iOS 裝置、Mac 桌面（透過 JAMF 整合）、Windows 桌面（原生使用適用于 Windows 10 的行動裝置管理，以及與 Microsoft 端點 Configuration Manager 的共同管理）和 Android 行動裝置。
- [混合式 Azure AD join](../devices/hybrid-azuread-join-managed-domains.md)可讓您在具有 Active Directory 已加入網域的電腦裝置的環境中，使用群組原則或 Microsoft 端點 Configuration Manager 進行管理。 組織可以透過 PHS 或 PTA 使用無縫 SSO 部署受管理的環境。 讓您的裝置 Azure AD 透過 SSO 在雲端和內部部署資源中提高使用者生產力，同時使用條件式存取來保護對雲端和內部部署資源的存取 [Conditional Access](../conditional-access/overview.md)   。

如果您有未在雲端註冊的已加入網域 Windows 裝置，或是已在雲端中註冊但沒有條件式存取原則的已加入網域 windows 裝置，則您應該註冊未註冊的裝置，並在任何一種情況下，在條件式存取原則中[使用混合式 Azure AD join 做為控制項](../conditional-access/require-managed-devices.md)。

![在需要混合式裝置的條件式存取原則中授與的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img6.png)

如果您使用 MDM 或 Microsoft Intune 來管理裝置，但未在條件式存取原則中使用裝置控制項，則建議使用 [[要求裝置必須標記為符合規範](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant)] 這些原則中的控制項。

![條件式存取原則中的授與要求裝置合規性的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>建議閱讀的裝置信任存取原則

- [How To:規劃混合式 Azure Active Directory Join 實作](../devices/hybrid-azuread-join-plan.md)
- [身分識別與裝置存取設定](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello 企業版

在 Windows 10 中， [Windows Hello 企業版](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)會以電腦上的強式雙因素驗證取代密碼。 Windows Hello 企業版可為使用者提供更流暢的 MFA 體驗，並減少您對密碼的相依性。 如果您尚未開始推出 Windows 10 裝置，或只有部分部署，建議您升級至 Windows 10，並在所有裝置上[啟用 Windows Hello 企業版](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-manage-in-organization)。

如果您想要深入瞭解無密碼 authentication，請參閱[不含密碼的全球 Azure Active Directory](../authentication/concept-authentication-passwordless.md)。

## <a name="application-authentication-and-assignment"></a>應用程式驗證和指派

### <a name="single-sign-on-for-apps"></a>應用程式的單一登入

為整個企業提供標準化的單一登入機制，對於最佳使用者體驗、降低風險、回報和治理的能力非常重要。 如果您使用的應用程式支援具有 Azure AD 的 SSO，但目前設定為使用本機帳戶，您應該重新設定這些應用程式，以搭配 Azure AD 使用 SSO。 同樣地，如果您使用的任何應用程式支援具有 Azure AD 的 SSO，但使用其他身分識別提供者，您應該將這些應用程式重新設定為使用 SSO 搭配 Azure AD。 針對不支援同盟通訊協定但支援以表單為基礎之驗證的應用程式，建議您將應用程式設定為使用[密碼](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md)保存庫搭配 Azure AD 應用程式 Proxy。

![AppProxy 以密碼為基礎的登入](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 如果您沒有任何機制可探索組織中的非受控應用程式，我們建議使用雲端存取安全性代理人解決方案（CASB）（例如[Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)）來執行探索流程。

最後，如果您有 Azure AD 應用程式資源庫，並使用支援與 Azure AD SSO 的應用程式，我們建議您[在應用程式庫中列出應用程式](../azuread-dev/howto-app-gallery-listing.md)。

#### <a name="single-sign-on-recommended-reading"></a>建議使用單一登入閱讀

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>將 AD FS 應用程式遷移至 Azure AD

[將應用程式從 AD FS 遷移至 Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md)可在安全性、更一致的管理性和更好的共同作業體驗方面提供額外的功能。 如果您在 AD FS 中設定應用程式以支援 Azure AD 的 SSO，則您應該將這些應用程式重新設定為使用 SSO 搭配 Azure AD。 如果您在 AD FS 中設定應用程式，但 Azure AD 不支援罕見設定，您應洽詢應用程式擁有者，瞭解特殊設定是否為應用程式的絕對需求。 如果不是必要的，您應該將應用程式重新設定為使用 SSO 搭配 Azure AD。

![作為主要身分識別提供者 Azure AD](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [ADFS 的 Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)可用來收集每個可能遷移至 Azure AD 之應用程式的設定詳細資料。

### <a name="assign-users-to-applications"></a>將使用者指派給應用程式

將[使用者指派給應用程式](../manage-apps/assign-user-or-group-access-portal.md)最適合使用群組來對應，因為它們可提供更大的彈性和大規模管理能力。 使用群組的優點包括以[屬性為基礎的動態群組成員資格](../users-groups-roles/groups-dynamic-membership.md)，以及[對應用程式擁有](../fundamentals/active-directory-accessmanagement-managing-group-owners.md)者的委派。 因此，如果您已經在使用和管理群組，建議您採取下列動作來改善大規模管理：

- 委派群組管理和治理給應用程式擁有者。
- 允許對應用程式進行自助存取。
- 如果使用者屬性可以一致地判斷應用程式的存取權，請定義動態群組。
- 使用[Azure AD 的存取權審查](../governance/access-reviews-overview.md)，對用於應用程式存取權的群組執行證明。

另一方面，如果您發現有指派給個別使用者的應用程式，請務必針對這些應用程式執行[治理](https://docs.microsoft.com/azure/active-directory/governance/index)。

#### <a name="assign-users-to-applications-recommended-reading"></a>將使用者指派給應用程式建議的閱讀

- [將使用者和群組指派至 Azure Active Directory 中的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups)
- [在 Azure Active Directory 中委派應用程式註冊許可權](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-app-roles)
- [Azure Active Directory 中群組的動態成員資格規則](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

## <a name="access-policies"></a>存取原則

### <a name="named-locations"></a>具名位置

在 Azure AD 中使用[已命名的位置](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)時，您可以為組織中的受信任 IP 位址範圍加上標籤。 Azure AD 使用具名位置來：

- 避免風險事件中的誤報。 從信任的網路位置登入，會降低使用者的登入風險。
- 設定[位置型條件式存取](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)。

![具名位置](./media/active-directory-ops-guide/active-directory-ops-img10.png)

根據優先順序，使用下表來尋找最符合貴組織需求的建議解決方案：

| **優先順序** | **案例** | **建議** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | 如果您使用 PHS 或 PTA，而且尚未定義命名位置 | 定義已命名的位置以改善風險事件的偵測 |
| 2 | 如果您是同盟的，但未使用 "insideCorporateNetwork" 宣告和命名位置，則尚未定義 | 定義已命名的位置以改善風險事件的偵測 |
| 3 | 如果您未在條件式存取原則中使用已命名的位置，而且條件式存取原則中沒有任何風險或裝置控制項 | 設定條件式存取原則以包含命名位置 |
| 4 | 如果您是同盟的，請使用 "insideCorporateNetwork" 宣告和未定義的命名位置 | 定義已命名的位置以改善風險事件的偵測 |
| 5 | 如果您使用具有 MFA 的信任 IP 位址，而不是指定的位置，並將它們標示為受信任 | 定義已命名的位置，並將它們標示為受信任，以改善風險事件的偵測 |

### <a name="risk-based-access-policies"></a>以風險為基礎的存取原則

Azure AD 可以計算每個登入和每個使用者的風險。 在存取原則中使用風險作為準則，可以提供更好的使用者體驗，例如，較少的驗證提示，以及更佳的安全性，例如，只在需要時提示使用者，並將回應和修復自動化。

![登入風險原則](./media/active-directory-ops-guide/active-directory-ops-img11.png)

如果您已擁有支援在存取原則中使用風險的 Azure AD Premium P2 授權，但不會使用它們，我們強烈建議您將風險新增至您的安全性狀態。

#### <a name="risk-based-access-policies-recommended-reading"></a>以風險為基礎的存取原則建議閱讀

- [如何：設定登入風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [如何：設定使用者風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>用戶端應用程式存取原則

Microsoft Intune 應用程式管理（MAM）可讓您將資料保護控制項（例如儲存體加密、PIN、遠端存放裝置清除等）推送至相容的用戶端行動應用程式，例如 Outlook Mobile。 此外，您可以建立條件式存取原則，以限制來自已核准或相容應用程式的雲端服務（例如 Exchange Online）的[存取權](../conditional-access/app-based-conditional-access.md)。

如果您的員工安裝支援 MAM 的應用程式（例如 Office 行動應用程式）來存取公司資源（例如 Exchange Online 或 SharePoint Online），而且您也支援 BYOD （攜帶您自己的裝置），建議您部署應用程式 MAM 原則來管理個人擁有的裝置中的應用程式設定，而不需進行 MDM 註冊，然後更新您的條件式存取原則，只允許

![條件式存取授與控制](./media/active-directory-ops-guide/active-directory-ops-img12.png)

員工是否應針對公司資源安裝支援 MAM 的應用程式，並在受 Intune 管理的裝置上受到限制的存取，然後您應該考慮部署應用程式 MAM 原則來管理個人裝置的應用程式設定，並更新條件式存取原則，只允許來自支援 MAM 的用戶端存取。

### <a name="conditional-access-implementation"></a>條件式存取執行

條件式存取是改善組織安全性狀態的基本工具。 因此，請務必遵循下列最佳作法：

- 確定所有 SaaS 應用程式都已套用至少一個原則
- 避免將 [**所有應用程式**] 篩選器與**封鎖**控制項結合，以避免鎖定的風險
- 避免使用 [**所有使用者**] 做為篩選準則，並不小心新增**來賓**
- **將所有「舊版」原則遷移至 Azure 入口網站**
- 攔截使用者、裝置和應用程式的所有準則
- 使用條件式存取原則來[執行 mfa](../conditional-access/plan-conditional-access.md)，而不是使用**每個使用者的 mfa**
- 具有一組可套用至多個應用程式的小型核心原則
- 定義空的例外狀況群組，並將其新增至原則以具有例外狀況策略
- 規劃無 MFA 控制項的[中斷玻璃](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency)帳戶
- 確保跨 Office 365 用戶端應用程式的一致體驗，例如小組、商務用 OneDrive、Outlook 等。）藉由為 Exchange Online 和 Sharepoint Online 等服務執行一組相同的控制項
- 應透過群組（而非個人）來執行原則指派
- 定期審查原則中所使用的例外狀況群組，以限制使用者超出安全性狀態的時間。 如果您擁有 Azure AD P2，則可以使用存取權審查來自動化程式

#### <a name="conditional-access-recommended-reading"></a>條件式存取建議閱讀

- [Azure Active Directory 中的條件式存取最佳做法](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)
- [身分識別與裝置存取設定](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory 條件式存取設定參考](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference)
- [一般條件式存取原則](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)

## <a name="access-surface-area"></a>存取介面區

### <a name="legacy-authentication"></a>舊版驗證

強式認證（例如 MFA）無法使用舊版驗證通訊協定來保護應用程式，這使得它成為惡意執行者慣用的攻擊媒介。 鎖定舊版驗證對於改善存取安全性狀態非常重要。

舊版驗證是指應用程式所使用的驗證通訊協定，如下所示：

- 不使用新式驗證的舊版 Office 用戶端（例如，Office 2010 用戶端）
- 使用郵件通訊協定的用戶端，例如 IMAP/SMTP/POP

攻擊者強烈偏好這些通訊協定，事實上，幾乎[100% 的密碼噴灑攻擊](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)會使用舊版驗證通訊協定！ 駭客會使用舊版驗證通訊協定，因為它們不支援互動式登入，這是額外的安全性挑戰所需的，例如多重要素驗證和裝置驗證。

如果您的環境中廣泛使用舊版驗證，您應該計畫儘快將舊版用戶端遷移至支援[新式驗證](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016)的用戶端。 在相同的權杖中，如果您有一些使用者已經使用新式驗證，但其他人仍使用舊版驗證，則您應該採取下列步驟來鎖定舊版驗證用戶端：

1. 使用登[入活動報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)來識別仍在使用舊版驗證和計畫補救的使用者：

   a. 將可支援新式驗證的用戶端升級至受影響的使用者。
   
   b. 規劃轉換的時間範圍，以根據下列步驟進行鎖定。
   
   c. 識別哪些繼承應用程式對舊版驗證有困難的相依性。 請參閱下面的步驟3。

2. 針對未使用舊版驗證的使用者，停用來源的舊版通訊協定（例如 Exchange 信箱），以避免產生更多風險。
3. 對於其餘帳戶（最好是非人類身分識別，例如服務帳戶），請使用[條件式存取來限制舊版通訊協定](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417)的驗證後。

#### <a name="legacy-authentication-recommended-reading"></a>建議閱讀的舊版驗證

- [啟用或停用 Exchange Server 中信箱的 POP3 或 IMAP4 存取](https://docs.microsoft.com/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>同意授權

在違法的同意授與攻擊中，攻擊者會建立一個 Azure AD 註冊的應用程式，以要求存取連絡人資訊、電子郵件或檔等資料。 當登陸惡意網站時，使用者可能會透過網路釣魚攻擊，將同意授與惡意應用程式。

以下是您可能想要對 Microsoft 雲端服務進行的許可權的應用程式清單：

- 具有應用程式或委派的應用程式 \* 。ReadWrite 許可權
- 具有委派許可權的應用程式可以代表使用者讀取、傳送或管理電子郵件
- 使用下列許可權授與的應用程式：

| 資源 | 權限 |
| :- | :- |
| Office 365 Exchange Online | 符合.AccessAsUser。 |
| | EWS.AccessAsUser。 |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | 郵件. 讀取。共用 |
| | Mail. ReadWrite |

- 已授與完整使用者模擬登入使用者的應用程式。 例如：

|資源 | 權限 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

若要避免這種情況，您應該參閱[在 Office 365 中偵測並補救違法的同意](https://docs.microsoft.com/office365/securitycompliance/detect-and-remediate-illicit-consent-grants)授與，以找出並修正具有非法授與的應用程式，或具有超出所需授權的應用程式。 接下來，[完全移除自助服務](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-user-consent)，並[建立治理程式](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)。 最後，排程應用程式許可權的定期檢查，並在不需要時予以移除。

#### <a name="consent-grants-recommended-reading"></a>同意授與建議的閱讀

- [Microsoft Graph API 許可權](https://docs.microsoft.com/graph/permissions-reference)

### <a name="user-and-group-settings"></a>使用者和群組設定

以下是在沒有明確的商務需求時，可以鎖定的使用者和群組設定：

#### <a name="user-settings"></a>使用者設定

- **外部使用者**-在企業中使用小組、Power BI、Sharepoint Online 和 Azure 資訊保護等服務，可能會發生外部共同作業。 如果您有明確的限制，可控制使用者起始的外部共同作業，建議您使用[Azure AD 權利管理](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)或受控制的作業（例如透過技術支援中心）來啟用外部使用者。 如果您不想要允許服務的有機外部共同作業，您可以[封鎖成員完全邀請外部使用者](https://docs.microsoft.com/azure/active-directory/b2b/delegate-invitations)。 或者，您也可以[允許或封鎖](https://docs.microsoft.com/azure/active-directory/b2b/allow-deny-list)外部使用者邀請中的特定網域。
- **應用程式註冊**-啟用應用程式註冊時，終端使用者可以將應用程式上架，並授與其資料的存取權。 應用程式註冊的典型範例是讓 Outlook 外掛程式或語音助理（例如 Alexa 和 Siri）讀取其電子郵件和行事曆，或代表他們傳送電子郵件的使用者。 如果客戶決定關閉應用程式註冊，則 InfoSec 和 IAM 小組必須納入例外狀況的管理（根據商務需求所需的應用程式註冊），因為他們需要使用系統管理員帳戶來註冊應用程式，而且很可能需要設計進程來讓進程。
- 系統**管理入口網站**-組織可以鎖定 Azure 入口網站的 [Azure AD] 分頁，讓非系統管理員無法存取 Azure 入口網站中的 Azure AD 管理，並使其感到困惑。 移至 Azure AD 管理入口網站中的使用者設定，以限制存取：

![系統管理入口網站限制存取](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 非管理員仍然可以透過命令列和其他程式設計介面來存取 Azure AD 管理介面。

#### <a name="group-settings"></a>群組設定

**自助群組管理/使用者可以建立安全性群組/O365 群組。** 如果雲端中沒有任何目前的自助服務方案，客戶可能會決定將其關閉，直到他們準備好使用這項功能為止。

#### <a name="groups-recommended-reading"></a>建議閱讀的群組

- [什麼是 Azure Active Directory B2B 共同作業？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
- [整合應用程式與 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- [Azure Active Directory 中的應用程式、許可權和同意。](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)
- [在 Azure Active Directory 中使用群組來管理資源的存取權](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
- [在 Azure Active Directory 中設定自助式應用程式存取管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)

### <a name="traffic-from-unexpected-locations"></a>來自非預期位置的流量

攻擊者來自世界各地的各個部分。 使用條件式存取原則搭配 location 作為條件來管理此風險。 條件式存取原則的[位置條件](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)可讓您封鎖從無法進行登入的商業理由存取位置。

![建立新的命名位置](./media/active-directory-ops-guide/active-directory-ops-img14.png)

如果有的話，請使用安全性資訊和事件管理（SIEM）解決方案來分析和尋找跨區域的存取模式。 如果您不使用 SIEM 產品，或它不會從 Azure AD 內嵌驗證資訊，建議您使用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/overview)來識別跨區域的存取模式。

## <a name="access-usage"></a>存取使用方式

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD 記錄封存並與事件回應計畫整合

有權存取登入活動、Azure AD 的審核和風險事件，對於疑難排解、流量分析和辯論調查都很重要。 Azure AD 透過具有有限保留期限的 REST Api，提供這些來源的存取權。 安全性資訊和事件管理（SIEM）系統或同等的封存技術，是長期儲存的審核和支援能力的關鍵。 若要啟用 Azure AD 記錄的長期儲存，您必須將它們新增至現有的 SIEM 解決方案，或使用[Azure 監視器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor)。 封存記錄檔，可用於事件回應計畫和調查的一部分。

#### <a name="logs-recommended-reading"></a>記錄建議的讀取

- [Azure Active Directory 稽核 API 參考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference)
- [Azure Active Directory 登入活動報告 API 參考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-sign-in-activity-reference)
- [使用 Azure AD 報告 API 搭配憑證來取得資料](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-with-certificates)
- [Azure Active Directory Identity Protection 的 Microsoft Graph](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)
- [Office 365 管理活動 API 參考](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference)
- [如何使用 Azure Active Directory Power BI 內容套件](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>摘要

安全身分識別基礎結構有12個層面。 這份清單可協助您進一步保護和管理認證、定義驗證體驗、委派指派、測量使用方式，以及根據企業安全性狀態定義存取原則。

- 將擁有者指派給主要工作。
- 執行解決方案來偵測弱式或洩露的密碼、改善密碼管理和保護，以及進一步保護使用者對資源的存取。
- 管理裝置的身分識別，以在任何時間和任何位置保護您的資源。
- 執行無密碼 authentication。
- 在整個組織中提供標準化的單一登入機制。
- 將應用程式從 AD FS 遷移至 Azure AD，以提供更佳的安全性和更一致的管理性。
- 使用群組將使用者指派給應用程式，以提供更大的彈性和大規模管理能力。
- 設定以風險為基礎的存取原則。
- 鎖定舊版驗證通訊協定。
- 偵測並補救違法的同意授權。
- 鎖定使用者和群組設定。
- 啟用 Azure AD 記錄的長期儲存，以進行疑難排解、使用方式分析和辯論調查。

## <a name="next-steps"></a>後續步驟

開始進行身分[識別管理操作檢查和動作](active-directory-ops-guide-govern.md)。
