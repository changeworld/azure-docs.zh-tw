---
title: 適用于內部部署工作負載的 Azure AD 雲端管控管理-Azure
description: 本主題說明適用于內部部署工作負載的雲端控管管理。
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
ms.openlocfilehash: ec74b9391c780cf673fe47bd82cc6d92534eb56d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84234100"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD 如何為內部部署工作負載提供雲端控管管理

Azure Active Directory （Azure AD）是一套全方位的身分識別即服務（IDaaS）解決方案，可供數百萬個組織用於身分識別、存取管理和安全性的所有層面。 Azure AD 保有超過10億個使用者身分識別，並協助使用者登入並安全地存取這兩者：

* 外部資源，例如 Microsoft Office 365、Azure 入口網站，以及數以千計的其他軟體即服務（SaaS）應用程式。
* 內部資源，例如組織的公司網路和內部網路上的應用程式，以及由該組織開發的任何雲端應用程式。

如果組織有內部部署工作負載，則可以使用 Azure AD （如果它們是「純雲端」）或「混合式」部署。 Azure AD 的混合式部署，可以是組織將其 IT 資產遷移至雲端，或繼續整合現有內部部署基礎結構與新的雲端服務等策略的一部分。

在過去，「混合式」組織已將 Azure AD 視為其現有內部部署基礎結構的延伸。 在這些部署中，內部部署身分識別治理管理、Windows Server Active Directory 或其他內部的目錄系統都是控制點，而使用者和群組會從這些系統同步處理到雲端目錄，例如 Azure AD。 一旦這些身分識別位於雲端之後，即可供 Office 365、Azure 和其他應用程式使用。

![身分識別生命週期](media/cloud-governed-management-for-on-premises//image1.png)

當組織將更多 IT 基礎結構及其應用程式移至雲端時，許多人都在尋找以服務身分識別管理的改良安全性和簡化管理功能。 在 Azure AD 中，雲端提供的 IDaaS 功能，提供了解決方案和功能，讓組織能夠快速採用並將更多的身分識別管理從傳統內部部署系統移至 Azure AD，同時繼續支援現有和新的應用程式，藉此加速轉換到雲端管理。

本白皮書概述 Microsoft 的混合式 IDaaS 策略，並說明組織如何使用現有應用程式的 Azure AD。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>雲端管控身分識別管理的 Azure AD 方法

當組織轉換至雲端時，他們需要保證他們可以控制其完整的環境-更安全且更能洞察活動、自動化支援及主動式深入解析。 「**雲端控管管理**」說明組織如何從雲端管理及控制他們的使用者、應用程式、群組和裝置。

在這個現代化的世界中，組織需要能夠大規模地管理，因為 SaaS 應用程式的激增，以及共同作業和外部身分識別的增加角色。 雲端的新風險面表示組織必須有更快的回應性，而危害雲端使用者的惡意執行者可能會影響雲端和內部部署應用程式。

特別是，混合式組織必須能夠委派和自動化工作，這在過去是以手動方式執行。 若要將工作自動化，他們需要可協調不同身分識別相關資源（使用者、群組、應用程式、裝置）之生命週期的 Api 和進程，讓他們可以將這些資源的日常管理委派給核心 IT 人員以外的更多個人。 Azure AD 透過使用者帳戶管理和使用者的原生驗證來解決這些需求，而不需要內部部署身分識別基礎結構。 不是建立內部部署基礎結構，可以讓具有新使用者群的組織（例如不是源自其內部部署目錄的商業夥伴）受益，但其存取管理對於達成商業成果至關重要。

此外，不需要治理就能完成管理---而且此新世界的治理是身分識別系統的整合部分，而不是附加元件。 身分識別控管可讓組織管理員工、企業合作夥伴和廠商以及服務和應用程式之間的身分識別和存取週期。

整合身分識別控管讓組織能夠更輕鬆地轉換到雲端管控管理，讓 IT 能夠進行調整，解決來賓的新挑戰，並提供比客戶對內部部署基礎結構更深入的見解和自動化。 此新世界的治理意味著組織能夠在存取組織內的資源時擁有透明度、可見度和適當的控制。 有了 Azure AD，安全性作業和 audit 團隊就能看到誰擁有---，以及誰可以存取組織中的哪些資源（在哪些裝置上）、這些使用者對該存取進行的動作，以及組織是否根據公司或法規原則，使用適當的控制項來移除或限制存取權。

新的管理模型可讓組織同時享有 SaaS 和企業營運（LOB）應用程式，因為它們更容易管理及保護這些應用程式的存取。 藉由整合應用程式與 Azure AD，組織將能夠以一致的方式使用和管理跨雲端和內部部署的存取。 應用程式生命週期管理會變得更自動化，Azure AD 針對在內部部署身分識別管理中無法輕鬆實現的應用程式使用方式提供豐富的深入解析。 透過 Azure AD、Office 365 群組和小組自助功能，組織可以輕鬆地建立群組來進行存取管理和共同作業，以及在雲端中新增或移除使用者，以啟用共同作業和存取管理需求。

選取適用于雲端管控管理的正確 Azure AD 功能取決於要使用的應用程式，以及這些應用程式將如何與 Azure AD 整合。 下列各節概述用於 AD 整合應用程式的方法，以及使用同盟通訊協定（例如 SAML、OAuth 或 OpenID Connect）的應用程式。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>AD 整合式應用程式的雲端管控管理

Azure AD 透過安全的遠端存取和對這些應用程式的條件式存取，來改善組織內部部署 Active Directory 整合應用程式的管理。 此外，Azure AD 也會提供使用者現有 AD 帳戶的帳戶生命週期管理和認證管理，包括：

* **保護內部部署應用程式的遠端存取和條件式存取**

對於許多組織而言，管理從雲端存取內部部署 AD 整合式 web 和遠端桌面應用程式的第一步，是在這些應用程式前面部署[應用程式 proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) ，以提供安全的遠端存取。

單一登入 Azure AD 後，使用者可以透過外部 URL 或內部應用程式入口網站存取雲端和內部部署應用程式。 例如，應用程式 Proxy 提供遠端桌面、SharePoint 以及應用程式（例如 Tableau 和 Qlik sense）和企業營運（LOB）應用程式的遠端存取與單一登入。 此外，條件式存取原則可以包含顯示[使用](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)規定，並[確保使用者](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou)在能夠存取應用程式之前，先同意這些條款。

![應用程式 Proxy 架構](media/cloud-governed-management-for-on-premises/image2.png)

* **Active Directory 帳戶的自動生命週期管理**

身分識別控管可協助*組織在產能之間取得*平衡---使用者可以快速存取所需的資源，例如加入組織的時間？ ---和*安全性*---其存取一段時間的變更方式，例如該人員的雇用狀態變更時？ 身分識別生命週期管理是身分識別控管的基礎，而大規模有效控管必須使應用程式的身分識別生命週期管理基礎結構現代化。

對於許多組織而言，員工的身分識別生命週期會系結到該使用者在人力資本管理（HCM）系統中的標記法。 對於使用 Workday 作為其 HCM 系統的組織，Azure AD 可以確保 AD 中的使用者帳戶會[自動布建，並針對 Workday 中的背景工作取消布建](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)。 這麼做可讓您透過自動化 birthright 帳戶來提升使用者生產力，並藉由確保當使用者變更角色或離開組織時，自動更新應用程式存取權來管理風險。 Workday 驅動的使用者布建[部署計畫](https://aka.ms/WorkdayDeploymentPlan)是逐步指南，可讓組織在五個步驟的程式中，透過 Workday 的最佳做法實行 Active Directory 使用者布建解決方案。

Azure AD Premium 也包括 Microsoft Identity Manager，可以從其他內部部署 HCM 系統（包括 SAP、Oracle 電子商務和 Oracle PeopleSoft）匯入記錄。

企業對企業共同作業越來越需要授與組織外部人員的存取權。 [AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)共同作業可讓組織安全地與來賓使用者和外部合作夥伴共用其應用程式和服務，同時保有自己公司資料的控制權。

Azure AD 可以視需要[自動為來賓使用者建立 AD 中的帳戶](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)，讓商務來賓存取內部部署 AD 整合的應用程式，而不需要另一個密碼。 組織可以[為來賓使用者設定多重要素驗證（mfa）原則](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)，讓 MFA 檢查在應用程式 proxy 驗證期間完成。 此外，在雲端 B2B 使用者上完成的任何[存取權審查](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)也適用于內部部署使用者。 例如，如果雲端使用者是透過生命週期管理原則來刪除，內部部署使用者也會一併刪除。

**Active Directory 帳戶的認證管理**Azure AD 的自助式密碼重設可讓忘記密碼的使用者重新驗證，並重設其密碼，並將變更的密碼[寫入內部部署 Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)。 密碼重設程式也可以使用內部部署 Active Directory 密碼原則：當使用者重設其密碼時，系統會檢查其是否符合內部部署 Active Directory 原則，再將其認可至該目錄。 自助式密碼重設[部署計畫](https://aka.ms/deploymentplans/sspr)概述了透過 Web 和 Windows 整合式體驗向使用者推出自助式密碼重設的最佳作法。

![Azure AD SSPR 架構](media/cloud-governed-management-for-on-premises/image3.png)

最後，對於允許使用者在 AD 中變更其密碼的組織，可以將 AD 設定為使用與組織在 Azure AD 中使用的相同密碼原則，其方式是透過[Azure AD 的密碼保護功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)，目前為公開預覽狀態。

當組織已準備好要將裝載應用程式的作業系統移至雲端時，您可以將主控應用程式的作業系統移動到 Azure， [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)提供與 ad 相容的網域服務（例如加入網域、群組原則、LDAP 和 KERBEROS/NTLM 驗證）。 Azure AD Domain Services 與組織現有的 Azure AD 租使用者整合，讓使用者可以使用其公司認證進行登入。 此外，您可以使用現有的群組和使用者帳戶來保護對資源的存取，以確保將內部部署資源的「隨即轉移」到 Azure 基礎結構服務的工作更加順暢。

![Azure AD 網域服務](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>適用于內部部署同盟應用程式的雲端控管管理

對於已經使用內部部署身分識別提供者的組織而言，將應用程式移至 Azure AD 可讓您更安全地存取，以及更輕鬆地進行同盟管理的系統管理體驗。 Azure AD 可以使用 Azure AD 條件式存取，設定每個應用程式的細微存取控制，包括 Azure 多重要素驗證。 Azure AD 支援更多的功能，包括應用程式特定的權杖簽署憑證和可設定的憑證到期日。 這些功能、工具和指引可讓組織淘汰其內部部署身分識別提供者。 其中一個範例是 microsoft 自己的 IT，已將17987應用程式從 Microsoft 的內部 Active Directory 同盟服務（AD FS）移至 Azure AD。

![Azure AD 演進](media/cloud-governed-management-for-on-premises/image5.png)

若要開始將同盟應用程式遷移至 Azure AD 做為身分識別提供者，請參閱 https://aka.ms/migrateapps ，其中包含下列連結：

* 這份白皮書將[您的應用程式遷移至 Azure Active Directory](https://aka.ms/migrateapps/whitepaper)，其中提供遷移的優點，並說明如何在四個明顯概述的階段中規劃遷移：探索、分類、遷移和持續管理。 您將會引導您瞭解如何考慮此程式，並將您的專案細分成容易取用的部分。 此文件全篇都有在過程中可協助您的重要資源連結。

* 將[應用程式驗證從 Active Directory 同盟服務遷移至 Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide)更詳細探索規劃和執行應用程式遷移專案的四個階段的解決方案指南。 在本指南中，您將瞭解如何將這些階段套用至將應用程式從 Active Directory 同盟服務（AD FS）移至 Azure AD 的特定目標。

* [Active Directory 同盟服務遷移準備就緒腳本](https://aka.ms/migrateapps/adfstools)可在現有的內部部署 Active Directory 同盟服務（AD FS）伺服器上執行，以判斷應用程式是否已準備好要遷移至 Azure AD。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>跨雲端和內部部署應用程式的持續存取管理

組織需要一個處理常式來管理可調整的存取權。 使用者會繼續累積存取權限，而最終會超過一開始為他們布建的存取權。 此外，企業組織必須能夠有效率地進行調整，以持續開發和強制執行存取原則和控制項。

一般而言，IT 會將存取核准決策委派給商務決策者。 此外，IT 可能是使用者本身。 例如，存取歐洲境內公司行銷應用程式中的機密客戶資料的使用者需要知道公司的原則。 來賓使用者也可能不知道他們受邀的組織中資料的處理需求。

組織可以透過[動態群組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)之類的技術，將使用者布建與[SaaS 應用程式](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)的使用者布建，或是[整合使用系統來進行跨網域身分識別管理（SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)）標準的應用程式，來自動化存取生命週期流程。 組織也可以控制哪些[來賓使用者具有內部部署應用程式的存取權](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)。 接著可以利用週期性 [Azure AD 存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)，定期檢閱這些存取權限。

## <a name="future-directions"></a>未來的指示

在混合式環境中，Microsoft 的策略是啟用**雲端是身分識別之控制平面**的部署，而內部部署目錄和其他身分識別系統（例如 Active Directory 和其他內部部署應用程式）則是提供存取權給使用者的目標。 此策略將繼續確保這些應用程式和工作負載中依賴它們的許可權、身分識別和存取權。 在此結束狀態下，組織將能夠完全從雲端提升使用者生產力。

![Azure AD 架構](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>後續步驟

如需如何開始使用此旅程的詳細資訊，請參閱位於的 Azure AD 部署計畫 <https://aka.ms/deploymentplans> 。 它們提供如何部署 Azure Active Directory （Azure AD）功能的端對端指引。 每個計畫都會說明成功推出一般 Azure AD 功能所需的商業價值、規劃考慮、設計和操作程式。 當我們透過 Azure AD 新增功能以從雲端管理時，Microsoft 會持續更新部署計畫，以及從客戶部署和其他意見反應中學習到的最佳作法。
