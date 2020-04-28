---
title: Azure Active Directory 中的身分識別保護常見問題
description: 常見問題 Azure AD Identity Protection
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75443580"
---
# <a name="frequently-asked-questions-identity-protection-in-azure-active-directory"></a>Azure Active Directory 中的常見問題身分識別保護

## <a name="dismiss-user-risk-known-issues"></a>解除使用者風險的已知問題

[在傳統身分識別保護中**解除使用者風險**] 會將 Identity protection 中使用者風險歷程記錄內的執行者設定為**Azure AD**。

在 identity protection 中**解除使用者風險**將身分識別保護中使用者風險歷程記錄內的執行者設定為** \<系統管理員的名稱，並將超連結\>指向使用者的**分頁。

目前的已知問題造成使用者風險關閉流程中的延遲。 如果您有「使用者風險原則」，按一下 [解除使用者風險] 的幾分鐘內，此原則將會停止將套用至已解除的使用者。 不過，已知解除使用者的 [風險狀態] 有 UX 重新整理延遲。 因應措施是在瀏覽器層級重新整理頁面，以查看最新的使用者 [風險狀態]。

## <a name="risky-users-report-known-issues"></a>有風險的使用者報告已知問題

[使用者名稱]**** 欄位上的查詢區分大小寫，[名稱]**** 欄位上的查詢則為大小寫無從驗證。

切換 [顯示日期為]**** 會隱藏 [RISK LAST UPDATED]**** 資料行。 若要讀取資料行，請按一下 [具風險的使用者] 刀鋒視窗頂端的 [資料行]****。

關閉 [傳統身分識別保護] 中的**所有事件**，將風險偵測的狀態設定為 [**已關閉（已解決）**]。

## <a name="risky-sign-ins-report-known-issues"></a>有風險的登入報告已知問題

**解決**風險偵測會將狀態設定為已**通過以風險為基礎之原則的 MFA 驅動的使用者**。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="why-is-a-user-is-at-risk"></a>為什麼使用者有風險？

如果您是 Azure AD Identity Protection 客戶，請移至 [具風險的[使用者](howto-identity-protection-investigate-risk.md#risky-users)] 視圖，並按一下 [有風險的使用者]。 在底部的下拉式清單中，[風險歷程記錄] 索引標籤會顯示導致使用者風險變更的所有事件。 若要查看使用者的所有具風險登入，請按一下 [使用者的具風險登入]。 若要查看此使用者的所有風險偵測，請按一下 [使用者的風險偵測]。

### <a name="how-can-i-get-a-report-of-detections-of-a-specific-type"></a>如何取得特定類型的偵測報告？

移至 [風險偵測] 視圖，並依 [偵測類型] 篩選。 然後您就可以在中下載這份報表。CSV 或。使用頂端的 [**下載**] 按鈕的 JSON 格式。 如需詳細資訊，請參閱[如何：調查風險](howto-identity-protection-investigate-risk.md#risk-detections)一文。

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-detection"></a>為什麼我無法針對每個風險偵測設定自己的風險層級？

Identity Protection 中的風險層級是以偵測的精確度為基礎，並由我們所監督的機器學習系統技術支援。 若要自訂向使用者呈現哪些體驗，管理員可以在使用者風險原則與登入風險原則中包含/排除特定的使用者/群組。

### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>登入的位置為何與使用者真正登入自的位置不符合？

IP 地理位置對應對整個產業而言是項挑戰。 如果您認為登入報告中所列的位置與實際位置不相符，請聯繫 Microsoft 支援服務。 

### <a name="how-can-i-close-specific-risk-detections-like-i-did-in-the-old-ui"></a>我要如何在舊的 UI 中關閉特定的風險偵測？

您可以確認連結的登入遭盜用或安全，以提供有關風險偵測的意見反應。 登入所提供的意見反應會向下分流至該登入所做的所有偵測。 如果您想要關閉未連結至登入的偵測，您可以在使用者層級提供該意見反應。 如需詳細資訊，請參閱[如何：在 Azure AD Identity Protection 中提供風險回饋](howto-identity-protection-risk-feedback.md)一文。

### <a name="how-far-can-i-go-back-in-time-to-understand-whats-going-on-with-my-user"></a>我可以回到多久的時間，瞭解我的使用者會發生什麼事？

- [具[風險的使用者](howto-identity-protection-investigate-risk.md#risky-users)] 視圖會根據所有過去的登入顯示使用者的風險。 
- [有風險的登[入](howto-identity-protection-investigate-risk.md#risky-sign-ins)] 視圖會顯示過去30天內的風險正負號。 
- [[風險](howto-identity-protection-investigate-risk.md#risk-detections)偵測] 視圖會顯示過去90天內所進行的風險偵測。

### <a name="how-can-i-learn-more-about-a-specific-detection"></a>如何深入瞭解特定偵測？

所有風險偵測都記載在「[什麼是風險](concept-identity-protection-risks.md#risk-types-and-detection)」一文中。 您可以將滑鼠停留在 Azure 入口網站上偵測旁的（i）符號，以深入瞭解偵測。

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
    > 如果您認為使用者未遭到盜用，請使用使用者層級上的 [解除使用者風險]****，而不是使用登入層級上的 [確認安全]****。 使用者層級的「**解除使用者」風險**會關閉使用者風險以及所有過去具風險的登入和風險偵測。

### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-detections-are-shown-in-identity-protection"></a>為什麼在 Identity Protection 中未顯示有風險的登入或風險偵測時，我會看到具有低（或以上）風險分數的使用者？

假設使用者的風險是累積的，而且不會過期，即使在 Identity Protection 中未顯示最新的具風險登入或風險偵測，使用者也可能會有低或高於的使用者風險。 如果使用者的唯一惡意活動只是在我們儲存具風險登入和風險偵測詳細資料的時間範圍內，就會發生這種情況。 我們不會讓使用者風險過期，因為已經知道不良執行者會以盜用的身分識別停留在客戶的環境中超過 140 天，再大肆攻擊。 客戶可以移至 `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab` 檢閱使用者的風險時間軸，以了解為何使用者會有風險

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>當與登入相關聯的偵測其風險為低或中時，為何該登入的「登入風險 (彙總)」分數為「高」？

高彙總風險分數是根據登入的其他特徵而定，或根據為該登入引發了多個偵測的這項事實而定。 相反地，即使與該登入相關聯的偵測為「高」風險，登入的「登入風險 (彙總)」也有可能為「中」。 
