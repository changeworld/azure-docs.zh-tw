---
title: 管理 Azure 多重身份認證的使用者設定 - Azure 活動目錄
description: 瞭解如何為 Azure 多重身份驗證設定 Azure 活動目錄使用者設定
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309763"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>管理 Azure 多重身份驗證的使用者設定

為了説明管理 Azure 多重身份驗證的使用者,可以要求使用者重置其密碼、重新註冊 MFA 或撤銷現有的 MFA 作業階段。 對於已定義應用密碼的使用者,您還可以選擇刪除這些密碼,從而導致這些應用程式中的舊身份驗證失敗。 如果您需要向使用者提供説明,或者想要重置其安全狀態,則可能需要執行這些操作。

## <a name="manage-user-authentication-options"></a>管理使用者身份驗證選項

如果您分配了*身份驗證管理員*角色,則可以要求使用者重置其密碼、重新註冊 MFA 或從其使用者物件撤銷現有的 MFA 作業階段。 要管理使用者設定,完成以下步驟:

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左邊,選擇**Azure 的目錄** > **使用者** > **所有使用者**。
1. 選擇要對其執行操作的使用者,然後選擇**認證方法**。 在視窗頂部,然後為使用者選擇以下選項之一:
   - **重置密碼**將重置使用者的密碼,並分配必須在下一個登錄時更改的臨時密碼。
   - **要求重新註冊 MFA**使用戶下次登錄時,會要求他們設置新的 MFA 身份驗證方法。
   - **撤銷 MFA 工作階段**會清除使用者記住的 MFA 作業階段,並要求他們在下次設備上的策略要求時執行 MFA。

   ![從 Azure 門戶管理身份驗證方法](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼

如果需要,可以刪除使用者創建的所有應用密碼。 與這些應用程式密碼相關的非瀏覽器應用程式會停止運作，直到建立新應用程式密碼為止。 執行此操作需要*全域管理員*許可權。

要刪除使用者的應用程式密碼,請完成以下步驟:

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左邊,選擇**Azure 的目錄** > **使用者** > **所有使用者**。
1. 選擇**多重身份認證**。 您可能需要向右滾動才能看到此功能表選項。 選擇下面的範例螢幕擷取以檢視完整的 Azure 門戶視窗和選單位置:[![](media/howto-mfa-userstates/selectmfa-cropped.png "從 Azure AD 中的「使用者」視窗選擇多重身份驗證")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. 勾選您想要管理之使用者旁邊的方塊。 右側將顯示快速步驟選項清單。
1. 選擇 **「管理使用者設定**」,然後選擇 **「刪除選取」 使用者產生的所有現有應用密碼**「複選框」,如以下範![例所示: 刪除所有現有應用密碼](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. 選擇 **'儲存**",然後**關閉**。

## <a name="next-steps"></a>後續步驟

本文説明配置了單個用戶設置。 要設定 Azure 多重身份驗證服務設定,請參閱[設定 Azure 多重身份驗證設定](howto-mfa-mfasettings.md)

如果使用者需要説明,請參閱[Azure 多重身份驗證的使用者指南](../user-help/multi-factor-authentication-end-user.md)。
