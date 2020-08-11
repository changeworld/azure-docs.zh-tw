---
title: 使用智慧鎖定預防攻擊-Azure Active Directory
description: 瞭解 Azure Active Directory 智慧鎖定如何協助保護您的組織免于嘗試猜測使用者密碼的暴力密碼破解攻擊。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5734cb76e4ed018778c6858597ec8efe3019bf9
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88065976"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>使用 Azure Active Directory 智慧鎖定保護使用者帳戶免于遭受攻擊

智慧鎖定可協助鎖定嘗試猜測使用者密碼或使用暴力方法來取得的不良動作專案。 智慧鎖定可以辨識來自有效使用者的登入，並將其視為不同于攻擊者和其他不明來源。 攻擊者會遭到鎖定，而您的使用者仍可繼續存取其帳戶並提高生產力。

## <a name="how-smart-lockout-works"></a>智慧鎖定的運作方式

根據預設，智慧鎖定會在 10 次失敗嘗試後鎖定帳戶，使其無法嘗試登入一分鐘。 在每次後續登入嘗試失敗之後，帳戶會再次鎖定，先鎖定一分鐘，後續嘗試鎖定時間會更長。 為了將攻擊者解決此行為的方式降到最低，我們不會洩漏鎖定期間超過額外登入嘗試失敗的速率。

智慧鎖定會追蹤最後三個不正確的密碼雜湊，以避免因為相同密碼而累計鎖定計數器。 如果有人多次輸入相同的錯誤密碼，此行為就不會造成帳戶鎖定。

> [!NOTE]
> 雜湊追蹤功能不適用於已啟用傳遞驗證的客戶，因為驗證是在內部部署環境中進行，而不是在雲端中發生。

使用 AD FS 2016 和 AF FS 2019 的同盟部署，可以使用[AD FS 外部網路鎖定和外部網路智慧鎖定](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)來啟用類似的優點。

所有 Azure AD 的客戶都會使用智慧型鎖定，這些預設設定可提供正確的安全性和使用性組合。 您的使用者需要 Azure AD Premium P1 或更高的授權，才能自訂智慧鎖定設定（具有貴組織特定的值）。

使用智慧鎖定並不保證真正的使用者永遠不會遭到鎖定。當智慧鎖定鎖定使用者帳戶時，我們會嘗試我們的最佳做法，不要鎖定真正的使用者。 鎖定服務會嘗試確保不良的執行者無法取得正版使用者帳戶的存取權。 您必須考量下列事項：

* 每個 Azure AD 資料中心都會獨立追蹤鎖定。 如果使用者按下每個資料中心，使用者會 (*threshold_limit datacenter_count **) 嘗試次數。
* 智慧鎖定會使用熟悉的位置與不熟悉的位置來區分實際使用者與不良執行者。 不熟悉和熟悉的位置都有不同的鎖定計數器。

智慧鎖定可以與使用密碼雜湊同步處理或傳遞驗證的混合式部署整合，以保護內部部署 Active Directory Domain Services (AD DS) 的帳戶遭到攻擊者鎖定。 藉由在 Azure AD 中適當地設定智慧鎖定原則，可以先將攻擊篩選掉，然後才會進入內部部署 AD DS。

使用[傳遞驗證](../hybrid/how-to-connect-pta.md)時，適用下列考慮事項：

* Azure AD 鎖定閾值**低於**AD DS 帳戶鎖定閾值。 設定值，讓 AD DS 帳戶鎖定閾值至少比 Azure AD 鎖定閾值長二或三倍。
* Azure AD 的鎖定持續時間必須設定超過持續時間後的 AD DS 重設帳戶鎖定計數器。 Azure AD 持續時間是以秒為單位設定，而 AD 持續時間設定為分鐘。

例如，如果您想要 Azure AD 計數器高於 AD DS，則 Azure AD 會是120秒 (2 分鐘) 而您的內部部署 AD 會設定為1分鐘 (60 秒) 。

> [!IMPORTANT]
> 目前，如果使用者的雲端帳戶已被智慧鎖定功能鎖定，則系統管理員無法將其解除鎖定。 系統管理員必須等待鎖定持續期間結束。 不過，使用者可以使用自助式密碼重設來解除鎖定， (SSPR) 來自受信任的裝置或位置。

## <a name="verify-on-premises-account-lockout-policy"></a>驗證內部部署帳戶鎖定原則

若要驗證您的內部部署 AD DS 帳戶鎖定原則，請使用系統管理員許可權，從已加入網域的系統完成下列步驟：

1. 開啟群組原則管理工具。
2. 編輯包含貴組織帳戶鎖定原則的群組原則，例如**預設網域原則**。
3. 流覽至 [**電腦**設定原則] [Windows] [  >  **Policies**  >  **Windows Settings**  >  **安全性設定**] [  >  **帳戶原則**] [  >  **帳戶鎖定原則**]
4. 請確認您的**帳戶鎖定閾值**，並**在值之後重設帳戶鎖定計數器**。

![修改內部部署 Active Directory 帳戶鎖定原則](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>管理 Azure AD 智慧鎖定值

根據您的組織需求，您可以自訂 Azure AD 智慧鎖定值。 您的使用者需要 Azure AD Premium P1 或更高的授權，才能自訂智慧鎖定設定（具有貴組織特定的值）。

若要檢查或修改貴組織的智慧鎖定值，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [ *Azure Active Directory*]，然後選取 [**安全性**  >  **驗證方法**] [  >  **密碼保護**]。
1. 根據帳戶遭到鎖定之前所允許的失敗登入次數，設定 [鎖定閾值]****。

    預設值為 10。

1. 將 [鎖定持續時間 (秒)]**** 設為每次鎖定的秒數。

    預設值為 60 秒 (一分鐘)。

> [!NOTE]
> 如果鎖定之後的第一個登入也失敗，帳戶將會再次鎖定。 如果帳戶重複鎖定，鎖定持續時間將會增加。

![在 Azure 入口網站中自訂 Azure AD 智慧鎖定原則](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>如何判斷智慧鎖定功能是否正常運作

觸發智慧鎖定臨界值時，您將會在帳戶被鎖定時收到下列訊息：

*您的帳戶已暫時鎖定，以防止未經授權的使用。請稍後再試一次，如果您仍然遇到問題，請洽詢您的系統管理員。*

## <a name="next-steps"></a>後續步驟

若要進一步自訂體驗，您可以[設定 Azure AD 密碼保護的自訂禁用密碼](tutorial-configure-custom-password-protection.md)。

若要協助使用者從網頁瀏覽器重設或變更其密碼，您可以[設定 Azure AD 自助式密碼重設](tutorial-enable-sspr.md)。
