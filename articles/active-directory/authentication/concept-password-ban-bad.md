---
title: Azure Active Directory 中的密碼保護
description: 瞭解如何使用 Azure Active Directory 密碼保護，從您的環境動態地禁止弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68419c33286457a770a9988f1f00cc0b5e1f91bc
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235293"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>使用 Azure Active Directory 密碼保護排除不正確的密碼

有很多安全性指引，建議您不要在多個位置使用相同的密碼，讓它變得複雜，以及避免簡單的密碼，例如 *Password123*。 您可以為使用者提供 [如何選擇密碼的指引](https://www.microsoft.com/research/publication/password-guidance)，但通常還是會使用弱式或不安全的密碼。 Azure AD 密碼保護會偵測並封鎖已知的弱式密碼及其變體，同時也會封鎖貴組織特定的其他弱式詞彙。

使用 Azure AD 密碼保護時，預設的全域禁用密碼清單會自動套用至 Azure AD 租使用者中的所有使用者。 若要支援您自己的商務和安全性需求，您可以在自訂的禁用密碼清單中定義專案。 當使用者變更或重設其密碼時，系統會檢查這些禁用的密碼清單，以強制使用強式密碼。

您應該使用 [Azure 多重要素驗證](concept-mfa-howitworks.md)之類的其他功能，而不只是依賴 Azure AD 密碼保護所強制執行的強式密碼。 如需針對登入事件使用多層安全性的詳細資訊，請參閱 [您的 Pa $ $word 並不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

> [!IMPORTANT]
> 本概念性文章會向系統管理員說明 Azure AD 密碼保護的運作方式。 如果您是已註冊自助式密碼重設的使用者，而且需要取回您的帳戶，請移至 [https://aka.ms/sspr](https://aka.ms/sspr) 。
>
> 如果您的 IT 小組尚未啟用重設您密碼的功能，請與您的技術服務人員聯繫以取得其他協助。

## <a name="global-banned-password-list"></a>全域禁用密碼清單

Azure AD Identity Protection 小組會持續分析 Azure AD 的安全性遙測資料，尋找經常使用的弱式或洩露密碼。 具體而言，分析會尋找通常用來做為弱式密碼基礎的基本詞彙。 找到弱式詞彙時，會將它們新增至 *全域禁用密碼清單*。 全域禁用密碼清單的內容不是以任何外部資料源為基礎，而是在 Azure AD 安全性遙測和分析的結果上。

當 Azure AD 租使用者中的任何使用者變更或重設密碼時，會使用目前版本的全域禁用密碼清單來驗證密碼的強度。 這項驗證檢查會為所有 Azure AD 客戶帶來更強的密碼。

全域禁用密碼清單會自動套用至 Azure AD 租使用者中的所有使用者。 不需要啟用或設定，且無法停用。 當使用者透過 Azure AD 變更或重設自己的密碼時，就會套用此全域禁用密碼清單。

> [!NOTE]
> 網路罪犯也會在其攻擊中使用類似的策略，以識別常見的弱式密碼和變化。 為了提升安全性，Microsoft 不會發佈全域禁用密碼清單的內容。

## <a name="custom-banned-password-list"></a>自訂禁用密碼清單

有些組織想要改善安全性，並在全域禁用密碼清單之上新增自己的自訂專案。 若要新增您自己的專案，您可以使用 *自訂的禁用密碼清單*。 新增至自訂禁用密碼清單的條款應該著重于組織特定的詞彙，例如下列範例：

- 品牌名稱
- 產品名稱
- 位置，例如公司總部
- 公司特有的內部字詞
- 具有特定公司意義的縮寫

當條款新增至自訂禁用密碼清單時，會與全域禁用密碼清單中的條款結合。 然後，密碼變更或重設事件會根據這些禁用密碼清單的組合來進行驗證。

> [!NOTE]
> 自訂禁用密碼清單的上限為 1000 個字詞。 它不是專門用來封鎖非常大的密碼清單。
>
> 若要充分利用自訂禁用密碼清單的優點，請先瞭解 [如何評估密碼](#how-are-passwords-evaluated) ，再將詞彙新增至自訂禁用清單。 這種方法可讓您有效地偵測並封鎖大量弱式密碼及其變體。

![在 [驗證方法] 下修改自訂禁用密碼清單](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

讓我們來看一個名為 *Contoso*的客戶。 該公司以倫敦為基礎，並製作名為 *Widget*的產品。 針對此範例客戶，嘗試封鎖這些詞彙的特定變化（例如下列各項）會造成浪費，而且較不安全：

- "Contoso！ 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso
- "LondonHQ"

相反地，您可以更有效率且安全地僅封鎖金鑰基底詞彙，如下列範例所示：

- 「Contoso」
- 位於
- 機械

密碼驗證演算法接著會自動封鎖弱式變體和組合。

若要開始使用自訂的禁用密碼清單，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：設定自訂的禁用密碼](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密碼噴灑攻擊和協力廠商遭到入侵的密碼清單

Azure AD 密碼保護可協助您防禦密碼噴灑攻擊。 大部分的密碼噴灑攻擊都不會嘗試多次攻擊任何指定的個別帳戶。 此行為會透過帳戶鎖定或其他方式增加偵測的可能性。

相反地，大部分的密碼噴灑攻擊都會針對企業中的每個帳戶只提交少數已知的最弱密碼。 這項技術可讓攻擊者快速搜尋容易遭到入侵的帳戶，並避免可能的偵測閾值。

Azure AD 密碼保護會有效率地封鎖可能在密碼噴灑攻擊中使用的所有已知弱式密碼。 這種保護是根據 Azure AD 的真實世界安全性遙測資料，建立全域禁用密碼清單。

有一些協力廠商網站列舉了先前公開已知的安全性缺口中已遭盜用的數百萬個密碼。 協力廠商密碼驗證產品通常是根據這些數百萬個密碼的暴力密碼破解。 不過，根據密碼噴灑攻擊者所使用的一般策略，這些技巧並不是改善整體密碼強度的最佳方式。

> [!NOTE]
> 全域禁用密碼清單不是以任何協力廠商資料來源為基礎，包括遭到入侵的密碼清單。

雖然全域禁用清單與一些協力廠商大容量清單相較之下是很小的，但它是源自于實際密碼噴灑攻擊的真實世界安全性遙測。 這種方法可改善整體安全性和效率，而密碼驗證演算法也會使用智慧型模糊比對技術。 因此，Azure AD 密碼保護會有效率地偵測並封鎖數百萬個最常見的弱式密碼，使其無法在您的企業中使用。

## <a name="on-premises-hybrid-scenarios"></a>內部部署混合式案例

許多組織都有混合式身分識別模型，其中包含內部部署 Active Directory Domain Services (AD DS) 環境中。 若要將 Azure AD 密碼保護的安全性優點擴充至您的 AD DS 環境，您可以在內部部署伺服器上安裝元件。 這些代理程式需要內部部署 AD DS 環境中的密碼變更事件，以符合 Azure AD 中的相同密碼原則。

如需詳細資訊，請參閱 [強制執行 AD DS 的 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)。

## <a name="how-are-passwords-evaluated"></a>如何評估密碼

當使用者變更或重設其密碼時，系統會根據全域和自訂禁用密碼清單中的合併詞彙清單來驗證新密碼，以檢查其強度和複雜度。

即使使用者的密碼包含禁用的密碼，如果整體密碼已夠強，則可能會接受密碼。 新設定的密碼會經歷下列步驟來評估其整體強度，以判斷是否應該接受或拒絕：

### <a name="step-1-normalization"></a>步驟1：正規化

新密碼會先經歷正規化程序。 這項技術可讓一小部分的禁用密碼對應到較大的一組可能弱式密碼。

正規化有下列兩個部分：

* 所有大寫字母都會變更為小寫。
* 然後，會執行一般字元替換，如下列範例所示：

   | 原始字母 | 替代的字母 |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

請考慮下列範例：

* 「空白」密碼已被禁用。
* 使用者嘗試將其密碼變更為 " Bl@nK "。
* 雖然不會禁止「」，但正規化程式會 Bl@nk 將此密碼轉換成「空白」。
* 此密碼會遭到拒絕。

### <a name="step-2-check-if-password-is-considered-banned"></a>步驟2：檢查是否將密碼視為已禁用

接著會檢查密碼是否有其他比對行為，並產生分數。 此最終分數會決定是否已接受或拒絕密碼變更要求。

#### <a name="fuzzy-matching-behavior"></a>模糊比對行為

模糊比對在正規化密碼上用來識別是否包含在全域或自訂禁用密碼清單上找到的密碼。 比對程序是以一 (1) 次比較的編輯差距為基礎。

請考慮下列範例：

* 已禁止 "abcdef" 密碼。
* 使用者嘗試將其密碼變更為下列其中一項：

   * ' abcdeg '- *最後一個字元已從 ' f ' 變更為 ' g '*
   * ' abcdefg '- *' g ' 附加至結尾*
   * ' abcde '- *尾端 ' f ' 已從結尾刪除*

* 上述每個密碼都不會特別符合禁用的密碼 "abcdef"。

    不過，由於每個範例都是在禁用字詞 ' abcdef ' 的1個編輯距離中，因此它們全都視為符合「abcdef」。
* 這些密碼會遭到拒絕。

#### <a name="substring-matching-on-specific-terms"></a>子字串比對 (在特定詞彙上)

子字串比對會用於正規化的密碼，以檢查使用者的名字和姓氏以及租使用者名稱。 在內部部署混合式案例的 AD DS 網域控制站上驗證密碼時，不會完成租使用者名稱比對。

> [!IMPORTANT]
> 子字串比對只會針對名稱和其他詞彙強制執行，且長度至少為四個字元。

請考慮下列範例：

* 名為輪詢的使用者，想要將其密碼重設為 "p0LL23fb"。
* 正規化之後，此密碼會變成 "poll23fb"。
* 子字串比對會發現密碼包含使用者的名字「輪詢」。
* 雖然 "poll23fb" 不是特別在禁用密碼清單上，但符合的子字串會在密碼中找到「輪詢」。
* 此密碼會遭到拒絕。

#### <a name="score-calculation"></a>分數計算

下一個步驟是在使用者的正規化新密碼中找出所有禁用密碼執行個體。 系統會根據下列準則來指派點：

1. 在使用者密碼中找到的每個禁用密碼都會提供一個點。
1. 每個剩餘的唯一字元會得到一分。
1. 密碼至少必須有五個 (5) 點才會被接受。

在接下來的兩個範例案例中，Contoso 會使用 Azure AD 密碼保護，並在其自訂禁用密碼清單中包含 "Contoso"。 我們也假設「空白」位於全域清單上。

在下列範例案例中，使用者將其密碼變更為 "C0ntos0Blank12"：

* 正規化之後，此密碼會變成 "contosoblank12"。
* 比對程式發現此密碼包含兩個禁用的密碼： "contoso" 和 "空白"。
* 此密碼接著會提供下列分數：

    *[contoso] + [空白] + [1] + [2] = 4 點*

* 因為此密碼在五個 (5) 點，所以會遭到拒絕。

讓我們看一下稍微不同的範例，示範密碼中額外的複雜性如何建立要接受的必要點數。 在下列範例案例中，使用者將其密碼變更為 " ContoS0Bl@nkf9 ！"：

* 正規化之後，此密碼會變成 "contosoblankf9！"。
* 比對程式發現此密碼包含兩個禁用的密碼： "contoso" 和 "空白"。
* 此密碼接著會提供下列分數：

    *[contoso] + [空白] + [f] + [9] + [！] = 5 點*

* 因為此密碼至少是五個 (5) 點，所以已接受。

> [!IMPORTANT]
> 「禁用密碼」演算法以及全域禁用密碼清單，可以隨時在 Azure 中根據持續的安全性分析和研究來進行變更。
>
> 針對混合式案例中的內部部署 DC 代理程式服務，只有在重新安裝 DC 代理程式軟體後，更新的演算法才會生效。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為會被禁用的內容時，會顯示下列錯誤訊息：

*「可惜的是，您的密碼包含單字、片語或模式，可讓您的密碼輕鬆地猜到。請使用不同的密碼再試一次。」*

## <a name="license-requirements"></a>授權需求

| 使用者 | 使用全域禁用密碼清單 Azure AD 密碼保護 | 使用自訂禁用密碼清單 Azure AD 密碼保護|
|-------------------------------------------|---------------------------|---------------------------|
| 僅限雲端使用者                          | Azure AD Free             | Azure AD Premium P1 或 P2 |
| 從內部部署 AD DS 同步處理的使用者 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 內部部署 AD DS 未同步處理至 Azure AD 的使用者也可以根據已同步處理之使用者的現有授權，受益于 Azure AD 密碼保護。

在 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到其他授權資訊 (包括成本)。

## <a name="next-steps"></a>後續步驟

若要開始使用自訂的禁用密碼清單，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：設定自訂的禁用密碼](tutorial-configure-custom-password-protection.md)

然後，您也可以 [啟用內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)。
