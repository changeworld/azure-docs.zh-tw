---
title: 什麼是存取權檢閱？ - Azure Active Directory | Microsoft Docs
description: 使用 Azure Active Directory 訪問審核，可以控制組成員身份和應用程式訪問，以滿足組織中的治理、風險管理和合規性計畫。
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
ms.date: 01/10/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5499c8808c3916842071df1f03a865efd98719f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262083"
---
# <a name="what-are-azure-ad-access-reviews"></a>什麼是 Azure AD 存取權檢閱？

Azure 活動目錄 （Azure AD） 訪問評審使組織能夠高效地管理組成員身份、訪問企業應用程式和角色指派。 您可以定期檢閱使用者的存取權，以確定只有適當的人員會具有持續存取權。

以下影片會快速地概述存取權檢閱：

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>存取權檢閱為何重要？

Azure AD 可讓您在組織內部以及與外部組織 (例如，合作夥伴) 的使用者共同作業。 使用者可以加入群組、邀請來賓、連線至雲端應用程式，以及從他們的工作或個人裝置遠端工作。 由於能夠方便地運用自助式服務的能力，因此需要更好的存取管理功能。

- 當有新員工加入時，如何確保他們會有適當的存取權，以便發揮生產力？
- 當有員工調動到其他小組或離職時，如何確保他們的舊有存取權會移除，特別是當此存取權涉及來賓時？
- 多餘的存取權限可能會導致稽核後果與洩露，因為這表示公司對於存取權的控制不夠嚴謹。
- 您必須主動與資源擁有者合作，以確保他們會定期檢閱可存取其資源的使用者。

## <a name="when-to-use-access-reviews"></a>何時要使用存取權檢閱？

- **在特權角色中的使用者太多：** 最好檢查有多少使用者具有管理存取權限，其中有多少是全域管理員，以及是否有任何被邀請的客人或合作夥伴在分配執行管理工作後尚未刪除。 您可以重新認證[Azure AD 角色](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)（如全域管理員）或 Azure[資源角色](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)（如 Azure AD[特權標識管理 （PIM）](../privileged-identity-management/pim-configure.md)體驗中的使用者訪問管理員）中的角色指派使用者。
- **當自動化不可行時：** 您可以為安全性群組或 Office 365 組的動態成員資格創建規則，但如果 HR 資料不在 Azure AD 中，或者使用者在離開組後仍需要訪問以培訓其替換，該怎麼辦？ 這時，您就可以在該群組上建立檢閱，以確保仍需要存取權的人員應該會具有持續存取權。
- **當群組用於新的用途時：** 如果您有要同步處理至 Azure AD 的群組，或如果您計劃讓銷售小組群組中的每個人都能使用 Salesforce 應用程式，則要求群組擁有者先檢閱群組成員資格，再將群組用於不同風險內容中的做法會很有用。
- **業務關鍵型資料訪問：** 對於某些資源，可能需要要求 IT 以外的人員定期登出，並說明為什麼他們需要訪問以用於審核目的。
- **要維護策略的異常清單，可以：** 在理想情況下，所有使用者都將遵循訪問策略來保護對組織資源的訪問。 不過，有時候會有需要您視為例外狀況的商務案例。 身為 IT 系統管理員，您可以管理這項工作、免於監督原則例外狀況，並向稽核人員證明您有定期檢閱這些例外狀況。
- **要求群組擁有者確認他們組中仍需要客人：** 員工訪問可能自動與一些內部 IAM，但不是邀請的客人。 如果有群組賦予來賓存取商務機密內容的權限，該群組的擁有者就有責任確認其來賓仍有合理獲得存取權的商務需求。
- **反覆定期檢閱：** 您可以設定以一定的頻率 (例如，每週、每月、每季或每年) 週期性地檢閱使用者的存取權，每次檢閱開始時，檢閱者都會收到通知。 檢閱者可以透過容易使用的介面與智慧建議的協助，來核准或拒絕存取權。

## <a name="where-do-you-create-reviews"></a>在哪裡建立檢閱？

根據要查看的內容，您將在 Azure AD 訪問審核、Azure AD 企業應用（預覽版）或 Azure AD PIM 中創建訪問審核。

| 使用者的存取權限 | 檢閱者可以是 | 檢閱建立於 | 檢閱者體驗 |
| --- | --- | --- | --- |
| 安全性群組成員</br>Office 群組成員 | 指定的檢閱者</br>群組擁有者</br>自我審查 | Azure AD 存取權檢閱</br>Azure AD 群組 | 存取面板 |
| 指派給已連線的應用程式 | 指定的檢閱者</br>自我審查 | Azure AD 存取權檢閱</br>Azure AD 企業應用程式 (預覽版) | 存取面板 |
| Azure AD 角色 | 指定的檢閱者</br>自我審查 | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 入口網站 |
| Azure 資源角色 | 指定的檢閱者</br>自我審查 | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure 入口網站 |


## <a name="create-access-reviews"></a>創建訪問審核

要創建訪問審核，請按照以下步驟操作：

1. 轉到[Azure 門戶](https://portal.azure.com)以管理訪問審核，並作為全域管理員或使用者管理員登錄。

1. 搜尋並選取 [Azure Active Directory]****。

      ![Azure 門戶搜索 Azure 活動目錄](media/access-reviews-overview/search-azure-active-directory.png)

1. 選擇**標識治理**。

1. 在"入門"頁上，按一下"**創建訪問審閱"** 按鈕。

   ![訪問評論起始頁](./media/access-reviews-overview/access-reviews-overview-create-access-reviews.png) 


## <a name="learn-about-access-reviews"></a>瞭解訪問評論

若要深入了解如何建立和執行存取權檢閱，請觀看這個簡短示範：

>[!VIDEO https://www.youtube.com/embed/6KB3TZ8Wi40]

如果您已準備好在組織中部署存取權檢閱，請遵循影片中的這些步驟，以便上架、訓練系統管理員，以及建立您的第一個存取權檢閱！

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

## <a name="license-requirements"></a>授權需求

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>您必須擁有多少許可證？

確保目錄至少具有與員工執行以下任務相同的 Azure AD 高級 P2 許可證：

- 被指定為檢閱者的成員和來賓使用者
- 執行自我審閱的成員和來賓使用者
- 執行訪問審核的組擁有者
- 執行訪問審核的應用程式擁有者

以下任務**不需要**Azure AD 高級 P2 許可證：

- 具有設置訪問審核、配置設置或應用審核決策的全域管理員或使用者管理員角色的使用者不需要許可證。

對於分配給自己組織的使用者之一的每個付費 Azure AD 高級 P2 許可證，可以使用 Azure AD 企業對企業 （B2B） 在外部使用者限額下最多邀請五個來賓使用者。 這些來賓使用者也可以使用 Azure AD Premium P2 功能。 有關詳細資訊，請參閱[Azure AD B2B 協作許可指南](../b2b/licensing-guidance.md)。

有關許可證的詳細資訊，請參閱使用[Azure 活動目錄門戶分配或刪除許可證](../fundamentals/license-users-groups.md)。

### <a name="example-license-scenarios"></a>許可證方案示例

下面是一些示例許可證方案，可説明您確定必須擁有的許可數。

| 狀況 | 計算 | 許可證數 |
| --- | --- | --- |
| 管理員創建具有 75 個使用者和 1 個組擁有者的組 A 的訪問審核，並將組擁有者分配為檢閱者。 | 組擁有者作為檢閱者的 1 個許可證 | 1 |
| 管理員創建包含 500 個使用者和 3 個組擁有者的組 B 的訪問審核，並將 3 個組擁有者分配為檢閱者。 | 每個組擁有者作為檢閱者有 3 個許可證 | 3 |
| 管理員創建具有 500 個使用者的組 B 的訪問審核。 使它成為自我檢討。 | 每個使用者為自我檢閱者提供 500 個許可證 | 500 |
| 管理員創建具有 50 個成員使用者和 25 個來賓使用者的組 C 的訪問審核。 使它成為自我檢討。 | 每個使用者有 50 個許可證作為自我檢閱者。<br/>（訪客使用者以要求的 1：5 比率提供） | 50 |
| 管理員創建具有 6 個成員使用者和 108 個來賓使用者的組 D 的訪問審核。 使它成為自我檢討。 | 每個使用者有 6 個許可證作為自我檢閱者 – 16 個附加許可證，以所需的 1：5 比率覆蓋所有 108 個來賓使用者。 6 個許可證，涵蓋\*6 個 5×30 個來賓使用者。 對於剩餘的 （108-6\*5）=78 個來賓使用者，需要 78/5=16 額外的許可證。 因此，總共需要 6+16*22 個許可證。 | 22 |

## <a name="next-steps"></a>後續步驟

- [創建組或應用程式的訪問審核](create-access-review.md)
- [在 Azure AD 管理角色中建立使用者的存取權檢閱](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [查看對組或應用程式的存取權限](perform-access-review.md)
- [完成組或應用程式的訪問審核](complete-access-review.md)
