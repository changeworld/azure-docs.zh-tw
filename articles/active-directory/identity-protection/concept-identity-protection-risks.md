---
title: 什麼是風險？ Azure AD Identity Protection
description: 說明 Azure AD Identity Protection 的風險
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bc7b0ce521522e677e0dc53809c8c33e0743f0d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327913"
---
# <a name="what-is-risk"></a>什麼是風險？

Azure AD Identity Protection 中的風險偵測包含任何與目錄中的使用者帳戶相關的已識別可疑動作。

Identity Protection 可讓組織存取強大的資源，以快速查看及回應這些可疑的動作。 

![顯示有風險的使用者和登入的安全性總覽](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>風險類型和偵測

有兩種類型的風險 **使用者** 和登 **入** ，以及 **即時** 和 **離線**的兩種類型的偵測或計算。

即時偵測可能不會在報告中顯示五到十分鐘的時間。 離線偵測可能不會顯示在報告中2到20個小時。

### <a name="user-risk"></a>使用者風險

使用者風險代表指定的身分識別或帳戶遭到入侵的機率。 

這些風險會使用 Microsoft 的內部和外部威脅情報來源（包括安全性研究人員、Microsoft 的安全性小組，以及其他受信任的來源）離線計算。

| 風險偵測 | 說明 |
| --- | --- |
| 認證外洩 | 此風險偵測類型表示使用者的有效認證已洩露。 當網路罪犯入侵合法使用者的有效密碼時，他們通常會共用這些認證。 這項共用通常是藉由在黑色市場上公開張貼、貼上網站，或透過交易和銷售認證來完成。 當 Microsoft 洩漏的認證服務從深色 web、貼上網站或其他來源取得使用者認證時，系統會根據 Azure AD 使用者目前有效的認證來檢查它們，以找出有效的相符專案。 如需洩漏認證的詳細資訊，請參閱 [常見問題](#common-questions)。 |
| Azure AD 威脅情報 | 此風險偵測類型指出指定使用者不尋常的使用者活動，或是與以 Microsoft 內部和外部威脅情報來源為基礎的已知攻擊模式一致的使用者活動。 |

### <a name="sign-in-risk"></a>登入風險

登入風險表示指定的驗證要求未獲身分識別擁有者授權的機率。 

您可以使用 Microsoft 的內部和外部威脅情報來源（包括安全性研究人員、Microsoft 的安全性小組，以及其他受信任的來源），即時計算這些風險或離線計算這些風險。

| 風險偵測 | 偵測類型 | 說明 |
| --- | --- | --- |
| 匿名 IP 位址 | 即時 | 此風險偵測類型表示來自匿名 IP 位址的登入 (例如，Tor browser 或匿名 VPN) 。 這些 IP 位址通常由意圖隱藏其登入遙測資料 (IP 位址、位置、裝置等) 的執行者所使用，以遂行其潛在惡意目的。 |
| 非慣用登入位置 | 離線 | 此風險偵測類型會識別來自不同地理位置的兩個登入，其中至少有一個位置對於使用者來說可能也是非典型的，但有提供過去的行為。 在許多其他因素中，此機器學習演算法會考量兩次登入之間的時間，以及使用者從第一個位置移到第二個位置所需的時間，這表示有不同的使用者正在使用相同的認證。 <br><br> 演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。 系統具有最早14天或10個登入的初始學習期間，在這段期間內，它會學習新使用者的登入行為。 |
| 已連結惡意程式碼的 IP 位址 | 離線 | 此風險偵測類型表示從受到惡意程式碼攻擊的 IP 位址登入，這些 IP 位址已知可主動與 bot 伺服器通訊。 這項偵測是藉由將使用者裝置的 IP 位址與 bot 伺服器使用中的 bot 伺服器相關聯的 IP 位址相互關聯而決定。 |
| 不熟悉的登入屬性 | 即時 | 此風險偵測類型會考慮過去的登入歷程記錄 (IP、緯度/經度和 ASN) 來尋找異常的登入。系統會儲存使用者先前使用位置的相關資訊，並考慮這些「熟悉的」位置。 從不在熟悉位置清單中的位置進行登入時，就會觸發風險偵測。 新建立的使用者將會處於「學習模式」一段時間，在此期間內，不熟悉的登入屬性會在我們的演算法瞭解使用者的行為時關閉風險偵測。 學習模式持續時間是動態的，取決於演算法收集足夠的使用者登入模式資訊所花的時間。 最小持續時間為五天。 使用者可在一段長時間無活動後回到學習模式。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 <br><br> 我們也會針對基本驗證 (或舊版通訊協定) 執行這項偵測。 由於這些通訊協定沒有用戶端識別碼之類的新式屬性，因此只能以有限的遙測資料減少誤判。 我們建議客戶移轉至新式驗證。 |
| 系統管理員已確認消費者遭盜用 | 離線 | 此偵測表示系統管理員已在具風險的使用者 UI 或使用 riskyUsers API 中選取 [確認使用者遭盜用]。 若要查看哪些系統管理員已確認此使用者遭盜用，請透過 UI 或 API) 檢查使用者的風險歷程記錄 (。 |
| 惡意 IP 位址 | 離線 | 此偵測表示從惡意 IP 位址登入。 因為從 IP 位址或其他 IP 信譽來源收到的認證無效，所以會根據高失敗率將 IP 位址視為惡意。 |
| 可疑的收件匣操作規則 | 離線 | [Microsoft Cloud App Security (MCAS) ](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules)會探索此偵測。 此偵測可描述您的環境，並在使用者收件匣上設定會刪除或移動訊息或資料夾的可疑規則時觸發警示。 這項偵測可能表示使用者的帳戶遭到入侵、已刻意隱藏訊息，以及用來在組織中散發垃圾郵件或惡意程式碼的信箱。 |
| 密碼噴灑 | 離線 | 密碼噴灑攻擊是指以統一的暴力密碼破解方式使用一般密碼來攻擊多個使用者名稱的攻擊，以取得未經授權的存取權。 執行密碼噴灑攻擊時，會觸發此風險偵測。 |
| 不可能的旅遊 | 離線 | [Microsoft Cloud App Security (MCAS) ](/cloud-app-security/anomaly-detection-policy#impossible-travel)會探索此偵測。 此偵測會識別在一段時間內出現的兩個使用者活動 (可能是單一或多個工作階段)，它們來自遙遠的地理位置，而使用者從第一個位置移動到第二個位置所需的時間會比此段時間長，這代表不同的使用者在使用相同的認證。 |

### <a name="other-risk-detections"></a>其他風險偵測

| 風險偵測 | 偵測類型 | 說明 |
| --- | --- | --- |
| 已偵測到的額外風險 | 即時或離線 | 此偵測表示偵測到上述其中一個 premium 偵測。 由於只有 Azure AD Premium P2 客戶才能看到 premium 偵測，因此在沒有 Azure AD Premium P2 授權的客戶上，其標題為「偵測到額外的風險」。 |

## <a name="common-questions"></a>常見問題

### <a name="risk-levels"></a>風險層級

身分識別保護會將風險分類成三個層級：低、中和高。 

雖然 Microsoft 並未提供如何計算風險的特定詳細資料，但我們會假設每個層級針對使用者或登入遭到入侵時都具備更高的信賴度。 例如，相較於使用者的其中一個執行個體出現不熟悉的登入屬性，將認證洩漏給其他使用者的情況比較嚴重。

### <a name="leaked-credentials"></a>認證外洩

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Microsoft 會在哪裡找到洩漏的認證？

Microsoft 在各式各樣的地方發現洩漏的認證，包括：

- 公開貼上的網站（例如 pastebin.com 和 paste.ca），不正確的執行者通常會張貼這類材料。 這個位置是最糟的動作專案，它會先停止搜尋以找出遭竊的認證。
- 執法機關。
- Microsoft 的其他小組執行深色的 web 研究。

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>為什麼我看不到任何洩漏的認證？

當 Microsoft 發現新的公開可用批次時，會處理認證外洩。 由於敏感性本質的緣故，在處理之後不久就會刪除洩漏的認證。 當您啟用密碼雜湊同步處理之後，只會找到新的洩漏認證 (PHS) 將會針對您的租使用者進行處理。 不會對先前找到的認證配對進行驗證。 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>我沒看到任何洩漏的認證風險事件嗎？

如果您沒有看到任何流失的認證風險事件，原因如下：

- 您未針對您的租使用者啟用 PHS。
- Microsoft 找不到任何與您的使用者相符的認證配對。

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Microsoft 處理新認證的頻率為何？

認證會在找到之後立即處理，通常是每天多個批次。

## <a name="next-steps"></a>後續步驟

- [可用來降低風險的原則](concept-identity-protection-policies.md)
- [安全性概觀](concept-identity-protection-security-overview.md)
