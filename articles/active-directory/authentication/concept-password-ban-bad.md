---
title: 動態禁用的密碼-Azure Active Directory
description: 使用 Azure AD 動態禁用密碼而在環境中禁用弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f905b3eb6d1675f0bc252c3500169b3144287d9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550697"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>避免在組織中使用不當密碼

業界領導人告訴您不要在多個地方使用相同的密碼，讓它變得複雜，而且不像「Password123」一樣簡單。 組織如何保證其使用者遵循最佳做法指導方針？ 他們要如何確保使用者不會使用弱式密碼，甚至是弱式密碼的變化？

具有更強式密碼的初始步驟是為您的使用者提供指引。 下列連結是目前 Microsoft 中有關本主題的指引：

[Microsoft 密碼指引](https://www.microsoft.com/research/publication/password-guidance)

有很棒的指引很重要，但是就算知道，許多使用者仍然會選擇弱式密碼。 Azure AD 密碼保護會偵測並封鎖已知的弱式密碼及其變體，並選擇性地封鎖貴組織特定的其他弱式詞彙，藉此保護您的組織。

如需現行安全性成果的詳細資訊，請參閱 [Microsoft 安全性情資報告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="global-banned-password-list"></a>全域禁用密碼清單

Azure AD Identity Protection 小組會持續分析 Azure AD 的安全性遙測資料，尋找經常使用的弱式或遭破解的密碼，更明確地說，通常是用來做為弱式密碼基礎的弱式基底詞彙。 找到這類弱式詞彙時，會將它們新增至全域禁用密碼清單。 全域禁用密碼清單的內容並非以任何外部資料源為基礎。 全域禁用密碼清單完全根據 Azure AD 安全性遙測和分析的持續性結果。

每當 Azure AD 的任何租使用者中的任何使用者變更或重設新密碼時，全域禁用密碼清單的目前版本會作為驗證密碼強度時的金鑰輸入。 這項驗證會為所有 Azure AD 客戶帶來更強的密碼。

> [!NOTE]
> 網路罪犯也會在其攻擊中使用類似的策略。 因此，Microsoft 不會公開發布此清單的內容。

## <a name="custom-banned-password-list"></a>自訂禁用密碼清單

有些組織可能會想要進一步改善安全性，方法是在 Microsoft 呼叫自訂禁用密碼清單的全域禁用密碼清單之上新增自己的自訂專案。 Microsoft 建議加入此清單的條款主要著重于組織特定的詞彙，例如：

- 品牌名稱
- 產品名稱
- 位置（例如，公司總部）
- 公司特定的內部詞彙
- 具有特定公司意義的縮寫。

一旦將詞彙新增至自訂禁用密碼清單，就會在驗證密碼時，與全域禁用密碼清單中的條款結合。

> [!NOTE]
> 自訂的禁用密碼清單限制為最多1000個詞彙。 它不是為了封鎖非常大的密碼清單而設計的。 為了充分利用自訂禁用密碼清單的優點，Microsoft 建議您先複習並瞭解密碼評估演算法（請參閱[如何評估](concept-password-ban-bad.md#how-are-passwords-evaluated)密碼），然後再將新的條款新增至自訂禁用清單。 瞭解演算法的運作方式，可讓您的企業有效率地偵測並封鎖大量弱式密碼及其變體。

例如：假設有一個名為 "Contoso" 的客戶，其以倫敦為基礎，並使產品名為「Widget」。 對於這類客戶而言，嘗試封鎖這些詞彙的特定變化（例如）可能會浪費工作，也不安全。

- "Contoso！ 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso
- "LondonHQ"
- ...等等

相反地，只會封鎖主要的基本術語，更有效率且更安全：

- 「Contoso」
- 位於
- 機械

密碼驗證演算法接著會自動封鎖弱式變體和上述的組合。

自訂禁用密碼清單和啟用內部部署 Active Directory 整合的能力，都可使用 Azure 入口網站來管理。

![在 [驗證方法] 下修改自訂禁用密碼清單](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密碼噴灑攻擊和協力廠商遭到入侵的密碼清單

其中一個關鍵 Azure AD 密碼保護的優點是協助您防禦密碼噴灑攻擊。 大部分的密碼噴灑攻擊都不會嘗試攻擊任何指定的個別帳戶，因為這類行為會透過帳戶鎖定或其他方式，大幅增加偵測的可能性。 因此，大部分的密碼噴灑攻擊會依賴針對企業中的每個帳戶只提交少數已知的最弱密碼。 這項技術可讓攻擊者快速搜尋容易遭到入侵的帳戶，同時避免可能的偵測閾值。

Azure AD 密碼保護的設計，是為了有效率地封鎖所有可能用於密碼噴灑攻擊的已知弱式密碼（根據 Azure AD 所見的真實世界安全性遙測資料）。  Microsoft 意識到協力廠商網站，列舉了先前公開已知的安全性缺口中已遭盜用的數百萬個密碼。 協力廠商密碼驗證產品通常是根據這些數百萬個密碼的暴力密碼破解。 Microsoft 認為這種技巧不是改善整體密碼強度的最佳方式，因為這是密碼噴灑攻擊者所使用的一般策略。

> [!NOTE]
> Microsoft 全域禁用密碼清單並不是以任何協力廠商資料來源為基礎，包括遭到入侵的密碼清單。

雖然 Microsoft 全域禁用清單與一些協力廠商大容量清單比較小，但它的安全性效果是源自于實際密碼噴灑攻擊的真實世界安全性遙測，而且 Microsoft 密碼驗證演算法會使用智慧型模糊比對技術。 最後的結果是，它會有效率地偵測並封鎖數百萬個最常見的弱式密碼，使其無法在您的企業中使用。 選擇將組織特定詞彙新增至自訂禁用密碼清單的客戶也可受益于相同的演算法。

如需以密碼為基礎的安全性問題的詳細資訊，請查閱[您的 Pa $ $word 並不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

## <a name="on-premises-hybrid-scenarios"></a>內部部署混合式案例

保護僅限雲端帳戶有其效用，但許多組織仍會維護包含內部部署 Windows Server Active Directory 的混合式案例。 Azure AD 密碼保護的安全性優點也可以透過安裝內部部署代理程式，延伸到您的 Windows Server Active Directory 環境中。 現在，在 Active Directory 中變更或重設密碼的使用者和系統管理員，都必須遵守與僅限雲端使用者相同的密碼原則。

## <a name="how-are-passwords-evaluated"></a>如何評估密碼

每當使用者變更或重設其密碼時，就會根據全域和自訂禁用密碼清單中的結合字詞清單（如果已設定後者），檢查新密碼的強度和複雜度。

即使使用者的密碼包含禁用密碼，如果整體密碼還不夠強，仍然可能接受此密碼。 新設定的密碼會經歷下列步驟來評估其整體強度，以判斷應該接受或拒絕該密碼。

### <a name="step-1-normalization"></a>步驟1：正規化

新密碼會先經歷正規化程序。 這項技術可讓一小部分的禁用密碼對應到較大的一組可能弱式密碼。

正規化有兩個部分。  第一個部分，所有大寫字母會變更為小寫。  第二個部，會執行一般字元替代，例如：  

| 原始字母  | 替代的字母 |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

範例：假設密碼 "blank" 遭到禁止，而使用者嘗試將其密碼變更為 “Bl@nK”。 即使 “Bl@nk” 並未明確遭到禁用，正規化程序會將這個密碼轉換為 “blank”，這禁用密碼。

### <a name="step-2-check-if-password-is-considered-banned"></a>步驟2：檢查是否將密碼視為已禁用

#### <a name="fuzzy-matching-behavior"></a>模糊比對行為

模糊比對在正規化密碼上用來識別是否包含在全域或自訂禁用密碼清單上找到的密碼。 比對程序是以一 (1) 次比較的編輯差距為基礎。  

範例：假設密碼 "abcdef" 遭到禁止，而使用者嘗試將其密碼變更為下列其中一項：

' abcdeg ' *（最後一個字元從 ' f ' 變更為 ' g '）* ' abcdefg ' *' （g ' 附加至結尾）* ' abcde ' *（尾端 ' f ' 已從結尾刪除）*

以上每個密碼並未明確符合禁用密碼 "abcdef"。 不過，由於每個範例都是在禁用字詞 ' abcdef ' 的1個編輯距離中，因此它們全都視為符合「abcdef」。

#### <a name="substring-matching-on-specific-terms"></a>子字串比對 (在特定詞彙上)

子字串比對在正規化密碼上用來檢查使用者的名字和姓氏以及租用戶名稱 (請注意，在 Active Directory 網域控制站上驗證密碼時不會進行租用戶名稱比對)。

範例：假設我們有一個使用者 Pol，他們想要將其密碼重設為 "P0l123fb"。 正規化之後，此密碼會變成 "pol123fb"。 子字串比對會發現密碼包含使用者的名字 "Pol"。 雖然 "P0l123fb" 不是特別在禁用密碼清單上，但符合的子字串會在密碼中找到 "Pol"。 因此，此密碼會遭到拒絕。

#### <a name="score-calculation"></a>分數計算

下一個步驟是在使用者的正規化新密碼中找出所有禁用密碼執行個體。 然後：

1. 在使用者密碼中找到的每個禁用密碼都會得到一分。
2. 每個剩餘的唯一字元會得到一分。
3. 密碼必須至少為五（5）個點，才會被接受。

在後續兩個範例中，我們假設 Contoso 使用 Azure AD 密碼保護，且其自訂清單上有 "contoso"。 我們也假設 “blank” 位於全域清單上。

範例：使用者將其密碼變更為 “C0ntos0Blank12”

正規化之後，此密碼會變成 “contosoblank12”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] + [空白] + [1] + [2] = 4 個點，因為此密碼低於五個（5）點，將會遭到拒絕。

範例：使用者將其密碼變更為 “ContoS0Bl@nkf9!”。

正規化之後，此密碼會變成 “contosoblankf9!”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] + [空白] + [f] + [9] + [！] = 5 分，因為此密碼至少為五（5）個點，所以已接受。

   > [!IMPORTANT]
   > 請注意，根據進行中的安全性分析和研究，禁用密碼演算法及全域清單可能在 Azure 中隨時變更。 針對內部部署 DC 代理程式服務，只有在重新安裝 DC 代理程式軟體後，更新的演算法才會生效。

## <a name="license-requirements"></a>授權需求

| 使用者 | 使用全域禁用密碼清單的 Azure AD 密碼保護 | 使用自訂禁用密碼清單的 Azure AD 密碼保護|
| --- | --- | --- |
| 僅限雲端使用者 | Azure AD Free | Azure AD Premium P1 或 P2 |
| 從內部部署 Windows Server Active Directory 同步處理的使用者 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 內部部署 Windows Server Active Directory 未同步處理至 Azure Active Directory 的使用者也可以根據已同步處理之使用者的現有授權，從 Azure AD 密碼保護中獲益。

在 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到其他授權資訊 (包括成本)。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為會禁用的密碼時，便會看到下列錯誤訊息：

不幸的是，您的密碼包含單字、片語或模式，可輕易猜到您的密碼。 請使用不同的密碼再試一次。

## <a name="next-steps"></a>後續步驟

- [設定自訂禁用密碼清單](howto-password-ban-bad.md)
- [啟用內部部署的 Azure AD 密碼保護代理程式](howto-password-ban-bad-on-premises-deploy.md)
