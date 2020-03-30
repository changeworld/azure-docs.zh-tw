---
title: 管理使用者和設備 Azure MFA - Azure 活動目錄
description: 管理員如何更改使用者設置，例如強制使用者再次執行校樣過程。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07845bb5b742b1bcfbb22d260457e9a8e16edab6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263708"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理雲端中 Azure Multi-Factor Authentication 的使用者設定

身為管理員，您可以管理下列使用者和裝置設定：

* 要求使用者再次提供連絡方法
* 刪除應用程式密碼
* 要求在所有受信任的裝置上使用 MFA

## <a name="manage-authentication-methods"></a>管理身份驗證方法

當管理員分配了身份驗證管理員角色時，您可以要求使用者重置其密碼、重新註冊 MFA 或從其使用者物件撤銷現有的 MFA 會話。

![從 Azure 門戶管理身份驗證方法](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在左側，選擇**Azure 活動目錄** > **使用者** > **所有使用者**。
1. 選擇要對其執行操作的使用者，然後選擇**身份驗證方法**。
   - **重置密碼**將重置使用者的密碼，並分配必須在下一個登錄時更改的臨時密碼。
   - **要求重新註冊 MFA**將使使用者下次登錄時，將請求他們設置新的 MFA 身份驗證方法。
   - **撤銷 MFA 會話**會清除使用者記住的 MFA 會話，並要求他們在下次設備上的策略要求時執行 MFA。

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼

此設定會刪除使用者建立的所有應用程式密碼。 與這些應用程式密碼相關的非瀏覽器應用程式會停止運作，直到建立新應用程式密碼為止。 執行此操作需要全域管理員許可權。

### <a name="how-to-delete-users-existing-app-passwords"></a>如何刪除使用者的現有應用程式密碼

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 在左側，選擇**Azure 活動目錄** > **使用者** > **所有使用者**。
3. 選取右邊工具列上的 [Multi-Factor Authentication]****。 多重要素驗證頁面隨即開啟。
4. 勾選您想要管理之使用者旁邊的方塊。 右側將顯示快速步驟選項清單。
5. 選取 [管理使用者設定]****。
6. 勾選 [刪除選定使用者產生的所有現有應用程式密碼] **** 方塊。
   ![刪除所有現有應用密碼](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. 按一下 **"保存**"。
8. 按一下 **"關閉**"。

## <a name="next-steps"></a>後續步驟

- 取得如何[設定 Azure Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)的詳細資訊
- 如果您的使用者需要協助，請將他們指向[雙步驟驗證的使用者指南](../user-help/multi-factor-authentication-end-user.md)
