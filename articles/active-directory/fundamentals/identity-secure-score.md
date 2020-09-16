---
title: 什麼是身分識別安全分數？ -Azure Active Directory
description: 您可以如何使用身分識別安全分數來改善目錄的安全性狀態
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c319489fe4c884cd5de48ac2d3e47e7beb3026f
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705481"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>什麼是 Azure Active Directory 中的身分識別安全分數？

您的 Azure AD 租用戶有多安全？ 如果您不知道如何回答這個問題，本文將說明身分識別安全分數如何協助您監視及改善身分識別安全性狀態。

## <a name="what-is-an-identity-secure-score"></a>什麼是身分識別安全分數？

身分識別安全分數是介於1到223之間的數位，可作為與 Microsoft 的最佳作法建議的安全性相關指標。 身分識別安全分數中的每個改進動作都會針對您的特定設定量身打造。  

![安全分數](./media/identity-secure-score/identity-secure-score-overview.png)

此分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

您可以在身分識別安全分數儀表板上存取分數和相關資訊。 在此儀表板上，您可以找到：

- 您的身分識別安全分數
- 比較圖，顯示您的身分識別安全分數與相同產業和類似大小的其他租使用者之間的比較
- 顯示身分識別安全分數如何隨時間改變的趨勢圖表
- 可能的改進清單

藉由遵循改善動作，您可以：

- 改善您的安全性狀態和分數
- 利用您的組織提供的功能作為身分識別投資的一部分

## <a name="how-do-i-get-my-secure-score"></a>如何取得安全分數？

身分識別安全分數適用于所有版本的 Azure AD。 組織可以從**Azure 入口網站**  >  **Azure Active Directory**  >  **安全性**身分  >  **識別安全分數**來存取其身分識別安全分數。

## <a name="how-does-it-work"></a>運作方式

每隔 48 小時，Azure 就會查看安全性組態，並將您的設定與建議的最佳做法進行比較。 根據這項評估的結果，系統會為您的目錄計算新的分數。 您的安全性設定可能未完全符合最佳做法指導方針，而且只會部分符合改進動作。 在這些情況下，您只會獲得可供控制項使用的最大分數部分。

系統會根據 Azure AD 組態測量每項建議。 如果您使用協力廠商產品來啟用最佳作法建議，您可以在改進動作的設定中指出此設定。 您也可以選擇設定建議，以在不適用您的環境時予以忽略。 遭到忽略的建議便不會列入分數的計算中。

![略過或標示協力廠商所涵蓋的動作](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>其如何提供協助？

此安全分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

## <a name="what-you-should-know"></a>您應該知道的事項

### <a name="who-can-use-the-identity-secure-score"></a>誰可以使用身分識別安全分數？

身分識別安全分數可供下列角色使用：

- 全域系統管理員
- 安全性系統管理員
- 安全性讀取者

### <a name="how-are-controls-scored"></a>如何評分控制項？

您可以透過兩種方式來計分控制項。 有些會以二進位方式進行評分-如果您已根據我們的建議設定功能或設定，則會得到100% 的分數。 其他分數會以總設定的百分比計算。 例如，如果您使用 MFA 保護所有使用者，且您只有5個以上的使用者受到保護，則您會收到30點的改進建議：您會獲得大約2點的部分分數 (5 100 個受保護/100 total * 30 max pt = 2 pt 部分分數) 。

### <a name="what-does-not-scored-mean"></a>[不計分] 是什麼意思？

標示為 [不計分] 的動作是可在組織中執行，但由於未連接到工具而不會列入計分的動作。 因此，您仍可改善安全性，但目前不會因為這些動作而獲得分數。

### <a name="how-often-is-my-score-updated"></a>分數多久會更新一次？

分數會每天計算一次 (大約在太平洋標準時間上午 1:00)。 如果您變更了測量的動作，分數會在隔天自動更新。 最久需要 48 小時的時間，分數中才會反映出該項變更。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>分數變了。 要如何找出原因？

前往 [Microsoft 365 安全性中心](https://security.microsoft.com/)，您可以在其中找到完整的 Microsoft 安全分數。 您可以透過查看 [歷程記錄] 索引標籤上的深入變更，輕鬆地查看安全分數的所有變更。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全分數是否會測量我遇到的風險？

不會。 安全分數不會表達您可能被入侵的可能性的絕對量值。 它只表示您所採用的功能有多大程度可以抵銷遭到入侵的風險。 沒有任何服務可以保證您將不會被入侵，而且安全分數不應以任何方式解讀為保證。

### <a name="how-should-i-interpret-my-score"></a>如何解讀分數？

設定建議的安全性功能或執行安全性相關工作 (例如，讀取報告) 就會獲得分數。 某些動作就算完成一部分也會獲得分數，例如為使用者啟用 Multi-Factor Authentication (MFA)。 您的安全分數直接代表您所使用的 Microsoft 安全性服務。 請記住，安全性必須與可用性保持平衡。 所有安全性控制都會對使用者造成影響。 對使用者影響較低的控制應該就不會對使用者的日常作業造成什麼影響。

若要查看您的分數歷程記錄，請前往 [Microsoft 365 安全性中心](https://security.microsoft.com/) ，並查看您的整體 Microsoft 安全分數。 您可以查看整體安全分數的變更，請按一下 [視圖歷程記錄]。 選擇特定日期，即可查看當天啟用了哪些控制，以及這些控制各自獲得的分數。

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>身分識別安全分數與 Microsoft 365 安全分數有何關聯？

[Microsoft 安全分數](/office365/securitycompliance/microsoft-secure-score)包含五個不同的控制和分數類別：

- 身分識別
- 資料
- 裝置
- 基礎結構
- 應用程式

身分識別安全分數表示 Microsoft 安全分數的身分識別部分。 此重迭表示您對於身分識別安全分數和 Microsoft 中的身分識別分數的建議相同。

## <a name="next-steps"></a>後續步驟

[深入瞭解 Microsoft 安全分數](/office365/securitycompliance/microsoft-secure-score)