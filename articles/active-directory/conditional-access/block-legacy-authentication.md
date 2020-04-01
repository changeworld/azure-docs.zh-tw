---
title: 封鎖舊版身份認證 ─ Azure 的項目
description: 瞭解如何通過使用 Azure AD 條件訪問阻止舊版身份驗證來改進安全狀態。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957aa77e18ea8f910f258d1dc59de0d093b0eab6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476639"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>如何:使用條件存取封鎖對 Azure AD 的舊身份驗證   

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 但是,舊協定不支援多重身份驗證 (MFA)。 在許多環境中，MFA 都是防止身分識別遭竊的常用工具。 

Microsoft 身份安全總監 Alex Weinert 在 2020 年 3 月 12 日博客文章"[阻止組織中的舊版身份驗證的新工具](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#)"中強調為什麼組織應阻止舊版身份驗證以及 Microsoft 為完成此任務提供了哪些其他工具:

> 要使 MFA 有效,還需要阻止舊版身份驗證。 這是因為 POP、SMTP、IMAP 和 MAPI 等遺留身份驗證協定無法強制實施 MFA,使它們成為攻擊組織的對手的首選入口點...
> 
>...從 Azure 活動目錄 (Azure AD) 流量分析中遺留下來的身份驗證數位是明顯的:
> 
> - 超過 99% 的密碼噴霧攻擊使用舊版身份驗證協定
> - 超過 97% 的認證使用舊版身份驗證
> - 關閉舊版認證的組織中的 Azure AD 帳戶比啟用舊版身份驗證的帳戶的危害減少 67%
>

如果環境已準備好阻止舊版身份驗證以改進租戶的保護,則可以通過條件訪問實現此目標。 本文介紹如何配置阻止租戶舊版身份驗證的條件訪問策略。

## <a name="prerequisites"></a>Prerequisites

本文假設您已熟悉以下各項： 

- Azure AD 條件存取[的基本概念](overview.md) 
- 在 Azure 門戶中設定條件存取策略的[最佳做法](best-practices.md)

## <a name="scenario-description"></a>案例描述

Azure AD 支援數個最常用的驗證和授權通訊協定，包括舊式驗證。 舊式驗證是指使用基本驗證的通訊協定。 一般而言，這些通訊協定無法強制執行任何類型的第二因素驗證。 舉例來說，以舊式驗證為基礎的應用程式包括：

- 舊版的 Microsoft Office 應用程式
- 使用 POP、IMAP、SMTP 等郵件通訊協定的應用程式

在現今的環境中，單一要素驗證 (例如，使用者名稱和密碼) 已不敷使用。 密碼的缺點在於容易被猜到，且一般人不太懂得如何選擇理想的密碼。 密碼也很容易遭受各種攻擊，例如網路釣魚和密碼噴濺。 要防範密碼威脅，最簡單的方式就是實作 MFA。 透過 MFA，即便攻擊者取得使用者的密碼，單靠密碼本身仍不足以成功進行驗證並存取資料。

如何防止使用舊式驗證的應用程式存取您租用戶的資源？ 建議只是使用條件訪問策略來阻止它們。 如有必要，您可以僅允許特定使用者和特定網路位置使用以舊式驗證為基礎的應用程式。

完成第一個要素驗證之後，即會強制執行條件式存取原則。 因此，條件式存取不適合作為拒絕服務 (DoS) 攻擊之類情節的第一道防線，但是可以利用來自這些事件的訊號 (例如登入風險層級、要求位置等等) 來決定存取權。

## <a name="implementation"></a>實作

本節介紹如何配置條件訪問策略以阻止舊版身份驗證。 

### <a name="legacy-authentication-protocols"></a>舊版驗證通訊協定

以下選項被視為舊身份驗證協定

- 經過身份驗證的 SMTP - POP 和 IMAP 用戶端用於發送電子郵件。
- 自動發現 - Outlook 和 EAS 用戶端用於在「線上交換」中尋找和連接到郵箱。
- 交換線上電源外殼 - 用於與遠端 PowerShell 連接到線上交換。 如果阻止 Exchange 線上 PowerShell 的基本身份驗證,則需要使用 Exchange 在線 PowerShell 模組進行連接。 有關說明,請參閱[使用多重身份驗證連接到交換在線 PowerShell。](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)
- 交換 Web 服務 (EWS) - Outlook、Mac Outlook 和第三方應用使用的程式設計介面。
- IMAP4 - IMAP 電子郵件用戶端使用。
- 通過 HTTP(MAPI/HTTP) 的 MAPI - 由 Outlook 2010 及更高版本使用。
- 離線通訊錄 (OAB) - Outlook 下載和使用的通訊地址清單集合的副本。
- 隨時隨地的 Outlook(HTTP 的 RPC) - 由 Outlook 2016 及更早版本使用。
- Outlook 服務 - 由 Windows 10 的郵件和行事曆應用使用。
- POP3 - 由 POP 電子郵件用戶端使用。
- 報告 Web 服務 - 用於在「連線交換」中檢索報表數據。
- 其他用戶端 - 標識為使用舊版身份驗證的其他協定。

有關這些身份驗證協定和服務的詳細資訊,請參閱[Azure 活動目錄門戶中的登入活動報告](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)。

### <a name="identify-legacy-authentication-use"></a>確定舊版身份驗證使用方式

在阻止目錄中的舊版身份驗證之前,首先需要瞭解使用者是否具有使用舊版身份驗證的應用,以及它如何影響整個目錄。 Azure AD 登錄日誌可用於瞭解是否使用舊版身份驗證。

1. 瀏覽到**Azure 門戶** > **Azure 的目錄** > **登入**。
1. 沒有按下 **「客戶端套用列」** > 顯示客戶端的應用程式,則新增此**欄**位 。
1. **添加篩選器** > **用戶端應用**>选择所有旧身份验证协议,然後按一下「**應用**」。

篩選將僅顯示由舊身份驗證協議進行的登錄嘗試。 按一下每個單獨的登錄嘗試將顯示其他詳細資訊。 **"基本資訊**"選項卡下的 **"用戶端應用**"欄位將指示使用了哪些舊版身份驗證協定。

這些日誌將指示哪些使用者仍然依賴於舊版身份驗證,以及哪些應用程式使用舊協議來發出身份驗證請求。 對於未出現在這些日誌中且已確認不使用舊版身份驗證的使用者,僅為這些使用者實施條件訪問策略。

### <a name="block-legacy-authentication"></a>封鎖舊式驗證 

在條件訪問策略中,可以設置與用於訪問資源的用戶端應用綁定的條件。 用戶端應用條件使您能夠透過在**行動應用程式和桌面用戶端**下選擇**Exchange ActiveSync 用戶端****和其他用戶端**,使用舊版身份驗證將範圍縮小到應用範圍。

![其他用戶端](./media/block-legacy-authentication/01.png)

若要封鎖這些應用程式的存取，您必須選取 [封鎖存取]****。

![封鎖存取](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>選取使用者和雲端應用程式

如果您想要為組織封鎖舊式驗證，您可能會認為藉由以下選取即可達成此目的：

- 所有使用者
- 所有雲端應用程式
- 封鎖存取

![指派](./media/block-legacy-authentication/03.png)

Azure 具有安全性功能,可阻止您建立這樣的策略,因為此設定違反了條件存取策略的[最佳做法](best-practices.md)。
 
![不支援原則設定](./media/block-legacy-authentication/04.png)

這項安全功能是必要的，因為*封鎖所有使用者和所有雲端應用程式*可能會使您的整個組織無法登入租用戶。 您至少須排除一個使用者，才能符合最低的最佳做法需求。 您也可以排除目錄角色。

![不支援原則設定](./media/block-legacy-authentication/05.png)

您可以從原則中排除一個使用者，以符合這項安全功能的需求。 在理想情況下，您應定義幾個 [Azure AD 中的緊急存取系統管理帳戶](../users-groups-roles/directory-emergency-access.md)，並將其從您的原則中排除。

啟用策略阻止舊版身份驗證時,使用[僅報告模式](concept-conditional-access-report-only.md)為您的組織提供了一個監視策略影響的機會。

## <a name="policy-deployment"></a>原則部署

在您將原則用於生產環境之前，請處理好：
 
- **服務帳戶** - 識別作為服務帳戶或由裝置 (例如會議室的電話) 使用的使用者帳戶。 請確定這些帳戶具有強式密碼，並將這些帳戶新增至排除的群組中。
- **登入報告** - 檢閱登入報告，並尋找**其他用戶端**流量。 識別最高使用量，並調查其使用原因。 流量通常由未使用新式驗證，或使用某些第三方郵件應用程式的舊版 Office 用戶端所產生。 請建立計劃以消除這些應用程式的使用，或者，如果影響不大，請通知使用者他們無法再使用這些應用程式。
 
如需詳細資訊，請參閱[如何部署新的原則？](best-practices.md#how-should-you-deploy-a-new-policy)。

## <a name="what-you-should-know"></a>您應該知道的事情

使用**其他客戶端**封鎖存取並阻止使用基本 au.

為**其他用戶端**設定原則會讓整個組織封鎖特定用戶端，例如 SPConnect。 之所以執行此封鎖，是因為舊版用戶端以非預期的方式進行驗證。 主要的 Office 應用程式 (例如，較舊的 Office 用戶端) 不會有這個問題。

原則最慢可能需要 24 小時才會生效。

可以為其他用戶端條件選擇所有可用的授予控制項;但是,您可以選擇**其他用戶端**條件。但是, 最終用戶體驗始終相同 - 阻止存取。

如果使用**其他用戶端**條件阻止舊版身份驗證,也可以設置設備平臺和位置條件。 例如，如果您只想要封鎖行動裝置的舊版驗證，請選取下列項目來設定**裝置平台**條件：

- Android
- iOS
- Windows Phone

![不支援原則設定](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>後續步驟

- [使用僅條件存取報告模式確定影響](howto-conditional-access-report-only.md)
- 如果您還不熟悉配置條件存取策略,請參閱[使用 Azure 活動目錄條件存取的特定應用需要 MFA](app-based-mfa.md)作為範例。
- 有關現代身份驗證支援的詳細資訊,請參閱[Office 2013 和 Office 2016 客戶端應用的現代身份驗證的工作原理](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
