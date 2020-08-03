---
title: 啟用 Azure Active Directory 自助式密碼重設
description: 在本教學課程中，您將了解如何為使用者群組啟用 Azure Active Directory 自助式密碼重設和測試密碼重設程序。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac13dc669ed20df58f05c672926b7bee55dbc90
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035021"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>教學課程：讓使用者使用 Azure Active Directory 自助式密碼重設來解除鎖定其帳戶或重設密碼

Azure Active Directory (Azure AD) 自助式密碼重設 (SSPR) 可讓使用者直接變更或重設其密碼，而無需系統管理員或技術支援中心介入。 如果使用者的帳戶遭到鎖定，或使用者忘記其密碼，他們可以依照提示自行解除封鎖，並繼續工作。 當使用者無法登入其裝置或應用程式時，這項功能將可減少技術服務中心的通話量，並避免失去生產力。

> [!IMPORTANT]
> 此教學課程將說明系統管理員如何啟用自助式密碼重設。 如果您是已註冊自助式密碼重設的使用者，而且需要取回您的帳戶，請移至 https://aka.ms/sspr 。
>
> 如果您的 IT 小組尚未啟用重設您密碼的功能，請與您的技術服務人員聯繫以取得其他協助。

在本教學課程中，您將了解如何：

> [!div class="checklist"]
> * 對 Azure AD 使用者群組啟用自助式密碼重設
> * 設定驗證方法和註冊選項
> * 以使用者身分測試 SSPR 程序

## <a name="prerequisites"></a>必要條件

若要完成此教學課程，您需要下列資源和權限：

* 可運作且至少已啟用試用版授權的 Azure AD 租用戶。
    * 如有需要，[請建立免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 具有「全域系統管理員」權限的帳戶。
* 具有已知密碼的非系統管理員使用者，例如 testuser。 在本教學課程中，您會使用此帳戶來測試終端使用者 SSPR 體驗。
    * 如果您需要建立使用者，請參閱 [快速入門：將使用者新增至 Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) 中的資訊。
* 非系統管理員使用者為其成員的群組，例如 SSPR-Test-Group。 您會在本教學課程中為此群組啟用 SSPR。
    * 如果您需要建立群組，請參閱如何[在 Azure Active Directory 中建立群組和新增成員](../fundamentals/active-directory-groups-create-azure-portal.md)。

## <a name="enable-self-service-password-reset"></a>啟用自助式密碼重設

Azure AD 可讓您針對 [無]、[已選取] 或 [所有] 使用者啟用 SSPR。 這項細微的功能可讓您選擇一部分的使用者來測試 SSPR 註冊程序和工作流程。 當您熟悉此程序並可與更廣泛的使用者溝通需求時，就可以選取要啟用 SSPR 的使用者群組。 或者，您可以為 Azure AD 租用戶中的每個人啟用 SSPR。

> [!NOTE]
>
> 目前只有一個 Azure AD 群組可以使用 Azure 入口網站啟用 SSPR。 在較廣泛的 SSPR 部署中，可支援巢狀群組。 請確定您選擇的群組中的使用者已獲派適當的授權。 目前沒有這些授權需求的驗證程序。

在本教學課程中，為測試群組中的一組使用者設定 SSPR。 下列範例中會使用群組 SSPR-Test-Group。 視需要提供您自己的 Azure AD 群組：

1. 使用具備「全域系統管理員」權限的帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 **Azure Active Directory**，然後從左側功能表中選擇 [密碼重設]。
1. 在 [屬性] 頁面的 [已啟用自助式密碼重設] 選項下方，選擇 [選取群組]。
1. 瀏覽並選取 Azure AD 群組，例如 *SSPR-Test-Group*，然後選擇 [選取]。

    [![](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png "Select a group in the Azure portal to enable for self-service password reset")](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. 若要為所選的使用者啟用 SSPR，請選取 [儲存]。

## <a name="select-authentication-methods-and-registration-options"></a>選取驗證方法和註冊選項

當使用者需要解除鎖定其帳戶或重設其密碼時，系統會提示他們提供其他確認方法。 這個額外的驗證因素可確保只會完成已核准的 SSPR 事件。 您可以根據使用者所提供的註冊資訊，選擇允許使用的驗證方法。

1. 在左側功能表的 [驗證方法] 頁面上，將 [需要重設的方法數] 設定為 *1*。

    若要提升安全性，您可以增加 SSPR 所需的驗證方法數目。

1. 選擇貴組織想要允許的 [使用者可用方法]。 在本教學課程中，請勾選方塊以啟用下列方法：

    * *行動應用程式通知*
    * *行動應用程式程式碼*
    * *電子郵件*
    * *行動電話*

    您可以視需要啟用其他驗證方法 (例如「辦公室電話」或「安全性問題」)，以符合您的商務需求。

1. 若要套用驗證方法，請選取 [儲存]。

使用者必須先註冊其連絡人資訊，才能解除鎖定其帳戶或重設密碼。 此連絡人資訊用於先前步驟中設定的不同驗證方法。

系統管理員可以手動提供此連絡人資訊，或使用者可以前往註冊入口網站來自行提供資訊。 在本教學課程中，設定系統會在使用者下一次登入時提示他們進行註冊。

1. 在左側功能表的 [註冊] 頁面上，針對 [登入時要求使用者註冊] 選取 [是]。
1. 務必讓連絡人資訊保持最新狀態。 如果在 SSPR 事件啟動時，連絡人資訊已過期，使用者可能無法解除鎖定其帳戶或重設其密碼。

    將 [要求使用者重新確認其驗證資訊的等候天數] 設定為 *180*。
1. 若要套用註冊設定，請選取 [儲存]。

## <a name="configure-notifications-and-customizations"></a>設定通知和自訂項目

若要讓使用者得知帳戶活動相關資訊，您可以設定要在 SSPR 事件發生時傳送的電子郵件通知。 這些通知可以涵蓋一般使用者帳戶和系統管理員帳戶。 若為系統管理員帳戶，此通知會在使用 SSPR 重設具有特殊權限的系統管理員帳戶密碼時，提供額外一層感知。 在系統管理員帳戶上使用 SSPR 時，會通知所有全域管理員。

1. 在左側功能表的 [通知] 頁面上，設定下列選項：

   * 將 [通知使用者密碼重設] 選項設定為 [是]。
   * 將 [當其他系統管理員重設其密碼時通知所有系統管理員] 設定為 [是]。

1. 若要套用通知喜好設定，請選取 [儲存]。

如果使用者需要 SSPR 程序的其他協助，您可以自訂「連絡您的系統管理員」連結。 此連結會使用於 SSPR 註冊程序，以及當使用者解除鎖定其帳戶或重設其密碼時。 為了確保您的使用者獲得所需的支援，強烈建議您提供自訂的技術服務人員電子郵件或 URL。

1. 在左側功能表的 [自訂] 頁面上，將 [自訂技術服務人員連結] 設定為 [是]。
1. 在 [自訂的技術服務人員電子郵件或 URL] 欄位中，提供可讓使用者從貴組織取得其他協助的電子郵件地址或網頁 URL，例如 *`https://support.contoso.com/`* 。
1. 若要套用自訂連結，請選取 [儲存]。

## <a name="test-self-service-password-reset"></a>測試自助式密碼重設

啟用並設定 SSPR 後，請使用屬於您在上一節中所選群組的使用者來測試 SSPR 程序，例如 *Test-SSPR-Group*。 下列範例會使用 testuser 帳戶。 提供您自己的使用者帳戶，其屬於您在本教學課程的第一節中針對 SSPR 啟用的群組。

> [!NOTE]
> 當您測試自助式密碼重設時，請使用非系統管理員帳戶。 系統管理員一律會啟用自助密碼重設，且必須使用兩種驗證方法來重設其密碼。

1. 若要查看手動註冊程序，請在 InPrivate 或 Incognito 模式中開啟新的瀏覽器視窗，並瀏覽至 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)。 使用者下次登入時，應會被導向至這個註冊入口網站。
1. 以非系統管理員測試使用者的身分登入 (例如 testuser)，並註冊您的驗證方法連絡人資訊。
1. 完成後，選取標示為**狀況良好**的按鈕，然後關閉瀏覽器視窗。
1. 在 InPrivate 或 Incognito 模式中開啟新的瀏覽器視窗，並瀏覽至 [https://aka.ms/sspr](https://aka.ms/sspr)。
1. 輸入非系統管理員測試使用者的帳戶資訊 (例如 testuser)，以及 CAPTCHA 中的字元，然後選取 [下一步]。

    ![輸入使用者帳戶資訊以重設密碼](media/tutorial-enable-sspr/password-reset-page.png)

1. 依照驗證步驟重設您的密碼。 完成時，您應會收到一封電子郵件通知，指出您的密碼已重設。

## <a name="clean-up-resources"></a>清除資源

在這系列的下列教學課程中，您會設定密碼回寫。 這項功能會將密碼變更從 Azure AD SSPR 寫回內部部署 AD 環境。 如果您想要繼續進行本教學課程系列以設定密碼回寫，請不要立即停用 SSPR。

如果您不再使用您在本教學課程中設定的 SSPR 功能，請使用下列步驟，將 SSPR 狀態設定為 [無]：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 搜尋並選取 **Azure Active Directory**，然後從左側功能表中選擇 [密碼重設]。
1. 在 [屬性] 頁面的 [已啟用自助式密碼重設] 選項下方，選擇 [無]。
1. 若要套用 SSPR 變更，請選取 [儲存]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已針對所選的使用者群組啟用了 Azure AD 自助式密碼重設。 您已了解如何︰

> [!div class="checklist"]
> * 對 Azure AD 使用者群組啟用自助式密碼重設
> * 設定驗證方法和註冊選項
> * 以使用者身分測試 SSPR 程序

> [!div class="nextstepaction"]
> [啟用 Azure Multi-Factor Authentication](tutorial-mfa-applications.md)
