---
title: Azure Active Directory Authentication 管理作業參考指南
description: 此操作參考指南說明保護驗證管理所應採取的檢查和動作
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
ms.openlocfilehash: 178c54b9726f21775603d67cb0911237aa4caf01
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90601359"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory Authentication 管理作業參考指南

[Azure AD 作業參考指南](active-directory-ops-guide-intro.md)的這一節說明您應採取的檢查和動作，以保護和管理認證、定義驗證經驗、委派指派、測量使用量，以及根據企業安全性狀態定義存取原則。

> [!NOTE]
> 這些建議是從發行日期開始的最新狀態，但可能會隨著時間而改變。 組織應持續評估其身分識別實務，因為 Microsoft 產品和服務會隨著時間而演進。

## <a name="key-operational-processes"></a>重要的操作流程

### <a name="assign-owners-to-key-tasks"></a>將擁有者指派給重要工作

管理 Azure Active Directory 需要持續執行重要的作業工作和進程，這可能不是首度發行專案的一部分。 您也必須設定這些工作來優化您的環境。 主要工作和其建議的擁有者包括：

| Task | 擁有者 |
| :- | :- |
| 在 Azure AD 中管理單一登入 (SSO) 設定的生命週期 | IAM 作業小組 |
| 設計 Azure AD 應用程式的條件式存取原則 | InfoSec 架構小組 |
| SIEM 系統中的封存登入活動 | InfoSec 作業小組 |
| 封存 SIEM 系統中的風險事件 | InfoSec 作業小組 |
| 分級和調查安全性報告 | InfoSec 作業小組 |
| 分級和調查風險事件 | InfoSec 作業小組 |
| 分級並調查 Azure AD Identity Protection 中標示為有風險的使用者和弱點報告 | InfoSec 作業小組 |

> [!NOTE]
> Azure AD Identity Protection 需要 Azure AD Premium P2 授權。 若要尋找您需求的正確授權，請參閱 [比較 Azure AD Free 的正式運作功能和 Azure AD Premium 的版本](https://azure.microsoft.com/pricing/details/active-directory/)。

當您複習清單時，可能會發現您需要為缺少擁有者的工作指派擁有者，或針對擁有者未與上述建議一致的工作，調整其擁有權。

#### <a name="owner-recommended-reading"></a>擁有者建議閱讀

- [在 Azure Active Directory 中指派系統管理員角色](../users-groups-roles/directory-assign-admin-roles.md)
- [Azure 中的治理](../../governance/index.yml)

## <a name="credentials-management"></a>認證管理

### <a name="password-policies"></a>密碼原則

安全地管理密碼是身分識別和存取管理的最重要部分，通常是最大的攻擊目標。 Azure AD 支援數個可協助防止攻擊成功的功能。

使用下表來尋找解決需要解決之問題的建議解決方案：

| 問題 | 建議 |
| :- | :- |
| 沒有任何機制可防止弱式密碼 | 啟用 Azure AD [自助式密碼重設 (SSPR) ](../authentication/concept-sspr-howitworks.md) 和 [密碼保護](../authentication/concept-password-ban-bad-on-premises.md) |
| 沒有偵測出密碼洩漏的機制 | 啟用 [密碼雜湊同步](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) 以深入瞭解 |
| 使用 AD FS 且無法移至受控驗證 | 啟用 [AD FS 外部網路智慧鎖定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) 及/或 [Azure AD 智慧鎖定](../authentication/howto-password-smart-lockout.md) |
| 密碼原則會使用以複雜度為基礎的規則，例如長度、多個字元集或到期日 | 重新考慮 [使用 Microsoft 建議的作法](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) ，並將您的方法切換為密碼管理，並部署 [Azure AD 密碼保護](../authentication/concept-password-ban-bad.md)。 |
| 使用者未註冊為使用多重要素驗證 (MFA)  | [註冊所有使用者的安全性資訊](../identity-protection/howto-identity-protection-configure-mfa-policy.md) ，使其可作為驗證使用者身分識別及其密碼的機制 |
| 不會根據使用者風險撤銷密碼 | 部署 Azure AD [Identity Protection 使用者風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md) ，以使用 SSPR 強制對洩漏的認證進行密碼變更 |
| 沒有智慧型鎖定機制可防止來自所識別 IP 位址的不良執行者進行惡意驗證 | 使用密碼雜湊同步處理或 [傳遞驗證](../hybrid/how-to-connect-pta-quick-start.md) (PTA 來部署雲端管理的驗證)  |

#### <a name="password-policies-recommended-reading"></a>建議閱讀的密碼原則

- [Azure AD 和 AD FS 的最佳作法：防禦密碼噴灑攻擊-Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>啟用自助式密碼重設和密碼保護

需要變更或重設其密碼的使用者，是大量和技術支援中心通話的最大來源之一。 除了成本之外，將密碼變更為降低使用者風險的工具，是改善組織安全性狀態的基本步驟。

建議您至少部署 Azure AD [自助式密碼重設](../authentication/concept-sspr-howitworks.md) (SSPR) 和內部部署 [密碼保護](../authentication/howto-password-ban-bad-on-premises-deploy.md) 來完成：

- 轉移服務台電話。
- 取代暫時密碼的使用。
- 取代依賴內部部署解決方案的任何現有自助密碼管理解決方案。
- [消除](../authentication/concept-password-ban-bad.md) 組織中的弱式密碼。

> [!NOTE]
> 針對具有 Azure AD Premium P2 訂用帳戶的組織，建議您部署 SSPR，並將其作為 [Identity Protection 使用者風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md)的一部分。

### <a name="strong-credential-management"></a>強式認證管理

密碼本身不夠安全，無法防止不良的動作專案存取您的環境。 具有特殊許可權帳戶的任何使用者至少都必須啟用 (MFA) 的多重要素驗證。 在理想情況下，您應該啟用 [合併的註冊](../authentication/concept-registration-mfa-sspr-combined.md) ，並要求所有使用者使用結合的 [註冊體驗](../user-help/security-info-setup-signin.md)來註冊 MFA 和 SSPR。 最後，我們建議您採用策略來 [提供復原能力](../authentication/concept-resilient-controls.md) ，以降低由於未預期的情況而鎖定的風險。

![合併的使用者體驗流程](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>內部部署中斷驗證恢復功能

除了簡單的優點，以及啟用洩漏的認證偵測之外，Azure AD 的密碼雜湊同步處理 (PHS) 和 Azure MFA 允許使用者存取 SaaS 應用程式和 Microsoft 365，但因為網路攻擊例如 [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)所造成的內部部署中斷。 您也可以同時啟用 PHS，同時搭配同盟。 啟用 PHS 可在同盟服務無法使用時，允許進行驗證。

如果您的內部部署組織缺少中斷復原策略，或有一個未與 Azure AD 整合，您應該部署 Azure AD PHS，並定義包含 PHS 的嚴重損壞修復計畫。 啟用 Azure AD PHS 可讓使用者在您的內部部署 Active Directory 無法使用時，對 Azure AD 進行驗證。

![密碼雜湊同步處理流程](./media/active-directory-ops-guide/active-directory-ops-img5.png)

若要進一步瞭解您的驗證選項，請參閱 [為您的 Azure Active Directory 混合式身分識別解決方案選擇正確的驗證方法](../hybrid/choose-ad-authn.md)。

### <a name="programmatic-usage-of-credentials"></a>以程式設計方式使用認證

使用 PowerShell 或使用 Microsoft Graph API 的應用程式 Azure AD 腳本需要安全驗證。 執行這些腳本和工具的認證管理不良，會提高認證遭竊的風險。 如果您使用的腳本或應用程式依賴硬式編碼的密碼或密碼提示，您應該先查看設定檔或原始程式碼中的密碼，然後取代這些相依性，並盡可能使用 Azure 受控識別、整合式 Windows 驗證或 [憑證](../reports-monitoring/tutorial-access-api-with-certificates.md) 。 針對無法使用先前解決方案的應用程式，請考慮使用 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

如果您判斷有包含密碼認證的服務主體，而且不確定腳本或應用程式如何保護這些密碼認證，請洽詢應用程式的擁有者，以進一步瞭解使用模式。

如果有具有密碼認證的服務主體，Microsoft 也會建議您聯繫應用程式擁有者，以瞭解使用模式。

## <a name="authentication-experience"></a>驗證體驗

### <a name="on-premises-authentication"></a>內部部署驗證

使用整合式 Windows 驗證 (IWA) 或無縫單一登入的同盟驗證 (SSO) 使用密碼雜湊同步處理的受控驗證或傳遞驗證，是在內部部署網域控制站可看見的公司網路內的最佳使用者體驗。 它會將認證提示疲勞降至最低，並降低使用者受害者網路釣魚攻擊的風險。 如果您已經使用雲端管理的驗證搭配 PHS 或 PTA，但使用者在內部部署驗證時仍需要輸入密碼，則您應該立即 [部署無縫 SSO](../hybrid/how-to-connect-sso.md)。 另一方面，如果您目前已與計畫進行同盟，最後遷移至雲端管理的驗證，則您應該在遷移專案中執行無縫 SSO。

### <a name="device-trust-access-policies"></a>裝置信任存取原則

就像是貴組織中的使用者，裝置是您想要保護的核心身分識別。 您可以使用裝置的身分識別，隨時隨地保護您的資源。驗證裝置，並針對其信任類型進行帳戶處理，可透過下列方式改善您的安全性狀態和可用性：

- 在裝置受信任時避免發生衝突，例如透過 MFA
- 封鎖不受信任裝置的存取
- 針對 Windows 10 裝置，請 [將單一登入順暢地提供給內部部署資源](../devices/azuread-join-sso.md)。

您可以使用下列其中一種方法，藉由在 Azure AD 中帶入裝置身分識別並管理它們，以達成此目標：

- 組織可以使用 [Microsoft Intune](/intune/what-is-intune) 來管理裝置並強制執行合規性原則、證明裝置健康情況，以及根據裝置是否符合規範來設定條件式存取原則。 Microsoft Intune 可以管理 iOS 裝置、Mac 桌面 (透過 JAMF 整合) 、Windows 桌面 (以原生方式使用行動裝置管理進行 Windows 10，以及使用 Microsoft Endpoint Configuration Manager) 和 Android 行動裝置進行共同管理。
- [混合式 Azure AD join](../devices/hybrid-azuread-join-managed-domains.md) 可在 Active Directory 加入網域的電腦裝置的環境中，使用群組原則或 Microsoft Endpoint Configuration Manager 來管理。 組織可以透過無縫 SSO 的 PHS 或 PTA 部署受控環境。 將您的裝置帶入 Azure AD 可透過跨雲端和內部部署資源的 SSO 來將使用者生產力最大化，同時讓您同時使用 [條件式存取](../conditional-access/overview.md)來保護雲端和內部部署資源的存取   。

如果您有已加入網域的 Windows 裝置未在雲端中註冊，或已加入網域的 Windows 裝置已在雲端中註冊，但沒有條件式存取原則，則您應該註冊未註冊的裝置，並在任何一種情況下，在條件式存取原則中 [使用混合式 Azure AD 聯結作為控制項](../conditional-access/require-managed-devices.md) 。

![在條件式存取原則中授與需要混合式裝置的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img6.png)

如果您使用 MDM 或 Microsoft Intune 來管理裝置，但未在條件式存取原則中使用裝置控制項，則建議使用 [ [要求裝置] 標示為符合](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) 這些原則中的控制措施。

![在條件式存取原則中授與需要裝置合規性的螢幕擷取畫面](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>建議閱讀的裝置信任存取原則

- [How To:規劃混合式 Azure Active Directory Join 實作](../devices/hybrid-azuread-join-plan.md)
- [身分識別與裝置存取設定](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Windows Hello 企業版

在 Windows 10 中， [Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-identity-verification) 會以強式雙因素驗證取代電腦上的密碼。 Windows Hello 企業版為使用者啟用更簡化的 MFA 體驗，並減少您對密碼的相依性。 如果您尚未開始推出 Windows 10 的裝置，或僅部分部署它們，建議您升級至 Windows 10，並在所有裝置上 [啟用 Windows Hello 企業版](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) 。

如果您想要深入瞭解無密碼 authentication，請參閱 [沒有密碼 Azure Active Directory 的世界](../authentication/concept-authentication-passwordless.md)。

## <a name="application-authentication-and-assignment"></a>應用程式驗證和指派

### <a name="single-sign-on-for-apps"></a>應用程式的單一登入

為整個企業提供標準化的單一登入機制，對最佳使用者體驗、降低風險、報告和治理的能力非常重要。 如果您使用的應用程式支援搭配 Azure AD 的 SSO，但目前設定為使用本機帳戶，您應該重新設定這些應用程式，以搭配 Azure AD 使用 SSO。 同樣地，如果您使用的任何應用程式都支援 SSO 搭配 Azure AD 但使用的是其他身分識別提供者，您也應該重新設定這些應用程式，以搭配 Azure AD 使用 SSO。 對於不支援同盟通訊協定但支援表單架構驗證的應用程式，我們建議您將應用程式設定為使用 [密碼](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) 保存庫搭配 Azure AD 應用程式 Proxy。

![以密碼為基礎的登入 AppProxy](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> 如果您沒有探索組織中非受控應用程式的機制，我們建議使用雲端存取安全性代理程式解決方案來執行探索程式， (CASB) 例如 [Microsoft Cloud App Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)。

最後，如果您有 Azure AD 的應用程式庫，並使用支援 SSO 與 Azure AD 的應用程式，我們建議您 [在應用程式庫中列出應用程式](../azuread-dev/howto-app-gallery-listing.md)。

#### <a name="single-sign-on-recommended-reading"></a>建議閱讀單一登入

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>將 AD FS 應用程式遷移至 Azure AD

[將應用程式從 AD FS 遷移至 Azure AD](../manage-apps/migrate-adfs-apps-to-azure.md) 可提供安全性的額外功能、更一致的管理能力，以及更好的共同作業體驗。 如果您在 AD FS 中設定的應用程式支援 Azure AD 的 SSO，則應該重新設定這些應用程式，以搭配使用 SSO 與 Azure AD。 如果您在 AD FS 中設定的應用程式具有 Azure AD 不支援的罕見設定，則應聯繫應用程式擁有者，以瞭解特殊設定是否為應用程式的絕對需求。 如果不需要，您應該重新設定應用程式，以搭配 Azure AD 使用 SSO。

![作為主要身分識別提供者 Azure AD](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> 您可以使用[ADFS 的 Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)來收集每個應用程式的設定詳細資料，其中可能會遷移到 Azure AD。

### <a name="assign-users-to-applications"></a>將使用者指派給應用程式

將[使用者指派給應用程式](../manage-apps/assign-user-or-group-access-portal.md)最好是使用群組進行對應，因為它們可提供更大的彈性和大規模管理的能力。 使用群組的優點包括以 [屬性為基礎的動態群組成員資格](../users-groups-roles/groups-dynamic-membership.md) ，以及 [對應用程式擁有](../fundamentals/active-directory-accessmanagement-managing-group-owners.md)者的委派。 因此，如果您已經在使用和管理群組，建議您採取下列動作來大規模改進管理：

- 將群組管理和治理委派給應用程式擁有者。
- 允許自助服務存取應用程式。
- 如果使用者屬性可以一致地判斷應用程式的存取權，請定義動態群組。
- 使用 [Azure AD 存取權評論](../governance/access-reviews-overview.md)，將證明套用至用來存取應用程式的群組。

另一方面，如果您發現將指派給個別使用者的應用程式，請務必在這些應用程式上執行 [治理](../governance/index.yml) 。

#### <a name="assign-users-to-applications-recommended-reading"></a>將使用者指派給建議的應用程式閱讀

- [將使用者和群組指派至 Azure Active Directory 中的應用程式](../manage-apps/assign-user-or-group-access-portal.md)
- [在 Azure Active Directory 中委派應用程式註冊許可權](../users-groups-roles/roles-delegate-app-roles.md)
- [Azure Active Directory 中群組的動態成員資格規則](../users-groups-roles/groups-dynamic-membership.md)

## <a name="access-policies"></a>存取原則

### <a name="named-locations"></a>具名位置

使用 Azure AD 中的 [命名位置](../reports-monitoring/quickstart-configure-named-locations.md) 時，您可以為組織中的受信任 IP 位址範圍加上標籤。 Azure AD 使用具名位置來：

- 避免風險事件中的誤報。 從受信任的網路位置登入，會降低使用者的登入風險。
- 設定[位置型條件式存取](../reports-monitoring/quickstart-configure-named-locations.md)。

![具名位置](./media/active-directory-ops-guide/active-directory-ops-img10.png)

根據優先順序，使用下表來尋找最符合貴組織需求的建議解決方案：

| **優先順序** | **案例** | **建議** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | 如果您使用 PHS 或 PTA，但未定義命名位置 | 定義已命名的位置以改善風險事件的偵測 |
| 2 | 如果您是同盟的，且未使用 "insideCorporateNetwork" 宣告和命名位置，則不會定義 | 定義已命名的位置以改善風險事件的偵測 |
| 3 | 如果您未在條件式存取原則中使用指定的位置，且條件式存取原則中沒有任何風險或裝置控制 | 設定條件式存取原則以包含命名位置 |
| 4 | 如果您是同盟的，請使用 "insideCorporateNetwork" 宣告，且未定義命名位置 | 定義已命名的位置以改善風險事件的偵測 |
| 5 | 如果您搭配 MFA 使用信任的 IP 位址，而不是指定的位置，並將其標示為受信任 | 定義指定的位置，並將其標示為受信任，以改善風險事件的偵測 |

### <a name="risk-based-access-policies"></a>以風險為基礎的存取原則

Azure AD 可以計算每次登入和每位使用者的風險。 使用風險作為存取原則的準則，可提供更好的使用者體驗，例如，驗證提示較少，以及更好的安全性，例如，只在需要使用者時提示使用者，以及將回應和補救自動化。

![登入風險原則](./media/active-directory-ops-guide/active-directory-ops-img11.png)

如果您已經擁有支援在存取原則中使用風險的 Azure AD Premium P2 授權，但我們強烈建議您增加安全性狀態的風險。

#### <a name="risk-based-access-policies-recommended-reading"></a>建議閱讀的風險型存取原則

- [如何：設定登入風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [如何：設定使用者風險原則](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>用戶端應用程式存取原則

Microsoft Intune 應用程式管理 (MAM) 提供將資料保護控制（例如儲存體加密、PIN、遠端存放裝置清除等）推送至相容用戶端行動應用程式（例如 Outlook Mobile）的功能。 此外，也可以建立條件式存取原則，以限制從核准或相容的應用程式 [存取](../conditional-access/app-based-conditional-access.md) Exchange Online 等雲端服務。

如果您的員工安裝支援 MAM 的應用程式（例如 Office 行動應用程式）來存取公司資源（例如 Exchange Online 或 SharePoint Online），而且您也支援 BYOD (自備裝置) ，我們建議您部署應用程式 MAM 原則來管理個人擁有裝置中不含 MDM 註冊的應用程式設定，然後更新您的條件式存取原則，以僅允許支援 MAM 的用戶端

![條件式存取授與控制](./media/active-directory-ops-guide/active-directory-ops-img12.png)

如果員工在受 Intune 管理的裝置上安裝支援 MAM 的應用程式，則您應該考慮部署應用程式 MAM 原則來管理個人裝置的應用程式設定，並將條件式存取原則更新為只允許從支援 MAM 的用戶端存取。

### <a name="conditional-access-implementation"></a>條件式存取執行

條件式存取是改善組織安全性狀態的基本工具。 因此，請務必遵循下列最佳作法：

- 確定所有 SaaS 應用程式都已套用至少一個原則
- 避免將 **所有應用程式** 濾波器與 **block** 控制項合併，以避免鎖定風險
- 避免使用 [ **所有使用者** ] 做為篩選準則，並且不小心新增 **來賓**
- **將所有 "legacy" 原則遷移至 Azure 入口網站**
- 攔截使用者、裝置和應用程式的所有準則
- 使用條件式存取原則來 [實行 mfa](../conditional-access/plan-conditional-access.md)，而不是使用 **每個使用者的 mfa**
- 具有可套用至多個應用程式的一組小型核心原則
- 定義空的例外狀況群組，並將其新增至原則以具有例外狀況策略
- 規劃 [中斷玻璃](../users-groups-roles/directory-admin-roles-secure.md#break-glass-what-to-do-in-an-emergency) 帳戶而不搭配 MFA 控制項
- 針對 Exchange Online 和 Sharepoint Online 等服務執行相同的一組控制項，確保跨 Microsoft 365 用戶端應用程式（例如，小組、OneDrive、Outlook 等） ) 一致的體驗。
- 原則的指派應透過群組（而非個人）來執行
- 定期審核原則中所使用的例外狀況群組，以限制使用者超出安全性狀態的時間。 如果您擁有 Azure AD P2，則可以使用存取權審核來自動化流程

#### <a name="conditional-access-recommended-reading"></a>建議閱讀條件式存取

- [Azure Active Directory 中條件式存取的最佳作法](../conditional-access/best-practices.md)
- [身分識別與裝置存取設定](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory 條件式存取設定參考](../conditional-access/concept-conditional-access-conditions.md)
- [一般條件式存取原則](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>存取介面區

### <a name="legacy-authentication"></a>舊版驗證

MFA 等強式認證無法使用舊版驗證通訊協定來保護應用程式，使其成為惡意執行者慣用的攻擊媒介。 鎖定舊版驗證對於改善存取安全性狀態非常重要。

舊版驗證是指應用程式所使用的驗證通訊協定，如下所示：

- 未使用新式驗證的舊版 Office 用戶端 (例如，Office 2010 用戶端) 
- 使用郵件通訊協定（例如 IMAP/SMTP/POP）的用戶端

攻擊者強烈偏好這些通訊協定，事實上，幾乎 [100% 的密碼噴灑攻擊](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) 會使用舊版驗證通訊協定！ 駭客會使用舊版驗證通訊協定，因為它們不支援互動式登入，而這對於額外的安全性挑戰（例如多重要素驗證和裝置驗證）是必要的。

如果您的環境廣泛使用舊版驗證，您應該計畫將舊版用戶端遷移至支援 [新式驗證](/office365/enterprise/modern-auth-for-office-2013-and-2016) 的用戶端。 在相同的權杖中，如果您有某些使用者已經使用新式驗證，但其他使用者仍使用舊版驗證，則您應該採取下列步驟來鎖定舊版驗證用戶端：

1. 使用登 [入活動報告](../reports-monitoring/concept-sign-ins.md) 來識別仍在使用舊版驗證和計畫補救的使用者：

   a. 將支援新式驗證的用戶端升級至受影響的使用者。
   
   b. 規劃轉換時間範圍以鎖定下列各步驟。
   
   c. 識別哪些繼承應用程式在舊版驗證上有困難的相依性。 請參閱下面的步驟3。

2. 在來源 (停用舊版通訊協定，例如 Exchange 信箱) 適用于未使用舊版驗證的使用者，以避免更多暴露。
3. 針對剩餘的帳戶 (的非人為身分識別，例如服務帳戶) ，請使用 [條件式存取來限制](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) 在驗證後的舊版通訊協定。

#### <a name="legacy-authentication-recommended-reading"></a>建議閱讀舊版驗證

- [啟用或停用 Exchange Server 中信箱的 POP3 或 IMAP4 存取](/exchange/clients/pop3-and-imap4/configure-mailbox-access?view=exchserver-2019)

### <a name="consent-grants"></a>同意授權

在非法同意授與攻擊中，攻擊者會建立 Azure AD 註冊的應用程式，要求存取連絡人資訊、電子郵件或檔等資料。 使用者可能會在登陸惡意網站時，透過網路釣魚攻擊將同意授與惡意應用程式。

以下是具有您可能想要針對 Microsoft 雲端服務進行查看之許可權的應用程式清單：

- 具有應用程式或委派的應用程式 \* 。ReadWrite 許可權
- 具有委派許可權的應用程式可以代表使用者讀取、傳送或管理電子郵件
- 使用下列許可權授與的應用程式：

| 資源 | 權限 |
| :- | :- |
| Exchange Online | 大通。AccessAsUser |
| | EWS.AccessAsUser |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | 郵件。共用 |
| | Mail ReadWrite |

- 應用程式授與已登入使用者的完整使用者模擬。 例如：

|資源 | 權限 |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

若要避免這種情況，您應該參閱 [Office 365 中的偵測和補救非法同意](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) 授與，以找出並修正具有非法授權的應用程式，或具有比所需更多授權的應用程式。 接下來， [完全移除自助服務](../manage-apps/configure-user-consent.md) 並 [建立治理程式](../manage-apps/configure-admin-consent-workflow.md)。 最後，排程應用程式許可權的定期審核，並在不需要時將其移除。

#### <a name="consent-grants-recommended-reading"></a>同意授與建議的讀取

- [Microsoft Graph API 許可權](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>使用者和群組設定

以下是如果沒有明確的商務需求，可以鎖定的使用者和群組設定：

#### <a name="user-settings"></a>使用者設定

- **外部使用者** -外部共同作業可能會在企業中使用小組、Power BI、Sharepoint Online 和 Azure 資訊保護等服務進行茁壯。 如果您有明確的限制，可控制使用者起始的外部共同作業，建議您使用 [Azure AD 權利管理](../governance/entitlement-management-overview.md) 或受控制的作業（例如透過服務台）來啟用外部使用者。 如果您不想要允許服務的有機外部共同作業，可以 [封鎖成員完全邀請外部使用者](../external-identities/delegate-invitations.md)。 或者，您也可以 [允許或封鎖](../external-identities/allow-deny-list.md) 外部使用者邀請中的特定網域。
- **應用程式註冊** -啟用應用程式註冊時，終端使用者可以將應用程式本身上線，並授與其資料的存取權。 應用程式註冊的一般範例是啟用 Outlook 外掛程式的使用者，或 Alexa 和 Siri 等語音助理來代表他們讀取電子郵件和行事曆，或傳送電子郵件。 如果客戶決定關閉應用程式註冊，InfoSec 和 IAM 小組必須在 (應用程式註冊時，根據商務需求) 管理例外狀況，因為他們需要向系統管理員帳戶註冊應用程式，而且很可能需要設計程式來讓程式。
- 系統**管理入口網站**-組織可以鎖定 Azure 入口網站中的 Azure AD 的分頁，讓非系統管理員無法存取 Azure 入口網站中的 Azure AD 管理，並讓人感到困惑。 移至 Azure AD management 入口網站中的使用者設定，以限制存取：

![系統管理入口網站限制存取](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> 非管理員仍可以透過命令列和其他程式設計介面存取 Azure AD 管理介面。

#### <a name="group-settings"></a>群組設定

**自助群組管理/使用者可以建立安全性群組/Microsoft 365 群組。** 如果雲端中沒有適用于群組的目前自助方案，客戶可能會決定將其關閉，直到準備好使用這項功能為止。

#### <a name="groups-recommended-reading"></a>建議閱讀的群組

- [什麼是 Azure Active Directory B2B 共同作業？](../external-identities/what-is-b2b.md)
- [整合應用程式與 Azure Active Directory](../develop/quickstart-register-app.md)
- [Azure Active Directory 中的應用程式、許可權及同意。](../develop/quickstart-register-app.md)
- [使用群組來管理 Azure Active Directory 中資源的存取權](./active-directory-manage-groups.md)
- [在 Azure Active Directory 中設定自助應用程式存取管理](../users-groups-roles/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>來自非預期位置的流量

攻擊者來自世界各地的各個部分。 使用條件式存取原則搭配位置作為條件來管理此風險。 條件式存取原則的 [位置條件](../conditional-access/location-condition.md) 可讓您封鎖從沒有商務理由登入之位置的存取權。

![建立新的命名位置](./media/active-directory-ops-guide/active-directory-ops-img14.png)

如果有的話，請使用安全性資訊和事件管理 (SIEM) 解決方案來分析和尋找跨區域的存取模式。 如果您未使用 SIEM 產品，或未從 Azure AD 擷取驗證資訊，建議您使用 [Azure 監視器](../../azure-monitor/overview.md) 來識別跨區域的存取模式。

## <a name="access-usage"></a>存取使用量

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Azure AD 記錄已封存，並與事件回應計畫整合

Azure AD 的登入活動、審核和風險事件的存取權，對於疑難排解、流量分析和辯論調查而言很重要。 Azure AD 透過具有有限保留期限的 REST Api，提供這些來源的存取權。 安全性資訊和事件管理 (SIEM) 系統或同等的封存技術，是長期儲存審核和支援能力的關鍵。 若要啟用 Azure AD 記錄的長期儲存，您必須將它們新增至現有的 SIEM 解決方案或使用 [Azure 監視器](../reports-monitoring/concept-activity-logs-azure-monitor.md)。 封存記錄，可作為事件回應計畫和調查的一部分使用。

#### <a name="logs-recommended-reading"></a>記錄建議的讀取

- [Azure Active Directory 稽核 API 參考](/graph/api/resources/directoryaudit?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [Azure Active Directory 登入活動報告 API 參考](/graph/api/resources/signin?view=graph-rest-beta%3fview%3dgraph-rest-beta)
- [使用 Azure AD 報告 API 搭配憑證來取得資料](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Azure Active Directory Identity Protection 的 Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 管理活動 API 參考](/office/office-365-management-api/office-365-management-activity-api-reference)
- [如何使用 Azure Active Directory Power BI 內容套件](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>[摘要]

安全的身分識別基礎結構有12個層面。 這份清單將協助您進一步保護和管理認證、定義驗證經驗、委派指派、測量使用量，以及根據企業安全性狀態定義存取原則。

- 將擁有者指派給主要工作。
- 實行解決方案來偵測弱式或洩漏的密碼、改進密碼管理和保護，以及進一步保護使用者對資源的存取。
- 管理裝置的身分識別，以隨時從任何位置保護您的資源。
- 執行無密碼 authentication。
- 提供整個組織的標準化單一登入機制。
- 將應用程式從 AD FS 遷移至 Azure AD，以提供更佳的安全性和更一致的管理性。
- 使用群組將使用者指派給應用程式，以提供更大的彈性和大規模管理的能力。
- 設定以風險為基礎的存取原則。
- 鎖定舊版驗證通訊協定。
- 偵測並補救違法的同意授與。
- 鎖定使用者和群組設定。
- 啟用 Azure AD 記錄的長期儲存，以進行疑難排解、使用方式分析和辯論調查。

## <a name="next-steps"></a>後續步驟

開始使用身分 [識別治理操作檢查和動作](active-directory-ops-guide-govern.md)。