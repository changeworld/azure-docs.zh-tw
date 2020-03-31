---
title: 什麼是身份安全分數？ - Azure 活動目錄
description: 如何使用標識安全分數來改進目錄的安全狀態
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
ms.openlocfilehash: 0f75dea2cffbe710bf2778ceab5eacc91ffcca9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523098"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>什麼是 Azure Active Directory 中的身分識別安全分數？

您的 Azure AD 租用戶有多安全？ 如果您不知道如何回答這個問題，本文將介紹身份安全分數如何説明您監控和改進身份安全狀態。

## <a name="what-is-an-identity-secure-score"></a>什麼是身分識別安全分數？

身份安全分數介於 1 和 223 之間，作為您與 Microsoft 安全最佳實踐建議的一致性的指示器。 身份安全分數中的每個改進操作都根據您的特定配置量身定制。  

![安全分數](./media/identity-secure-score/identity-secure-score-overview.png)

此分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

您可以在身分識別安全分數儀表板上存取分數和相關資訊。 在此儀表板上，您可以找到：

- 您的身份安全分數
- 顯示您的身份安全分數與同行業中其他租戶和類似大小的比較圖
- 顯示您的身份安全分數隨時間變化的趨勢圖
- 可能改進的清單

藉由遵循改善動作，您可以：

- 改善您的安全姿勢和分數
- 利用作為身份投資的一部分您的組織可用的功能

## <a name="how-do-i-get-my-secure-score"></a>如何取得安全分數？

標識安全分數在所有版本的 Azure AD 中都可用。 組織可以從**Azure 門戶** > **Azure 活動目錄** > **安全** > **標識安全分數**訪問其標識安全分數。

## <a name="how-does-it-work"></a>運作方式

每隔 48 小時，Azure 就會查看安全性組態，並將您的設定與建議的最佳做法進行比較。 根據此評估的結果，為您的目錄計算一個新分數。 您的安全配置可能未與最佳實踐指南完全一致，並且改進操作僅部分滿足。 在這些情況下，您只能獲得控制項可用的最大分數的一部分。

系統會根據 Azure AD 組態測量每項建議。 如果使用協力廠商產品來啟用最佳實踐建議，則可以在改進操作的設置中指示此配置。 如果建議不適用於您的環境，您還可以選擇設置要忽略的建議。 遭到忽略的建議便不會列入分數的計算中。

![忽略或標記協力廠商涵蓋的操作](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>其如何提供協助？

此安全分數可協助您：

- 客觀地測量身分識別安全性狀態
- 規劃如何改善身分識別安全性
- 檢閱改善是否成功

## <a name="what-you-should-know"></a>您應該知道的事情

### <a name="who-can-use-the-identity-secure-score"></a>誰可以使用身分識別安全分數？

身分識別安全分數可供下列角色使用：

- 全域管理員
- 安全性系統管理員
- 安全性讀取者

### <a name="how-are-controls-scored"></a>如何對控制項進行評分？

控制項可通過兩種方式評分。 有些是以二進位方式評分的 - 如果您根據我們的建議配置了功能或設置，您將獲得 100% 的分數。 其他分數按總配置的百分比計算。 例如，如果改進建議聲明，如果您使用 MFA 保護所有使用者，並且僅保護了 100 個使用者中的 5 個，您將獲得大約 2 分的部分分數（5 個受保護 / 100 個總計 = 30 個最大點 = 2 點部分分數），您將獲得 30 分。

### <a name="what-does-not-scored-mean"></a>[不計分] 是什麼意思？

標示為 [不計分] 的動作是可在組織中執行，但由於未連接到工具而不會列入計分的動作。 因此，您仍可改善安全性，但目前不會因為這些動作而獲得分數。

### <a name="how-often-is-my-score-updated"></a>分數多久會更新一次？

分數會每天計算一次 (大約在太平洋標準時間上午 1:00)。 如果您變更了測量的動作，分數會在隔天自動更新。 最久需要 48 小時的時間，分數中才會反映出該項變更。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>分數變了。 要如何找出原因？

前往[微軟365安全中心](https://security.microsoft.com/)，在那裡你會發現你的微軟安全得分。 通過查看歷史記錄選項卡上的深入更改，可以輕鬆查看安全分數的所有更改。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>安全分數是否衡量我被攻破的風險？

不會。 安全分數並不表示您被突破的可能性的絕對衡量。 它只表示您所採用的功能有多大程度可以抵銷遭到入侵的風險。 任何服務都無法保證您不會被違反，安全分數不應以任何方式被解釋為保證。

### <a name="how-should-i-interpret-my-score"></a>如何解讀分數？

設定建議的安全性功能或執行安全性相關工作 (例如，讀取報告) 就會獲得分數。 某些動作就算完成一部分也會獲得分數，例如為使用者啟用 Multi-Factor Authentication (MFA)。 您的安全分數直接代表您使用的 Microsoft 安全服務。 請記住，安全性必須與可用性相平衡。 所有安全性控制都會對使用者造成影響。 對使用者影響較低的控制應該就不會對使用者的日常作業造成什麼影響。

要查看您的分數歷史記錄，請前往 Microsoft [365 安全中心](https://security.microsoft.com/)並查看您的整體 Microsoft 安全分數。 您可以查看對整體安全分數的更改，按一下"查看歷史記錄"。 選擇特定日期，即可查看當天啟用了哪些控制，以及這些控制各自獲得的分數。

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>身分識別安全分數與 Office 365 安全分數有何關聯？

[Microsoft 安全分數](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)包含五個不同的控制和分數類別：

- 身分識別
- 資料
- 裝置
- 基礎結構
- 應用程式

標識安全分數表示 Microsoft 安全分數的標識部分。 此重疊意味著您對 Microsoft 中標識安全分數和身份分數的建議是相同的。

## <a name="next-steps"></a>後續步驟

[瞭解有關微軟安全分數的更多](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
