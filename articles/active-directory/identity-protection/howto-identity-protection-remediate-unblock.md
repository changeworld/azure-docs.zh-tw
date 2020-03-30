---
title: 在 Azure AD 標識保護中修復風險並取消阻止使用者
description: 瞭解具有密切活動風險檢測的選項。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 350e7b37d36be70cea345db52cdfb639b2f1c1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382106"
---
# <a name="remediate-risks-and-unblock-users"></a>修復風險並取消阻止使用者

完成[調查後](howto-identity-protection-investigate-risk.md)，您需要採取行動來補救風險或取消阻止使用者。 組織還可以選擇使用風險[策略](howto-identity-protection-configure-risk-policies.md)啟用自動補救。 組織應嘗試關閉組織熟悉的時間期內顯示的所有風險檢測。 Microsoft 建議儘快關閉事件，因為處理風險時時間很重要。

## <a name="remediation"></a>補救

所有活動風險檢測都有助於計算稱為使用者風險級別的值。 使用者風險層級是帳戶遭到入侵的可能性指標 (低、中、高)。 作為管理員，您希望關閉所有風險檢測，以便受影響的使用者不再面臨風險。

某些風險檢測可能將身份保護標記為"已關閉（系統）"，因為這些事件不再被確定為有風險事件。

管理員有以下選項進行修復：

- 使用風險策略進行自我補救
- 手動重設密碼
- 解除使用者風險
- 手動關閉單個風險檢測

### <a name="self-remediation-with-risk-policy"></a>使用風險策略進行自我補救

如果允許使用者在風險策略中使用 Azure 多重要素驗證 （MFA） 和自助服務密碼重設 （SSPR） 進行自我修復，則可以在檢測到風險時自行解除阻止。 然後，這些檢測被視為已關閉。 使用者必須以前註冊 Azure MFA 和 SSPR，以便在檢測到風險時使用。

某些檢測可能不會將風險提升到需要使用者自我修復的程度，但管理員仍應評估這些檢測。 管理員可能確定有必要採取其他措施，例如[阻止從位置訪問](../conditional-access/howto-conditional-access-policy-location.md)或降低其策略中的可接受風險。

### <a name="manual-password-reset"></a>手動重設密碼

如果使用使用者風險策略要求重置密碼不是一個選項，則管理員可以關閉手動密碼重設的使用者的所有風險檢測。

在為使用者重置密碼時，給管理員兩個選項：

- **產生臨時密碼** - 藉由產生暫時密碼，您可以立即讓身分識別回到安全狀態。 此方法需要聯繫受影響的使用者，因為他們需要知道臨時密碼是什麼。 由於密碼是臨時的，因此系統會提示使用者在下一次登錄期間將密碼更改為新內容。

- **要求使用者重設密碼** - 要求使用者重設密碼能夠自行復原，而不需要連絡技術支援中心或系統管理員。 此方法僅適用于註冊 Azure MFA 和 SSPR 的使用者。 對於尚未註冊的使用者，此選項不可用。

### <a name="dismiss-user-risk"></a>解除使用者風險

如果密碼重設不是您的選項，因為例如使用者已被刪除，您可以選擇關閉使用者風險檢測。

當您按一下 **"關閉使用者風險**"時，所有事件都已關閉，受影響的使用者不再面臨風險。 不過，因為這個方法不會影響現有的密碼，所以不會讓相關的身分識別回到安全狀態。 

### <a name="close-individual-risk-detections-manually"></a>手動關閉單個風險檢測

您可以手動關閉單個風險檢測。 通過手動關閉風險檢測，可以降低使用者風險級別。 通常，風險檢測會手動關閉，以回應相關調查。 例如，當與使用者交談時，會顯示不再需要主動的風險檢測。 
 
手動關閉風險檢測時，您可以選擇執行以下任一操作來更改風險檢測的狀態：

- 確認使用者受損
- 解除使用者風險
- 確認登錄安全
- 確認登錄已洩露

## <a name="unblocking-users"></a>取消阻止使用者

管理員可以選擇根據其風險策略或調查阻止登錄。 塊可能基於登錄或使用者風險發生。

### <a name="unblocking-based-on-user-risk"></a>根據使用者風險取消阻止

要取消阻止由於使用者風險而阻止的帳戶，管理員具有以下選項：

1. **重設密碼** - 您可以重設使用者的密碼。
1. **消除使用者風險**- 如果已達到已配置的使用者風險級別以阻止訪問，則使用者風險策略會阻止使用者。 您可以通過消除使用者風險或手動關閉報告的風險檢測來降低使用者的風險級別。
1. **將使用者從策略中排除**- 如果您認為登錄策略的當前配置導致特定使用者出現問題，則可以從策略中排除使用者。 有關詳細資訊，請參閱文章["如何：配置和啟用風險策略](howto-identity-protection-configure-risk-policies.md#exclusions)"中的"排除"一節。
1. **停用原則** - 如果您認為您的原則設定對所有使用者造成問題，您可以停用原則。 有關詳細資訊，請參閱文章["如何：配置和啟用風險策略](howto-identity-protection-configure-risk-policies.md)"。

### <a name="unblocking-based-on-sign-in-risk"></a>基於登錄風險取消阻止

要根據登錄風險取消阻止帳戶，管理員具有以下選項：

1. **從熟悉的位置或裝置登入** - 可疑的登入會遭封鎖通常是因為使用者嘗試從不熟悉的位置或裝置登入。 通過嘗試從熟悉的位置或設備登錄，使用者可以快速確定此原因是否是阻止原因。
1. **將使用者從策略中排除**- 如果您認為登錄策略的當前配置導致特定使用者出現問題，則可以從策略中排除使用者。 有關詳細資訊，請參閱文章["如何：配置和啟用風險策略](howto-identity-protection-configure-risk-policies.md#exclusions)"中的"排除"一節。
1. **停用原則** - 如果您認為您的原則設定對所有使用者造成問題，您可以停用原則。 有關詳細資訊，請參閱文章["如何：配置和啟用風險策略](howto-identity-protection-configure-risk-policies.md)"。

## <a name="next-steps"></a>後續步驟

若要取得 Azure AD Identity Protection 的概觀，請參閱 [Azure AD Identity Protection 概觀](overview-identity-protection.md)。
