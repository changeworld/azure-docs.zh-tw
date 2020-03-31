---
title: 本地工作負載的 Azure AD 雲治理管理 - Azure
description: 本主題介紹本地工作負載的雲治理管理。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109510"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Azure AD 如何為本地工作負載提供雲治理管理

Azure 活動目錄 （Azure AD） 是一種全面的身份標識作為服務 （IDaaS） 解決方案，由數百萬組織使用，涵蓋標識、訪問管理和安全性的所有方面。 Azure AD 包含超過 10 億個使用者身份，可説明使用者登錄並安全地訪問這兩者：

* 外部資源，如 Microsoft Office 365、Azure 門戶和數千個其他軟體即服務 （SaaS） 應用程式。
* 內部資源（如組織商業網路和 Intranet 上的應用程式）以及該組織開發的任何雲應用程式。

如果組織是"純雲"，則可以使用 Azure AD，或者使用"混合"部署（如果他們具有本地工作負荷）。 Azure AD 的混合部署可以是組織將其 IT 資產遷移到雲的策略的一部分，或者繼續將現有的本地基礎結構與新雲服務集成。

從歷史上看，"混合"組織將 Azure AD 視為其現有本地基礎結構的擴展。 在這些部署中，本地標識治理管理、Windows Server 活動目錄或其他內部目錄系統是控制點，使用者和組從這些系統同步到雲目錄（如 Azure AD）。 一旦這些標識在雲中，它們就可以提供給 Office 365、Azure 和其他應用程式。

![身分識別生命週期](media/cloud-governed-management-for-on-premises//image1.png)

隨著組織將更多的 IT 基礎架構及其應用程式遷移到雲中，許多公司都在尋求改進身份管理作為服務的安全性和簡化的管理功能。 Azure AD 中雲交付的 IDaaS 功能通過提供解決方案和功能，使組織能夠快速採用和移動更多其身份管理，從而加快向雲治理管理的過渡系統到 Azure AD，同時繼續支援現有應用程式和新應用程式。

本文概述了 Microsoft 的混合 IDaaS 策略，並介紹了組織如何使用 Azure AD 進行現有應用程式。

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Azure AD 雲治理標識管理的方法

隨著組織向雲過渡，他們需要保證自己能夠控制其完整的環境 - 更高的安全性和對活動的可見度，並由自動化和主動洞察提供支援。 "**雲治理管理**"描述了組織如何管理和管理來自雲的使用者、應用程式、組和設備。

在這個現代世界中，由於 SaaS 應用程式的激增以及協作和外部身份的作用日益增加，組織需要能夠大規模有效地管理。 雲的新風險環境意味著組織必須回應更快 - 危害雲使用者的惡意參與者可能會影響雲和本地應用程式。

特別是，混合組織需要能夠委派和自動化任務，這在歷史上是 IT 手動執行的。 要自動執行任務，他們需要協調不同標識相關資源（使用者、組、應用程式、設備）生命週期的 API 和流程，以便他們可以將這些資源的日常管理委派給核心 IT 員工。 Azure AD 通過使用者帳戶管理和使用者本機身份驗證來滿足這些要求，而無需本地標識基礎結構。 不構建本地基礎結構可以使具有新使用者社區的組織受益，例如業務合作夥伴，這些使用者不是源自其本地目錄，但其訪問管理對於實現業務成果至關重要。

此外，如果沒有治理---，管理是不完整的，在這個新世界中，治理是標識系統的集成部分，而不是附加元件。 身份治理使組織能夠跨員工、業務合作夥伴和供應商以及服務和應用程式管理身份和訪問生命週期。

通過採用身份治理，使組織能夠更輕鬆地過渡到雲治理管理，允許 IT 部門擴展、向來賓應對新的挑戰，並提供比客戶擁有更深入的見解和自動化本地基礎結構。 在這個新世界中的治理意味著組織能夠對組織內資源的訪問具有透明度、可見度和適當的控制。 借助 Azure AD，安全操作和審核團隊可以瞭解誰擁有---以及誰應該擁有 - 訪問組織中哪些資源（在哪些設備上）、這些使用者使用該存取權限執行的操作，以及組織是否具有和使用適當的資源根據公司或監管政策取消或限制訪問的控制措施。

新的管理模式使具有 SaaS 和業務線 （LOB） 應用程式的組織受益，因為它們更易於管理和安全地訪問這些應用程式。 通過將應用程式與 Azure AD 集成，組織將能夠一致地使用和管理跨雲和本地源標識的訪問。 應用程式生命週期管理變得更加自動化，Azure AD 提供了對應用程式使用方式的豐富見解，在本地標識管理中不容易實現。 通過 Azure AD、Office 365 組和 Teams 自助服務功能，組織可以輕鬆地創建用於訪問管理和協作的組，並在雲中添加或刪除使用者，以啟用協作和訪問管理要求。

為雲管理選擇正確的 Azure AD 功能取決於要使用的應用程式以及這些應用程式將如何與 Azure AD 集成。 以下各節概述了 AD 集成應用程式和使用聯合協定的應用程式（例如 SAML、OAuth 或 OpenID 連接）的方法。

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>面向 AD 集成應用程式的雲治理管理

Azure AD 通過安全遠端存取和對這些應用程式進行條件訪問，改進了組織的本地活動目錄集成應用程式的管理。 此外，Azure AD 還為使用者的現有 AD 帳戶提供帳戶生命週期管理和憑據管理，包括：

* **為本地應用程式安全遠端存取和條件訪問**

對於許多組織來說，管理本地 AD 集成 Web 和基於遠端桌面的應用程式從雲訪問的第一步是在這些應用程式前面部署[應用程式代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)，以提供安全的遠端存取。

單一登入 Azure AD 後，使用者可以透過外部 URL 或內部應用程式入口網站存取雲端和內部部署應用程式。 例如，應用程式代理提供遠端存取和單一登入遠端桌面、SharePoint 以及 Tableau 和 Qlik 等應用程式以及業務線 （LOB） 應用程式。 此外，條件訪問策略可以包括顯示[使用條款](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)，並確保使用者在能夠訪問應用程式之前[已同意它們](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou)。

![應用代理體系結構](media/cloud-governed-management-for-on-premises/image2.png)

* **活動目錄帳戶的自動生命週期管理**

身份治理可説明組織在*工作效率*---人員訪問所需資源（例如何時加入組織）之間實現平衡？ ---*和安全*---，他們的存取權限應該隨著時間的推移而變化，例如此人的就業狀態何時發生變化？ 身分識別生命週期管理是身分識別控管的基礎，而大規模有效控管必須使應用程式的身分識別生命週期管理基礎結構現代化。

對於許多組織來說，員工的身份生命週期與該使用者在人力資本管理 （HCM） 系統中的表示形式相關聯。 對於使用工作日作為 HCM 系統的組織，Azure AD 可確保 AD 中的使用者帳戶[自動預配和取消預配到工作日中的工作人員](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)。 這樣做通過自動實現與生俱來帳戶的自動化從而提高了使用者工作效率，並通過確保當使用者更改角色或離開組織時自動更新應用程式訪問來管理風險。 工作日驅動的使用者預配[部署計畫是](https://aka.ms/WorkdayDeploymentPlan)分步指南，用於引導組織在五步過程中完成"工作日"到"活動目錄使用者預配"解決方案的最佳實踐實現。

Azure AD 高級版還包括 Microsoft 標識管理器，該管理器可以從其他本地 HCM 系統（包括 SAP、Oracle 電子商務和 Oracle PeopleSoft）導入記錄。

企業對企業協作越來越需要授予組織外部人員存取權限。 [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/)協作使組織能夠安全地與來賓使用者和外部合作夥伴共用其應用程式和服務，同時保持對自身公司資料的控制。

Azure AD 可以根據需要[在 AD 中自動為來賓使用者創建帳戶](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)，使業務來賓無需另一個密碼即可訪問本地 AD 集成應用程式。 組織可以為[來賓使用者設置多重要素驗證 （MFA） 策略](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)，以便在應用程式代理身份驗證期間執行 MFA 檢查。 此外，對雲 B2B 使用者執行的任何[訪問評論](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)都適用于本地使用者。 例如，如果通過生命週期管理原則刪除了雲使用者，則本地使用者也會被刪除。

**活動目錄帳戶的憑據管理**Azure AD 的自助服務密碼重設允許忘記密碼的使用者重新驗證並重置其密碼，更改的密碼[將寫入本地活動目錄](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)。 密碼重設過程還可以使用本地活動目錄密碼原則：當使用者重置其密碼時，將檢查它以確保在將其提交到該目錄之前滿足本地活動目錄策略。 自助服務密碼重設[部署計畫](https://aka.ms/deploymentplans/sspr)概述了通過 Web 和 Windows 集成體驗向使用者推出自助服務密碼重設的最佳做法。

![Azure AD SSPR 架構](media/cloud-governed-management-for-on-premises/image3.png)

最後，對於允許使用者在 AD 中更改其密碼的組織，可以通過[Azure AD 密碼保護功能](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)（當前處於公共預覽版中）將 AD 配置為使用與組織在 Azure AD 中使用的密碼原則相同的密碼原則。

當組織準備好通過將託管應用程式的作業系統移動到 Azure 將 AD 集成應用程式移動到雲中時[，Azure AD 域服務](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview)提供與 AD 相容的域服務（如域聯接、群組原則、LDAP 和 Kerberos/NTLM 身份驗證）。 Azure AD 域服務與組織現有的 Azure AD 租戶集成，使使用者能夠使用其公司憑據登錄。 此外，現有組和使用者帳戶可用於保護對資源的訪問，確保將本地資源更順暢地"提升和轉移"到 Azure 基礎結構服務。

![Azure AD 網域服務](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>基於本地同盟應用程式的雲治理管理

對於已經使用本地標識提供程式的組織，將應用程式移動到 Azure AD 可實現更安全的訪問，並簡化聯合管理管理體驗。 Azure AD 支援通過使用 Azure AD 條件訪問配置每個應用程式精細的訪問控制項，包括 Azure 多重要素驗證。 Azure AD 支援更多功能，包括特定于應用程式的權杖簽署憑證和可配置的證書到期日期。 這些功能、工具和指南使組織能夠停用其本地標識提供程式。 例如，微軟自己的 IT 部門將 17，987 個應用程式從 Microsoft 的內部活動目錄聯合服務 （AD FS） 遷移到 Azure AD。

![Azure AD 演進](media/cloud-governed-management-for-on-premises/image5.png)

要開始將同盟應用程式遷移到 Azure AD 作為標識提供程式，請參閱https://aka.ms/migrateapps包含指向以下連結的連結：

* 白皮書[將應用程式遷移到 Azure 活動目錄](https://aka.ms/migrateapps/whitepaper)，其仲介紹了遷移的好處，並介紹了如何在四個明確概述的階段規劃遷移：發現、分類、遷移和持續管理。 您將指導您如何思考流程，並將專案分解為便於使用的部分。 此文件全篇都有在過程中可協助您的重要資源連結。

* 解決方案指南[將應用程式身份驗證從活動目錄聯合服務遷移到 Azure 活動目錄](https://aka.ms/migrateapps/adfssolutionguide)，更詳細地探討了規劃和執行應用程式遷移專案的四個階段。 在本指南中，您將瞭解如何將這些階段應用於將應用程式從活動目錄聯合服務 （AD FS） 移動到 Azure AD 的特定目標。

* [活動目錄聯合服務遷移就緒腳本](https://aka.ms/migrateapps/adfstools)可以在現有本地活動目錄聯合服務 （AD FS） 伺服器上運行，以確定應用程式遷移到 Azure AD 的準備情況。

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>雲和本地應用程式的持續訪問管理

組織需要一個流程來管理可擴展的訪問。 使用者繼續累積存取權限，最終超出最初為其預配的許可權。 此外，企業組織需要能夠有效地擴展，以不斷制定和實施訪問策略和控制。

一般而言，IT 會將存取核准決策委派給商務決策者。 此外，IT 可能是使用者本身。 例如，存取歐洲境內公司行銷應用程式中的機密客戶資料的使用者需要知道公司的原則。 來賓使用者可能還不知道受邀到的組織中資料的處理方式要求。

組織可以通過[動態組](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)等技術，加上使用者預配到[SaaS 應用程式](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)，或使用[跨域身份管理系統 （SCIM） 標準集成的應用程式](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)來自動執行訪問生命週期過程。 組織還可以控制哪些[訪客使用者有權訪問本地應用程式](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises)。 接著可以利用週期性 [Azure AD 存取權檢閱](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)，定期檢閱這些存取權限。

## <a name="future-directions"></a>未來方向

在混合環境中，Microsoft 的策略是啟用部署，其中**雲是標識的控制平面**，而本地目錄和其他標識系統（如 Active Directory 和其他本地應用程式）是向使用者預配存取權限的目標。 此策略將繼續確保依賴這些許可權和工作負載中的許可權、身份和存取權限。 在此結束狀態下，組織將能夠完全從雲中提高最終使用者的工作效率。

![Azure AD 架構](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>後續步驟

有關如何開始此旅程的詳細資訊，請參閱位於 的<https://aka.ms/deploymentplans>Azure AD 部署計畫。 它們提供有關如何部署 Azure 活動目錄 （Azure AD） 功能的端到端指導。 每個計畫都解釋了成功推出常見 Azure AD 功能所需的業務價值、規劃注意事項、設計和操作過程。 當我們使用 Azure AD 從雲添加新功能進行管理時，Microsoft 會不斷更新部署計畫，並獲取從客戶部署和其他回饋中學到的最佳實踐。
