---
title: 封鎖舊版驗證-Azure Active Directory
description: 瞭解如何藉由使用 Azure AD 條件式存取來封鎖舊版驗證，以改善您的安全性狀態。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80476639"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>如何：使用條件式存取封鎖舊版驗證 Azure AD   

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 不過，舊版通訊協定不支援多重要素驗證（MFA）。 在許多環境中，MFA 都是防止身分識別遭竊的常用工具。 

Alex Weinert 是 Microsoft 的身分識別安全性總監，在他的2020年3月12日，文章中會張貼[新工具來封鎖貴組織中的舊版驗證](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#)，強調組織為何要封鎖舊版驗證，以及 Microsoft 提供的其他工具來完成這項工作：

> 若要讓 MFA 生效，您也必須封鎖舊版驗證。 這是因為舊版驗證通訊協定（例如 POP、SMTP、IMAP 和 MAPI）無法強制執行 MFA，因此敵人攻擊貴組織的偏好進入點 .。。
> 
>...來自 Azure Active Directory （Azure AD）流量分析的舊版驗證數位 stark：
> 
> - 超過99% 的密碼噴灑攻擊使用舊版驗證通訊協定
> - 超過97% 的認證裝攻擊使用舊版驗證
> - 在已停用舊版驗證體驗67% 以下的組織中 Azure AD 帳戶，而不是已啟用舊版驗證的威脅
>

如果您的環境已準備好封鎖舊版驗證以改善租使用者的保護，您可以使用條件式存取來達成此目標。 本文說明如何設定條件式存取原則，以封鎖您租使用者的舊版驗證。

## <a name="prerequisites"></a>先決條件

本文假設您已熟悉以下各項： 

- Azure AD 條件式存取的[基本概念](overview.md) 
- 在 Azure 入口網站中設定條件式存取原則的[最佳作法](best-practices.md)

## <a name="scenario-description"></a>案例描述

Azure AD 支援數個最常用的驗證和授權通訊協定，包括舊式驗證。 舊式驗證是指使用基本驗證的通訊協定。 一般而言，這些通訊協定無法強制執行任何類型的第二因素驗證。 舉例來說，以舊式驗證為基礎的應用程式包括：

- 舊版的 Microsoft Office 應用程式
- 使用 POP、IMAP、SMTP 等郵件通訊協定的應用程式

在現今的環境中，單一要素驗證 (例如，使用者名稱和密碼) 已不敷使用。 密碼的缺點在於容易被猜到，且一般人不太懂得如何選擇理想的密碼。 密碼也很容易遭受各種攻擊，例如網路釣魚和密碼噴濺。 要防範密碼威脅，最簡單的方式就是實作 MFA。 透過 MFA，即便攻擊者取得使用者的密碼，單靠密碼本身仍不足以成功進行驗證並存取資料。

如何防止使用舊式驗證的應用程式存取您租用戶的資源？ 建議您只使用條件式存取原則來封鎖它們。 如有必要，您可以僅允許特定使用者和特定網路位置使用以舊式驗證為基礎的應用程式。

完成第一個要素驗證之後，即會強制執行條件式存取原則。 因此，條件式存取不適合作為拒絕服務 (DoS) 攻擊之類情節的第一道防線，但是可以利用來自這些事件的訊號 (例如登入風險層級、要求位置等等) 來決定存取權。

## <a name="implementation"></a>實作

本節說明如何設定條件式存取原則，以封鎖舊版驗證。 

### <a name="legacy-authentication-protocols"></a>舊版驗證通訊協定

下列選項視為舊版驗證通訊協定

- 已驗證的 SMTP-POP 和 IMAP 用戶端用來傳送電子郵件訊息。
- 自動探索-供 Outlook 和 EAS 用戶端用來尋找並聯機到 Exchange Online 中的信箱。
- Exchange Online PowerShell-用來透過遠端 PowerShell 連線到 Exchange Online。 如果您封鎖 Exchange Online PowerShell 的基本驗證，您需要使用 Exchange Online PowerShell 模組來連接。 如需指示，請參閱[使用多重要素驗證連接到 Exchange Online PowerShell](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
- Exchange Web 服務（EWS）-Outlook、Outlook for Mac 及協力廠商應用程式所使用的程式設計介面。
- IMAP4-供 IMAP 電子郵件客戶程式使用。
- MAPI over HTTP （MAPI/HTTP）-由 Outlook 2010 和更新版本使用。
- 離線通訊錄（OAB）-Outlook 下載並使用的地址清單集合複本。
- Outlook Anywhere （RPC over HTTP）-由 Outlook 2016 和更早版本使用。
- Outlook 服務-適用于 Windows 10 的電子郵件和行事曆應用程式。
- POP3-由 POP 電子郵件客戶程式使用。
- 報表 Web 服務-用來在 Exchange Online 中取出報表資料。
- 其他用戶端-識別為利用舊版驗證的其他通訊協定。

如需這些驗證通訊協定和服務的詳細資訊，請參閱[Azure Active Directory 入口網站中的登入活動報告](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)。

### <a name="identify-legacy-authentication-use"></a>識別舊版驗證使用

您必須先瞭解您的使用者是否有使用舊版驗證的應用程式，以及它如何影響您的整體目錄，才可以在目錄中封鎖舊版驗證。 Azure AD 登入記錄可以用來瞭解您是否使用舊版驗證。

1. 流覽至**Azure 入口網站** > **Azure Active Directory** > 登**入**。
1. 新增 [用戶端應用程式] 欄（如果未顯示，請按一下 [資料**行** > ] [**用戶端應用程式**]）
1. **新增篩選** > **用戶端應用程式**> 選取所有舊版驗證通訊協定，然後按一下 [套用 **]。**

篩選只會顯示舊版驗證通訊協定所進行的登入嘗試。 按一下每個個別的登入嘗試，將會顯示其他詳細資料。 [**基本資訊**] 索引標籤下的 [**用戶端應用程式**] 欄位會指出所使用的舊版驗證通訊協定。

這些記錄會指出哪些使用者仍會根據傳統驗證，以及哪些應用程式使用舊版通訊協定來提出驗證要求。 對於不會出現在這些記錄中且已確認不會使用舊版驗證的使用者，請只為這些使用者執行條件式存取原則。

### <a name="block-legacy-authentication"></a>封鎖舊式驗證 

在條件式存取原則中，您可以設定與用來存取資源的用戶端應用程式相關聯的條件。 用戶端應用程式條件可讓您藉由選取 [行動**應用程式和桌面用戶端**] 底下的 [ **Exchange ActiveSync 用戶端**] 和 [**其他用戶端**]，將範圍縮小到應用程式。

![其他用戶端](./media/block-legacy-authentication/01.png)

若要封鎖這些應用程式的存取，您必須選取 [封鎖存取]****。

![封鎖存取](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>選取使用者和雲端應用程式

如果您想要為組織封鎖舊式驗證，您可能會認為藉由以下選取即可達成此目的：

- 所有使用者
- 所有雲端應用程式
- 封鎖存取

![指派](./media/block-legacy-authentication/03.png)

Azure 有一項安全性功能，可防止您建立這類原則，因為此設定違反條件式存取原則的[最佳作法](best-practices.md)。
 
![不支援原則設定](./media/block-legacy-authentication/04.png)

這項安全功能是必要的，因為*封鎖所有使用者和所有雲端應用程式*可能會使您的整個組織無法登入租用戶。 您至少須排除一個使用者，才能符合最低的最佳做法需求。 您也可以排除目錄角色。

![不支援原則設定](./media/block-legacy-authentication/05.png)

您可以從原則中排除一個使用者，以符合這項安全功能的需求。 在理想情況下，您應定義幾個 [Azure AD 中的緊急存取系統管理帳戶](../users-groups-roles/directory-emergency-access.md)，並將其從您的原則中排除。

當啟用您的原則以封鎖舊版驗證時，使用[僅限報告模式](concept-conditional-access-report-only.md)可讓您的組織有機會監視原則的影響。

## <a name="policy-deployment"></a>原則部署

在您將原則用於生產環境之前，請處理好：
 
- **服務帳戶** - 識別作為服務帳戶或由裝置 (例如會議室的電話) 使用的使用者帳戶。 請確定這些帳戶具有強式密碼，並將這些帳戶新增至排除的群組中。
- **登入報告** - 檢閱登入報告，並尋找**其他用戶端**流量。 識別最高使用量，並調查其使用原因。 流量通常由未使用新式驗證，或使用某些第三方郵件應用程式的舊版 Office 用戶端所產生。 請建立計劃以消除這些應用程式的使用，或者，如果影響不大，請通知使用者他們無法再使用這些應用程式。
 
如需詳細資訊，請參閱[如何部署新的原則？](best-practices.md#how-should-you-deploy-a-new-policy)。

## <a name="what-you-should-know"></a>您應該知道的事項

使用**其他用戶端**封鎖存取也會使用基本驗證來封鎖 Exchange Online PowerShell 和 Dynamics 365。

為**其他用戶端**設定原則會讓整個組織封鎖特定用戶端，例如 SPConnect。 之所以執行此封鎖，是因為舊版用戶端以非預期的方式進行驗證。 主要的 Office 應用程式 (例如，較舊的 Office 用戶端) 不會有這個問題。

原則最慢可能需要 24 小時才會生效。

您可以為**其他用戶端**條件選取所有可用的授與控制項;不過，終端使用者體驗一律是相同的封鎖存取。

如果您使用**其他用戶端**條件封鎖舊版驗證，您也可以設定裝置平臺和位置條件。 例如，如果您只想要封鎖行動裝置的舊版驗證，請選取下列項目來設定**裝置平台**條件：

- Android
- iOS
- Windows Phone

![不支援原則設定](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>後續步驟

- [使用條件式存取僅限報告模式判斷影響](howto-conditional-access-report-only.md)
- 如果您還不熟悉如何設定條件式存取原則，請參閱[使用 Azure Active Directory 條件式存取來要求特定應用程式的 MFA](app-based-mfa.md) ，以取得範例。
- 如需新式驗證支援的詳細資訊，請參閱[office 2013 和 office 2016 用戶端應用程式的新式驗證運作方式](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
