---
title: 管理 Azure Multi-Factor Authentication 的使用者設定-Azure Active Directory
description: 瞭解如何設定 Azure Multi-Factor Authentication 的 Azure Active Directory 使用者設定
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
ms.openlocfilehash: 49c389170e9d586a2001009226c6542d4d152f7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87418214"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>管理 Azure Multi-Factor Authentication 的使用者設定

若要協助管理 Azure Multi-Factor Authentication 的使用者，您可以要求使用者重設其密碼、重新註冊 MFA，或撤銷現有的 MFA 會話。 針對已定義應用程式密碼的使用者，您也可以選擇刪除這些密碼，導致這些應用程式中的舊版驗證失敗。 如果您需要為使用者提供協助，或想要重設其安全性狀態，則可能需要執行這些動作。

## <a name="manage-user-authentication-options"></a>管理使用者驗證選項

如果您已獲指派 *驗證系統管理員* 角色，您可以要求使用者重設其密碼、重新註冊 MFA，或從其使用者物件撤銷現有的 mfa 會話。 若要管理使用者設定，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左邊的 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 選擇您想要對其執行動作的使用者，然後選取 [ **驗證方法**]。 在視窗頂端，選擇下列其中一個使用者選項：
   - **重設密碼** 會重設使用者的密碼，並指派暫時密碼，必須在下次登入時變更。
   - **需要重新註冊 MFA** 才能讓使用者下次登入時，要求他們設定新的 MFA 驗證方法。
   
      > [!NOTE]
      > 當系統管理員需要重新註冊 MFA 時，不會刪除使用者目前註冊的驗證方法。 在使用者重新註冊 MFA 之後，我們建議他們檢查安全性資訊，並刪除任何先前註冊的驗證方法，而這些方法不再可用。
   
   - **撤銷 MFA 會話** 會清除使用者記住的 mfa 會話，並要求他們在下次裝置上的原則需要時執行 mfa。

   ![從 Azure 入口網站管理驗證方法](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼

如有需要，您可以刪除使用者已建立的所有應用程式密碼。 與這些應用程式密碼相關的非瀏覽器應用程式會停止運作，直到建立新應用程式密碼為止。 需要*全域管理員*許可權，才能執行此動作。

若要刪除使用者的應用程式密碼，請完成下列步驟：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左側的 [ **Azure Active Directory**  >  **使用者**  >  **所有使用者**]。
1. 選取 [多重要素驗證]。 您可能必須捲動到右邊才能看到此功能表選項。 選取下方的範例螢幕擷取畫面，以查看完整的 Azure 入口網站視窗和功能表位置： [ ![ 從 Azure AD 的 [使用者] 視窗中選取 [Multi-Factor Authentication]。](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 勾選您想要管理之使用者旁邊的方塊。 快速步驟選項的清單會出現在右側。
1. 選取 [ **管理使用者設定**]，然後核取 [ **刪除所選使用者產生的所有現有應用程式密碼**] 核取方塊，如下列範例所示： ![ 刪除所有現有的應用程式密碼](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. 選取 [ **儲存**]，然後 **關閉**。

## <a name="next-steps"></a>接下來的步驟

本文說明如何設定個別的使用者設定。 若要設定整體 Azure Multi-Factor Authentication 服務設定，請參閱 [設定 azure Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)。

如果您的使用者需要協助，請參閱 [Azure Multi-Factor Authentication 的使用者指南](../user-help/multi-factor-authentication-end-user-first-time.md)。
