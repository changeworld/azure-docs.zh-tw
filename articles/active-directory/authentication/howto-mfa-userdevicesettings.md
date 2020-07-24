---
title: 管理 Azure 多重要素驗證的使用者設定-Azure Active Directory
description: 瞭解如何為 Azure 多因素驗證設定 Azure Active Directory 的使用者設定
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9861e8e8be39781ad1b256b9939df3ab03e74be6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87027524"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>管理 Azure 多重要素驗證的使用者設定

若要協助管理 Azure 多重要素驗證的使用者，您可以要求使用者重設其密碼、重新註冊 MFA，或撤銷現有的 MFA 會話。 針對已定義應用程式密碼的使用者，您也可以選擇刪除這些密碼，導致這些應用程式中的舊版驗證失敗。 如果您需要為使用者提供協助，或想要重設其安全性狀態，則可能需要執行這些動作。

## <a name="manage-user-authentication-options"></a>管理使用者驗證選項

如果您已獲指派「*驗證系統管理員*」角色，您可以要求使用者重設其密碼、重新註冊 MFA，或從使用者物件撤銷現有的 MFA 會話。 若要管理使用者設定，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左邊的 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 選擇您想要對其執行動作的使用者，然後選取 [**驗證方法**]。 在視窗頂端，為使用者選擇下列其中一個選項：
   - [**重設密碼**] 會重設使用者的密碼，並指派必須在下次登入時變更的暫時密碼。
   - [**需要重新註冊 MFA** ] 會讓使用者在下一次登入時，要求他們設定新的 MFA 驗證方法。
   
      > [!NOTE]
      > 當系統管理員需要重新註冊 MFA 時，使用者目前註冊的驗證方法不會被刪除。 使用者重新註冊 MFA 之後，建議您檢查其安全性資訊，並刪除任何先前已註冊且已無法使用的驗證方法。
   
   - **撤銷 Mfa 會話**會清除使用者已記住的 mfa 會話，並要求他們在裝置上的原則需要時執行 mfa。

   ![從 Azure 入口網站管理驗證方法](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼

如有需要，您可以刪除使用者建立的所有應用程式密碼。 與這些應用程式密碼相關的非瀏覽器應用程式會停止運作，直到建立新應用程式密碼為止。 需要*全域管理員*許可權才能執行此動作。

若要刪除使用者的應用程式密碼，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左側，選取 [ **Azure Active Directory**  >  **使用者**] [  >  **所有使用者**]。
1. 選取 [多重要素驗證]。 您可能必須捲動到右邊才能看到此功能表選項。 選取以下的範例螢幕擷取畫面，以查看完整的 Azure 入口網站視窗和功能表位置：[![](media/howto-mfa-userstates/selectmfa-cropped.png "從 Azure AD 中的 [使用者] 視窗選取 [Multi-Factor Authentication]")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 勾選您想要管理之使用者旁邊的方塊。 快速步驟選項的清單會出現在右側。
1. 選取 [**管理使用者設定**]，然後核取 [**刪除選取的使用者產生的所有現有應用程式密碼**] 方塊，如下列範例所示： ![ 刪除所有現有的應用程式密碼](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. 選取 [**儲存**]，然後按一下 [**關閉**]。

## <a name="next-steps"></a>接下來的步驟

本文說明如何設定個別使用者設定。 若要設定整體 Azure 多因素驗證服務設定，請參閱[設定 Azure 多因素驗證設定](howto-mfa-mfasettings.md)。

如果您的使用者需要協助，請參閱[Azure 多重要素驗證的使用者指南](../user-help/multi-factor-authentication-end-user-first-time.md)。
