---
title: B2B 來賓使用者的一次性密碼驗證 - Azure AD
description: 如何無需 Microsoft 帳戶而使用電子郵件的單次密碼來驗證 B2B 來賓使用者。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9a0668b3ea651d129dc076e5f2247e38f5ab7d0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725490"
---
# <a name="email-one-time-passcode-authentication"></a>電子郵件單次密碼驗證

本文說明如何針對 B2B 來賓使用者啟用電子郵件單次密碼驗證。 當您無法透過其他方式（例如 Azure AD、Microsoft 帳戶 (MSA) 或 Google 同盟）驗證 B2B 來賓使用者時，電子郵件單次密碼功能會對其進行驗證。 使用單次密碼驗證時，不需要建立 Microsoft 帳戶。 當來賓使用者兌換邀請或存取共用資源時，他們可以要求一個暫時性驗證碼，此驗證碼會傳送到他們的電子郵件地址。 之後，他們便可輸入此驗證碼繼續登入。

![電子郵件單次密碼總覽圖表](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> 自 **2021 年3月起**，新租使用者的電子郵件單次密碼功能將會針對所有現有的租使用者開啟，並預設為啟用。 如果您不想要讓這項功能自動開啟，您可以將它停用。 請參閱下方 [的停用電子郵件一次性密碼](#disable-email-one-time-passcode) 。

> [!NOTE]
> 單次密碼使用者必須使用包含租用戶內容的連結 (例如 `https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>`，如果是已驗證的網域，則為 `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`) 來登入。 應用程式和資源的直接連結只要包含租用戶內容，也可有同樣作用。 來賓使用者目前無法使用沒有租用戶內容的端點來登入。 例如，使用 `https://myapps.microsoft.com` `https://portal.azure.com` 會導致錯誤。

## <a name="user-experience-for-one-time-passcode-guest-users"></a>單次密碼來賓使用者的使用者體驗

啟用電子郵件單次密碼功能時， [符合特定條件](#when-does-a-guest-user-get-a-one-time-passcode) 的新邀使用者將會使用單次密碼驗證。 在啟用電子郵件單次密碼的情況下，兌換邀請的來賓使用者將繼續使用相同的驗證方法。

使用單次密碼驗證時，來賓使用者可以兌換您的邀請，方法是按一下直接連結，或使用邀請電子郵件。 無論是哪一種方法，瀏覽器中會顯示訊息，指出驗證碼將傳送到來賓使用者的電子郵件地址。 來賓使用者可選取 [傳送驗證碼]：

   ![顯示 [傳送驗證碼] 按鈕的螢幕擷取畫面](media/one-time-passcode/otp-send-code.png)

密碼會傳送到使用者的電子郵件地址。 使用者可從電子郵件中擷取該密碼，並在瀏覽器視窗中輸入：

   ![顯示 [輸入驗證碼] 頁面的螢幕擷取畫面](media/one-time-passcode/otp-enter-code.png)

來賓使用者現在已通過驗證，可看見共用資源或繼續登入。

> [!NOTE]
> 單次密碼的有效時間為 30 分鐘。 30 分鐘之後，該特定的單次密碼不再有效，使用者必須要求新的密碼。 使用者工作階段會在 24 小時後過期。 在此之後，來賓使用者在存取資源時會收到新密碼。 工作階段到期能夠增加安全性，尤其是在來賓使用者離開公司或不再需要存取時。

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>來賓使用者何時會收到單次密碼？

在下列情況下，當來賓使用者兌換邀請，或使用已與他們共用的資源連結時，便會收到單次密碼：

- 他們沒有 Azure AD 帳戶
- 他們沒有 Microsoft 帳戶
- 邀請方租用戶未為 @gmail.com 和 @googlemail.com 使用者設定 Google 同盟

邀請時，不會指出您邀請的使用者將使用單次密碼驗證。 但當來賓使用者登入時，如果沒有其他驗證方法可使用，單次密碼驗證將作為後援方法。

您可以查看使用者的詳細資料中的 [ **來源** ] 屬性，以查看來賓使用者是否使用單次密碼進行驗證。 在 [Azure 入口網站] 中，移至 [ **Azure Active Directory**  >  **使用者**]，然後選取使用者以開啟 [詳細資料] 頁面。

![螢幕擷取畫面，其中顯示具有 OTP 來源值的一次性密碼使用者](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> 當使用者兌換單次密碼，並稍後取得 MSA、Azure AD 帳戶或其他同盟帳戶時，系統仍會繼續使用單次密碼進行驗證。 如果您想要更新驗證方法，您可以刪除來賓使用者帳戶，然後重新邀請他們。

### <a name="example"></a>範例

邀請來賓使用者 teri@gmail.com 到 Fabrikam，該使用者尚未設定 Google 同盟。 Teri 沒有 Microsoft 帳戶。 他們會收到一次性密碼進行驗證。

## <a name="disable-email-one-time-passcode"></a>停用電子郵件一次性密碼

自2021年3月起，新租使用者的電子郵件單次密碼功能將會針對所有現有的租使用者開啟，並預設為啟用。 屆時，Microsoft 將不再支援兌換邀請，方法是為 B2B 共同作業案例建立非受控 ( 「病毒」或「即時」 ) Azure AD 帳戶和租使用者。 我們會啟用電子郵件單次密碼功能，因為它會為您的來賓使用者提供順暢的回溯驗證方法。 但是，如果您選擇不使用此功能，您可以選擇停用此功能。

> [!NOTE]
>
> 如果您的租使用者中已啟用電子郵件單次密碼功能，而您將其關閉，則已兌換單次密碼的任何來賓使用者將無法登入。 您可以刪除來賓使用者並加以重新邀請，讓他們可以使用另一個驗證方法再次登入。

### <a name="to-disable-the-email-one-time-passcode-feature"></a>停用電子郵件單次密碼功能

1. 以 Azure AD 全域管理員身分登入 [Azure 入口網站](https://portal.azure.com/)。

2. 在導覽窗格中，選取 [Azure Active Directory]。

3. 選取 [外部身分識別] > [外部共同作業設定]。

4. 在 [ **來賓的電子郵件單次密碼**] 下，選取 [停用 **來賓的電子郵件單次密碼**]。

    ![電子郵件單次密碼設定](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > 如果您看到下列切換，而不是上面顯示的選項，這表示您先前已啟用、停用或加入宣告功能的預覽。 選取 [ **否** ] 以停用此功能。
   >
   >![啟用電子郵件單次密碼選擇](media/delegate-invitations/enable-email-otp-opted-in.png)

5. 選取 [儲存]。

## <a name="note-for-public-preview-customers"></a>公開預覽客戶注意事項

如果您先前已選擇使用電子郵件單次密碼公開預覽，則在2021年3月的自動功能啟用日期不適用於您，因此您的相關商務程式不會受到影響。 此外，在 Azure 入口網站的 [來賓內容的 **電子郵件單次密碼** ] 下，您將不會看到在 **2021 年3月針對來賓自動啟用電子郵件單次密碼** 的選項。 相反地，您會看到下列 [ **是]** 或 [ **否** ] 切換：

![啟用電子郵件單次密碼選擇](media/delegate-invitations/enable-email-otp-opted-in.png)

但是，如果您想要選擇不使用此功能，並允許它在2021年3月自動啟用，您可以使用 Microsoft Graph API [電子郵件驗證方法設定資源類型](/graph/api/resources/emailauthenticationmethodconfiguration)來還原為預設值。 還原為預設值之後， **來賓的電子郵件單次密碼** 下將提供下列選項：

- **在2021年3月自動為來賓啟用電子郵件單次密碼**。  (預設) 如果您的租使用者尚未啟用電子郵件單次密碼功能，則會在2021年3月自動開啟。 如果您想要在該時間啟用功能，則不需要採取任何進一步的動作。 如果您已經啟用或停用此功能，將無法使用此選項。

- **為來賓啟用電子郵件單次密碼立即生效**。 開啟租使用者的電子郵件一次性密碼功能。

- **停用來賓的電子郵件一次性密碼**。 關閉您租使用者的電子郵件單次密碼功能，並防止此功能在2021年3月開啟。