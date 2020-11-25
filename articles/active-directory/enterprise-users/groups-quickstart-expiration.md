---
title: 群組到期原則的快速入門 - Azure AD | Microsoft Docs
description: Microsoft 365 群組的到期日 - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: quickstart
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a0b9e4ccd595e90ab941cb061a016f8c4d4f3d0
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503026"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>設定 Microsoft 365 群組 Azure Active Directory 中的到期日

在本快速入門中，您將設定 Microsoft 365 群組的到期原則。 當使用者可以設定自己的群組時，未使用的群組可能會逐漸增多。 要管理未使用的群組，其中一個方式是設定這些群組的到期日，以減少手動方式刪除群組的維護作業。

到期原則十分簡單：

- 具有使用者活動的群組會在到期時自動更新
- 通知群組擁有者更新即將過期的群組
- 刪除未更新的群組
- 群組擁有者或 Azure AD 系統管理員可在 30 天內還原已刪除的 Microsoft 365 群組

> [!NOTE]
> 群組現在使用 Azure AD 智慧，可根據群組最近的使用狀態，來自動進行更新。 此更新會根據 Microsoft 365 服務 (例如 Outlook、SharePoint、Teams 和 Yammer 等) 上群組的使用者活動來決定。

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisite"></a>必要條件

 設定群組到期日所需的最低權限角色是組織中的使用者管理員。

## <a name="turn-on-user-creation-for-groups"></a>開啟使用者建立群組的功能

1. 使用使用者管理員帳戶登入 [Azure 入口網站](https://portal.azure.com)。

2. 選取 [群組]，然後選取 [一般]。
  
   ![自助式群組設定頁面](./media/groups-quickstart-expiration/self-service-settings.png)

3. 將 [使用者可以建立 Microsoft 365 群組] 設定為 [是]。

4. 完成作業後，選取 [儲存] 以儲存群組設定。

## <a name="set-group-expiration"></a>設定群組到期日

1. 登入 [Azure 入口網站](https://portal.azure.com)、選取 [Azure Active Directory] > [群組] > [到期]，以開啟到期設定。
  
   ![群組的到期設定頁面](./media/groups-quickstart-expiration/expiration-settings.png)

2. 設定到期間隔。 選取預設值，或輸入 31 天以上的自訂值。 

3. 提供在群組沒有擁有者時，到期通知所應傳送到的電子郵件地址。

4. 在此快速入門中，請將 [啟用這些 Microsoft 365 群組的到期日] 設定為 [全部]。

5. 完成作業後，選取 [儲存] 以儲存到期設定。

就這麼簡單！ 在本快速入門中，您已成功設定所選 Microsoft 365 群組的到期原則。

## <a name="clean-up-resources"></a>清除資源

### <a name="to-remove-the-expiration-policy"></a>移除到期原則

1. 確實使用具備 Azure AD 組織全域管理員身分的帳戶來登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [Azure Active Directory] > [群組] > [到期]。
3. 將 [啟用這些 Microsoft 365 群組的到期日] 設定為 [無]。

### <a name="to-turn-off-user-creation-for-groups"></a>關閉使用者建立群組的功能

1. 選取 [Azure Active Directory] > [群組] > [一般]。 
2. 將 **使用者可在 Azure 入口網站中建立 Microsoft 365 群組** 設為 **否**。

## <a name="next-steps"></a>後續步驟

如需有關到期的詳細資訊，包括 PowerShell 指示及技術限制，請參閱下列文章：

> [!div class="nextstepaction"]
> [到期原則 PowerShell](groups-lifecycle.md)
