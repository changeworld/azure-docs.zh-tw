---
title: PIM 中 Azure AD 角色的安全性警示-Azure AD |Microsoft Docs
description: 在 Azure Active Directory 中 Privileged Identity Management Azure AD 角色設定安全性警示。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/05/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 706770db4309d1a909bc1161ab9d6657b6c5310a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91533543"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>在 Privileged Identity Management 中設定 Azure AD 角色的安全性警示

當您的 Azure Active Directory (Azure AD) 組織中有可疑或不安全的活動時，Privileged Identity Management (PIM) 會產生警示。 觸發警示時，會顯示在 Privileged Identity Management 儀表板上。 選取警示，以查看詳列觸發警示之使用者或角色的報告。

## <a name="determine-your-version-of-pim"></a>判斷您的 PIM 版本

自2019年11月起，Privileged Identity Management 的 Azure AD 角色部分會更新為符合 Azure 資源角色體驗的新版本。 這會建立其他功能以及 [現有 API 的變更](azure-ad-roles-features.md#api-changes)。 當新版本推出時，您在本文中遵循的程式將取決於您目前擁有的 Privileged Identity Management 版本。 遵循本節中的步驟，判斷您擁有的 Privileged Identity Management 版本。 知道您的 Privileged Identity Management 版本之後，您可以在本文中選取符合該版本的程式。

1. 使用[特殊權限角色管理員](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)角色的使用者登入[Azure 入口網站](https://portal.azure.com/)。
1. 開啟 **Azure AD Privileged Identity Management**。 如果您在 [總覽] 頁面頂端有橫幅，請依照本文的 **新版本** 索引標籤中的指示進行。 否則，請遵循 [ **舊版** ] 索引標籤中的指示。

  [![選取 Azure AD > Privileged Identity Management。](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

遵循本文中的步驟來調查 Azure AD 角色的安全性警示。

# <a name="new-version"></a>[新版本](#tab/new)

![顯示 [警示] 頁面的螢幕擷取畫面，其中包含警示清單及其嚴重性。](./media/pim-how-to-configure-security-alerts/view-alerts.png)

## <a name="security-alerts"></a>安全性警示

此區段會列出 Azure AD 角色的所有安全性警示，以及如何修正及如何避免。 嚴重性具有下列意義：

- **高**：因為發生原則違規而需要立即採取行動。
- **中**：不需要立即採取行動，但表示可能發生原則違規。
- **低**：不需要立即採取行動，但建議進行原則變更。

### <a name="administrators-arent-using-their-privileged-roles"></a>系統管理員未使用其特殊權限角色

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 將特殊權限角色指派給不需要的使用者會增加受攻擊的機會。 攻擊者也比較容易忽視未受到積極使用的帳戶。 |
| **如何修正？** | 檢查清單中的使用者，並將其從不需要的特殊許可權角色中移除。 |
| **防範** | 將特殊許可權角色指派給具有業務理由的使用者。 </br>排程定期[存取權檢閱](pim-how-to-start-security-review.md)，以確認使用者仍然需要其存取權。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發程序** | 如果使用者在不啟用角色的情況下超過指定的天數，就會觸發。 |
| **天數** | 這項設定會指定使用者在不啟用角色的情況下，所能執行的最大天數（從0到100）。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色不需要多重要素驗證來進行啟用

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 如果沒有多重要素驗證，遭入侵的使用者就可以啟用特殊許可權角色。 |
| **如何修正？** | 檢查角色清單，並針對每個角色 [要求多重要素驗證](pim-how-to-change-default-settings.md) 。 |
| **防範** | 針對每一個角色[要求 MFA](pim-how-to-change-default-settings.md)。  |
| **入口網站內風險降低措施** | 進行特殊許可權角色的啟用所需的多重要素驗證。 |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>組織沒有 Azure AD Premium P2

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 目前的 Azure AD 組織沒有 Azure AD Premium P2。 |
| **如何修正？** | 檢閱 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的相關資訊。 升級至 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>特殊權限角色中的潛在過時帳戶

| | |
| --- | --- |
| **嚴重性** | 中 |
| **為什麼會收到此警示？** | 具有特殊許可權角色的帳戶在過去90天內未變更其密碼。 這些帳戶可能是未受到維護且容易遭到攻擊的服務或共用帳戶。 |
| **如何修正？** | 檢閱清單中的帳戶。 如果他們不再需要存取，請將它們從其特殊權限角色中移除。 |
| **防範** | 確定共用帳戶會在知道密碼的使用者有所變更時，輪替使用強式密碼。 </br>使用 [存取權審核](pim-how-to-start-security-review.md) 和移除不再需要的角色指派，定期審核具有特殊許可權角色的帳戶。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **最佳作法** | 使用密碼進行驗證並指派給高許可權系統管理角色（例如全域系統管理員或安全性系統管理員）的共用、服務和緊急存取帳戶，應該在下列情況下輪替其密碼：<ul><li>在涉及系統管理存取權限誤用或遭到入侵的安全性事件之後</li><li>在變更任意使用者的權限，使其不再為系統管理員之後 (例如，在先前為系統管理員的員工離開 IT 或離開組織之後)</li><li>定期 (例如每季或每年)，即使沒有任何已知的缺口或 IT 人員的變更也一樣</li></ul>由於多人有權存取這些帳戶的認證，因此，應該輪替認證，以確保已離開其角色的人員無法再存取帳戶。 [深入瞭解如何保護帳戶](../users-groups-roles/directory-admin-roles-secure.md) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>角色被指派于 Privileged Identity Management 之外

| | |
| --- | --- |
| **嚴重性** | 高 |
| **為什麼會收到此警示？** | 在 Privileged Identity Management 之外進行的特殊許可權角色指派未受到適當的監視，而且可能表示主動攻擊。 |
| **如何修正？** | 檢查清單中的使用者，並將其從 Privileged Identity Management 外部指派的特殊許可權角色中移除。 |
| **防範** | 調查使用者在 Privileged Identity Management 之外被指派特殊許可權角色的位置，並禁止未來的指派。 |
| **入口網站內風險降低措施** | 將使用者從其特殊許可權角色中移除。 |

### <a name="there-are-too-many-global-administrators"></a>全域管理員太多

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 全域管理員是許可權最高的角色。 如果全域管理員遭到入侵，攻擊者就會取得其擁有權限的存取權，讓您的整個系統都有風險。 |
| **如何修正？** | 檢查清單中的使用者，並移除任何不是完全需要全域管理員角色的使用者。 </br>請改為將較低許可權的角色指派給這些使用者。 |
| **防範** | 將使用者所需的最低特殊權限角色指派給使用者。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發程序** | 當符合兩個不同的條件時就會觸發，而您可以同時設定這兩個條件。 首先，您必須達到特定的全域管理員角色指派閾值。 其次，您的總角色指派百分比必須是全域管理員。 如果只符合這些測量結果其中之一，就不會出現警示。 |
| **全域管理員數目下限** | 這項設定會指定您認為對 Azure AD 組織而言太少的全域管理員角色指派數目，從2到100。 |
| **全域管理員百分比** | 這項設定會指定全域系統管理員的最小百分比，從0% 到100%，您不希望 Azure AD 的組織到 dip。 |

### <a name="roles-are-being-activated-too-frequently"></a>啟用角色的次數太頻繁

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 同一使用者多次啟用相同的特殊權限角色是受到攻擊的徵兆。 |
| **如何修正？** | 檢閱清單中的使用者，並確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓他們執行工作。 |
| **防範** | 請確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓使用者執行他們的工作。</br>針對具有多個系統管理員共用之帳戶的特殊許可權角色，[需要多重要素驗證](pim-how-to-change-default-settings.md)。 |
| **入口網站內風險降低措施** | N/A |
| **觸發程序** | 當使用者在指定期間內多次啟用相同的特殊權限角色時，就會觸發。 您可以同時設定時段和啟用次數。 |
| **啟用更新時間範圍** | 此設定會以天、小時、分鐘及秒為單位，指定您想要用來追蹤可疑更新的時段。 |
| **啟用更新次數** | 這項設定會指定在您選擇的時間範圍內，您想要收到通知的啟用次數（從2到100）。 您可以移動滑桿或在文字方塊中輸入數字，以變更此設定。 |

## <a name="customize-security-alert-settings"></a>自訂安全性警示設定

在 [ **警示** ] 頁面上，選取 [ **設定**]。

![已反白顯示設定的警示頁面](media/pim-how-to-configure-security-alerts/alert-settings.png)

自訂不同警示的設定，以便合您的環境和安全性目標。

![設定警示的頁面，以啟用和設定設定](media/pim-how-to-configure-security-alerts/security-alert-settings.png)

# <a name="previous-version"></a>[先前版本](#tab/previous)

![Azure AD 角色-警示窗格清單警示和嚴重性](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alert-details"></a>安全性警示詳細資料

此區段會列出 Azure AD 角色的所有安全性警示，以及如何修正及如何避免。 嚴重性具有下列意義：

- **高**：因為發生原則違規而需要立即採取行動。
- **中**：不需要立即採取行動，但表示可能發生原則違規。
- **低**：不需要立即採取行動，但建議進行原則變更。

### <a name="administrators-arent-using-their-privileged-roles"></a>系統管理員未使用其特殊權限角色

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 將特殊權限角色指派給不需要的使用者會增加受攻擊的機會。 攻擊者也比較容易忽視未受到積極使用的帳戶。 |
| **如何修正？** | 檢查清單中的使用者，並將其從不需要的特殊許可權角色中移除。 |
| **防範** | 將特殊許可權角色指派給具有業務理由的使用者。 </br>排程定期[存取權檢閱](pim-how-to-start-security-review.md)，以確認使用者仍然需要其存取權。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發程序** | 如果使用者在不啟用角色的情況下超過指定的天數，就會觸發。 |
| **天數** | 這項設定會指定使用者在不啟用角色的情況下，所能執行的最大天數（從0到100）。|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>角色不需要多重要素驗證來進行啟用

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 如果沒有多重要素驗證，遭入侵的使用者就可以啟用特殊許可權角色。 |
| **如何修正？** | 檢查角色清單，並針對每個角色 [要求多重要素驗證](pim-how-to-change-default-settings.md) 。 |
| **防範** | 針對每一個角色[要求 MFA](pim-how-to-change-default-settings.md)。  |
| **入口網站內風險降低措施** | 進行特殊許可權角色的啟用所需的多重要素驗證。 |

### <a name="the-organization-doesnt-have-azure-ad-premium-p2"></a>組織沒有 Azure AD Premium P2

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 目前的 Azure AD 組織沒有 Azure AD Premium P2。 |
| **如何修正？** | 檢閱 [Azure AD 版本](../fundamentals/active-directory-whatis.md)的相關資訊。 升級至 Azure AD Premium P2。 |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>特殊權限角色中的潛在過時帳戶

| | |
| --- | --- |
| **嚴重性** | 中 |
| **為什麼會收到此警示？** | 具有特殊許可權角色的帳戶在過去90天內未變更其密碼。 這些帳戶可能是未受到維護且容易遭到攻擊的服務或共用帳戶。 |
| **如何修正？** | 檢閱清單中的帳戶。 如果他們不再需要存取，請將它們從其特殊權限角色中移除。 |
| **防範** | 確定共用帳戶會在知道密碼的使用者有所變更時，輪替使用強式密碼。 </br>使用 [存取權審核](pim-how-to-start-security-review.md) 和移除不再需要的角色指派，定期審核具有特殊許可權角色的帳戶。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **最佳作法** | 使用密碼進行驗證並指派給高許可權系統管理角色（例如全域系統管理員或安全性系統管理員）的共用、服務和緊急存取帳戶，應該在下列情況下輪替其密碼：<ul><li>在涉及系統管理存取權限誤用或遭到入侵的安全性事件之後</li><li>在變更任意使用者的權限，使其不再為系統管理員之後 (例如，在先前為系統管理員的員工離開 IT 或離開組織之後)</li><li>定期 (例如每季或每年)，即使沒有任何已知的缺口或 IT 人員的變更也一樣</li></ul>由於多人有權存取這些帳戶的認證，因此，應該輪替認證，以確保已離開其角色的人員無法再存取帳戶。 [深入了解](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>角色被指派于 Privileged Identity Management 之外

| | |
| --- | --- |
| **嚴重性** | 高 |
| **為什麼會收到此警示？** | 在 Privileged Identity Management 之外進行的特殊許可權角色指派未受到適當的監視，而且可能表示主動攻擊。 |
| **如何修正？** | 檢查清單中的使用者，並將其從 Privileged Identity Management 外部指派的特殊許可權角色中移除。 |
| **防範** | 調查使用者在 Privileged Identity Management 之外被指派特殊許可權角色的位置，並禁止未來的指派。 |
| **入口網站內風險降低措施** | 將使用者從其特殊許可權角色中移除。 |

### <a name="there-are-too-many-global-administrators"></a>全域管理員太多

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 全域管理員是許可權最高的角色。 如果全域管理員遭到入侵，攻擊者就會取得其擁有權限的存取權，讓您的整個系統都有風險。 |
| **如何修正？** | 檢查清單中的使用者，並移除任何不是完全需要全域管理員角色的使用者。 </br>請改為將較低許可權的角色指派給這些使用者。 |
| **防範** | 將使用者所需的最低特殊權限角色指派給使用者。 |
| **入口網站內風險降低措施** | 從其特殊權限角色中移除帳戶。 |
| **觸發程序** | 當符合兩個不同的條件時就會觸發，而您可以同時設定這兩個條件。 首先，您必須達到特定的全域管理員臨界值。 其次，您的總角色指派百分比必須是全域管理員。 如果只符合這些測量結果其中之一，就不會出現警示。 |
| **全域管理員數目下限** | 這項設定會指定您認為 Azure AD 組織的全域管理員數目（從2到100）。 |
| **全域管理員百分比** | 這項設定會指定全域系統管理員的最小百分比，從0% 到100%，您不希望 Azure AD 的組織到 dip。 |

### <a name="roles-are-being-activated-too-frequently"></a>啟用角色的次數太頻繁

| | |
| --- | --- |
| **嚴重性** | 低 |
| **為什麼會收到此警示？** | 同一使用者多次啟用相同的特殊權限角色是受到攻擊的徵兆。 |
| **如何修正？** | 檢閱清單中的使用者，並確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓他們執行工作。 |
| **防範** | 請確定為他們的特殊權限角色設定的[啟用持續時間](pim-how-to-change-default-settings.md)夠長，足以讓使用者執行他們的工作。</br>針對具有多個系統管理員共用之帳戶的特殊許可權角色，[需要多重要素驗證](pim-how-to-change-default-settings.md)。 |
| **入口網站內風險降低措施** | N/A |
| **觸發程序** | 當使用者在指定期間內多次啟用相同的特殊權限角色時，就會觸發。 您可以同時設定時段和啟用次數。 |
| **啟用更新時間範圍** | 此設定會以天、小時、分鐘及秒為單位，指定您想要用來追蹤可疑更新的時段。 |
| **啟用更新次數** | 這項設定會指定在您選擇的時間範圍內，您想要收到通知的啟用次數（從2到100）。 您可以移動滑桿或在文字方塊中輸入數字，以變更此設定。 |

## <a name="configure-security-alert-settings"></a>設定安全性警示設定

您可以自訂 Privileged Identity Management 中的一些安全性警示，以處理組織的需求和安全性目標。 請遵循下列步驟開啟安全性警示設定：

1. 開啟 Azure AD 中的 **Privileged Identity Management** 。

1. 選取 **Azure AD 角色**]。

1. 選取 [ **設定** ]，然後選取 [ **警示**]。

    ![Azure AD 角色-已選取警示的設定](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. 選取警示名稱以設定該警示的設定。

    ![針對選取的警示，安全性警示設定窗格](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

---

## <a name="next-steps"></a>後續步驟

- [在 Privileged Identity Management 中設定 Azure AD 角色設定](pim-how-to-change-default-settings.md)
