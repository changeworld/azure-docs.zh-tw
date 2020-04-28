---
title: 有什麼風險？ Azure AD Identity Protection
description: 說明 Azure AD Identity Protection 中的風險
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79253685"
---
# <a name="what-is-risk"></a>有什麼風險？

Azure AD Identity Protection 中的風險偵測包括任何與目錄中的使用者帳戶相關的已識別可疑動作。

Identity Protection 可讓組織存取強大的資源，以快速查看和回應這些可疑動作。 

![顯示有風險的使用者和登入的安全性總覽](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>風險類型和偵測

有兩種類型的風險**使用者**和登**入**，以及兩種類型的偵測或計算**即時**和**離線**。

### <a name="user-risk"></a>使用者風險

使用者風險代表特定身分識別或帳戶遭到入侵的機率。 

這些風險會使用 Microsoft 的內部和外部威脅情報來源進行離線計算，包括安全性研究人員、法律強制執行專家、Microsoft 的安全性小組，以及其他受信任的來源。

| 風險偵測 | 說明 |
| --- | --- |
| 認證外洩 | 此風險偵測類型指出使用者的有效認證已洩漏。 當網路罪犯入侵合法使用者的有效密碼時，他們通常會共用這些認證。 這項共用通常是藉由在黑色市場上公開張貼、貼上網站，或貿易和銷售認證而完成。 當 Microsoft 流失的認證服務從深色 web、貼上網站或其他來源取得使用者認證時，會針對 Azure AD 使用者目前的有效認證進行檢查，以尋找有效的相符專案。 |
| Azure AD 威脅情報 | 此風險偵測類型指出指定使用者不尋常的使用者活動，或根據 Microsoft 內部和外部威脅情報來源，與已知的攻擊模式一致。 |

### <a name="sign-in-risk"></a>登入風險

登入風險表示指定的驗證要求未被身分識別擁有者授權的機率。 

您可以使用 Microsoft 的內部和外部威脅情報來源，即時計算這些風險，或進行離線計算，包括安全性研究人員、法律強制專業人員、Microsoft 的安全性小組，以及其他受信任的來源。

| 風險偵測 | 偵測類型 | 說明 |
| --- | --- | --- |
| 匿名 IP 位址 | 即時 | 此風險偵測類型表示從匿名 IP 位址登入（例如 Tor 瀏覽器或匿名 VPN）。 這些 IP 位址通常由意圖隱藏其登入遙測資料 (IP 位址、位置、裝置等) 的執行者所使用，以遂行其潛在惡意目的。 |
| 非慣用登入位置 | 離線 | 此風險偵測類型會識別源自地理位置相距較遠的兩個登入，其中至少有一個位置對於使用者而言可能也是不尋常的，但會提供過去的行為。 在許多其他因素中，此機器學習演算法會考量兩次登入之間的時間，以及使用者從第一個位置移到第二個位置所需的時間，這表示有不同的使用者正在使用相同的認證。 <br><br> 演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。 系統有14天或10次登入的初始學習期間，在這段期間內，它會學習新使用者的登入行為。 |
| 已連結惡意程式碼的 IP 位址 | 離線 | 此風險偵測類型指出來自受惡意程式碼感染的 IP 位址的登入，而這項功能已知會主動與 bot 伺服器通訊。 這項偵測的判斷方式是將使用者裝置的 IP 位址與 bot 伺服器在使用中時所連線的 IP 位址相互關聯。 |
| 不熟悉的登入屬性 | 即時 | 此風險偵測類型會考慮過去的登入歷程記錄（IP、緯度/經度和 ASN），以尋找異常的登入。系統會儲存使用者先前所使用位置的相關資訊，並考慮這些「熟悉的」位置。 當登入來自不在熟悉位置清單中的位置時，就會觸發風險偵測。 新建立的使用者將處於「學習模式」一段時間，其中不熟悉的登入屬性風險偵測會在我們的演算法學慣用戶行為時關閉。 學習模式持續時間是動態的，而且取決於演算法收集足夠的使用者登入模式資訊所需的時間。 最短持續時間為五天。 使用者可以在長時間沒有活動後回到學習模式。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 <br><br> 我們也會針對基本驗證 (或舊版通訊協定) 執行這項偵測。 由於這些通訊協定沒有用戶端識別碼之類的新式屬性，因此只能以有限的遙測資料減少誤判。 我們建議客戶移轉至新式驗證。 |
| 系統管理員已確認使用者遭盜用 | 離線 | 此偵測表示系統管理員已在具風險的使用者 UI 或使用 riskyUsers API 中，選取 [確認使用者遭盜用]。 若要查看哪些系統管理員已確認此使用者遭入侵，請檢查使用者的風險歷程記錄（透過 UI 或 API）。 |
| 惡意 IP 位址 | 離線 | 此偵測會指出從惡意 IP 位址登入。 由於從 IP 位址或其他 IP 信譽來源收到不正確認證，因此會根據高失敗率將 IP 位址視為惡意。 |
| 可疑的收件匣操作規則 | 離線 | [Microsoft Cloud App Security （MCAS）](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)會探索此偵測。 此偵測可描述您的環境，並在使用者收件匣上設定會刪除或移動訊息或資料夾的可疑規則時觸發警示。 這可能表示使用者的帳戶遭到入侵、訊息被刻意隱藏，以及信箱正用來散佈組織中的垃圾郵件或惡意程式碼。 |
| 不可能的旅遊 | 離線 | [Microsoft Cloud App Security （MCAS）](/cloud-app-security/anomaly-detection-policy#impossible-travel)會探索此偵測。 此偵測會識別在一段時間內出現的兩個使用者活動 (可能是單一或多個工作階段)，它們來自遙遠的地理位置，而使用者從第一個位置移動到第二個位置所需的時間會比此段時間長，這代表不同的使用者在使用相同的認證。 |

### <a name="other-risk-detections"></a>其他風險偵測

| 風險偵測 | 偵測類型 | 說明 |
| --- | --- | --- |
| 偵測到額外的風險 | 即時或離線 | 這項偵測會指出偵測到上述其中一個 premium 偵測。 由於高階偵測只會 Azure AD Premium P2 客戶看到，所以它們的標題為「偵測到額外的風險」，適用于沒有 Azure AD Premium P2 授權的客戶。 |

## <a name="next-steps"></a>後續步驟

- [可用來降低風險的原則](concept-identity-protection-policies.md)

- [安全性概觀](concept-identity-protection-security-overview.md)
