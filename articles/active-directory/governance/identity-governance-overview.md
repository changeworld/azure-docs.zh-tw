---
title: Identity Governance - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance 可讓您透過適當的程序與可見性，平衡貴組織的安全性需求與員工生產力。
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ecbebfc75cb8c77ebb99ad04b1f9e33b3c4ef64
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306458"
---
# <a name="what-is-azure-ad-identity-governance"></a>什麼是 Azure AD Identity Governance？

Azure Active Directory (Azure AD) Identity Governance 可讓您透過適當的程序與可見性，平衡貴組織的安全性需求與員工生產力。 其可為您提供功能，確保正確人員會有正確資源的正確存取權。 這些功能和相關的 Azure AD 與 Enterprise Mobility + Security 功能可讓您藉由保護、監視及稽核對重要資產的存取，來降低存取風險，同時確保員工和企業合作夥伴擁有生產力。  

Identity Governance 讓組織能夠對員工、商務夥伴和廠商以及對內部部署環境和雲端中的服務和應用程式執行下列工作：

- 控管身分識別生命週期
- 控管存取生命週期
- 保護用於系統管理的特殊權限存取

具體來說，主要協助組織處理下列四個重要問題：

- 哪些使用者應該存取哪些資源？
- 使用者利用該存取權來做什麼？
- 是否有有效的組織控制項可管理存取權？
- 稽核人員可以確認這些控制項有用嗎？

## <a name="identity-lifecycle"></a>身分識別生命週期

Identity Governance 可協助組織取得以下兩者之間的平衡：*生產力* - 人員可以存取資源所需的速度，例如他們何時加入我的組織？ 與*安全性* - 其存取權應該如何隨著時間變更，例如由於該人員的雇用狀態變更？  身分識別生命週期管理是 Identity Governance 的基礎，而大規模有效控管必須使應用程式的身分識別生命週期管理基礎結構現代化。

![身分識別生命週期](./media/identity-governance-overview/identity-lifecycle.png)

對許多組織來說，員工的身分識別生命週期會繫結到該使用者在 HCM (人力資本管理) 系統中的代表項目。  Azure AD Premium 會在 Active Directory 與 Azure Active Directory 中自動維護人員在 Workday 和 SuccessFactors 中表示的使用者身分識別，如 [Azure Active Directory 使用者佈建計劃指南的雲端 HR 應用程式](../app-provisioning/plan-cloud-hr-provision.md)中所述。  Azure AD Premium 也包含 [Microsoft Identity Manager](/microsoft-identity-manager/)，其可從內部部署 HCM 系統 (例如 SAP HCM、Oracle eBusiness 和 PeopleSoft) 匯入記錄。

有愈來愈多的案例需要與組織外部人員共同作業。 [Azure AD B2B](/azure/active-directory/b2b/) 共同作業可讓您與來賓使用者和來自任何組織的外部夥伴安全地共用貴組織的應用程式與服務，同時持續控制貴公司的資料。  [Azure AD 權利管理](entitlement-management-overview.md)可讓您選取要允許哪些組織的使用者要求存取權，並將其新增為您組織目錄中的 B2B 來賓，以及確保這些來賓不再需要存取權時會遭到移除。

## <a name="access-lifecycle"></a>存取生命週期

組織需有一個程序，可管理在建立使用者的身分識別時起初為該使用者所佈建以外的存取權。  此外，企業組織必須能夠擴充有效率地縮放，才能夠持續開發並強制執行存取原則和控制項。

![存取生命週期](./media/identity-governance-overview/access-lifecycle.png)

一般而言，IT 會將存取核准決策委派給商務決策者。  此外，IT 可能是使用者本身。  例如，存取歐洲境內公司行銷應用程式中的機密客戶資料的使用者需要知道公司的原則。 來賓使用者可能不知道他們受邀的組織中資料的處理需求。

組織可以透過[動態群組](../users-groups-roles/groups-dynamic-membership.md)這類技術，並結合使用者佈建至 [SaaS 應用程式](../saas-apps/tutorial-list.md)或[與 SCIM 整合的應用程式](../app-provisioning/use-scim-to-provision-users-and-groups.md)來自動存取生命週期程序。  組織也可以控制哪些[來賓使用者能夠存取內部部署應用程式](../external-identities/hybrid-cloud-to-on-premises.md)。  接著可以利用週期性 [Azure AD 存取權檢閱](access-reviews-overview.md)，定期檢閱這些存取權限。   [Azure AD 權利管理](entitlement-management-overview.md)也可讓您定義使用者要如何跨一套群組和小組成員資格、應用程式角色與 SharePoint Online 角色來要求存取權。

當使用者嘗試存取應用程式時，Azure AD 會強制執行[條件式存取](../conditional-access/index.yml)原則。 例如，條件式存取原則可以包含先顯示[使用規定](../conditional-access/terms-of-use.md)並[確保使用者已同意這些規定](../conditional-access/require-tou.md)，才能夠存取應用程式。

## <a name="privileged-access-lifecycle"></a>特殊權限的存取生命週期

過去，其他廠商將特殊權限的存取描述為與 Identity Governance 不同的功能。 不過，在 Microsoft，我們認為控管特殊權限的存取是 Identity Governance 的重要部分 -- 尤其是可能對組織造成與這些系統管理員權限相關聯的誤用可能性。 具有系統管理權限的員工、廠商和約聘人員都需要加以控管。

![特殊權限的存取生命週期](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 提供量身訂做的其他控制項，以保護 Azure AD、Azure 和其他 Microsoft Online Services 中資源的存取權限。  除了多重要素驗證和條件式存取，Just-In-Time 存取以及 Azure AD PIM 所提供的角色變更警示功能都會提供一組完整的控管控制項，協助保護貴公司的資源 (目錄、Microsoft 365 和 Azure 資源角色)。 如同其他形式的存取權，組織可以使用存取權檢閱，對具有系統管理員角色的所有使用者設定週期性存取權重新確認。

## <a name="governance-capabilities-in-other-azure-ad-features"></a>其他 Azure AD 功能中的治理功能

除了以上所列的功能之外，經常用來提供身分識別治理案例的其他 Azure AD 功能包括：

| 功能 | 案例 |特徵
| ------- | --------------------- |-----|
|身分識別生命週期 (員工)|管理員可以啟用從 Workday 或 SuccessFactors 雲端 HR 或內部部署 HR 進行使用者帳戶佈建。|[Azure AD 使用者佈建的雲端 HR](../app-provisioning/plan-cloud-hr-provision.md)|
|身分識別生命週期 (來賓)|管理員可以從另一個 Azure AD 租用戶、直接同盟、單次密碼 (OTP) 或 Google 帳戶，啟用自助式來賓使用者上線。  系統會根據生命週期原則自動佈建和取消佈建來賓使用者。|使用 [B2B](../external-identities/what-is-b2b.md) 進行[權利管理](entitlement-management-overview.md)|
|權利管理|資源擁有者可以建立包含應用程式、Teams、Azure AD 和 Microsoft 365 群組的存取套件，以及 SharePoint Online 網站。|[權利管理](entitlement-management-overview.md)|
|存取要求|終端使用者可以要求群組成員資格或應用程式存取權。 終端使用者 (包括來自其他組織的來賓) 可以要求存取套件的存取權。|[權利管理](entitlement-management-overview.md)|
|工作流程|資源擁有者可以針對角色啟用要求的存取要求和核准者定義核准者和升級核准者。  |[權利管理](entitlement-management-overview.md)和 [PIM](../privileged-identity-management/pim-configure.md)|
|原則和角色管理|管理員可以針對應用程式的執行階段存取權定義條件式存取原則。  資源擁有者可以透過存取套件來定義使用者的存取原則。|[條件式存取](../conditional-access/overview.md)和[權利管理](entitlement-management-overview.md)原則|
|存取認證|管理員可以針對下列項目啟用重複存取重新認證：SaaS 應用程式或雲端群組成員資格、Azure AD 或 Azure 資源角色指派。 自動移除資源存取權、封鎖來賓存取權，以及刪除來賓帳戶。|[存取權檢閱](access-reviews-overview.md)也會顯示在 [PIM](../privileged-identity-management/pim-how-to-start-security-review.md) 中|
|履行和佈建|自動佈建和解除佈建至 Azure AD 連線的應用程式，包括透過 SCIM 以及至 SharePoint Online 網站。 |[使用者佈建](../app-provisioning/user-provisioning.md)|
|報告與分析|管理員可以擷取最近使用者佈建和登入活動的稽核記錄。 與 Azure 監視器和「誰可以透過存取套件進行存取」進行整合。|[Azure AD 報告](../reports-monitoring/overview-reports.md)和[監視](../reports-monitoring/overview-monitoring.md)|
|特殊權限存取|Azure AD 角色 (包括自訂角色) 及 Azure 資源角色的 Just-In-Time 及排程存取、警示、核准工作流程。|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|稽核|管理員可以在建立管理員帳戶時收到警示。|[Azure AD PIM 警示](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>開始使用

請在 Azure 入口網站中查看 **Identity Governance** 的 [入門] 索引標籤，以開始使用權利管理、存取權檢閱、Privileged Identity Management 和使用規定。

![Identity Governance 快速入門](./media/identity-governance-overview/getting-started.png)


如果您有任何關於 Identity Governance 功能的意見反應，請在 Azure 入口網站中按一下 [想提供意見反應嗎？] 以提交您的意見反應。 會有專門的小組定期檢閱您的意見反應。

雖然沒有適用於每位客戶的完美解決方案或建議，但下列設定指南也提供了 Microsoft 建議您遵循的基準原則，以確保員工能更加安全且更有生產力。

- [身分識別與裝置存取設定](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [保護特殊權限存取](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>附錄 - 要管理 Identity Governance 功能所需的最低特殊權限角色

您最好使用最低特殊權限角色來執行 Identity Governance 中的系統管理工作。 建議您視需要使用 Azure AD PIM 來啟動角色以執行這些工作。 以下是要設定 Identity Governance 功能所需的最低特殊權限目錄角色：

| 功能 | 最低特殊權限角色 |
| ------- | --------------------- |
| 權利管理 | 使用者管理員 (將 SharePoint Online 網站新增至目錄時除外，這需要全域管理員) |
| 存取權檢閱 | 使用者管理員 (Azure 或 Azure AD 角色的存取權檢閱除外，這需要特殊權限角色管理員) |
|Privileged Identity Management | 特殊權限角色管理員 |
| 使用規定 | 安全性系統管理員或條件式存取系統管理員 |

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure AD 權利管理？](entitlement-management-overview.md)
- [什麼是 Azure AD 存取權檢閱？](access-reviews-overview.md)
- [什麼是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)
- [使用規定對我有何幫助？](../conditional-access/terms-of-use.md)