---
title: Azure 活動目錄中標識保護的常見問題解答
description: 常見問題 Azure AD 標識保護
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 140ad45d9c4f6b6f49a4ea4aefb9298e58a2cf10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443580"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Azure 活動目錄中的常見問題身份保護

## <a name="dismiss-user-risk-known-issues"></a>消除使用者風險已知問題

在經典身份保護中**消除使用者風險**將設置使用者在**Azure AD**標識保護中的風險歷史記錄中的參與者。

在"身份保護"中**消除使用者風險**，在"身份保護"中將使用者風險歷史記錄中的參與者設置為**\<管理員名稱，並帶有指向使用者的邊欄\>選項卡的超連結**。

當前已知問題會導致使用者風險解除流延遲。 如果您有「使用者風險原則」，按一下 [解除使用者風險] 的幾分鐘內，此原則將會停止將套用至已解除的使用者。 不過，已知解除使用者的 [風險狀態] 有 UX 重新整理延遲。 因應措施是在瀏覽器層級重新整理頁面，以查看最新的使用者 [風險狀態]。

## <a name="risky-users-report-known-issues"></a>風險使用者報告已知問題

[使用者名稱]**** 欄位上的查詢區分大小寫，[名稱]**** 欄位上的查詢則為大小寫無從驗證。

切換 [顯示日期為]**** 會隱藏 [RISK LAST UPDATED]**** 資料行。 若要讀取資料行，請按一下 [具風險的使用者] 刀鋒視窗頂端的 [資料行]****。

**關閉**經典身份保護中的所有事件將風險檢測的狀態設置為 **"已關閉（已解決）"。**

## <a name="risky-sign-ins-report-known-issues"></a>風險登錄報告已知問題

**在**風險檢測上解決將狀態設置給**由基於風險的策略驅動的使用者傳遞的 MFA。**

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-a-user-is-at-risk"></a>為什麼使用者有風險？

如果您是 Azure AD 標識保護客戶，請轉到[風險使用者](howto-identity-protection-investigate-risk.md#risky-users)視圖並按一下風險使用者。 在底部的抽屜中，選項卡"風險歷史記錄"將顯示導致使用者風險更改的所有事件。 要查看使用者的所有風險登錄，請按一下"使用者的風險登錄"。 要查看此使用者的所有風險檢測，請按一下"使用者的風險檢測"。

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>如何獲得特定類型的檢測報告？

轉到風險檢測視圖，然後按"檢測類型"進行篩選。 然後，您可以在 中下載此報表。CSV 或 。使用頂部的 **"下載**"按鈕的 JSON 格式。 有關詳細資訊，請參閱文章["如何：調查風險](howto-identity-protection-investigate-risk.md#risk-detections)"。

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>為什麼我不能為每個風險檢測設置自己的風險級別？

Identity Protection 中的風險層級是以偵測的精確度為基礎，並由我們所監督的機器學習系統技術支援。 若要自訂向使用者呈現哪些體驗，管理員可以在使用者風險原則與登入風險原則中包含/排除特定的使用者/群組。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>登入的位置為何與使用者真正登入自的位置不符合？

IP 地理位置對應對整個產業而言是項挑戰。 如果您覺得登錄報告中列出的位置與實際位置不匹配，請聯繫 Microsoft 支援。 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>如何像在舊 UI 中那樣關閉特定的風險檢測？

您可以通過確認連結登錄為已洩露或安全，提供風險檢測回饋。 登錄時給出的回饋會逐漸滲透到該登錄上進行的所有檢測中。 如果要關閉未連結到登錄的檢測，可以在使用者級別上提供該回饋。 有關詳細資訊，請參閱文章["如何：在 Azure AD 標識保護中提供風險回饋](howto-identity-protection-risk-feedback.md)"。

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>我可以回到多遠，瞭解我的使用者發生了什麼事情？

- [風險使用者](howto-identity-protection-investigate-risk.md#risky-users)視圖顯示使用者基於所有過去登錄的風險。 
- [風險登錄](howto-identity-protection-investigate-risk.md#risky-sign-ins)視圖顯示過去 30 天記憶體在風險跡象。 
- [風險檢測](howto-identity-protection-investigate-risk.md#risk-detections)視圖顯示過去 90 天內進行的風險檢測。

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>如何瞭解有關特定檢測的更多詳細資訊？

所有風險檢測都記錄在文章[什麼是風險](concept-identity-protection-risks.md#risk-types-and-detection)。 您可以將滑鼠懸停在 Azure 門戶檢測旁邊的 （i） 符號上，以瞭解有關檢測的更多內容。

### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Identity Protection 中的意見反應機制如何運作？

**確認遭盜用** (登入時) – 告知 Azure AD Identity Protection：登入不是由身分擁有者所執行的，並表示遭到盜用。

- 收到此意見反應時，我們會將登入和使用者風險狀態改為 [確認遭盜用]****，並將風險層級改為 [高]****。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果已補救使用者，請不要按一下 [確認遭盜用]****，因為它會將登入與使用者風險狀態改為 [確認遭盜用]****，並將風險層級改為 [高]****。

**確認安全** (登入時) – 告知 Azure AD Identity Protection：登入是由身分擁有者執行的，不會表示遭到盜用。

- 收到此意見反應時，我們會將登入 (非使用者) 風險狀態改為 [確認安全]****，並將風險層級改為 **-**。

- 此外，我們會將資訊提供給我們的機器學習系統，以供未來改進風險評定之用。

    > [!NOTE]
    > 如果您認為使用者未遭到盜用，請使用使用者層級上的 [解除使用者風險]****，而不是使用登入層級上的 [確認安全]****。 使用者級別的 **"消除使用者風險"** 將關閉使用者風險以及所有過去的風險登錄和風險檢測。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>為什麼我看到使用者的風險評分較低（或高於），即使身份保護中未顯示風險登錄或風險檢測？

鑒於使用者風險在性質上是累積的，並且不會過期，即使標識保護中未顯示最近的風險登錄或風險檢測，使用者也可能具有低或以上的風險。 如果使用者上的唯一惡意活動發生在我們存儲風險登錄和風險檢測的詳細資訊的時間範圍之外，則可能發生此情況。 我們不會讓使用者風險過期，因為已經知道不良執行者會以盜用的身分識別停留在客戶的環境中超過 140 天，再大肆攻擊。 客戶可以移至 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab` 檢閱使用者的風險時間軸，以了解為何使用者會有風險

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>當與登入相關聯的偵測其風險為低或中時，為何該登入的「登入風險 (彙總)」分數為「高」？

高彙總風險分數是根據登入的其他特徵而定，或根據為該登入引發了多個偵測的這項事實而定。 相反地，即使與該登入相關聯的偵測為「高」風險，登入的「登入風險 (彙總)」也有可能為「中」。 
