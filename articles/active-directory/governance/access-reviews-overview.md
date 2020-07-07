---
title: 什麼是存取權檢閱？ - Azure Active Directory | Microsoft Docs
description: 您可以使用 Azure Active Directory 存取權檢閱，來控制群組成員資格和應用程式存取權，以符合貴組織的控管、風險管理和合規性計畫。
services: active-directory
documentationcenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/17/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22a7cafbf8223c3d9e7641851d02f61bc2ad16ae
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/19/2020
ms.locfileid: "85078897"
---
# <a name="what-are-azure-ad-access-reviews"></a>什麼是 Azure AD 存取權檢閱？

Azure Active Directory (Azure AD) 存取權檢閱可讓組織有效地管理群組成員資格、對企業應用程式的存取，以及角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的人員會具有持續存取權。

以下影片會快速地概述存取權檢閱：

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>存取權檢閱為何重要？

Azure AD 可讓您在組織內部以及與外部組織 (例如，合作夥伴) 的使用者共同作業。 使用者可以加入群組、邀請來賓、連線至雲端應用程式，以及從他們的工作或個人裝置遠端工作。 由於能夠方便地運用自助式服務的能力，因此需要更好的存取管理功能。

- 當有新員工加入時，如何確保他們會有適當的存取權，以便發揮生產力？
- 當有員工調動到其他小組或離職時，如何確保他們的舊有存取權會移除，特別是當此存取權涉及來賓時？
- 多餘的存取權限可能會導致稽核後果與洩露，因為這表示公司對於存取權的控制不夠嚴謹。
- 您必須主動與資源擁有者合作，以確保他們會定期檢閱可存取其資源的使用者。

## <a name="when-to-use-access-reviews"></a>何時要使用存取權檢閱？

- **太多使用者具有特殊權限角色：** 您最好檢查有多少使用者擁有管理存取權、其中有多少人是全域管理員，以及是否有任何受邀來賓或合作夥伴未在受指派執行管理工作之後移除。 您可以針對 [Azure AD 角色](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) (例如，全域管理員) 或 [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) 體驗中的 [Azure 資源角色](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) (例如，使用者存取管理員)，重新認證其中的角色指派使用者。
- **無法自動化時：** 您可以在安全性群組或 Office 365 群組上建立動態成員資格的規則，但如果 HR 資料未放在 Azure AD 中，或如果使用者在離開群組之後依然需要存取權以便訓練其接替者呢？ 這時，您就可以在該群組上建立檢閱，以確保仍需要存取權的人員應該會具有持續存取權。
- **當群組用於新的用途時：** 如果您有要同步處理至 Azure AD 的群組，或如果您計劃讓銷售小組群組中的每個人都能使用 Salesforce 應用程式，則要求群組擁有者先檢閱群組成員資格，再將群組用於不同風險內容中的做法會很有用。
- **資料存取權：** 對於某些資源來說，您可能必須要求 IT 以外的人定期登出，並請他們提出需要存取權的理由，以便進行稽核。
- **為了維護原則的例外狀況清單：** 在理想的世界中，所有使用者都會遵循存取原則來安全地存取您組織的資源。 不過，有時候會有需要您視為例外狀況的商務案例。 身為 IT 系統管理員，您可以管理這項工作、免於監督原則例外狀況，並向稽核人員證明您有定期檢閱這些例外狀況。
- **要求群組擁有者確認他們的群組中仍然需要來賓：** 員工的存取權可透過某些內部部署 IAM 來自動指派，但受邀的來賓則不行。 如果有群組賦予來賓存取商務機密內容的權限，該群組的擁有者就有責任確認其來賓仍有合理獲得存取權的商務需求。
- **反覆定期檢閱：** 您可以設定以一定的頻率 (例如，每週、每月、每季或每年) 週期性地檢閱使用者的存取權，每次檢閱開始時，檢閱者都會收到通知。 檢閱者可以透過容易使用的介面與智慧建議的協助，來核准或拒絕存取權。

## <a name="where-do-you-create-reviews"></a>在哪裡建立檢閱？

根據所要檢閱的內容，可將存取權檢閱建立在 Azure AD 存取權檢閱、Azure AD 企業應用程式 (預覽版) 或 Azure AD PIM 中。

| 使用者的存取權限 | 檢閱者可以是 | 檢閱建立於 | 檢閱者體驗 |
| --- | --- | --- | --- |
| 安全性群組成員</br>Office 群組成員 | 指定的檢閱者</br>群組擁有者</br>自我檢閱 | Azure AD 存取權檢閱</br>Azure AD 群組 | 存取面板 |
| 指派給已連線的應用程式 | 指定的檢閱者</br>自我檢閱 | Azure AD 存取權檢閱</br>Azure AD 企業應用程式 (預覽版) | 存取面板 |
| Azure AD 角色 | 指定的檢閱者</br>自我檢閱 | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 入口網站 |
| Azure 資源角色 | 指定的檢閱者</br>自我檢閱 | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 入口網站 |


## <a name="create-access-reviews"></a>建立存取權檢閱

若要建立存取權檢閱，請遵循下列步驟：

1. 移至 [Azure 入口網站](https://portal.azure.com)來管理存取權檢閱，並以全域管理員或使用者管理員身分登入 Azure 入口網站。

1. 搜尋並選取 [Azure Active Directory]。

      ![Azure 入口網站搜尋 Azure Active Directory](media/access-reviews-overview/search-azure-active-directory.png)

1. 選取 **Identity Governance**。

1. 在 [開始使用] 頁面上，按一下 [建立存取權檢閱] 按鈕。

   ![存取權檢閱啟動頁面](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 

### <a name="creating-access-review-on-a-group-that-can-be-assigned-to-azure-ad-role"></a>在可指派給 Azure AD 角色的群組上建立存取權檢閱
如果您是在最新版本的存取權檢閱上 (您的檢閱者預設會被導向至 [我的存取權])，則只有全域管理員才能在可指派角色的群組上建立存取權檢閱。 不過，如果您是在舊版的存取權檢閱上 (您的檢閱者預設會被導向至 [存取面板])，則全域管理員和使用者管理員都可以在可指派角色的群組上建立存取權檢閱。  

新體驗將於 2020 年 8 月 1 日推出給所有客戶，但如果您想要更快升級，請在這裡提出要求 - [Azure AD 存取權檢閱 - 已在我的存取權註冊中更新了檢閱者體驗](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

[深入了解如何將群組指派給 Azure AD 角色](https://go.microsoft.com/fwlink/?linkid=2103037)。

## <a name="learn-about-access-reviews"></a>深入了解存取權檢閱

若要深入了解如何建立和執行存取權檢閱，請觀看這個簡短示範：

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

如果您已準備好在組織中部署存取權檢閱，請遵循影片中的這些步驟，以便上架、訓練系統管理員，以及建立您的第一個存取權檢閱！

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>您必須擁有多少個授權？

請確定您目錄擁有的 Azure AD Premium P2 授權至少與將執行下列工作的員工一樣多：

- 指派為檢閱者的成員和來賓使用者
- 執行自我檢閱的成員和來賓使用者
- 執行存取權檢閱的群組擁有者
- 執行存取權檢閱的應用程式擁有者

下列工作**不**需要 Azure AD Premium P2 授權：

- 具有全域管理員或使用者管理員角色的使用者不需要任何授權，即可設定存取權檢閱、設定組態，或套用來自檢閱的決策。

至於您對於貴組織使用者指派的每個付費 Azure AD Premium P2 授權，在「外部使用者額度」下，您可以使用 Azure AD 企業對企業 (B2B) 邀請最多 5 位使用者。 這些來賓使用者也可以使用 Azure AD Premium P2 功能。 如需詳細資訊，請參閱 [Azure AD B2B 共同作業授權指引](../b2b/licensing-guidance.md)。

如需授權的詳細資訊，請參閱[使用 Azure Active Directory 入口網站指派或移除授權](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>範例授權案例

以下是一些範例授權案例，可協助您判斷您必須擁有的授權數目。

| 狀況 | 計算 | 授權數目 |
| --- | --- | --- |
| 管理員會建立群組 A (其中包含 75 個使用者和 1 個群組擁有者) 的存取權檢閱，並將群組擁有者指派為檢閱者。 | 做為檢閱者的群組擁有者 1 個授權 | 1 |
| 管理員會建立群組 B (其中包含 500 個使用者和 3 個群組擁有者) 的存取權檢閱，並將 3 個群組擁有者指派為檢閱者。 | 每個做為檢閱者的群組擁有者 3 個授權 | 3 |
| 管理員會建立群組 B (其中包含 500 個使用者) 的存取權檢閱。 讓其成為自我檢閱。 | 每個做為自我檢閱者的使用者 500 個授權 | 500 |
| 管理員會建立群組 C (其中包含 50 個成員使用者和 25 個來賓使用者) 的存取權檢閱。 讓其成為自我檢閱。 | 每個做為自我檢閱者的使用者 50 個授權。<br/>(以所需的1:5 比例涵蓋來賓使用者) | 50 |
| 管理員會建立群組 D (其中包含 6 個成員使用者和 108 個來賓使用者) 的存取權檢閱。 讓其成為自我檢閱。 | 每個做為自我檢閱者的使用者 6 個授權 + 16 個額外授權，以所需的 1:5 比例涵蓋所有 108 個來賓使用者。 6 個授權，其中涵蓋 6\*5=30 個來賓使用者。 對於剩餘的 (108-6\*5)=78 個來賓使用者，需要 78/5=16 個額外授權。 因此，總共需要 6+16=22 個授權。 | 22 |

## <a name="next-steps"></a>後續步驟

- [建立群組或應用程式的存取權檢閱](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [檢閱群組或應用程式的存取權](perform-access-review.md)
- [完成群組或應用程式的存取權檢閱](complete-access-review.md)
