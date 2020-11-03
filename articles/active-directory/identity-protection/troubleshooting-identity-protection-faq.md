---
title: Azure Active Directory 中的身分識別保護常見問題
description: 常見問題 Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 10/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec45ce7634b7bc0a8f38f354112cdc2e172f1e17
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288360"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>常見問題身分識別保護 Azure Active Directory 的常見問題

## <a name="dismiss-user-risk-known-issues"></a>關閉使用者風險的已知問題

關閉傳統 Identity Protection 中的 **使用者風險** 將 Identity protection 中使用者風險歷程記錄的執行者設定為 **Azure AD** 。

關閉 Identity Protection 中的 **使用者風險** 將 identity protection 中使用者風險歷程記錄的執行者設定為 **\<Admin’s name with a hyperlink pointing to user’s blade\>** 。

目前已知的問題會導致使用者風險關閉流程中的延遲。 如果您有「使用者風險原則」，按一下 [解除使用者風險] 的幾分鐘內，此原則將會停止將套用至已解除的使用者。 不過，已知解除使用者的 [風險狀態] 有 UX 重新整理延遲。 因應措施是在瀏覽器層級重新整理頁面，以查看最新的使用者 [風險狀態]。


## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-a-user-is-at-risk"></a>為什麼使用者有風險？

如果您是 Azure AD Identity Protection 客戶，請前往 [具風險的 [使用者](howto-identity-protection-investigate-risk.md#risky-users) ] 視圖，然後按一下 [具風險的使用者]。 在底部的選單中，索引標籤的 [風險歷程記錄] 會顯示導致使用者風險變更的所有事件。 若要查看使用者的所有具風險登入，請按一下 [使用者的有風險的登入]。 若要查看此使用者的所有風險偵測，請按一下 [使用者的風險偵測]。

## <a name="why-was-my-sign-in-blocked-but-identity-protection-didnt-generate-a-risk-detection"></a>為什麼我的登入被封鎖，但 Identity Protection 未產生風險偵測？
登入可能會因為許多原因而遭到封鎖。 請務必注意，只有在驗證要求中使用正確的認證時，身分識別保護才會產生風險偵測。 如果使用者使用不正確的認證，則不會受到 Identity Protection 的旗標，除非有不良執行者使用正確的認證，否則不會有認證洩漏的風險。 有一些原因會封鎖使用者不會產生身分識別保護偵測的簽章，包括：
* 由於 IP 位址有惡意活動，因此 **ip 可能已被封鎖** 。 IP 封鎖的訊息不會區分認證是否正確。 如果 IP 遭到封鎖且未使用正確的認證，則不會產生身分識別保護偵測
* **[Smart 鎖定](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)** 可以封鎖帳戶在多次嘗試失敗後登入
* 可以強制執行 **條件式存取原則** ，以使用風險層級以外的條件來封鎖驗證要求

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>如何取得特定類型偵測的報告？

移至風險偵測視圖，並依「偵測類型」進行篩選。 然後您可以在中下載此報表。CSV 或。使用頂端的 [ **下載** ] 按鈕的 JSON 格式。 如需詳細資訊，請參閱「 [如何：調查風險](howto-identity-protection-investigate-risk.md#risk-detections)」一文。

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>為什麼我無法針對每個風險偵測設定自己的風險層級？

Identity Protection 中的風險層級是以偵測的精確度為基礎，並由我們所監督的機器學習系統技術支援。 若要自訂向使用者呈現哪些體驗，管理員可以在使用者風險原則與登入風險原則中包含/排除特定的使用者/群組。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>登入的位置為何與使用者真正登入自的位置不符合？

IP 地理位置對應對整個產業而言是項挑戰。 如果您認為登入報告中所列的位置與實際位置不相符，請與 Microsoft 支援服務聯繫。 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>如何關閉特定的風險偵測，就像我在舊的 UI 中所做的一樣？

您可以藉由確認連結的登入遭盜用或安全，來提供有關風險偵測的意見反應。 針對登入分流所提供的意見反應會向下登入所有在該登入上進行的偵測。 如果您想要關閉未連結至登入的偵測，您可以在使用者層級提供該意見反應。 如需詳細資訊，請參閱文章 [：如何：在 Azure AD Identity Protection 中提供風險意見](howto-identity-protection-risk-feedback.md)反應。

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>我可以回到多久的時間，以瞭解我的使用者會發生什麼事？

- [具 [風險的使用者](howto-identity-protection-investigate-risk.md#risky-users) ] 視圖會根據過去的所有登入顯示使用者的風險。 
- 有風險的登 [入](howto-identity-protection-investigate-risk.md#risky-sign-ins) 視圖會顯示過去30天內的風險正負號。 
- [風險](howto-identity-protection-investigate-risk.md#risk-detections)偵測視圖會顯示過去90天內所進行的風險偵測。

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>如何深入瞭解特定偵測？

所有風險偵測都記載于文章 [有什麼風險](concept-identity-protection-risks.md#risk-types-and-detection)。 您可以將滑鼠停留在 Azure 入口網站偵測旁的 (i) 符號上，以深入瞭解偵測。

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Identity Protection 中的意見反應機制如何運作？

**確認遭盜用** (登入時) – 告知 Azure AD Identity Protection：登入不是由身分擁有者所執行的，並表示遭到盜用。

- 收到此意見反應時，我們會將登入和使用者風險狀態改為 [確認遭盜用]，並將風險層級改為 [高]。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果已補救使用者，請不要按一下 [確認遭盜用]，因為它會將登入與使用者風險狀態改為 [確認遭盜用]，並將風險層級改為 [高]。

**確認安全** (登入時) – 告知 Azure AD Identity Protection：登入是由身分擁有者執行的，不會表示遭到盜用。

- 收到此意見反應時，我們會將登入 (非使用者) 風險狀態改為 [確認安全]，並將風險層級改為 **-** 。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果您認為使用者未遭到盜用，請使用使用者層級上的 [解除使用者風險]，而不是使用登入層級上的 [確認安全]。 使用者層級的 **解除使用者風險** 會關閉使用者風險，以及所有過去有風險的登入和風險偵測。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>為什麼我看到低 (或以上) 風險分數的使用者，即使 Identity Protection 中沒有顯示有風險的登入或風險偵測？

由於使用者風險是累積的，且不會過期，使用者可能會有低或更高的使用者風險，即使在 Identity Protection 中未顯示任何最近有風險的登入或風險偵測。 如果使用者的唯一惡意活動發生在我們儲存具風險登入和風險偵測之詳細資料的時間範圍內，就會發生這種情況。 我們不會讓使用者風險過期，因為已經知道不良執行者會以盜用的身分識別停留在客戶的環境中超過 140 天，再大肆攻擊。 客戶可以移至 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab` 檢閱使用者的風險時間軸，以了解為何使用者會有風險

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>當與登入相關聯的偵測其風險為低或中時，為何該登入的「登入風險 (彙總)」分數為「高」？

高彙總風險分數是根據登入的其他特徵而定，或根據為該登入引發了多個偵測的這項事實而定。 相反地，即使與該登入相關聯的偵測為「高」風險，登入的「登入風險 (彙總)」也有可能為「中」。 

### <a name="why-is-the-detection-which-is-linked-to-a-risky-sign-in-have-a-different-risk-level-than-the-sign-in-risk-level-real-time"></a>為什麼連結到具風險登入的偵測與登入風險層級的風險層級不同 (即時) ？ 

我們最近已改進了如何計算即時登入風險。 在風險偵測層級與登入風險層級之間觀察到的差異是這些變更的結果。 請注意，即時登入風險是在強制執行原則時使用的值。 
