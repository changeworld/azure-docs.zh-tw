---
title: 管理使用者和裝置 Azure MFA Azure Active Directory
description: 系統管理員如何變更使用者設定，例如強制使用者重新執行證明程式。
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378025"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理雲端中 Azure Multi-Factor Authentication 的使用者設定

身為管理員，您可以管理下列使用者和裝置設定：

* 要求使用者再次提供連絡方法
* 刪除應用程式密碼
* 要求在所有受信任的裝置上使用 MFA

## <a name="manage-authentication-methods"></a>管理驗證方法

身為系統管理員指派「驗證系統管理員」角色，您可以要求使用者重設其密碼、重新註冊 MFA，或從使用者物件撤銷現有的 MFA 會話。

![從 Azure 入口網站管理驗證方法](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 選取左邊的 [Azure Active Directory] > [使用者] > [所有使用者]。
1. 選擇您想要對其執行動作的使用者，然後選取 [**驗證方法**]。
   - [**重設密碼**] 將會重設使用者的密碼，並指派必須在下次登入時變更的暫時密碼。
   - [**需要重新註冊 MFA** ] 會讓使用者在下次登入時，會要求他們設定新的 MFA 驗證方法。
   - **撤銷 Mfa 會話**會清除使用者已記住的 mfa 會話，並要求他們在下次裝置上的原則要求時執行 mfa。

## <a name="delete-users-existing-app-passwords"></a>刪除使用者現有的應用程式密碼

此設定會刪除使用者建立的所有應用程式密碼。 與這些應用程式密碼相關的非瀏覽器應用程式會停止運作，直到建立新應用程式密碼為止。 需要全域管理員許可權才能執行此動作。

### <a name="how-to-delete-users-existing-app-passwords"></a>如何刪除使用者的現有應用程式密碼

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左邊的 [Azure Active Directory] > [使用者] > [所有使用者]。
3. 選取右邊工具列上的 [Multi-Factor Authentication]。 多重要素驗證頁面隨即開啟。
4. 勾選您想要管理之使用者旁邊的方塊。 快速步驟選項的清單會出現在右側。
5. 選取 [管理使用者設定]。
6. 勾選 [刪除選定使用者產生的所有現有應用程式密碼] 方塊。
   ![刪除所有現有的應用程式密碼](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. 按一下 [儲存]。
8. 按一下 [關閉]。

## <a name="next-steps"></a>後續步驟

- 取得如何[設定 Azure Multi-Factor Authentication 設定](howto-mfa-mfasettings.md)的詳細資訊
- 如果您的使用者需要協助，請將他們指向[雙步驟驗證的使用者指南](../user-help/multi-factor-authentication-end-user.md)
