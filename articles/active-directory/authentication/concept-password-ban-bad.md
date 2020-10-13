---
title: Azure Active Directory 中的密碼保護
description: 瞭解如何使用 Azure Active Directory 密碼保護，以動態方式從您的環境中禁止弱式密碼
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fd367b337a0f26323411111ea7eb1120bf6d75d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965177"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>使用 Azure Active Directory 密碼保護來消除錯誤的密碼

許多安全性指引建議您不要在多個位置使用相同的密碼，使其變得複雜，並避免像 *Password123*這樣的簡單密碼。 您可以為使用者提供 [如何選擇密碼的指引](https://www.microsoft.com/research/publication/password-guidance)，但通常仍會使用弱式或不安全的密碼。 Azure AD 密碼保護會偵測並封鎖已知的弱式密碼及其變體，也可以封鎖您組織專屬的其他弱式詞彙。

使用 Azure AD 密碼保護時，會自動將預設全域禁用密碼清單套用至 Azure AD 租使用者中的所有使用者。 若要支援您自己的商務和安全性需求，您可以在自訂禁用密碼清單中定義專案。 當使用者變更或重設其密碼時，系統會檢查這些禁用的密碼清單，以強制使用強式密碼。

您應該使用 [Azure Multi-Factor Authentication](concept-mfa-howitworks.md)等其他功能，而不只是依賴 Azure AD 密碼保護所強制執行的強式密碼。 如需使用多層安全性來登入事件的詳細資訊，請參閱 [您的 Pa $ $word 並不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

> [!IMPORTANT]
> 本文說明 Azure AD 密碼保護如何運作的系統管理員。 如果您是已註冊自助式密碼重設的終端使用者，且需要取回您的帳戶，請移至 [https://aka.ms/sspr](https://aka.ms/sspr) 。
>
> 如果您的 IT 小組尚未啟用重設您密碼的功能，請與您的技術服務人員聯繫以取得其他協助。

## <a name="global-banned-password-list"></a>全域禁用密碼清單

Azure AD Identity Protection 團隊會持續分析 Azure AD 的安全性遙測資料，以尋找常用的弱式或遭盜用的密碼。 具體來說，分析會尋找通常用來做為弱式密碼基礎的基底詞彙。 當發現弱式字詞時，它們會新增至 *全域禁用密碼清單*。 全域禁用密碼清單的內容不是以任何外部資料源為基礎，而是根據 Azure AD 安全性遙測和分析的結果。

當 Azure AD 租使用者中的任何使用者的密碼變更或重設時，會使用目前版本的全域禁用密碼清單來驗證密碼的強度。 這項驗證檢查會為所有 Azure AD 客戶帶來更強的密碼。

全域禁用密碼清單會自動套用至 Azure AD 租使用者中的所有使用者。 沒有任何專案可以啟用或設定，也不能停用。 當使用者透過 Azure AD 變更或重設自己的密碼時，會將此全域禁用密碼清單套用至使用者。

> [!NOTE]
> 網路罪犯也會在其攻擊中使用類似的策略，以識別常見的弱式密碼和變化。 為了改善安全性，Microsoft 不會發佈全域禁用密碼清單的內容。

## <a name="custom-banned-password-list"></a>自訂禁用密碼清單

有些組織想要改善安全性，並在全域禁用密碼清單上新增自己的自訂。 若要新增您自己的專案，您可以使用 *自訂禁用密碼清單*。 新增至自訂禁用密碼清單的詞彙應著重于組織特定的詞彙，例如下列範例：

- 品牌名稱
- 產品名稱
- 位置，例如公司總部
- 公司特有的內部字詞
- 具有特定公司意義的縮寫

當條款新增至自訂禁用密碼清單時，它們會與全域禁用密碼清單中的條款結合。 然後，系統會針對這些禁用密碼清單的組合，驗證密碼變更或重設事件。

> [!NOTE]
> 自訂禁用密碼清單的上限為 1000 個字詞。 它不是針對封鎖極大型的密碼清單所設計。
>
> 若要充分利用自訂禁用密碼清單的優點，請先瞭解在將條款新增至自訂禁用清單之前， [如何評估密碼](#how-are-passwords-evaluated) 。 這種方法可讓您有效率地偵測和封鎖大量的弱式密碼及其變異數。

![修改 [驗證方法] 底下的自訂禁用密碼清單](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

讓我們來看看名為 *Contoso*的客戶。 公司以倫敦為基礎，並製作名為 *Widget*的產品。 在此範例客戶中，嘗試封鎖這些詞彙的特定變化（如下所示）會很浪費且較不安全：

- "Contoso！ 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso
- "LondonHQ"

相反地，只會封鎖主要的基底詞彙，例如下列範例，是更有效率且更安全的作法：

- 「Contoso」
- 倫敦
- 構件

密碼驗證演算法接著會自動封鎖弱式變體和組合。

若要開始使用自訂禁用密碼清單，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：設定自訂禁用密碼](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密碼噴灑攻擊和協力廠商遭入侵的密碼清單

Azure AD 密碼保護可協助您抵禦密碼噴灑攻擊。 大部分的密碼噴灑攻擊都不會嘗試多次攻擊指定的個別帳戶。 此行為會藉由帳戶鎖定或其他方式，來提高偵測的可能性。

相反地，大部分的密碼噴灑攻擊都只會針對企業中的每個帳戶提交少數已知的最弱密碼。 這項技術可讓攻擊者快速搜尋容易遭到入侵的帳戶，並避免可能的偵測臨界值。

Azure AD 密碼保護可有效率地封鎖可能在密碼噴灑攻擊中使用的所有已知弱式密碼。 這項保護是根據 Azure AD 的真實世界安全性遙測資料來建立全域禁用密碼清單。

有一些協力廠商網站會列舉在先前公開已知安全性缺口中遭盜用的數百萬個密碼。 協力廠商密碼驗證產品通常是根據這些上百萬的密碼進行暴力密碼破解比較。 不過，由於密碼噴灑攻擊者所使用的一般策略，這些技術並不是改善整個密碼強度的最佳方式。

> [!NOTE]
> 全域禁用密碼清單不是以任何協力廠商資料來源為基礎，包括遭盜用的密碼清單。

雖然全域禁用清單與某些協力廠商大量清單相比很小，但它是源自實際的密碼噴灑攻擊的真實世界安全性遙測。 這種方法可改善整體的安全性和有效性，而且密碼驗證演算法也會使用智慧型模糊比對技術。 因此，Azure AD 密碼保護可有效率地偵測和封鎖數百萬個最常見的弱式密碼，使其無法在您的企業中使用。

## <a name="on-premises-hybrid-scenarios"></a>內部部署混合式案例

許多組織都具有混合式身分識別模型，其中包含內部部署 Active Directory Domain Services (AD DS) 環境。 若要將 Azure AD 密碼保護的安全性優點擴充到 AD DS 環境中，您可以將元件安裝在內部部署伺服器上。 這些代理程式需要內部部署 AD DS 環境中的密碼變更事件，才能符合 Azure AD 中的相同密碼原則。

如需詳細資訊，請參閱 [強制執行 AD DS 的 Azure AD 密碼保護](concept-password-ban-bad-on-premises.md)。

## <a name="how-are-passwords-evaluated"></a>如何評估密碼

當使用者變更或重設其密碼時，會藉由針對全域和自訂禁用密碼清單中的合併詞彙清單進行驗證，來檢查新密碼的強度和複雜度。

即使使用者的密碼包含禁用密碼，如果整體密碼已足夠，則可能會接受密碼。 新設定的密碼會經歷下列步驟來評估其整體強度，以判斷是否應該接受或拒絕：

### <a name="step-1-normalization"></a>步驟1：正規化

新密碼會先經歷正規化程序。 這項技術可讓您將一小部分的禁用密碼對應到更大的一組可能的弱式密碼。

正規化有下列兩個部分：

* 所有大寫字母都會變更為小寫。
* 然後，會執行一般字元替代，如下列範例所示：

   | 原始字母 | 替代的字母 |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

請考慮下列範例：

* 密碼「空白」是禁用的。
* 使用者嘗試將其密碼變更為 " Bl@nK "。
* 雖然不會禁用 ""，但正規化程式會 Bl@nk 將這個密碼轉換成「空白」。
* 此密碼將會遭到拒絕。

### <a name="step-2-check-if-password-is-considered-banned"></a>步驟2：檢查密碼是否視為禁止

接著會檢查密碼是否有其他相符的行為，並產生分數。 最後一個分數會決定是否接受或拒絕密碼變更要求。

#### <a name="fuzzy-matching-behavior"></a>模糊比對行為

模糊比對在正規化密碼上用來識別是否包含在全域或自訂禁用密碼清單上找到的密碼。 比對程序是以一 (1) 次比較的編輯差距為基礎。

請考慮下列範例：

* 密碼 "abcdef" 已被禁用。
* 使用者嘗試將其密碼變更為下列其中一項：

   * ' abcdeg '- *最後一個字元從 ' f ' 變更為 ' g '*
   * ' abcdefg '- *' g ' 附加至結尾*
   * ' abcde '-尾端 *' f ' 已從結尾刪除*

* 上述每個密碼都不會特別符合禁用密碼 "abcdef"。

    不過，由於每個範例都是在禁用字詞 ' abcdef ' 的編輯距離1的範圍內，因此它們都會被視為與 "abcdef" 相符。
* 這些密碼將會遭到拒絕。

#### <a name="substring-matching-on-specific-terms"></a>子字串比對 (在特定詞彙上)

子字串比對用於標準化密碼，以檢查使用者的名字和姓氏，以及租使用者名稱。 在內部部署混合式案例的 AD DS 網域控制站上驗證密碼時，不會進行租使用者名稱比對。

> [!IMPORTANT]
> 子字串比對只會針對長度至少為四個字元的名稱和其他詞彙強制執行。

請考慮下列範例：

* 要將其密碼重設為「p0LL23fb」的使用者，名為「投票」。
* 正規化之後，此密碼會變成 "poll23fb"。
* 子字串比對會找出密碼包含使用者的名字「輪詢」。
* 雖然 "poll23fb" 不是特別在任何禁用的密碼清單上，但符合在密碼中找到「輪詢」的子字串。
* 此密碼將會遭到拒絕。

#### <a name="score-calculation"></a>分數計算

下一個步驟是在使用者的正規化新密碼中找出所有禁用密碼執行個體。 系統會根據下列準則來指派點數：

1. 在使用者密碼中找到的每個禁用密碼都會提供一個點。
1. 每個剩餘的唯一字元會得到一分。
1. 密碼必須至少有五個 (5) 點才會被接受。

在接下來的兩個範例案例中，Contoso 會使用 Azure AD 的密碼保護，並在其自訂禁用密碼清單上使用 "Contoso"。 讓我們也假設「空白」位於全域清單上。

在下列範例案例中，使用者將其密碼變更為 "C0ntos0Blank12"：

* 正規化之後，此密碼會變成 "contosoblank12"。
* 相符的進程會發現此密碼包含兩個禁用密碼： "contoso" 和 "空白"。
* 此密碼接著會獲得下列分數：

    *[contoso] + [空白] + [1] + [2] = 4 個點*

* 因為此密碼低於五 (5) 點，所以會被拒絕。

讓我們看看稍微不同的範例，以顯示密碼中的額外複雜性如何建立所需的點數以獲得接受。 在下列範例案例中，使用者將其密碼變更為 " ContoS0Bl@nkf9 ！"：

* 正規化之後，此密碼會變成 "contosoblankf9！"。
* 相符的進程會發現此密碼包含兩個禁用密碼： "contoso" 和 "空白"。
* 此密碼接著會獲得下列分數：

    *[contoso] + [空白] + [f] + [9] + [！] = 5 點*

* 因為此密碼至少有五個 (5) 點，所以會接受。

> [!IMPORTANT]
> 禁用密碼演算法以及全域禁用密碼清單，可以在 Azure 中的任何時間，根據持續性的安全性分析和研究來進行變更。
>
> 對於混合式案例中的內部部署 DC 代理程式服務，只有在重新安裝 DC 代理程式軟體之後，更新的演算法才會生效。

## <a name="what-do-users-see"></a>使用者看到的內容

當使用者嘗試將密碼重設為將被禁用的某個部分時，會顯示下列錯誤訊息：

*「可惜的是，您的密碼包含單字、片語或模式，可讓您輕鬆地猜到密碼。請使用不同的密碼再試一次。」*

## <a name="license-requirements"></a>授權需求

| 使用者 | Azure AD 具有全域禁用密碼清單的密碼保護 | 使用自訂禁用密碼清單 Azure AD 密碼保護|
|-------------------------------------------|---------------------------|---------------------------|
| 僅限雲端使用者                          | Azure AD Free             | Azure AD Premium P1 或 P2 |
| 從內部部署 AD DS 同步處理的使用者 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 未同步處理至 Azure AD 的內部部署 AD DS 使用者也可以根據已同步處理之使用者的現有授權，從 Azure AD 密碼保護中獲益。

在 [Azure Active Directory 價格網站](https://azure.microsoft.com/pricing/details/active-directory/)上可以找到其他授權資訊 (包括成本)。

## <a name="next-steps"></a>後續步驟

若要開始使用自訂禁用密碼清單，請完成下列教學課程：

> [!div class="nextstepaction"]
> [教學課程：設定自訂禁用密碼](tutorial-configure-custom-password-protection.md)

然後，您也可以 [啟用內部部署 Azure AD 密碼保護](howto-password-ban-bad-on-premises-deploy.md)。
