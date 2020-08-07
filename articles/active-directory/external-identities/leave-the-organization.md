---
title: 將組織保留為來賓使用者-Azure Active Directory
description: 說明 Azure AD B2B 來賓使用者如何使用 [存取面板] 從組織離職。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc3ef7d168c17ec10fe64925adbda7044a2a4c82
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908228"
---
# <a name="leave-an-organization-as-a-guest-user"></a>以來賓使用者的身分從組織離職

Azure Active Directory (Azure AD) B2B 使用者如果不再需要組織的應用程式，或不需要與組織保持關聯，隨時皆可決定從組織離職。 使用者可以自行從組織離職，不需要聯絡系統管理員。

> [!NOTE]
> 如果您的帳戶已在家庭租使用者或資源租使用者中停用，來賓使用者就不能離開組織。 如果其帳戶已停用，來賓使用者將必須與租使用者系統管理員聯繫，使用者可以刪除來賓帳戶或啟用來賓帳戶，讓使用者可以離開組織。

## <a name="leave-an-organization"></a>從組織離職

若要離開組織，請遵循下列步驟。

1. 執行下列其中一個步驟，移至您的存取面板設定檔頁面面：
   
   - 在 [ [Azure 入口網站](https://portal.azure.com)中，按一下右上方的名稱，然後選取 [ **View account**]。
   - 開啟您的[存取面板](https://myapps.microsoft.com)，按一下右上角的名稱，然後在 [**組織**] 旁選取 [設定] 圖示 (齒輪) 。
 
   ![顯示 [存取面板] 中使用者設定的螢幕擷取畫面](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > 如果您尚未登入您想要離開的組織，請在 [**組織**] 底下，按一下組織名稱旁的 [登**入以離開組織**] 連結。 登入之後，請在右上角按一下您的名稱，然後在 [**組織**] 旁選取 [設定] 圖示 (齒輪) 。

3. 在 [組織]**** 下方找到要離開的組織，然後選取 [從組織離職]****。

   ![顯示使用者介面中 [從組織離職] 選項的螢幕擷取畫面](media/leave-the-organization/LeaveOrg.png)

4. 系統要求您確認時，請選取 [離開]****。 

## <a name="account-removal"></a>帳戶移除

使用者從組織離職後，其帳戶將在目錄中「虛刪除」。 根據預設，使用者物件會移至 Azure AD 中的 [**已刪除的使用者**] 區域，但不會永久刪除30天。 透過虛刪除的作法，若使用者在 30 天內提出要求，系統管理員便可還原使用者帳戶 (包括群組和權限)。

如有需要，租用戶系統管理員在 30 天內隨時可永久刪除帳戶。 若要這樣做：

1. 在 [Azure 入口網站] 中，選取 [Azure Active Directory]。
2. 在 [管理] 底下選取 [使用者]。
3. 選取 [刪除的使用者]****。
4. 選取已刪除使用者旁邊的核取方塊，然後選取 [永久刪除]****。

若永久刪除使用者，此動作將無法回復。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>後續步驟

- 如需 Azure AD B2B 的概觀，請參閱[何謂 Azure AD B2B 共同作業？](what-is-b2b.md)



