---
title: 封鎖 Azure AD 中的舊版驗證通訊協定
description: 瞭解組織應封鎖舊版驗證通訊協定的方式和原因
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 24640254f32270b8c96c790dca7db31e285cc27f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895283"
---
# <a name="blocking-legacy-authentication"></a>封鎖舊版驗證
 
為了讓您的使用者能夠輕鬆存取雲端應用程式，Azure Active Directory (Azure AD) 支援多種驗證通訊協定，包括舊式驗證。 「舊版驗證」一詞，是指由以下幾者提出的驗證要求：

- 未使用新式驗證的舊版 Office 用戶端 (例如，Office 2010 用戶端) 
- 任何使用舊版郵件通訊協定的用戶端，例如 IMAP/SMTP/POP3

目前，所有危害的登入嘗試都是來自舊版驗證。 舊版驗證不支援 (MFA) 的多重要素驗證。 即使您已在目錄上啟用 MFA 原則，不良的動作專案仍可使用舊版通訊協定進行驗證並略過 MFA。 保護您的帳戶免于舊版通訊協定所提出之惡意驗證要求的最佳方式，就是完全封鎖這些嘗試。

## <a name="identify-legacy-authentication-use"></a>辨識是否使用舊式驗證

首先需要先了解您的使用者是否有應用程式在使用舊式驗證，以及舊式驗證會對您的整體目錄產生何種影響，如此才能在目錄中封鎖舊式驗證。 可以使用 Azure AD 登入記錄來確認是否在使用舊式驗證。

1. 瀏覽至 [Azure 入口網站]  >  [Azure Active Directory]  >  [登入]。
1. 如果未顯示 [**用戶端應用程式**] 資料行，請按一下 [資料  **行**   >  **用戶端應用程式**]。
1. 依 **用戶端應用程式** 篩選 > 檢查顯示的所有 **舊版驗證用戶端** 選項。
1. 依 **狀態**  >  **成功** 篩選。 
1. 如有必要，請使用 **日期** 篩選來展開您的日期範圍。
1. 如果您已啟用新的登 [入活動報告預覽](../reports-monitoring/concept-all-sign-ins.md)，請在 **使用者登入 (非互動式)** 索引標籤上重複上述步驟。

篩選只會顯示所選舊版驗證通訊協定所做的成功登入嘗試。 按一下各項登入嘗試，即會顯示更多詳細資料。 選取個別資料列之後，[基本資訊] 索引標籤下的 [用戶端應用程式] 資料行或 [用戶端應用程式] 欄位，將會指出使用的是舊版驗證通訊協定 這些記錄會顯示哪些使用者仍依賴舊式驗證，以及哪些應用程式目前會使用舊式通訊協定提出驗證要求。 針對未出現在這些記錄中且已確認不使用舊版驗證的使用者，請執行條件式存取原則或啟用基準原則：僅封鎖這些使用者的舊版驗證。

## <a name="moving-away-from-legacy-authentication"></a>移離舊版驗證 

當您進一步瞭解在您的目錄中使用舊版驗證，以及哪些應用程式相依于該目錄時，下一個步驟是將您的使用者升級為使用新式驗證。 新式驗證是身分識別管理的方法，可提供更安全的使用者驗證和授權。 如果您的目錄上已有 MFA 原則，新式驗證可確保使用者在需要時收到 MFA 提示。 這是較安全的傳統驗證通訊協定替代方法。

本節提供逐步解說，說明如何將您的環境更新為新式驗證。 在您的組織中啟用舊版驗證封鎖原則之前，請先閱讀以下步驟。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步驟1：在您的目錄中啟用新式驗證

啟用新式驗證的第一個步驟是確定您的目錄支援新式驗證。 針對在2017年8月1日或之後建立的目錄，預設會啟用新式驗證。 如果您的目錄是在此日期之前建立的，您必須使用下列步驟，為您的目錄手動啟用新式驗證：

1.  `Get-CsOAuthConfiguration`   從 [商務用 Skype Online PowerShell 模組](/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)執行，查看您的目錄是否已支援新式驗證。
1. 如果您的命令傳回空白  `OAuthServers`   屬性，則會停用新式驗證。 使用來更新設定，以啟用新式驗證  `Set-CsOAuthConfiguration` 。 如果您  `OAuthServers`   的屬性包含專案，您就可以開始了。

請務必先完成此步驟，再繼續進行。 先變更您的目錄設定是很重要的，因為它們會指出所有 Office 用戶端將使用的通訊協定。 即使您正在使用支援新式驗證的 Office 用戶端，如果您的目錄已停用新式驗證，則會預設為使用舊版通訊協定。

### <a name="step-2-office-applications"></a>步驟2： Office 應用程式

在您的目錄中啟用新式驗證之後，您就可以啟用 Office 用戶端的新式驗證來開始更新應用程式。 Office 2016 或更新版本的用戶端預設支援新式驗證。 不需要額外的步驟。

如果您使用的是 Office 2013 Windows 用戶端或更舊版本，建議您升級至 Office 2016 或更新版本。 即使在您的目錄中完成啟用新式驗證的先前步驟之後，舊版 Office 應用程式仍將繼續使用舊版驗證通訊協定。 如果您使用 Office 2013 用戶端且無法立即升級至 Office 2016 或更新版本，請遵循下列文章中的步驟，在 [Windows 裝置上啟用 office 2013 的新式驗證](/office365/admin/security-and-compliance/enable-modern-authentication)。 若要在使用舊版驗證時協助保護您的帳戶，建議您在目錄中使用強式密碼。 請查看 [Azure AD 密碼保護](../authentication/concept-password-ban-bad.md)   ，以禁止在您的目錄中的弱式密碼。

Office 2010 不支援新式驗證。 您必須將任何使用 Office 2010 的使用者升級至較新版本的 Office。 建議您升級至 Office 2016 或更新版本，因為它預設會封鎖舊版驗證。

如果您使用 macOS，建議您升級至 Office for Mac 2016 或更新版本。 如果您使用原生郵件用戶端，在所有裝置上都必須有 macOS 10.14 版或更新版本。

### <a name="step-3-exchange-and-sharepoint"></a>步驟3： Exchange 和 SharePoint

針對以 Windows 為基礎的 Outlook 用戶端使用新式驗證，Exchange Online 也必須啟用新式驗證。 如果 Exchange Online 已停用新式驗證，則支援新式驗證 (Outlook 2013 或更新版本的 Windows 型 Outlook 用戶端) 將會使用基本驗證來連接到 Exchange Online 信箱。

針對新式驗證預設會啟用 SharePoint Online。 針對在2017年8月1日之後建立的目錄，Exchange Online 預設會啟用新式驗證。 但是，如果您先前已停用新式驗證，或您使用在此日期之前建立的目錄，請依照下列文章中的步驟， [在 Exchange Online 中啟用新式驗證](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步驟4：商務用 Skype

為了避免商務用 Skype 提出舊版驗證要求，必須啟用商務用 Skype Online 的新式驗證。 針對在2017年8月1日之後建立的目錄，預設會啟用商務用 Skype 的新式驗證。

建議您轉換至 Microsoft 小組，預設支援新式驗證。 但是，如果您目前無法遷移，您將需要啟用商務用 Skype Online 的新式驗證，以便商務用 Skype 用戶端開始使用新式驗證。 請依照此文章中的步驟， [使用新式驗證所支援的商務用 skype 拓撲](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)來啟用適用于商務用 Skype 的新式驗證。

除了啟用商務用 Skype Online 的新式驗證之外，我們也建議在啟用商務用 Skype 的新式驗證時啟用 Exchange Online 的新式驗證。 此程式將有助於在 Exchange Online 和商務用 Skype online 中同步處理新式驗證的狀態，並可防止商務用 Skype 用戶端的多重登錄提示。

### <a name="step-5-using-mobile-devices"></a>步驟5：使用行動裝置

您行動裝置上的應用程式也需要封鎖舊版驗證。 建議您在行動裝置上使用 Outlook。 適用于 Mobile 的 Outlook 預設支援新式驗證，而且會滿足其他 MFA 基準保護原則。

若要使用原生 iOS 郵件用戶端，您將必須執行 iOS 11.0 版或更新版本，以確保郵件用戶端已更新為封鎖舊版驗證。

### <a name="step-6-on-premises-clients"></a>步驟6：內部部署用戶端

如果您是使用 Exchange Server 內部部署和商務用 Skype 內部部署的混合式客戶，則這兩項服務都必須更新，才能啟用新式驗證。 在混合式環境中使用新式驗證時，您仍在驗證內部部署使用者。 授權其存取資源 (檔案或電子郵件) 變更的案例。

在您可以開始在內部部署環境中啟用新式驗證之前，請確定您已符合必要條件。 您現在已經準備好在內部部署環境中啟用新式驗證。

您可以在下列文章中找到啟用新式驗證的步驟：

* [如何設定 Exchange Server 內部部署以使用混合式新式驗證](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何使用適用于商務用 Skype) 的新式驗證 (ADAL](/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>後續步驟

- [如何設定 Exchange Server 內部部署以使用混合式新式驗證](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [如何使用適用于商務用 Skype) 的新式驗證 (ADAL](/skypeforbusiness/manage/authentication/use-adal)
- [封鎖舊式驗證](../conditional-access/block-legacy-authentication.md)