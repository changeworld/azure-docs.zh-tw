---
title: PIM 中 Azure AD 角色的安全警報 - Azure AD |微軟文檔
description: 為 Azure AD 角色在 Azure 活動目錄中配置特權標識管理的安全警報。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86dbcdc24c90ba8b161b041af96cbdd0665ad827
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253308"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>在特權標識管理中為 Azure AD 角色配置安全警報

特權標識管理 （PIM） 在 Azure 活動目錄 （Azure AD） 組織中存在可疑或不安全活動時生成警報。 觸發警報時，警報顯示在特權標識管理儀表板上。 選取警示，以查看詳列觸發警示之使用者或角色的報告。

## <a name="determine-your-version-of-pim"></a>確定 PIM 的版本

從 2019 年 11 月開始，特權標識管理的 Azure AD 角色部分正在更新為與 Azure 資源角色體驗相匹配的新版本。 這將創建其他功能以及對現有[API 的更改](azure-ad-roles-features.md#api-changes)。 在推出新版本時，本文中遵循的哪些過程取決於您當前擁有的特權標識管理版本。 按照本節中的步驟確定您擁有的特權標識管理版本。 瞭解特權標識管理版本後，可以選擇本文中與該版本匹配的過程。

1. 使用處于[特權角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者登錄到[Azure 門戶](https://portal.azure.com/)。
1. 打開**Azure AD 特權標識管理**。 如果概述頁面頂部有橫幅，請按照本文**的"新版本**"選項卡中的說明進行操作。 否則，請按照 **"以前版本**"選項卡中的說明操作。

  [![](media/pim-how-to-add-role-to-user/pim-new-version.png "Select Azure AD > Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

按照本文中的步驟調查 Azure AD 角色的安全警報。

# <a name="new-version"></a>[新版本](#tab/new)

![Azure AD 角色 - 警報窗格列出警報和嚴重性](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>安全性警示

本節列出了 Azure AD 角色的所有安全警報，以及如何修復以及如何防止。 嚴重性具有下列意義：

- **高**：因為發生原則違規而需要立即採取行動。
- **中**：不需要立即採取行動，但表示可能發生原則違規。
- **低**：不需要立即採取行動，但建議進行原則變更。

### <a name="administrators-arent-using-their-privileged-roles"></a>系統管理員未使用其特殊權限角色

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 將特殊權限角色指派給不需要的使用者會增加受攻擊的機會。 攻擊者也比較容易忽視未受到積極使用的帳戶。 |
| **如何修復？** | 查看清單中的使用者，並從他們不需要的特權角色中刪除他們。 |
| **預防** | 僅為具有業務理由的使用者分配特權角色。 </br>排程定期[存取權檢閱](pim-how-to-start-security-review.md)，以確認使用者仍然需要其存取權。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發** | 如果使用者在未啟動角色的情況下超過指定天數，則觸發。 |
| **天數** | 此設置指定使用者可以在不啟動角色的情況下可以轉到的最大天數（從 0 到 100 天）。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色不需要多重要素驗證來進行啟用

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 如果沒有多重要素驗證，受攻擊的使用者可以啟動特權角色。 |
| **如何修復？** | 查看角色清單，並要求每個角色[進行多重要素驗證](pim-how-to-change-default-settings.md)。 |
| **預防** | 針對每一個角色[要求 MFA](pim-how-to-change-default-settings.md)。  |
| **入口網站內風險降低措施** | 為啟動特權角色需要多重要素驗證。 |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>組織沒有 Azure AD 高級 P2

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 當前 Azure AD 組織沒有 Azure AD 高級 P2。 |
| **如何修復？** | 檢閱 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的相關資訊。 升級至 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>特殊權限角色中的潛在過時帳戶

| | |
| --- | --- |
| **嚴重性** | 中 |
| **為什麼會收到此警示？** | 在過去 90 天內，處於特權角色的帳戶未更改其密碼。 這些帳戶可能是未受到維護且容易遭到攻擊的服務或共用帳戶。 |
| **如何修復？** | 檢閱清單中的帳戶。 如果他們不再需要存取，請將它們從其特殊權限角色中移除。 |
| **預防** | 確定共用帳戶會在知道密碼的使用者有所變更時，輪替使用強式密碼。 </br>使用[訪問審核](pim-how-to-start-security-review.md)定期審核具有特權角色的帳戶，並刪除不再需要的角色指派。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **最佳作法** | 使用密碼進行身份驗證並分配給高度特權管理角色的共用、服務和緊急存取帳戶（如 Global 管理員或安全管理員）應針對以下情況輪換其密碼：<ul><li>在涉及系統管理存取權限誤用或遭到入侵的安全性事件之後</li><li>在變更任意使用者的權限，使其不再為系統管理員之後 (例如，在先前為系統管理員的員工離開 IT 或離開組織之後)</li><li>定期 (例如每季或每年)，即使沒有任何已知的缺口或 IT 人員的變更也一樣</li></ul>由於多人有權存取這些帳戶的認證，因此，應該輪替認證，以確保已離開其角色的人員無法再存取帳戶。 [瞭解有關保護帳戶的更多詳細資訊](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>角色指派在特權身份管理之外

| | |
| --- | --- |
| **嚴重性** | 高 |
| **為什麼會收到此警示？** | 在特權身份管理之外進行的特權角色指派未受到正確監視，可能表示存在主動攻擊。 |
| **如何修復？** | 查看清單中的使用者，並從特權標識管理之外分配的特權角色中刪除他們。 |
| **預防** | 調查在特權標識管理之外向使用者分配特權角色的位置，並禁止將來分配。 |
| **入口網站內風險降低措施** | 將使用者從其特權角色中刪除。 |

### <a name="there-are-too-many-global-administrators"></a>全域管理員太多

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 全域管理員是最高特權角色。 如果全域管理員受到威脅，攻擊者將訪問其擁有權限，這會使整個系統面臨風險。 |
| **如何修復？** | 查看清單中的使用者並刪除任何不需要全域管理員角色的使用者。 </br>而是為這些使用者分配較低的特權角色。 |
| **預防** | 將使用者所需的最低特殊權限角色指派給使用者。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發** | 當符合兩個不同的條件時就會觸發，而您可以同時設定這兩個條件。 首先，您需要達到某個全域管理員的閾值。 其次，您總角色指派中一定百分比必須是全域管理員。 如果只符合這些測量結果其中之一，就不會出現警示。 |
| **全域管理員數目下限** | 此設置指定全域管理員的數量（從 2 到 100），對於 Azure AD 組織來說，這些管理員的數量太少。 |
| **全域管理員百分比** | 此設置指定全域管理員的最低百分比，從 0% 到 100%，低於此百分比，您不希望 Azure AD 組織下降。 |

### <a name="roles-are-being-activated-too-frequently"></a>啟用角色的次數太頻繁

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 同一使用者多次啟用相同的特殊權限角色是受到攻擊的徵兆。 |
| **如何修復？** | 檢閱清單中的使用者，並確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓他們執行工作。 |
| **預防** | 請確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓使用者執行他們的工作。</br>對於具有多個管理員共用帳戶的特權角色，[需要多重要素驗證](pim-how-to-change-default-settings.md)。 |
| **入口網站內風險降低措施** | N/A |
| **觸發** | 當使用者在指定期間內多次啟用相同的特殊權限角色時，就會觸發。 您可以同時設定時段和啟用次數。 |
| **啟用更新時間範圍** | 此設定會以天、小時、分鐘及秒為單位，指定您想要用來追蹤可疑更新的時段。 |
| **啟用更新次數** | 此設置指定在您選擇的時間範圍內，從 2 到 100 的啟動次數，您希望在其中通知您。 您可以移動滑桿或在文字方塊中輸入數字，以變更此設定。 |

## <a name="configure-security-alert-settings"></a>設定安全性警示設定

從 [警示] 頁面中移至 [設定]****。

![突出顯示"設置"的警報頁面](media/pim-how-to-configure-security-alerts/alert-settings.png)

自訂不同警示的設定，以便合您的環境和安全性目標。

![設置警報頁面以啟用和配置設置](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

# <a name="previous-version"></a>[早期版本](#tab/previous)

![Azure AD 角色 - 警報窗格列出警報和嚴重性](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>安全性警示

本節列出了 Azure AD 角色的所有安全警報，以及如何修復以及如何防止。 嚴重性具有下列意義：

- **高**：因為發生原則違規而需要立即採取行動。
- **中**：不需要立即採取行動，但表示可能發生原則違規。
- **低**：不需要立即採取行動，但建議進行原則變更。

### <a name="administrators-arent-using-their-privileged-roles"></a>系統管理員未使用其特殊權限角色

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 將特殊權限角色指派給不需要的使用者會增加受攻擊的機會。 攻擊者也比較容易忽視未受到積極使用的帳戶。 |
| **如何修復？** | 查看清單中的使用者，並從他們不需要的特權角色中刪除他們。 |
| **預防** | 僅為具有業務理由的使用者分配特權角色。 </br>排程定期[存取權檢閱](pim-how-to-start-security-review.md)，以確認使用者仍然需要其存取權。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發** | 如果使用者在未啟動角色的情況下超過指定天數，則觸發。 |
| **天數** | 此設置指定使用者可以在不啟動角色的情況下可以轉到的最大天數（從 0 到 100 天）。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色不需要多重要素驗證來進行啟用

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 如果沒有多重要素驗證，受攻擊的使用者可以啟動特權角色。 |
| **如何修復？** | 查看角色清單，並要求每個角色[進行多重要素驗證](pim-how-to-change-default-settings.md)。 |
| **預防** | 針對每一個角色[要求 MFA](pim-how-to-change-default-settings.md)。  |
| **入口網站內風險降低措施** | 為啟動特權角色需要多重要素驗證。 |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>組織沒有 Azure AD 高級 P2

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 當前 Azure AD 組織沒有 Azure AD 高級 P2。 |
| **如何修復？** | 檢閱 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的相關資訊。 升級至 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>特殊權限角色中的潛在過時帳戶

| | |
| --- | --- |
| **嚴重性** | 中 |
| **為什麼會收到此警示？** | 在過去 90 天內，處於特權角色的帳戶未更改其密碼。 這些帳戶可能是未受到維護且容易遭到攻擊的服務或共用帳戶。 |
| **如何修復？** | 檢閱清單中的帳戶。 如果他們不再需要存取，請將它們從其特殊權限角色中移除。 |
| **預防** | 確定共用帳戶會在知道密碼的使用者有所變更時，輪替使用強式密碼。 </br>使用[訪問審核](pim-how-to-start-security-review.md)定期審核具有特權角色的帳戶，並刪除不再需要的角色指派。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **最佳作法** | 使用密碼進行身份驗證並分配給高度特權管理角色的共用、服務和緊急存取帳戶（如 Global 管理員或安全管理員）應針對以下情況輪換其密碼：<ul><li>在涉及系統管理存取權限誤用或遭到入侵的安全性事件之後</li><li>在變更任意使用者的權限，使其不再為系統管理員之後 (例如，在先前為系統管理員的員工離開 IT 或離開組織之後)</li><li>定期 (例如每季或每年)，即使沒有任何已知的缺口或 IT 人員的變更也一樣</li></ul>由於多人有權存取這些帳戶的認證，因此，應該輪替認證，以確保已離開其角色的人員無法再存取帳戶。 [深入了解](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>角色指派在特權身份管理之外

| | |
| --- | --- |
| **嚴重性** | 高 |
| **為什麼會收到此警示？** | 在特權身份管理之外進行的特權角色指派未受到正確監視，可能表示存在主動攻擊。 |
| **如何修復？** | 查看清單中的使用者，並從特權標識管理之外分配的特權角色中刪除他們。 |
| **預防** | 調查在特權標識管理之外向使用者分配特權角色的位置，並禁止將來分配。 |
| **入口網站內風險降低措施** | 將使用者從其特權角色中刪除。 |

### <a name="there-are-too-many-global-administrators"></a>全域管理員太多

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 全域管理員是最高特權角色。 如果全域管理員受到威脅，攻擊者將訪問其擁有權限，這會使整個系統面臨風險。 |
| **如何修復？** | 查看清單中的使用者並刪除任何不需要全域管理員角色的使用者。 </br>而是為這些使用者分配較低的特權角色。 |
| **預防** | 將使用者所需的最低特殊權限角色指派給使用者。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發** | 當符合兩個不同的條件時就會觸發，而您可以同時設定這兩個條件。 首先，您需要達到某個全域管理員的閾值。 其次，您總角色指派中一定百分比必須是全域管理員。 如果只符合這些測量結果其中之一，就不會出現警示。 |
| **全域管理員數目下限** | 此設置指定全域管理員的數量（從 2 到 100），對於 Azure AD 組織來說，這些管理員的數量太少。 |
| **全域管理員百分比** | 此設置指定全域管理員的最低百分比，從 0% 到 100%，低於此百分比，您不希望 Azure AD 組織下降。 |

### <a name="roles-are-being-activated-too-frequently"></a>啟用角色的次數太頻繁

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 同一使用者多次啟用相同的特殊權限角色是受到攻擊的徵兆。 |
| **如何修復？** | 檢閱清單中的使用者，並確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓他們執行工作。 |
| **預防** | 請確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓使用者執行他們的工作。</br>對於具有多個管理員共用帳戶的特權角色，[需要多重要素驗證](pim-how-to-change-default-settings.md)。 |
| **入口網站內風險降低措施** | N/A |
| **觸發** | 當使用者在指定期間內多次啟用相同的特殊權限角色時，就會觸發。 您可以同時設定時段和啟用次數。 |
| **啟用更新時間範圍** | 此設定會以天、小時、分鐘及秒為單位，指定您想要用來追蹤可疑更新的時段。 |
| **啟用更新次數** | 此設置指定在您選擇的時間範圍內，從 2 到 100 的啟動次數，您希望在其中通知您。 您可以移動滑桿或在文字方塊中輸入數字，以變更此設定。 |

## <a name="configure-security-alert-settings"></a>設定安全性警示設定

您可以自訂特權標識管理中的一些安全警報，以便處理組織的需求和安全目標。 請遵循下列步驟開啟安全性警示設定：

1. 在 Azure AD 中打開**特權標識管理**。

1. 選擇**Azure AD 角色**。

1. 選擇 **"設置"，** 然後**選擇警報**。

    ![Azure AD 角色 - 選擇警報的設置](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. 選擇警示名稱以配置該警報的設置。

    ![對於選定的警報，安全警報設置窗格](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>後續步驟

- [在特權標識管理中配置 Azure AD 角色設置](pim-how-to-change-default-settings.md)
