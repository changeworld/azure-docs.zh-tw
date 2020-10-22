---
title: 針對內部部署工作負載 Azure AD 雲端控管管理-Azure
description: 本主題說明內部部署工作負載的雲端控管管理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed23ffa9971bf4c97b784f230053aed4b1acf0a4
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369789"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD 如何為內部部署工作負載提供雲端控管管理

Azure Active Directory (Azure AD) 是一項全方位的身分識別即服務 (IDaaS) 解決方案，這些組織會橫跨身分識別、存取管理和安全性的所有層面。 Azure AD 保存超過10億個使用者身分識別，並協助使用者登入並安全地存取兩者：

* 外部資源，例如 Microsoft 365、Azure 入口網站和數以千計的其他軟體即服務 (SaaS) 應用程式。
* 內部資源，例如組織的公司網路和內部網路上的應用程式，以及該組織開發的任何雲端應用程式。

如果組織擁有內部部署工作負載，則可以使用 Azure AD （如果它們是「純雲端」）或「混合式」部署。 Azure AD 的混合式部署，可以成為組織將其 IT 資產遷移至雲端的策略之一，或繼續整合現有的內部部署基礎結構與新的雲端服務。

在過去，「混合式」組織已將 Azure AD 視為其現有內部部署基礎結構的延伸。 在這些部署中，內部部署身分識別治理管理、Windows Server Active Directory 或其他內部目錄系統都是控制點，而使用者和群組會從這些系統同步到雲端目錄，例如 Azure AD。 一旦這些身分識別位於雲端中，就可以將它們提供給 Microsoft 365、Azure 和其他應用程式使用。

![身分識別生命週期](media/cloud-governed-management-for-on-premises//image1.png)

當組織將更多的 IT 基礎結構及其應用程式移至雲端時，許多人都在尋找以服務身分識別管理的改良安全性和簡化管理功能。 Azure AD 中的雲端提供的 IDaaS 功能可提供解決方案和功能，讓組織能夠快速地將更多的身分識別管理從傳統內部部署系統移至 Azure AD，同時繼續支援現有和新的應用程式，藉此加速轉換至雲端管理的管理。

本白皮書概述 Microsoft 的混合式 IDaaS 策略，並說明組織如何使用現有應用程式的 Azure AD。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>雲端受控身分識別管理的 Azure AD 方法

當組織轉換至雲端時，他們需要保證他們能夠掌控整個環境-更安全且更容易掌握活動、自動化支援和主動式見解。 「**雲端管制管理**」說明組織如何從雲端管理和管理其使用者、應用程式、群組和裝置。

在這個現代化的世界中，組織必須能夠大規模地管理，因為 SaaS 應用程式的激增，以及共同作業和外部身分識別的增加角色。 雲端的新風險環境，表示組織必須更具回應性，這是危害雲端使用者的惡意執行者可能會影響雲端和內部部署應用程式。

尤其是，混合式組織必須能夠委派和自動化工作，這在過去是以手動方式進行。 為了將工作自動化，他們需要 Api 和程式來協調不同身分識別相關資源的生命週期 (使用者、群組、應用程式、裝置) ，讓他們可以將這些資源的日常管理工作委派給核心 IT 人員以外的其他人。 Azure AD 在不需要內部部署身分識別基礎結構的情況下，透過使用者帳戶管理和原生驗證來解決這些需求。 若組織中有新的使用者（例如商業夥伴），而不是源自內部部署目錄，但其存取管理對於達成業務成果至關重要，則不會建立內部部署基礎結構。

此外，管理不會在沒有治理---的情況下完成，而且這個新世界中的治理是身分識別系統的整合式元件，而不是附加元件。 身分識別治理可讓組織管理員工、商業夥伴和廠商，以及服務和應用程式之間的身分識別和存取生命週期。

整合身分識別治理可讓組織更輕鬆地轉換至雲端管理的管理、讓 IT 能夠調整規模、解決來賓的新挑戰，並提供比客戶對內部部署基礎結構更深入的見解和自動化。 這項新世界中的治理表示組織可以在存取組織內的資源時擁有透明、可見度和適當的控制能力。 有了 Azure AD，安全性作業和審核小組就能看到誰有---，以及誰可以存取組織中的哪些資源 () 的裝置、這些使用者如何使用該存取權，以及組織是否有並使用適當的控制項來移除或限制存取權，以符合公司或法規原則。

新的管理模型可讓具有 SaaS 和企業營運 (LOB) 應用程式的組織受益，因為它們更容易管理及保護這些應用程式的存取權。 藉由整合應用程式與 Azure AD，組織將能夠一致地使用及管理雲端和內部部署之間的存取權。 應用程式生命週期管理變得更自動化，Azure AD 可提供豐富的應用程式使用方式見解，讓您無法在內部部署身分識別管理中輕鬆實現。 透過 Azure AD、Microsoft 365 群組和小組自助功能，組織可以輕鬆地建立用於存取管理和共同作業的群組，並在雲端中新增或移除使用者，以啟用共同作業和存取管理需求。

針對雲端管理的管理選取適當的 Azure AD 功能，取決於要使用的應用程式，以及這些應用程式將如何與 Azure AD 整合。 下列各節概述適用于 AD 整合應用程式的方法，以及使用同盟通訊協定的應用程式 (例如 SAML、OAuth 或 OpenID Connect) 。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>適用于 AD 整合式應用程式的雲端控管管理

Azure AD 透過安全的遠端存取以及對這些應用程式的條件式存取，改善組織內部部署 Active Directory 整合應用程式的管理。 此外，Azure AD 也為使用者現有的 AD 帳戶提供帳戶生命週期管理和認證管理，包括：

* **保護內部部署應用程式的遠端存取與條件式存取**

對於許多組織而言，管理從雲端存取內部部署 AD 整合 web 和遠端桌面應用程式的第一個步驟，就是在這些應用程式前面部署 [應用程式 proxy](../manage-apps/application-proxy.md) ，以提供安全的遠端存取。

單一登入 Azure AD 後，使用者可以透過外部 URL 或內部應用程式入口網站存取雲端和內部部署應用程式。 例如，應用程式 Proxy 可提供遠端存取和單一登入，讓遠端桌面、SharePoint 及應用程式（例如 Tableau 和 Qlik sense），以及企業營運 (LOB) 應用程式。 此外，條件式存取原則可以包括顯示 [使用](../conditional-access/terms-of-use.md) 規定，並確保使用者在能夠存取應用程式之前， [已同意這些](../conditional-access/require-tou.md) 條款。

![應用程式 Proxy 架構](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 帳戶的自動生命週期管理**

身分識別治理可協助 *組織在產能之間達成* 平衡---使用者可以如何快速存取他們所需的資源，例如加入組織的時候？ ---和 *安全性* ---其存取會如何隨著時間而變更，例如當該人的就業狀態變更時？ 身分識別生命週期管理是身分識別控管的基礎，而大規模有效控管必須使應用程式的身分識別生命週期管理基礎結構現代化。

對於許多組織來說，員工的身分識別生命週期會系結至人類資本管理 (HCM) 系統中的代表使用者。 針對使用 Workday 作為其 HCM 系統的組織，Azure AD 可以確保在 [workday 中自動布建和取消布建](../saas-apps/workday-inbound-tutorial.md)AD 中的使用者帳戶。 這麼做可透過自動化 birthright 帳戶來提升使用者生產力，並藉由確保使用者變更角色或離開組織時，自動更新應用程式存取來管理風險。 Workday 驅動的使用者布建 [部署計畫](https://aka.ms/WorkdayDeploymentPlan) 是逐步指南，可逐步引導組織在五個步驟的程式中，利用 workday 的最佳作法來 Active Directory 使用者布建解決方案。

Azure AD Premium 也包含 Microsoft Identity Manager，其可以從其他內部部署 HCM 系統（包括 SAP、Oracle 電子商務和 Oracle PeopleSoft）匯入記錄。

企業對企業的共同作業日益需要授與組織外部人員的存取權。 [AZURE AD B2B](/azure/active-directory/b2b/) 共同作業可讓組織安全地與來賓使用者和外部合作夥伴共用其應用程式和服務，同時保有對自己公司資料的控制權。

Azure AD 可以 [在必要時自動為來賓使用者建立 ad 帳戶](../external-identities/hybrid-cloud-to-on-premises.md) ，讓商務來賓存取內部部署 AD 整合的應用程式，而不需要其他密碼。 組織可以 [為來賓使用者設定多重要素驗證 (mfa) 原則](../external-identities/conditional-access.md)，以便在應用程式 proxy 驗證期間完成 mfa 檢查。 此外，在雲端 B2B 使用者上完成的任何 [存取權審核](../governance/manage-guest-access-with-access-reviews.md) 都適用于內部部署使用者。 例如，如果雲端使用者是透過生命週期管理原則刪除，則內部部署使用者也會一併刪除。

**Active Directory 帳戶的認證管理** Azure AD 的自助式密碼重設可讓已忘記密碼的使用者重新驗證及重設其密碼，並將變更的密碼 [寫入內部部署 Active Directory](../authentication/concept-sspr-writeback.md)。 密碼重設程式也可以使用內部部署 Active Directory 密碼原則：當使用者重設其密碼時，會進行檢查以確保它符合內部部署 Active Directory 原則，然後才將其認可至該目錄。 自助式密碼重設 [部署計畫](https://aka.ms/deploymentplans/sspr) 概述可透過 Web 和 Windows 整合式體驗將自助式密碼重設推出給使用者的最佳作法。

![Azure AD SSPR 架構](media/cloud-governed-management-for-on-premises/image3.png)

最後，對於允許使用者在 AD 中變更其密碼的組織，可以設定 AD 來使用與組織在 Azure AD 中使用的相同密碼原則（目前處於公開預覽狀態） [Azure AD 密碼保護功能](../authentication/concept-password-ban-bad-on-premises.md)。

當組織已準備好將裝載應用程式的作業系統移至雲端，並將裝載應用程式的作業系統移至雲端時， [Azure AD Domain Services](../../active-directory-domain-services/overview.md) 提供與 ad 相容的網域服務 (例如網域加入、群組原則、LDAP 和 KERBEROS/NTLM 驗證) 。 Azure AD Domain Services 與組織現有的 Azure AD 租使用者整合，讓使用者可以使用其公司認證登入。 此外，您可以使用現有的群組和使用者帳戶來保護對資源的存取，以確保將內部部署資源更順暢地「隨即轉移」至 Azure 基礎結構服務。

![Azure AD 網域服務](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>適用于內部部署同盟應用程式的雲端控管管理

對於已經使用內部部署身分識別提供者的組織而言，將應用程式移至 Azure AD 可提供更安全的存取，以及更容易進行同盟管理的管理體驗。 Azure AD 可以使用 Azure AD 條件式存取，設定細微的每個應用程式存取控制，包括 Azure Multi-Factor Authentication。 Azure AD 支援更多功能，包括應用程式特定的權杖簽署憑證和可設定的憑證到期日。 這些功能、工具和指引可讓組織淘汰其內部部署身分識別提供者。 其中一個範例是 microsoft 本身的 IT，將17987的應用程式從 Microsoft 的內部 Active Directory 同盟服務 (AD FS) 移至 Azure AD。

![Azure AD 演進](media/cloud-governed-management-for-on-premises/image5.png)

若要開始將同盟應用程式遷移至 Azure AD 作為身分識別提供者，請參閱 https://aka.ms/migrateapps ，其中包含下列連結：

* 將您的 [應用程式遷移至 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)的白皮書，可提供遷移的優點，並說明如何在四個清楚敘述的階段中規劃遷移：探索、分類、遷移和持續管理。 您將會引導您瞭解如何思考流程，並將您的專案細分為易於取用的部分。 此文件全篇都有在過程中可協助您的重要資源連結。

* 解決方案指南 [可將應用程式驗證從 Active Directory 同盟服務遷移至 Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) 更詳細地探索規劃和執行應用程式遷移專案的四個階段。 在本指南中，您將瞭解如何將這些階段套用至將應用程式從 Active Directory 同盟服務 (AD FS) 移至 Azure AD 的特定目標。

* [Active Directory 同盟服務的遷移就緒腳本](https://aka.ms/migrateapps/adfstools)可在現有的內部部署 Active Directory 同盟服務 (AD FS) 伺服器上執行，以判斷要遷移至 Azure AD 的應用程式是否就緒。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>跨雲端和內部部署應用程式的持續存取管理

組織需要一個流程來管理可調整的存取權。 使用者會繼續累積存取權，並以超過最初布建的許可權結束。 此外，企業組織必須能夠有效率地調整規模，以持續開發和強制執行存取原則和控制項。

一般而言，IT 會將存取核准決策委派給商務決策者。 此外，IT 可能是使用者本身。 例如，存取歐洲境內公司行銷應用程式中的機密客戶資料的使用者需要知道公司的原則。 來賓使用者也可能不知道其受邀組織中的資料處理需求。

組織可以透過 [動態群組](../enterprise-users/groups-dynamic-membership.md)之類的技術（例如，將使用者布建到 [SaaS 應用程式](../saas-apps/tutorial-list.md)），或 [使用系統整合的應用程式（用於跨網域身分識別管理 (SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)) 標準）來自動化存取生命週期程式。 組織也可以控制哪些 [來賓使用者可以存取內部部署應用程式](../external-identities/hybrid-cloud-to-on-premises.md)。 接著可以利用週期性 [Azure AD 存取權檢閱](../governance/access-reviews-overview.md)，定期檢閱這些存取權限。

## <a name="future-directions"></a>未來方向

在混合式環境中，Microsoft 的策略是為了讓 **雲端成為身分識別的控制平面**，以及內部部署目錄和其他身分識別系統（例如 Active Directory 和其他內部部署應用程式），提供可供使用者存取的目標。 此策略將繼續確保這些應用程式中的許可權、身分識別和存取權，以及依賴這些應用程式的工作負載。 在此結束狀態下，組織將能夠完全從雲端推動終端使用者生產力。

![Azure AD 架構](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>後續步驟

如需有關如何開始使用此旅程的詳細資訊，請參閱 Azure AD 部署計畫，位於 <https://aka.ms/deploymentplans> 。 它們提供有關如何部署 Azure Active Directory (Azure AD) 功能的端對端指引。 每個方案都會說明成功推出常見 Azure AD 功能所需的商業價值、規劃考慮、設計和操作程式。 當我們新增可透過 Azure AD 從雲端管理的新功能時，Microsoft 會持續以客戶部署的最佳作法和其他意見反應來更新部署計畫。