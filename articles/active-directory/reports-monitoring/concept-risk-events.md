---
title: Azure Active Directory 風險偵測 |Microsoft Docs
description: 此 artice 可讓您深入瞭解有哪些風險偵測。
services: active-directory
keywords: azure active directory identity protection, 安全性, 風險, 風險層級, 弱點, 安全性原則
author: MarkusVi
manager: daveba
ms.assetid: fa2c8b51-d43d-4349-8308-97e87665400b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1f3755d61b5fa082665cfdb9aa91d1e31e2d4e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266477"
---
# <a name="azure-active-directory-risk-detections"></a>Azure Active Directory 風險偵測

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 探索遭入侵的身分識別並不容易。 Azure Active Directory 使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 每個偵測到的可疑動作都會儲存在名為**風險偵測**的記錄中。

您可以在兩個地方查看回報的風險偵測：

 - **Azure AD 報告**-風險偵測屬於 Azure AD 的安全性報告。 如需詳細資訊，請參閱[有風險的安全性報告上的使用者](concept-user-at-risk.md)和[有風險的登入安全性報告](concept-risky-sign-ins.md)。

 - **Azure AD Identity Protection** -風險偵測也是[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)報告功能的一部分。

此外，您可以使用身分[識別保護風險偵測 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent) ，利用 Microsoft Graph 以程式設計方式存取安全性偵測。 如需詳細資訊，請參閱[開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph](../identity-protection/graph-get-started.md)。 

目前，Azure Active Directory 會偵測六種類型的風險偵測：

- [認證外洩的使用者](#leaked-credentials) 
- [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) 
- [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) 
- [從受感染的裝置登入](#sign-ins-from-infected-devices) 
- [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations) 

![風險偵測](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> 有時候，您可能會發現在登[入報告](concept-sign-ins.md)中沒有相對應登入專案的風險偵測。 這是因為 Identity Protection 會同時評估**互動式**和**非互動式**登入的風險，而登入報告則只會顯示互動式登入的部分。

您針對偵測到的風險偵測所取得的深入解析，會系結至您的 Azure AD 訂用帳戶。 

* 使用 **Azure AD Premium P2 版本**時，您會獲得有關所有基礎偵測的最詳細資訊。 
* 使用**Azure AD Premium P1 版本**時，您的授權不會涵蓋「先進的偵測」（例如不熟悉的登入屬性），而且會出現在 [登入已偵測**到其他風險**] 的 [名稱] 底下。 此外，[風險層級] 和 [風險詳細資料] 欄位是隱藏的。

雖然風險偵測的偵測已代表保護您身分識別的重要層面，但您也可以選擇手動解決這些問題，或藉由設定條件式存取原則來實行自動化回應。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。

## <a name="risk-detection-types"></a>風險偵測類型

「**風險偵測類型**」屬性是已為其建立風險偵測記錄之可疑動作的識別碼。

Microsoft 針對偵測程序的持續投資的結果是︰

- 已改善現有風險偵測的偵測精確度 
- 未來將新增的新風險偵測類型

### <a name="leaked-credentials"></a>認證外洩

當網路罪犯入侵合法使用者的有效密碼時，他們通常會共用這些認證。 他們的做法通常是在深層網路上公開張貼或貼上站台，或是在黑市上交易或銷售認證。 Microsoft 的認證外洩服務取得使用者名稱 / 密碼組的方式，是監視公用及深層網路，以及使用：

- 研究員
- 執法機關
- Microsoft 安全性小組
- 其他受信任的來源 

當服務取得使用者名稱 / 密碼組時，它們會針對 AAD 使用者目前的有效認證進行檢查。 找到相符的時，表示使用者的密碼已遭盜用，而且會建立**洩漏的認證風險偵測**。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入

此風險偵測類型會識別已從已識別為匿名 proxy IP 位址的 IP 位址成功登入的使用者。 這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。

### <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動

此風險偵測類型會識別源自地理位置相距較遠的兩個登入，其中至少有一個位置對於使用者而言可能也是不尋常的，但會提供過去的行為。 在許多其他因素中，此機器學習演算法會考量兩次登入之間的時間，以及使用者從第一個位置移到第二個位置所需的時間，這表示有不同的使用者正在使用相同的認證。

演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。 系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者的登入行為。 

### <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入

此風險偵測類型會考慮過去的登入位置（IP、緯度/經度和 ASN），以判斷新的/不熟悉的位置。 系統會儲存有關使用者先前所用位置的資訊，並考量這些「熟悉的」位置。 當登入來自不在熟悉位置清單中的位置時，就會觸發風險偵測。 系統有為期 30 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 

Identity Protection 偵測到來不熟悉位置的登入也適用於基本驗證 / 舊版通訊協定。 因為這些通訊協定沒有熟悉的新式功能，例如用戶端識別碼，所以沒有足夠的遙測可減少誤判。 若要減少偵測到的風險偵測數目，您應該移至新式驗證。   

### <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入

此風險偵測類型會識別受惡意程式碼感染之裝置的登入，已知要主動與 bot 伺服器通訊。 讓使用者裝置的 IP 位址與聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
此風險偵測類型會在很短的時間內，識別在多個使用者帳戶中，看到大量失敗登入嘗試的 IP 位址。 這符合攻擊者所使用的 IP 位址流量模式，而且強烈指出帳戶已經或即將遭到入侵。 這種機器學習演算法會忽略明顯的「誤判」，例如，組織中的其他使用者定期使用的 IP 位址。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者和新租用戶的登入行為。

## <a name="detection-type"></a>偵測類型

[偵測類型] 屬性是風險偵測的偵測時間範圍指標（**即時**或**離線**）。 目前，在發生風險偵測之後，會在後續處理作業中離線偵測到大部分的風險偵測。

下表列出偵測類型要在相關報告中顯示所花費的時間：

| 偵測類型 | 報告延遲 |
| --- | --- |
| 即時 | 5 至 10 分鐘 |
| 離線 | 2 至 4 小時 |


針對 Azure Active Directory 偵測的風險偵測類型，偵測類型為：

| 風險偵測類型 | 偵測類型 |
| :-- | --- | 
| [認證外洩的使用者](#leaked-credentials) | 離線 |
| [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) | 即時 |
| [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) | 離線 |
| [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations) | 即時 |
| [從受感染的裝置登入](#sign-ins-from-infected-devices) | 離線 |
| [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) | 離線|


## <a name="risk-level"></a>風險層級

風險偵測的風險層級屬性是風險偵測的嚴重性和信賴度的指標（**高**、**中**或**低**）。 這個屬性可協助您排定必須採取之動作的優先順序。 

風險偵測的嚴重性代表身分識別洩露預測的信號強度。 信賴度是發生誤判可能性的指標。 

例如， 

* **高**：高信賴度和高嚴重性風險偵測。 這些事件強烈指出使用者的身分識別已遭入侵，而且應該立即補救任何受影響的使用者帳戶。

* **中**：高嚴重性，但信賴度較低的風險偵測，或反之亦然。 這些事件具有潛在風險，而且應該補救任何受影響的使用者帳戶。

* **低**：低信賴度和低嚴重性風險偵測。 此事件可能不需要立即採取行動，但當與其他風險偵測結合時，可能會提供強式表示身分識別遭到入侵。

![風險層級](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>認證外洩

洩漏的認證風險偵測會分類為**高**，因為它們會清楚指出使用者名稱和密碼可供攻擊者使用。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入

此風險偵測類型的風險層級為「**中**」，因為匿名 IP 位址並不是帳戶洩露的強式指示。 我們建議您立即連絡使用者，確認他們是否使用匿名 IP 位址。


### <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動

不可能的移動通常會明顯指出駭客已能夠成功登入。 不過，當使用者使用新裝置或使用組織中其他使用者通常不會使用的 VPN 進行移動時，可能會發生誤判。 另一個誤判來源是誤將伺服器 IP 當作用戶端 IP 傳遞的應用程式，其可能會導致從裝載應用程式後端的資料中心進行登入 (這些通常是 Microsoft 資料中心，其可能導致從 Microsoft 擁有的 IP 位址進行登入)。 由於這些誤報，此風險偵測的風險層級為「**中**」。

> [!TIP]
> 您可以藉由設定 [[命名位置](../active-directory-named-locations.md)]，來減少此風險偵測類型的報告錯誤數量。 

### <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入

不熟悉的位置可以強烈指出攻擊者可能使用遭竊的身分識別。 當使用者正在移動、試用新裝置或使用新的 VPN 時，可能會發生誤判。 由於這些誤判，以致此事件類型的風險層級為**中**。

### <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入

此風險偵測會識別 IP 位址，而不是使用者裝置。 如果有數個裝置位於單一 IP 位址後方，而只有部分受 bot 網路控制，則來自其他裝置的登入會不必要地觸發此事件，這就是為何此風險偵測分類為**低**的原因。  

建議您連絡使用者並掃描使用者的所有裝置。 使用者的個人裝置也可能受到感染，或可能是其他人從與使用者相同的 IP 位址使用受感染的裝置。 受感染的裝置通常是受到防毒軟體尚未識別的惡意程式碼所感染，這也表示任何不良的使用者習慣可能會導致裝置受到感染。

如需如何處理惡意程式碼感染的詳細資訊，請參閱 [惡意程式碼防護中心](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)。

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入

我們建議您連絡使用者，確認他們是否實際從標示為可疑的 IP 位址進行登入。 此事件類型的風險層級為「**中**」，因為相同 IP 位址背後可能有數個裝置，而只有某些裝置可能負責進行可疑的活動。 


## <a name="next-steps"></a>後續步驟

* [有風險使用者的安全性報告](concept-user-at-risk.md)
* [有風險登入的安全性報告](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md)。
