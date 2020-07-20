---
title: 封鎖舊式驗證 - Azure Active Directory
description: 了解如何藉由使用 Azure AD 條件式存取封鎖舊式驗證來改善安全性狀態。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd66bc742d0832cba5d6f302bfe30c85e2d82716
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253336"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>如何：使用條件式存取封鎖對 Azure AD 的舊式驗證   

為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 不過，舊式通訊協定並不支援多重要素驗證 (MFA)。 在許多環境中，MFA 都是防止身分識別遭竊的常用工具。 

Microsoft 身分識別安全性總監 Alex Weinert 在 2020 年 3 月 12 日發表了一篇部落格文章：[封鎖組織中舊式驗證的新工具](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) (英文)。文中強調組織為何應封鎖舊式驗證，並重點介紹 Microsoft 為了達成這項工作所提供的額外工具：

> 若要讓 MFA 生效，也需要封鎖舊式驗證。 這是因為如 POP、SMTP、IMAP 和 MAPI 等舊式驗證通訊協定無法強制執行 MFA，所以敵人會偏好將這類通訊協定當成攻擊貴組織的進入點。
> 
>……根據 Azure Active Directory (Azure AD) 流量分析所得的舊式驗證數目，更凸顯了此現象：
> 
> - 超過 99% 的密碼噴濺攻擊使用舊式驗證通訊協定
> - 超過 97% 的認證填充攻擊使用舊式驗證
> - 在停用舊式驗證的組織中，遭到入侵的 Azure AD 帳戶比啟用舊式驗證的組織少 67%
>

如果您的環境已準備就緒，可封鎖舊式驗證以改善您租用戶的防護能力，您可以使用條件式存取來達成此目標。 本文說明如何設定條件式存取原則來為您的租用戶封鎖舊式驗證。

## <a name="prerequisites"></a>Prerequisites

本文假設您已熟悉以下各項： 

- Azure AD 條件式存取的[基本概念](overview.md) 
- 在 Azure 入口網站中設定條件式存取原則的[最佳做法](best-practices.md)

## <a name="scenario-description"></a>案例描述

Azure AD 支援數個最常用的驗證和授權通訊協定，包括舊式驗證。 舊式驗證是指使用基本驗證的通訊協定。 一般而言，這些通訊協定無法強制執行任何類型的第二因素驗證。 舉例來說，以舊式驗證為基礎的應用程式包括：

- 舊版的 Microsoft Office 應用程式
- 使用 POP、IMAP、SMTP 等郵件通訊協定的應用程式

在現今的環境中，單一要素驗證 (例如，使用者名稱和密碼) 已不敷使用。 密碼的缺點在於容易被猜到，且一般人不太懂得如何選擇理想的密碼。 密碼也很容易遭受各種攻擊，例如網路釣魚和密碼噴濺。 要防範密碼威脅，最簡單的方式就是實作 MFA。 透過 MFA，即便攻擊者取得使用者的密碼，單靠密碼本身仍不足以成功進行驗證並存取資料。

如何防止使用舊式驗證的應用程式存取您租用戶的資源？ 建議您使用條件式存取原則直接加以封鎖。 如有必要，您可以僅允許特定使用者和特定網路位置使用以舊式驗證為基礎的應用程式。

完成第一個要素驗證之後，即會強制執行條件式存取原則。 因此，條件式存取不適合作為拒絕服務 (DoS) 攻擊之類情節的第一道防線，但是可以利用來自這些事件的訊號 (例如登入風險層級、要求位置等等) 來決定存取權。

## <a name="implementation"></a>實作

本節說明如何使用條件式存取原則來封鎖舊式驗證。 

### <a name="legacy-authentication-protocols"></a>舊版驗證通訊協定

下列項目視為舊式驗證通訊協定

- 經過驗證的 SMTP - 由 POP 與 IMAP 用戶端用於傳送電子郵件。
- 自動探索 - 由 Outlook 與 EAS 用戶端用於尋找及連線至 Exchange Online 中的信箱。
- Exchange ActiveSync （EAS）-用來連接到 Exchange Online 中的信箱。
- Exchange Online PowerShell - 用於透過 PowerShell 連線至 Exchange Online。 如果您封鎖 Exchange Online PowerShell 的基本驗證，則需使用 Exchange Online PowerShell 模組來連線。 如需相關說明，請參閱[使用多重要素驗證連線至 Exchange Online PowerShell](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
- Exchange Web 服務 (EWS) - Outlook、Mac 版 Outlook 及協力廠商應用程式使用的程式設計介面。
- IMAP4 - IMAP 電子郵件用戶端所使用。
- MAPI over HTTP (MAPI/HTTP) - Outlook 2010 與以後版本所使用。
- 離線通訊錄 (OAB) - Outlook 所下載與使用的一份地址清單集合。
- Outlook Anywhere (RPC over HTTP) - Outlook 2016 及之前版本所使用。
- Outlook Service - Windows 10 版郵件與行事曆應用程式所使用。
- POP3 - POP 電子郵件用戶端所使用。
- Reporting Web Services - 用於擷取 Exchange Online 中的報告資料。
- 其他用戶端 - 其他視為使用舊式驗證的通訊協定。

如需上述驗證通訊協定和服務的詳細資訊，請參閱 [Azure Active Directory 入口網站中的登入活動報告](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)。

### <a name="identify-legacy-authentication-use"></a>辨識是否使用舊式驗證

首先需要先了解您的使用者是否有應用程式在使用舊式驗證，以及舊式驗證會對您的整體目錄產生何種影響，如此才能在目錄中封鎖舊式驗證。 可以使用 Azure AD 登入記錄來確認是否在使用舊式驗證。

1. 瀏覽至 [Azure 入口網站]  >  [Azure Active Directory]  >  [登入]。
1. 如果未顯示 [用戶端應用程式] 欄，請按一下 [欄]  >  [用戶端應用程式] 來新增此欄。
1. [新增篩選]  >  [用戶端應用程式] > 選取所有的舊式驗證通訊協定，然按一下 [套用]。

篩選後，只會顯示您使用舊式驗證通訊協定的登入嘗試。 按一下各項登入嘗試，即會顯示更多詳細資料。 [基本資訊] 索引標籤下方的 [用戶端應用程式] 欄位，會標明使用的舊式驗證通訊協定。

這些記錄會顯示哪些使用者仍依賴舊式驗證，以及哪些應用程式目前會使用舊式通訊協定提出驗證要求。 實施條件式存取原則時，僅限未出現在上述記錄中，且已確認並未使用舊式驗證的使用者。

### <a name="block-legacy-authentication"></a>封鎖舊式驗證 

在條件式存取原則中，您可以設定與用來存取資源的用戶端應用程式繫結的條件。 用戶端應用程式條件可讓您將範圍縮小至使用舊式驗證的應用程式，做法是選取 [Exchange ActiveSync 用戶端]，再選取 [行動裝置 App 及桌面用戶端] 下方的 [其他用戶端]。

![其他用戶端](./media/block-legacy-authentication/01.png)

若要封鎖這些應用程式的存取，您必須選取 [封鎖存取]。

![封鎖存取](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>選取使用者和雲端應用程式

如果您想要為組織封鎖舊式驗證，您可能會認為藉由以下選取即可達成此目的：

- 所有使用者
- 所有雲端應用程式
- 封鎖存取

![指派](./media/block-legacy-authentication/03.png)

Azure 有一項安全功能會防止您建立此類原則，因為這種設定違反條件式存取原則的[最佳做法](best-practices.md)。
 
![不支援原則設定](./media/block-legacy-authentication/04.png)

這項安全功能是必要的，因為*封鎖所有使用者和所有雲端應用程式*可能會使您的整個組織無法登入租用戶。 您至少須排除一個使用者，才能符合最低的最佳做法需求。 您也可以排除目錄角色。

![不支援原則設定](./media/block-legacy-authentication/05.png)

您可以從原則中排除一個使用者，以符合這項安全功能的需求。 在理想情況下，您應定義幾個 [Azure AD 中的緊急存取系統管理帳戶](../users-groups-roles/directory-emergency-access.md)，並將其從您的原則中排除。

啟用原則以封鎖舊式驗證時，使用[僅限報告模式](concept-conditional-access-report-only.md)，就能讓您的組織有機會監視原則的影響。

## <a name="policy-deployment"></a>原則部署

在您將原則用於生產環境之前，請處理好：
 
- **服務帳戶** - 識別作為服務帳戶或由裝置 (例如會議室的電話) 使用的使用者帳戶。 請確定這些帳戶具有強式密碼，並將這些帳戶新增至排除的群組中。
- **登入報告** - 檢閱登入報告，並尋找**其他用戶端**流量。 識別最高使用量，並調查其使用原因。 流量通常由未使用新式驗證，或使用某些第三方郵件應用程式的舊版 Office 用戶端所產生。 請建立計劃以消除這些應用程式的使用，或者，如果影響不大，請通知使用者他們無法再使用這些應用程式。
 
如需詳細資訊，請參閱[如何部署新的原則？](best-practices.md#how-should-you-deploy-a-new-policy)。

## <a name="what-you-should-know"></a>您應該知道的事情

使用 [其他用戶端] 封鎖存取時，也會封鎖使用基本驗證的 Exchange Online PowerShell 和 Dynamics 365。

為**其他用戶端**設定原則會讓整個組織封鎖特定用戶端，例如 SPConnect。 之所以執行此封鎖，是因為舊版用戶端以非預期的方式進行驗證。 主要的 Office 應用程式 (例如，較舊的 Office 用戶端) 不會有這個問題。

原則最慢可能需要 24 小時才會生效。

您可以選取  [其他用戶端] 條件所有可用的授與控制項，但使用者體驗一律相同，存取均會遭到封鎖。

如果您使用 [其他用戶端] 條件來封鎖舊式驗證，也可以設定裝置平台和位置條件。 例如，如果您只想要封鎖行動裝置的舊版驗證，請選取下列項目來設定**裝置平台**條件：

- Android
- iOS
- Windows Phone

![不支援原則設定](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>後續步驟

- [使用條件式存取的僅限報告模式來判斷影響](howto-conditional-access-report-only.md)
- 如果您還不太了解如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](app-based-mfa.md)，以取得範例。
- 如需新式驗證支援的詳細資訊，請參閱 [Office 2013 和 Office 2016 用戶端應用程式的新式驗證運作方式](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [如何將多功能裝置或應用程式設定為使用 Office 365 和 Microsoft 365 傳送電子郵件](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
