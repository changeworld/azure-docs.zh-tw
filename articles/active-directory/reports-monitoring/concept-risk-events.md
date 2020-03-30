---
title: Azure 活動目錄風險檢測 |微軟文檔
description: 本藝術為您提供了風險檢測的詳細概述。
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
ms.openlocfilehash: 4caa248f6972609ecb6bf71dd521c68d78cebd70
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383951"
---
# <a name="azure-active-directory-risk-detections"></a>Azure 活動目錄風險檢測

大部分的安全性缺口出現於當攻擊者藉由竊取使用者的身分識別來取得環境的存取權時。 探索遭入侵的身分識別並不容易。 Azure Active Directory 使用調適性機器學習服務演算法和啟發學習法，來偵測與您使用者帳戶相關的可疑動作。 每個檢測到的可疑操作都存儲在稱為**風險檢測**的記錄中。

在兩個位置，您可以查看報告的風險檢測：

 - **Azure AD 報告**- 風險檢測是 Azure AD 安全報告的一部分。 如需詳細資訊，請參閱[有風險的安全性報告上的使用者](concept-user-at-risk.md)和[有風險的登入安全性報告](concept-risky-sign-ins.md)。

 - **Azure AD 標識保護**- 風險檢測也是[Azure 活動目錄標識保護](../active-directory-identityprotection.md)的報告功能的一部分。

此外，您可以使用[身份保護風險檢測 API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent)使用 Microsoft 圖形獲得對安全檢測的程式設計訪問。 如需詳細資訊，請參閱[開始使用 Azure Active Directory Identity Protection 和 Microsoft Graph](../identity-protection/graph-get-started.md)。 

目前，Azure 活動目錄檢測六種類型的風險檢測：

- [認證外洩的使用者](#leaked-credentials) 
- [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) 
- [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) 
- [從受感染的裝置登入](#sign-ins-from-infected-devices) 
- [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) 
- [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations) 

![風險檢測](./media/concept-risk-events/91.png)

> [!IMPORTANT]
> 有時，您可能會在[登錄報告中](concept-sign-ins.md)發現沒有相應登錄條目的風險檢測。 這是因為 Identity Protection 會同時評估**互動式**和**非互動式**登入的風險，而登入報告則只會顯示互動式登入的部分。

檢測到的風險檢測的見解與 Azure AD 訂閱相關聯。 

* 使用**Azure AD 高級 P2 版本**，您可以獲取有關所有基礎檢測的最詳細資訊。 
* 使用**Azure AD 高級 P1 版本**時，高級檢測（如不熟悉的登錄屬性）不在許可證範圍內，並將以登錄名稱顯示，**並檢測到其他風險**。 此外，風險級別和風險詳細資訊欄位被隱藏。

雖然檢測風險檢測已經是保護身份的一個重要方面，但您也可以選擇手動解決這些問題或通過配置條件訪問策略實現自動回應。 如需詳細資訊，請參閱 [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)。

## <a name="risk-detection-types"></a>風險檢測類型

**風險檢測類型**屬性是已為其創建風險檢測記錄的可疑操作的識別碼。

Microsoft 針對偵測程序的持續投資的結果是︰

- 提高現有風險檢測的檢測精度 
- 將來將添加的新風險檢測類型

### <a name="leaked-credentials"></a>認證外洩

當網路罪犯入侵合法使用者的有效密碼時，他們通常會共用這些認證。 他們的做法通常是在深層網路上公開張貼或貼上站台，或是在黑市上交易或銷售認證。 Microsoft 的認證外洩服務取得使用者名稱 / 密碼組的方式，是監視公用及深層網路，以及使用：

- 研究員
- 執法機關
- Microsoft 安全性小組
- 其他受信任的來源 

當服務取得使用者名稱 / 密碼組時，它們會針對 AAD 使用者目前的有效認證進行檢查。 找到匹配項時，意味著使用者的密碼已洩露，並創建**洩露的憑據風險檢測**。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入

此風險檢測類型標識從已標識為匿名代理 IP 位址的 IP 位址成功登錄的使用者。 這些 Proxy 通常由想要隱藏其裝置 IP 位址的人員使用，而且可能用於惡意意圖。

### <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動

此風險檢測類型標識源自地理位置遙遠的位置的兩個登錄名，其中至少有一個位置對於使用者來說可能也是非典型位置，因為過去的行為。 在許多其他因素中，此機器學習演算法會考量兩次登入之間的時間，以及使用者從第一個位置移到第二個位置所需的時間，這表示有不同的使用者正在使用相同的認證。

演算法會忽略明顯的「誤判」，以致發生不可能的移動情況，例如，組織中的其他使用者定期使用的 VPN 和位置。 系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者的登入行為。 

### <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入

此風險檢測類型考慮過去的登錄位置（IP、緯度/經度和 ASN），以確定新的/不熟悉的位置。 系統會儲存有關使用者先前所用位置的資訊，並考量這些「熟悉的」位置。 當登錄從尚未在熟悉位置清單中的位置發生時，將觸發風險檢測。 系統有為期 30 天的初始學習期間，在這段期間內，它不會將任何新位置標示為不熟悉的位置。 系統也會忽略從熟悉的裝置以及地理上靠近熟悉位置的位置進行的登入。 

Identity Protection 偵測到來不熟悉位置的登入也適用於基本驗證 / 舊版通訊協定。 因為這些通訊協定沒有熟悉的新式功能，例如用戶端識別碼，所以沒有足夠的遙測可減少誤判。 要減少檢測到的風險檢測數量，應轉到現代身份驗證。   

> [!NOTE]
> 如果登錄使用者名和密碼不匹配，登錄將失敗，並且不會發生風險檢測。 僅成功登錄時才會觸發來自不熟悉的位置風險檢測的登錄。

### <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入

此風險檢測類型標識受惡意軟體感染的設備的登錄，已知這些設備會主動與機器人伺服器通信。 讓使用者裝置的 IP 位址與聯繫 Bot 伺服器的 IP 位址相互關聯，即可判定此類型。 

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入
此風險檢測類型標識 IP 位址，這些位址在短時間內在多個使用者帳戶之間看到大量失敗的登錄嘗試。 這符合攻擊者所使用的 IP 位址流量模式，而且強烈指出帳戶已經或即將遭到入侵。 這種機器學習演算法會忽略明顯的「誤判」，例如，組織中的其他使用者定期使用的 IP 位址。  系統有為期 14 天的初始學習期間，它會在這段期間了解新使用者和新租用戶的登入行為。

## <a name="detection-type"></a>偵測類型

檢測類型屬性是風險檢測檢測時間幀的指示器（**即時**或**離線**）。 目前，大多數風險檢測在風險檢測發生後在後處理操作中處於離線狀態。

下表列出偵測類型要在相關報告中顯示所花費的時間：

| 偵測類型 | 報告延遲 |
| --- | --- |
| 即時 | 5 至 10 分鐘 |
| 離線 | 2 至 4 小時 |


對於 Azure 活動目錄檢測到的風險檢測類型，檢測類型為：

| 風險檢測類型 | 偵測類型 |
| :-- | --- | 
| [認證外洩的使用者](#leaked-credentials) | 離線 |
| [從匿名 IP 位址登入](#sign-ins-from-anonymous-ip-addresses) | 即時 |
| [不可能進入非慣用位置](#impossible-travel-to-atypical-locations) | 離線 |
| [從不熟悉的位置登入](#sign-in-from-unfamiliar-locations) | 即時 |
| [從受感染的裝置登入](#sign-ins-from-infected-devices) | 離線 |
| [從具有可疑活動的 IP 位址登入](#sign-ins-from-ip-addresses-with-suspicious-activity) | 離線|


## <a name="risk-level"></a>風險層級

風險檢測的風險級別屬性是風險檢測嚴重性和信任度（**高**、**中**或**低**）的指標。 這個屬性可協助您排定必須採取之動作的優先順序。 

風險檢測的嚴重性表示信號的強度，作為身份洩露的預測器。 信賴度是發生誤判可能性的指標。 

例如， 

* **高**： 高置信度和高嚴重性風險檢測。 這些事件強烈指出使用者的身分識別已遭入侵，而且應該立即補救任何受影響的使用者帳戶。

* **中等**： 嚴重性高，但置信風險檢測較低，反之亦然。 這些事件具有潛在風險，而且應該補救任何受影響的使用者帳戶。

* **低**： 低置信度和低嚴重性風險檢測。 此事件可能不需要立即操作，但當與其他風險檢測結合使用時，可能會提供標識受損的強烈指示。

![風險層級](./media/concept-risk-events/01.png)

### <a name="leaked-credentials"></a>認證外洩

洩露的憑據風險檢測被歸類為**高**，因為它們提供了一個明確的指示，表明攻擊者可以使用使用者名和密碼。

### <a name="sign-ins-from-anonymous-ip-addresses"></a>從匿名 IP 位址登入

此風險檢測類型的風險級別為 **"中等"，** 因為匿名 IP 位址不是帳戶洩露的有力指示。 我們建議您立即連絡使用者，確認他們是否使用匿名 IP 位址。


### <a name="impossible-travel-to-atypical-locations"></a>不可能到達非典型位置的移動

不可能的移動通常會明顯指出駭客已能夠成功登入。 不過，當使用者使用新裝置或使用組織中其他使用者通常不會使用的 VPN 進行移動時，可能會發生誤判。 另一個誤判來源是誤將伺服器 IP 當作用戶端 IP 傳遞的應用程式，其可能會導致從裝載應用程式後端的資料中心進行登入 (這些通常是 Microsoft 資料中心，其可能導致從 Microsoft 擁有的 IP 位址進行登入)。 由於這些誤報，此風險檢測的風險級別為**中等**。

> [!TIP]
> 您可以通過配置[命名位置](../active-directory-named-locations.md)來減少此風險檢測類型的報告誤報量。 

### <a name="sign-in-from-unfamiliar-locations"></a>從不熟悉的位置登入

不熟悉的位置可以強烈指出攻擊者可能使用遭竊的身分識別。 當使用者正在移動、試用新裝置或使用新的 VPN 時，可能會發生誤判。 由於這些誤報，此事件種類的風險級別為 **"中**"。

### <a name="sign-ins-from-infected-devices"></a>從受感染的裝置登入

此風險檢測標識 IP 位址，而不是使用者設備。 如果多個設備位於單個 IP 位址後面，並且只有一些設備由自動程式網路控制，則從其他設備登錄我不必要地觸發此事件，這就是為什麼此風險檢測被歸類為**Low**。  

建議您連絡使用者並掃描使用者的所有裝置。 使用者的個人裝置也可能受到感染，或可能是其他人從與使用者相同的 IP 位址使用受感染的裝置。 受感染的裝置通常是受到防毒軟體尚未識別的惡意程式碼所感染，這也表示任何不良的使用者習慣可能會導致裝置受到感染。

如需如何處理惡意程式碼感染的詳細資訊，請參閱 [惡意程式碼防護中心](https://www.microsoft.com/en-us/security/portal/definitions/adl.aspx/)。

### <a name="sign-ins-from-ip-addresses-with-suspicious-activity"></a>從具有可疑活動的 IP 位址登入

我們建議您連絡使用者，確認他們是否實際從標示為可疑的 IP 位址進行登入。 此事件類型的風險層級為「**中**」，因為相同 IP 位址背後可能有數個裝置，而只有某些裝置可能負責進行可疑的活動。 


## <a name="next-steps"></a>後續步驟

* [有風險使用者的安全性報告](concept-user-at-risk.md)
* [有風險登入的安全性報告](concept-risky-sign-ins.md)
* [Azure AD Identity Protection](../active-directory-identityprotection.md)。
