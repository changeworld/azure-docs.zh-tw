---
title: 封鎖舊式驗證 - Azure Active Directory
description: 了解如何藉由使用 Azure AD 條件式存取封鎖舊式驗證來改善安全性狀態。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1485c2abd24022dbfa6476e3c5a530413b9cb4f2
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233792"
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

本文假設您已熟悉 Azure AD 條件式存取的 [基本概念](overview.md) 。

## <a name="scenario-description"></a>案例描述

Azure AD 支援數個最常用的驗證和授權通訊協定，包括舊式驗證。 舊式驗證是指使用基本驗證的通訊協定。 一般而言，這些通訊協定無法強制執行任何類型的第二因素驗證。 舉例來說，以舊式驗證為基礎的應用程式包括：

- 舊版的 Microsoft Office 應用程式
- 使用 POP、IMAP、SMTP 等郵件通訊協定的應用程式

在現今的環境中，單一要素驗證 (例如，使用者名稱和密碼) 已不敷使用。 密碼的缺點在於容易被猜到，且一般人不太懂得如何選擇理想的密碼。 密碼也很容易遭受各種攻擊，例如網路釣魚和密碼噴濺。 保護密碼威脅的最簡單方式之一，就是執行多因素驗證 (MFA) 。 透過 MFA，即便攻擊者取得使用者的密碼，單靠密碼本身仍不足以成功進行驗證並存取資料。

如何防止使用舊式驗證的應用程式存取您租用戶的資源？ 建議您使用條件式存取原則直接加以封鎖。 如有必要，您可以僅允許特定使用者和特定網路位置使用以舊式驗證為基礎的應用程式。

完成第一個要素驗證之後，即會強制執行條件式存取原則。 因此，條件式存取不適合作為拒絕服務 (DoS) 攻擊之類情節的第一道防線，但是可以利用來自這些事件的訊號 (例如登入風險層級、要求位置等等) 來決定存取權。

## <a name="implementation"></a>實作

本節說明如何使用條件式存取原則來封鎖舊式驗證。 

### <a name="legacy-authentication-protocols"></a>舊版驗證通訊協定

下列項目視為舊式驗證通訊協定

- 已驗證的 SMTP-由 POP 和 IMAP 用戶端用來傳送電子郵件訊息。
- 自動探索 - 由 Outlook 與 EAS 用戶端用於尋找及連線至 Exchange Online 中的信箱。
- Exchange ActiveSync (EAS) -用來連線到 Exchange Online 中的信箱。
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
1. **新增篩選**  > **用戶端應用程式** > 選取所有舊版驗證通訊協定。 選取 [篩選] 對話方塊以外的範圍，以套用您的選取專案並關閉對話方塊。

篩選後，只會顯示您使用舊式驗證通訊協定的登入嘗試。 按一下各項登入嘗試，即會顯示更多詳細資料。 [基本資訊] 索引標籤下方的 [用戶端應用程式] 欄位，會標明使用的舊式驗證通訊協定。

這些記錄會顯示哪些使用者仍依賴舊式驗證，以及哪些應用程式目前會使用舊式通訊協定提出驗證要求。 實施條件式存取原則時，僅限未出現在上述記錄中，且已確認並未使用舊式驗證的使用者。

## <a name="block-legacy-authentication"></a>封鎖舊式驗證 

有兩種方式可以使用條件式存取原則來封鎖舊版驗證。

- [直接封鎖舊版驗證](#directly-blocking-legacy-authentication)
- [間接封鎖舊版驗證](#indirectly-blocking-legacy-authentication)
 
### <a name="directly-blocking-legacy-authentication"></a>直接封鎖舊版驗證

若要封鎖整個組織的舊版驗證，最簡單的方式就是設定適用于舊版驗證用戶端的條件式存取原則，並封鎖存取。 將使用者和應用程式指派給原則時，請務必排除仍需要使用舊版驗證登入的使用者和服務帳戶。 選取 [ **Exchange ActiveSync 用戶端** ] 和 [ **其他用戶端** ] 來設定用戶端應用程式條件。 若要封鎖這些用戶端應用程式的存取，請設定存取控制以封鎖存取。

![設定為封鎖舊版驗證的用戶端應用程式條件](./media/block-legacy-authentication/client-apps-condition-configured-yes.png)

### <a name="indirectly-blocking-legacy-authentication"></a>間接封鎖舊版驗證

即使您的組織尚未準備好封鎖整個組織的舊版驗證，您仍應確定使用舊版驗證的登入不會略過需要授與控制的原則，例如需要多重要素驗證或符合規範/混合式 Azure AD 加入的裝置。 在驗證期間，舊版驗證用戶端不支援將 MFA、裝置合規性或聯結狀態資訊傳送至 Azure AD。 因此，將具有授與控制權的原則套用至所有用戶端應用程式，以便封鎖無法滿足 grant 控制項的舊版驗證型登入。 在2020年8月的用戶端應用程式條件正式推出後，新建立的條件式存取原則預設會套用至所有用戶端應用程式。

![用戶端應用程式條件預設設定](./media/block-legacy-authentication/client-apps-condition-configured-no.png)

## <a name="what-you-should-know"></a>您應該知道的事情

使用 [其他用戶端] 封鎖存取時，也會封鎖使用基本驗證的 Exchange Online PowerShell 和 Dynamics 365。

為 **其他用戶端** 設定原則會讓整個組織封鎖特定用戶端，例如 SPConnect。 之所以執行此封鎖，是因為舊版用戶端以非預期的方式進行驗證。 主要的 Office 應用程式 (例如，較舊的 Office 用戶端) 不會有這個問題。

原則最慢可能需要 24 小時才會生效。

您可以選取  [其他用戶端] 條件所有可用的授與控制項，但使用者體驗一律相同，存取均會遭到封鎖。

## <a name="next-steps"></a>後續步驟

- [使用條件式存取的僅限報告模式來判斷影響](howto-conditional-access-insights-reporting.md)
- 如果您還不太了解如何設定條件式存取原則，請參閱[利用 Azure Active Directory 條件式存取來取得特定應用程式的 MFA](../authentication/tutorial-enable-azure-mfa.md)，以取得範例。
- 如需新式驗證支援的詳細資訊，請參閱 [Office 2013 和 Office 2016 用戶端應用程式的新式驗證運作方式](/office365/enterprise/modern-auth-for-office-2013-and-2016) 
- [如何使用 Microsoft 365 設定多功能裝置或應用程式以傳送電子郵件](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-office-3)
