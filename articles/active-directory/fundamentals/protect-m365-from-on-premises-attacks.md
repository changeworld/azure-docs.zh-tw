---
title: 保護來自內部部署攻擊的 Microsoft 365
description: 如何確保內部部署攻擊不會影響 Microsoft 365 的指引
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecddb950c06c9f8e61f31e104051f5e3b3640ae5
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725005"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>保護來自內部部署攻擊的 Microsoft 365

許多客戶將其私人公司網路連線到 Microsoft 365，以受益于其使用者、裝置和應用程式。 不過，這些私用網路可能會遭入侵的方式有很多。 因為 Microsoft 365 可作為許多組織的「緊張系統」，所以保護其免于遭到入侵的內部部署基礎結構是很重要的。

本文說明如何設定您的系統，以保護您的 Microsoft 365 雲端環境免受內部部署的危害。 我們主要著重于 Azure AD 租使用者設定、Azure AD 租使用者安全地連線至內部部署系統的方式，以及以保護雲端系統免于內部部署入侵的方式來作業系統所需的取捨。

強烈建議您執行此指導方針，以保護您的 Microsoft 365 雲端環境。
> [!NOTE]
> 本文一開始是以 blog 文章的形式發行。 它已在此移出壽命和維護。 <br>
若要建立本文的離線版本，請使用瀏覽器的 [列印至 PDF] 功能。 請經常回來查看更新。

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>遭入侵內部部署環境的主要威脅向量


您的 Microsoft 365 雲端環境可享有廣泛的監視和安全性基礎結構。 使用機器學習和人類智慧來查看全球流量，可以快速偵測攻擊，並讓您以近乎即時的方式進行重新設定。 在將內部部署基礎結構連線到 Microsoft 365 的混合式部署中，許多組織會將信任委派給內部部署元件，以進行重大驗證和目錄物件狀態管理決策。
可惜的是，如果內部部署環境遭到入侵，則這些信任關係會導致攻擊者入侵您 Microsoft 365 環境的機會。

這兩個主要威脅向量為 **同盟信任關係** 和 **帳戶同步處理。** 這兩個向量都可以將您雲端的系統管理存取權授與攻擊者。

* 同盟 **信任關係**（例如 SAML 驗證）是透過您的內部部署身分識別基礎結構，用來向 Microsoft 365 進行驗證。 如果 SAML 權杖簽署憑證遭到入侵，同盟可讓任何具有該憑證的使用者模擬您雲端中的任何使用者。 **建議您停用同盟信任關係，以在可能的情況下 Microsoft 365 驗證。**

* **帳戶同步** 處理可以用來修改具有特殊許可權的使用者 (包括其認證) 或在 Microsoft 365 中授與系統管理許可權的群組。 **建議您確保同步處理的物件不會在 Microsoft 365 中的使用者以外的任何許可權，** 不論是直接或透過包含在信任的角色或群組中。 請確定這些物件在受信任的雲端角色或群組中沒有直接或嵌套的指派。

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>保護內部部署的 Microsoft 365 危害


若要解決上述的威脅向量，建議您遵守以下所述的原則：

![用於保護 Microsoft 365 的參考架構 ](media/protect-m365/protect-m365-principles.png)

*  **完全找出您的 Microsoft 365 系統管理員帳戶。** 它們應該是

    * Azure AD 中的主要。

     * 使用多重要素驗證進行驗證 (MFA) 。

     *  受 Azure AD 條件式存取保護。

     *  只能使用 Azure 受控工作站進行存取。

這些是限制使用的帳戶。 **在 Microsoft 365 中，不應該有具有系統管理許可權的內部部署帳戶。** 如需詳細資訊，請參閱 [Microsoft 365 系統管理員角色的總覽](/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)。
另請參閱 [Azure Active Directory 中 Microsoft 365 的角色](../roles/m365-workload-docs.md)。

*  **從 Microsoft 365 管理裝置。** 使用 Azure AD 加入和雲端式行動裝置管理 (MDM) 來消除內部部署裝置管理基礎結構的相依性，這可能會危害裝置和安全性控制措施。

* **沒有任何內部部署帳戶有更高的許可權可 Microsoft 365。**
    存取需要 NTLM、LDAP 或 Kerberos 驗證之內部部署應用程式的帳戶，需要組織內部部署身分識別基礎結構中的帳戶。 請確定這些帳戶（包括服務帳戶）不包含在特殊許可權雲端角色或群組中，而且這些帳戶的變更不會影響雲端環境的完整性。 具有特殊許可權的內部部署軟體不得影響 Microsoft 365 特殊許可權帳戶或角色。

*  **使用 Azure AD 雲端驗證** 來消除對內部部署認證的相依性。 請一律使用增強式驗證，例如 Windows Hello、FIDO、Microsoft Authenticator 或 Azure AD MFA。

## <a name="specific-recommendations"></a>特定建議


下列各節提供有關如何實行上述原則的特定指引。

### <a name="isolate-privileged-identities"></a>隔離特殊許可權身分識別


在 Azure AD 中，具有特殊許可權角色（例如系統管理員）的使用者，是建立及管理環境其餘部分的根信任。 執行下列做法以將危及的影響降至最低。

* 使用僅限雲端的帳戶進行 Azure AD 和 Microsoft 365 特殊許可權的角色。 d

* 部署特殊許可權 [存取裝置](/security/compass/privileged-access-devices#device-roles-and-profiles) 以進行特殊許可權存取，以管理 Microsoft 365 和 Azure AD。

*  立即部署 [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM)  (JIT) 存取具有特殊許可權角色的所有人帳戶，以及需要增強式驗證才能啟用角色。

* 提供系統管理角色 [最少的許可權來執行其](../roles/delegate-by-task.md)工作。

* 若要啟用同時包含委派和多個角色的更豐富角色指派體驗，請考慮使用 Azure AD 安全性群組或 Microsoft 365 組 (統稱為「雲端群組」 ) 並 [啟用角色型存取控制](../roles/groups-assign-role.md)。 您也可以使用 [管理單位](../roles/administrative-units.md) ，將角色的範圍限制在組織的某個部分。

* 部署 [緊急存取帳戶](../roles/security-emergency-access.md) ，且不要使用內部部署密碼保存庫來儲存認證。

如需詳細資訊，請參閱保護特殊許可權 [存取](/security/compass/overview)，此主題有詳細的指引。 此外，請參閱 [Azure AD 中系統管理員的安全存取做法](../roles/security-planning.md)。

### <a name="use-cloud-authentication"></a>使用雲端驗證 

認證是主要的攻擊媒介。 執行下列做法，讓認證更安全。

* [部署無密碼 authentication](../authentication/howto-authentication-passwordless-deployment.md)：部署無密碼認證，盡可能減少密碼的使用。 這些認證會以原生方式在雲端中進行管理和驗證。 從下列選項進行選擇：

   * [商務 Windows Hello](/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Authenticator 應用程式](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 安全性金鑰](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [部署 Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)： [使用 Azure AD MFA 布建多個強式認證](../fundamentals/resilience-in-credentials.md)。 如此一來，雲端資源的存取權除了可操作的內部部署密碼之外，還需要 Azure AD 中管理的認證。

   * 如需詳細資訊，請參閱 [使用 Azure Active Directory 建立彈性的存取控制管理原則](./resilience-overview.md)。

**限制和取捨**

* 混合式帳戶密碼管理需要混合式元件，例如密碼保護代理程式和密碼回寫代理程式。 如果您的內部部署基礎結構遭到入侵，攻擊者可以控制這些代理程式所在的電腦。 雖然這不會危害您的雲端基礎結構，但您的雲端帳戶將無法保護這些元件免于內部部署的危害。

*  從 Active Directory 同步處理的內部部署帳戶，會標示為永遠不會在 Azure AD 中到期，根據假設內部部署 AD 密碼原則可減輕此問題。 如果您的內部部署 AD 遭到入侵，而且需要停用來自 AD connect 的同步處理，則必須設定選項 [EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md)。

## <a name="provision-user-access-from-the-cloud"></a>從雲端布建使用者存取權

布建指的是在應用程式或身分識別提供者中建立使用者帳戶和群組。

![布建架構圖表](media/protect-m365/protect-m365-provision.png)

* **從雲端 HR 應用程式布建至 Azure AD：** 這可讓內部部署的洩漏隔離，而不會中斷您的從雲端 HR 應用程式到 Azure AD 的 Leaver 週期。

* **雲端應用程式：** 可能的話，請部署 [Azure AD App](../app-provisioning/user-provisioning.md) 布建，而不是內部部署布建解決方案。 這可保護某些 SaaS 應用程式，使其免于因內部部署缺口而有害的惡意使用者設定檔。 

* **外部** 身分識別：使用 [AZURE AD B2B 協同](../external-identities/what-is-b2b.md)作業。
    這會降低對內部部署帳戶的相依性，以便與夥伴、客戶和供應商進行外部共同作業。 請仔細評估與其他身分識別提供者的任何直接同盟。 建議您以下列方式限制 B2B 來賓帳戶。

   *  限制來賓存取目錄中的群組和其他屬性。 使用外部共同作業設定，以限制來賓讀取不是其成員之群組的能力。 

    *   封鎖對 Azure 入口網站的存取。 您可以進行罕見的必要例外狀況。  建立包含所有來賓和外部使用者的條件式存取原則，然後 [執行原則來封鎖存取](../../role-based-access-control/conditional-access-azure-management.md)。 

* **中斷** 連線的樹系：使用 [Azure AD 雲端](../cloud-provisioning/what-is-cloud-provisioning.md)布建。 這可讓您連線到中斷連線的樹系，而不需要建立跨樹系連線或信任，進而放寬內部部署缺口的影響。 * 
 
**限制和取捨：**

* 當用來布建混合式帳戶時，來自雲端 HR 系統的 Azure AD 會依賴內部部署同步處理，以完成從 AD 到 Azure AD 的資料流程。 如果同步處理中斷，Azure AD 中將無法使用新的員工記錄。

## <a name="use-cloud-groups-for-collaboration-and-access"></a>使用雲端群組進行共同作業和存取

雲端群組可讓您將共同作業和存取權與內部部署基礎結構分離開來。

* 共同作業 **：** 使用 Microsoft 365 群組和 Microsoft 團隊進行新式共同作業。 解除委任內部部署通訊群組清單，並 [將通訊群組清單升級為 Outlook 中的 Microsoft 365 群組](/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide)。

* **存取：** 使用 Azure AD 安全性群組或 Microsoft 365 群組，在 Azure AD 中授權存取應用程式。
* **Office 365 授權：** 使用以群組為基礎的授權，以使用僅限雲端的群組布建至 Office 365。 這會將群組成員資格的控制與內部部署基礎結構分開。

用於存取的群組擁有者應該被視為具有特殊許可權的身分識別，以避免在內部部署遭到入侵的成員接管。
接管包括直接操作內部部署的群組成員資格或操作內部部署屬性，這些屬性可能會影響 Microsoft 365 中的動態群組成員資格。

## <a name="manage-devices-from-the-cloud"></a>從雲端管理裝置


使用 Azure AD 功能來安全地管理裝置。

-   **使用 Windows 10 工作站：使用** MDM 原則 [部署 Azure AD 加入](../devices/azureadjoin-plan.md) 的裝置。 啟用 [Windows Autopilot](/mem/autopilot/windows-autopilot) ，以提供完全自動化的布建體驗。

    -   取代 Windows 8.1 和先前的電腦。

    -   請勿將伺服器作業系統電腦部署為工作站。

    -   使用 [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) 作為所有裝置管理工作負載的授權來源。

-   [**部署特殊許可權存取裝置**](/security/compass/privileged-access-devices#device-roles-and-profiles) 以進行特殊許可權存取，以管理 Microsoft 365 和 Azure AD。

 ## <a name="workloads-applications-and-resources"></a>工作負載、應用程式和資源 

-   **內部部署 SSO 系統：** 取代任何內部部署同盟和 Web 存取管理基礎結構，並設定應用程式以使用 Azure AD。  

-   **支援新式驗證通訊協定的 SaaS 和 LOB 應用程式：** [使用 Azure AD 進行單一登入](../manage-apps/what-is-single-sign-on.md)。 您設定使用 Azure AD 進行驗證的應用程式愈多，在內部部署遭到入侵的情況下就會降低風險。


* **繼承應用程式** 

   * 對於不支援新式驗證的繼承應用程式，可以透過 [Azure AD 的應用程式 Proxy](../manage-apps/application-proxy.md)來啟用驗證、授權和遠端存取。您也可以使用  [安全的混合式存取合作夥伴](../manage-apps/secure-hybrid-access.md)整合，透過網路或應用程式傳遞控制器解決方案來啟用它們。   

   * 選擇支援新式驗證的 VPN 廠商，並將其驗證與 Azure AD 整合。 在 anon 部署的情況下，您可以使用 Azure AD 停用 VPN 來停用或封鎖存取。

*  **應用程式和工作負載伺服器**

   * 需要伺服器的應用程式或資源可以遷移至 Azure IaaS，並使用 [Azure AD Domain Services](../../active-directory-domain-services/overview.md) (Azure AD DS) 將信任和相依性與 AD 內部部署的相依性分離。 為了達成這種分離，用於 Azure AD DS 的虛擬網路應該不會連接到公司網路。

   * 遵循 [認證分層](/security/compass/privileged-access-access-model#ADATM_BM)的指導方針。 應用程式伺服器通常視為第1層資產。

 ## <a name="conditional-access-policies"></a>條件式存取原則

使用 Azure AD 條件式存取來解讀信號，並根據它們進行驗證決策。 如需詳細資訊，請參閱 [條件式存取部署計畫。](../conditional-access/plan-conditional-access.md)

* [舊版驗證通訊協定](../fundamentals/auth-sync-overview.md)：使用條件式存取，盡可能 [封鎖傳統驗證](../conditional-access/howto-conditional-access-policy-block-legacy.md) 通訊協定。 此外，請使用應用程式特定設定，在應用層級停用舊版驗證通訊協定。

   * 請參閱 [Exchange online](/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) 和 [SharePoint online](/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps)的特定詳細資料。

* 執行建議的身分 [識別和裝置存取設定。](/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)

* 如果您使用的 Azure AD 版本不包含條件式存取，請確定您使用的是 [Azure AD 安全性預設值](../fundamentals/concept-fundamentals-security-defaults.md)。

   * 如需 Azure AD 功能授權的詳細資訊，請參閱 [Azure AD 定價指南](https://azure.microsoft.com/pricing/details/active-directory/)。

## <a name="monitoring"></a>監視 

一旦設定您的環境，以保護您的 Microsoft 365 免于內部部署的危害，請 [主動監視](../reports-monitoring/overview-monitoring.md) 環境。
### <a name="scenarios-to-monitor"></a>要監視的案例

除了您組織專屬的任何案例之外，還可以監視下列主要案例。 例如，您應該主動監視對商務關鍵應用程式和資源的存取。

* **可疑活動**：應針對可疑活動監視所有 [Azure AD 風險事件](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation) 。 [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) 原本就與 Azure 資訊安全中心整合。

   * 定義名為「 [位置](../reports-monitoring/quickstart-configure-named-locations.md) 」的網路，以避免在以位置為基礎的信號上偵測出雜訊。 
*  **使用者實體行為分析 (UEBA) 警示** 使用 UEBA 取得異常偵測的見解。
   * Microsoft Cloud App Discovery (MCAS) 提供 [雲端 UEBA](/cloud-app-security/tutorial-ueba)。

   * 您可以 [從 Azure ATP 整合內部部署 UEBA](/defender-for-identity/install-step2)。 MCAS 會讀取來自 Azure AD Identity Protection 的信號。 

* **緊急存取帳戶活動**：使用 [緊急存取帳戶](../roles/security-emergency-access.md) 的任何存取都應受監視，並建立警示以進行調查。 此監視必須包含： 

   * 登入。 

   * 認證管理。 

   * 群組成員資格的任何更新。 

   *    應用程式指派。 
* 特殊 **許可權角色活動**：設定和審核 [Azure AD PIM 所產生的安全性警示](../privileged-identity-management/pim-how-to-configure-security-alerts.md?tabs=new#security-alerts)。
    藉由在每次直接指派使用者時產生警示，來監視 PIM 外的特殊許可權角色的直接指派。
* **Azure AD 整個租使用者的** 設定：對租使用者範圍設定的任何變更都應該在系統中產生警示。 這些包括但不限於
  *  更新自訂網域  

  * Azure AD B2B 允許/封鎖清單變更。
  * 透過直接同盟或社交登入) Azure AD B2B 允許的身分識別提供者 (SAML Idp。  
  * 條件式存取或風險原則變更 

* **應用程式和服務主體物件**：
   * 可能需要條件式存取原則的新應用程式或服務主體。 

   * 新增至服務主體的其他認證。
   * 應用程式同意活動。 

* **自訂角色**：
   * 更新自訂角色定義。 

   * 已建立新的自訂角色。 

### <a name="log-management"></a>記錄檔管理

定義記錄儲存體和保留原則、設計和實行，以促進一致的工具組，例如 Azure Sentinel、一般查詢，以及調查和取證研究手冊等 SIEM 系統。

* **Azure AD 記錄** 以一致的最佳作法（包括診斷設定、記錄保留和 SIEM 內嵌）產生的內嵌記錄和信號。 記錄策略必須包含下列 Azure AD 記錄：
   * 登入活動 

   * 稽核記錄 

   * 風險事件 

Azure AD 為登入活動記錄和審核記錄提供 [Azure 監視器整合](../reports-monitoring/concept-activity-logs-azure-monitor.md) 。 您可以透過 [MICROSOFT GRAPH API](/graph/api/resources/identityriskevent)來內嵌風險事件。 您可以 [將 Azure AD 記錄串流至 Azure 監視器記錄](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)。

* **混合式基礎結構 OS 安全性記錄。** 所有的混合式身分識別基礎結構作業系統記錄都應該封存，並謹慎監視為 <br>第0層系統，提供介面區的含意。 這包括： 

   *  Azure AD Connect。 您必須部署[Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md) ，才能監視身分識別同步處理。

   *  應用程式 Proxy 代理程式 


   * 密碼回寫代理程式 

   * 密碼保護閘道電腦  

   * 具有 Azure MFA RADIUS 延伸模組的 NPS 

## <a name="next-steps"></a>後續步驟
* [使用 Azure AD 建置身分識別和存取管理的復原能力](resilience-overview.md)

* [保護資源的外部存取](secure-external-access-resources.md) 
* [將您的所有應用程式與 Azure AD 整合](five-steps-to-full-application-integration-with-azure-ad.md)