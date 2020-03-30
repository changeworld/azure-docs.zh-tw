---
title: 使用 Microsoft 身份驗證器應用登錄 - Azure AD
description: 使用 Microsoft Authenticator 應用程式登入公司或學校帳戶，或使用雙因素驗證或手機登入，登入您個人的 Microsoft 和非 Microsoft 帳戶。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 9b8c44f99953d4518f0bc3f558f396250657c632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138938"
---
# <a name="sign-in-to-your-accounts-using-the-microsoft-authenticator-app"></a>使用 Microsoft Authenticator 應用程式登入帳戶

如果使用雙重驗證，Microsoft 身份驗證器應用可説明您登錄帳戶。 雙因素驗證有助於您以更安全的方式存取帳戶，尤其是在檢視機密資訊時。 您有可能忘記密碼，或密碼有可能遭竊或遭到破解，而雙因素驗證可作為額外的安全性步驟，協助您保護帳戶而使他人難以入侵。

您可以透過多種方式使用 Microsoft Authenticator 應用程式，包括：

- 在您以使用者名稱和密碼登入後，提供另一個驗證方法的提示。

- 使用您的使用者名稱和採用指紋、臉部或 PIN 的行動裝置提供登入，而無須輸入密碼。

  >[!Important]
  >此手機登入方法僅適用於您的公司或學校以及您的個人 Microsoft 帳戶。 您的帳戶和非 Microsoft 帳戶會要求您使用標準的雙因素驗證程序。

## <a name="prerequisites"></a>Prerequisites

使用 Microsoft Authenticator 應用程式之前，您必須：

 1. 下載並安裝 Microsoft Authenticator 應用程式。 如果您還沒有這麼做，請參閱[下載並安裝應用程式](user-help-auth-app-download-install.md)。

 2. 將您的公司/學校、個人和第三方帳戶新增至 Microsoft Authenticator 應用程式中。 如需詳細步驟，請參閱[新增公司或學校帳戶](user-help-auth-app-add-work-school-account.md)、[新增個人帳戶](user-help-auth-app-add-personal-ms-account.md)，以及[新增的非 Microsoft 帳戶](user-help-auth-app-add-non-ms-account.md)。

## <a name="turn-on-and-use-phone-sign-in-for-your-work-or-school-account"></a>為您的公司或學校帳戶開啟並使用手機登入

手機登入是一種雙步驟驗證類型。 您仍然必須提供您知道的一件事和您擁有的一個東西來確認您的身分識別，但手機登入可讓您略過輸入您的帳戶密碼，並在行動裝置上執行所有身分識別驗證。

開啟手機登入之前，您必須開啟雙因素驗證。 如需有關如何開啟帳戶的雙因素驗證的詳細資訊，請參閱[新增公司或學校帳戶](user-help-auth-app-add-work-school-account.md)和[新增個人帳戶](user-help-auth-app-add-personal-ms-account.md)。

手機登入僅適用於 iOS 以及執行 Android 6.0 或更新版本的 Android 裝置。

### <a name="turn-on-phone-sign-in"></a>開啟手機登入

打開 Microsoft 身份驗證器應用，轉到您的工作或學校帳戶，然後打開電話登錄。

在 Android 設備上：

- **如果您看到這個圖示![顯示您已設定好的圖示](media/user-help-auth-app-sign-in/icon.png)。** 如果這個圖示出現在您的公司或學校帳戶名稱的旁邊，表示您已經為帳戶設定好手機登入。 系統可能會要求您新增帳戶的推播通知，如此您就可以在應用程式外部收到有關驗證要求的通知。
- **如果您已經在使用該應用程式進行雙重驗證。** 如果您已經使用應用和雙重驗證，則可以選擇帳戶名稱旁邊的箭頭，然後選擇啟用**電話登錄**。
- **如果找不到您的公司或學校帳戶。** 如果在應用程式的 [帳戶]**** 畫面上找不到您的公司或學校帳戶，表示您還沒有將帳戶新增到應用程式中。 依照[新增公司或學校帳戶](user-help-auth-app-add-work-school-account.md)一文中的步驟，新增工作或學校帳戶。

在 iOS 設備上：

- **當您點擊帳戶磁貼時**，該帳戶將打開到帳戶的全屏視圖中。 如果您看到**啟用了電話登錄**，則意味著您已完全設置為在沒有密碼的情況下登錄。 如果看到**啟用電話登錄**，請點按該登錄以打開電話登錄。
- **如果您已經使用該應用程式進行雙重驗證**，則可以點擊帳戶磁貼，然後擴展到帳戶的全屏視圖。 然後點按 **"啟用電話登錄"** 以打開電話登錄。
- 如果在應用的 **"帳戶"** 螢幕上**找不到工作或學校帳戶**，則表示您尚未將其添加到應用。 依照新增公司或學校帳戶一文中的步驟，新增工作或學校帳戶。

開啟手機登入之後，您可以只使用 Microsoft Authenticator 應用程式登入。

1. 登入您的公司或學校帳戶。

    輸入您的使用者名稱之後，[核准登入]**** 畫面隨即出現，其中顯示一個兩位數的號碼，並要求您透過 Microsoft Authenticator 應用程式登入。 如果您不想使用這個登入方法，您可以選取 [改為使用您的密碼]****，然後使用您的密碼登入。

    ![電腦上的核准登入方塊](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in.png)

2. 在裝置上開啟通知或 Microsoft Authenticator 應用程式，然後點選與您在電腦的 [核准登入]**** 畫面上看到之數字相符的數字。

    ![裝置上的核准登入方塊](media/user-help-auth-app-sign-in/microsoft-auth-app-sign-in-numbers.png)

3. 如果您可以辨識該登入嘗試，請選擇 [核准]****。 否則，請選擇 [拒絕]****。

4. 使用您手機的 PIN 或生物識別金鑰來完成驗證。

## <a name="turn-on-and-use-phone-sign-in-for-your-personal-microsoft-accounts"></a>為您的個人 Microsoft 帳戶開啟並使用手機登入

您可以針對個人的 Microsoft 帳戶開啟手機登入，例如，您用來登入 Outlook.com、Xbox 或 Skype 的帳戶。

>[!NOTE]
>若要協助保護您的帳戶，Microsoft Authenticator 應用程式在裝置上需要有 PIN 或生物識別鎖定。 如果您讓手機保持解除鎖定狀態，應用程式就會要求您先設定安全鎖定之後，才能開啟手機登入。

### <a name="turn-on-phone-sign-in"></a>開啟手機登入 

打開 Microsoft 身份驗證器應用，轉到您的工作或學校帳戶，然後打開電話登錄。

在 Android 設備上：

- **如果您看到這個圖示![顯示您已設定好的圖示](media/user-help-auth-app-sign-in/icon.png)。** 如果此圖示顯示在您的個人 Microsoft 帳戶名稱旁邊，則表示您已為該帳戶設置了電話登錄。 系統可能會要求您新增帳戶的推播通知，如此您就可以在應用程式外部收到有關驗證要求的通知。
- **如果您使用應用程式進行雙因素驗證。** 如果您已經使用應用和雙重驗證，則可以選擇帳戶名稱旁邊的箭頭，然後選擇啟用**電話登錄**。
- **如果找不到您的公司或學校帳戶。** 如果在應用程式的 [帳戶]**** 畫面上找不到您的帳戶，表示您還沒有將帳戶新增到應用程式中。 按照"[添加個人 Microsoft 帳戶](user-help-auth-app-add-personal-ms-account.md)"一文中的步驟添加您的帳戶。

在 iOS 設備上：

- **當您點擊帳戶磁貼時**，該帳戶將打開到帳戶的全屏視圖中。 如果您看到**啟用了電話登錄**，則意味著您已完全設置為在沒有密碼的情況下登錄。 如果看到**啟用電話登錄**，請點按該登錄以打開電話登錄。
- **如果您已經在使用該應用程式進行雙重驗證**，則可以點擊帳戶磁貼，然後擴展到帳戶的全屏視圖。 然後點按 **"啟用電話登錄"** 以打開電話登錄。
- 如果在應用的 **"帳戶"** 螢幕上**找不到您的帳戶**，則表示您尚未將其添加到應用。 按照["添加個人 Microsoft 帳戶](user-help-auth-app-add-personal-ms-account.md)"一文中的步驟添加您的個人 Microsoft 帳戶。

### <a name="sign-in-to-your-account-using-phone-sign-in"></a>使用手機登入來登入您的帳戶

1. 移至您個人 Microsoft 帳戶登入頁面，然後選取 [改為使用 Microsoft Authenticator 應用程式]**** 連結，而不是輸入您的密碼。

    Microsoft 會傳送通知至您的電話。

2. 核准通知。

## <a name="sign-in-using-two-factor-verification-for-your-account"></a>為您的帳戶使用雙因素驗證登入

標準雙因素驗證方法要求您在登錄的設備中輸入使用者名和密碼。 然後，打開 Microsoft 身份驗證器應用，並在 **"輸入代碼"** 框中鍵入帳戶隨機生成的代碼。 在 Android 設備上，可以在 **"帳戶"** 螢幕上找到這些驗證碼。 在 iOS 設備上，這些驗證碼可以在**帳戶的"帳戶"** 螢幕或帳戶的全屏視圖中找到，具體取決於帳戶的類型。 您要在過程中為您的帳戶開啟雙因素驗證，才能將該帳戶新增到 Microsoft Authenticator 應用程式。

>[!Note]
>如果您在 Microsoft Authenticator 應用程式的 [帳戶]**** 畫面上沒有看到您的公司或學校帳戶，或您個人的帳戶，表示您還沒有將該帳戶新增到 Microsoft Authenticator 應用程式。 若要新增您的帳戶，請參閱[新增公司或學校帳戶](user-help-auth-app-add-work-school-account.md)或[新增個人帳戶](user-help-auth-app-add-personal-ms-account.md)。

如需了解使用雙因素驗證的各種方法登入公司或學校或個人帳戶所需的步驟，請參閱[使用雙步驟驗證或安全性資訊登入](user-help-sign-in.md)。

## <a name="frequently-asked-questions"></a>常見問題集

| 問題 | 解決方法 |
| -------- | -------- |
| 使用我的電話登入會如何比輸入密碼更安全？ | 現今大部分的人會使用使用者名稱和密碼登入網站或應用程式。 不幸的是，密碼可能會遺失、遭竊或被駭客猜到。<br><br>設定 Microsoft Authenticator 應用程式之後，它會在您的手機上設定一個金鑰，來將您受到手機 PIN 或生物識別鎖定所保護的帳戶解除鎖定。 這個金鑰接著會在登入時用來證明您的身分識別。<br><br>**重要**<br>您的資料只會用來在本機保護您的金鑰。 絕對不會將它傳送到雲端或儲存在雲端。 |
| 手機登入是否會取代雙步驟驗證？ 應該將它關機嗎？ | 手機登入是一種雙步驟驗證，其中兩個步驟會行動裝置上同時進行。 您應該將雙步驟驗證保持在開啟狀態，以協助為您的帳戶提供額外的安全性。 |
| 如果我為帳戶持續開啟了雙步驟驗證，是否必須核准兩個通知呢？ | 否。 使用手機登入您的 Microsoft 帳戶，也會被視為雙步驟驗證，因此不需要第二個核准。 |
| 如果我遺失手機或者沒帶手機，該怎麼辦？ 如何存取我的帳戶？ | 您隨時都可按一下登入頁面上的 [改為使用密碼] 連結，切換回使用您的密碼。 不過，如果您使用雙步驟驗證，您仍需要使用第二個方法來驗證您的身分識別。<br><br>**重要**<br>強烈建議您確定有多個與您帳戶相關聯的最新驗證方法。<br><br>您可以從[安全性設定](https://account.live.com/proofs/manage)頁面來管理個人帳戶的驗證方法。 針對公司或學校帳戶，如果您的系統管理員已開啟安全性資訊，您就可以移至組織的[其他安全性驗證](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)頁面或**保護您的帳戶安全**頁面。 有關安全資訊的詳細資訊，請參閱[安全資訊（預覽）概述](user-help-security-info-overview.md)。<br><br>如果您無法管理驗證方法，就必須連絡您的系統管理員。 |
| 如何停止使用此功能並改回使用我的密碼？ | 對於個人帳戶，在登入期間選取 [改為使用密碼]**** 連結。 系統會記住您最近的選擇，並在您下次登入時依預設提供該選擇。 如果您想要改回使用手機登入，請在登入期間選取 [改為使用應用程式]**** 連結。<br><br>對於工作或學校帳戶，您必須從 Microsoft 身份驗證器應用的 **"設置"** 頁取消註冊設備，或者從設定檔的 **"設備&活動**區域禁用設備。 有關從設定檔中禁用設備的詳細資訊，請參閱[從"我的應用"門戶更新您的個人資料和帳戶資訊](my-apps-portal-end-user-update-profile.md#view-your-organization-related-profile-information)。 |
| 為什麼不能使用多個公司或學校帳戶進行手機登入？ | 一支手機必須向單一公司或學校帳戶進行註冊。 如果要為其他工作或學校帳戶打開電話登錄，則必須通過 **"設置"** 頁面從該設備取消註冊您的帳戶。 |
| 可以使用手機來登入我的電腦嗎？ | 針對您的電腦，我們建議使用 Windows 10 上的 Windows Hello 來登入。 Windows Hello 可讓您使用臉部、指紋或 PIN 來登入。 |

## <a name="next-steps"></a>後續步驟

- 如果您在獲取個人 Microsoft 帳戶的驗證碼時遇到問題，請參閱[Microsoft 帳戶安全資訊&驗證碼](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes)一文的**故障排除驗證碼問題**部分。

- 如果您有更多關於應用程式的一般問題，請參閱 [Microsoft Authenticator 常見問題集](user-help-auth-app-faq.md)

- 如果您需要雙步驟驗證的詳細資訊，請參閱[對我的帳戶進行雙步驟驗證設定](multi-factor-authentication-end-user-first-time.md)

- 如果需要有關安全資訊的詳細資訊，請參閱[安全資訊（預覽）概述](user-help-security-info-overview.md)
