---
title: 封鎖 Azure AD 中的舊版驗證通訊協定
description: 瞭解組織應該封鎖舊版驗證通訊協定的方式和原因
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1e17421b27082a079d078f53c38d0c942db7ae71
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200550"
---
# <a name="blocking-legacy-authentication"></a>封鎖舊版驗證
 
為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 「舊版驗證」一詞，是指由以下幾者提出的驗證要求：

- 未使用新式驗證的舊版 Office 用戶端 (例如 Office 2010 用戶端) 
- 任何使用舊版郵件通訊協定的用戶端，例如 IMAP/SMTP/POP3

現今，所有危害的登入嘗試都是來自舊版驗證。 舊版驗證不支援 MFA)  (多重要素驗證。 即使您的目錄已啟用 MFA 原則，不良的執行者仍可使用舊版通訊協定進行驗證，並略過 MFA。 保護您的帳戶免于舊版通訊協定所提出之惡意驗證要求的最佳方式，就是完全封鎖這些嘗試。

## <a name="identify-legacy-authentication-use"></a>辨識是否使用舊式驗證

首先需要先了解您的使用者是否有應用程式在使用舊式驗證，以及舊式驗證會對您的整體目錄產生何種影響，如此才能在目錄中封鎖舊式驗證。 可以使用 Azure AD 登入記錄來確認是否在使用舊式驗證。

1. 瀏覽至 [Azure 入口網站]  >  [Azure Active Directory]  >  [登入]。
1. 新增 [**用戶端應用程式**] 欄（如果未顯示，請按一下 [資料 **行**] [   >  **用戶端應用程式**]）
1. 依**用戶端應用程式**篩選 > 檢查呈現的所有**舊版驗證用戶端**選項。
1. 依**狀態**  >  **成功**篩選。 
1. 如有必要，請使用**日期**篩選器來展開您的日期範圍。

篩選只會顯示選取的舊版驗證通訊協定所進行的成功登入嘗試。 按一下各項登入嘗試，即會顯示更多詳細資料。 選取個別資料列之後，[基本資訊] 索引標籤下的 [用戶端應用程式] 資料行或 [用戶端應用程式] 欄位會指出使用了哪一個舊版驗證通訊協定。 這些記錄會顯示哪些使用者仍依賴舊式驗證，以及哪些應用程式目前會使用舊式通訊協定提出驗證要求。 對於不會出現在這些記錄中且已確認不會使用舊版驗證的使用者，請執行條件式存取原則，或啟用基準原則：僅針對這些使用者封鎖舊版驗證。

## <a name="moving-away-from-legacy-authentication"></a>離開舊版驗證 

當您更瞭解誰在您的目錄中使用舊版驗證，以及哪些應用程式依賴它時，下一步就是將您的使用者升級為使用新式驗證。 新式驗證是身分識別管理的一種方法，可提供更安全的使用者驗證和授權。 如果您的目錄已有 MFA 原則，新式驗證可確保使用者在需要時收到 MFA 的提示。 這是舊版驗證通訊協定更安全的替代方式。

本節提供如何將您的環境更新為新式驗證的逐步概述。 在您的組織中啟用舊版驗證封鎖原則之前，請先閱讀下列步驟。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步驟1：在您的目錄中啟用新式驗證

啟用新式驗證的第一個步驟，是確定您的目錄支援新式驗證。 2017年8月1日當天或之後所建立的目錄，預設會啟用新式驗證。 如果您的目錄是在此日期之前建立的，您必須使用下列步驟，為您的目錄手動啟用新式驗證：

1.  `Get-CsOAuthConfiguration`   從 [商務用 Skype Online PowerShell 模組](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)執行，以查看您的目錄是否已支援新式驗證。
1. 如果您的命令傳回空的  `OAuthServers`   屬性，則會停用新式驗證。 使用來更新設定，以啟用新式驗證  `Set-CsOAuthConfiguration` 。 如果您  `OAuthServers`   的屬性包含專案，您就可以開始使用了。

請務必先完成此步驟，再繼續進行。 請務必先變更目錄設定，因為這些設定會規定所有 Office 用戶端將使用的通訊協定。 即使您使用的是支援新式驗證的 Office 用戶端，如果您的目錄上已停用新式驗證，則會預設為使用舊版通訊協定。

### <a name="step-2-office-applications"></a>步驟2： Office 應用程式

在您的目錄中啟用新式驗證之後，您就可以藉由啟用 Office 用戶端的新式驗證來開始更新應用程式。 根據預設，Office 2016 或更新版本的用戶端支援新式驗證。 不需要額外的步驟。

如果您使用的是 Office 2013 Windows 用戶端或更舊版本，建議您升級到 Office 2016 或更新版本。 即使在完成在目錄中啟用新式驗證的先前步驟之後，舊版 Office 應用程式仍會繼續使用舊版驗證通訊協定。 如果您使用的是 Office 2013 用戶端，而且無法立即升級為 Office 2016 或更新版本，請遵循下列文章中的步驟， [在 Windows 裝置上啟用 Office 2013 的新式驗證](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 若要在使用傳統驗證時協助保護您的帳戶，建議您在目錄中使用強式密碼。 查看 [Azure AD 密碼保護](../authentication/concept-password-ban-bad.md)   ，以禁止在您的目錄中進行弱式密碼。

Office 2010 不支援新式驗證。 您必須將任何使用 Office 2010 的使用者升級至較新版本的 Office。 我們建議您升級至 Office 2016 或更新版本，因為它預設會封鎖舊版驗證。

如果您使用 macOS，建議您升級至適用于 Mac 2016 或更新版本的 Office。 如果您使用原生郵件用戶端，則在所有裝置上都必須有 macOS 10.14 版或更新版本。

### <a name="step-3-exchange-and-sharepoint"></a>步驟3： Exchange 和 SharePoint

對於以 Windows 為基礎的 Outlook 用戶端若要使用新式驗證，Exchange Online 也必須啟用新式驗證。 如果已停用 Exchange Online 的新式驗證，支援新式驗證的 Windows 型 Outlook 用戶端 (Outlook 2013 或更新版本) 會使用基本驗證來連接 Exchange Online 信箱。

SharePoint Online 已啟用新式驗證預設值。 針對2017年8月1日之後建立的目錄，Exchange Online 預設會啟用新式驗證。 不過，如果您先前已停用新式驗證，或使用在此日期之前建立的目錄，請遵循下列文章中的步驟， [在 Exchange Online 中啟用新式驗證](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步驟4：商務用 Skype

若要防止商務用 Skype 提出的舊版驗證要求，必須啟用商務用 Skype Online 的新式驗證。 針對2017年8月1日之後建立的目錄，預設會啟用商務用 Skype 的新式驗證。

我們建議您轉換至 Microsoft 小組，其預設支援新式驗證。 不過，如果您目前無法遷移，則必須啟用商務用 Skype Online 的新式驗證，商務用 Skype 用戶端才會開始使用新式驗證。 請依照這篇文章中的步驟， [使用新式驗證支援的商務拓朴](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)，啟用商務用 Skype 的新式驗證。

除了針對商務用 Skype Online 啟用新式驗證之外，我們也建議在啟用商務用 Skype 的新式驗證時，啟用 Exchange Online 的新式驗證。 此程式將有助於同步處理 Exchange Online 和商務用 Skype online 的新式驗證狀態，並防止商務用 Skype 用戶端有多個登入提示。

### <a name="step-5-using-mobile-devices"></a>步驟5：使用行動裝置

您的行動裝置上的應用程式也必須封鎖舊版驗證。 我們建議使用 Outlook for Mobile。 適用于 Mobile 的 Outlook 預設支援新式驗證，並將滿足其他 MFA 基準保護原則。

若要使用原生 iOS 郵件用戶端，您必須執行 iOS 11.0 版或更新版本，以確保郵件用戶端已更新為封鎖舊版驗證。

### <a name="step-6-on-premises-clients"></a>步驟6：內部部署用戶端

如果您是使用內部部署 Exchange Server 和商務用 Skype 內部部署的混合式客戶，這兩項服務都必須更新以啟用新式驗證。 在混合式環境中使用新式驗證時，您仍在驗證內部部署使用者。 授權其存取資源 (檔案或電子郵件) 變更的案例。

在您開始啟用內部部署的新式驗證之前，請確定您已符合先決條件。 您現在已經準備好啟用內部部署的新式驗證。

您可以在下列文章中找到啟用新式驗證的步驟：

* [如何設定 Exchange Server 內部部署以使用混合式新式驗證](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何使用新式驗證 (ADAL) 與商務用 Skype](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>後續步驟

- [如何設定 Exchange Server 內部部署以使用混合式新式驗證](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [如何使用新式驗證 (ADAL) 與商務用 Skype](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [封鎖舊式驗證](../conditional-access/block-legacy-authentication.md)
