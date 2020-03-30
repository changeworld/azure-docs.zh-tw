---
title: 動態禁用的密碼 - Azure 活動目錄
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
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263994"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>避免在組織中使用不當密碼

行業領導者告訴您不要在多個位置使用相同的密碼，使其變得複雜，並且不要像"密碼123"那樣簡單。 組織如何保證其使用者遵循最佳實踐指南？ 他們如何確保使用者不使用弱密碼，甚至不使用弱密碼的變體？

擁有更強的密碼的第一步是為使用者提供指導。 下列連結是目前 Microsoft 中有關本主題的指引：

[Microsoft 密碼指引](https://www.microsoft.com/research/publication/password-guidance)

擁有良好的指導很重要，但即使這樣，我們知道許多使用者最終還是會選擇弱密碼。 Azure AD 密碼保護通過檢測和阻止已知的弱密碼及其變體，以及選擇性地阻止特定于組織的其他弱術語來保護組織。

如需現行安全性成果的詳細資訊，請參閱 [Microsoft 安全性情資報告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="global-banned-password-list"></a>全域禁用密碼清單

Azure AD 標識保護團隊不斷分析 Azure AD 安全遙測資料，查找常用的弱密碼或洩露的密碼，或者更具體地說，通常用作弱密碼基礎的弱基本術語。 當發現此類弱項時，它們將添加到全域禁止密碼清單中。 全域禁止密碼清單的內容不基於任何外部資料源。 全域禁用密碼清單完全基於 Azure AD 安全遙測和分析的持續結果。

每當 Azure AD 中任何租戶中的任何使用者更改或重置新密碼時，在驗證密碼強度時，全域禁止密碼清單的當前版本將用作金鑰輸入。 此驗證為所有 Azure AD 客戶生成更強的密碼。

> [!NOTE]
> 網路犯罪分子在攻擊中也使用類似的策略。 因此，Microsoft 不會公開發布此清單的內容。

## <a name="custom-banned-password-list"></a>自訂禁用密碼清單

一些組織可能希望通過在 Microsoft 所稱的自訂禁止密碼清單中添加自己的自訂項來進一步提高安全性。 Microsoft 建議添加到此清單的術語主要側重于特定于組織的術語，例如：

- 品牌
- 產品名稱
- 地點（例如，如公司總部）
- 公司特定的內部條款
- 具有特定公司含義的縮寫。

將術語添加到自訂禁止密碼清單中後，在驗證密碼時，這些術語將與全域禁止密碼清單中的術語結合使用。

> [!NOTE]
> 自訂禁止密碼清單限制最多包含 1000 個術語。 它不是用於阻止非常大的密碼清單。 為了充分利用自訂禁止密碼清單的好處，Microsoft 建議您在向自訂禁止清單添加新術語之前，首先查看並瞭解密碼評估演算法（請參閱[如何計算密碼](concept-password-ban-bad.md#how-are-passwords-evaluated)）。 瞭解該演算法的工作原理將使您的企業能夠有效地檢測和阻止大量弱密碼及其變體。

例如：考慮一個名為"Contoso"的客戶，該客戶位於倫敦，生產名為"Widget"的產品。 對於這樣的客戶來說，試圖阻止這些術語的特定變體（例如：

- "康托索！1"
- "Contoso@London"
- "ContosoWidget"
- "!孔托索"
- "倫敦總部"
- ...諸如此類

相反，僅阻止關鍵基本術語會更加高效和安全：

- 「Contoso」
- "倫敦"
- "小工具"

然後，密碼驗證演算法將自動阻止上述弱變體和組合。

自訂禁用密碼清單和啟用內部部署 Active Directory 整合的能力，都可使用 Azure 入口網站來管理。

![修改身份驗證方法下的自訂禁止密碼清單](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密碼噴霧攻擊和協力廠商洩露的密碼清單

Azure AD 密碼保護的一個關鍵好處是可説明您防禦密碼噴射攻擊。 大多數密碼噴霧攻擊不會嘗試攻擊任何特定的個人帳戶多次，因為這種行為大大增加了通過帳戶鎖定或其他方式檢測的可能性。 因此，大多數密碼噴射攻擊都依賴于僅針對企業中的每個帳戶提交少量已知最弱的密碼。 此技術允許攻擊者快速搜尋容易洩露的帳戶，同時避免潛在的檢測閾值。

Azure AD 密碼保護旨在根據 Azure AD 所示的實際安全遙測資料，有效地阻止所有可能用於密碼噴射攻擊的已知弱密碼。  Microsoft 知道協力廠商網站枚舉了以前公開已知的安全性漏洞中洩露的數百萬個密碼。 協力廠商密碼驗證產品通常基於與數百萬個密碼的強力比較。 Microsoft 認為，鑒於密碼噴霧攻擊者使用的典型策略，此類技術不是提高整體密碼強度的最佳方式。

> [!NOTE]
> Microsoft 全球禁止密碼清單不基於任何協力廠商資料來源，包括洩露的密碼清單。

儘管與某些協力廠商批量清單相比，Microsoft 全球禁止清單很小，但其安全影響因來自實際密碼噴射攻擊的實際安全遙測資料以及 Microsoft 的事實而放大了密碼驗證演算法使用智慧模糊比對技術。 最終結果是，它將有效地檢測和阻止企業中數百萬個最常見的弱密碼。 選擇將特定于組織的術語添加到自訂禁止密碼清單中的客戶也受益于同一演算法。

有關基於密碼的安全問題的其他資訊可能會在[您的 Pa$$word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)上查看。這並不重要。

## <a name="on-premises-hybrid-scenarios"></a>內部部署混合式案例

保護僅限雲端帳戶有其效用，但許多組織仍會維護包含內部部署 Windows Server Active Directory 的混合式案例。 Azure AD 密碼保護的安全優勢也可能通過安裝本地代理擴展到 Windows 伺服器活動目錄環境。 現在，在 Active Directory 中更改或重置密碼的使用者和管理員必須遵守與僅雲使用者相同的密碼原則。

## <a name="how-are-passwords-evaluated"></a>如何評估密碼

每當使用者更改或重置其密碼時，都會根據全域和自訂禁止密碼清單中的術語清單（如果配置了後者）中的術語清單對其進行驗證，以檢查新密碼的強度和複雜性。

即使使用者的密碼包含禁用密碼，如果整體密碼還不夠強，仍然可能接受此密碼。 新設定的密碼會經歷下列步驟來評估其整體強度，以判斷應該接受或拒絕該密碼。

### <a name="step-1-normalization"></a>第 1 步：正常化

新密碼會先經歷正規化程序。 此技術允許將一小組被禁止的密碼映射到一組可能較弱的密碼。

正規化有兩個部分。  第一個部分，所有大寫字母會變更為小寫。  第二個部，會執行一般字元替代，例如：  

| 原始字母  | 替代的字母 |
| --- | --- |
| '0'  | 'o' |
| '1'  | 'l' |
| '$'  | 's' |
| '\@'  | 'a' |

範例：假設密碼 "blank" 遭到禁止，而使用者嘗試將其密碼變更為 “Bl@nK”。 即使 “Bl@nk” 並未明確遭到禁用，正規化程序會將這個密碼轉換為 “blank”，這禁用密碼。

### <a name="step-2-check-if-password-is-considered-banned"></a>第 2 步：檢查密碼是否被視為禁止

#### <a name="fuzzy-matching-behavior"></a>模糊比對行為

模糊比對在正規化密碼上用來識別是否包含在全域或自訂禁用密碼清單上找到的密碼。 比對程序是以一 (1) 次比較的編輯差距為基礎。  

範例：假設密碼 "abcdef" 遭到禁止，而使用者嘗試將其密碼變更為下列其中一項：

"abcdeg"（*最後一個字元從"f"更改為"g"）"abcdefg""（g"**追加到結束*）"abcde"（*尾隨'f'從末尾刪除）*

以上每個密碼並未明確符合禁用密碼 "abcdef"。 但是，由於每個示例在禁用術語"abcdef"的編輯距離 1 內，因此它們都被視為與"abcdef"的匹配。

#### <a name="substring-matching-on-specific-terms"></a>子字串比對 (在特定詞彙上)

子字串比對在正規化密碼上用來檢查使用者的名字和姓氏以及租用戶名稱 (請注意，在 Active Directory 網域控制站上驗證密碼時不會進行租用戶名稱比對)。

示例：假設我們有一個使用者 Pol，他想要將其密碼重設為"P0l123fb"。 正常化後，此密碼將成為"pol123fb"。 子字串匹配發現密碼包含使用者的名字"Pol"。 儘管"P0l123fb"沒有具體出現在禁止的密碼清單中，但密碼中找到了"Pol"的子字串匹配。 因此，此密碼會遭到拒絕。

#### <a name="score-calculation"></a>分數計算

下一個步驟是在使用者的正規化新密碼中找出所有禁用密碼執行個體。 然後：

1. 在使用者密碼中找到的每個禁用密碼都會得到一分。
2. 每個剩餘的唯一字元會得到一分。
3. 密碼必須至少為五 （5） 個點才能接受。

在後續兩個範例中，我們假設 Contoso 使用 Azure AD 密碼保護，且其自訂清單上有 "contoso"。 我們也假設 “blank” 位於全域清單上。

範例：使用者將其密碼變更為 “C0ntos0Blank12”

正規化之後，此密碼會變成 “contosoblank12”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] = [空白] [ 1] [ 2] = 4 點 由於此密碼低於 5 （5） 點，它將被拒絕。

範例：使用者將其密碼變更為 “ContoS0Bl@nkf9!”。

正規化之後，此密碼會變成 “contosoblankf9!”。 比對程序發現此密碼包含兩個禁用密碼：contoso 和 blank。 此密碼接著會被評分：

[contoso] = [空白] [ f] [ 9] [ ！] = 5 點 由於此密碼至少為五 （5） 點，因此接受。

   > [!IMPORTANT]
   > 請注意，根據進行中的安全性分析和研究，禁用密碼演算法及全域清單可能在 Azure 中隨時變更。 對於本地 DC 代理服務，更新後的演算法僅在重新安裝 DC 代理軟體後生效。

## <a name="license-requirements"></a>授權需求

|   | 使用全域禁用密碼清單的 Azure AD 密碼保護 | 使用自訂禁用密碼清單的 Azure AD 密碼保護|
| --- | --- | --- |
| 僅限雲端使用者 | Azure AD Free | Azure AD Premium P1 或 P2 |
| 從內部部署 Windows Server Active Directory 同步處理的使用者 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 未同步到 Azure 活動目錄的本地 Windows 伺服器活動目錄使用者還受益于基於同步使用者的現有許可的 Azure AD 密碼保護。

在 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到其他授權資訊 (包括成本)。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為會禁用的密碼時，便會看到下列錯誤訊息：

不幸的是，您的密碼包含單字、片語或模式，可輕易猜到您的密碼。 請使用不同的密碼再試一次。

## <a name="next-steps"></a>後續步驟

- [設定自訂禁用密碼清單](howto-password-ban-bad.md)
- [啟用內部部署的 Azure AD 密碼保護代理程式](howto-password-ban-bad-on-premises-deploy.md)
