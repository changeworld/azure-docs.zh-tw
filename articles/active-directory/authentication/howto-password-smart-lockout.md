---
title: 使用智慧鎖定防止攻擊-Azure Active Directory
description: Azure Active Directory 智慧鎖定有助於保護組織免於遭受嘗試猜測密碼的暴力攻擊
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61ae942ed189dc4245a9a0b282daf4cad5323536
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80652570"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory 智慧鎖定

智慧鎖定可協助鎖定嘗試猜測使用者密碼或使用暴力方法來取得的不良執行者。 此外也會辨識來自有效使用者的登入，並將其視為不同於攻擊者和其他不明來源所進行的登入。 智慧鎖定可在鎖定攻擊者的同時，讓您的使用者仍可繼續存取其帳戶並保有生產力。

根據預設，智慧鎖定會在 10 次失敗嘗試後鎖定帳戶，使其無法嘗試登入一分鐘。 在每次後續登入嘗試失敗之後，帳戶會再次鎖定，先鎖定一分鐘，後續嘗試鎖定時間會更長。

智慧鎖定會追蹤最後三個不正確的密碼雜湊，以避免因為相同密碼而累計鎖定計數器。 如果有人多次輸入相同的錯誤密碼，此行為並不會導致帳戶鎖定。

 > [!NOTE]
 > 雜湊追蹤功能不適用於傳遞驗證已啟用的客戶，因為驗證是發生在內部部署，而不是雲端。

使用 AD FS 2016 和 AF FS 2019 的同盟部署，可以使用[AD FS 外部網路鎖定和外部網路智慧鎖定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)來啟用類似的優點。

任何 Azure AD 客戶只要採用兼具適當安全性和可用性的預設設定，智慧鎖定就一律會啟用。 您的使用者必須付費 Azure AD 授權，才能自訂智慧鎖定設定，其中包含您組織的特定值。

使用智慧鎖定並不保證真正的使用者永遠不會遭到鎖定。當智慧鎖定鎖定使用者帳戶時，我們會嘗試我們的最佳做法，不要鎖定真正的使用者。 鎖定服務會嘗試確保不良的執行者無法取得正版使用者帳戶的存取權。  

* 每個 Azure Active Directory 資料中心會獨立追蹤鎖定。 如果使用者叫用每個資料中心，使用者會有 (threshold_limit * datacenter_count) 的嘗試次數。
* 智慧鎖定會使用熟悉的位置與不熟悉的位置來區分實際使用者與不良執行者。 不熟悉的位置與熟悉的位置會有各自的鎖定計數器。

智慧鎖定可與混合式部署整合，使用密碼雜湊同步或傳遞驗證來保護內部部署 Active Directory 帳戶不被攻擊者鎖定。 藉由適當地在 Azure AD 中設定智慧鎖定原則，攻擊將會在到達內部部署 Active Directory 之前遭到篩除。

使用[傳遞驗證](../hybrid/how-to-connect-pta.md)時，您必須確定：

* Azure AD 的鎖定閾值「小於」**** Active Directory 帳戶的鎖定閾值。 請適當設定這些值，使 Active Directory 帳戶的鎖定閾值比 Azure AD 鎖定閾值至少長兩到三倍。 
* Azure AD 的鎖定持續時間必須設定超過持續時間後的 Active Directory 重設帳戶鎖定計數器。 請注意，Azure AD 持續時間是以秒為單位設定，而 AD 持續時間設定為分鐘。 

例如，如果您想要 Azure AD 計數器高於 AD，則當您的內部部署 AD 設定為1分鐘（60秒）時，Azure AD 會是120秒（2分鐘）。

> [!IMPORTANT]
> 目前，如果使用者的雲端帳戶已被智慧鎖定功能鎖定，則系統管理員無法將其解除鎖定。 系統管理員必須等待鎖定持續期間結束。 不過，使用者可以使用來自受信任裝置或位置的自助式密碼重設（SSPR）來解除鎖定。

## <a name="verify-on-premises-account-lockout-policy"></a>驗證內部部署帳戶鎖定原則

請使用下列指示來驗證您的內部部署 Active Directory 帳戶鎖定原則：

1. 開啟群組原則管理工具。
2. 編輯包含組織帳戶鎖定原則的群組原則，例如**預設網域原則**。
3. 流覽至 **[電腦** > **設定** > **原則** > ] [Windows] [**安全性設定** > ] [**帳戶原則** > ] [**帳戶鎖定原則**]
4. 請確認您的**帳戶鎖定閾值**，並**在值之後重設帳戶鎖定計數器**。

![修改內部部署 Active Directory 帳戶鎖定原則](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>管理 Azure AD 智慧鎖定值

根據您的組織需求，智慧鎖定值可能需要進行自訂。 您的使用者必須付費 Azure AD 授權，才能自訂智慧鎖定設定，其中包含您組織的特定值。

若要檢查或修改組織的智慧鎖定值，請使用下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 [Azure Active Directory]  。 選取 [**安全性** > **驗證方法** > ] [**密碼保護**]。
1. 根據帳戶遭到鎖定之前所允許的失敗登入次數，設定 [鎖定閾值]****。 預設值為 10。
1. 將 [鎖定持續時間 (秒)]**** 設為每次鎖定的秒數。 預設值為 60 秒 (一分鐘)。

> [!NOTE]
> 如果鎖定之後的第一個登入也失敗，帳戶將會再次鎖定。 如果帳戶重複鎖定，鎖定持續時間將會增加。

![在 Azure 入口網站中自訂 Azure AD 智慧鎖定原則](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>如何判斷智慧鎖定功能是否正常運作

觸發智慧鎖定臨界值時，您將會在帳戶被鎖定時收到下列訊息：

**您的帳戶已暫時鎖定，以防止未經授權的使用。請稍後再試一次，如果您仍然遇到問題，請洽詢您的系統管理員。**

## <a name="next-steps"></a>後續步驟

* [了解如何使用 Azure AD 在組織中禁用不當密碼。](howto-password-ban-bad.md)
* [設定自助式密碼重設以允許使用者解除鎖定其本身的帳戶。](quickstart-sspr.md)
