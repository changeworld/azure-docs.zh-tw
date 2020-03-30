---
title: 配置身份驗證會話管理 - Azure 活動目錄
description: 自訂 Azure AD 身份驗證會話配置，包括使用者登錄頻率和瀏覽器會話持久性。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263279"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>使用條件訪問配置身份驗證會話管理

在複雜的部署中，組織可能需要限制身份驗證會話。 某些方案可能包括：

* 來自非託管或共用設備的資源訪問
* 從外部網路訪問敏感資訊
* 高影響力使用者
* 關鍵商務應用程式

條件訪問控制項允許您創建針對組織內特定用例的策略，而不會影響所有使用者。

在深入瞭解如何配置策略之前，讓我們先檢查一下預設配置。

## <a name="user-sign-in-frequency"></a>使用者登錄頻率

登錄頻率定義在嘗試訪問資源時要求使用者重新登錄之前的時間段。

使用者登錄頻率的 Azure 活動目錄 （Azure AD） 預設配置是 90 天的滾動視窗。 向使用者請求憑據通常看起來是明智之舉，但可能會適得其反：經過培訓，無需考慮即可輸入憑據的使用者可能會無意中將其供應到惡意認證提示。

不要求使用者重新登錄聽起來可能令人震驚，實際上任何違反 IT 策略的行為都會撤銷會話。 一些示例包括（但不限於）密碼更改、不符合設備或帳戶禁用。 您還可以使用[PowerShell 顯式撤銷使用者的會話](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 Azure AD 預設配置歸結為"如果會話的安全狀態未更改，請不要要求使用者提供憑據"。

登錄頻率設置適用于已根據標準實現 OAUTH2 或 OIDC 協定的應用。 大多數適用于 Windows、Mac 和行動裝置的 Microsoft 本機應用（包括以下 Web 應用程式）都符合該設置。

- 字， Excel， 電源點線上
- 一個筆記線上
- Office.com
- O365 監管中心
- Exchange Online
- SharePoint 與 OneDrive
- 團隊 Web 用戶端
- Dynamics CRM Online
- Azure 入口網站

### <a name="user-sign-in-frequency-and-device-identities"></a>使用者登錄頻率和設備標識

如果已加入 Azure AD、混合 Azure AD 加入或 Azure AD 註冊設備，當使用者以對話模式解鎖其設備或標誌時，此事件也將滿足登錄頻率策略。 在以下 2 個示例中，使用者登錄頻率設置為 1 小時：

範例 1：

- 00：00，使用者登錄到其 Windows 10 Azure AD 加入設備，並開始處理存儲在 SharePoint Online 上的文檔。
- 使用者在其設備上繼續處理同一文檔一小時。
- 在 01：00 時，系統會提示使用者根據管理員配置的條件訪問策略中的登錄頻率要求再次登錄。

範例 2：

- 00：00，使用者登錄到其 Windows 10 Azure AD 加入設備，並開始處理存儲在 SharePoint Online 上的文檔。
- 在 00：30 時，使用者起身並休息鎖定設備。
- 在 00：45 時，使用者從中斷中返回並解鎖設備。
- 在 01：45 時，系統會提示使用者根據管理員配置的條件訪問策略中的登錄頻率要求再次登錄，因為上次登錄發生在 00：45。

## <a name="persistence-of-browsing-sessions"></a>流覽會話的持久性

持久性瀏覽器會話允許使用者在關閉和重新打開瀏覽器視窗後保持登錄狀態。

瀏覽器會話持久性的 Azure AD 預設值允許個人設備上的使用者通過顯示"保持登錄狀態？ 成功身份驗證後提示。 如果使用文章[AD FS 單點登錄設置](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)中的指南在 AD FS 中配置瀏覽器持久性，我們將遵守該策略並保留 Azure AD 會話。 您還可以配置租戶中的使用者是否看到"保持登錄狀態？ 使用"[自訂 Azure AD 登錄頁](../fundamentals/customize-branding.md)"中的指南，更改 Azure 門戶中的公司品牌窗格中的相應設置，提示提示。

## <a name="configuring-authentication-session-controls"></a>配置身份驗證會話控制項

條件訪問是 Azure AD 高級功能，需要高級許可證。 如果要瞭解有關條件訪問的更多內容，請參閱[Azure 活動目錄中的條件訪問是什麼？](overview.md#license-requirements)

> [!WARNING]
> 如果您當前在公共預覽版中使用[可配置的權杖存留期](../develop/active-directory-configurable-token-lifetimes.md)功能，請注意，我們不支援為同一使用者或應用組合創建兩個不同的策略：一個具有此功能，另一個具有可配置權杖存留期功能。 Microsoft 計畫在 2020 年 5 月 1 日停用可配置的權杖存留期功能，並將其替換為條件訪問身份驗證會話管理功能。  

### <a name="policy-1-sign-in-frequency-control"></a>策略 1：登錄頻率控制

1. 建立新原則
1. 選擇客戶環境的所有必需條件，包括目標雲應用。

   > [!NOTE]
   > 建議為關鍵的 Microsoft Office 應用（如"線上交換"和"共用點線上"）設置相等的身份驗證提示頻率，以獲得最佳使用者體驗。

1. 轉到**存取控制** > **會話**，然後按一下**登錄頻率**
1. 在第一個文字方塊中輸入日、小時所需的值
1. 從下拉清單中選擇**小時**或**天**的值
1. 保存策略

![為登錄頻率配置的條件訪問策略](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

在 Azure AD 上註冊的 Windows 設備登錄到設備被視為提示。 例如，如果 Office 應用的登錄頻率配置為 24 小時，則 Azure AD 註冊的 Windows 設備上的使用者將通過登錄到設備來滿足登錄頻率策略，並且在打開 Office 應用時不會再次提示。

如果為在同一瀏覽器會話中運行的不同 Web 應用配置了不同的登錄頻率，則最嚴格的策略將應用於這兩個應用，因為在同一瀏覽器會話中運行的所有應用共用單個會話權杖。

### <a name="policy-2-persistent-browser-session"></a>策略 2：持久瀏覽器會話

1. 建立新原則
1. 選擇所有必需的條件。

   > [!NOTE]
   > 請注意，此控制項需要選擇"所有雲應用"作為條件。 瀏覽器會話持久性由身份驗證會話權杖控制。 瀏覽器會話中的所有選項卡共用單個會話權杖，因此它們都必須共用持久性狀態。

1. 轉到**存取控制** > **會話**，然後按一下 **"持久瀏覽器會話"**
1. 從下拉清單中選擇值
1. 保存策略

![為持久瀏覽器配置的條件訪問策略](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 條件訪問中的持久瀏覽器會話配置將覆蓋"保持登錄狀態？ 如果配置了兩個策略，則在 Azure 門戶中為同一使用者設置公司品牌窗格。

## <a name="validation"></a>驗證

使用"What-If"工具根據配置策略的方式類比從使用者登錄到目標應用程式的登錄和其他條件。 身份驗證會話管理控制項顯示在工具的結果中。

![條件訪問如果工具結果](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>原則部署

若要確定您的原則會如預期般運作，建議的最佳做法是先測試，再推出到生產環境。 在理想情況下，可以使用測試租用戶來驗證您的新原則是否如預期般運作。 有關詳細資訊，請參閱[Azure 活動目錄中的條件訪問最佳做法](best-practices.md)一文。

## <a name="next-steps"></a>後續步驟

* 如果想知道如何配置條件訪問策略，請參閱[使用 Azure 活動目錄條件訪問的特定應用需要 MFA](app-based-mfa.md)的文章。
* 如果已準備好為環境配置條件訪問策略，請參閱[Azure 活動目錄中的條件訪問最佳做法](best-practices.md)一文。
