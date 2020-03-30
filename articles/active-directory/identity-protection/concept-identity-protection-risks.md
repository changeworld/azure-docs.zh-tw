---
title: 什麼是風險？ Azure AD Identity Protection
description: 在 Azure AD 標識保護中解釋風險
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 775ff6b3ba003bed22ccd5a42cb4da005c4dbb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253685"
---
# <a name="what-is-risk"></a>什麼是風險？

Azure AD 標識保護中的風險檢測包括與目錄中使用者帳戶相關的任何已識別的可疑操作。

身份保護使組織能夠訪問強大的資源，以便查看和快速回應這些可疑操作。 

![顯示風險使用者和登錄的安全概述](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>風險類型和檢測

有兩種類型的風險**使用者**和**登錄**和兩種類型的檢測或計算**即時**和**離線**。

### <a name="user-risk"></a>使用者風險

使用者風險表示給定標識或帳戶受到威脅的可能性。 

這些風險是使用 Microsoft 的內部和外部威脅情報來源（包括安全研究人員、執法專業人員、Microsoft 的安全團隊和其他可信來源）離線計算的。

| 風險檢測 | 描述 |
| --- | --- |
| 認證外洩 | 此風險檢測類型指示使用者的有效憑據已洩露。 當網路罪犯入侵合法使用者的有效密碼時，他們通常會共用這些認證。 這種共用通常是通過在黑網上公開發布、粘貼網站或在黑市上買賣憑據來完成的。 當 Microsoft 洩露的憑據服務從暗網、粘貼網站或其他源獲取使用者憑據時，將對照 Azure AD 使用者當前的有效憑據檢查這些憑據以查找有效的匹配項。 |
| Azure AD 威脅情報 | 此風險檢測類型指示給定使用者異常或與基於 Microsoft 內部和外部威脅情報源的已知攻擊模式一致的使用者活動。 |

### <a name="sign-in-risk"></a>登入風險

登錄風險表示給定身份驗證請求未由標識擁有者授權的概率。 

這些風險可以通過 Microsoft 的內部和外部威脅情報來源（包括安全研究人員、執法專業人員、Microsoft 的安全團隊和其他可信來源）即時計算或離線計算。

| 風險檢測 | 偵測類型 | 描述 |
| --- | --- | --- |
| 匿名 IP 位址 | 即時 | 此風險檢測類型指示來自匿名 IP 位址（例如，Tor 瀏覽器或匿名 VPN）的登錄。 這些 IP 位址通常由意圖隱藏其登入遙測資料 (IP 位址、位置、裝置等) 的執行者所使用，以遂行其潛在惡意目的。 |
| 非慣用登入位置 | 離線 | 此風險檢測類型標識源自地理位置遙遠的位置的兩個登錄名，其中至少有一個位置對於使用者來說可能也是非典型位置，因為過去的行為。 在許多其他因素中，此機器學習演算法會考量兩次登入之間的時間，以及使用者從第一個位置移到第二個位置所需的時間，這表示有不同的使用者正在使用相同的認證。 <br><br> 演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。 系統的初始學習期最早為 14 天或 10 天，在此期間，系統會學習新使用者的登錄行為。 |
| 已連結惡意程式碼的 IP 位址 | 離線 | 此風險檢測類型指示受已知與機器人伺服器主動通信的惡意軟體感染的 IP 位址的登錄。 此檢測由將使用者設備的 IP 位址與在機器人伺服器處於活動狀態時與機器人伺服器接觸的 IP 位址相關聯來確定。 |
| 不熟悉的登入屬性 | 即時 | 此風險檢測類型考慮過去的登錄歷史記錄（IP、緯度/經度和 ASN）來查找異常登錄。系統存儲有關使用者使用以前的位置的資訊，並考慮這些"熟悉"的位置。 當登錄從尚未在熟悉位置清單中的位置發生時，將觸發風險檢測。 新創建的使用者將在"學習模式"中運行一段時間，在我們的演算法瞭解使用者的行為時，將關閉不熟悉的登錄屬性風險檢測。 學習模式持續時間是動態的，取決於演算法收集有關使用者登錄模式的足夠資訊所需的時間。 最短持續時間為五天。 長時間不活動後，使用者可以回到學習模式。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 <br><br> 我們也會針對基本驗證 (或舊版通訊協定) 執行這項偵測。 由於這些通訊協定沒有用戶端識別碼之類的新式屬性，因此只能以有限的遙測資料減少誤判。 我們建議客戶移轉至新式驗證。 |
| 管理員確認的使用者已洩露 | 離線 | 此檢測指示管理員已在風險使用者 UI 或使用風險使用者 API 中選擇"確認使用者受損"。 要查看哪個管理員已確認此使用者已洩露，請檢查使用者的風險歷史記錄（通過 UI 或 API）。 |
| 惡意 IP 位址 | 離線 | 此檢測指示來自惡意 IP 位址的登錄。 由於從 IP 位址或其他 IP 信譽源接收的憑據無效，因此基於高失敗率，IP 位址被視為惡意位址。 |
| 可疑的收件匣操作規則 | 離線 | 此檢測由[微軟雲應用安全 （MCAS）](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)發現。 此偵測可描述您的環境，並在使用者收件匣上設定會刪除或移動訊息或資料夾的可疑規則時觸發警示。 這可能表示使用者帳戶已洩露，郵件被有意隱藏，並且郵箱正用於在組織中分發垃圾郵件或惡意軟體。 |
| 不可能的旅遊 | 離線 | 此檢測由[微軟雲應用安全 （MCAS）](/cloud-app-security/anomaly-detection-policy#impossible-travel)發現。 此偵測會識別在一段時間內出現的兩個使用者活動 (可能是單一或多個工作階段)，它們來自遙遠的地理位置，而使用者從第一個位置移動到第二個位置所需的時間會比此段時間長，這代表不同的使用者在使用相同的認證。 |

### <a name="other-risk-detections"></a>其他風險檢測

| 風險檢測 | 偵測類型 | 描述 |
| --- | --- | --- |
| 檢測到其他風險 | 即時或離線 | 此檢測指示檢測到上述高級檢測之一。 由於高級檢測僅對 Azure AD 高級 P2 客戶可見，因此對於沒有 Azure AD 高級 P2 許可證的客戶，這些檢測名為"檢測到的額外風險"。 |

## <a name="next-steps"></a>後續步驟

- [可用來降低風險的原則](concept-identity-protection-policies.md)

- [安全性概觀](concept-identity-protection-security-overview.md)
