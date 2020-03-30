---
title: 標識治理 - Azure 活動目錄 |微軟文檔
description: Azure 活動目錄標識治理允許您平衡組織對安全性和員工工作效率的需求以及正確的流程和可見度。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/24/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd319dd6a83a392f6df26d07a58be22a9c8bdb61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063674"
---
# <a name="what-is-azure-ad-identity-governance"></a>什麼是 Azure AD Identity Governance？

Azure 活動目錄 （Azure AD） 標識治理允許您平衡組織對安全性和員工工作效率的需求以及正確的流程和可見度。 它為您提供了確保合適的人員能夠正確訪問正確的資源的能力。 這些相關的 Azure AD 和企業移動性 + 安全功能允許您通過保護、監視和審核對關鍵資產的訪問來降低訪問風險，同時確保員工和業務合作夥伴的工作效率。  

身份治理使組織能夠跨員工、業務合作夥伴和供應商以及跨本地和雲中的服務和應用程式執行以下任務：

- 控管身分識別生命週期
- 控管存取生命週期
- 保護用於管理的特權訪問

具體來說，主要協助組織處理下列四個重要問題：

- 哪些使用者應該存取哪些資源？
- 使用者利用該存取權來做什麼？
- 是否有有效的組織控制項可管理存取權？
- 稽核人員可以確認這些控制項有用嗎？

## <a name="identity-lifecycle"></a>身分識別生命週期

身份治理可説明組織在*工作效率*之間取得平衡 - 一個人訪問所需資源的速度有多快，例如當他們加入我的組織時？ 與*安全性* - 其存取權應該如何隨著時間變更，例如由於該人員的雇用狀態變更？  身份生命週期管理是身份治理的基礎，大規模有效治理需要使應用程式的標識生命週期管理基礎結構現代化。

![身分識別生命週期](./media/identity-governance-overview/identity-lifecycle.png)

對許多組織來說，員工的身分識別生命週期會繫結到該使用者在 HCM (人力資本管理) 系統中的代表項目。  Azure AD 高級版會自動維護活動目錄和 Azure 活動目錄中工作日中代表的人員的使用者身份，如[工作日入站預配教程](../saas-apps/workday-inbound-tutorial.md)中所述。  Azure AD Premium 也包含 [Microsoft Identity Manager](/microsoft-identity-manager/)，其可從內部部署 HCM 系統 (例如 SAP、Oracle eBusiness 和 PeopleSoft) 匯入記錄。

有愈來愈多的案例需要與組織外部人員共同作業。 [Azure AD B2B](/azure/active-directory/b2b/) 共同作業可讓您與來賓使用者和來自任何組織的外部夥伴安全地共用貴組織的應用程式與服務，同時持續控制貴公司的資料。  [Azure AD 授權管理](entitlement-management-overview.md)使您能夠選擇允許哪個組織的使用者請求訪問，並將其添加為 B2B 來賓到組織的目錄，並確保在不再需要存取權限時刪除這些來賓。

## <a name="access-lifecycle"></a>存取生命週期

組織需有一個程序，可管理在建立使用者的身分識別時起初為該使用者所佈建以外的存取權。  此外，企業組織必須能夠擴充有效率地縮放，才能夠持續開發並強制執行存取原則和控制項。

![存取生命週期](./media/identity-governance-overview/access-lifecycle.png)

一般而言，IT 會將存取核准決策委派給商務決策者。  此外，IT 可能是使用者本身。  例如，存取歐洲境內公司行銷應用程式中的機密客戶資料的使用者需要知道公司的原則。 來賓使用者可能不知道他們受邀的組織中資料的處理需求。

組織可以透過[動態群組](../users-groups-roles/groups-dynamic-membership.md)這類技術，並結合使用者佈建至 [SaaS 應用程式](../saas-apps/tutorial-list.md)或[與 SCIM 整合的應用程式](../app-provisioning/use-scim-to-provision-users-and-groups.md)來自動存取生命週期程序。  組織也可以控制哪些[來賓使用者能夠存取內部部署應用程式](../b2b/hybrid-cloud-to-on-premises.md)。  接著可以利用週期性 [Azure AD 存取權檢閱](access-reviews-overview.md)，定期檢閱這些存取權限。   [Azure AD 授權管理](entitlement-management-overview.md)還使您能夠定義使用者如何跨組和團隊成員身份、應用程式角色和 SharePoint Online 角色的包請求訪問。

當使用者嘗試訪問應用程式時，Azure AD 將強制實施[條件訪問](/azure/active-directory/conditional-access/)策略。 例如，條件訪問策略可以包括顯示[使用條款](../conditional-access/terms-of-use.md)，並確保使用者在能夠訪問應用程式之前[同意這些條款](../conditional-access/require-tou.md)。

## <a name="privileged-access-lifecycle"></a>特殊權限的存取生命週期

從歷史上看，特權訪問被其他供應商描述為獨立于標識治理的功能。 但是，在 Microsoft 中，我們認為管理特權訪問是身份治理的關鍵區段，特別是考慮到與這些管理員許可權關聯的濫用可能導致組織。 具有系統管理權限的員工、廠商和約聘人員都需要加以控管。

![特殊權限的存取生命週期](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD 特權標識管理 （PIM）](../privileged-identity-management/pim-configure.md)提供了其他控制項，用於保護 Azure AD、Azure 和其他 Microsoft 線上服務中資源的存取權限。  Azure AD PIM 提供的及時訪問和角色更改警報功能，以及多重要素驗證和條件訪問，還提供一套全面的治理控制項，以説明保護公司的資源（目錄，Office 365 和 Azure 資源角色）。 如同其他形式的存取權，組織可以使用存取權檢閱，對具有系統管理員角色的所有使用者設定週期性存取權重新確認。

## <a name="getting-started"></a>開始使用

查看 Azure 門戶中的 **"開始使用標識治理**"選項卡，開始使用授權管理、訪問審核、特權標識管理和使用條款。

![身份治理開始](./media/identity-governance-overview/getting-started.png)


如果您對標識治理功能有任何回饋，請在 Azure 門戶中按一下 **"獲得回饋"** 以提交回饋。 團隊定期審核您的回饋。

雖然沒有針對每個客戶的完美解決方案或建議，但以下配置指南還提供 Microsoft 建議您遵循的基準策略，以確保員工隊伍更安全、更高效。

- [身分識別與裝置存取設定](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [保護特殊權限存取](../users-groups-roles/directory-admin-roles-secure.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>附錄 - 身份治理功能中管理的特權最低角色

最佳做法是使用特權最低的角色在身份治理中執行管理工作。 我們建議您使用 Azure AD PIM 根據需要啟動角色以執行這些任務。 以下是配置標識治理功能的最小特權目錄角色：

| 功能 | 最低特殊權限角色 |
| ------- | --------------------- |
| 權利管理 | 使用者管理員（將 SharePoint Online 網站添加到目錄（這需要全域管理員） |
| 存取權檢閱 | 使用者管理員（Azure 或 Azure AD 角色的訪問審查除外，這需要特權角色管理員） |
|Privileged Identity Management | 特殊權限角色管理員 |
| 使用規定 | 安全管理員或條件訪問管理員 |

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure AD 權利管理？](entitlement-management-overview.md)
- [什麼是 Azure AD 訪問審核？](access-reviews-overview.md)
- [什麼是 Azure AD Privileged Identity Management？](../privileged-identity-management/pim-configure.md)
- [我可以用使用規定來做什麼？](active-directory-tou.md)


