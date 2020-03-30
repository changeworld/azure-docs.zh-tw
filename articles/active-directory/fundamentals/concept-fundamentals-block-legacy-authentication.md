---
title: 阻止 Azure AD 中的舊身份驗證協定
description: 瞭解組織如何以及為什麼阻止舊式身份驗證協定
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63271567e70955f6dfb0b10a5c882b6dce9545ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932485"
---
# <a name="blocking-legacy-authentication"></a>阻止舊版身份驗證
 
為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 舊身份驗證是一個術語，它是指由：

- 不使用現代身份驗證的舊 Office 用戶端（例如，Office 2010 用戶端）
- 使用舊郵件協定（如 IMAP/SMTP/POP3）的任何用戶端

今天，大多數妥協的登錄嘗試都來自舊版身份驗證。 舊身份驗證不支援多重要素驗證 （MFA）。 即使您在目錄中啟用了 MFA 策略，壞參與者也可以使用舊協定進行身份驗證並繞過 MFA。 保護帳戶免受舊協定惡意身份驗證請求攻擊的最佳方式是完全阻止這些嘗試。

## <a name="identify-legacy-authentication-use"></a>確定舊版身份驗證使用方式

在阻止目錄中的舊版身份驗證之前，首先需要瞭解使用者是否具有使用舊版身份驗證的應用，以及它如何影響整個目錄。 Azure AD 登錄日誌可用於瞭解是否使用舊版身份驗證。

1. 導航到 Azure 門戶> Azure 活動目錄>登錄。
1. 如果未通過按一下"用戶端應用"列>列來顯示用戶端應用列，請添加該列。
1. 按用戶端應用篩選>選中顯示的所有其他用戶端選項，然後按一下"應用"。
1. 按狀態篩選>成功，然後按一下"應用"。 
1. 如有必要，請使用"日期"篩選器擴展日期範圍。

篩選將僅顯示所選遺留身份驗證協定成功登錄的嘗試。 按一下每個單獨的登錄嘗試將顯示其他詳細資訊。 選擇單個資料行後，"基本資訊"選項卡下的"用戶端應用"列或用戶端應用欄位將指示使用了哪些舊身份驗證協定。 這些日誌將指示哪些使用者仍然依賴于舊版身份驗證，以及哪些應用程式使用舊協定來發出身份驗證請求。 對於未出現在這些日誌中且已確認不使用舊版身份驗證的使用者，實施條件訪問策略或啟用基線策略：僅針對這些使用者阻止舊版身份驗證。

## <a name="moving-away-from-legacy-authentication"></a>遠離舊版身份驗證 

更好地瞭解目錄中誰在使用舊版身份驗證以及哪些應用程式依賴于它，下一步是升級使用者以使用現代身份驗證。 現代身份驗證是一種身份管理方法，它提供了更安全的使用者身份驗證和授權。 如果您的目錄中有 MFA 策略，則現代身份驗證可確保在需要時提示使用者輸入 MFA。 它是舊式身份驗證協定的更安全替代方案。

本節概述了如何將環境更新為現代身份驗證。 在組織中啟用舊版身份驗證阻止策略之前，請通讀以下步驟。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>第 1 步：在目錄中啟用現代身份驗證

啟用現代身份驗證的第一步是確保您的目錄支援現代身份驗證。 預設情況下，對於在 2017 年 8 月 1 日或之後創建的目錄，將啟用現代身份驗證。 如果目錄是在此日期之前創建的，則需要使用以下步驟手動為目錄啟用現代身份驗證：

1. 檢查您的目錄是否已經支援現代身份驗證從 `Get-CsOAuthConfiguration`  [Skype業務線上PowerShell模組](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)運行。
1. 如果命令返回一個空 `OAuthServers` 屬性，則禁用現代身份驗證。 更新設置以啟用使用 `Set-CsOAuthConfiguration`的現代身份驗證。 如果您 `OAuthServers` 的財產包含條目，您就該走了。

在前進之前，請務必完成此步驟。 首先更改目錄配置至關重要，因為它們決定了所有 Office 用戶端都將使用哪種協定。 即使您使用的是支援現代身份驗證的 Office 用戶端，如果目錄上禁用了現代身份驗證，它們也會預設使用舊協定。

### <a name="step-2-office-applications"></a>第 2 步：辦公室申請

在目錄中啟用現代身份驗證後，可以通過為 Office 用戶端啟用現代身份驗證來開始更新應用程式。 預設情況下，Office 2016 或更高版本的用戶端支援現代身份驗證。 無需額外步驟。

如果您使用的是 Office 2013 Windows 用戶端或更高版本的用戶端，我們建議您升級到 Office 2016 或更高版本。 即使在完成目錄中啟用現代身份驗證的先前步驟後，較舊的 Office 應用程式仍將繼續使用舊版身份驗證協定。 如果您正在使用 Office 2013 用戶端，並且無法立即升級到 Office 2016 或更高版本，請按照以下步驟在 [Windows 設備上啟用 Office 2013 的現代身份驗證](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 為了説明在使用舊版身份驗證時保護您的帳戶，我們建議在目錄中使用強式密碼。 查看 [Azure AD 密碼保護](../authentication/concept-password-ban-bad.md) 以禁止整個目錄中的弱密碼。

Office 2010 不支援現代身份驗證。 您需要將 Office 2010 的任何使用者升級到較新版本的 Office。 我們建議升級到 Office 2016 或更高版本，因為它預設阻止舊版身份驗證。

如果您使用的是 MacOS，我們建議您升級到 Office 的 Mac 2016 或更高版本。 如果您使用的是本機郵件用戶端，則需要在所有設備上具有 MacOS 版本 10.14 或更高版本。

### <a name="step-3-exchange-and-sharepoint"></a>第 3 步：交換和共用點

對於基於 Windows 的 Outlook 用戶端，使用現代身份驗證，Exchange Online 也必須啟用現代身份驗證。 如果 Exchange Online 禁用了現代身份驗證，則支援現代身份驗證（Outlook 2013 或更高版本）的基於 Windows 的 Outlook 用戶端將使用基本驗證連接到 Exchange Online 郵箱。

為現代身份驗證預設值啟用共用點連線。 對於 2017 年 8 月 1 日之後創建的目錄，預設情況下在 Exchange Online 中啟用現代身份驗證。 但是，如果您以前禁用了現代身份驗證，或者是否使用了在此日期之前創建的目錄，請按照以下文章中的步驟在 Exchange Online [中啟用現代身份驗證](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>第 4 步：業務 Skype

為了防止 Skype 為業務而發出舊版身份驗證請求，有必要為 Skype 為業務連線啟用現代身份驗證。 對於 2017 年 8 月 1 日之後創建的目錄，預設情況下將啟用 Skype 業務身份驗證。

我們建議您過渡到 Microsoft Teams，後者預設支援現代身份驗證。 但是，如果您此時無法遷移，則需要為 Skype 為業務連線啟用現代身份驗證，以便業務用戶端的 Skype 開始使用現代身份驗證。 按照本文中的步驟 [，使用現代身份驗證支援的 Skype 業務拓撲](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)，為業務啟用現代 Skype 身份驗證。

除了為業務線上 Skype 啟用現代身份驗證外，我們建議在為 Skype 業務啟用現代身份驗證時為 Exchange Online 啟用現代身份驗證。 此過程將説明同步 Exchange Online 和 Skype 線上業務中現代身份驗證的狀態，並防止業務用戶端的 Skype 多次登錄提示。

### <a name="step-5-using-mobile-devices"></a>第 5 步：使用行動裝置

行動裝置上的應用程式也需要阻止舊版身份驗證。 我們建議使用移動 Outlook。 預設情況下，Outlook For Mobile 支援現代身份驗證，並將滿足其他 MFA 基線保護原則。

為了使用本機 iOS 郵件用戶端，您需要運行 iOS 版本 11.0 或更高版本，以確保郵件用戶端已更新以阻止舊版身份驗證。

### <a name="step-6-on-premises-clients"></a>第 6 步：本地用戶端

如果您是使用本地 Exchange Server 和本地業務 Skype 的混合客戶，則需要更新這兩種服務以啟用現代身份驗證。 在混合環境中使用現代身份驗證時，您仍在本地對使用者進行身份驗證。 授權他們訪問資源（檔或電子郵件）的故事會發生變化。

在開始本地啟用現代身份驗證之前，請確保滿足先決條件。 現在，您已準備好在本地啟用現代身份驗證。

可在以下文章中找到啟用現代身份驗證的步驟：

* [如何配置本地 Exchange 伺服器以使用混合現代身份驗證](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何使用現代身份驗證 （ADAL） 與 Skype 業務](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>後續步驟

- [如何配置本地 Exchange 伺服器以使用混合現代身份驗證](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [如何使用現代身份驗證 （ADAL） 與 Skype 業務](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [封鎖舊式驗證](../conditional-access/block-legacy-authentication.md)
