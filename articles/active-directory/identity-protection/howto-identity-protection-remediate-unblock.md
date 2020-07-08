---
title: 在 Azure AD Identity Protection 中補救風險和解除封鎖使用者
description: 瞭解您已關閉作用中風險偵測的選項。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06335798addadcd8591625e25ed2eafe8469ae48
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84463955"
---
# <a name="remediate-risks-and-unblock-users"></a>補救風險並解除封鎖使用者

完成[調查](howto-identity-protection-investigate-risk.md)之後，您會想要採取動作來補救風險或解除封鎖使用者。 組織也可以選擇使用其[風險原則](howto-identity-protection-configure-risk-policies.md)來啟用自動補救。 組織應該嘗試在您的組織熟悉的一段時間內，關閉他們呈現的所有風險偵測。 Microsoft 建議您儘快關閉事件，因為時間在處理風險時很重要。

## <a name="remediation"></a>修復

所有作用中的風險偵測都有助於計算名為「使用者風險層級」的值。 使用者風險層級是帳戶遭到入侵的可能性指標 (低、中、高)。 身為系統管理員，您想要讓所有風險偵測關閉，讓受影響的使用者不再有風險。

某些風險偵測可能會由 Identity Protection 標示為「已關閉（系統）」，因為這些事件已不再被視為有風險。

系統管理員可以使用下列選項來進行補救：

- 具有風險原則的自我補救
- 手動重設密碼
- 解除使用者風險
- 手動關閉個別風險偵測

### <a name="self-remediation-with-risk-policy"></a>具有風險原則的自我補救

如果您允許使用者自行補救，並在您的風險原則中使用 Azure 多重要素驗證（MFA）和自助式密碼重設（SSPR），他們可以在偵測到風險時解除封鎖。 這些偵測會被視為已關閉。 使用者必須先前已註冊 Azure MFA 和 SSPR，才能在偵測到風險時使用。

某些偵測可能不會對需要使用者自行補救的層級產生風險，但系統管理員仍應評估這些偵測。 系統管理員可以決定是否需要額外的量值，例如[封鎖來自位置的存取](../conditional-access/howto-conditional-access-policy-location.md)，或降低其原則中可接受的風險。

### <a name="manual-password-reset"></a>手動重設密碼

如果您無法使用使用者風險原則來要求重設密碼，系統管理員可以使用手動密碼重設來關閉使用者的所有風險偵測。

系統管理員在重設使用者的密碼時，會提供兩個選項：

- **產生臨時密碼** - 藉由產生暫時密碼，您可以立即讓身分識別回到安全狀態。 這個方法需要聯絡受影響的使用者，因為他們需要知道暫時密碼是什麼。 由於密碼是暫時性的，因此系統會提示使用者在下一次登入時，將密碼變更為新的內容。

- **要求使用者重設密碼** - 要求使用者重設密碼能夠自行復原，而不需要連絡技術支援中心或系統管理員。 此方法僅適用于已註冊 Azure MFA 和 SSPR 的使用者。 若使用者尚未註冊，則無法使用此選項。

### <a name="dismiss-user-risk"></a>解除使用者風險

如果密碼重設不是您的選項，因為例如使用者已被刪除，您可以選擇解除使用者風險偵測。

當您按一下 [**關閉使用者風險**] 時，所有事件都會關閉，而受影響的使用者不再有風險。 不過，因為這個方法不會影響現有的密碼，所以不會讓相關的身分識別回到安全狀態。 

### <a name="close-individual-risk-detections-manually"></a>手動關閉個別風險偵測

您可以手動關閉個別風險偵測。 藉由手動關閉風險偵測，您可以降低使用者風險層級。 通常會以手動方式關閉風險偵測，以回應相關的調查。 例如，與使用者交談時，會顯示不再需要作用中的風險偵測。 
 
手動關閉風險偵測時，您可以選擇採取下列任何動作來變更風險偵測的狀態：

- 確認使用者遭盜用
- 解除使用者風險
- 確認登入安全
- 確認登入遭盜用

## <a name="unblocking-users"></a>解除封鎖使用者

系統管理員可以選擇根據其風險原則或調查來封鎖登入。 視登入或使用者風險而定，可能會發生封鎖。

### <a name="unblocking-based-on-user-risk"></a>根據使用者風險解除封鎖

若要解除封鎖因使用者風險而遭到封鎖的帳戶，系統管理員可以使用下列選項：

1. **重設密碼** - 您可以重設使用者的密碼。
1. **解除使用者風險**-如果已達到設定的封鎖存取使用者風險層級，使用者風險原則就會封鎖使用者。 您可以藉由關閉使用者風險或手動關閉回報的風險偵測，來降低使用者的風險層級。
1. **從原則中排除使用者**-如果您認為登入原則的目前設定會造成特定使用者的問題，您可以將使用者排除在其中。 如需詳細資訊，請參閱[如何：設定及啟用風險原則](howto-identity-protection-configure-risk-policies.md#exclusions)一文中的排除範圍一節。
1. **停用原則** - 如果您認為您的原則設定對所有使用者造成問題，您可以停用原則。 如需詳細資訊，請參閱[如何：設定及啟用風險原則](howto-identity-protection-configure-risk-policies.md)一文。

### <a name="unblocking-based-on-sign-in-risk"></a>根據登入風險解除封鎖

若要根據登入風險來解除封鎖帳戶，系統管理員可以使用下列選項：

1. **從熟悉的位置或裝置登入** - 可疑的登入會遭封鎖通常是因為使用者嘗試從不熟悉的位置或裝置登入。 您的使用者可以嘗試從熟悉的位置或裝置登入，以快速判斷此原因是否為封鎖原因。
1. **從原則中排除使用者**-如果您認為登入原則的目前設定會造成特定使用者的問題，您可以將使用者排除在其中。 如需詳細資訊，請參閱[如何：設定及啟用風險原則](howto-identity-protection-configure-risk-policies.md#exclusions)一文中的排除範圍一節。
1. **停用原則** - 如果您認為您的原則設定對所有使用者造成問題，您可以停用原則。 如需詳細資訊，請參閱[如何：設定及啟用風險原則](howto-identity-protection-configure-risk-policies.md)一文。

## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview-identity-protection.md)。
